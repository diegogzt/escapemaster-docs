# Auditoría Técnica: Proceso de Onboarding en Escapemaster Web

## 1. Resumen del Flujo
El proceso de onboarding está diseñado para guiar al usuario desde el registro hasta la configuración inicial de su organización. A continuación se detalla cada etapa técnica.

## 2. Creación de Sesión y Autenticación
- **Frontend**:
  - El usuario ingresa credenciales en `/login`.
  - Se llama a `auth.login` (`POST /auth/login`).
  - Al recibir el `access_token` (JWT), se almacena en **`localStorage`** bajo la clave `"token"`.
  - `AuthContext` detecta el token y actualiza el estado `isAuthenticated` a `true`.
- **Backend**:
  - `AuthService.login` verifica las credenciales (contra BD local o Supabase).
  - Genera un JWT firmado con `JWT_SECRET_KEY` que incluye `user_id`, `email`, `organization_id` y `role_id`.

## 3. Verificación de Email
- **Envío**:
  - En **Producción (Supabase)**: Supabase Auth envía automáticamente el correo de confirmación al registrarse (`client.auth.sign_up`).
  - En **Desarrollo (Local)**: El sistema simula el registro creando el usuario directamente en la BD sin enviar correo real.
- **Comprobación**:
  - El frontend muestra una pantalla de "Revisa tu email" basada en una marca de tiempo en `localStorage` (`email_confirmation`).
  - El bloqueo de login por falta de verificación es manejado enteramente por Supabase (retornaría error 400/401 si el email no está confirmado). En modo desarrollo local, esto se omite.

## 4. Almacenamiento de Sesión
- **Mecanismo**: `localStorage`.
- **Persistencia**: El token persiste entre recargas.
- **Validación**:
  - Al cargar la app, `AuthContext` lee el token.
  - Realiza una llamada a `/auth/me` para validar el token y obtener datos frescos del usuario (especialmente `organization_id`).
  - Si el token es inválido o expira (401), un interceptor de Axios (`api.ts`) limpia el storage y redirige a `/login`.

## 5. Creación de Organización (Onboarding)
- **Disparador**:
  - Si `/auth/me` retorna un usuario con `organization_id: null`, el `AuthContext` fuerza la redirección a `/onboarding`.
- **Proceso Backend (`OrganizationService.create_organization`)**:
  1.  Crea la entidad `Organization`.
  2.  Genera un `invitation_code` único de 6 caracteres.
  3.  Crea los **Roles por Defecto** para esa organización: `Admin`, `Manager`, `Recepcionista`.
  4.  Asigna **Permisos**:
      - `Admin`: Recibe TODOS los permisos del sistema.
      - `Manager`: Recibe la mayoría (lógica actual asigna casi todos).
      - `Recepcionista`: Recibe permisos de categorías `bookings`, `customers`, `rooms`, `payments`.

## 6. Asignación de Administrador
- **Lógica**:
  - Es una operación atómica dentro de la transacción de creación de la organización.
  - El usuario que crea la organización (`current_user`) es actualizado automáticamente:
    ```python
    user.organization_id = organization.id
    user.role_id = admin_role.id
    ```
  - Esto garantiza que el creador siempre tenga acceso total inmediato.

## 7. Puntos de Atención (Hallazgos)
- **Seguridad de Sesión**: El uso de `localStorage` es vulnerable a XSS. Se recomienda evaluar cookies `HttpOnly` para mayor seguridad en el futuro.
- **Validación de Email en Dev**: En entorno local, cualquiera puede registrarse con un email falso y entrar, ya que el paso de verificación de Supabase se salta.
 