#=======================================================================
# UCB VLSI FLOW: Makefile for riscv-tests
#-----------------------------------------------------------------------
# Yunsup Lee (yunsup@cs.berkeley.edu)
#

default: all

#--------------------------------------------------------------------
# Sources
#--------------------------------------------------------------------


asm_tests =  $(wildcard *.S)

#--------------------------------------------------------------------
# Build rules
#--------------------------------------------------------------------

RISCV_GCC = riscv32-unknown-elf-gcc
RISCV_GCC_OPTS = -nostdlib -nostartfiles -Wa,-march=rv32im
#RISCV_OBJDUMP = riscv32-unknown-elf-objdump --disassemble-all --section=.text --section=.data --section=.bss
RISCV_OBJDUMP = riscv32-unknown-elf-objdump -S
RISCV_SIM = spike --extension=hwacha

#------------------------------------------------------------
# Build assembly tests

asm_tests_bin  = $(patsubst %.S, %, $(asm_tests))
asm_tests_dump = $(addsuffix .dump, $(asm_tests_bin))
asm_tests_sig  = $(addsuffix .sig,  $(asm_tests_bin))
asm_tests_hex  = $(addsuffix .hex,  $(asm_tests_bin))

$(asm_tests_dump): %.dump: %
	$(RISCV_OBJDUMP) $< > $@

$(asm_tests_bin): %: %.S
	$(RISCV_GCC) $(RISCV_GCC_OPTS) -I../env/p -T../env/p/link.ld $< -o $@

$(asm_tests_hex): %.hex: %
	elf2hex 8 2097152  $< > $@

$(asm_tests_sig): %.sig: %
	$(RISCV_SIM) +signature=$@ $<

new:
	cd ..; make gen

run: $(asm_tests_sig)
	echo; perl -ne 'print "  [$$1] $$ARGV \t$$2\n" if /\*{3}(.{8})\*{3}(.*)/' \
	       $(asm_tests_sig); echo;

junk += $(asm_tests_bin) $(asm_tests_dump) $(asm_tests_sig) $(asm_tests_hex)

#------------------------------------------------------------
# Default

all: $(asm_tests_dump)

#------------------------------------------------------------
# Clean up

clean:
	rm -rf $(junk)

clean-all: clean
	rm -rf test*.stats test*.hex test*.out test*.dump test test_1* test_pseg_* schad* failedtests/*
