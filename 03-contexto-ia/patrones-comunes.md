# Patrones Comunes - Escapemaster

## Visión General

Este documento describe patrones de código y componentes reutilizables que se usan a través de todos los proyectos de Escapemaster. El objetivo es promover la consistencia y evitar la duplicación de código.

---

## Patrones de Backend (Python/FastAPI)

### 1. Service Layer Pattern

**Propósito**: Separar lógica de negocio de los endpoints HTTP.

```python
# manager/api/services/booking.py

from sqlalchemy.orm import Session
from typing import List, Optional
from datetime import datetime

class BookingService:
    """
    Servicio para gestión de reservas.
    Encapsula toda la lógica de negocio relacionada con reservas.
    """

    def __init__(self, db: Session):
        self.db = db

    def list_by_organization(
        self,
        organization_id: str,
        skip: int = 0,
        limit: int = 20,
    ) -> List[Booking]:
        """
        Listar reservas filtradas por organización.
        """
        return (
            self.db.query(Booking)
            .filter(Booking.organization_id == organization_id)
            .order_by(Booking.booking_date.desc())
            .offset(skip)
            .limit(limit)
            .all()
        )

    def create_with_validation(
        self,
        booking_data: BookingCreate,
        organization_id: str,
    ) -> Booking:
        """
        Crear reserva con validaciones de negocio.
        """
        # 1. Validar disponibilidad de sala
        if not self._is_room_available(
            booking_data.room_id,
            booking_data.booking_date,
            booking_data.start_time,
        ):
            raise ValueError("La sala no está disponible en el horario seleccionado")

        # 2. Calcular precio
        total_price = self._calculate_price(booking_data)

        # 3. Crear reserva
        booking = Booking(
            **booking_data.dict(),
            organization_id=organization_id,
            total_amount=total_price,
            status="pending",
        )
        self.db.add(booking)
        self.db.commit()
        self.db.refresh(booking)

        # 4. Enviar notificación
        self._send_confirmation_email(booking)

        return booking

    def _is_room_available(
        self,
        room_id: str,
        booking_date: datetime,
        start_time: str,
    ) -> bool:
        """
        Validar disponibilidad de sala.
        Método privado (prefijo con _).
        """
        existing_booking = (
            self.db.query(Booking)
            .filter(
                Booking.room_id == room_id,
                Booking.booking_date == booking_date,
                Booking.start_time == start_time,
                Booking.status.in_(["confirmed", "in_progress"]),
            )
            .first()
        )
        return existing_booking is None

    def _calculate_price(self, booking_data: BookingCreate) -> float:
        """Calcular precio según número de jugadores."""
        room = self.db.query(Room).get(booking_data.room_id)
        base_price = room.price
        num_players = booking_data.num_players

        # Lógica de precio dinámico
        if num_players > room.min_players:
            extra_players = num_players - room.min_players
            extra_price = extra_players * room.extra_player_price
            return base_price + extra_price

        return base_price
```

**Uso en Routes**:
```python
# manager/api/routes/bookings.py

@router.post("/", response_model=BookingResponse)
def create_booking(
    booking_data: BookingCreate,
    db: Session = Depends(get_db),
    current_user: User = Depends(get_current_user),
):
    """
    Route delgada: valida autenticación y delega a service.
    """
    service = BookingService(db)
    return service.create_with_validation(booking_data, current_user.organization_id)
```

---

### 2. Pydantic Schema Pattern

**Propósito**: Validación automática de datos de entrada/salida.

```python
# manager/api/schemas/booking.py

from pydantic import BaseModel, EmailStr, validator
from typing import Optional, List
from datetime import datetime

class BookingBase(BaseModel):
    """Base model con campos comunes."""
    room_id: str
    booking_date: datetime
    start_time: str
    num_players: int
    notes: Optional[str] = None

class BookingCreate(BookingBase):
    """Schema para crear reserva (sin campos automáticos)."""
    customer_name: str
    customer_email: EmailStr
    customer_phone: Optional[str] = None
    coupon_code: Optional[str] = None

    @validator('num_players')
    def validate_num_players(cls, v, values):
        """Validar número de jugadores según capacidad de sala."""
        # Nota: Esto requeriría injection de DB o validación en service
        if v < 2:
            raise ValueError("Mínimo 2 jugadores")
        if v > 10:
            raise ValueError("Máximo 10 jugadores")
        return v

class BookingUpdate(BaseModel):
    """Schema para actualizar reserva (todos campos opcionales)."""
    # Permite actualización parcial
    num_players: Optional[int] = None
    notes: Optional[str] = None
    status: Optional[str] = None

class BookingResponse(BookingBase):
    """Schema para respuesta (con campos de sistema)."""
    id: str
    organization_id: str
    room_name: str  # Campo calculado (no en DB)
    total_amount: float
    status: str
    created_at: datetime
    updated_at: datetime

    class Config:
        from_attributes = True  # Pydantic v2: permite crear desde ORM models
```

---

### 3. Dependency Injection Pattern

**Propósito**: Inyectar dependencias (DB, user auth) en endpoints.

```python
# manager/api/dependencies.py

from fastapi import Depends, HTTPException, status
from sqlalchemy.orm import Session
from app.database import get_db
from app.models.user import User
from app.utils.jwt import decode_token

async def get_current_user(
    token: str = Depends(get_token_from_header),
    db: Session = Depends(get_db),
) -> User:
    """
    Dependency para obtener usuario autenticado.
    Usar en cualquier endpoint que requiera autenticación.
    """
    try:
        payload = decode_token(token)
        user_id = payload.get("sub")
        user = db.query(User).get(user_id)

        if user is None:
            raise HTTPException(status_code=401, detail="Usuario no encontrado")

        if not user.is_active:
            raise HTTPException(status_code=403, detail="Usuario inactivo")

        return user

    except Exception:
        raise HTTPException(status_code=401, detail="Token inválido")

async def require_permission(permission: str):
    """
    Dependency factory para requerir permiso específico.
    Uso: current_user: User = Depends(get_current_user),
             admin_user: User = Depends(require_permission("admin:delete_orgs"))
    """
    async def permission_checker(
        current_user: User = Depends(get_current_user),
    ) -> User:
        if not current_user.has_permission(permission):
            raise HTTPException(status_code=403, detail="Permiso insuficiente")
        return current_user

    return permission_checker
```

**Uso en Routes**:
```python
# manager/api/routes/users.py

@router.delete("/{user_id}")
def delete_user(
    user_id: str,
    current_user: User = Depends(get_current_user),
    admin_user: User = Depends(require_permission("users:delete")),
):
    """
    Requiere autenticación Y permiso específico.
    """
    # Lógica de eliminación...
    pass
```

---

### 4. Error Handling Pattern

**Propósito**: Manejar errores consistentemente con HTTP exceptions.

```python
# manager/api/utils/errors.py

from fastapi import HTTPException, status

class BookingException(HTTPException):
    """Excepción personalizada para errores de reservas."""

    def __init__(self, detail: str):
        super().__init__(status_code=status.HTTP_400_BAD_REQUEST, detail=detail)

class PermissionException(HTTPException):
    """Excepción personalizada para errores de permisos."""

    def __init__(self, detail: str = "Permiso insuficiente"):
        super().__init__(status_code=status.HTTP_403_FORBIDDEN, detail=detail)

class NotFoundException(HTTPException):
    """Excepción personalizada para recursos no encontrados."""

    def __init__(self, resource: str = "Recurso"):
        super().__init__(
            status_code=status.HTTP_404_NOT_FOUND,
            detail=f"{resource} no encontrado",
        )

# Uso en services
from app.utils.errors import BookingException

class BookingService:
    def create_booking(self, data):
        if not self._is_available(data.room_id):
            raise BookingException("La sala no está disponible en el horario seleccionado")

        # ...
```

---

### 5. Database Transaction Pattern

**Propósito**: Manejar transacciones ACID correctamente.

```python
# manager/api/services/booking.py

from sqlalchemy.exc import IntegrityError

class BookingService:
    def create_booking_with_payment(self, booking_data: BookingCreate):
        """
        Crear reserva y procesar pago en una transacción.
        Si algo falla, se hace rollback de todo.
        """
        try:
            # Iniciar transacción ( SQLAlchemy lo hace automáticamente en context)
            booking = self._create_booking(booking_data)
            payment = self._process_payment(booking.id, booking.total_amount)
            self._update_booking_status(booking.id, "confirmed")

            # Commit automático al salir del try
            return booking

        except IntegrityError as e:
            # Rollback automático
            self.db.rollback()
            raise BookingException("Error de integridad: reserva duplicada")
        except Exception as e:
            self.db.rollback()
            raise BookingException(f"Error al crear reserva: {str(e)}")
```

---

## Patrones de Frontend (TypeScript/React)

### 1. Custom Hook Pattern

**Propósito**: Reutilizar lógica con estado y efectos entre componentes.

```typescript
// manager/gestor/src/hooks/useBookings.ts

import { useState, useEffect, useCallback } from 'react';
import axios from '@/lib/axios';
import type { Booking, BookingFilters } from '@/types/booking';

interface UseBookingsReturn {
  bookings: Booking[];
  loading: boolean;
  error: string | null;
  refetch: () => Promise<void>;
}

export function useBookings(filters?: BookingFilters): UseBookingsReturn {
  const [bookings, setBookings] = useState<Booking[]>([]);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState<string | null>(null);

  const fetchBookings = useCallback(async () => {
    setLoading(true);
    setError(null);

    try {
      const response = await axios.get<{ items: Booking[] }>('/bookings', {
        params: filters,
      });
      setBookings(response.data.items);
    } catch (err) {
      setError('Error al cargar reservas');
      console.error(err);
    } finally {
      setLoading(false);
    }
  }, [filters]);

  useEffect(() => {
    fetchBookings();
  }, [fetchBookings]);

  return { bookings, loading, error, refetch: fetchBookings };
}

// Uso en componente
import { useBookings } from '@/hooks/useBookings';

export function BookingList() {
  const { bookings, loading, error, refetch } = useBookings();

  if (loading) return <Spinner />;
  if (error) return <Alert variant="error">{error}</Alert>;

  return (
    <div>
      {bookings.map(booking => (
        <BookingCard key={booking.id} booking={booking} />
      ))}
    </div>
  );
}
```

---

### 2. Compound Component Pattern

**Propósito**: Crear componentes flexibles que pueden ser compuestos.

```typescript
// manager/ui-kit/src/components/Card/index.tsx

import { cn } from '@/lib/utils';

interface CardProps {
  children: React.ReactNode;
  className?: string;
  noPadding?: boolean;
  hoverEffect?: boolean;
}

export function Card({ children, className, noPadding, hoverEffect }: CardProps) {
  return (
    <div
      className={cn(
        "bg-surface rounded-lg shadow-md",
        !noPadding && "p-6",
        hoverEffect && "hover:shadow-lg transition-shadow",
        className,
      )}
    >
      {children}
    </div>
  );
}

// Subcomponentes para composición
export function CardHeader({ children, className }: CardProps) {
  return (
    <div className={cn("bg-primary text-white p-4 rounded-t-lg", className)}>
      {children}
    </div>
  );
}

export function CardTitle({ children }: { children: React.ReactNode }) {
  return <h3 className="text-lg font-bold">{children}</h3>;
}

export function CardFooter({ children, className }: CardProps) {
  return <div className={cn("pt-4 mt-4 border-t", className)}>{children}</div>;
}

// Uso compuesto
import { Card, CardHeader, CardTitle, CardFooter, Button } from '@escapemaster/ui';

export function BookingForm() {
  return (
    <Card>
      <CardHeader>
        <CardTitle>Nueva Reserva</CardTitle>
      </CardHeader>

      <form>
        {/* Campos del formulario */}
      </form>

      <CardFooter className="flex gap-2 justify-end">
        <Button variant="ghost">Cancelar</Button>
        <Button type="submit">Guardar</Button>
      </CardFooter>
    </Card>
  );
}
```

---

### 3. Controlled Form Pattern

**Propósito**: Formularios con React Hook Form + Zod.

```typescript
// manager/gestor/src/components/booking/BookingForm.tsx

import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';
import { Button, Input } from '@escapemaster/ui';

// Schema de validación
const bookingSchema = z.object({
  room_id: z.string().min(1, 'Selecciona una sala'),
  booking_date: z.string().min(1, 'Selecciona una fecha'),
  start_time: z.string().regex(/^\d{2}:\d{2}$/, 'Hora inválida'),
  num_players: z.number().min(2, 'Mínimo 2 jugadores').max(10, 'Máximo 10'),
  customer_name: z.string().min(3, 'Nombre muy corto'),
  customer_email: z.string().email('Email inválido'),
  customer_phone: z.string().regex(/^\+?\d{9,15}$/, 'Teléfono inválido').optional(),
});

type BookingFormData = z.infer<typeof bookingSchema>;

export function BookingForm({ onSuccess }: { onSuccess: (data: BookingFormData) => void }) {
  const {
    register,
    handleSubmit,
    formState: { errors, isSubmitting },
  } = useForm<BookingFormData>({
    resolver: zodResolver(bookingSchema),
    defaultValues: {
      num_players: 2,
    },
  });

  const onSubmit = async (data: BookingFormData) => {
    try {
      // Lógica de envío
      await axios.post('/bookings', data);
      onSuccess(data);
    } catch (err) {
      console.error('Error al crear reserva:', err);
    }
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)} className="space-y-4">
      <div>
        <label>Nombre del cliente</label>
        <Input
          {...register('customer_name')}
          error={errors.customer_name?.message}
          placeholder="Ej: Juan García"
        />
      </div>

      <div>
        <label>Email</label>
        <Input
          {...register('customer_email')}
          type="email"
          error={errors.customer_email?.message}
          placeholder="cliente@ejemplo.com"
        />
      </div>

      <div>
        <label>Número de jugadores</label>
        <Input
          {...register('num_players', { valueAsNumber: true })}
          type="number"
          min={2}
          max={10}
          error={errors.num_players?.message}
        />
      </div>

      <Button type="submit" disabled={isSubmitting} className="w-full">
        {isSubmitting ? 'Enviando...' : 'Crear Reserva'}
      </Button>
    </form>
  );
}
```

---

### 4. API Service Pattern

**Propósito**: Encapsular llamadas API con manejo de errores y autenticación.

```typescript
// manager/gestor/src/services/bookingService.ts

import axios from '@/lib/axios';
import type { Booking, BookingCreate, BookingUpdate } from '@/types/booking';

class BookingService {
  private static BASE_URL = '/bookings';

  /**
   * Listar todas las reservas.
   */
  static async list(params?: {
    skip?: number;
    limit?: number;
    status?: string;
  }): Promise<Booking[]> {
    const response = await axios.get<{ items: Booking[] }>(this.BASE_URL, { params });
    return response.data.items;
  }

  /**
   * Obtener reserva por ID.
   */
  static async getById(id: string): Promise<Booking> {
    const response = await axios.get<Booking>(`${this.BASE_URL}/${id}`);
    return response.data;
  }

  /**
   * Crear nueva reserva.
   */
  static async create(data: BookingCreate): Promise<Booking> {
    const response = await axios.post<Booking>(this.BASE_URL, data);
    return response.data;
  }

  /**
   * Actualizar reserva existente.
   */
  static async update(id: string, data: BookingUpdate): Promise<Booking> {
    const response = await axios.patch<Booking>(`${this.BASE_URL}/${id}`, data);
    return response.data;
  }

  /**
   * Cancelar reserva.
   */
  static async cancel(id: string): Promise<void> {
    await axios.patch(`${this.BASE_URL}/${id}/status`, { status: 'cancelled' });
  }

  /**
   * Asignar reserva a usuario actual.
   */
  static async claim(id: string): Promise<Booking> {
    const response = await axios.post<Booking>(`${this.BASE_URL}/${id}/claim`);
    return response.data;
  }
}

export default BookingService;

// Uso en componente
import BookingService from '@/services/bookingService';

export function BookingList() {
  useEffect(() => {
    const loadBookings = async () => {
      const bookings = await BookingService.list({ skip: 0, limit: 20 });
      setBookings(bookings);
    };
    loadBookings();
  }, []);

  const handleDelete = async (id: string) => {
    await BookingService.cancel(id);
    // Recargar lista...
  };
}
```

---

### 5. Theme Provider Pattern

**Propósito**: Proveer tema global a toda la aplicación.

```typescript
// manager/gestor/src/context/ThemeContext.tsx

import { createContext, useContext, useState, ReactNode, useMemo } from 'react';

type Theme = 'twilight' | 'tropical' | 'vista' | 'escapemaster-original' | 'neon';

type ThemeContextType = {
  theme: Theme;
  setTheme: (theme: Theme) => void;
};

const ThemeContext = createContext<ThemeContextType | undefined>(undefined);

export function ThemeProvider({ children }: { children: ReactNode }) {
  const [theme, setThemeState] = useState<Theme>(() => {
    // Cargar tema del localStorage
    const saved = localStorage.getItem('escapemaster-theme') as Theme;
    return saved || 'escapemaster-original';
  });

  const setTheme = (newTheme: Theme) => {
    setThemeState(newTheme);
    localStorage.setItem('escapemaster-theme', newTheme);

    // Aplicar tema al document
    document.documentElement.setAttribute('data-theme', newTheme);
  };

  // Aplicar tema inicial
  useMemo(() => {
    document.documentElement.setAttribute('data-theme', theme);
  }, [theme]);

  const value = useMemo(() => ({ theme, setTheme }), [theme, setTheme]);

  return <ThemeContext.Provider value={value}>{children}</ThemeContext.Provider>;
}

export function useTheme(): ThemeContextType {
  const context = useContext(ThemeContext);
  if (context === undefined) {
    throw new Error('useTheme debe usarse dentro de ThemeProvider');
  }
  return context;
}

// Uso en app.tsx
import { ThemeProvider } from '@/context/ThemeContext';

export default function App() {
  return (
    <ThemeProvider>
      <RootLayout>
        <RootPage />
      </RootLayout>
    </ThemeProvider>
  );
}

// Uso en cualquier componente
import { useTheme } from '@/context/ThemeContext';

export function ThemeSwitcher() {
  const { theme, setTheme } = useTheme();

  return (
    <div className="flex gap-2">
      {(['twilight', 'tropical', 'vista', 'escapemaster-original', 'neon'] as const).map(
        t => (
          <button
            key={t}
            onClick={() => setTheme(t)}
            className={cn(
              'px-4 py-2 rounded',
              theme === t ? 'bg-primary text-white' : 'bg-gray-200',
            )}
          >
            {t}
          </button>
        ),
      )}
    </div>
  );
}
```

---

## Patrones de Mobile (React Native)

### 1. Secure Storage Pattern

**Propósito**: Almacenar datos sensibles de forma segura.

```typescript
// manager/staff-mobile/src/services/secureStorage.ts

import * as SecureStore from 'expo-secure-store';

const TOKEN_KEY = 'escapemaster_access_token';
const REFRESH_TOKEN_KEY = 'escapemaster_refresh_token';

export const secureStorage = {
  /**
   * Guardar access token.
   */
  async saveAccessToken(token: string): Promise<void> {
    await SecureStore.setItemAsync(TOKEN_KEY, token);
  },

  /**
   * Obtener access token.
   */
  async getAccessToken(): Promise<string | null> {
    return await SecureStore.getItemAsync(TOKEN_KEY);
  },

  /**
   * Guardar refresh token.
   */
  async saveRefreshToken(token: string): Promise<void> {
    await SecureStore.setItemAsync(REFRESH_TOKEN_KEY, token);
  },

  /**
   * Obtener refresh token.
   */
  async getRefreshToken(): Promise<string | null> {
    return await SecureStore.getItemAsync(REFRESH_TOKEN_KEY);
  },

  /**
   * Limpiar todos los tokens (logout).
   */
  async clearTokens(): Promise<void> {
    await SecureStore.deleteItemAsync(TOKEN_KEY);
    await SecureStore.deleteItemAsync(REFRESH_TOKEN_KEY);
  },
};

// Uso
import { secureStorage } from '@/services/secureStorage';

export function LoginScreen() {
  const handleLogin = async (email: string, password: string) => {
    const response = await api.post('/auth/login', { email, password });

    await secureStorage.saveAccessToken(response.data.access_token);
    await secureStorage.saveRefreshToken(response.data.refresh_token);

    router.replace('/dashboard');
  };

  const handleLogout = async () => {
    await secureStorage.clearTokens();
    router.replace('/login');
  };
}
```

---

### 2. Camera/QR Scanner Pattern

**Propósito**: Escanear códigos QR de reservas.

```typescript
// manager/staff-mobile/src/components/QRScanner.tsx

import { useState, useEffect } from 'react';
import { CameraView, Camera } from 'expo-camera';
import { BarCodeScanner } from 'expo-barcode-scanner';

export function QRScanner({ onScan }: { onScan: (data: string) => void }) {
  const [hasPermission, setHasPermission] = useState(false);

  useEffect(() => {
    (async () => {
      const { status } = await Camera.requestCameraPermissionsAsync();
      setHasPermission(status === 'granted');
    })();
  }, []);

  if (!hasPermission) {
    return <Text>Necesitamos permiso de cámara</Text>;
  }

  return (
    <View className="flex-1">
      <CameraView
        style={{ flex: 1 }}
        facing="back"
        onBarcodeScanned={({ data }) => {
          // Solo escanear una vez
          if (data) {
            onScan(data);
          }
        }}
      />
      <View className="absolute inset-0 border-4 border-primary" />
    </View>
  );
}

// Uso
import { QRScanner } from '@/components/QRScanner';
import { bookingService } from '@/services/bookingService';

export function CheckInScreen() {
  const handleScan = async (qrData: string) => {
    try {
      const booking = await bookingService.getById(qrData);
      // Mostrar detalles de reserva
      router.push(`/bookings/${qrData}`);
    } catch (err) {
      alert('Código QR no válido');
    }
  };

  return (
    <View>
      <Text>Escanea el código QR de la reserva</Text>
      <QRScanner onScan={handleScan} />
    </View>
  );
}
```

---

## Patrones de UI Kit

### 1. Variant Component Pattern (Class Variance Authority)

**Propósito**: Definir variantes de componentes de forma type-safe.

```typescript
// manager/ui-kit/src/components/Button/types.ts

import { cva, type VariantProps } from 'class-variance-authority';

const buttonVariants = cva(
  // Base styles
  'inline-flex items-center justify-center rounded-lg font-medium transition-colors',
  {
    variants: {
      variant: {
        primary: 'bg-primary text-white hover:bg-primary-dark',
        secondary: 'bg-secondary text-dark hover:bg-secondary-dark',
        accent: 'bg-accent text-white hover:bg-accent-dark',
        outline: 'border-2 border-primary text-primary hover:bg-primary hover:text-white',
        ghost: 'text-primary hover:bg-background-light',
      },
      size: {
        sm: 'px-3 py-1.5 text-sm',
        md: 'px-4 py-2 text-base',
        lg: 'px-6 py-3 text-lg',
      },
    },
    defaultVariants: {
      variant: 'primary',
      size: 'md',
    },
  },
);

type ButtonVariants = VariantProps<typeof buttonVariants>;

export type { ButtonVariants };
export { buttonVariants };
```

### 2. Merge Classes Pattern (Tailwind Merge)

**Propósito**: Merge inteligente de clases Tailwind sin conflictos.

```typescript
// manager/ui-kit/src/lib/utils.ts

import { type ClassValue, clsx } from 'clsx';
import { twMerge } from 'tailwind-merge';

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs));
}

// Uso en componente
import { cn } from '@/lib/utils';

export function Card({ className }: { className?: string }) {
  return (
    <div className={cn('bg-surface rounded-lg shadow-md', className)}>
      {/* contenido */}
    </div>
  );
}

// Permite sobrescribir sin perder clases base
<Card className="p-8 hover:shadow-xl" />  // Mantiene bg-surface, rounded-lg, shadow-md y añade p-8, hover:shadow-xl
```

---

## Resumen de Patrones

| Patrón | Backend | Frontend | Mobile | UI Kit |
|--------|----------|-----------|---------|----------|
| Service Layer | ✅ | ✅ (Hook) | ✅ (Service) | - |
| Dependency Injection | ✅ | ✅ (Context) | ✅ (Context) | - |
| Error Handling | ✅ | ✅ (Try/Catch) | ✅ (Try/Catch) | - |
| Compound Component | - | ✅ | - | ✅ |
| Controlled Form | - | ✅ | ✅ | ✅ |
| API Service | - | ✅ | ✅ | - |
| Variant Component | - | - | - | ✅ |
| Merge Classes | - | - | - | ✅ |

---

## Checklist para Usar Patrones

Antes de crear nuevo código, verificar:

- [ ] ¿Hay un patrón existente que pueda reutilizar?
- [ ] ¿El patrón es coherente con otros módulos?
- [ ] ¿El código sigue los estándares definidos en `estandares-codigo.md`?
- [ ] ¿El patrón está documentado en este archivo?
- [ ] ¿El nombre del patrón/patrón es descriptivo?

---

**Última actualización:** 4 de febrero de 2026
