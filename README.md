Gestión de Clientes y Órdenes en SQL Server
=============================================

**Resumen**
-----------

Este proyecto implementa una base de datos llamada ClientOrderDB diseñada para gestionar la información de clientes y sus órdenes asociadas. La base de datos almacena y manipula datos de clientes, registra sus órdenes, y realiza consultas y operaciones avanzadas como procedimientos almacenados, triggers, y funciones.

**Estructura de la Base de Datos**
--------------------------------

### 1. Creación de la Base de Datos

La base de datos ClientOrderDB es creada y seleccionada para uso mediante los siguientes comandos:

```sql
CREATE DATABASE ClientOrderDB;
USE ClientOrderDB;
```
### 2. Tablas
Tabla Clientes

Almacena la información básica de los clientes, incluyendo un ID único, nombre y correo electrónico

    CREATE TABLE Clientes(
        ClienteID INT IDENTITY(1,1) PRIMARY KEY,
        Nombre VARCHAR(100),
        Email VARCHAR(100)
    );

Tabla Ordenes

Registra las órdenes realizadas por los clientes, incluyendo un ID de orden, la fecha de la orden, el total de la orden, y un ID de cliente como llave foránea que referencia la tabla Clientes.

    CREATE TABLE Ordenes(
        OrdenID INT IDENTITY(1,1) PRIMARY KEY,
        ClienteID INT,
        FechaOrden DATE,
        TotalOrden DECIMAL(10,2),
        FOREIGN KEY (ClienteID) REFERENCES Clientes(ClienteID)
    );

Operaciones y Consultas
### 3. Insertar Datos

Datos iniciales son insertados tanto en la tabla Clientes como en la tabla Ordenes:

    INSERT INTO Clientes (Nombre, Email) VALUES
    ('Juan Pérez', 'juan.perez@example.com'),
    ('Ana Gómez', 'ana.gomez@example.com'), ...;
    
    INSERT INTO Ordenes (ClienteID, FechaOrden, TotalOrden) VALUES
    (1, '2024-01-01', 150.75), ...;

### 4. Consultas
Consultar Clientes y Órdenes

Se puede obtener una lista de todos los clientes junto con sus órdenes correspondientes usando un JOIN entre las tablas Clientes y Ordenes:

    SELECT Ordenes.ClienteID, Clientes.Nombre, Ordenes.FechaOrden, Ordenes.TotalOrden
    FROM Clientes
    INNER JOIN Ordenes ON Clientes.ClienteID = Ordenes.ClienteID;

### 5. Procedimientos Almacenados
OrdenesClientesSP

Este procedimiento almacenado permite obtener las órdenes asociadas a un cliente específico proporcionando su ClienteID:

    CREATE PROCEDURE OrdenesClientesSP @ClienteId INT AS
    BEGIN
        SELECT Ordenes.OrdenID, Ordenes.FechaOrden, Ordenes.TotalOrden, Clientes.Nombre
        FROM Ordenes
        INNER JOIN Clientes ON Ordenes.ClienteID = Clientes.ClienteID
        WHERE Clientes.ClienteID = @ClienteID;
    END;
    
Ejemplo de ejecución:
-----------

    EXEC OrdenesClientesSP @ClienteID = 5;

### 6. Triggers
ValidacionTotalTRG

Este trigger garantiza que el total de una orden sea mayor a 0 antes de ser insertado en la tabla Ordenes:

    CREATE TRIGGER ValidacionTotalTRG
    ON Ordenes
    AFTER INSERT
    AS
    BEGIN
        IF EXISTS (SELECT 1 FROM inserted WHERE TotalOrden <= 0)
        BEGIN
            RAISERROR ('El total de la orden debe ser mayor a 0', 16, 1);
            ROLLBACK TRANSACTION;
        END;
    END;

### 7. Funciones
OrdenesPorFecha

Esta función devuelve el número de órdenes realizadas en una fecha específica:

    CREATE FUNCTION OrdenesPorFecha (@Fecha DATE)
    RETURNS INT
    AS
    BEGIN
        DECLARE @NumeroOrdenes INT;
        SELECT @NumeroOrdenes = COUNT(*) FROM Ordenes WHERE FechaOrden = @Fecha;
        RETURN @NumeroOrdenes;
    END;

Ejemplo de uso:
-----------

    SELECT dbo.OrdenesPorFecha('2024-12-01') AS NumeroOrdenes;

Cómo Ejecutar el Proyecto

  Crear la Base de Datos: Ejecuta los scripts SQL para crear la base de datos y las tablas.
  
  Insertar Datos: Llena las tablas con los datos de ejemplo proporcionados.
  
  Realizar Consultas y Operaciones: Utiliza las consultas, procedimientos almacenados, triggers y funciones para interactuar con los datos.

Este proyecto demuestra conceptos clave en el manejo de bases de datos relacionales usando SQL Server, incluyendo la creación de tablas, llaves foráneas, procedimientos almacenados, triggers y funciones definidas por el usuario.









    
