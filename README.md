# Actividad: Fundamentos de Redes

---

## 🎯 Objetivo

Comprender cómo funcionan los componentes básicos de red (**router, switch y hub**) y **por qué son relevantes para el trabajo de un desarrollador** (backend, frontend, DevOps).

---

## 🚀 Contexto (para decir en clase)

> “Como desarrolladores, no trabajamos en el vacío. Nuestro código vive en una red: servidores, APIs, bases de datos, internet…
> 
> 
> Si no entendemos cómo viaja la información, estamos programando a ciegas.”
> 

---

## 👥 Modalidad

- Grupos de 3 **a 4 personas**
- Tiempo: **30–40 minutos investigación + 10 min presentación**
- Resultado: **Mini presentación + ejemplo aplicado**

---

## 🔍 Investigación guiada (NO al azar)

Cada grupo debe responder **TODAS** las siguientes preguntas:

---

### 🧩 1. ¿Qué es cada dispositivo?

Completar tabla:

| Concepto | Definición simple | Nivel técnico (breve) | Ejemplo real |
| --- | --- | --- | --- |
| Router | Es básicamente el repartidor de señales de tu casa. Recibe el internet que viene de la calle y decide a qué aparato enviárselo (si a tu cel, a la tele o a la consola). | Es el puente al exterior. Conecta tu red local con Internet (o con otras redes) usando direcciones IP. | El equipo que te da tu proveedor de internet para que todos tus dispositivos tengan Wi-Fi y salida a la web. |
| Switch | Es como una extensión de enchufes para el internet: conectas un solo cable del router y sacas 5 o 10 entradas más para conectar por cable todo lo que quieras. | Es el conector interno. Une dispositivos (PC, impresoras, servidores) dentro de una misma red usando direcciones MAC. | Una "regleta" de red en una oficina donde conectas 20 computadoras por cable para que compartan archivos entre ellas. |
| Hub | Manda los datos a todos y deja que los aparatos decidan cuál es para ellos. Es lento porque satura la red con tráfico innecesario. | Es el "aduanero". Conecta tu red local con el mundo exterior (Internet) usando direcciones IP. | El aparato que te da Wi-Fi y te permite entrar a Google. |

👉 Regla:

- Máximo **3 líneas por definición**
- Debe poder explicarse a alguien sin conocimientos técnicos

---

### ⚙️ 2. ¿Cómo funciona realmente?

Responder:

**¿Qué hace el router con los paquetes de datos?**
- El router es el encargado de leer las direcciones IP de cada paquete para decidir si deben viajar por el mundo o quedarse en casa, asegurándose de que nadia se pierda en el camino.

**¿Cómo decide un switch a dónde enviar la información?**
- El Switch decide a dónde enviar la información porque se memoriza qué dirección MAC está asociada a cada cable físico. Así, el tráfico es privado y rápido, a diferencia del antiguo "Hub" que le gritaba a todo el mundo todo el tiempo.

**¿Por qué el hub es considerado obsoleto?**
- El Hub es obsoleto porque utiliza una tecnologia llamada Broadcast, le envía tus datos a todo el mundo, aunque no sean los destinatarios. El Switch hace el mismo trabajo pero de forma inteligente, privada y mucho más rápida, permitiendo que todos hablen al mismo tiempo sin estorbarse.

👉 Tip: aquí deben aparecer conceptos como:

- IP
- MAC
- Broadcast

---

### 💻 3. Conexión directa con desarrollo (CLAVE)

Responder con ejemplos concretos:

#### ¿Qué rol cumple el router cuando tú haces una petición a una API? (Ej: `fetch("https://api.miapp.com")`)

Cuando haces un fetch("https://api.miapp.com"), el Router actúa como un intermediario inteligente realizando estas tres funciones críticas:
- Enrutamiento (El Mapa): El router recibe tu petición, lee la IP de destino de la API y decide que, como no está en tu casa, debe enviarla hacia Internet a través de tu proveedor (ISP).
  
- NAT/Traducción (El Camuflaje): Cambia tu IP privada (ej. 192.XXX.X.XX) por tu IP pública oficial. Esto es vital porque la API no sabe cómo responder a una dirección privada, solo a la pública del router.
  
- Gestión de Puertos (El Retorno): El router anota en una tabla: "El puerto 5001 está esperando respuesta de la API para la laptop de Juan". Cuando llega el JSON de la API, el router mira esa nota y te entrega los datos solo a ti.

#### ¿Por qué un switch es importante en un backend o data center?

Como Developer o Arquitecto de Software, tendemos a ver la red como una "nube" abstracta, pero en el backend (especialmente en racks de servidores o clusters de Kubernetes), el Switch es la pieza que define la viabilidad de tu arquitectura. Aquí explico por qué es vital para developers:

- **Latencia de Microservicios (Este-Oeste)**: En una arquitectura de microservicios, el tráfico Este-Oeste (comunicación entre servidores internos) es masivo. Si tu servicio de Orders necesita consultar al servicio de Inventory:
    - El Switch permite que esa petición viaje a velocidad de nanosegundos por el cable físico.
    - Al usar direcciones MAC, el Switch sabe exactamente en qué puerto del rack está el servidor de inventario, eliminando saltos innecesarios que añadirían milisegundos fatales a tu Response Time.

- **Segmentación por VLANs (Seguridad de Capa 2)**: Desde el código, confiamos en que la base de datos es privada. El Switch refuerza esto físicamente:
    - Podemos configurar VLANs para que, aunque el servidor web y la base de datos estén enchufados al mismo aparato, el tráfico de la DB esté aislado.
    - Si un atacante compromete tu frontend (Node.js/Python), el Switch impide que "escuche" o "sniffee" el tráfico de otros servidores mediante aislamiento de puertos.

- **Alta Disponibilidad (LACP / Bonding)**: Como devs, queremos que nuestro backend nunca muera. El Switch permite hacer Link Aggregation:
    - Podemos conectar un servidor al Switch con dos cables a la vez actuando como uno solo.
    - Si un cable falla, el Switch redirige el tráfico al otro instantáneamente. Tu código ni siquiera se entera de que hubo un problema de infraestructura; no hay excepciones de Connection Reset.

- **Soporte para el Plano de Control (Kubernetes/Docker)**: Cuando despliegas un cluster, los nodos necesitan comunicarse constantemente para saber quién está vivo (Heartbeats). Un Switch de alta gama (gestionable) asegura que estos mensajes tengan prioridad, evitando que un pico de tráfico en tu App sature el canal y cause que el cluster piense que un nodo se "cayó" (falsos positivos de Node Not Ready).


#### ¿Qué problemas de red podrían afectar tu aplicación aunque tu código esté “correcto”?

Como Developer, es frustrante cuando los logs dicen que tu código está perfecto, pero los usuarios reportan errores o lentitud. Aquí tienes los "fantasmas" de la red que rompen tu app sin que toques una sola línea de código:

- **Latencia y "Jitter" (El asesino de Microservicios)**: Tu código hace un fetch a otro servicio interno. Si el switch o router están saturados, la respuesta tarda 200ms en lugar de 5ms.
  - El problema: Tus timeouts de conexión empiezan a saltar.
  - Efecto: Cascada de errores (Circuit Breaker abierto) solo porque la red está "pesada".

- **Pérdida de Paquetes (Packet Loss)**: Si un cable está dañado o un switch es viejo, algunos paquetes de datos se pierden. TCP intentará retransmitirlos automáticamente.
  - El problema: Tú no verás un error de "conexión fallida", pero verás una lentitud inexplicable.
  - Efecto: Tu API parece "congelarse" aleatoriamente mientras el protocolo intenta recuperar los datos perdidos.

- **Agotamiento de Puertos (Port Exhaustion)**: Tu servidor tiene un límite de conexiones simultáneas hacia una base de datos o API externa.
  - El problema: Si el router o el firewall no cierran las conexiones inactivas rápido, te quedas sin "puertos" para abrir nuevas peticiones.
  - Efecto: Tu código lanza un Connection Refused o EADDRNOTAVAIL aunque el servidor de destino esté encendido y sano.

- **Configuración de MTU (Fragmentación)** Si tu router está configurado con un tamaño de paquete (MTU) más pequeño que el de tu servidor.
  - El problema: Los paquetes grandes de tu JSON se rompen en trozos más pequeños a mitad de camino.
  - Efecto: Algunas peticiones pequeñas funcionan bien, pero cuando envías un objeto grande, la conexión se queda "colgada" (Hanging) y nunca termina.

---

### 🌍 4. Caso práctico real

Escenario:

> “Tu aplicación está en producción, pero los usuarios no pueden acceder.”
> 

Responder:

1. ¿Podría ser problema de Router? SÍ

El router es el punto de entrada (Gateway). Si falla, tu app queda aislada del mundo.

    ¿Por qué?

        Falla de NAT: El router no sabe a qué servidor interno enviar el tráfico que llega de internet.

        Reglas de Firewall: Alguien actualizó una regla en el router y cerró el puerto 443 (HTTPS).

        Saturación de Banda Ancha: El enlace con el ISP está al 100%, causando que los paquetes de tus usuarios se descarten antes de entrar a tu red.

        IP Pública cambió: Si no tienes IP estática, el router cambió de dirección y tu DNS ahora apunta al vacío.

2. ¿Podría ser problema de Switch? SÍ

El switch es el sistema circulatorio interno. Si falla, tus servidores están vivos pero "mudos" entre ellos.

    ¿Por qué?

        Bucle de red (Broadcast Storm): Un error de cableado hace que los datos giren en círculos infinitos, saturando el switch y congelando la comunicación.

        VLAN mal configurada: Tu servidor web y tu base de datos quedaron en "habitaciones" digitales distintas y no pueden hablarse.

        Falla de puerto físico: El cable que conecta el balanceador de carga al switch murió o está dando errores de CRC (datos corruptos).

3. ¿Cómo distinguir si es Red o Código?

Usa la técnica de aislamiento de capas:

| Prueba | Si falla... | Si funciona... |
| --- | --- | --- |
| Ping a la IP pública | Problema de Router/ISP. | El router está vivo, el problema es interno. |
| Telnet/NC al puerto (443) | El Router/Firewall bloquea el paso. | La red permite el flujo, el problema es la App. |
| Cargar por IP (saltando DNS) | Problema de DNS, no de red ni código. | El DNS está bien. |
| Logs del servidor (Localhost) | Problema de Código (el proceso murió). | El código corre; el problema es cómo llega el tráfico. |
| Curl desde el Web al DB | Problema de Switch/VLAN (comunicación interna). | La red interna está sana. |



---

### 🧪 5. Analogía obligatoria (para entender de verdad)

Crear una analogía:

- Router =
- Switch =
- Hub =

👉 Ejemplo esperado:

- Router = “oficina de correos entre ciudades”
- Switch = “recepcionista que sabe exactamente a quién entregar”
- Hub = “persona que grita el mensaje a todos”

---

## 🎤 Presentación (10 min por grupo)

Cada grupo debe explicar:

1. Diferencia clave entre router, switch y hub
2. Un ejemplo real en desarrollo
3. Su analogía

---

## 🧠 BONUS

Responder:

- ¿Dónde entra un **load balancer** en todo esto?
- ¿Qué relación tiene esto con **cloud (AWS, Azure, etc.)**?

---

## 🧩 Entregable

Formato libre:

- README.md
- Slides (Gamma, PPT)

---

*fuentes: https://righteous-baron-17e.notion.site/Actividad-Fundamentos-de-Redes-32c4db47a25580479f48fe5f3dff71ef*

---

## 💡 Cierre (tu discurso final)

> “Un developer que entiende redes no solo programa…
> 
> 
>
