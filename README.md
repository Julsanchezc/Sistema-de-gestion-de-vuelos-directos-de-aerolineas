<p align="center">
  <img src="docs/assets/banner.png" alt="Sistema de Gestión de Vuelos Directos de Aerolíneas" width="100%">
</p>

<p align="center">
  <img src="https://img.shields.io/badge/PostgreSQL-14+-4169E1?style=for-the-badge&logo=postgresql&logoColor=white" alt="PostgreSQL">
  <img src="https://img.shields.io/badge/PL/pgSQL-Triggers-336791?style=for-the-badge" alt="PL/pgSQL">
  <img src="https://img.shields.io/badge/Modelo-E--R-6366F1?style=for-the-badge" alt="Modelo">
  <img src="https://img.shields.io/badge/UNAL-Bases%20de%20Datos-0F766E?style=for-the-badge" alt="UNAL">
</p>

# ✈️ Sistema de Gestión de Vuelos Directos de Aerolíneas (SGVDA)

Base de datos relacional en **PostgreSQL** que modela la operación completa de una aerolínea de vuelos directos: infraestructura (países, ciudades, aeropuertos y rutas), flota (modelos y aviones), tripulación y su asignación a vuelos, y el ciclo comercial (clientes, reservas, boletos por clase tarifaria y pagos).

Proyecto final de **Bases de Datos · Universidad Nacional de Colombia, Facultad de Ingeniería**.

## ✨ Lo más destacado

- 🗂️ **15 tablas normalizadas** en un esquema dedicado (`aerolinea`), con llaves primarias, foráneas y constraints de integridad.
- ⚙️ **6 reglas de negocio** implementadas en la base de datos con funciones **PL/pgSQL** y **triggers**: cálculo automático del precio del boleto, control de capacidad del avión, validación de cruces de horario de la tripulación y más.
- ⚡ **Índices** sobre llaves foráneas y columnas de filtro frecuente.
- 🔍 **5 consultas DQL** diseñadas para interfaces reales: buscador de vuelos, detalle de reserva, manifiesto de tripulación, dashboard de ingresos por ruta e historial de cliente.
- 🧪 **Datos de prueba** (60 registros por tabla) que además validan que los triggers funcionan al cargarse.

<p align="center">
  <img src="diagrama_relacional.png" alt="Modelo relacional" width="70%">
</p>

## 👥 Autores

- Michelle Alejandra Gómez Sánchez
- **Julián Santiago Sánchez Castro**

---

## Estructura del repositorio

```
.
├── componentes/                # cada script por separado (una pieza del proyecto)
│   ├── proyectoo.txt           # esquema, secuencias, tablas, PK, FK y constraints
│   ├── indices.txt             # índices adicionales sobre FK y columnas de filtro frecuente
│   ├── funciones.txt           # funciones PL/pgSQL de las reglas de negocio
│   ├── triggers.txt            # triggers que activan esas funciones
│   ├── llenado_datos.txt       # datos de prueba (60 registros por tabla, con excepciones)
│   └── consultas.txt           # 5 consultas DQL que soportan interfaces de usuario reales
├── script_general.txt          # TODO lo anterior unido, en orden, con guía de ejecución
├── documentacion_proyecto.md   # documentación detallada del modelo y decisiones de diseño
├── diagrama_er.png             # imagen en alta resolución del diagrama E-R
├── diagrama_er.svg             # diagrama vectorial escalable
├── diagrama_relacional.png     # diagrama del modelo relacional normalizado
├── diagrama_relacional.svg     # diagrama relacional en formato SVG
└── README.md                    # este archivo
```

## Diagrama Entidad-Relación y Modelo Relacional

```mermaid
erDiagram
    PAIS ||--o{ CIUDAD : "contiene"
    CIUDAD ||--o{ AEROPUERTO : "alberga"
    AEROPUERTO ||--o{ RUTA : "origen"
    AEROPUERTO ||--o{ RUTA : "destino"
    RUTA ||--o{ VUELO : "programa"
    MODELO ||--o{ AVION : "define"
    AVION ||--o{ VUELO : "opera"
    VUELO ||--o{ ASIGNACION_TRIPULACION : "asigna"
    TRIPULANTE ||--o{ ASIGNACION_TRIPULACION : "participa"
    ROL_TRIPULANTE ||--o{ ASIGNACION_TRIPULACION : "desempena"
    CLIENTE ||--o{ RESERVA : "realiza"
    VUELO ||--o{ RESERVA : "corresponde"
    RESERVA ||--o{ PAGO : "liquida"
    RESERVA ||--o{ BOLETO : "incluye"
    CLASE ||--o{ BOLETO : "tarifica"

    PAIS {
        integer id_pais PK
        varchar nombre_pais
    }
    CIUDAD {
        integer id_ciudad PK
        varchar nombre_ciudad
        integer id_pais FK
    }
    AEROPUERTO {
        integer id_aeropuerto PK
        varchar nombre_aeropuerto
        integer id_ciudad FK
    }
    RUTA {
        integer id_ruta PK
        varchar nombre_ruta
        numeric precio_base
        integer id_origen FK
        integer id_destino FK
    }
    MODELO {
        integer id_modelo PK
        varchar nombre_modelo
        varchar fabricante
    }
    AVION {
        integer id_avion PK
        integer capacidad
        varchar estado
        integer id_modelo FK
    }
    VUELO {
        integer id_vuelo PK
        date fecha_vuelo
        time hora_salida
        time hora_llegada
        numeric precio_ajuste
        integer id_ruta FK
        integer id_avion FK
    }
    ROL_TRIPULANTE {
        integer id_rol_tripulante PK
        varchar descripcion_rol_tripulante
    }
    TRIPULANTE {
        integer id_tripulante PK
        varchar nombre_tripulante
        varchar apellido_tripulante
        varchar telefono_tripulante
        varchar correo_tripulante
        date fecha_ingreso_tripulante
        date fecha_salida_tripulante
        varchar estado_tripulante
        date fecha_nacimiento_tripulante
    }
    ASIGNACION_TRIPULACION {
        integer id_asignacion_tripulacion PK
        date fecha_asignacion
        integer id_tripulante FK
        integer id_rol_tripulante FK
        integer id_vuelo FK
    }
    CLIENTE {
        integer id_cliente PK
        varchar nombre_cliente
        varchar apellido_cliente
        bigint telefono_cliente
        varchar correo_cliente
    }
    RESERVA {
        integer id_reserva PK
        date fecha_reserva
        varchar estado_reserva
        integer id_vuelo FK
        integer id_cliente FK
    }
    PAGO {
        integer id_pago PK
        numeric monto
        date fecha_pago
        varchar metodo_pago
        varchar estado_pago
        integer id_reserva FK
    }
    CLASE {
        integer id_clase PK
        varchar descripcion_clase
        numeric multiplicador
    }
    BOLETO {
        integer id_boleto PK
        integer asiento
        date fecha_compra
        numeric precio
        varchar nombre_viajero
        integer id_reserva FK
        integer id_clase FK
    }
```

> 💡 **Archivos descargables del diagrama:** [diagrama_er.png (Imagen HD con fondo blanco)](./diagrama_er.png) · [diagrama_er.svg (Vectorial)](./diagrama_er.svg) · [Documentación detallada](./documentacion_proyecto.md)

## Orden de ejecución

Ya sea corriendo `script_general.txt` de una sola vez, o los archivos de
`componentes/` por separado, el orden es el mismo:

1. **`proyectoo.txt`** — crea el esquema `aerolinea`, las 15 tablas, sus secuencias,
   llaves primarias, llaves foráneas y constraints de integridad.
2. **`indices.txt`** — crea índices adicionales sobre columnas de llave foránea y
   columnas usadas en filtros/joins frecuentes (fechas, estados, nombres).
3. **`funciones.txt`** — crea las funciones PL/pgSQL que implementan las reglas de negocio.
4. **`triggers.txt`** — crea los triggers que invocan esas funciones sobre las tablas correspondientes.
5. **`llenado_datos.txt`** — inserta los datos de prueba. Al ejecutarse dispara los
   triggers (cálculo automático de precios, control de capacidad, actualización de
   estado de reserva según el pago), por lo que también sirve como primera validación
   de que las reglas de negocio funcionan.
6. **`consultas.txt`** — consultas de solo lectura (DQL) para verificar que la base
   de datos responde correctamente a los casos de uso definidos.

```bash
# Opción 1: un solo archivo con todo
psql -U <usuario> -d <base_de_datos> -f script_general.txt

# Opción 2: por componentes, respetando el orden
psql -U <usuario> -d <base_de_datos> -f componentes/proyectoo.txt
psql -U <usuario> -d <base_de_datos> -f componentes/indices.txt
psql -U <usuario> -d <base_de_datos> -f componentes/funciones.txt
psql -U <usuario> -d <base_de_datos> -f componentes/triggers.txt
psql -U <usuario> -d <base_de_datos> -f componentes/llenado_datos.txt
psql -U <usuario> -d <base_de_datos> -f componentes/consultas.txt
```

## Modelo de datos

15 tablas: `pais`, `ciudad`, `aeropuerto`, `ruta`, `vuelo`, `avion`, `modelo`,
`tripulante`, `rol_tripulante`, `asignacion_tripulacion`, `cliente`, `reserva`,
`pago`, `boleto`, `clase`. El detalle de cada tabla, sus relaciones y las
decisiones de diseño tomadas están en [`documentacion_proyecto.md`](./documentacion_proyecto.md).

## Reglas de negocio (funciones/triggers)

| # | Regla | Tabla / evento |
|---|---|---|
| 1 | Un tripulante no puede tener asignaciones en vuelos con horario cruzado el mismo día | `asignacion_tripulacion` |
| 2 | El precio del boleto se calcula automáticamente | `boleto` |
| 3 | No se puede vender un boleto si ya se alcanzó la capacidad del avión | `boleto` |
| 4 | El estado de la reserva se actualiza según el estado del pago | `pago` |
| 5 | No se puede reservar un vuelo cuya fecha ya pasó | `reserva` |
| 6 (adicional) | Origen y destino de una ruta no pueden ser la misma ciudad | `ruta` |

Se pidieron mínimo 5 reglas; el proyecto implementa 6.

## Datos de prueba

`llenado_datos.txt` carga 60 registros en 13 de las 15 tablas. `rol_tripulante`
(6 registros) y `clase` (4 registros) quedan con menos porque son catálogos con
un dominio de valores naturalmente pequeño y fijo en la vida real (el enunciado
permite menos del mínimo "en donde sea posible"). Los IDs no se insertan
manualmente: cada tabla los genera con su propia secuencia (`nextval`), en el
mismo orden en que se cargan los `insert`.

## Consultas DQL

Las 5 consultas en `consultas.txt` están pensadas para interfaces de usuario
concretas (buscador de vuelos, detalle de reserva, manifiesto de tripulación,
dashboard de ingresos por ruta, historial de un cliente). Cada una incluye un
comentario explicando para qué interfaz sirve y por qué.
