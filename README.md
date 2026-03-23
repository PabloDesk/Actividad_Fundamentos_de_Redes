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

**¿Qué rol cumple el router cuando tú haces una petición a una API?
(Ej: `fetch("https://api.miapp.com")`)**

Cuando haces un fetch("https://api.miapp.com"), el Router actúa como un intermediario inteligente realizando estas tres funciones críticas:
- Enrutamiento (El Mapa): El router recibe tu petición, lee la IP de destino de la API y decide que, como no está en tu casa, debe enviarla hacia Internet a través de tu proveedor (ISP).
- NAT/Traducción (El Camuflaje): Cambia tu IP privada (ej. 192.XXX.X.XX) por tu IP pública oficial. Esto es vital porque la API no sabe cómo responder a una dirección privada, solo a la pública del router.
- Gestión de Puertos (El Retorno): El router anota en una tabla: "El puerto 5001 está esperando respuesta de la API para la laptop de Juan". Cuando llega el JSON de la API, el router mira esa nota y te entrega los datos solo a ti.


- ¿Por qué un switch es importante en un backend o data center?
- ¿Qué problemas de red podrían afectar tu aplicación aunque tu código esté “correcto”?

👉 Aquí deben pensar como developers, no como técnicos de redes.

---

### 🌍 4. Caso práctico real

Escenario:

> “Tu aplicación está en producción, pero los usuarios no pueden acceder.”
> 

Responder:

- ¿Podría ser problema de router? ¿por qué?
- ¿Podría ser problema de switch? ¿por qué?
- ¿Cómo distinguir si es problema de red o de código?

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

## 💡 Cierre (tu discurso final)

> “Un developer que entiende redes no solo programa…
> 
> 
>
