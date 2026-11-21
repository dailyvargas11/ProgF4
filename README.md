# ProgF4
Fase 4 - Componente Practico - Practicas simuladas
# Sistema Integral de Gestión de Clientes, Servicios y Reservas - Software FJ

Proyecto académico desarrollado para el curso Programacion Universidad Nacional Abierta y a Distancia (UNAD).

## Descripción general

El sistema implementa una arquitectura orientada a objetos para Software FJ sin utilizar ningún motor de base de datos. Toda la gestión se realiza mediante objetos y listas internas en memoria, registrando eventos y errores en un archivo de log (`software_fj.log`).

### Características principales
- **Abstracción y herencia:** Clase abstracta `EntidadSistema` y clase abstracta `Servicio` con tres servicios especializados (`ReservaSala`, `AlquilerEquipo` y `AsesoriaEspecializada`).
- **Polimorfismo y métodos sobrecargados:** Sobrescritura de métodos para validar parámetros, calcular subtotales y describir servicios, además de `calcular_costo_total` con variantes mediante parámetros opcionales (impuestos y descuentos).
- **Encapsulación y validaciones:** Uso de propiedades (`@property` y setters) y validaciones estrictas para clientes, tarifas, aforo y disponibilidad.
- **Manejo avanzado de excepciones:** Excepciones personalizadas (`ErrorSoftwareFJ`, `ErrorValidacionCliente`, `ErrorServicio`, `ErrorReserva`, `ErrorCalculoFinanciero`), bloques `try/except/else/finally` y encadenamiento con `raise ... from ...`.
- **Simulación de operaciones:** Incluye 12 operaciones simuladas (registros válidos e inválidos de clientes, creación correcta e incorrecta de servicios y reservas exitosas y fallidas) demostrando continuidad operativa ante errores.

## Estructura del proyecto

- `sistema_software_fj.py`: Código fuente principal con la arquitectura POO, manejo de excepciones y simulación de operaciones.
- `README.md`: Documentación general del repositorio.
- `software_fj.log`: Archivo de registro generado automáticamente al ejecutar el programa.

## Ejecución del programa

1. Abre la carpeta del proyecto en *Visual Studio Code*.
2. Asegúrate de tener *Python 3* instalado.
3. Ejecuta el script desde la terminal integrada:
```bash
python sistema_software_fj.py
Autora :Daily Vargas — Ingeniería de telecomunicaciones, UNAD.

Repositorio: https://github.com/dailyvargas11/ProgF4