# INSTALLATION (ARCH LINUX, SUPPORTING LATEX AND PDF IMPORT/EXPORT):
	
    $ pacman -S pandoc texlive-core texlive-bin texlive-science zathura zathura-pdf-poppler


# CAN CONVERT FROM AND TO:

- markdown
- html
- docx
- odt (open/libre office)
- epub
- html slideshows (Slidy, reveal.js, Slideous, S5, or DZSlides.)
- etc: http://pandoc.org/index.html


# GENERAL SYNTAX:

## Converting from markdown to html

    $ pandoc test1.md -f markdown -t html -s -o test1.html

The filename test1.md tells pandoc which file to convert. The -s option says to
create a “standalone” file, with a header and footer, not just a fragment. And
the -o test1.html says to put the output in the file test1.html. Note that we
could have omitted -f markdown and -t html, since the default is to convert
from markdown to HTML, but it doesn’t hurt to include them.

## Converting from latex to a nicelly formatted pdf

    $ pandoc spot_workflow.project.md -f markdown -t latex -o spot_workflow.project.pdf -V geometry:"top=3cm, bottom=1.5cm, left=2cm, right=2cm"  

    ("-V" specifies the document margins)