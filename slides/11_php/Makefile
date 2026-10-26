file = php.tex
pdflatex = lualatex
publicado = ../../src/slides/aula_11_php.pdf
figura = fluxo_backend_php.pdf

all: pdf

pdf: ${figura}
	latexmk -pdflatex=${pdflatex} -pdf -shell-escape ${file}
	cp php.pdf ${publicado}

${figura}: ../../src/images/php/fluxo_backend_php.svg
	rsvg-convert -f pdf -o $@ $<

clean: dist
	latexmk -pdflatex=${pdflatex} -C ${file}

dist:
	rm -rf *.auxlock *.nav *.bbl *.snm *.vrb *.aux *.blg *.fdb_latexmk *.fls *.log *.out *.toc *-figure* _minted*
