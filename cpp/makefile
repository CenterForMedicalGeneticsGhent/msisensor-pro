CXX ?= g++

MAKEFILE_DIR := $(dir $(abspath $(lastword $(MAKEFILE_LIST))))
HTSLIB := $(abspath $(MAKEFILE_DIR)/../vendor/htslib-1.24)
HTSLIB_LIB := $(HTSLIB)/libhts.a

# Static HTSlib so msisensor-pro does not need vendor libhts at runtime.
-include $(HTSLIB)/htslib_static.mk

CXXFLAGS += -std=c++11 -O2 -fopenmp -pthread
INCLUDES += -I$(HTSLIB)
LIBS += -pthread $(HTSLIB_static_LIBS)
LDFLAGS += $(HTSLIB_static_LDFLAGS)

SOURCE = cmds scan distribution refseq polyscan param utilities homo window bamreader sample chi somatic
OBJS = $(patsubst %,%.o,$(SOURCE))
DEPS = $(OBJS:.o=.d)

.PHONY: all htslib clean distclean

all: msisensor-pro

htslib: $(HTSLIB_LIB)

$(HTSLIB_LIB):
	$(MAKE) -C $(HTSLIB) lib-static

%.o: %.cpp | $(HTSLIB_LIB)
	$(CXX) $(CXXFLAGS) $(INCLUDES) -MMD -MP -c $< -o $@

msisensor-pro: $(OBJS) $(HTSLIB_LIB)
	$(CXX) $(CXXFLAGS) $^ $(LDFLAGS) $(LIBS) -o $@

clean:
	rm -f $(OBJS) $(DEPS) msisensor-pro

distclean: clean
	$(MAKE) -C $(HTSLIB) clean

-include $(DEPS)
