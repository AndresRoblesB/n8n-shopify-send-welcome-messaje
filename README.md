# 🛍️ Automatización de Envío de Bienvenida — Shopify + n8n + Gmail + PostgreSQL

Este flujo automatiza el **envío de correos de bienvenida y recordatorios** a nuevos clientes que se registran en tu tienda Shopify.  
Está construido completamente en **[n8n](https://n8n.io)** y utiliza integraciones con **Shopify**, **PostgreSQL (Supabase)** y **Gmail**.

![Flujo en n8n](./template/flujo%20n8n%20shopify%20bienvenida.png)

## ✨ Características principales

- 🔔 **Trigger Shopify**: Se activa automáticamente cuando un cliente nuevo es creado en Shopify.  
- 📝 **Transformación de datos**: Se extraen y preparan los campos relevantes (nombre, email, fecha, teléfono, etc.).  
- 🗃️ **Inserción en Base de Datos**: Los registros se guardan en una tabla de PostgreSQL para seguimiento (`registro_clientes`).  
- 📧 **Primer envío automático**: Se envía un correo de bienvenida con un diseño HTML moderno y un cupón de descuento.  
- 🕓 **Programación del segundo envío**: Un flujo separado consulta la base de datos periódicamente para enviar un segundo correo tras un tiempo definido.  
- 📨 **Segundo envío**: Se recuerda al cliente su descuento y se le presenta la historia y valores de la marca.  
- ✅ **Actualización de estado**: Después de cada envío, el registro se marca como `enviado` en la base de datos.

---

## 🧱 Estructura del flujo

### 📩 **ENVÍO 1** – Bienvenida inmediata
1. **Shopify Trigger** → Se activa al crear un cliente.  
2. **Edit Fields** → Mapea los campos de la respuesta de Shopify.  
3. **Insert rows in a table** → Guarda los datos en PostgreSQL.  
4. **Send a message (Gmail)** → Envía el correo HTML de bienvenida.  
5. **If** → Verifica si el envío fue exitoso.  
6. **Update rows in a table** → Actualiza el registro como “enviado”.

---

### ⏰ **ENVÍO 2** – Recordatorio programado
1. **Schedule Trigger** → Corre cada cierto intervalo (por ejemplo, cada 10 minutos).  
2. **Edit Fields1** → Define la cantidad de horas a esperar antes del segundo envío.  
3. **Select rows from a table** → Busca registros en la base de datos.  
4. **Date & Time** → Calcula si ha pasado el tiempo definido.  
5. **If** → Filtra los registros que ya pueden recibir el segundo envío.  
6. **Send a message1 (Gmail)** → Envía el correo de recordatorio.  
7. **Update rows in a table1** → Marca el segundo envío como realizado.

---

## 🛠️ Requisitos

- [n8n](https://n8n.io) instalado en local o en servidor.  
- Cuenta de Shopify con permisos para usar Webhooks.  
- Base de datos PostgreSQL (se recomienda [Supabase](https://supabase.com)).  
- Credenciales de Gmail (OAuth2 configurado en n8n).  
- Tabla `registro_clientes` con los siguientes campos mínimos:
  - `id` (número, PK)
  - `created_at_registry` (datetime)
  - `created_at` / `updated_at` (datetime)
  - `first_name`, `last_name`, `email`, `phone`
  - `envio` (número)
  - `estado` (texto)

---

## 🚀 Instalación

1. Clona este repositorio:
   ```bash
   git clone https://github.com/AndresRoblesB/n8n-shopify-send-welcome-messaje.git

   ```

2. Importa el flujo a tu instancia de n8n:
   - Ve a **n8n → Workflows → Import**.
   - Selecciona el archivo [`Envio de bienvenida  shopify.json`](./Envio%20de%20bienvenida%20%20shopify.json).

3. Configura las credenciales:
   - **Shopify Access Token**
   - **PostgreSQL / Supabase**
   - **Gmail OAuth2**

4. Ajusta el nodo `Schedule Trigger` para definir el intervalo del segundo envío.

5. Activa el flujo 🔄

---

## 🧪 Prueba rápida

- Crea un cliente de prueba en Shopify.  
- Verifica que:
  - Se inserte un registro en la tabla `registro_clientes`.  
  - Se reciba el primer correo de bienvenida.  
  - Luego de X horas, se reciba el segundo correo.

---

## 📝 Notas adicionales

- Puedes personalizar el contenido HTML de los correos directamente en los nodos `Send a message` y `Send a message1`.  
- El campo `horas_segundo_envio` es configurable para adaptar el tiempo entre correos.  
- Puedes agregar filtros adicionales en la consulta SQL si quieres segmentar los envíos.

---

## 📄 Licencia

Este proyecto está bajo la licencia [MIT](./LICENSE).  
Puedes usarlo, adaptarlo y distribuirlo libremente.

---

## 💡 Autor

Desarrollado por **[AndresRoblesB](https://github.com/AndresRoblesB)**  
Inspirado en flujos de bienvenida automatizados para e-commerce.
