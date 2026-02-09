# **Guía de Funcionalidades y Experiencia de Usuario (Escapista)**

Este documento detalla la experiencia completa del usuario final ("Player") en el Marketplace de **Escapemaster** (marketplace/web). El objetivo es crear un ecosistema gamificado que elimine la fricción de reserva y fomente la recurrencia.

## **1\. Descubrimiento y Búsqueda Inteligente**

El primer paso es encontrar la sala perfecta. No solo listamos salas, las "curamos".

### **1.1 Motor de Filtros Avanzados**

* **Por Ubicación:** Geolocalización actual o búsqueda por ciudad/barrio.  
* **Por Temática:** Filtros visuales basados en nuestros Design Tokens (Tropical, Terror, Futurista, Histórico, Crimen).  
* **Factor Miedo (Fear Meter):** Slider de 0 (Aventura familiar) a 5 (Terror extremo con contacto).  
* **Dificultad:** Filtrar por % de éxito de otros equipos (ej. "Solo salas con \<30% de éxito" para expertos).  
* **Disponibilidad:** "Jugar HOY" o "Jugar este fin de semana".

### **1.2 Colecciones Curadas**

Listas generadas editorialmente o por IA para inspirar:

* *"Para primera cita"* (Salas cooperativas de 2 personas).  
* *"Adrenalina Pura"* (Terror \+ Dificultad Alta).  
* *"Family Friendly"* (Sin sustos, puzzles lógicos).

## **2\. El Proceso de Reserva (Booking Flow)**

Diseñado para grupos, resolviendo el problema del "quién paga".

### **2.1 Disponibilidad en Tiempo Real**

Visualización de calendario clara, mostrando slots disponibles en verde. Integración directa con la API del backend para evitar *overbooking*.

### **2.2 Pago Dividido (Split Payment 2.0)**

La funcionalidad estrella para grupos.

* **Flujo Técnico:**  
  1. Líder selecciona slot y marca "Pago Dividido".  
  2. Paga su cuota (ej. 1/5 del total) para **reservar el bloqueo temporal**.  
  3. El sistema retiene el slot durante **24 horas** (o hasta 2h antes del juego).  
  4. Se genera un "Lobby de Pago" con una URL única compartible (WhatsApp/Telegram).  
  5. Los amigos entran, ven quién ha pagado y pagan su parte.  
  6. **Confirmación:** Si se completa el 100%, se emite el QR.  
  7. **Fallo:** Si expira el tiempo sin completar el pago, se libera la sala y se reembolsa la parte pagada (menos pequeña tasa de gestión) o se convierte en crédito.

### **2.3 Extras y Personalización**

Upselling durante el checkout:

* **"Modo Actor":** Añadir un actor extra a la sala.  
* **"Sorpresa de Cumpleaños":** Esconder un regalo traído por el usuario dentro de la sala.  
* **Video Recuerdo:** Comprar la grabación de la sesión (si la sala lo soporta).

## **3\. Ecosistema Social: Squads (Equipos)**

Fomentamos que los usuarios jueguen con los mismos amigos para crear "Equipos estables".

### **3.1 Creación de Squad**

* **Identidad:** Nombre del equipo, Avatar, Lema.  
* **Roster:** Lista de miembros fijos.  
* **Estadísticas Agregadas:**  
  * *Win Rate:* % de salas escapadas.  
  * *Record:* Tiempo más rápido.  
  * *Sinergia:* Número de partidas jugadas juntos.

### **3.2 Gestión de Reservas de Squad**

* Al reservar, el líder selecciona "Jugar como \[Nombre Squad\]".  
* El sistema pre-rellena los datos de los participantes.  
* El historial de la sala se guarda en el perfil del equipo, no solo en los individuales.

## **4\. Gamificación y Sistema de Niveles (Loyalty)**

Transformamos el jugar Escape Rooms en un videojuego en sí mismo.

### **4.1 Escapemaster Rank (Niveles)**

Sistema de XP (Puntos de Experiencia). Se gana XP por: Jugar (100xp), Escapar (50xp bonus), Reseñar (10xp), Subir fotos (10xp).

1. **Novato (The Newbie) \[0-500 XP\]:**  
   * Perfil básico.  
2. **Investigador (The Solver) \[500-2000 XP\]:**  
   * *Early Access:* Reservar salas nuevas 48h antes que el público general.  
   * *Descuento:* 2% en todas las reservas.  
3. **Maestro (The Escapemaster) \[+2000 XP\]:**  
   * *Priority Support:* Atención al cliente preferente.  
   * *Descuento:* 5% permanente.  
   * *Eventos:* Invitaciones a beta-testing de nuevas salas.

### **4.2 Medallas (Achievements)**

Logros visuales para el perfil público:

* *Speedrunner:* Escapar con \>15 min de sobra.  
* *Sherlock:* Resolver una sala sin pedir pistas (Validado por Game Master en la App).  
* *Night Owl:* Jugar una sala después de las 23:00.  
* *Marathon:* Jugar 3 salas en un fin de semana.

## **5\. Post-Experiencia**

### **5.1 Reseñas Verificadas**

Solo se puede reseñar si se ha completado la reserva.

* **Sistema:** 1 a 5 estrellas \+ Texto \+ Tags (ej. "Inmersiva", "Difícil", "Game Master Top").  
* **Validación:** El propietario puede replicar, pero no borrar (salvo insultos).

### **5.2 El Muro de la Fama**

Los usuarios pueden subir su "foto de equipo" final a la plataforma. Estas fotos aparecen en la ficha de la sala (con permiso) sirviendo como "Prueba Social" para futuros clientes.