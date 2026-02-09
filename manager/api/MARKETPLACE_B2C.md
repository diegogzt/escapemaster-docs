# Marketplace B2C - Documentación Técnica

## Resumen

Sistema completo de marketplace para usuarios finales (jugadores) que incluye:
- Perfiles de jugador con gamificación (XP, rangos, logros)
- Sistema social (squads/grupos)
- Reviews y fotos de salas
- Pagos divididos para grupos
- Búsqueda y descubrimiento de salas

## Arquitectura

### Base de Datos

```
users
└── players                       # Perfil de jugador marketplace
    ├── player_preferences        # Preferencias (temas, horror, ciudad)
    ├── xp_transactions           # Historial de XP ganado
    ├── player_credits            # Créditos/vouchers disponibles
    ├── player_achievements       # Logros desbloqueados
    └── squad_members             # Membresías en grupos

squads                            # Grupos de jugadores
├── squad_members
└── squad_invitations

reviews                           # Reviews de salas
room_photos                       # Fotos subidas por usuarios

achievements                      # Catálogo de logros

split_payment_lobbies             # Lobbies de pago dividido
└── split_payment_participants

curated_collections               # Colecciones editoriales
└── collection_rooms
```

## Sistema de Gamificación

### Rangos por XP

| Rango | XP Mínimo | Icono |
|-------|-----------|-------|
| Newbie | 0 | 🌱 |
| Rookie | 100 | 🎮 |
| Solver | 500 | 🔓 |
| Expert | 1500 | 🏆 |
| Master | 3000 | 👑 |
| Legend | 5000 | ⭐ |
| EscapeMaster | 10000 | 🎯 |

### Formas de Ganar XP

| Acción | XP |
|--------|-----|
| Completar reserva | 25 |
| Escapar | +25 bonus |
| Escribir review | 50 |
| Subir foto | 15 |
| Referir amigo | 100 |
| Desbloquear logro | Variable |

### Logros Predefinidos

| Código | Nombre | Condición | XP |
|--------|--------|-----------|-----|
| first_escape | Primera Escapada | 1 sala completada | 50 |
| escape_5 | Escapista | 5 salas | 100 |
| escape_10 | Veterano | 10 salas | 200 |
| escape_25 | Experto | 25 salas | 500 |
| escape_50 | Maestro | 50 salas | 1000 |
| first_review | Crítico Novato | 1 review | 25 |
| review_10 | Crítico Experto | 10 reviews | 200 |
| first_photo | Fotógrafo | 1 foto | 25 |
| first_referral | Embajador | 1 referido | 100 |
| squad_escape | Escapada en Equipo | Escape con squad | 75 |

## API Endpoints

### Players

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| GET | `/players/me` | Mi perfil |
| POST | `/players/me` | Crear perfil |
| PUT | `/players/me` | Actualizar perfil |
| GET | `/players/me/stats` | Mis estadísticas |
| GET | `/players/me/xp` | Historial de XP |
| GET | `/players/me/credits` | Mis créditos |
| GET | `/players/me/preferences` | Mis preferencias |
| PUT | `/players/me/preferences` | Actualizar preferencias |
| GET | `/players/{id}` | Perfil público |
| GET | `/players/search/username` | Buscar jugadores |

### Squads

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| GET | `/squads` | Mis squads |
| POST | `/squads` | Crear squad |
| GET | `/squads/{id}` | Detalle squad |
| PUT | `/squads/{id}` | Actualizar squad |
| GET | `/squads/{id}/members` | Miembros |
| POST | `/squads/{id}/invite` | Invitar jugador |
| POST | `/squads/join/{code}` | Unirse por código |
| POST | `/squads/{id}/invites/{id}/accept` | Aceptar invitación |
| DELETE | `/squads/{id}/leave` | Abandonar |
| DELETE | `/squads/{id}/members/{id}` | Expulsar miembro |

### Reviews

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| GET | `/reviews/rooms/{id}` | Reviews de sala |
| GET | `/reviews/rooms/{id}/summary` | Estadísticas |
| POST | `/reviews/rooms/{id}` | Crear review |
| PUT | `/reviews/{id}` | Editar review |
| DELETE | `/reviews/{id}` | Eliminar review |
| POST | `/reviews/{id}/helpful` | Marcar útil |
| GET | `/reviews/rooms/{id}/photos` | Fotos de sala |
| POST | `/reviews/rooms/{id}/photos` | Subir foto |

### Achievements

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| GET | `/achievements` | Todos los logros |
| GET | `/achievements/categories` | Categorías |
| GET | `/achievements/me` | Mis logros |
| GET | `/achievements/me/progress` | Mi progreso |
| POST | `/achievements/check` | Verificar y desbloquear |

### Split Payment

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| POST | `/split-payment` | Crear lobby |
| GET | `/split-payment/join/{code}` | Info por código |
| GET | `/split-payment/{id}` | Mi lobby |
| POST | `/split-payment/{id}/pay` | Registrar pago |
| PUT | `/split-payment/{id}/amounts` | Montos custom |
| POST | `/split-payment/{id}/remind` | Enviar recordatorios |
| DELETE | `/split-payment/{id}` | Cancelar |

### Marketplace

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| GET | `/marketplace/search` | Buscar salas |
| GET | `/marketplace/search/suggestions` | Autocompletado |
| GET | `/marketplace/collections` | Colecciones |
| GET | `/marketplace/collections/{slug}` | Detalle colección |
| GET | `/marketplace/featured` | Salas destacadas |
| GET | `/marketplace/new` | Salas nuevas |
| GET | `/marketplace/cities` | Ciudades con salas |
| GET | `/marketplace/stats` | Estadísticas generales |
| GET | `/marketplace/rooms/{slug}` | Detalle de sala |

## Sistema de Reviews

### Ratings Múltiples

- **Rating general** (1-5 estrellas)
- **Dificultad** (1-5)
- **Ambientación** (1-5)
- **Puzzles** (1-5)
- **Staff** (1-5)

### Datos Adicionales

- ¿Escaparon? (sí/no)
- Tiempo de escape (minutos)
- Tamaño del equipo
- ¿Lo recomendarías? (sí/no)

### Moderación

- Reviews publicadas por defecto
- Sistema de reportes
- Panel admin para moderar

## Split Payment Flow

```
1. Organizador crea lobby
   └── Genera código compartible

2. Participantes acceden con código
   └── Ven monto que deben pagar

3. Cada participante paga su parte
   └── Estado: pending → paid

4. Cuando todos pagan
   └── Lobby status: completed
   └── Se confirma la reserva
```

### Tipos de Split

- **Equal**: División equitativa automática
- **Custom**: El organizador asigna montos
- **Per Person**: Cada quien paga por su plaza

## Colecciones Curadas

Colecciones editoriales para destacar salas:

- "Terror Extremo" - Salas de máximo miedo
- "Para Principiantes" - Ideales para novatos
- "Mejor Valoradas" - Top ratings
- "Nuevas Aventuras" - Recién añadidas
- "Aventuras Familiares" - Aptas para niños

## Triggers Automáticos

### Auto-Ranking
```sql
-- Actualiza rank automáticamente cuando cambia XP
UPDATE players SET rank = CASE
    WHEN total_xp >= 10000 THEN 'escapemaster'
    WHEN total_xp >= 5000 THEN 'legend'
    ...
END
```

### Stats de Sala
```sql
-- Actualiza rating_average y reviews_count en rooms
-- cuando se crea/modifica/elimina un review
```

## Próximos Pasos

1. [ ] Leaderboards por ciudad/nacional
2. [ ] Retos temporales (ej: "Escapa 3 veces en Halloween")
3. [ ] Sistema de amigos/followers
4. [ ] Chat entre miembros de squad
5. [ ] Notificaciones push para invitaciones
6. [ ] Integración redes sociales para compartir logros
