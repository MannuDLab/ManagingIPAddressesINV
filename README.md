Welcome to this Investigation Repository!

# 🌐 Redes para Developers — Guía práctica
 
---
 
## 🧩 1. ¿Qué es una dirección IP?
 
| Concepto | Definición simple | Nivel técnico breve | Ejemplo |
|----------|------------------|---------------------|---------|
| **IP** | Es la dirección de un dispositivo en internet | Identifica y permite enviar datos entre equipos | `192.168.1.10` |
| **IPv4** | Versión antigua y más común de IP | Usa números separados por puntos (32 bits) | `8.8.8.8` |
| **IPv6** | Versión nueva con muchas más direcciones | Usa números y letras (128 bits) | `2001:0db8::1` |
 
---
 
## ⚙️ 2. Tipos de IP
 
### 🔹 Diferencias
 
**IP pública vs privada**
- **Pública:** visible en internet
- **Privada:** usada dentro de tu red (casa/oficina)
 
**IP estática vs dinámica**
- **Estática:** no cambia
- **Dinámica:** cambia automáticamente
 
### 🔹 Tabla
 
| Tipo | ¿Qué es? | ¿Cuándo se usa? | Ejemplo real |
|------|----------|-----------------|--------------|
| **Pública** | IP visible en internet | Para que otros accedan a tu servidor | IP de tu router |
| **Privada** | IP dentro de tu red | Comunicación entre dispositivos internos | `192.168.1.5` |
| **Estática** | IP fija que no cambia | Servidores, APIs, producción | Servidor web |
| **Dinámica** | IP que cambia sola | Usuarios comunes en casa | WiFi del hogar |
 
---
 
## 💻 3. ¿Qué IP usa tu backend?
 
### 🖥️ En local (localhost)
 
IP: `127.0.0.1`
 
También puedes usar:
- `localhost`
- `::1` (IPv6)
 
👉 Ejemplo real:
```
http://localhost:3000
http://127.0.0.1:3000
```
> 📌 Esto significa: el servidor está corriendo en **TU** propio computador
 
---
 
### 🌍 En producción (internet)
 
IP: IP pública del servidor o un dominio
 
👉 Ejemplo real:
```
https://api.midominio.com
https://34.120.55.210
```
> 📌 Aquí el backend está en otro computador (servidor en la nube)
 
---
 
### 🔹 ¿Por qué no puedes acceder a localhost desde otro computador?
 
> 👉 `localhost` = **"este mismo computador"**
 
Si otro PC intenta:
```
http://localhost:3000
```
❌ Se conecta a **su propio** localhost, no al tuyo
 
| Situación | Resultado |
|-----------|-----------|
| Tu PC con backend en `localhost:3000` | ✅ Funciona para ti |
| Tu amigo intenta `localhost:3000` | ❌ No funciona |
 
✅ **¿Cómo sí podría acceder?**
 
Usando tu IP local:
```
http://192.168.1.50:3000
```
Pero **SOLO** si:
- El servidor permite conexiones externas (`0.0.0.0`)
- Están en la misma red WiFi
 
---
 
### 🔹 ¿Qué pasa cuando haces `npm run dev`?
 
```bash
npm run dev
```
Resultado típico:
```
Local:   http://localhost:5173
Network: http://192.168.1.50:5173
```
 
**🔍 ¿Qué está pasando detrás?**
- Se inicia un servidor (Node, Vite, Next, etc.)
- Escucha en una IP y puerto
- Por defecto: IP `127.0.0.1` — Puerto: `3000`, `5173`, etc.
 
| Configuración | Resultado |
|---------------|-----------|
| `127.0.0.1` | 🔒 Solo tú puedes acceder |
| `0.0.0.0` | ✅ Otros dispositivos pueden entrar |
 
Para exponer a la red:
```bash
vite --host
# o
npm run dev -- --host
```
 
---
 
### 🔹 ¿Qué IP usa una base de datos en la nube?
 
| Proveedor | Endpoint ejemplo |
|-----------|-----------------|
| **AWS RDS** | `mydb.abc123xyz.us-east-1.rds.amazonaws.com` |
| **MongoDB Atlas** | `cluster0.mongodb.net` |
| **Supabase** | `db.supabase.co` |
 
> 📌 No siempre te conectas con IP directa. Usas DNS porque la IP puede
> cambiar y es más seguro.
 
---
 
### 🧠 Resumen rápido
 
| Caso | IP usada | Acceso externo |
|------|----------|----------------|
| Local (localhost) | `127.0.0.1` | ❌ No |
| Red local | `192.168.x.x` | ⚠️ Solo LAN |
| Producción | IP pública / dominio | ✅ Sí |
| DB en la nube | Endpoint (DNS) | ✅ Sí |
 
---
 
## 🐛 4. Debugging: Frontend no conecta al backend
 
### 🔹 ¿Podría ser problema de IP?
 
> 👉 **Sí, es una de las causas MÁS comunes**
 
**💥 Ejemplo clásico (error):**
```
Frontend → http://localhost:5173
Backend  → http://localhost:3000
```
✅ Funciona en TU navegador
❌ No funciona desde otro dispositivo
 
**✅ Solución:**
```
http://192.168.1.50:3000
```
 
---
 
### 🔹 Orden de debugging (checklist PRO)
 
1. **¿El backend está corriendo?**
   ```bash
   npm run dev
   ```
   Verificar: ¿está levantado? ¿en qué puerto?
 
2. **URL que usa el frontend**
   ```js
   fetch("http://localhost:3000/api")
   ```
   ¿Es `localhost` o una IP? ¿Es el puerto correcto?
 
3. **Probar el backend directamente** (navegador o Postman)
   ```
http://localhost:3000/api
   ```
   ✔️ Si responde → backend OK | ❌ Si no → problema backend
 
4. **Revisar la red** — Backend debe estar en `0.0.0.0`, no en `127.0.0.1`
 
5. **Consola del navegador (F12)** — Errores típicos:
   - `ERR_CONNECTION_REFUSED`
   - `CORS error`
   - `Failed to fetch`
 
---
 
### 🔹 Errores típicos
 
| # | Error | Causa | Solución |
|---|-------|-------|----------|
| 1 | Localhost incorrecto | Solo funciona en tu PC | Usar IP local o dominio |
| 2 | Puerto incorrecto | Frontend → `3000`, backend → `3001` | Verificar y alinear puertos |
| 3 | Backend en `127.0.0.1` | Nadie más puede conectarse | `server.listen(3000, "0.0.0.0")` |
| 4 | CORS bloqueando | Política del navegador | `app.use(cors())` en Express |
| 5 | Firewall / antivirus | Bloquea conexiones externas | Revisar Windows Defender / router |
| 6 | Ruta incorrecta | `/api/users` ❌ vs `/users` ✅ | Verificar rutas del backend |
| 7 | HTTPS vs HTTP | Navegador bloquea mezcla | Unificar protocolo |
 
```js
// Solución CORS (Node/Express)
import cors from "cors"
app.use(cors())
```
 
> 💡 **Frase clave:** *"¿Estoy apuntando al lugar correcto desde el dispositivo correcto?"*
 
---
 
## 🧪 5. DHCP y DNS — Lo que todo developer debe saber
 
### ¿Qué hace DHCP?
 
**DHCP (Dynamic Host Configuration Protocol)** es el protocolo que asigna
automáticamente una dirección IP a tu dispositivo cuando se conecta a una red.
Sin él, tendrías que configurar manualmente la IP de cada computador, celular
o tablet. También entrega información extra como la puerta de enlace
predeterminada (el router) y la dirección del servidor DNS.
 
El proceso funciona en **4 pasos** llamados **DORA**:
 
1. **Discover** — Tu dispositivo grita: *"¡Hay algún servidor DHCP en la red?"*
2. **Offer** — El servidor responde: *"¡Sí! Te ofrezco la IP 192.168.1.45"*
3. **Request** — Tu dispositivo acepta: *"¡Perfecto, la quiero!"*
4. **Acknowledge** — El servidor confirma: *"¡Listo, es tuya por X tiempo!"*
 
---
 
### ¿Qué hace DNS?
 
**DNS (Domain Name System)** traduce nombres de dominio legibles por humanos
(como `google.com`) a direcciones IP numéricas que las máquinas entienden
(como `142.250.191.14`). Es esencialmente el directorio telefónico de
Internet: tú buscas el nombre, él te da el número.
 
---
 
### ¿Qué pasa cuando escribes `google.com` en el navegador?
 
Flujo paso a paso:
 
1. **Escribes** `google.com` → el navegador detecta que es una URL, no una búsqueda
2. **Revisa su caché local** → ¿ya sé la IP de Google? Si sí, la usa directamente
3. **Consulta al DNS Resolver** (tu proveedor o router) → *"¿Cuál es la IP de google.com?"*
4. **El Resolver pregunta al Root Server** → el root dice: *"pregunta al servidor .com"*
5. **Pregunta al servidor TLD (.com)** → responde: *"pregunta al servidor autoritativo de Google"*
6. **Servidor autoritativo de Google** responde con la IP real: ej. `142.250.191.14`
7. **Tu navegador establece conexión TCP/HTTPS** con esa IP
8. **Google te devuelve el HTML** → el navegador lo renderiza y ves la página ✅
 
> 💡 Todo esto ocurre en **milisegundos**, aunque internamente son muchos pasos.
 
---
 
## 🔄 6. Analogía obligatoria
 
| Concepto | Analogía |
|----------|----------|
| **IP** | 📍 La **dirección exacta de tu casa** (`Av. Siempreviva 742, Santiago`) — es única y permite que el cartero llegue directo a ti |
| **DNS** | 📱 Los **contactos de tu celular** — en vez de memorizar `+56 9 1234 5678`, guardas `"Mamá"` y el sistema busca el número por ti |
| **DHCP** | 🏘️ El **administrador del condominio** que asigna automáticamente un departamento a cada residente nuevo sin que tenga que pedirlo |
 
### Ejemplo conectado
 
> Imagina una ciudad nueva donde llegan habitantes todos los días:
> - El **DHCP** es quien les asigna casa automáticamente al llegar 🏠
> - La **IP** es el número de esa casa (su dirección única) 📍
> - El **DNS** es la agenda donde buscas *"casa de Google"* y te da el número exacto 📖
 
---
 
## 🧠 BONUS — Nivel Bootcamp Pro
 
---
 
### 🔹 ¿Qué es NAT y por qué es importante?
 
**NAT (Network Address Translation)** es la técnica que permite que
**múltiples dispositivos compartan una sola IP pública**. Es lo que hace
tu router cuando varios celulares y computadores de tu casa usan el
mismo internet.
 
**¿Por qué existe?**
> IPv4 solo tiene ~4.3 mil millones de direcciones disponibles.
> Como hay más dispositivos que IPs, NAT permite que muchos compartan una sola. 🌐
 
**¿Cómo funciona paso a paso?**
 
1. Tu PC (`192.168.1.10`) hace una petición a `google.com`
2. El router **reemplaza** tu IP privada por su IP pública (ej. `200.45.100.5`)
3. Google responde a esa IP pública
4. El router recibe la respuesta y la **reenvía** a tu PC interna
5. Tú ves el resultado en el navegador ✅
 
> 📌 Este proceso es completamente invisible para ti. Ocurre en cada
> petición que haces desde tu red.
 
**Analogía rápida:**
 
> Es como una empresa con una sola línea telefónica principal.
> Todos los empleados llaman por esa línea, pero la recepcionista
> sabe a quién reenviar cada llamada. 📞
 
---
 
### 🔹 ¿Cómo funciona una IP en Docker o contenedores?
 
Cuando corres un contenedor Docker, se crea una **red virtual privada**
dentro de tu máquina. Cada contenedor recibe su propia IP privada
gestionada automáticamente por Docker, sin tocar tu red real.
 
**Red por defecto en Docker:**
 
```
Tu PC (host)
└── Red virtual Docker (bridge)
    ├── Contenedor A → 172.17.0.2
    ├── Contenedor B → 172.17.0.3
    └── Contenedor C → 172.17.0.4
```
 
**Tipos de red en Docker:**
 
| Tipo | ¿Qué hace? | ¿Cuándo usarla? |
|------|-----------|-----------------|
| `bridge` | Red interna privada (default) | Desarrollo local |
| `host` | Comparte la IP del PC anfitrión | Cuando necesitas acceso directo |
| `none` | Sin red | Contenedores aislados |
 
**⚠️ Problema típico:**
 
```bash
# Esto NO funciona desde un contenedor:
fetch("http://localhost:3000")
 
# ❌ Porque localhost dentro del contenedor = el propio contenedor
# ✅ Debes usar:
fetch("http://host.docker.internal:3000")  # apunta al PC anfitrión
```
 
**Puerto mapping (la clave):**
 
```bash
docker run -p 8080:3000 mi-app
#              ↑      ↑
#         Puerto PC  Puerto contenedor
```
 
> 📌 El contenedor vive en su propia red. El `-p` es el "puente" que
> conecta tu PC con el interior del contenedor.
 
---
 
### 🔹 ¿Qué pasa con las IP en AWS (EC2)?
 
En AWS, cada instancia EC2 recibe **dos IPs automáticamente**: una
privada (siempre) y una pública (opcional).
 
| Tipo de IP | ¿Qué es? | ¿Cambia al reiniciar? | Ejemplo |
|------------|----------|----------------------|---------|
| **Privada** | IP interna dentro de AWS (VPC) | ❌ No cambia | `172.31.7.164` |
| **Pública** | IP expuesta a internet | ✅ Sí cambia | `54.210.45.200` |
| **Elastic IP** | IP pública fija y permanente | ❌ No cambia | `52.90.100.5` |
 
**¿Cuándo usar cada una?**
 
```
Privada    → Comunicación entre servicios dentro de AWS (DB ↔ Backend)
Pública    → Acceso temporal desde internet (desarrollo/testing)
Elastic IP → Producción real (dominios, APIs públicas)
```
 
**Flujo típico en producción:**
 
```
Usuario
  ↓
Internet
  ↓
Elastic IP (IP fija pública)
  ↓
EC2 Instance
  ↓
Base de datos (IP privada, nunca expuesta) 🔒
```
 
> 💡 **Tip importante:** La IP pública de EC2 **cambia cada vez que
> reinicias** la instancia. Si necesitas una IP fija para tu dominio
> o API, **siempre usa Elastic IP** en producción.
 
---
 
### 🧠 Resumen BONUS
 
| Concepto | ¿Qué resuelve? | Analogía |
|----------|---------------|----------|
| **NAT** | Muchos dispositivos con una sola IP pública | Recepcionista que redirige llamadas |
| **Docker IP** | Red virtual privada por contenedor | Cada contenedor es su propia "oficina" aislada |
| **EC2 Privada** | Comunicación interna en AWS | Interno del edificio, nadie afuera lo ve |
| **EC2 Pública** | Acceso desde internet | Puerta principal del edificio |
| **Elastic IP** | IP pública fija en producción | Dirección permanente que no cambia |
