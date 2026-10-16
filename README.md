# Diseño Arquitectural del Sistema FitZone

## 1. Descripción del Proyecto

**FitZone** es un sistema enfocado en la gestión de reservaciones de clases grupales, control de acceso, membresías, asistencias y listas de espera dentro de un gimnasio.

El presente proyecto desarrolla el diseño arquitectural del sistema a partir del análisis de requerimientos realizado en la Unidad 2, considerando el documento de requerimientos del negocio (BRD), las historias de usuario, los diagramas BPMN y el glosario del proyecto.

El objetivo es establecer una arquitectura organizada, donde cada componente tenga responsabilidades definidas y las reglas del negocio se encuentren separadas de los detalles técnicos de implementación.

Para ello, se utiliza principalmente el patrón **Domain Model**, complementado con una capa de aplicación (*Service Layer*), los patrones *Repository* y *Unit of Work*, y un enfoque básico de **CQRS** para separar las operaciones que modifican información de aquellas que únicamente realizan consultas.

Esta arquitectura busca mejorar la consistencia de las reservaciones, evitar que se exceda la capacidad de las clases, automatizar la gestión de las listas de espera y reducir los problemas relacionados con el control de acceso y el registro de asistencias.

## 2. Empresa

FitZone pertenece al sector del acondicionamiento físico y ofrece servicios relacionados con el gimnasio y las clases grupales.

Para organizar estos servicios, se plantea una solución de software que facilite la interacción entre los socios, el personal de recepción, los instructores y los administradores.

El sistema considera las necesidades operativas del gimnasio, especialmente la administración de las clases, la disponibilidad de lugares, la vigencia de las membresías y el seguimiento de las asistencias.

## 3. Alcance del Proyecto

### 3.1 Funcionalidades incluidas

El diseño contempla los siguientes procesos:

- **Consulta de clases:** consultar el calendario, los horarios, los instructores y los lugares disponibles.
- **Gestión de reservaciones:** registrar y cancelar reservaciones de clases grupales.
- **Validación de membresías:** comprobar que la membresía esté vigente y que el plan incluya la clase solicitada.
- **Control de capacidad:** evitar que el número de reservaciones y ofertas vigentes supere la capacidad máxima.
- **Listas de espera:** registrar a los socios cuando una clase está llena y administrar las ofertas de lugares disponibles.
- **Control de acceso:** validar las condiciones de acceso y registrar la asistencia.
- **Registro de no-shows:** identificar las reservaciones en las que el socio no se presentó.
- **Administración de clases:** permitir la cancelación, reprogramación y reasignación de instructores por parte del administrador.
- **Indisponibilidad de instructores:** registrar los avisos de los instructores que no pueden impartir una clase.
- **Notificaciones:** contemplar avisos a los socios sobre ofertas de lugares y cambios en las clases.
- **Indicadores:** consultar información sobre ocupación, no-shows y listas de espera.

### 3.2 Funcionalidades fuera del alcance

El diseño no contempla:

- Procesamiento de pagos.
- Contratación o modificación de membresías.
- Administración de nómina y contabilidad.
- Gestión completa de entrenamientos personalizados.
- Servicios adicionales no relacionados con el alcance definido.

Además, las políticas definitivas de cancelación, las consecuencias de los no-shows y el tiempo límite para confirmar una oferta de la lista de espera requieren validación antes de la implementación definitiva.

## 4. Reglas de Negocio Principales

El diseño arquitectural considera las siguientes reglas:

- Solo el administrador puede cancelar, reprogramar o reasignar clases.
- Los instructores únicamente pueden reportar su indisponibilidad.
- El personal de recepción puede gestionar reservaciones y cancelaciones, pero no modificar membresías.
- La capacidad máxima de una clase no debe superarse.
- Las membresías vencidas impiden realizar reservaciones o acceder al servicio.
- Los planes deben incluir el tipo de clase que el socio desea reservar.
- Los no-shows deben registrarse en el historial sin aplicar sanciones en el diseño actual.
- Las cancelaciones y las ofertas de lugares deben respetar las políticas y los tiempos de confirmación que se definan para el negocio.

Estas reglas se utilizan como base para distribuir las responsabilidades entre los componentes del dominio y los servicios de aplicación.

## 5. Arquitectura del Sistema

La arquitectura se organiza en tres capas principales:

### 5.1 Capa de Dominio

Contiene los conceptos principales del negocio, las reglas que deben cumplirse y las operaciones que modifican el estado del sistema.

Entre sus componentes destacan `ClaseGrupal`, `Membresia`, `PlanDeMembresia`, `HistorialDeAsistencia` y `AvisoDeIndisponibilidad`.

También incluye objetos de valor, servicios de dominio, políticas y eventos que permiten representar las reglas y los cambios importantes del negocio.

### 5.2 Capa de Aplicación

Coordina la ejecución de los casos de uso mediante servicios de aplicación, comandos y consultas.

Sus responsabilidades incluyen recuperar los objetos necesarios, solicitar al dominio la validación o ejecución de las operaciones, controlar las transacciones y devolver los resultados correspondientes.

La capa de aplicación no debe duplicar las reglas del negocio, sino delegarlas a los componentes responsables del dominio.

### 5.3 Capa de Infraestructura

Contiene los detalles técnicos necesarios para implementar la persistencia y las integraciones externas.

Incluye las implementaciones de los repositorios, los mecanismos de almacenamiento, el control de transacciones y los componentes utilizados para enviar notificaciones.

De esta manera, el dominio puede mantener su independencia respecto a la base de datos y a los detalles específicos de implementación.

## 6. Patrones de Diseño Utilizados

- **Domain Model:** concentra las reglas del negocio en los objetos que son responsables de ellas.
- **Service Layer:** coordina los casos de uso sin concentrar toda la lógica en un solo servicio.
- **Repository:** abstrae el acceso y la persistencia de los objetos del dominio.
- **Unit of Work:** administra las transacciones y permite confirmar o revertir los cambios.
- **CQRS básico:** separa los comandos que modifican el estado de las consultas que recuperan información.
- **Eventos de dominio:** comunican cambios importantes para permitir acciones posteriores, como enviar notificaciones o actualizar el historial.

La combinación de estos patrones busca reducir el acoplamiento, evitar la duplicación de reglas y mantener la consistencia de la información.

## 7. Componentes Principales del Dominio

- **ClaseGrupal:** controla la capacidad de la clase, las reservaciones, la lista de espera y el registro de asistencias.
- **Membresia:** representa la suscripción del socio y permite consultar su vigencia.
- **PlanDeMembresia:** determina qué tipos de clases están incluidos en el plan.
- **Reserva:** registra el lugar apartado por un socio y su estado.
- **EntradaDeListaDeEspera:** representa la participación y posición de un socio en la lista de espera.
- **HistorialDeAsistencia:** conserva los registros de asistencias, cancelaciones y no-shows.
- **AvisoDeIndisponibilidad:** registra los avisos de los instructores.
- **ReglasDeElegibilidad:** verifica que la membresía esté vigente y que el plan incluya la clase solicitada.

Estos componentes colaboran para que las operaciones respeten las reglas definidas y los estados del sistema se mantengan consistentes.

## 8. Estructura del Repositorio

El repositorio está organizado en dos carpetas principales:

```text
Actividad-Fundamental-3/
│
├── docs/
│   └── Informe de Arquitectura_Equipo 11.pdf
│
├── diagrams/
│   ├── Diagrama de Dominio y Clases_Equipo 11.pdf
│   ├── Diagrama de Mapa de Dominio_Equipo 11.pdf
│   ├── Diagrama de Secuencia #1_Equipo 11.pdf
│   └── Diagrama de Secuencia #2_Equipo 11.pdf
│
└── README.md
```

### 8.1 Carpeta `docs/`

Contiene el informe de arquitectura, donde se documentan el alcance, los casos de uso, la selección de patrones, el modelo de dominio, los servicios de aplicación, la persistencia, las decisiones arquitecturales y la trazabilidad de los requerimientos.

### 8.2 Carpeta `diagrams/`

Contiene las representaciones gráficas utilizadas para explicar la estructura del dominio y el comportamiento de los componentes durante los principales procesos del sistema.

## 9. Enlaces a los Entregables

### Documentación

- [Informe de Arquitectura — Equipo 11](./docs/Informe%20de%20Arquitectura_Equipo%2011.pdf)

### Diagramas

- [Diagrama de Dominio y Clases — Equipo 11](./diagrams/Diagrama%20de%20Dominio%20y%20Clases_Equipo%2011.pdf)
- [Diagrama de Mapa de Dominio — Equipo 11](./diagrams/Diagrama%20de%20Mapa%20de%20Dominio_Equipo%2011.pdf)
- [Diagrama de Secuencia #1 — Equipo 11](./diagrams/Diagrama%20de%20Secuencia%20%231_Equipo%2011.pdf)
- [Diagrama de Secuencia #2 — Equipo 11](./diagrams/Diagrama%20de%20Secuencia%20%232_Equipo%2011.pdf)

## 10. Instrucciones de Consulta

Para revisar los entregables del proyecto:

1. Acceder a la carpeta `docs/` y abrir el informe de arquitectura para conocer el diseño completo.
2. Consultar el diagrama de dominio y clases para identificar los componentes y sus relaciones.
3. Revisar el mapa de dominio para comprender los conceptos principales y los límites del modelo.
4. Consultar los diagramas de secuencia para observar cómo interactúan los componentes durante las operaciones.
5. Relacionar los diagramas con las reglas de negocio y los casos de uso descritos en el informe.

Este repositorio contiene documentación del diseño arquitectural. No representa, por sí mismo, una implementación ejecutable del sistema.

## 11. Decisiones Arquitecturales

El informe registra cuatro decisiones arquitecturales principales (*Architecture Decision Records*, ADR):

- **ADR-001:** selección de Domain Model como patrón principal de lógica de negocio.
- **ADR-002:** definición de `ClaseGrupal` como agregado que contiene las reservaciones y la lista de espera.
- **ADR-003:** utilización de eventos de dominio, Unit of Work y una bandeja de salida para desacoplar las notificaciones y las actualizaciones del historial.
- **ADR-004:** propuesta de políticas configurables para manejar las cancelaciones, las consecuencias de los no-shows y los tiempos de confirmación, pendiente de validación por parte de FitZone.

Estas decisiones documentan las alternativas consideradas, sus consecuencias y su relación con las necesidades del sistema.

## 12. Objetivo del Diseño

El propósito de esta arquitectura es proporcionar una base organizada para el desarrollo futuro de FitZone, distribuyendo las responsabilidades entre los componentes y protegiendo las reglas fundamentales del negocio.

Se busca reducir los accesos inconsistentes, evitar que se exceda la capacidad de las clases, automatizar la lista de espera y facilitar el seguimiento de las asistencias.

La arquitectura también permite que los cambios futuros en las políticas del gimnasio puedan incorporarse sin tener que modificar indiscriminadamente todos los casos de uso.

## 13. Información Académica

- **Curso:** Componentes de Lógica de Negocios.
- **Actividad:** Diseño Arquitectural del Sistema FitZone.
- **Equipo:** 11.
- **Profesor:** Miguel Ángel Gómez Marroquín.
- **Fecha:** 16 de octubre de 2026.

---

**Proyecto académico — Diseño Arquitectural del Sistema FitZone.**
