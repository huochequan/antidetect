# Compiler flags:
#  -q   no banner
#  -wx  max warnings
#  -0   generate 8086 code
#  -ms  force small model (default)
#  -s   remove runtime stack checks (required)
#  -os  optimize for size
#  -zu  assume that SS != DS (required)
#  -zl  do not emit default library information
#
!ifdef __UNIX__
Q=\"
!else
Q="
!endif
CFLAGS = -q -3 -wx -zu -zl -s -oas -d1 -ms
DEFS   = -DVBOX -DVBE -DVBOX_VERSION_STRING=$(Q)0.9.90$(Q) -DVBOX_VENDOR=$(Q)Nvidia_Corporation$(Q) -DVBOX_PRODUCT=$(Q)Antidetect 2018 Pro$(Q)
#DEFS   = -DVBOX -DVBOX_VERSION_STRING=$(Q)0.9$(Q) -DVBOX_VENDOR=$(Q)NVIDIA$(Q) -DVBOX_PRODUCT=$(Q)VideoBios$(Q)

AFLAGS = -q -0 -wx

INCLS  = -I$(Q)../../../../../include$(Q)

.c.obj : .autodepend
	wcc -fo=.obj $(CFLAGS) $(DEFS) $(INCLS) $<

.asm.obj : .autodepend
	wasm -fo=.obj $(AFLAGS) $(DEFS) $<

OBJS = 	vgarom.obj vberom.obj vgabios.obj vbe.obj

vgabios.rom : vgabios.bin
	biossums $< $@

vgabios.bin : $(OBJS) $(__MAKEFILES__)
	wlink name $@ system dos debug all option quiet &
	 option nofarcalls, map, verbose, statics, symfile &
	 output raw offset=0xC0000 order &
	 clname CODE segment VGAROM segaddr=0xC000 &
	 segment _TEXT segaddr=0xC000 offset=0xA00 &
	 segment VBE32 segaddr=0xC000 offset=0x4600 &
	 clname DATA segment _DATA segaddr=0xC000 offset=0x4800 &
	 file { $(OBJS) } disable 1014, 1023, 2120

clean : .symbolic
	@rm -f *.obj *.err
	@rm -f vgabios.bin vgabios.rom vgabios.map vgabios.sym
