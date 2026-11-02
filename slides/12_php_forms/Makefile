file = forms.tex
pdflatex = lualatex
publicado = ../../src/slides/aula_12_php_forms.pdf

all: pdf

pdf:
	latexmk -pdflatex=${pdflatex} -pdf -shell-escape ${file}
	cp forms.pdf ${publicado}

clean: dist
	latexmk -pdflatex=${pdflatex} -C ${file}

dist:
	rm -rf *.auxlock *.nav *.bbl *.snm *.vrb *.aux *.blg *.fdb_latexmk *.fls *.log *.out *.toc *-figure* _minted*
