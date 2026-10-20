# Sistema de Gestión para la Liga Sanmartiniana de Fútbol

## 1. Definición del Dominio del Problema
El proyecto consiste en el desarrollo de la plataforma digital oficial para la **Liga Sanmartiniana de Fútbol**, orientada a digitalizar y centralizar la administración de torneos, clubes, planteles de jugadores, programación de partidos y estadísticas en tiempo real (tabla de posiciones, goleadores y valla menos vencida).

El objetivo principal es resolver la dispersión de información y la lentitud en la actualización de las fichas de los partidos y planillas arbitrales. En esta solución, la base de datos NoSQL orientada a documentos (MongoDB) permitirá almacenar las fichas de los encuentros con toda su información asociada (goles, tarjetas y cambios) en estructuras atómicas de rápida lectura, optimizando las consultas públicas de los hinchas y delegados sin sobrecargar el servidor con consultas complejas.

---

## 2. Esquema de Colecciones (Mockup JSON)

### Colección: `clubes`
```json
{
  "_id": { "$oid": "650c1f1e9b1d8e2a3c4b1001" },
  "nombre": "Liga San Martiniana de Futbol",
  "nombre_corto": "LSMF",
  "fundacion": 1927,
  "localidad": "San Martín",
  "estadio": "General San Martín",
  "colores": ["Rojo", "Blanco","Azul"],
  "delegado": {
    "nombre": "Carlos Gómez",
    "telefono": "+542634000000",
    "email": "ligasanmartiniana@gmail.com"
  }
}
{
  "_id": { "$oid": "650c1f1e9b1d8e2a3c4b1002" },
  "dni": "42123456",
  "nombre": "Matías",
  "apellido": "Rodríguez",
  "fecha_nacimiento": "2000-05-14",
  "club_id": { "$oid": "650c1f1e9b1d8e2a3c4b1001" },
  "posicion": "Delantero",
  "dorsal_habitual": 9,
  "estado": "habilitado",
  "fichaje_fecha": "2025-02-10"
}
{
  "_id": { "$oid": "650c1f1e9b1d8e2a3c4b1003" },
  "torneo": "Torneo Apertura 2026",
  "categoria": "Primera A",
  "fecha_numero": 5,
  "fecha_hora": "2026-08-23T15:30:00Z",
  "estadio": "General San Martín",
  "equipo_local": {
    "club_id": { "$oid": "650c1f1e9b1d8e2a3c4b1001" },
    "nombre": "Club La 60 Granaderos",
    "goles": 2
  },
  "equipo_visitante": {
    "club_id": { "$oid": "650c1f1e9b1d8e2a3c4b1004" },
    "nombre": "Club Alianza Ferrisi",
    "goles": 1
  },
  "estado": "finalizado",
  "incidencias": {
    "goles": [
      { "minuto": 12, "equipo_id": { "$oid": "650c1f1e9b1d8e2a3c4b1001" }, "jugador_id": { "$oid": "650c1f1e9b1d8e2a3c4b1002" }, "tipo": "jugada" },
      { "minuto": 45, "equipo_id": { "$oid": "650c1f1e9b1d8e2a3c4b1004" }, "jugador_id": { "$oid": "650c1f1e9b1d8e2a3c4b1009" }, "tipo": "penal" },
      { "minuto": 78, "equipo_id": { "$oid": "650c1f1e9b1d8e2a3c4b1001" }, "jugador_id": { "$oid": "650c1f1e9b1d8e2a3c4b1002" }, "tipo": "cabeza" }
    ],
    "tarjetas": [
      { "minuto": 30, "equipo_id": { "$oid": "650c1f1e9b1d8e2a3c4b1001" }, "jugador_id": { "$oid": "650c1f1e9b1d8e2a3c4b1002" }, "tipo": "amarilla" }
    ]
  },
  "terna_arbitral": {
    "arbitro_principal": "Juan Pérez",
    "asistente_1": "Roberto Díaz",
    "asistente_2": "Mariano Silva"
  }
}

## 3. Pruebas de Consultas MQL

### Consulta 1: Filtrado de Jugadores Goleadores

* **Problema de Negocio:** La Liga Sanmartiniana requiere obtener el listado de jugadores que han anotado más de 5 goles en el torneo para la tabla de goleadores.
* **Consulta ejecutada:**
  ```javascript
  db.jugadores.find({ "goles": { "$gt": 5 } })
```
#### Evidencia de Ejecución (MongoDB Atlas)
<img width="1436" height="453" alt="WhatsApp Image 2026-10-13 at 10 29 50 PM" src="https://github.com/user-attachments/assets/cc5d0f9f-1f30-4ba6-93d0-cc275bf0f4d8" />
<img width="1600" height="652" alt="WhatsApp Image 2026-10-13 at 10 29 29 PM" src="https://github.com/user-attachments/assets/2fd1b4e3-a3b0-4903-ad5a-9c1d71cc88e0" />

# Fase 3: Resiliencia, Transacciones ACID y Securización
**Asignatura:** Base de Datos III  
**Proyecto:** Sistema de Gestión de la Liga Sanmartiniana de Fútbol  

---

## 1. Arquitectura de Alta Disponibilidad y Resiliencia (Replica Set)

Para garantizar la continuidad operativa del sistema de la Liga Sanmartiniana ante posibles fallos de hardware o red, se desplegó un clúster con topología de **Replica Set** (`rsLiga`).

### Configuración del Clúster
* **Nodo Primario (Puerto 27017):** Recibe todas las operaciones de escritura y lectura principales.
* **Nodo Secundario (Puerto 27018):** Replica de forma asíncrona los datos del nodo primario para proporcionar redundancia.
* **Nodo Árbitro (Puerto 27019):** Participa en las elecciones de un nuevo primario sin almacenar datos de la base de datos.

### Prueba de Tolerancia a Fallos (Failover)
Se simuló la caída abrupta del nodo primario interrumpiendo su proceso (`mongod`). Durante la prueba:
1. El nodo secundario detectó la falta de respuesta del primario mediante los *heartbeats*.
2. El nodo árbitro desempató la votación, promoviendo al nodo secundario como nuevo **Primario**.
3. Al restaurarse el nodo original, este se reincorporó automáticamente al grupo como **Secundario**, manteniendo la integridad de los datos.

---

## 2. Consistencia y Transacciones ACID Multi-documento

En el ámbito de la Liga Sanmartiniana, registrar el resultado de un partido implica actualizar múltiples documentos simultáneamente (por ejemplo, sumar puntos a la tabla de posiciones y actualizar los goles del jugador). Para evitar estados inconsistentes en caso de fallos de red o de ejecución, se utiliza la API de sesiones y transacciones ACID.

### Script de Transacción (`scripts/transaccion_partido.js`)

```javascript
// Inicio de sesión y transacción multi-documento
const session = db.getMongo().startSession();
session.startTransaction();

try {
  const dbTrans = session.getDatabase("liga_sanmartiniana");

  // 1. Actualización atómica de la tabla de posiciones (Equipo Ganador)
  const resEquipo = dbTrans.equipos.updateOne(
    { "nombre": "Club Atlético San Martín" },
    { "$inc": { "puntos": 3 } },
    { session }
  );

  // 2. Actualización atómica de estadísticas de jugador (Goles del goleador)
  const resJugador = dbTrans.jugadores.updateOne(
    { "nombre": "Lautaro" },
    { "$inc": { "goles": 1 } },
    { session }
  );

  // Verificación de operaciones antes del commit
  if (resEquipo.matchedCount === 0 || resJugador.matchedCount === 0) {
    throw new Error("No se encontró el equipo o jugador especificado.");
  }

  // Confirmación atómica de todos los cambios
  session.commitTransaction();
  print("Transacción ACID ejecutada con éxito. Datos actualizados en la Liga Sanmartiniana.");

} catch (error) {
  // Reversión completa (Rollback) si ocurre algún error
  session.abortTransaction();
  print("Error detectado durante la transacción. Rollback ejecutado correctamente: " + error.message);

} finally {
  session.endSession();
}
  "fichaje_fecha": "2025-02-10"
}
