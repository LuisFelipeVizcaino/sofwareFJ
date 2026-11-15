# sofwareFJ
# Software FJ – Sistema Integral de Gestión de Clientes, Servicios y Reservas

Proyecto del curso **Programación (213023)** – Universidad Nacional Abierta y a Distancia (UNAD) – Fase 4.

## Descripción

Software FJ es una aplicación de consola, desarrollada en Python con **programación orientada a objetos**, que gestiona los clientes, servicios y reservas de una empresa que ofrece tres tipos de servicios: reserva de salas, alquiler de equipos y asesorías especializadas.

El sistema **no usa bases de datos**: toda la información se maneja con objetos y listas en memoria. El único archivo que se escribe es el registro de eventos y errores (`software_fj.log`). La aplicación está diseñada para **seguir funcionando aunque ocurran errores**: cada falla se captura, se muestra al usuario y se guarda en el log.

## Requisitos y ejecución

- Python 3.9 o superior
- No requiere instalar librerías externas

```bash
python software_fj.py
```

Al ejecutarlo se abre un menú interactivo. Se genera el archivo `software_fj.log` en la misma carpeta del programa.

## Menú interactivo

| Opción | Función |
|---|---|
| 1 | Registrar cliente (nombre, documento, correo, teléfono) |
| 2 | Crear servicio (sala, equipo o asesoría) |
| 3 | Crear reserva (elegir cliente, servicio y duración en horas) |
| 4 | Confirmar reserva |
| 5 | Cancelar reserva |
| 6 | Procesar reserva y calcular el costo (con impuesto y descuento opcionales) |
| 7 | Cambiar la disponibilidad de un servicio |
| 8 | Ver clientes, servicios y reservas |
| 9 | Ejecutar la simulación automática de 18 operaciones |
| 0 | Salir |

Para elegir un cliente, servicio o reserva, el usuario escribe el número que aparece en la lista.

## Arquitectura de clases

```
Entidad (abstracta)
├── Cliente
├── Servicio (abstracta)
│   ├── ReservaSala
│   ├── AlquilerEquipo
│   └── Asesoria
└── Reserva

SoftwareFJ  →  administra las listas de clientes, servicios y reservas
```

- **Entidad:** clase abstracta que representa las entidades generales del sistema (tiene un `id` y el método abstracto `describir()`).
- **Cliente:** guarda sus datos personales en atributos privados y los valida al crearse:
  - nombre de mínimo 3 letras, sin números;
  - documento numérico de 6 a 12 dígitos (no se permiten documentos duplicados);
  - correo con formato válido;
  - teléfono numérico de 7 a 10 dígitos.
- **Servicio:** clase abstracta con los métodos `calcular_costo()`, `describir()` y `validar_parametros()`, que cada servicio hijo sobrescribe (polimorfismo):

| Servicio | Duración máxima | Regla de costo |
|---|---|---|
| `ReservaSala` | 8 horas | Recargo del 10 % si la capacidad es de 20 personas o más |
| `AlquilerEquipo` | 72 horas | Incluye un seguro del 5 % |
| `Asesoria` | 4 horas | El nivel *senior* cuesta 50 % más que el *junior* |

- **Reserva:** integra cliente, servicio, duración y estado (`Pendiente`, `Confirmada`, `Cancelada`, `Procesada` o `Fallida`). Implementa los métodos `confirmar()`, `cancelar()` y `procesar()`.
- **SoftwareFJ:** mantiene las listas internas y el método `ejecutar()`, que corre cada operación de forma segura para que ningún error detenga el programa.

## Métodos sobrecargados

Python no tiene sobrecarga nativa, por lo que se simula con parámetros opcionales. El método `costo_total(horas, impuesto=None, descuento=0.0)` admite varias formas de uso:

```python
servicio.costo_total(3)                          # con IVA por defecto (19 %)
servicio.costo_total(3, impuesto=0.05)           # con otro impuesto
servicio.costo_total(3, 0.19, descuento=0.10)    # con impuesto y descuento
```

## Manejo de excepciones

Todas las excepciones personalizadas derivan de `ErrorFJ`:

| Excepción | Situación que representa |
|---|---|
| `DatoInvalidoError` | Datos que no cumplen las validaciones |
| `ParametroFaltanteError` | Falta un dato obligatorio |
| `ServicioNoDisponibleError` | El servicio no está disponible |
| `OperacionNoPermitidaError` | Operación no válida para el estado actual de la reserva |
| `ReservaInvalidaError` | Intento de reserva incorrecto |
| `CalculoInconsistenteError` | Impuestos o descuentos fuera de rango |

Se usan los bloques `try/except`, `try/except/else` y `try/except/finally` (por ejemplo en `Reserva.confirmar()` y `Reserva.procesar()`), y el **encadenamiento de excepciones** con `raise ... from e`, que conserva la causa original del error en el log.

## Archivo de logs

Todos los eventos relevantes (clientes registrados, servicios creados, reservas confirmadas, canceladas o procesadas) y todos los errores quedan registrados en `software_fj.log`, con fecha, hora y nivel (`INFO`, `WARNING`, `ERROR`, `CRITICAL`).

## Simulación automática

La opción 9 del menú ejecuta 18 operaciones completas, sin base de datos, que incluyen:

- registros de clientes válidos e inválidos (correo incorrecto, nombre faltante);
- creación correcta e incorrecta de servicios (capacidad negativa, nivel inexistente);
- reservas exitosas y fallidas (duración excesiva, servicio no disponible, reserva sin cliente);
- operaciones no permitidas (confirmar una reserva cancelada);
- cálculos inconsistentes (descuento del 150 %).

Al finalizar muestra un resumen de las reservas y el programa continúa funcionando con normalidad.

## Estructura del código

El archivo `software_fj.py` está organizado en secciones:

1. Log y excepciones personalizadas
2. Clase abstracta `Entidad` y clase `Cliente`
3. Clase abstracta `Servicio` y servicios especializados
4. Clase `Reserva`
5. Clase `SoftwareFJ` (listas internas y ejecución segura)
6. Simulación automática
7. Menú interactivo

## Integrantes y responsabilidades

| Integrante | Sección | Responsabilidad |
|---|---|---|
| Integrante 1 (luis felipe ulloa rada) | 1 y 6 | Excepciones, log y simulación |
| Integrante 2 (nombre) | 2 | `Entidad` y `Cliente` |
| Integrante 3 (nombre) | 3 | `Servicio` y servicios especializados |
| Integrante 4 (nombre) | 4 | `Reserva` |
| Integrante 5 (nombre) | 5 y 7 | `SoftwareFJ` y menú interactivo |
