# Onboarding B2B - Documentación Técnica

## Resumen

Sistema completo de onboarding para empresas de escape rooms que permite:
- Verificación KYB (Know Your Business) con documentos
- Gestión de cuentas bancarias con validación IBAN
- Liquidaciones semanales automáticas

## Arquitectura

### Base de Datos

#### Tablas Principales

```
organizations
├── organization_kyb_documents    # Documentos de verificación
├── organization_bank_accounts    # Cuentas bancarias verificadas
└── payouts                       # Historial de liquidaciones
    └── payout_bookings           # Desglose por reserva
```

#### ENUMs

- `organization_verification_status`: pending, kyb_submitted, documents_under_review, verified, rejected
- `kyb_document_type`: cif_certificate, legal_representative_id, company_deed, bank_account_proof, other
- `document_status`: pending, approved, rejected
- `payout_status`: pending, processing, completed, failed

### Flujo de Verificación KYB

```
1. Registro de empresa
   └── Estado: pending

2. Subida de documentos
   ├── CIF/NIF de empresa
   ├── DNI del representante legal
   └── Escrituras de constitución
   └── Estado: kyb_submitted

3. Revisión por admin
   └── Estado: documents_under_review

4. Aprobación/Rechazo
   ├── Si aprobado → Estado: verified
   └── Si rechazado → Estado: rejected (con motivo)
```

## API Endpoints

### KYB - Empresa

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| GET | `/kyb/documents` | Listar mis documentos KYB |
| POST | `/kyb/documents` | Subir nuevo documento |
| GET | `/kyb/status` | Estado de verificación |
| GET | `/kyb/bank-account` | Obtener cuenta bancaria |
| POST | `/kyb/bank-account` | Registrar cuenta bancaria |

### KYB - Admin

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| GET | `/kyb/admin/pending` | Documentos pendientes de revisión |
| PUT | `/kyb/admin/documents/{id}` | Aprobar/rechazar documento |

### Payouts

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| GET | `/payouts` | Historial de liquidaciones |
| GET | `/payouts/summary` | Resumen para dashboard |
| GET | `/payouts/{id}` | Detalle con desglose |
| GET | `/payouts/{id}/invoice` | Descargar factura |

## Validaciones

### CIF Español
```python
# Formato: Letra + 7 dígitos + Dígito/Letra de control
# Ejemplo: B12345678, A87654321
```

### IBAN
```python
# Formato: ES + 2 dígitos + 20 dígitos
# Ejemplo: ES9121000418450200051332
# Validación: Algoritmo ISO 13616 (módulo 97)
```

## Modelo de Comisiones

| Canal | Comisión |
|-------|----------|
| Reserva propia (widget) | 15% |
| Reserva marketplace | 10% |

### Cálculo de Payout
```
payout_neto = suma(reservas) - comisión_plataforma - iva
```

## Ciclo de Pagos

- **Periodicidad**: Semanal (lunes a domingo)
- **Procesamiento**: Lunes siguiente
- **Método**: Transferencia bancaria SEPA

## Integración Admin

### Panel KYB (`/kyb`)
- Lista de documentos pendientes de revisión
- Preview de documento
- Botones aprobar/rechazar
- Campo para motivo de rechazo

### Panel Payouts (`/payouts`)
- Resumen de pagos pendientes
- Lista por organización
- Botón para procesar pagos masivos
- Historial de pagos anteriores

## Seguridad

- Documentos almacenados en S3/CloudFlare R2 con URLs firmadas
- Solo admins pueden revisar documentos KYB
- Validación de permisos por organización en payouts
- Logs de auditoría para cambios de estado

## Próximos Pasos

1. [ ] Integración con servicio de validación CIF (AEAT)
2. [ ] Notificaciones email para cambios de estado
3. [ ] Webhook para notificar pago completado
4. [ ] Dashboard empresa con estado KYB en tiempo real
5. [ ] Integración pasarela de pagos para transferencias automáticas
