# Estándares de Código - Escapemaster

## Visión General

Este documento define los estándares de código y convenciones que deben seguir todos los desarrolladores trabajando en el proyecto Escapemaster. El objetivo es mantener consistencia, legibilidad y mantenibilidad a través de todos los módulos.

---

## Convenciones Generales

### 1. Idioma del Código

**Backend (Python)**:
- **Comentarios**: Español (explicar *qué* hace el código)
- **Nombres de variables**: Inglés (estándar de la industria)
- **Documentación de API**: Español (para usuarios finales)

**Frontend (TypeScript/React)**:
- **Comentarios**: Español
- **Nombres de variables**: Inglés
- **Componentes**: Inglés
- **Props**: Inglés

**Ejemplo**:
```python
# Backend - Comentario en español, variables en inglés
def calculate_total_price(num_players: int, base_price: float) -> float:
    # Calcula el precio total aplicando descuentos por volumen
    discount = 0
    if num_players >= 4:
        discount = 0.10  # 10% descuento para grupos grandes
    return base_price * (1 - discount)
```

```typescript
// Frontend - Comentarios en español, nombres en inglés
export function BookingCard({ booking, onEdit }: BookingCardProps) {
  // Muestra información básica de la reserva
  return (
    <Card>
      <h3>{booking.room_name}</h3>
      <p>{booking.date}</p>
      <Button onClick={() => onEdit(booking.id)}>Editar</Button>
    </Card>
  );
}
```

### 2. Formato de Archivos

**Encoding**: UTF-8
**Final de línea**: LF (Unix style, no CRLF)
**Indentación**: 2 espacios (Python) / 2 espacios (TypeScript/React)
**Trailing whitespace**: Eliminar espacios al final de líneas

---

## Estándares Backend (Python/FastAPI)

### 1. Estructura de Archivos

```python
# manager/api/routes/bookings.py

# Imports estándar primero
from fastapi import APIRouter, Depends, HTTPException, status
from sqlalchemy.orm import Session
from typing import List

# Imports del proyecto
from app.database import get_db
from app.schemas.booking import BookingCreate, BookingResponse
from app.services.booking import BookingService

# Configuración
router = APIRouter(prefix="/bookings", tags=["bookings"])


@router.get("/", response_model=List[BookingResponse])
def list_bookings(
    skip: int = 0,
    limit: int = 20,
    db: Session = Depends(get_db),
    current_user: User = Depends(get_current_user),
):
    """
    Listar todas las reservas de la organización del usuario.
    """
    service = BookingService(db)
    bookings = service.list_by_organization(
        organization_id=current_user.organization_id,
        skip=skip,
        limit=limit,
    )
    return bookings
```

### 2. Nombres de Variables y Funciones

```python
# ✅ BIEN - snake_case para variables y funciones
user_id = "abc123"
total_price = 100.50

def get_booking_by_id(booking_id: str) -> Booking:
    pass

def calculate_discount(base_price: float, num_players: int) -> float:
    pass

# ❌ MAL - camelCase para Python
userId = "abc123"
totalPrice = 100.50

def getBookingById(bookingId: str) -> Booking:
    pass
```

### 3. Nombres de Clases

```python
# ✅ BIEN - PascalCase para clases
class BookingService:
    def __init__(self, db: Session):
        self.db = db

class UserCreate(BaseModel):
    email: EmailStr
    full_name: str

# ❌ MAL - snake_case para clases
class booking_service:
    pass

class user_create(BaseModel):
    pass
```

### 4. Constantes

```python
# ✅ BIEN - UPPER_SNAKE_CASE para constantes
MAX_BOOKINGS_PER_USER = 50
DEFAULT_PAGE_SIZE = 20
JWT_SECRET_KEY = "clave-secreta"
STRIPE_COMMISSION_RATE = 0.045  # 4.5%

# ❌ MAL - lowercase para constantes
max_bookings = 50
default_page_size = 20
```

### 5. Type Hints

**OBLIGATORIO**: Todas las funciones deben tener type hints.

```python
# ✅ BIEN - Type hints completos
def create_booking(
    db: Session,
    booking_data: BookingCreate,
    current_user: User,
) -> BookingResponse:
    """
    Crea una nueva reserva en el sistema.
    """
    pass

# ❌ MAL - Sin type hints
def create_booking(db, booking_data, current_user):
    pass
```

### 6. Docstrings

**Formato**: Google Style (común en Python)

```python
def calculate_booking_price(
    num_players: int,
    base_price: float,
    discount_code: str | None = None,
) -> float:
    """
    Calcula el precio total de una reserva.

    Args:
        num_players: Número de jugadores en la reserva.
        base_price: Precio base por sesión.
        discount_code: Código de descuento opcional.

    Returns:
        Precio total después de aplicar descuentos.

    Raises:
        ValueError: Si el código de descuento no es válido.
    """
    # Implementation...
    pass
```

### 7. Manejo de Errores

```python
# ✅ BIEN - HTTPException específico
@router.post("/")
def create_booking(booking: BookingCreate):
    # Validar disponibilidad
    if not is_room_available(booking.room_id, booking.date):
        raise HTTPException(
            status_code=status.HTTP_400_BAD_REQUEST,
            detail="La sala no está disponible en la fecha seleccionada",
        )

    # Crear reserva
    new_booking = service.create(booking)
    return new_booking

# ❌ MAL - Error genérico
@router.post("/")
def create_booking(booking: BookingCreate):
    if not is_room_available(booking.room_id, booking.date):
        raise Exception("No disponible")  # No usa HTTPException
```

### 8. Services vs Routes

**Separación**: Routes manejan requests HTTP, Services contienen lógica de negocio.

```python
# ✅ BIEN - Route delgada, Service con lógica
@router.post("/bookings")
def create_booking(
    booking_data: BookingCreate,
    db: Session = Depends(get_db),
):
    """Route: solo valida y llama service"""
    service = BookingService(db)
    return service.create_with_validation(booking_data)

# ❌ MAL - Lógica en route
@router.post("/bookings")
def create_booking(booking_data: BookingCreate, db: Session = Depends(get_db)):
    """Route con toda la lógica - DIFÍCIL DE TESTEAR"""
    # Validar disponibilidad
    # Calcular precio
    # Verificar permisos
    # Crear registro
    # Enviar email
    # ...mucha lógica aquí...
    pass
```

---

## Estándares Frontend (TypeScript/React)

### 1. Estructura de Componentes

```typescript
// manager/gestor/src/components/booking/BookingCard.tsx

import { useState } from 'react';
import { Card, Button, Badge } from '@escapemaster/ui';
import type { Booking } from '@/types/booking';

interface BookingCardProps {
  booking: Booking;
  onEdit: (id: string) => void;
  onDelete: (id: string) => void;
}

export function BookingCard({ booking, onEdit, onDelete }: BookingCardProps) {
  const [isExpanded, setIsExpanded] = useState(false);

  return (
    <Card className="booking-card">
      <div className="flex justify-between items-center">
        <div>
          <h3>{booking.room_name}</h3>
          <p>{booking.date}</p>
        </div>
        <Badge variant={booking.status === 'confirmed' ? 'success' : 'warning'}>
          {booking.status}
        </Badge>
      </div>

      {isExpanded && (
        <div className="booking-details">
          <p>Jugadores: {booking.num_players}</p>
          <p>Precio: {booking.total_amount}€</p>
        </div>
      )}

      <div className="actions">
        <Button
          variant="ghost"
          size="sm"
          onClick={() => setIsExpanded(!isExpanded)}
        >
          {isExpanded ? 'Ocultar' : 'Ver detalles'}
        </Button>
        <Button
          variant="secondary"
          onClick={() => onEdit(booking.id)}
        >
          Editar
        </Button>
        <Button
          variant="danger"
          onClick={() => onDelete(booking.id)}
        >
          Cancelar
        </Button>
      </div>
    </Card>
  );
}
```

### 2. Nombres de Variables y Funciones

```typescript
// ✅ BIEN - camelCase para variables y funciones
const userId = "abc123";
const userName = "Juan";

function handleBookingClick(bookingId: string): void {
  // ...
}

// ❌ MAL - snake_case en TypeScript
const user_id = "abc123";
const user_name = "Juan";

function handle_booking_click(booking_id: string): void {
  // ...
}
```

### 3. Nombres de Componentes

```typescript
// ✅ BIEN - PascalCase para componentes
export function BookingCard(): JSX.Element { }
export function UserList(): JSX.Element { }
export function CalendarWidget(): JSX.Element { }

// ❌ MAL - camelCase para componentes
export function bookingCard(): JSX.Element { }
export function userList(): JSX.Element { }
```

### 4. Nombres de Interfaces y Types

```typescript
// ✅ BIEN - PascalCase para interfaces/types
interface Booking {
  id: string;
  room_name: string;
  date: string;
  status: 'pending' | 'confirmed' | 'cancelled';
}

interface BookingCardProps {
  booking: Booking;
  onEdit: (id: string) => void;
}

type BookingStatus = 'pending' | 'confirmed' | 'cancelled';

// ❌ MAL - camelCase para interfaces
interface booking {
  id: string;
  room_name: string;
}
```

### 5. Imports

```typescript
// ✅ BIEN - Imports ordenados
// 1. React y bibliotecas externas
import { useState, useEffect } from 'react';
import axios from 'axios';

// 2. UI Kit
import { Button, Card } from '@escapemaster/ui';

// 3. Tipos del proyecto
import type { Booking } from '@/types/booking';

// 4. Servicios y utilidades
import { bookingService } from '@/services/booking';

// ❌ MAL - Imports desordenados
import { Button } from '@escapemaster/ui';
import { useState } from 'react';
import axios from 'axios';
import type { Booking } from '@/types/booking';
import { bookingService } from '@/services/booking';
```

### 6. Props de Componentes

```typescript
// ✅ BIEN - Interface para props
interface ButtonProps {
  children: React.ReactNode;
  variant?: 'primary' | 'secondary' | 'outline';
  size?: 'sm' | 'md' | 'lg';
  onClick?: () => void;
  disabled?: boolean;
}

export function Button({ children, variant = 'primary', size = 'md', onClick, disabled }: ButtonProps) {
  // ...
}

// ❌ MAL - Props sin tipar
export function Button({ children, variant, size, onClick, disabled }) {
  // TypeScript no puede validar
}
```

### 7. Hooks Personalizados

```typescript
// ✅ BIEN - Nombres empiezan con "use"
export function useBookings() {
  const [bookings, setBookings] = useState<Booking[]>([]);
  const [loading, setLoading] = useState(false);

  // ...

  return { bookings, loading, refetch };
}

// ✅ BIEN - Pasar parámetros a hooks
export function useBookings(filters: BookingFilters) {
  const [bookings, setBookings] = useState<Booking[]>([]);

  useEffect(() => {
    // Cargar con filtros
    loadBookings(filters).then(setBookings);
  }, [filters]);

  return bookings;
}

// ❌ MAL - Nombre no convencional
export function bookings() { }
export function fetchBookings() { }  // Es un service, no un hook
```

### 8. Clases de Tailwind

```typescript
// ✅ BIEN - Clases descriptivas, evitar valores hardcoded
<div className="flex items-center justify-between gap-4">
  <Button className="w-full md:w-auto" variant="primary">
    Guardar
  </Button>
</div>

// ❌ MAL - Valores hardcoded inline
<div style={{ display: 'flex', alignItems: 'center', gap: '16px' }}>
  <Button
    style={{ backgroundColor: '#E46F20', color: 'white' }}
    onClick={handleSave}
  >
    Guardar
  </Button>
</div>
```

---

## Estándares Mobile (React Native)

### 1. Estructura de Screens

```typescript
// manager/staff-mobile/app/(dashboard)/bookings/index.tsx

import { View, Text, ScrollView } from 'react-native';
import { useLocalSearchParams } from 'expo-router';
import { BookingCard } from '@/components/BookingCard';

export default function BookingsScreen() {
  const { date } = useLocalSearchParams<{ date?: string }>();
  // ...

  return (
    <View className="flex-1 bg-background">
      <ScrollView>
        {bookings.map(booking => (
          <BookingCard key={booking.id} booking={booking} />
        ))}
      </ScrollView>
    </View>
  );
}
```

### 2. NativeWind vs Inline Styles

```typescript
// ✅ BIEN - Usar clases NativeWind
<View className="flex items-center justify-center p-4">
  <Text className="text-lg font-bold">Título</Text>
</View>

// ❌ MAL - Stylesheet inline
<View style={{ flex: 1, alignItems: 'center', justifyContent: 'center', padding: 16 }}>
  <Text style={{ fontSize: 18, fontWeight: 'bold' }}>Título</Text>
</View>
```

---

## Estándares de Git y Commits

### 1. Formato de Mensajes de Commit

**Convención**: Conventional Commits

```
<type>(<scope>): <description>

[optional body]

[optional footer(s)]
```

**Tipos permitidos**:
- `feat`: Nueva funcionalidad
- `fix`: Bug fix
- `docs`: Cambios en documentación
- `refactor`: Refactorización (sin cambios funcionales)
- `perf`: Mejoras de performance
- `style`: Cambios de formato/estilo
- `test`: Añadir o actualizar tests
- `chore`: Tareas de mantenimiento

**Ejemplos**:
```
feat(backend): añadir endpoint para gestión de cupones

- Implementar CRUD completo de cupones
- Añadir validación de códigos únicos
- Documentar endpoint en API

Closes #123

fix(web): corregir error en cálculo de precio total

El precio no se actualizaba al cambiar número de jugadores.
Ahora recalcula correctamente el total.

fixes #456

docs(api): actualizar documentación de autenticación

- Añadir ejemplos de uso de refresh tokens
- Actualizar versiones dependencias

refactor(ui-kit): simplificar componente Calendar

- Eliminar código duplicado
- Mejorar rendimiento en listas largas
```

### 2. Ramas (Branches)

```bash
feature/add-sms-notifications
feature/stripe-webhooks
fix/booking-calculation-error
docs/update-api-docs
refactor/user-service-cleanup
```

---

## Estándares de Testing

### Backend (Pytest)

```python
# manager/api/tests/test_bookings.py

import pytest
from fastapi.testclient import TestClient
from app.main import app

client = TestClient(app)

def test_create_booking_success():
    """Test: Crear reserva con datos válidos"""
    response = client.post(
        "/bookings",
        json={
            "room_id": "room-123",
            "booking_date": "2025-12-15",
            "start_time": "18:00",
            "num_players": 4,
        },
    )
    assert response.status_code == 201
    assert "id" in response.json()

def test_create_booking_room_not_available():
    """Test: Error si sala no disponible"""
    response = client.post(
        "/bookings",
        json={
            "room_id": "room-occupied",
            "booking_date": "2025-12-15",
            "start_time": "18:00",
            "num_players": 4,
        },
    )
    assert response.status_code == 400
    assert "no disponible" in response.json()["detail"].lower()

# ✅ BIEN - Nombres descriptivos, AAA pattern (Arrange-Act-Assert)
# ❌ MAL - Nombres genéricos
def test_booking_1():
    pass
```

### Frontend (Vitest)

```typescript
// manager/gestor/src/components/booking/__tests__/BookingCard.test.tsx

import { render, screen } from '@testing-library/react';
import { describe, it, expect } from 'vitest';
import { BookingCard } from '../BookingCard';

describe('BookingCard', () => {
  const mockBooking = {
    id: '1',
    room_name: 'La Cripta',
    date: '2025-12-15',
    status: 'confirmed',
    num_players: 4,
    total_amount: 100,
  };

  it('muestra el nombre de la sala', () => {
    render(<BookingCard booking={mockBooking} onEdit={vi.fn()} onDelete={vi.fn()} />);
    expect(screen.getByText('La Cripta')).toBeInTheDocument();
  });

  it('llama a onEdit al hacer click en botón Editar', () => {
    const onEdit = vi.fn();
    render(<BookingCard booking={mockBooking} onEdit={onEdit} onDelete={vi.fn()} />);

    const editButton = screen.getByText('Editar');
    editButton.click();

    expect(onEdit).toHaveBeenCalledWith('1');
  });

  // ✅ BIEN - Tests descriptivos, AAA pattern
});
```

### Mobile (Expo/Jest)

```typescript
// manager/staff-mobile/__tests__/screens/BookingsScreen.test.tsx

import { render } from '@testing-library/react-native';
import { describe, it, expect } from '@jest/globals';
import { BookingsScreen } from '../app/(dashboard)/bookings';

describe('BookingsScreen', () => {
  it('muestra lista de reservas', () => {
    const mockBookings = [
      { id: '1', room_name: 'Sala 1' },
      { id: '2', room_name: 'Sala 2' },
    ];
    render(<BookingsScreen bookings={mockBookings} />);

    expect(screen.getByText('Sala 1')).toBeTruthy();
    expect(screen.getByText('Sala 2')).toBeTruthy();
  });
});
```

---

## Linting y Formating

### Backend (Python)

```bash
# Linting con flake8
flake8 app/

# Formating con black
black app/

# Type checking con mypy (opcional)
mypy app/
```

**Configuración** (`pyproject.toml`):
```toml
[tool.black]
line-length = 100
target-version = ['py311']

[tool.isort]
profile = "black"
```

### Frontend (TypeScript/React)

```bash
# Linting con ESLint
npm run lint

# Fix automático
npm run lint:fix

# Formating con Prettier
npm run format
```

**Configuración** (`.eslintrc.json`):
```json
{
  "extends": ["next/core-web-vitals", "prettier"],
  "rules": {
    "@typescript-eslint/no-unused-vars": "error",
    "react-hooks/rules-of-hooks": "error",
    "react-hooks/exhaustive-deps": "warn"
  }
}
```

**Prettier** (`.prettierrc`):
```json
{
  "semi": true,
  "singleQuote": false,
  "tabWidth": 2,
  "trailingComma": "es5",
  "printWidth": 100
}
```

---

## Checklist de Revisión de Código

Antes de hacer push a main:

- [ ] El código sigue las convenciones de nomenclatura
- [ ] Las funciones tienen type hints completos
- [ ] Los componentes tienen interfaces para props
- [ ] Los tests cubren los cambios (mínimo 80%)
- [ ] El commit message sigue Conventional Commits
- [ ] No hay console.log() ni debugger statements
- [ ] Los archivos tienen encoding UTF-8
- [ ] No hay trailing whitespace
- [ ] El código formatea con el formatter correcto (black/prettier)
- [ ] Las dependencias están actualizadas si es necesario

---

**Última actualización:** 4 de febrero de 2026
