# Requisitos 

## Requisitos de negocio 
Descripción de las necesidades y objetivos de negocio que motivan el desarrollo del sistema.

Utilizar una plataforma de facturas y cobros propia
Independiente a el resto del negocio (luego ellos la conectan)
Agilizar los cobros

## Requisitos de usuario 
Descripción de las necesidades y servicios que el sistema debe proporcionar a sus usuarios. 

Poder hacer Log in pero no Sign in
Poder acceder a un historial de facturas con opcion a descarga de cada una
Tipos de facturas:
    -> Puntual
    -> Recurrente
    -> Por fechas

Tipo de cobros:
    -> Nuevo
    -> Existente

Tipos de usuario:
    -> Admin
    -> Lector

## Requisitos del sistema 

### Requisitos Funcionales 
- FR1. Posibilidad de Log in con correo y contraseña proporcionados por TurbineH
- FR2. Recordatorios vía correo electrónico 
- FR2.1. Recordatorio de pago 5 y 3 días antes de expiración de la suscripción 
- FR2.2. Recordaritorio por impago post expiración cada 2-3 días 
- FR3. Posibilidad a dar de baja (si hay algo no pagado reordatorio por impago y no se procesa la baja)
- FR4. Acceso a historial de recibos
- FR5. Generación de facturas
- FR6. Descarga de recibos y facturas formato PDF
- FR7. Confirmación de pago via email
- FR8. Generación de perfiles por parte de los ADMIN
- FR8.1. ADMIN puede designar roles
- FR8.1. Designación de Super ADMIN
- FR9. Modificación de los plazos y modos de pago 
- FR10. Acceso a calendario de facturación para ver de forma Semanal-Mensual-Anual el progreso de la facturación de la empresa


### Requisitos No-Funcionales 
- NFR1. Cifrado de Datos (IMPORTANTE)
- NFR2. Escalabilidad (modularización)
- NFR3. Rapidez (a ser posible en menos de 1 min tiene q estar realizado el pago, pocos click)
- NFR4. Multiplataforma
- NFR5. Guardado de datos por temas legales (5 años)
- NFR6. Facturas generadas tienen q estar estandarizadas
- NFR7. Interfaz visual siguiendo el modelo visual de TurbineH
