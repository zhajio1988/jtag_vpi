SOURCE_DIR=../..
BENCH_DIR=$(SOURCE_DIR)/bench/verilog
INCLUDE_DIR=$(BENCH_DIR)/include

# Set V=1 when calling make to enable verbose output
# mainly for debugging purposes.
ifeq ($(V), 1)
Q=
QUIET=
else
Q ?=@
QUIET=-quiet
endif

IVERILOG_VPI_SRC = $(SOURCE_DIR)/jtag_common.c $(SOURCE_DIR)/jtag_vpi.c
#IVERILOG_VPI_SRC = $(SOURCE_DIR)/jtag_vpi.c
ADV_DEBUG_SRC = $(shell ls $(BENCH_DIR)/adv_debugsys/*.v)

IVERILOG_SRC = $(SOURCE_DIR)/jtag_vpi.v \
               $(BENCH_DIR)/jtag_tap/jtag_tap.v \
               $(BENCH_DIR)/jtag_vpi_tb.v \
               $(BENCH_DIR)/jtag_soc.v \
               $(BENCH_DIR)/ram/ram_wb_b3.v \
               $(ADV_DEBUG_SRC)

all: build

build:
	@echo '##### Building ######'
	$(Q)gcc -otest_client $(BENCH_DIR)/../test_client.c
	$(Q)vcs -full64 -debug_acc+all +vcs+fsdbon -timescale=1ns/1ps -lca -kdb -sverilog -l build.log +incdir+$(INCLUDE_DIR) $(IVERILOG_VPI_SRC)  $(IVERILOG_SRC) -P jtag_vpi.tab +vpi -top jtag_vpi_tb

sim: build
	@echo '##### Start the simulation ######'
	./simv  +fsdb+all=on -l sim.log +fsdbfile+test.fsdb +jtag_vpi_enable=1 &
	@sleep 1
	@echo '##### Running the test client ######'
	$(Q)./test_client
	@sleep 1
clean:
	@rm -rf *.log simv.daidir simv csrc DVEfiles verdiLog novas.rc novas.conf *.fsdb *.log ucli.key vc_hdrs.h AN.DB work test_client  verdi_config_file
