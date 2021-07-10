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

