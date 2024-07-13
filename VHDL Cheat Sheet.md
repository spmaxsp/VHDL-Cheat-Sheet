# VHDL Cheat Sheet

## 0. Table of Contents
1. [Entity Declaration](#1-entity-declaration)
2. [Architecture Declaration](#2-architecture-declaration)
    1. [Signal Assignment](#211-conditional-signal-assignment-when-else)
    2. [Selected Signal Assignment](#212-selected-signal-assignment-with-select)
    3. [Entity Instantiation as Component](#22-entity-instantiation-as-component)
    4. [Direct Entity Instantiation](#23-direct-entity-instantiation)
3. [Process Statement](#3-process-statement)
    1. [Sequential Statements](#31-sequential-statements)
    2. [Wait Statements](#32-wait-statements)
4. [Operators](#4-operators)
    1. [Relational Operators](#41-relational-operators)
    2. [Logical Operators](#42-logical-operators)
    3. [Shift Operators](#43-shift-operators)
5. [Logical Values with STD_LOGIC](#5-logical-values-with-std_logic)
    1. [Bus Resolution Functions](#51-bus-resolution-functions)
6. [Numerical Values with NUMERIC_STD](#6-numerical-values-with-numeric_std)
7. [Attributes](#7-attributes)
8. [Libraries and Packages](#8-libraries-and-packages)
    1. [Using Libraries and Packages](#81-using-libraries-and-packages)
    2. [Standard Libraries](#82-standard-libraries)
    3. [Defining Libraries and Packages](#83-defining-libraries-and-packages)
9. [Functions and Procedures](#9-functions-and-procedures)
    1. [Functions](#91-functions)
    2. [Procedures](#92-procedures)
10. [Configuration](#10-configuration)
    1. [Entity Configuration (Type 1)](#101-entity-configuration-type-1)
    2. [Instance Configuration (Type 2)](#102-instance-configuration-type-2)
11. [Data Types](#11-data-types)
    1. [Categories of Data Types](#111-categories-of-data-types)
    2. [Enumerated Types](#111-enumerated-types)
    3. [Numeric Types](#111-numeric-types)
        1. [Integer and Real Types](#1111-integer-and-real-types)
        2. [Physical Types](#1112-physical-types)
12. [Testbenches (For Simulation Only)](#12-testbenches-for-simulation-only)
    1. [Testbench Structure](#121-testbench-structure)
    2. [TEXTIO Package](#122-textio-package)
13. [Assertion Statements (For Simulation Only)](#13-assertion-statements-for-simulation-only)
14. [Modeling Delays (For Simulation Only)](#14-modeling-delays-for-simulation-only)
    1. [Transport Delay](#141-transport-delay)
    2. [Inertial Delay](#142-inertial-delay)


## 1. Entity Declaration

A entity is a module that describes the interface of a digital circuit.

```vhdl
ENTITY entity_name IS
    GENERIC ( generic_name : type := default_value; );
    PORT ( a, b : <port_direction> <port_type>;
              c : <port_direction> <port_type>);
END ENTITY entity_name; 
```

* Valid Port Directions: `IN`, `OUT`, `INOUT`, `BUFFER`
* Basic valid Port Types: `BOOLEAN`, `BIT`, `INTEGER`, `REAL`, `CHARACTER` or `BIT_VECTOR (1 DOWNTO 0)`
* Generic is used to pass parameters to the entity. It is optional.
* Ports are used to communicate with the outside world.


## 2. Architecture Declaration

An architecture is a module that describes the behavior of a digital circuit.

```vhdl
ARCHITECTURE architecture_name OF entity_name IS
    SIGNAL signal_name : type;
BEGIN
    -- VHDL code here
END ARCHITECTURE architecture_name;
```
* Internal Signals are used to communicate between different parts of the architecture.

### 2.1. Signal Assignment

```vhdl
signal_name <= value;
```
* `<=` is the signal assignment operator.
* This is a concurrent statement and is executed whenever any of the signals on the right-hand side change.

#### 2.1.1. Conditional Signal Assignment (WHEN ELSE)

```vhdl
signal_name <= value1 WHEN condition ELSE
               value2 WHEN condition ELSE
                 ...
               other_value;
```
* `WHEN` is used to assign a value to a signal based on a condition.
* `ELSE` is optional. If not present, the signal will retain its previous value.

#### 2.1.2. Selected Signal Assignment (WITH SELECT)

```vhdl
WITH selector SELECT
    signal_name <= value1 WHEN sel_value1,
                   value2 WHEN sel_value2,
                     ...
                   other_value WHEN OTHERS;
```

* `SELECT` is used to assign a value to a signal based on the value of a selector.
* `OTHERS` is used to catch all other cases. It is always required.

### 2.2. Entity Instantiation as Component

A component is used to instantiate a 'socket'. A socket is a placeholder for an entity that is used in an architecture.

```vhdl
ARCHITECTURE architecture_name OF entity_name IS
    COMPONENT component_name
    PORT ( a, b : <port_type>;
              c : <port_type>);
    END COMPONENT component_name;
BEGIN
    instance_name : component_name PORT MAP (a, b, c);
END ARCHITECTURE architecture_name;
```

* `PORT MAP` is used to connect the ports of the entity to the signals in the architecture.
* The order of the ports in the entity and the architecture must match.
* Ports can be left unconnected by using the `open` keyword. (input ports then require a default value)

* `GENERIC MAP` is used to pass parameters to the entity. It is optional.

### 2.3. Direct Entity Instantiation

```vhdl
instance_name : ENTITY library_name.entity_name(architecture_name) PORT MAP (a, b, c);
```

* `library_name` is the name of the library where the entity is defined. (if not specified, the default "`work`" library is used)

## 3. Process Statement

A process is used in an architecture to describe the behavior of a digital circuit.
A process is a sequential block of code that is executed whenever any of the signals in the sensitivity list change.

```vhdl
ARCHITECTURE architecture_name OF entity_name IS
BEGIN
    PROCESS (senitivity_list) IS
        VARIABLE variable_name : type;
    BEGIN
        -- VHDL code here
    END PROCESS;
END ARCHITECTURE architecture_name;
```
* The sensitivity list is a list of signals that the process is sensitive to. If any of these signals change, the process is executed.
* Variables are used to store temporary values within a process.
* To assign a value to a variable, use `:=` instead of `<=`.
* Signals take on their new values after the process has completed.

#### 3.1. Sequential Statements

* `IF` statement:
    ```vhdl
    IF condition THEN
        -- VHDL code here
    ELSIF condition THEN
        -- VHDL code here
    ELSE
        -- VHDL code here
    END IF;
    ```

* `CASE` statement:
    ```vhdl
    CASE expression IS
        WHEN value1 =>
            -- VHDL code here
        WHEN value2 =>
            -- VHDL code here
        ...
        WHEN OTHERS =>
            -- VHDL code here
    END CASE;
    ```
    * `OTHERS` is used to catch all other cases. It is always required. (can be replaced with `WHEN OTHERS => NULL;`)

* `WHILE` statement:
    ```vhdl
    WHILE condition LOOP
        -- VHDL code here
    END LOOP;
    ```

* `FOR` statement:
    ```vhdl
    FOR i IN range LOOP
        -- VHDL code here
    END LOOP;
    ```

    * the loop variable `i` can be used in the loop.
    * `range` can be the length of a signal (`signal'RANGE`), a range (`0 TO 7`), or a list of values (`0, 1, 2, 3`).

* `LOOP`-`EXIT` statement:
    ```vhdl
    LOOP
        -- VHDL code here
        EXIT WHEN condition;
    END LOOP;
    ```

    * `EXIT` is used to exit the loop when a condition is met.

* `NEXT` statement:
    ```vhdl
    FOR i IN range LOOP
        NEXT WHEN condition;
        -- VHDL code here
    END LOOP;
    ```

    * `NEXT` is used to skip the rest of the loop and continue with the next iteration.

* Labeling a Loop:
    ```vhdl
    label_name: 
    LOOP
        -- VHDL code here
        NEXT label_name WHEN condition;
    END LOOP label_name;
    ```

    * A label can be used to exit a loop from within a nested loop.
    * `NEXT`, `EXIT`, and `RETURN` can be used with a label, as well as a conditional statement.

### 3.2. Wait Statements

Wait statements are used to pause the execution of a process until a certain condition is met.
They replace the sensitivity list in a process.

* `WAIT;` - wait indefinitely.
* `WAIT ON signal_name;` - wait until signal_name changes.
* `WAIT FOR time;` - wait for a certain amount of time.
* `WAIT UNTIL condition;` - wait until a condition is met.

The `ON`, `FOR`, and `UNTIL` can be combined:

```vhdl
WAIT ON signal_name  UNTIL condition FOR time; 
-- Process runns when condition is met and signal_name changes or time has passed.
```

A `WAIT ON` statement at the end of a process is equivalent to a sensitivity list.


## 4. Operators

### 4.1. Relational Operators#

`=`, `/=`, `<`, `<=`, `>`, `>=`

### 4.2. Logical Operators

`AND`, `OR`, `NAND`, `NOR`, `XOR`, `XNOR`, `NOT` (defined for BOOLEAN and BIT types)

* `NOT` has the highest precedence all other operators are evaluated from left to right.

### 4.3. Shift Operators

`SLL`, `SRL`, `SLA`, `SRA`, `ROL`, `ROR`

* shift logical -> void and fill with 0
* shift arithmetic -> void and fill with sign bit
* rotate logical -> fill with bits from the other side


## 5. Logical Values with STD_LOGIC

The `IEEE.STD_LOGIC_1164` package defines the `STD_ULOGIC` and `STD_ULOGIC_VECTOR` data types.
They are used to represent digital signals with additional states.

* `U` - uninitialized
* `X` - unknown
* `0` - logic 0
* `1` - logic 1
* `Z` - high impedance
* `W` - weak unknown
* `L` - weak logic 0
* `H` - weak logic 1
* `-` - don't care

The compiler will generate errors if a signal is driven by multiple sources with conflicting values.

### 5.1. Bus Resolution Functions

The Bus Resolution Functions are used to resolve multiple sources driving a signal.

Using the data type `STD_LOGIC` and `STD_LOGIC_VECTOR` will introduce the Bus Resolution Functions.

```text
The Result will be the value hightest up in scheme:
        U
        │  
        X
    ┌───┼───┐
    0   -   1
    └───┬───┘
        W
    ┌───┴───┐
    L       H
    └───┬───┘
        Z
```


## 6. Numerical Values with NUMERIC_STD

The `IEEE.NUMERIC_STD` and `IEEE.NUMERIC_BIT` allows for numeric operations on Vector types.
Therefor the `SIGNED` and `UNSIGNED` data types are defined.
`NUMERIC_STD` uses the `STD_LOGIC_VECTOR` data type.
`NUMERIC_BIT` uses the `BIT_VECTOR` data type.

For Converting from and to `STD_LOGIC_VECTOR` use the following functions:
* `unsigned()` 
* `signed()`
* `std_logic_vector()`


## 7. Attributes

Attributes are used to get information about signals, types, or values.

* `signal'EVENT` - returns TRUE if the signal has changed in the current simulation cycle.
* `signal'ACTIVE` - returns TRUE if the signal is currently driven.
* `signal'STABLE(t)` - returns TRUE if the signal has been no Event for t time units. (Event = change of signal value)
* `signal'QUIET(t)` - returns TRUE if the signal has been no Transaction for t time units. (Transaction = update of signal value)
* `signal'DELAYED(t)` - returns the value of the signal delayed by t time units.
* `signal'TRANSACTION` - BIT that flips on every Transaction of the signal.
* `signal'LAST_EVENT` - returns the time since the last Event of the signal.
* `signal'LAST_ACTIVE` - returns the time since the last Transaction of the signal.
* `signal'LAST_VALUE` - returns the value of the signal before the last Event.

* `signal'LENGTH` - returns the length of the signal.
* `signal'RANGE` - returns the range of the signal. (e.g. `0 TO 7`) (used for loops)
* `signal'REVERSE_RANGE` - returns the reverse range of the signal. (e.g. `7 DOWNTO 0`) (used for loops)

`RISING_EDGE(signal)` and `FALLING_EDGE(signal)` are predefined attributes by the `IEEE.STD_LOGIC_1164` package.
Returns TRUE if the signal has a rising or falling edge in the current simulation cycle.


## 8. Libraries and Packages
### 8.1. Using Libraries and Packages
```vhdl
LIBRARY library_name;
USE library_name.package_name.ALL;
```

The library name is defined at build time or by the vendor.
The standard library name for own Packages is `work`.

### 8.2. Standard Libraries
* `IEEE` - standard library for VHDL.
    * `IEEE.STD_LOGIC_1164` - standard package for STD_LOGIC data type.
    * `IEEE.NUMERIC_STD` - standard package for numeric data types.
    * `IEEE.NUMERIC_BIT` - standard package for bit-wise operations.

### 8.3. Defining Libraries and Packages
```vhdl
PACKAGE package_name IS
    CONSTANT constant_name : type := value;
    TYPE type_name IS (value1, value2, ...);
    SUBTYPE subtype_name IS type_name RANGE value1 TO value2;

    FUNCTION function_name (argument1 : type; argument2 : type) RETURN type;
    PROCEDURE procedure_name (argument1 : type; argument2 : type);
END PACKAGE package_name;

PACKAGE BODY package_name IS
    FUNCTION function_name (argument1 : type; argument2 : type) RETURN type IS
    BEGIN
        -- VHDL code here
        RETURN value;
    END FUNCTION function_name;

    PROCEDURE procedure_name (argument1 : type; argument2 : type) IS
    BEGIN
        -- VHDL code here
    END PROCEDURE procedure_name;
END PACKAGE BODY package_name;
```


## 9. Functions and Procedures

Functions and Procedures are used to encapsulate reusable code.

* They allow only Variables to be used inside
* They can be used in the architecture body as well as in the process body.
* Functions and Procedures can be defined in the head of a architecture or in a package.
* Functions and Procedures can be overloaded.
* Arguments can be passed by value or by reference.

```vhdl	
procedure_name (argument1, argument2, ...);
procedure_name (argument1 => value1, argument2 => value2, ...);
```

### 9.1. Functions

*A function can not change the given parameters and only returns a value.
*Variables are initialized on every call of the function.

```vhdl
FUNCTION function_name (argument1 : type; argument2 : type) RETURN type IS
    VARIABLE variable_name : type;
BEGIN
    -- Sequential VHDL code here
    RETURN value;
END FUNCTION function_name;
```

### 9.2. Procedures

* A procedure can change the given parameters and does not return a value.
* A procedure can have a `WAIT` statement inside.
* A procedure can modify a signal in the architecture body.
* Variables are initialized on every call of the procedure.

```vhdl
PROCEDURE procedure_name (SIGNAL argument1 : port_direction port_type;
argument2 : type := default_value) IS
    VARIABLE variable_name : type;
BEGIN
    -- Sequential VHDL code here
END PROCEDURE procedure_name;
```

## 10. Configuration

A configuration is used to define the architecture of an entity or the architecture of a instance.

### 10.1. Entity Configuration (Type 1)

Defines the architecture of an entity.

```vhdl
CONFIGURATION configuration_name OF entity_using_config IS
    FOR architecture_to_use
    END FOR;
END CONFIGURATION configuration_name;
```

entity_using_config uses architecture_to_use.

### 10.2. Instance Configuration (Type 2)

Defines for instances inside an architecture which entity and architecture to use.

```vhdl
CONFIGURATION configuration_name OF entity_using_config IS
    FOR architecture_using_config
        FOR instance_name : component_name USE ENTITY library_name.entity_name(architecture_name);
    END FOR;
END CONFIGURATION configuration_name;
```

instance_name in architecture_using_config uses entity_name with architecture_name

This can also be done in the architecture head:

```vhdl
ARCHITECTURE architecture_name OF entity_name IS
    FOR instance_name : component_name USE ENTITY library_name.entity_name(architecture_name);
BEGIN
    -- VHDL code here
END ARCHITECTURE architecture_name;
```


## 11. Data Types

### 11.1. Categories of Data Types

* Scalar Types
    * Enumeration Types
    * Numeric Types
        * Integer Types
        * Real Types
        * Physical Types
* Composite Types
    * Array Types
    * Record Types
* File Types

### 11.1. Enumerated Types

```vhdl
TYPE type_name IS (value1, value2, ...);
SUBTYPE subtype_name IS type_name RANGE value1 TO value2;
```
### 11.1. Numeric Types

#### 11.1.1. Integer and Real Types
`INTEGER` (range: -2^31 to 2^31-1)
    
```vhdl
SUBTYPE NATURAL IS INTEGER RANGE 0 TO INTEGER'HIGH;
SUBTYPE POSITIVE IS INTEGER RANGE 1 TO INTEGER'HIGH;
```

`REAL` (range: -2^63 to 2^63-1)

```vhdl
SUBTYPE REAL IS INTEGER RANGE -2**63 TO 2**63-1;
```

#### 11.1.2. Physical Types

```vhdl
TYPE time IS RANGE from TO to
UNITS 
    fs;
    ps = 1000 fs;
    ns = 1000 ps;
    ...
END UNITS;
```

### 11.2. Arrays

```vhdl
TYPE fixed_range IS ARRAY (range) OF element_type;
TYPE x_by_y IS ARRAY (range1, range2) OF element_type;
TYPE variable IS ARRAY ( positive RANGE <>) OF element_type;

VARIABLE array_name : variable(1 TO 10);
VARIABLE array_name : variable(10 DOWNTO 1);
```

### 11.3. Records

```vhdl
TYPE record_name IS RECORD
    field1 : type1;
    field2 : type2;
    ...
END RECORD;

VARIABLE record_name : record_name;

record_name.field1 := value;
```


## 12. Testbenches (For Simulation Only)

A testbench is used to test the functionality of a digital circuit.

### 12.1. Testbench Structure

```vhdl
ENTITY testbench_name IS
END ENTITY testbench_name;

ARCHITECTURE testbench_name OF testbench_name IS
    -- Component Declaration
    COMPONENT dut IS
        PORT ( ... );
    END COMPONENT dut;

    -- Signal Declaration

BEGIN
    -- Component Instantiation
    dut_inst : dut PORT MAP ( ... );

    -- Stimulus Process
    PROCESS
    BEGIN
        -- VHDL code here
    END PROCESS;

    -- Check Process
    PROCESS
    BEGIN
        -- VHDL code here
    END PROCESS;

END ARCHITECTURE testbench_name;
```


#### 12.2. TEXTIO Package

The `std.textio` package is used to read and write text files.

```vhdl
USE STD.TEXTIO.ALL;
```
Open a file:

```vhdl 
FILE file_name : TEXT OPEN READ_MODE IS "file_name.txt";
```

Example Read from a file:

```vhdl
VARIABLE line : LINE;
VARIABLE sample_char : CHARACTER;
VARIABLE sample_bit : BIT;
VARIABLE sample_time : TIME;

WHILE NOT ENDFILE(file_name) LOOP
    READLINE(file_name, line);
        READ(line, sample_char);
        READ(line, sample_bit);
        READ(line, sample_time);
END LOOP;
```

Example Write to a file:

```vhdl
VARIABLE line : LINE;
VARIABLE sample_char : CHARACTER := 'A';
VARIABLE sample_bit : BIT := '1';
VARIABLE sample_time : TIME := 10 ns;

WRITELINE(file_name, line);
    WRITE(line, sample_char);
    WRITE(line, sample_bit);
    WRITE(line, sample_time);
```

Close a file:

```vhdl
CLOSE file_name;
```


## 13. Assertion Statements (For Simulation Only)

Used to check the correctness of the design during simulation.
Assertions are placed in the architecture body.

```vhdl
ASSERT condition REPORT "Error message" SEVERITY severity_level;
```

* `condition` is the condition to be checked.
* `REPORT` is the message to be displayed if the condition is false.
* `SEVERITY` is the severity level of the error message.
    * `NOTE` - informational message
    * `WARNING` - warning message
    * `ERROR` - error message
    * `FAILURE` - fatal error message


## 14. Modeling Delays (For Simulation Only)
### 14.1. Transport Delay

Transport delay is used to delay a signal by a certain amount of time.

```vhdl
x <= TRANSPORT logical_expression AFTER 10 ns;
-- delays the signal by 10 ns 
```

### 14.2. Inertial Delay

Inertial delay is used to delay a signal by a certain amount of time. Additionally, the signal must remain stable for the duration of the delay.

```vhdl
x <= logical_expression AFTER 10 ns;
-- delays the signal by 10 ns if the logical_expression is true for the entire duration.

-- Variant of Inertial Delay:

x <= REJECT reject_time INERTIAL logical_expression AFTER 10 ns;
-- delays the signal by 10 ns and rejects changes that are shorter than reject_time.
```
