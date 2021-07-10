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
### default goal
If you don't specify a target to make. The chosen target will be the target of the first rule.
<code>make</code>(not specifying a target).
### phony targets
A phony target is one that is not really the name of a file; rather it is just a name for a recipe to be executed when you make an explicit request.

<code style="white-space: pre-wrap">
.PHONY: clean
clean:
        rm *.o temp
</code>

### recipes
The recipe of a rule consists of one or more shell command lines to be executed, one at a time, in the order they appear.
