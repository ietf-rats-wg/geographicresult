# The worked examples of the document.
#
# Each result is written once, in CBOR diagnostic notation (.edn).
# Its encoding (.hex) is derived from it.  The document includes both,
# and the annotated listings (.pretty) of its figure.
#
#   make          derive the .hex files, then check
#   make check    check only
#
# The checks:
#   - each result validates against the CDDL of the document;
#   - each .hex file is the encoding of its .edn file;
#   - each .pretty file annotates the encoding of its .edn file;
#   - each map in rejected/ fails to validate.
#
# Tools: diag2cbor.rb and pretty2cbor.rb (gem install cbor-diag),
# cddl (gem install cddl), xxd.

RESULTS   := $(basename $(wildcard *.edn))
ANNOTATED := $(basename $(wildcard *.pretty))
REJECTED  := $(basename $(notdir $(wildcard rejected/*.edn)))

# The CDDL of the document, and what it uses and does not define.
CDDL := ../cddl/geographic-results.cddl ../cddl/imports.cddl

# 31 bytes to a line: 62 characters, which fit the text rendering.
HEX := xxd -p -c 31

$(foreach tool,diag2cbor.rb pretty2cbor.rb cddl xxd,\
  $(if $(shell command -v $(tool)),,$(error $(tool) not found)))

.PHONY: all check clean FORCE
all: $(RESULTS:=.hex)
	@$(MAKE) --no-print-directory check

# Derived every time: the files are in git, so their dates say nothing.
%.hex: %.edn FORCE
	@bytes=$$(diag2cbor.rb $< | $(HEX)); \
	if [ -n "$$bytes" ]; \
	then echo "$$bytes" >$@; echo "made    $@"; \
	else echo "FAILED  $< is not CBOR diagnostic notation"; exit 1; fi

# The worked examples are geographic-result-claims maps.
check.cddl: $(CDDL)
	@{ echo 'start = geographic-result-claims'; cat $(CDDL); } >$@

check: $(RESULTS:%=valid-%) $(RESULTS:%=hex-%) \
       $(ANNOTATED:%=pretty-%) $(REJECTED:%=rejected-%)

valid-%: %.edn check.cddl
	@if out=$$(diag2cbor.rb $< | cddl check.cddl validate - 2>&1); \
	then echo "ok      $< validates against the CDDL"; \
	else echo "$$out"; echo "FAILED  $< does not validate"; exit 1; fi

hex-%: %.edn
	@bytes=$$(diag2cbor.rb $< | $(HEX)); \
	if [ -n "$$bytes" ] && [ "$$bytes" = "$$(cat $*.hex)" ]; \
	then echo "ok      $*.hex is the encoding of $<"; \
	else echo "FAILED  $*.hex is not the encoding of $<: run make"; \
	     exit 1; fi

pretty-%: %.edn %.pretty
	@bytes=$$(diag2cbor.rb $< | xxd -p); \
	if [ -n "$$bytes" ] && \
	   [ "$$bytes" = "$$(pretty2cbor.rb $*.pretty | xxd -p)" ]; \
	then echo "ok      $*.pretty annotates the encoding of $<"; \
	else echo "FAILED  $*.pretty does not annotate the encoding of $<"; \
	     exit 1; fi

rejected-%: rejected/%.edn check.cddl
	@if ! diag2cbor.rb $< >/dev/null; \
	then echo "FAILED  $< is not CBOR diagnostic notation"; exit 1; fi; \
	if diag2cbor.rb $< | cddl check.cddl validate - >/dev/null 2>&1; \
	then echo "FAILED  $< validates, and it must not"; exit 1; \
	else echo "ok      $< is rejected"; fi

clean:
	rm -f check.cddl
