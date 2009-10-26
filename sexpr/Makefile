CC = gcc
CFLAGS = -Wall -fPIC -O2 -I/opt/xensource/lib/ocaml
OCAMLC = ocamlc -g
OCAMLOPT = ocamlopt
OCAMLLEX = ocamllex
OCAMLYACC = ocamlyacc

LDFLAGS = -cclib -L./

DESTDIR ?= /
VERSION := $(shell hg parents --template "{rev}" 2>/dev/null || echo 0.0)
OCAMLOPTFLAGS = -g -dtypes

OCAMLABI := $(shell ocamlc -version)
OCAMLLIBDIR := $(shell ocamlc -where)
OCAMLDESTDIR ?= $(OCAMLLIBDIR)

OBJS = sExpr sExprLexer sExprParser sexpr
INTF = $(foreach obj, $(OBJS),$(obj).cmi)
LIBS = sexpr.cma sexpr.cmxa

PROGRAMS = sexprpp

all: $(INTF) $(LIBS)

bins: $(PROGRAMS)

libs: $(LIBS)

sexprpp: sexprpp.ml all
	ocamlfind $(OCAMLOPT) -I ../stdext stdext.cmxa sexpr.cmxa -linkpkg -o $@ $<

sexpr.cmxa: $(foreach obj,$(OBJS),$(obj).cmx)
	$(OCAMLOPT) $(OCAMLOPTFLAGS) -a -o $@ $(foreach obj,$(OBJS),$(obj).cmx)

sexpr.cma: $(foreach obj,$(OBJS),$(obj).cmo)
	$(OCAMLC) -a -o $@ $(foreach obj,$(OBJS),$(obj).cmo)

sExprLexer.ml: sExprLexer.mll
	$(OCAMLLEX) $<

sExprLexer.mli: sExprParser.cmo sExprLexer.ml
	$(OCAMLC) -i $^ > $@

sExprParser.ml: sExprParser.mly
	$(OCAMLYACC) $<

sExprParser.mli: sExprParser.ml
	$(OCAMLC) -i $< > $@

%.cmo: %.ml %.cmi
	$(OCAMLC) -I ../stdext -c -o $@ $<

%.cmi: %.mli
	$(OCAMLC) -c -o $@ $<

%.cmx: %.ml
	$(OCAMLOPT) $(OCAMLOPTFLAGS) -I ../stdext -c -o $@ $<

%.o: %.c
	$(CC) $(CFLAGS) -c -o $@ $<

META: META.in
	sed 's/@VERSION@/$(VERSION)/g' < $< > $@

.PHONY: install
install: $(LIBS) META
	ocamlfind install -destdir $(DESTDIR)$(shell ocamlfind printconf destdir) -ldconf ignore sexpr META $(INTF) $(LIBS) *.a *.cmx

.PHONY: uninstall
uninstall:
	ocamlfind remove sexpr

clean:
	rm -f *.o *.so *.a *.cmo *.cmi *.cma *.cmx *.cmxa *.annot sExprLexer.ml{,i} sExprParser.ml{,i} $(LIBS) $(PROGRAMS)