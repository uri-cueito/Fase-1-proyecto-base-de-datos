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
    "nombre": "Club Atlético San Martín",
    "goles": 2
  },
  "equipo_visitante": {
    "club_id": { "$oid": "650c1f1e9b1d8e2a3c4b1004" },
    "nombre": "Atlético Palmira",
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












  "estado": "habilitado",
  "fichaje_fecha": "2025-02-10"
}
