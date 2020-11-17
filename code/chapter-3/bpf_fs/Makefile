CLANG = clang

PINEXECABLE = map-pin
READEXECABLE = map-read

PINBPFCODE = map_pinning_save
READBPFCODE = map_pinning_fetch

BPFTOOLS = /kernel-src/samples/bpf
BPFLOADER = $(BPFTOOLS)/bpf_load.c

INCLUDE += -I/kernel-src/samples/bpf
INCLUDE += -I/kernel-src/tools/lib

CCINCLUDE += -I/kernel-src/tools/testing/selftests/bpf
CCINCLUDE += -I/kernel-src/tools/lib/bpf
CCINCLUDE += ${INCLUDE}

LOADINCLUDE += -I/kernel-src/tools/include
LOADINCLUDE += -I/kernel-src/tools/perf
LOADINCLUDE += ${INCLUDE}
LIBRARY_PATH = -L/usr/local/lib64
BPFSO = -lbpf

# Setting -DHAVE_ATTR_TEST=0 for the kernel containing below patch:
# 06f84d1989b7 perf tools: Make usage of test_attr__* optional for perf-sys.h
#
# The patch was included in Linus's tree starting v5.5-rc1, but was also included
# in stable kernel branch linux-5.4.y. So it's hard to determine whether a kernel
# is affected based on the kernel version alone:
# - for a v5.4 kernel from Linus's tree, no;
# - for a v5.4 kernel from the stable tree (used by many distros), yes.
#
# So let's look at the actual kernel source code to decide.
#
# See more context at:
# https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=06f84d1989b7e58d56fa2e448664585749d41221
# https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=fce9501aec6bdda45ef3a5e365a5e0de7de7fe2d
CFLAGS += $(shell grep -q "define HAVE_ATTR_TEST 1" /kernel-src/tools/perf/perf-sys.h \
                  && echo "-DHAVE_ATTR_TEST=0")

.PHONY: clean bpfload build

clean:
	rm -f *.o *.so map-*

build: ${BPFCODE.c}
	$(CLANG) $(CFLAGS) $(LIBRARY_PATH) $(BPFSO) $(BPFLOADER) $(PINBPFCODE:=.c) $(CCINCLUDE) $(LOADINCLUDE) -lelf -o ${PINEXECABLE}
	$(CLANG) $(CFLAGS) $(BPFSO) $(READBPFCODE:=.c) $(CCINCLUDE) -o ${READEXECABLE}

.DEFAULT_GOAL := build
