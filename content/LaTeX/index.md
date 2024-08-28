# LaTeX

This is a collection of useful tips and tricks for using LaTeX. This is not a guide to use LaTeX -- if you don't know how to use it, I recommend you use [Overleaf](https://www.overleaf.com) to get started (it is cloud based so you don't have to install anything). Find an example project and use that to begin with.


## Macros

The equivalent of functions in programming, you can use macros to make repetitive actions easier.


### Dotfill

Originally found [here](https://tex.stackexchange.com/questions/85335/how-to-change-dot-spacing-in-dotfill), this is useful to change the spacing to some custom value. I found this useful when making my own list of symbols and abbreviations, because in the document I was using the lists of figures and tables used a non-default spacing.

```tex
\documentclass{article}

\makeatletter % Change category code of @ from default
\newcommand \Dotfill {\leavevmode \leaders \hb@xt@ .8em{\hss.\hss}\hfill \kern \z@} % \leaders to align dots across different lines
\makeatother % Return category code of @ to default

\begin{document}
\Dotfill
\end{document}
```


### Easy view names

My PhD thesis was all about ultrasonic imaging using the multi-view total focusing method. This is well beyond the scope of this guide, but the point is that "Views" have names, which are written as strings of letters which are joined by subscripted letters. Originally I was approaching this by writing ``L$_\text{S}$L--L$_\text{S}$L``. While it does result in the correct formatting, it is slow to write and is difficult to read in the code editor. I put together the following solution based on [this thread](https://tex.stackexchange.com/questions/359189/looping-over-strings) to make it nicer.

```tex
\documentclass{article}
\usepackage{amsmath} % For \ensuremath{}

\newcommand{\View}[2]{\textAsMath{#1}--\textAsMath{#2}} % Join transmit and receive paths
\def\fmtloop#1{%
  \ifx\relax#1 % If at end of the string
    \else
      \ifcat_#1 % If subscript
        #1
      \else\ifcat^#1 % If superscript
        #1
      \else
        \text{#1} % Otherwise probably text
      \fi\fi\expandafter\fmtloop% Next iteration
  \fi}
\def\textAsMath#1{\ensuremath{\fmtloop#1\relax}}% Recursive loop through string

\begin{document}
\View{L_SL}{L_SL};
\View{L_{Backwall}L}{L_{Backwall}L}
\end{document}
```

If anybody reading this knows a better way to do this kind of thing, please get in touch! While this works for the intended purpose, I don't know how widely applicable it would be beyond this. For example, currently I'm chaining two "if" statements which would make extending it more difficult to manage -- an "or" statement would be good for the current implementation, or else some sort of "switch" statement or pattern matching would be good for generalisability.


### Unit conversion

When making figures, I usually use Python to save pdf files from `matplotlib`. While an installed version of LaTeX is required to compile mathtext, it ensures that any text: has a font consistent with the final document; is searchable; and has consistent font size if the figure size is set correctly. While the first two are freebies, if the figure size is larger than the page width in the LaTeX document then the figure will overflow the page. To get the width of the page, this can be extracted from the compiler by including `\the\linewidth` in the document, however the unit this returns it in pts. I use the following macro which I found in [this thread](https://tex.stackexchange.com/questions/8260/what-are-the-various-units-ex-em-in-pt-bp-dd-pc-expressed-in-mm) to convert it to inches, which is what the `figsize` parameter of the `Figure` class in `matplotlib.pyplot` uses. Note that this may not be the final size -- I frequently add colorbars, text, etc. outside the bounds of the original figure. If your figure does this, then the final image will be larger than the `figsize` parameter, so if you don't decrease the value from LaTeX then your figure may overflow anyway!

```tex
\documentclass{article}

\makeatletter
\def\convertto#1#2{\strip@pt\dimexpr #2*65536/\number\dimexpr 1#1}
\makeatother

\begin{document}
\the\linewidth % Returns value in points.

\convertto{in}{\linewidth} % Returns value in inches.
\end{document}
```

I also use the following structure in my Python code when making images. This is included because I copied and pasted this a lot while writing my thesis, so I think it's useful to have it in one place rather than across a lot of different files.

```python
import matplotlib as mpl
from matplotlib.gridspec import GridSpec
import matplotlib.pyplot as plt
import os

if __name__ == '__main__':
    mpl.use('module://matplotlib_inline.backend_inline')
    mpl.rcParams.update(mpl.rcParamsDefault) # Reset from any previous run.
    mpl.rc('font', family='serif')
    mpl.rc('text', usetex=True)              # Requires local install of LaTeX. I have used MikTeX on Windows, and TexLive on Linux.
    mpl.rc('text.latex', preamble='\n'.join([
        r'\usepackage{amsmath}',
        r'\usepackage{fouriernc}',           # Nice fonts - must be installed in local version of LaTeX.
        r'\usepackage[T1]{fontenc}',         # Same as above.
    ]))

    save_pdf = False                         # Switch for easy debugging before actually saving.

    if save_pdf:
        mpl.rc('pgf', rcfonts=False)
        mpl.rc('pgf', preamble='\n'.join([   # Also need to set pgf params when saving.
            r'\usepackage{url}',
            r'\usepackage{unicode_math}',
            r'\usepackage{amsmath}',
            r'\usepackage{fouriernc}',
            r'\usepackage[T1]{fontenc}',
        ]))

    gs  = Gridspec(1, 1)
    fig = plt.figure(figsize=(6, 4), dpi=200)
    ax  = fig.add_subplot(gs[0, 0])
    ax.plot(...)
    ax.set_xlabel(r'$\Delta x$ (mm)')        # Can include compilable mathtext!

    if save_pdf:
        plt.savefig(
            os.path.join(".", "figure1.pdf"),
            pad_inches=0,
            bbox_inches="tight",
        )
    else:
        plt.show()
    plt.close('all')
```
