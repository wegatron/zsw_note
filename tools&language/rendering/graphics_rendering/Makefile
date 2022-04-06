min:
	xelatex -shell-escape graphics_rendering.tex
	evince graphics_rendering.pdf

all: clean
	xelatex -shell-escape graphics_rendering.tex
	bibtex graphics_rendering.aux
	xelatex -shell-escape graphics_rendering.tex
	xelatex -shell-escape graphics_rendering.tex
	evince graphics_rendering.pdf

clean:
	rm -rf *.log *.out *.pdf *.blg *.bbl *.aux
