## build process
<img src="imgs/build_process.png">

## Cross tool chain important binaries
### arm-none-eabi-gcc
It's a compiler, linker and assembler.

#### common flags:
<ul>
  <li>
    <code>-o</code> to specifiy the name of the output file. If it's not specified, the default name is a.out
  </li>
  <li>
    <code>-c</code> compile or assemble but don't link
  </li>
  <li>
    <code>-march=name</code> to specifiy the ARM architecture.
  </li>
  <li>
    <code>-mcpu=name</code> to specify the processor name. In this case it's not nessary to specify the ARM architecure.
  </li>
  <li>
    <code>-mthump</code>ARM or Thump state. (modern processors architecture is thump ISA, so choose -mthump).
  </li>
  <li>
    <code>-S</code> generate assembly file only and don't assemble.
  </li>
  <li>
    <code>-std=</code>determine the C standard.
  </li>
  <li>
    <code>-O</code>Determine the optimization level.
  </li>
</ul>

### arm-none-eabi-ld
It's a linker.
### arm-none-eabi-as
It's an assembler.
### arm-none-eabi-objcopy
It's a format converter to change from executable format to another executable format.
### arm-none-eabi-objdump
To dump an object file e.g. main.o
#### common flags:
<ul>
  <li>
    <code>-h</code>(headers)display the contents of the section headers.
  </li>
  <li>
    <code>-d</code>(disassemble)display the assembler contents of the executable sections.
    ```
    arm-none-eabi-objdump -d main.o > main_log
    ```
  </li>
  <li>
    <code>-s</code>(full-content)display the full contents of all sections requested. 
  </li>
</ul>
### arm-none-eabi-readelf
### arm-none-eabi-nm

## Makefile
### rule
A rule appears in the makefile and says when and how to remake certain files, called the rule’s <i>targets</i> (most often only one per rule). It lists the other files that are the <i>prerequisites</i> of the target, and the <i>recipe</i> to use to create or update the target.

```
target: dependency

main.o: main.c
```
### default goal
If you don't specify a target to make. The chosen target will be the target of the first rule.
<code>make</code>(not specifying a target).

### phony targets
A phony target is one that is not really the name of a file; rather it is just a name for a recipe to be executed when you make an explicit request.

```
.PHONY: clean
clean:
        rm *.o temp
```

### recipes
The recipe of a rule consists of one or more shell command lines to be executed, one at a time, in the order they appear.
```
main.o: main.c
        $(CC) $(CFLAGS) main.c -o main.o (recipe)
```

### variables 
A variable is a name defined in a makefile to represent a string of text, called the variable’s value. It's case senesitive and conventionally upper case.

To dereference a variable use <code>$(VAR)</code>

### automatic variables
Suppose you are writing a pattern rule to compile a ‘.c’ file into a ‘.o’ file: how do you write the ‘cc’ command so that it operates on the right source file name? You cannot write the name in the recipe, because the name is different each time the implicit rule is applied.

What you do is use a special feature of make, the automatic variables. These variables have values computed afresh for each rule that is executed, based on the target and prerequisites of the rule. In this example, you would use ‘$@’ for the object file name and ‘$<’ for the source file name.

It's used only in recipes.
<ul>
  <li>
    <code>$@</code>: The file name of the target of the rule.
  </li>
  <li>
    <code>$^</code>: The names of all the prerequisites, with spaces between them.
  </li>
  <li>
    <code>$<</code>: The name of the first prerequisite.
  </li>
</ul>

### how patterns match
A target pattern is composed of a ‘%’ between a prefix and a suffix, either or both of which may be empty. The pattern matches a file name only if the file name starts with the prefix and ends with the suffix, without overlap. The text between the prefix and the suffix is called the stem. Thus, when the pattern ‘%.o’ matches the file name test.o, the stem is ‘test’. The pattern rule prerequisites are turned into actual file names by substituting the stem for the character ‘%’. Thus, if in the same example one of the prerequisites is written as ‘%.c’, it expands to ‘test.c’.
```
%.o: %.c
        $(CC) -c $(CFLAGS) $(CPPFLAGS) $< -o $@
```

### Analyzing .o filies (Relocatable object files)
A file format standard describes a way of organizing various elements (data, read-only data, code, uninitialized data, etc.) of a program in different sections.
#### ELF (executable and linkable format)
ELF is a standard file format for object files and executable files when you use GCC.

#### Other formats:
<ul>
  <li>
    (Common Object File Format) COFF: introduced by Unix SystemV.
  </li>
  <li>
    (Arm Image Format) AIF: introduced by ARM.
  </li>
  <li>
    SRECORD: introduced by Motorola.
  </li>
</ul>

main.o doesn't include any <strong>absolute addresses</strong> for data and code. If you disassembled the content using objdump tool, you'll find that every section starts at 0 address in memory, that's why it's called relocatable (they will be relocated later by the linker using the linker script).

#### sections:
##### .text
It holds the code or the instructions of the program.
##### .data
It holds the initialized data.
##### .bss
It holds the uninitialized data.
##### .rodata
It holds the read-only data of the program.
#### User defined sections
You can add your own sections.

## startup file
### Importance of startup file
- The startup file is responsible for setting up the right environment for the main user code to run.
- Code written in startup file runs before main(). So you can say startup file calls main().
- Some part of the startup code file is the target (Processor) dependent.
- Startup code takes care of vector table placement in code memory as required by the ARM cortex Mx processor.
- Startup code may also take care of stack reinitialization. 
- Startup code is responsible of .data, .bss section initialization in main memory.

### startup file components
- A vector table for your microcontroller. Vector tables are MCU specific.
- .data, .bss sections initialization in SRAM.
- main function call.

### language of the startup file
- C
- assembly

### 1-vector table
An array of integers that should be located in the right position in memory and not in .data section. This can be achieved with compiler attribute <code>section ("section-name")</code>.
```C
uint32 vector[] __attribute__((section (".isr_vector"))) = 
{MSP, vector_table_address1, vector_table_address2...};
```

#### compiler attributes</a>
<a style="text-decoration: none" href="https://gcc.gnu.org/onlinedocs/gcc/Variable-Attributes.html">GCC reference </a>
The keyword ```__attribute__``` allows you to specify special properties of variables, function parameters, or structure, union, and, in C++, class members. This __attribute__ keyword is followed by an attribute specification enclosed in double parentheses. Some attributes are currently defined generically for variables. Other attributes are defined for variables on particular target systems. Other attributes are available for functions (see Function Attributes), labels (see Label Attributes), enumerators (see Enumerator Attributes), statements (see Statement Attributes), and for types (see Type Attributes). Other front ends might define more attributes (see Extensions to the C++ Language).

##### variable attributes
<ul>
  <li>
      <code>section ("section-name")</code> Normally, the compiler places the objects it generates in sections like data and bss. Sometimes, however, you need additional sections, or you need certain particular variables to appear in special sections, for example to map to special hardware. The section attribute specifies that a variable (or function) lives in a particular section.
  </li>
</ul>

##### functions attributes
<ul>
  <li>
    <code>Alias</code> It allows to give alias name for a function.
    <br>
    <code>
    void NMI_Handler(void) __attribute__((alias("Default_Handler")));
    </code>
  </li>
  <li>
    <code>weak</code> It allows to override already defined weak function(dummy), with the same function name.
    <br>
    <code>
    void NMI_Handler(void) __attribute__((weak, alias("Default_Handler")));
    </code>
  </li>
</ul>

### 2-.data, .bss initialization
To get the .data section from flash, you should know the boundaries of this section in flash. The linker script passes this boundaries to the startup file.
