# Redes y dispositivos

## ¿Qué es cada dispositivo?

Completar tabla:

| Concepto | Definición simple | Nivel técnico (breve) | Ejemplo real |
| --- | --- | --- | --- |
| Router | Repite la señal a todos los dispositivos conectados. Como gritar en una sala: todos escuchan, aunque el mensaje sea para uno. | Opera en capa 1 (física). No distingue dispositivos; hace broadcast de cada trama a todos los puertos. Genera colisiones (CSMA/CD). | Red de oficina antigua: 5 PCs comparten un hub. Si PC1 envía un archivo a PC2, las otras 3 PCs también reciben los datos. |
| Switch | Envía datos solo al dispositivo correcto. Como un cartero que lleva la carta a la dirección exacta. | Opera en capa 2 (enlace). Aprende direcciones MAC y crea una tabla para reenviar tramas solo al puerto destino. | Red de oficina moderna: PC1 envía archivo a PC2. El switch lo dirige solo a PC2. Las demás PCs no ven nada. |
| Hub | Conecta redes distintas y elige el mejor camino. Como un GPS: sabe cómo llegar de una ciudad a otra. | Opera en capa 3 (red). Usa direcciones IP y tablas de enrutamiento para mover paquetes entre subredes/Internet. | Tu casa: el router conecta tu red local (PCs, celulares) con Internet, asignando IPs a cada dispositivo.  |

## ¿Cómo funciona realmente?

### ¿Qué hace el router con los paquetes de datos?
El router trabaja con direcciones IP. Cuando llega un paquete, lee la IP de destino y consulta su tabla de enrutamiento para decidir por qué camino mandarlo. Si el destino está en otra red (como Internet), lo reenvía al siguiente salto - que puede ser otro router —, luego, al siguiente salto, y así hasta llegar a destino. También hace NAT: traduce la IP privada de tu PC (ej. 192.168.1.5) a la IP pública de tu casa, para que el servidor web sepa a dónde responder.

### ¿Cómo decide un switch a dónde enviar la información?
El switch aprende solo. La primera vez que un dispositivo envía algo, el switch anota en su tabla MAC qué dirección física `(AA:BB:CC:...)` está conectada a qué puerto físico. Después, cuando llega un paquete con una MAC de destino conocida, lo manda directo a ese puerto y solo a ese. Si la MAC no está en la tabla aún, hace broadcast (lo manda a todos) hasta aprenderla.

### ¿Por qué el hub es considerado obsoleto?
Tres razones concretas: primero, el broadcast constante desperdicia ancho de banda — todos reciben todo aunque no les corresponda. Segundo, genera colisiones (CSMA/CD) que obligan a retransmitir paquetes, degradando el rendimiento con cada dispositivo que se suma. Tercero, es un riesgo de seguridad: cualquier PC conectada al hub puede ver el tráfico de todas las demás con un sniffer (herramienta que captura y lee el tráfico de red). El switch resuelve los tres problemas sin costo adicional relevante hoy en día.

## Conexión directa con desarrollo
### ¿Qué rol cumple el router cuando tú haces una petición a una API?
> (Ej: `fetch("https://api.miapp.com")`)

El código que uno escribe no sabe nada de routers — solo lanza la petición. Pero por debajo, el router de la red local recibe ese paquete, ve que el destino (`api.miapp.com`) no está en la red local, lo manda al ISP, y desde ahí salta por varios routers hasta llegar al servidor. De vuelta hace el camino inverso. Todo eso ocurre antes de que el `then()` o `await` siquiera reciba algo. Si el router falla o la ruta es lenta, la app espera — y si no se manejó el timeout, se cae.

### ¿Por qué un switch es importante en un backend o data center?
En un data center tienes decenas o cientos de servidores que se hablan entre sí constantemente: tu servidor de app le pide datos al servidor de base de datos, que le responde, que le avisa al servidor de caché, etc. Todo eso es tráfico interno. El switch garantiza que cada conversación vaya directo de A a B sin molestar a C, D y E. Sin él (o con uno mal configurado), el tráfico interno se vuelve un caos que satura la red antes de que siquiera llegue una petición externa. En la práctica, la latencia entre tu API y tu base de datos depende directamente de qué tan bien esté configurado el switch entre ellos.

### ¿Qué problemas de red podrían afectar tu aplicación aunque tu código esté “correcto”?
* **Timeout (Red lenta o caída)**: Tu fetch espera indefinidamente. Sin AbortController o timeout configurado, la UI se congela sin explicación.
* **CORS (Config de red/servidor)**: El router/servidor bloquea tu request por política de origen. Tu código es correcto, pero la red dice no.
* **DNS lento (Resolución de dominio)**: Antes de que salga el paquete, tu app debe resolver "api.miapp.com" a una IP. Si el DNS tarda, todo tarda.
* **Packet loss (Paquetes perdidos)**: TCP reintenta automáticamente, pero eso aumenta la latencia. Tu API parece lenta sin estarlo realmente.
* **Rate limiting**: El router o servidor corta tus requests si superas el límite → 429 Too Many Requests.

La lección clave es que como dev tu código termina en el momento en que sale de tu máquina — de ahí en adelante manda la red, y tienes que escribir código que lo asuma: timeouts, reintentos, manejo de errores HTTP, y nunca confiar en que la red siempre va a responder.

## Caso práctico real
Escenario:
> “Tu aplicación está en producción, pero los usuarios no pueden acceder.”

### ¿Podría ser problema de router? ¿por qué?
Sí, y es de los más comunes. Si el router falla, ningún paquete externo llega a tu servidor — los usuarios ven un timeout o "no se puede acceder al sitio" sin ningún error HTTP (porque el request nunca llega). También puede ser un problema parcial: el router está vivo pero una ruta específica está caída, entonces algunos usuarios acceden (los que vienen por otra ruta) y otros no. Esto es especialmente raro de diagnosticar porque parece intermitente.

### ¿Podría ser problema de switch? ¿por qué?
Sí, pero se manifestaría distinto. Si el switch interno del data center falla, tu servidor web puede estar vivo y responder pings, pero no puede hablar con tu base de datos ni con otros servicios internos. El resultado desde afuera: el usuario llega a tu app, pero la app no puede completar ninguna operación y devuelve errores 500. Tu código no cambió nada — el switch simplemente cortó la conversación interna.

### ¿Cómo distinguir si es problema de red o de código?
La regla práctica como dev es: primero verifica que el servidor exista en la red (`ping`, `traceroute`), luego que el puerto esté escuchando (`curl -v`, `telnet`), y recién después miras los logs. Si el servidor no responde antes de llegar a los logs, el problema es de red y no de tu código — no pierdas tiempo ahí.
Un detalle importante: los errores de red casi nunca dan un código HTTP de vuelta. Si ves un `500` o un `503`, el servidor al menos está vivo y el problema probablemente es de código o de un servicio interno (base de datos, caché). Si ves timeout sin respuesta o `ERR_CONNECTION_REFUSED`, ahí sí sospechas de la red primero.

## Analogía para entender de verdad: edificio de oficinas
- Router = el ascensor que conecta pisos distintos (o edificios distintos): sin él no puedes salir de tu piso.
- Switch = la recepción de tu piso que sabe exactamente en qué oficina está cada persona.
- Hub = el altoparlante del piso que anuncia todos los mensajes a todas las oficinas, aunque solo le importe a una.

## Cierre
### ¿Dónde entra un load balancer en todo esto?
El load balancer vive "delante" de tus servidores y distribuye el tráfico entrante entre varias instancias de tu app. Piénsalo como una capa nueva en el camino que ya conoces: el router lleva el paquete hasta tu infraestructura, y ahí el load balancer decide cuál de tus servidores lo atiende.

La analogía: si el switch es el recepcionista que sabe a qué oficina ir, el load balancer es el supervisor que decide cuál de tres recepcionistas idénticos está menos ocupado hoy. Opera en capa 4 (TCP) o capa 7 (HTTP), lo que significa que puede tomar decisiones más inteligentes que un switch: por ejemplo, mandar todas las requests de `/api/video` al servidor con más RAM, y las de `/api/auth` a otro.

### ¿Qué relación tiene esto con cloud (AWS, Azure, etc.)?
En AWS, Azure o GCP, todo lo que vimos existe — solo que ya no lo configuras tú físicamente. La nube abstrae el hardware pero los conceptos son exactamente los mismos:

| Concepto físico | AWS | Para qué sirve |
|---|---|---|
| Router | Internet Gateway + Route Table | Conectar VPC a Internet |
| Switch | VPC + Subnets | Red privada interna |
| Load balancer | ALB / NLB | Distribuir tráfico HTTP |
| Firewall | Security Groups + NACLs | Controlar quién entra/sale |

La diferencia clave es que en la nube no ves el hardware — configuras todo esto desde una consola o con código (Infrastructure as Code, como Terraform). Pero cuando algo falla en producción y ves que tu Lambda no llega a tu RDS, estás mirando exactamente el mismo problema de switch/red interna que vimos antes, solo que ahora se llama "subnet routing" o "security group mal configurado".
