# Types
Types are hierarchical, where a type can only contain lower level types. 

## Primitives
### Classical primitives
TODO

### Inferred variable `var`
Any new variable in a method must be inferred.

### References `ref`
References are used as a shorthand when the programmer does not need to explicitly request or assign 

### Registers
The `register` type provides a method to describe memory mapped functionality. They are described by bits and collections of bits and are initialized with a memory address or when contained in a `peripial`. They are `volatile` by default and each bit or collecton of bits can be configured with access type. Are allways the size of the sysyems `word` size.

#### Example
```
// Declaration
register reg_type 
{
    rw  0-5u: value,
    w      6: writable_flag,
    r      9: readable_flag,
    rw     7: rw_flag,
}

// Instantiation
ref reg = reg_type <- 0xdeadbeef;

// Usage
val a = reg.readable_flag; // Forces a request of the register
ref b = reg.rw_flag; // Creates a reference for the scope of the method, it is not a pointer
reg.value++; 
reg.rw_flag = a; // Sets rw_flag to a
b = a; // Also sets rw_flag to a
reg.writable_flag = readable_flag;
reg = 0; // Writes 0 to all writable parts 
```

### Bitfields `bitfield`
Used to alias bits

```
// Declaration
bitfield(u32) bits
{
    0-5u: value,
       6: a,
       9: b,
       7: c,
}

// Instantiation
var a = bitfield bits <- 0;
var b = bitfield bits <- {value = 3, c = 1};

// Usage
val c = a.value;
ref d = b.c;
a = 30; // Writes to whole bitfield
```

## Complex types
### Peripherals `peripheral`
A collection of register to allow a logical way to reference multiple related registers

#### Example
```
// Declaration
peripheral my_peripheral
{
    some_register,
    some_other_register,
    register_explicip_offset = 0x30,
}

// Instantiation
ref per = my_peripheral <- 0xcafef00d;

// Usage
per.some_register.some_flag = 0;
```

### Memory `memory`
Describes real memory, similar to a packed struct in other languages. Is not considered volatile. Can only contain primitives or other memories.

#### Example
```
// Declaration
memory my_memory
{
     u32 a,
     u8 b,
     bitfield(u32) c
     {
         0-2s: a,
         5: c
     }[4]
     existing_bitfield d,
}

// Instantiation
ref a = my_memory <- 0xcafef00d;
val b = my_memory <- 0;

// Usage
TODO
```

### Struct `struct`
Similar to `memory` in syntex, but is not an accual construct but rather a logical collection of that is easier to manage and forward, can contain `memory` but `memory` cannot contain a `struct`

## Conceptual types
* Contains the logic
* Hieratical, types can only directly access suberviant types.

### Methods `method`
* "unsafe"
* Serializable
* Semaphorically locked

### Functions `function`
* Deterministic "functional" method
* Reentrant-safe
* Paralizable

### Module `module`
* Single threaded and long lived
* Similar to classes
* No inhertitance
* Polymorfism via an `interface`
* All variables are private
* Is not a continius memory region
* Only one method can be called from outside the module at a time

### Handler `handler`
* Short
* Must be in a `module`
* Made for interrupts, callbacks, etc.
* Cannot call other modules interfaces, instead it is provided with references to an `atomic` variable that will atomically be synced module->handler if module is not semaphorically locked and handler->module on the next entry to any of the modules methods.

### Interface `interface`
* Polymorphism

### `application`
* Top level platform type
* Contains scheduling, garbage collection, memory allocation, IPC, etc.
* Declares the program

### `system`
* Optional
* Used to describe multi application systems, can distributed over networks

### Example
```c
system my_system 
{

};
```
