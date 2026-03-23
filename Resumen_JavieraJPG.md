# 🌐 Networking básico para desarrolladores

> Entender la red te permite saber si un error es **de código o de infraestructura**.

---

# 🧩 1. ¿Qué es cada dispositivo?

| Concepto   | Definición simple                           | Nivel técnico (breve)                           | Ejemplo real                    |
| ---------- | ------------------------------------------- | ----------------------------------------------- | ------------------------------- |
| **Router** | Conecta tu red con otras redes (internet)   | Capa 3. Usa IP, rutas y NAT                     | Router de casa / AWS Gateway    |
| **Switch** | Conecta dispositivos dentro de la misma red | Capa 2. Usa MAC para enviar datos correctamente | Switch en oficina o data center |
| **Hub**    | Envía todo a todos sin filtrar              | Capa 1. Solo repite señal (obsoleto)            | Equipos antiguos                |

---

# ⚙️ 2. ¿Cómo funciona realmente?

## 🔹 Router (explicado simple)

Cuando envías una petición:

```js
fetch("https://api.miapp.com")
```

El router:

1. Recibe el paquete
2. Lee la **IP destino**
3. Decide por dónde enviarlo (tabla de rutas)
4. Aplica **NAT** (IP privada → pública)
5. Lo envía a internet

👉 Es como “el que decide el camino”.

---

## 🔹 Switch (explicado simple)

El switch:

1. Aprende qué dispositivo está en cada puerto (MAC)
2. Cuando llega un dato:

   * Si conoce el destino → lo envía directo
   * Si no → lo manda a todos (temporalmente)

👉 Es como “el que entrega el mensaje dentro de la red”.

---

## 🔹 Hub (por qué no se usa)

* No sabe a quién enviar
* Manda todo a todos
* Genera tráfico innecesario

👉 Es ineficiente y fue reemplazado por switches.

---

# 💻 3. ¿Qué pasa cuando haces una petición?

## 🔄 Flujo simple

```text
Tu app → DNS → Router → Internet → API → Respuesta
```

### Paso a paso:

1. DNS traduce el dominio a IP
2. El router envía el paquete
3. Viaja por internet
4. Llega a la API
5. La respuesta vuelve

---

# 🏗️ 4. ¿Por qué importa esto en backend?

* Los servidores se comunican por red
* Un error de red puede parecer error de código
* Se usan switches para:

  * conectar servidores
  * reducir tráfico
  * separar entornos (VLANs)

---

# ⚠️ 5. Problemas comunes

| Problema  | Qué pasa                 |
| --------- | ------------------------ |
| DNS mal   | No encuentra la API      |
| NAT mal   | No salen o vuelven datos |
| Red lenta | Timeouts                 |
| VLAN mal  | Servicios no se ven      |
| Firewall  | Bloquea conexión         |

---

# 🧪 6. Caso real

> “La app no funciona en producción”

## 🔍 Posibles causas

**Router:**

* Ruta caída
* NAT roto
* Gateway incorrecto

**Switch:**

* VLAN mal configurada
* Puerto incorrecto
* Error en tabla MAC

---

# 🛠️ 7. Cómo diagnosticar

```bash
ping api.miapp.com
traceroute api.miapp.com
curl https://api.miapp.com
```

## 📌 Regla clave

* ❌ No llega tráfico → problema de red
* ⚠️ Llega tráfico pero falla → problema de código

---

# 🧩 8. Analogías (2 formas de entenderlo)

## 📦 Analogía 1: Correos

* **Router** → oficina que envía cartas entre ciudades
* **Switch** → persona que entrega la carta dentro del edificio
* **Hub** → alguien que grita la carta a todos

---

## 🏢 Analogía 2: Edificio

* **Router** → portería que conecta con el exterior
* **Switch** → conserje que sabe en qué departamento estás
* **Hub** → altavoz que habla a todo el edificio

---

# 📚 9. Glosario (términos importantes)

| Término       | Explicación simple                                |
| ------------- | ------------------------------------------------- |
| **IP**        | Dirección única de un dispositivo en la red       |
| **MAC**       | Identificador físico de un dispositivo            |
| **DNS**       | Traduce nombres (google.com → IP)                 |
| **NAT**       | Convierte IP privada en pública                   |
| **Broadcast** | Enviar mensaje a todos                            |
| **VLAN**      | Separar redes dentro de una misma infraestructura |
| **Paquete**   | Unidad de datos que viaja por la red              |
| **Latencia**  | Tiempo que tarda en viajar un dato                |

---

# 🎯 10. Resumen rápido

* **Router** → conecta redes (usa IP)
* **Switch** → organiza red interna (usa MAC)
* **Hub** → envía todo a todos (obsoleto)

---

# 📌 Conclusión

Si entiendes esto, puedes:

* Detectar si el problema es red o backend
* Entender mejor cómo viajan los datos
* Ser mejor desarrollador en entornos reales

---
---

## 11. 🛒 Analogía: Mercado Libre

Imagina que haces una compra en Mercado Libre:

1. Buscas un producto y haces clic en **comprar**  
2. El sistema encuentra al vendedor  
3. El producto viaja hasta tu casa  

### 🔹 Equivalente en red

- **Tu app (fetch)** → haces la compra  
- **DNS** → encuentra la dirección del servidor  
- **Router** → decide por dónde enviar el paquete  
- **Internet** → camino por donde viajan los datos  
- **API / Servidor** → vendedor que recibe el pedido  
- **Respuesta** → el producto llega a tu casa  

---

### 🔹 ¿Dónde entran los dispositivos?

- **Router** → logística que define la ruta del envío  
- **Switch** → centro de distribución que entrega al destino correcto dentro de la red  
- **Hub** → todos los paquetes se envían a todos (ineficiente y desordenado)  

---

### 📌 Idea clave

> La red funciona como un sistema de envíos:  
> - Si el paquete no llega → problema de red  
> - Si llega pero falla → problema de backend  

---
