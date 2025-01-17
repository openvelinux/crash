#
# Copyright Petr Tesarik <petr@tesarici.cz>
#
# Makefile for LIBEPPIC unit tests
#
# This program is free software; you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation; either version 2 of the License, or
# (at your option) any later version.
#
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with this program.  If not, see <http://www.gnu.org/licenses/>.
#

TESTS = \
	tst-constants \
	tst-dynarray \
	tst-enum \
	tst-enum-eppic \
	tst-exists \
	tst-hello \
	tst-hello-bytes \
	tst-loops \
	tst-op \
	tst-struct \
	tst-struct-eppic \
	tst-switch \
	tst-union \
	tst-union-eppic

CONFIG = testenv.conf

CCLD = $(CC)
LDFLAGS = -L..
LIBS = -leppic -lncurses

BINARIES = runeppic
OUTPUTS = $(TESTS:=.out) \
	tst-bad-constants.out

CFLAGS += -I.. -g -O2
ifeq ($(TARGET), PPC64)
	CFLAGS += -m64
endif
ifeq ($(TARGET), ARM)
	CFLAGS += -m32
endif
ifeq ($(TARGET), X86)
  CFLAGS += -m32
endif
ifeq ($(TARGET), MIPS)
       CFLAGS += -m32
endif

all:
install:

runeppic.o: runeppic.c ../eppic_api.h
runeppic: runeppic.o ../libeppic.a
	$(CCLD) $(CFLAGS) $(LDFLAGS) -o $@ $^ $(LIBS)

mkop: mkop.o
	$(CCLD) $(CFLAGS) -o $@ $^

tst-op.c: mkop
	./mkop > $@

tst-op.o: tst-op.c
	$(CC) $(CFLAGS) -Wno-overflow -Wno-shift-overflow -c -o $@ $^
mkop-expect: tst-op.o
	$(CCLD) $(CFLAGS) -o $@ $^
tst-op.expect: mkop-expect
	./mkop-expect > $@
tst-op.out: tst-op.expect

%.out: %.c runeppic
	./runeppic --config $(CONFIG) $< >$@

%.out: %.sh runeppic
	./$< >$@

check:
	rm -f test-suite.log $(OUTPUTS)
	$(MAKE) test-suite.log

test-suite.log:
	-$(MAKE) -k $(OUTPUTS)
	@$(SHELL) ./check-output.sh $(OUTPUTS) | tee $@

clean:
	-/bin/rm -f *.o *.output *.log $(BINARIES)

.PHONY: all install check clean
.DELETE_ON_ERROR:
