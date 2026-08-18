# 🗄️ Base de Datos — Schema

## Conexión

| Parámetro | Valor |
|---|---|
| Host | `localhost:5432` |
| Base de datos | `bidly` |
| Usuario | `postgres` |
| Contraseña | `bidly2026` |
| pgAdmin contraseña | `admin123` |

---

## Tablas del sistema

### `paises`
| Columna | Tipo | Notas |
|---|---|---|
| numero | INT (PK) | ID del país |
| nombre | VARCHAR(250) | |
| nombreCorto | VARCHAR(250) | |
| capital | VARCHAR(250) | |
| nacionalidad | VARCHAR(250) | |
| idiomas | VARCHAR(150) | |

---

### `personas`
| Columna | Tipo | Notas |
|---|---|---|
| identificador | SERIAL (PK) | Auto-generado |
| documento | VARCHAR(20) | CUIT/DNI |
| nombre | VARCHAR(150) | |
| direccion | VARCHAR(250) | |
| estado | VARCHAR(15) | `activo` / `inactivo` |
| foto | BYTEA | Foto en binario |

---

### `clientes`
| Columna | Tipo | Notas |
|---|---|---|
| identificador | INT (PK, FK→personas) | Mismo ID que persona |
| numeroPais | INT (FK→paises) | ⚠️ Es Integer en Java |
| admitido | VARCHAR(2) | `si` / `no` |
| categoria | VARCHAR(10) | `comun/especial/plata/oro/platino` |
| verificador | INT (FK→empleados) | Empleado que verificó |
| email | VARCHAR(200) UNIQUE | |
| passwordHash | VARCHAR(300) | Sin hash real en dev |

---

### `subastas`
| Columna | Tipo | Notas |
|---|---|---|
| identificador | SERIAL (PK) | |
| fecha | DATE | Mínimo 10 días desde hoy |
| hora | TIME | |
| estado | VARCHAR(10) | `abierta` / `cerrada` |
| subastador | INT (FK→subastadores) | |
| ubicacion | VARCHAR(350) | |
| categoria | VARCHAR(10) | `comun/especial/plata/oro/platino` |
| moneda | VARCHAR(10) | `pesos` / `dolares` |

---

### `mediosdepago` ⚠️
| Columna | Tipo | Notas |
|---|---|---|
| identificador | SERIAL (PK) | |
| cliente | INT (FK→clientes) | |
| tipo | VARCHAR(10) | `tarjeta/cuenta/cheque` |
| numeroTarjeta | VARCHAR(20) | Solo si tipo=tarjeta |
| vencimiento | VARCHAR(7) | ⚠️ Es String (MM/YYYY), NO LocalDate |
| titular | VARCHAR(150) | |
| numeroCuenta | VARCHAR(50) | Solo si tipo=cuenta |
| banco | VARCHAR(150) | |
| numeroCheque | VARCHAR(30) | Solo si tipo=cheque |
| montoCheque | DECIMAL(18,2) | |
| verificado | VARCHAR(2) | `si` / `no`, default `no` |

---

### `multas`
| Columna | Tipo | Notas |
|---|---|---|
| identificador | SERIAL (PK) | |
| cliente | INT (FK→clientes) | |
| pujo | INT (FK→pujos) | |
| importe | DECIMAL(18,2) | 10% del valor pujado |
| pagada | VARCHAR(2) | `si` / `no` |
| fechaGenerada | TIMESTAMP | ⚠️ Es LocalDateTime en Java |

---

### `registrodesubasta`
| Columna | Tipo | Notas |
|---|---|---|
| identificador | SERIAL (PK) | |
| subasta | INT (FK→subastas) | |
| duenio | INT (FK→duenios) | |
| producto | INT (FK→productos) | |
| cliente | INT (FK→clientes) | |
| importe | DECIMAL(18,2) | |
| comision | DECIMAL(18,2) | |
| reembolsada | VARCHAR(2) | ⚠️ Columna agregada después — `si`/`no` |

---

### `seguros`
| Columna | Tipo | Notas |
|---|---|---|
| nroPoliza | VARCHAR(30) (PK) | PK es String, no número |
| compania | VARCHAR(150) | |
| polizaCombinada | VARCHAR(2) | `si` / `no` |
| importe | DECIMAL(18,2) | Mayor a 0 |

---

### `notificaciones`
| Columna | Tipo | Notas |
|---|---|---|
| identificador | SERIAL (PK) | |
| cliente | INT (FK→clientes) | |
| tipo | VARCHAR(20) | `lider/perdiste/ganaste/multa/subasta_por_cerrar` |
| mensaje | VARCHAR(500) | |
| leida | VARCHAR(2) | `si` / `no` |
| fechaHora | TIMESTAMP | |

---

### `fotos`
| Columna | Tipo | Notas |
|---|---|---|
| identificador | SERIAL (PK) | |
| producto | INT (FK→productos) | |
| foto | BYTEA | Imagen en binario |

---

## Relaciones entre tablas

```
paises
  └── clientes.numeropais → paises.numero

personas
  ├── clientes.identificador → personas.identificador
  ├── duenios.identificador  → personas.identificador
  └── subastadores.identificador → personas.identificador

subastas
  ├── asistentes.subasta → subastas.identificador
  ├── catalogos.subasta  → subastas.identificador
  └── registrodesubasta.subasta → subastas.identificador

clientes
  ├── asistentes.cliente → clientes.identificador
  ├── mediosdepago.cliente → clientes.identificador
  ├── multas.cliente → clientes.identificador
  ├── notificaciones.cliente → clientes.identificador
  └── registrodesubasta.cliente → clientes.identificador

catalogos
  └── itemscatalogo.catalogo → catalogos.identificador

itemscatalogo
  └── pujos.item → itemscatalogo.identificador

asistentes
  └── pujos.asistente → asistentes.identificador

pujos
  └── multas.pujo → pujos.identificador
```
