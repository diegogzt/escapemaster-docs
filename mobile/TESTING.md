# Guía de Testing - EscapeMaster Mobile

Esta guía describe cómo ejecutar y escribir pruebas para la aplicación móvil de EscapeMaster.

## Tecnologías Utilizadas

- **Vitest**: Test runner moderno y rápido.
- **React Testing Library**: Para pruebas de componentes y hooks.
- **react-native-web**: Utilizado como alias para permitir la ejecución de tests en un entorno JSDOM/Node.

## Estructura de Tests

Los tests se encuentran en carpetas `__tests__` dentro de cada directorio relevante:

- `services/__tests__`: Pruebas de servicios API.
- `hooks/__tests__`: Pruebas de hooks personalizados.
- `components/__tests__`: Pruebas de componentes UI.

## Ejecución de Tests

Para ejecutar todos los tests:

```bash
cd apps/mobile
npm test
```

Para ejecutar un test específico:

```bash
npm test path/to/test.test.ts
```

## Estrategia de Mocking

### API (Axios)
Para probar servicios que usan la API, exportamos la instancia de `api` desde `services/api.ts` y usamos `vi.spyOn`:

```typescript
import { api, auth } from '../api';

vi.spyOn(api, 'post').mockResolvedValue({ data: { ... } });
await auth.login('user', 'pass');
expect(api.post).toHaveBeenCalledWith('/auth/login', { ... });
```

### Expo Modules
Los módulos específicos de Expo (como `expo-secure-store` o `expo-router`) deben ser mockeados en `vitest.setup.ts` o localmente en el test:

```typescript
vi.mock('expo-secure-store', () => ({
  getItemAsync: vi.fn(),
}));
```

## Pruebas de Componentes

Debido al uso de `react-native-web` como alias, los componentes se prueban usando `@testing-library/react` (en lugar de `@testing-library/react-native`) para evitar problemas de sintaxis con el código nativo en el entorno de Node.

```typescript
import { render } from '@testing-library/react';
import { MyComponent } from '../MyComponent';

it('renders correctly', () => {
  const { getByText } = render(<MyComponent label="Test" />);
  expect(getByText('Test')).toBeTruthy();
});
```
