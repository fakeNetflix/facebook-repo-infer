# Copyright (c) Facebook, Inc. and its affiliates.
#
# This source code is licensed under the MIT license found in the
# LICENSE file in the root directory of this source tree.

# E2E test involving the interesting_paths_filter function

TESTS_DIR = ../..

MODIFIED_FILES_FILE = changed_files.txt
SOURCES = $(wildcard src_after/*.java src_before/*.java)
INFER_OPTIONS = --changed-files-index $(MODIFIED_FILES_FILE) \
  --incremental-analysis \
  --debug-level-analysis 1

include $(TESTS_DIR)/differential.make

.PHONY: compare_cg
compare_cg: $(PREVIOUS_REPORT)
	$(QUIET)$(call check_no_diff,\
	  reverse_analysis_callgraph.dot,\
	  reverse_analysis_callgraph.dot.test)

replace_cg: $(PREVIOUS_REPORT)
	$(COPY) reverse_analysis_callgraph.dot.test reverse_analysis_callgraph.dot

test: compare_cg
replace: replace_cg

$(CURRENT_REPORT): $(INFER_CLANG_DEPS) $(SOURCES)
	$(QUIET)$(REMOVE_DIR) src
	$(QUIET)$(MKDIR_P) src
	$(QUIET)$(COPY) src_after/* src/
	$(QUIET)$(INFER_BIN) -o $(CURRENT_DIR) --cost-only --enable-issue-type INFINITE_EXECUTION_TIME -- javac src/*.java

$(PREVIOUS_REPORT): $(INFER_CLANG_DEPS) $(SOURCES) $(CURRENT_REPORT)
	$(QUIET)$(REMOVE_DIR) src
	$(QUIET)$(MKDIR_P) src
	$(QUIET)$(COPY) src_before/* src/
	$(QUIET)$(REMOVE_DIR) $(PREVIOUS_DIR)
	$(QUIET)$(COPY) -r $(CURRENT_DIR) $(PREVIOUS_DIR)
	$(QUIET)$(REMOVE) $@
	$(QUIET)$(INFER_BIN) -o $(PREVIOUS_DIR) $(INFER_OPTIONS) --cost-only --enable-issue-type INFINITE_EXECUTION_TIME -- javac  src/*.java
	$(QUIET)$(COPY) $(PREVIOUS_DIR)/reverse_analysis_callgraph.dot \
	        reverse_analysis_callgraph.dot.test
