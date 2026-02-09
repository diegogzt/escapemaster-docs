# **Manual Maestro de Onboarding y Operativa para Partners (B2B)**

Este documento detalla el ciclo de vida completo de una empresa (Escape Room) dentro del ecosistema **Escapemaster**, desde el primer contacto hasta la gestión diaria y liquidación financiera.

## **1\. Fase de Admisión y Legal (KYB)**

Para garantizar la calidad y seguridad de la plataforma, implementamos un proceso estricto de *Know Your Business* (KYB).

### **1.1 Registro de la Organización**

El propietario inicia el proceso en manager.escapemaster.es/register.

* **Datos de Cuenta:** Email corporativo, contraseña segura.  
* **Datos de Organización:** Nombre Comercial, Razón Social, CIF/NIF, Dirección Fiscal.

### **1.2 Verificación Documental**

Antes de activar la posibilidad de recibir reservas, el sistema bloquea el estado de la cuenta en pending\_verification. Se requiere la subida de:

1. **Escrituras de Constitución:** O documento de autónomo.  
2. **Certificado de Titularidad Bancaria:** Para validar el IBAN de cobro.  
3. **Seguro de Responsabilidad Civil:** Obligatorio para actividades de ocio.

*SLA de Validación:* El equipo de Ops de Escapemaster revisa estos documentos en un plazo máximo de 24-48h laborables.

## **2\. Configuración del Producto (El "Manager")**

Una vez verificado, el partner accede al Dashboard (manager/gestor) para configurar su oferta.

### **2.1 Definición de Salas (Inventory)**

Cada sala se configura con metadatos ricos para el algoritmo de búsqueda del Marketplace:

* **Ficha Técnica:** Nombre, Sinopsis, Dificultad (1-5), Factor Miedo (0-5), Duración (minutos).  
* **Multimedia:** Galería de imágenes HD y Link a Video Trailer (Youtube/Vimeo).  
* **Capacidad:** Mínimo y Máximo de jugadores.  
* **Pricing Engine:**  
  * *Precio Base:* Coste fijo por sesión.  
  * *Precio Dinámico:* Coste extra por jugador adicional.  
  * *Ofertas:* Descuentos automáticos para días de baja demanda (ej. martes por la mañana).

### **2.2 Gestión de Horarios y Buffers**

El sistema de reservas evita solapamientos y permite la gestión operativa:

* **Slot Generation:** El partner define hora de apertura y cierre.  
* **Buffer Time:** Tiempo obligatorio entre sesiones para resetear la sala (ej. 15 min).  
  * *Cálculo:* Fin Sesión 1 \+ Buffer \<= Inicio Sesión 2\.

## **3\. Integración Tecnológica**

Escapemaster no es solo un portal, es el motor tecnológico del Escape Room.

### **3.1 Vinculación de Software (API)**

* **Clientes Nativos:** Usan nuestro Manager (manager/gestor) como única fuente de verdad.  
* **Clientes Externos:** Si usan otro software, ofrecemos una API de sincronización (vía POST /link-manager) para importar disponibilidad, aunque incentivamos el uso de nuestra suite completa por la integración de pagos.

### **3.2 El Widget de Reservas (Escapemaster Plugin)**

Para que el Escape Room no pierda ventas en su propia web, ofrecemos un widget embebible.

* **Tecnología:** Web Component / Iframe o Plugin de WordPress (shared/plugin-WP).  
* **Ventaja:** Las reservas que entran por el widget tienen una **comisión reducida (1.5%)** en lugar del 4% estándar, incentivando al partner a usar nuestra tecnología en todos sus canales.

## **4\. Operativa Diaria y Staff**

### **4.1 Gestión de Roles (RBAC)**

El propietario puede invitar empleados con permisos granulares:

* **Admin:** Acceso total (Finanzas, Configuración).  
* **Manager:** Gestión de reservas y horarios, sin acceso a facturación.  
* **Game Master:** Solo ver calendario de hoy y fichar entrada/salida.

### **4.2 Escapemaster Mobile App (manager/staff-mobile)**

Herramienta operativa para el Game Master en sala (iOS/Android):

* **Check-in de Jugadores:** Escaneo de QR de la reserva al llegar el grupo.  
* **Control de Tiempo:** Cronómetro sincronizado con la sala.  
* **Reset Checklist:** Lista de tareas interactiva para dejar la sala lista (ej. "Cerrar candado caja fuerte", "Esconder llave maestra").  
* **RRHH:** Fichaje de entrada y salida laboral (Timeclock) geolocalizado.

## **5\. Modelo Financiero y Liquidaciones**

El flujo de dinero está diseñado para dar liquidez semanal al partner.

### **5.1 Flujo de Cobro (Split Payment Support)**

* Escapemaster actúa como pasarela de pago (Merchant of Record) ante el cliente final.  
* Soportamos pagos parciales (señales) y pagos completos.

### **5.2 Facturación y Payouts**

* **Ciclo de Facturación:** Semanal (Lunes a Domingo).  
* **Liquidación (El "Lunes de Pago"):**  
  1. El sistema calcula el Gross Merchandise Value (GMV) de las reservas *ejecutadas* (no solo reservadas, para evitar fraudes).  
  2. Resta la **Comisión de Plataforma (4%)**.  
  3. Resta posibles devoluciones/cancelaciones.  
  4. Emite una orden de transferencia SEPA inmediata al IBAN verificado.  
* **Autofactura:** El sistema genera automáticamente una factura de comisión en nombre de Escapemaster para la contabilidad del partner.

### **5.3 Política de Cancelación**

Configurable por el partner, pero con estándares de plataforma:

* **Flexible:** Reembolso 100% hasta 48h antes.  
* **Estricta:** Sin reembolso, solo cambio de fecha (Voucher).