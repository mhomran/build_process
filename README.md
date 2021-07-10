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

