# ==============================================================================
# 
# Software FJ
# Sistema Integral de Gestion de Clientes, Servicios y Reservas - 
# Curso: Programacion (213023) - Fase 4 
# Nombre completo: Daily Daniela Vargas Ospina
# Número de cédula:1013676905
# ==============================================================================

# Importacion de modulos estandar necesarios para abstraccion, fechas y logs
from abc import ABC, abstractmethod
from datetime import datetime
import logging
import os


# ==============================================================================
# Configuracion del sistema de registro de eventos y errores 
# ==============================================================================

# Ruta del archivo de log en el mismo directorio del script
RUTA_LOG = os.path.join(os.path.dirname(os.path.abspath(__file__)), "software_fj.log")

# Configuracion de logging para registrar eventos informativos, advertencias y errores
logger = logging.getLogger("SoftwareFJLogger")
logger.setLevel(logging.DEBUG)

# Evita duplicar manejadores si el script se reejecuta en el mismo entorno
if not logger.handlers:
    # Manejador de archivo con codificacion UTF-8 para caracteres en espanol
    file_handler = logging.FileHandler(RUTA_LOG, mode="a", encoding="utf-8")
    file_handler.setLevel(logging.INFO)
    # Formato detallado: fecha/hora, nivel, nombre y mensaje
    formato_log = logging.Formatter("%(asctime)s | %(levelname)-8s | %(message)s")
    file_handler.setFormatter(formato_log)
    logger.addHandler(file_handler)


# ==============================================================================
# Jerarquia de excepciones personalizadas de Software FJ
# ==============================================================================

# Excepcion base del sistema para agrupar errores del dominio
class ErrorSoftwareFJ(Exception):
    # Constructor que almacena un mensaje y un codigo de error opcional
    def __init__(self, mensaje, codigo="ERR_GENERAL"):
        super().__init__(mensaje)
        self.codigo = codigo


# Excepcion lanzada cuando los datos de un cliente no cumplen las validaciones
class ErrorValidacionCliente(ErrorSoftwareFJ):
    def __init__(self, mensaje):
        super().__init__(mensaje, codigo="ERR_CLIENTE")


# Excepcion lanzada cuando un servicio tiene parametros invalidos o no existe
class ErrorServicio(ErrorSoftwareFJ):
    def __init__(self, mensaje):
        super().__init__(mensaje, codigo="ERR_SERVICIO")


# Excepcion lanzada cuando una reserva falla por disponibilidad, estado o calculo
class ErrorReserva(ErrorSoftwareFJ):
    def __init__(self, mensaje):
        super().__init__(mensaje, codigo="ERR_RESERVA")


# Excepcion lanzada ante calculos financieros inconsistentes o valores negativos
class ErrorCalculoFinanciero(ErrorSoftwareFJ):
    def __init__(self, mensaje):
        super().__init__(mensaje, codigo="ERR_CALCULO")


# ==============================================================================
# Clase abstracta base para entidades generales del sistema
# ==============================================================================

# Representa cualquier entidad identificable dentro de Software FJ
class EntidadSistema(ABC):
    # Inicializa el identificador unico y la fecha de creacion de la entidad
    def __init__(self, id_entidad):
        # Valida que el identificador sea una cadena no vacia
        if not isinstance(id_entidad, str) or not id_entidad.strip():
            raise ValueError("El identificador de la entidad debe ser un texto no vacio.")
        self._id_entidad = id_entidad.strip().upper()
        self._fecha_registro = datetime.now()

    # Propiedad de solo lectura para consultar el ID de la entidad
    @property
    def id_entidad(self):
        return self._id_entidad

    # Propiedad de solo lectura para consultar la fecha de registro
    @property
    def fecha_registro(self):
        return self._fecha_registro

    # Metodo abstracto que obliga a las subclases a entregar un resumen legible
    @abstractmethod
    def resumen(self):
        pass


# ==============================================================================
# Clase Cliente con encapsulacion de datos personales y validaciones estrictas
# ==============================================================================

# Hereda de EntidadSistema y protege atributos sensibles mediante encapsulacion
class Cliente(EntidadSistema):
    # Constructor del cliente con validacion de nombre, correo y telefono
    def __init__(self, id_cliente, nombre, correo, telefono, activo=True):
        # Invoca el constructor de la clase abstracta base
        super().__init__(id_cliente)
        # Usa setters para aplicar validaciones desde la creacion del objeto
        self.nombre = nombre
        self.correo = correo
        self.telefono = telefono
        self._activo = bool(activo)

    # Getter del nombre del cliente
    @property
    def nombre(self):
        return self._nombre

    # Setter del nombre con validacion de longitud y tipo
    @nombre.setter
    def nombre(self, valor):
        if not isinstance(valor, str) or len(valor.strip()) < 3:
            raise ErrorValidacionCliente("Nombre invalido: debe tener al menos 3 caracteres.")
        self._nombre = valor.strip()

    # Getter del correo electronico
    @property
    def correo(self):
        return self._correo

    # Setter del correo con validacion basica de estructura
    @correo.setter
    def correo(self, valor):
        if not isinstance(valor, str) or "@" not in valor or "." not in valor.split("@")[-1]:
            raise ErrorValidacionCliente(f"Correo electronico invalido: '{valor}'.")
        self._correo = valor.strip().lower()

    # Getter del telefono
    @property
    def telefono(self):
        return self._telefono

    # Setter del telefono con validacion de digitos y longitud minima
    @telefono.setter
    def telefono(self, valor):
        texto_tel = str(valor).strip()
        if not texto_tel.isdigit() or len(texto_tel) < 7:
            raise ErrorValidacionCliente(f"Telefono invalido: '{valor}' (minimo 7 digitos).")
        self._telefono = texto_tel

    # Propiedad para consultar si el cliente esta habilitado para reservar
    @property
    def activo(self):
        return self._activo

    # Permite cambiar el estado activo/inactivo del cliente
    def establecer_estado(self, estado_activo):
        self._activo = bool(estado_activo)

    # Implementacion del metodo abstracto heredado de EntidadSistema
    def resumen(self):
        estado_texto = "Activo" if self._activo else "Inactivo"
        return f"Cliente [{self._id_entidad}] {self._nombre} | Correo: {self._correo} | Estado: {estado_texto}"


# ==============================================================================
# Clase abstracta Servicio y tres servicios especializados derivados
# ==============================================================================

# Clase base abstracta para todos los servicios que ofrece Software FJ
class Servicio(EntidadSistema):
    # Inicializa codigo, nombre, tarifa base por hora/unidad y disponibilidad
    def __init__(self, id_servicio, nombre, tarifa_base, disponible=True):
        super().__init__(id_servicio)
        if not isinstance(nombre, str) or len(nombre.strip()) < 3:
            raise ErrorServicio("El nombre del servicio debe tener al menos 3 caracteres.")
        # Valida que la tarifa base sea numerica y positiva
        if not isinstance(tarifa_base, (int, float)) or isinstance(tarifa_base, bool) or tarifa_base <= 0:
            raise ErrorServicio("La tarifa base del servicio debe ser mayor que cero.")
        self._nombre = nombre.strip()
        self._tarifa_base = float(tarifa_base)
        self._disponible = bool(disponible)

    # Getter del nombre del servicio
    @property
    def nombre(self):
        return self._nombre

    # Getter de la tarifa base
    @property
    def tarifa_base(self):
        return self._tarifa_base

    # Getter de disponibilidad
    @property
    def disponible(self):
        return self._disponible

    # Setter de disponibilidad
    @disponible.setter
    def disponible(self, valor):
        self._disponible = bool(valor)

    # Metodo abstracto para validar parametros especificos de cada servicio
    @abstractmethod
    def validar_parametros(self, cantidad_unidades, asistentes=1):
        pass

    # Metodo abstracto para calcular el subtotal segun el tipo de servicio
    @abstractmethod
    def calcular_subtotal(self, cantidad_unidades, asistentes=1):
        pass

    # Metodo abstracto para entregar la descripcion detallada del servicio
    @abstractmethod
    def describir(self):
        pass

    # Metodo con variantes tipo sobrecarga (parametros opcionales) para calcular costo total
    # Variante 1: calcular_costo_total(unidades)
    # Variante 2: calcular_costo_total(unidades, asistentes=..., impuesto_pct=..., descuento_pct=...)
    def calcular_costo_total(self, cantidad_unidades, asistentes=1, impuesto_pct=0.19, descuento_pct=0.0):
        try:
            # Valida porcentajes de impuesto y descuento
            if not (0.0 <= impuesto_pct <= 1.0):
                raise ErrorCalculoFinanciero("El porcentaje de impuesto debe estar entre 0.0 y 1.0.")
            if not (0.0 <= descuento_pct < 1.0):
                raise ErrorCalculoFinanciero("El porcentaje de descuento debe estar entre 0.0 y 1.0.")

            # Calcula subtotal usando polimorfismo de la subclase
            subtotal = self.calcular_subtotal(cantidad_unidades, asistentes=asistentes)
            if subtotal < 0:
                raise ErrorCalculoFinanciero("El subtotal calculado no puede ser negativo.")

            # Aplica descuento y luego impuesto sobre el valor descontado
            monto_descuento = subtotal * descuento_pct
            base_gravable = subtotal - monto_descuento
            monto_impuesto = base_gravable * impuesto_pct
            total = round(base_gravable + monto_impuesto, 2)

            if total < 0:
                raise ErrorCalculoFinanciero("El total final resulto negativo.")
            return total
        except ErrorCalculoFinanciero:
            # Re-lanza excepciones financieras ya tipificadas
            raise
        except Exception as exc:
            # Encadena excepciones inesperadas con contexto claro (raise ... from ...)
            raise ErrorCalculoFinanciero("Fallo inesperado al calcular el costo total del servicio.") from exc

    # Implementacion del resumen general de la entidad
    def resumen(self):
        estado = "Disponible" if self._disponible else "No disponible"
        return f"Servicio [{self._id_entidad}] {self._nombre} (${self._tarifa_base:.2f}/h) - {estado}"


# Servicio especializado 1: Reserva de salas de reuniones o auditorios
class ReservaSala(Servicio):
    # Constructor con capacidad maxima de personas en la sala
    def __init__(self, id_servicio, nombre, tarifa_base, capacidad_maxima, disponible=True):
        super().__init__(id_servicio, nombre, tarifa_base, disponible=disponible)
        if not isinstance(capacidad_maxima, int) or isinstance(capacidad_maxima, bool) or capacidad_maxima <= 0:
            raise ErrorServicio("La capacidad maxima de la sala debe ser un entero positivo.")
        self._capacidad_maxima = capacidad_maxima

    # Valida duracion en horas y que los asistentes no superen el aforo
    def validar_parametros(self, cantidad_unidades, asistentes=1):
        if not isinstance(cantidad_unidades, (int, float)) or isinstance(cantidad_unidades, bool) or cantidad_unidades <= 0:
            raise ErrorServicio("La duracion de la sala en horas debe ser mayor que cero.")
        if not isinstance(asistentes, int) or isinstance(asistentes, bool) or asistentes <= 0:
            raise ErrorServicio("El numero de asistentes debe ser un entero positivo.")
        if asistentes > self._capacidad_maxima:
            raise ErrorServicio(
                f"Aforo excedido en '{self._nombre}': {asistentes} asistentes para capacidad de {self._capacidad_maxima}."
            )
        return True

    # Calcula el subtotal por horas de uso de la sala
    def calcular_subtotal(self, cantidad_unidades, asistentes=1):
        self.validar_parametros(cantidad_unidades, asistentes=asistentes)
        return float(cantidad_unidades) * self._tarifa_base

    # Describe el servicio de reserva de sala
    def describir(self):
        return f"Sala '{self._nombre}' (Capacidad max: {self._capacidad_maxima} personas, Tarifa: ${self._tarifa_base:.2f}/hora)."


# Servicio especializado 2: Alquiler de equipos tecnologicos
class AlquilerEquipo(Servicio):
    # Constructor con categoria del equipo y cargo fijo de preparacion/soporte
    def __init__(self, id_servicio, nombre, tarifa_base, categoria_equipo, cargo_soporte=15.0, disponible=True):
        super().__init__(id_servicio, nombre, tarifa_base, disponible=disponible)
        if not isinstance(categoria_equipo, str) or not categoria_equipo.strip():
            raise ErrorServicio("La categoria del equipo es obligatoria.")
        if not isinstance(cargo_soporte, (int, float)) or isinstance(cargo_soporte, bool) or cargo_soporte < 0:
            raise ErrorServicio("El cargo de soporte no puede ser negativo.")
        self._categoria_equipo = categoria_equipo.strip()
        self._cargo_soporte = float(cargo_soporte)

    # Valida cantidad de horas/dias de alquiler y numero de equipos solicitados
    def validar_parametros(self, cantidad_unidades, asistentes=1):
        if not isinstance(cantidad_unidades, (int, float)) or isinstance(cantidad_unidades, bool) or cantidad_unidades <= 0:
            raise ErrorServicio("El tiempo de alquiler debe ser mayor que cero.")
        if not isinstance(asistentes, int) or isinstance(asistentes, bool) or asistentes <= 0:
            raise ErrorServicio("La cantidad de equipos a alquilar debe ser al menos 1.")
        return True

    # Calcula subtotal multiplicando unidades, cantidad de equipos y tarifa, mas cargo de soporte
    def calcular_subtotal(self, cantidad_unidades, asistentes=1):
        self.validar_parametros(cantidad_unidades, asistentes=asistentes)
        return (float(cantidad_unidades) * self._tarifa_base * asistentes) + self._cargo_soporte

    # Describe el servicio de alquiler de equipos
    def describir(self):
        return (
            f"Equipo '{self._nombre}' [{self._categoria_equipo}] - "
            f"Tarifa: ${self._tarifa_base:.2f}/h + Soporte: ${self._cargo_soporte:.2f}."
        )


# Servicio especializado 3: Asesorias especializadas en software y arquitectura
class AsesoriaEspecializada(Servicio):
    # Constructor con area de especialidad y multiplicador de complejidad tecnica
    def __init__(self, id_servicio, nombre, tarifa_base, especialidad, multiplicador_complejidad=1.0, disponible=True):
        super().__init__(id_servicio, nombre, tarifa_base, disponible=disponible)
        if not isinstance(especialidad, str) or not especialidad.strip():
            raise ErrorServicio("La especialidad de la asesoria es obligatoria.")
        if not isinstance(multiplicador_complejidad, (int, float)) or multiplicador_complejidad < 1.0:
            raise ErrorServicio("El multiplicador de complejidad debe ser mayor o igual a 1.0.")
        self._especialidad = especialidad.strip()
        self._multiplicador_complejidad = float(multiplicador_complejidad)

    # Valida horas de asesoria y consultores requeridos
    def validar_parametros(self, cantidad_unidades, asistentes=1):
        if not isinstance(cantidad_unidades, (int, float)) or isinstance(cantidad_unidades, bool) or cantidad_unidades <= 0:
            raise ErrorServicio("Las horas de asesoria deben ser mayores que cero.")
        if not isinstance(asistentes, int) or isinstance(asistentes, bool) or asistentes <= 0:
            raise ErrorServicio("Debe asignarse al menos 1 especialista/participante valido.")
        return True

    # Calcula subtotal aplicando el multiplicador de complejidad tecnica
    def calcular_subtotal(self, cantidad_unidades, asistentes=1):
        self.validar_parametros(cantidad_unidades, asistentes=asistentes)
        return float(cantidad_unidades) * self._tarifa_base * self._multiplicador_complejidad

    # Describe el servicio de asesoria especializada
    def describir(self):
        return (
            f"Asesoria '{self._nombre}' ({self._especialidad}) - "
            f"Tarifa: ${self._tarifa_base:.2f}/h x Complejidad {self._multiplicador_complejidad:.1f}."
        )


# ==============================================================================
# Clase Reserva que integra Cliente, Servicio, duracion, estado y procesamiento
# ==============================================================================

# Gestiona el ciclo de vida de una reserva (Pendiente, Confirmada, Cancelada)
class Reserva(EntidadSistema):
    ESTADOS_VALIDOS = ("PENDIENTE", "CONFIRMADA", "CANCELADA")

    # Constructor que vincula cliente, servicio, duracion, asistentes y parametros de cobro
    def __init__(self, id_reserva, cliente, servicio, duracion, asistentes=1, descuento_pct=0.0, impuesto_pct=0.19):
        super().__init__(id_reserva)

        # Verifica que el cliente exista y sea instancia valida
        if not isinstance(cliente, Cliente):
            raise ErrorReserva("La reserva requiere un objeto Cliente valido.")
        # Verifica que el servicio exista y sea instancia valida
        if not isinstance(servicio, Servicio):
            raise ErrorReserva("La reserva requiere un objeto Servicio valido.")
        # Verifica que el cliente este activo
        if not cliente.activo:
            raise ErrorReserva(f"El cliente '{cliente.nombre}' esta inactivo y no puede realizar reservas.")
        # Verifica que el servicio este disponible
        if not servicio.disponible:
            raise ErrorReserva(f"El servicio '{servicio.nombre}' no se encuentra disponible.")

        self.cliente = cliente
        self.servicio = servicio
        self.duracion = duracion
        self.asistentes = asistentes
        self.descuento_pct = descuento_pct
        self.impuesto_pct = impuesto_pct
        self._estado = "PENDIENTE"
        self._monto_total = 0.0

    # Propiedad para consultar el estado actual de la reserva
    @property
    def estado(self):
        return self._estado

    # Propiedad para consultar el monto total calculado
    @property
    def monto_total(self):
        return self._monto_total

    # Procesa y confirma la reserva validando parametros y calculando el costo total
    def confirmar(self):
        if self._estado == "CANCELADA":
            raise ErrorReserva(f"No se puede confirmar la reserva '{self._id_entidad}' porque ya fue cancelada.")
        try:
            # Valida parametros especificos del servicio y calcula el costo final
            self.servicio.validar_parametros(self.duracion, asistentes=self.asistentes)
            total = self.servicio.calcular_costo_total(
                self.duracion,
                asistentes=self.asistentes,
                impuesto_pct=self.impuesto_pct,
                descuento_pct=self.descuento_pct,
            )
        except ErrorServicio as exc:
            # Encadena error de servicio dentro de un error de reserva
            raise ErrorReserva(f"Fallo al confirmar reserva '{self._id_entidad}': {exc}") from exc
        except ErrorCalculoFinanciero as exc:
            # Encadena error financiero dentro de un error de reserva
            raise ErrorReserva(f"Error de calculo en reserva '{self._id_entidad}': {exc}") from exc
        else:
            # Solo se ejecuta si no hubo excepciones en el bloque try
            self._monto_total = total
            self._estado = "CONFIRMADA"
            return self._monto_total

    # Cancela una reserva activa o pendiente
    def cancelar(self, motivo="Solicitud del usuario"):
        if self._estado == "CANCELADA":
            raise ErrorReserva(f"La reserva '{self._id_entidad}' ya se encuentra cancelada.")
        self._estado = "CANCELADA"
        return f"Reserva {self._id_entidad} cancelada ({motivo})."

    # Resumen legible de la reserva
    def resumen(self):
        return (
            f"Reserva [{self._id_entidad}] | Cliente: {self.cliente.nombre} | "
            f"Servicio: {self.servicio.nombre} | Duracion: {self.duracion} | "
            f"Total: ${self._monto_total:.2f} | Estado: {self._estado}"
        )


# ==============================================================================
# Gestor central en memoria
# ==============================================================================

# Coordina las listas internas de clientes, servicios y reservas
# (listas internas) sin motor de base de datos
class GestorSoftwareFJ:
    def __init__(self):
        self.clientes = []
        self.servicios = []
        self.reservas = []

    # Registra un cliente validado en la lista interna
    def registrar_cliente(self, cliente):
        if not isinstance(cliente, Cliente):
            raise ErrorValidacionCliente("Solo se pueden registrar objetos de tipo Cliente.")
        if any(c.id_entidad == cliente.id_entidad for c in self.clientes):
            raise ErrorValidacionCliente(f"Ya existe un cliente con el ID '{cliente.id_entidad}'.")
        self.clientes.append(cliente)
        return cliente

    # Registra un servicio validado en la lista interna
    def registrar_servicio(self, servicio):
        if not isinstance(servicio, Servicio):
            raise ErrorServicio("Solo se pueden registrar objetos de tipo Servicio.")
        if any(s.id_entidad == servicio.id_entidad for s in self.servicios):
            raise ErrorServicio(f"Ya existe un servicio con el ID '{servicio.id_entidad}'.")
        self.servicios.append(servicio)
        return servicio

    # Busca un cliente por su ID o lanza ErrorValidacionCliente
    def buscar_cliente(self, id_cliente):
        for cliente in self.clientes:
            if cliente.id_entidad == id_cliente.strip().upper():
                return cliente
        raise ErrorValidacionCliente(f"Cliente con ID '{id_cliente}' no encontrado.")

    # Busca un servicio por su ID o lanza ErrorServicio
    def buscar_servicio(self, id_servicio):
        for servicio in self.servicios:
            if servicio.id_entidad == id_servicio.strip().upper():
                return servicio
        raise ErrorServicio(f"Servicio con ID '{id_servicio}' no encontrado.")

    # Crea y opcionalmente confirma una reserva
    def crear_reserva(self, id_reserva, id_cliente, id_servicio, duracion, asistentes=1, descuento_pct=0.0, confirmar_ahora=True):
        try:
            cliente = self.buscar_cliente(id_cliente)
            servicio = self.buscar_servicio(id_servicio)
            reserva = Reserva(
                id_reserva=id_reserva,
                cliente=cliente,
                servicio=servicio,
                duracion=duracion,
                asistentes=asistentes,
                descuento_pct=descuento_pct,
            )
            if confirmar_ahora:
                reserva.confirmar()
        except (ErrorValidacionCliente, ErrorServicio) as exc:
            # Encadena el error de origen en un ErrorReserva para trazabilidad completa
            raise ErrorReserva(f"No fue posible crear la reserva '{id_reserva}'.") from exc

        self.reservas.append(reserva)
        return reserva


# ==============================================================================
# Funcion auxiliar para ejecutar cada operacion con try/except/else/finally
# ==============================================================================

# Ejecuta una operacion de simulacion, registra resultados y errores en el log
# y demuestra que el sistema continua funcionando sin detenerse
def ejecutar_operacion(numero_op, descripcion, accion):
    print(f"\n--- Operacion {numero_op}: {descripcion} ---")
    exito = False
    try:
        resultado = accion()
    except ErrorSoftwareFJ as exc:
        # Maneja errores propios del dominio Software FJ y su causa encadenada si existe
        causa = f" | Causa: {exc.__cause__}" if exc.__cause__ else ""
        mensaje_error = f"Op {numero_op} [{exc.codigo}] fallida: {exc}{causa}"
        logger.error(mensaje_error)
        print(f"[ERROR CONTROLADO] {mensaje_error}")
    except Exception as exc:
        # Captura cualquier otra excepcion no prevista para evitar caidas del programa
        mensaje_error = f"Op {numero_op} [ERR_INESPERADO] fallida: {type(exc).__name__}: {exc}"
        logger.critical(mensaje_error)
        print(f"[ERROR CRITICO CONTROLADO] {mensaje_error}")
    else:
        # Se ejecuta unicamente cuando no ocurren excepciones en el bloque try
        exito = True
        mensaje_ok = f"Op {numero_op} exitosa ({descripcion}): {resultado}"
        logger.info(mensaje_ok)
        print(f"[EXITO] {resultado}")
    finally:
        # Se ejecuta siempre al finalizar el intento, haya ocurrido error o no
        estado_op = "COMPLETADA" if exito else "RECHAZADA CON MANEJO DE ERROR"
        logger.info(f"Fin de Operacion {numero_op} -> Estado: {estado_op}")
        print(f"[FINALLY] Operacion {numero_op} finalizada -> {estado_op}")


# ==============================================================================
# Simulacion de 12 operaciones completas 
# ==============================================================================

def simular_sistema():
    gestor = GestorSoftwareFJ()
    logger.info("=== INICIO DE SIMULACION SOFTWARE FJ (SIN BASE DE DATOS) ===")

    # Operacion 1: Registro valido de cliente
    ejecutar_operacion(
        1,
        "Registro valido de cliente corporativo",
        lambda: gestor.registrar_cliente(
            Cliente("CLI-01", "Camila Restrepo", "camila.restrepo@empresa.co", "3104567890")
        ).resumen(),
    )

    # Operacion 2: Registro invalido de cliente (correo sin arroba / formato incorrecto)
    ejecutar_operacion(
        2,
        "Registro invalido de cliente por correo incorrecto",
        lambda: gestor.registrar_cliente(
            Cliente("CLI-02", "Andres Mejia", "correo_invalido.com", "3009876543")
        ).resumen(),
    )

    # Operacion 3: Segundo registro valido de cliente
    ejecutar_operacion(
        3,
        "Registro valido de segundo cliente",
        lambda: gestor.registrar_cliente(
            Cliente("CLI-03", "Valeria Ospina", "v.ospina@innovatech.org", "3152233445")
        ).resumen(),
    )

    # Operacion 4: Creacion correcta de servicio 1 (ReservaSala)
    ejecutar_operacion(
        4,
        "Creacion correcta de servicio ReservaSala",
        lambda: gestor.registrar_servicio(
            ReservaSala("SRV-01", "Sala Ejecutiva Andromeda", tarifa_base=80.0, capacidad_maxima=12)
        ).describir(),
    )

    # Operacion 5: Creacion correcta de servicio 2 (AlquilerEquipo)
    ejecutar_operacion(
        5,
        "Creacion correcta de servicio AlquilerEquipo",
        lambda: gestor.registrar_servicio(
            AlquilerEquipo("SRV-02", "Estacion Workstation GPU", tarifa_base=35.0, categoria_equipo="Computo", cargo_soporte=20.0)
        ).describir(),
    )

    # Operacion 6: Creacion correcta de servicio 3 (AsesoriaEspecializada)
    ejecutar_operacion(
        6,
        "Creacion correcta de servicio AsesoriaEspecializada",
        lambda: gestor.registrar_servicio(
            AsesoriaEspecializada("SRV-03", "Auditoria de Arquitectura Cloud", tarifa_base=120.0, especialidad="Cloud & DevOps", multiplicador_complejidad=1.5)
        ).describir(),
    )

    # Operacion 7: Creacion incorrecta de servicio (tarifa base negativa o cero)
    ejecutar_operacion(
        7,
        "Creacion incorrecta de servicio con tarifa negativa",
        lambda: gestor.registrar_servicio(
            ReservaSala("SRV-04", "Sala Beta", tarifa_base=-50.0, capacidad_maxima=8)
        ).describir(),
    )

    # Operacion 8: Reserva exitosa de sala con calculo de costo y descuento opcional
    ejecutar_operacion(
        8,
        "Reserva exitosa de sala con descuento del 10%",
        lambda: gestor.crear_reserva(
            "RES-01", "CLI-01", "SRV-01", duracion=4, asistentes=10, descuento_pct=0.10
        ).resumen(),
    )

    # Operacion 9: Reserva exitosa de alquiler de equipos
    ejecutar_operacion(
        9,
        "Reserva exitosa de alquiler de 2 equipos por 3 horas",
        lambda: gestor.crear_reserva(
            "RES-02", "CLI-03", "SRV-02", duracion=3, asistentes=2, descuento_pct=0.0
        ).resumen(),
    )

    # Operacion 10: Reserva fallida por exceder el aforo permitido de la sala
    ejecutar_operacion(
        10,
        "Reserva fallida de sala por exceso de asistentes (20 > 12)",
        lambda: gestor.crear_reserva(
            "RES-03", "CLI-01", "SRV-01", duracion=2, asistentes=20
        ).resumen(),
    )

    # Operacion 11: Reserva fallida por servicio marcado como no disponible
    def reservar_servicio_no_disponible():
        srv_no_disp = AsesoriaEspecializada(
            "SRV-05", "Consultoria IA Generativa", tarifa_base=150.0, especialidad="IA", disponible=False
        )
        gestor.registrar_servicio(srv_no_disp)
        return gestor.crear_reserva("RES-04", "CLI-03", "SRV-05", duracion=2, asistentes=1).resumen()

    ejecutar_operacion(
        11,
        "Reserva fallida por servicio no disponible",
        reservar_servicio_no_disponible,
    )

    # Operacion 12: Reserva exitosa de asesoria especializada (demostrando estabilidad continua)
    ejecutar_operacion(
        12,
        "Reserva exitosa de asesoria especializada tras errores previos",
        lambda: gestor.crear_reserva(
            "RES-05", "CLI-03", "SRV-03", duracion=5, asistentes=3, descuento_pct=0.05
        ).resumen(),
    )

    # Resumen final en consola y archivo de log
    print("\n=== RESUMEN FINAL DEL SISTEMA (SIN BASE DE DATOS) ===")
    print(f"Clientes registrados: {len(gestor.clientes)} | Servicios: {len(gestor.servicios)} | Reservas: {len(gestor.reservas)}")
    for r in gestor.reservas:
        print("  *", r.resumen())
    print(f"\nArchivo de registro generado en: {RUTA_LOG}")
    logger.info("=== FIN DE SIMULACION SOFTWARE FJ ===")


# Punto de entrada principal del programa
if __name__ == "__main__":
    simular_sistema()