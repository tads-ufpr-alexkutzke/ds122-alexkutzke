file = php.tex
pdflatex = lualatex
publicado = ../../src/slides/aula_11_php.pdf

all: pdf

pdf:
	latexmk -pdflatex=${pdflatex} -pdf -shell-escape ${file}
	cp php.pdf ${publicado}

clean: dist
	latexmk -pdflatex=${pdflatex} -C ${file}

dist:
	rm -rf *.auxlock *.nav *.bbl *.snm *.vrb *.aux *.blg *.fdb_latexmk *.fls *.log *.out *.toc *-figure* _minted*
