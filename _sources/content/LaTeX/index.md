# LaTeX

This is a collection of useful tips and tricks for using LaTeX. This is not a guide to use LaTeX - if you don't know how to use it, I recommend you use [Overleaf](https://www.overleaf.com) to get started (it is cloud based so you don't have to install anything). Find an example project and use that to begin with.


## Macros

The equivalent of functions in programming, you can use macros to make repetitive actions easier.


### Dotfill

Originally found [here](https://tex.stackexchange.com/questions/85335/how-to-change-dot-spacing-in-dotfill), this is useful to change the spacing to some custom value. I found this useful when making my own list of symbols and abbreviations, because in the document I was using the lists of figures and tables used a non-default spacing.

```
\documentclass{article}

\makeatletter % Change category code of @ from default
\newcommand \Dotfill {\leavevmode \leaders \hb@xt@ .8em{\hss.\hss}\hfill \kern \z@} % \leaders to align dots across different lines
\makeatother % Return category code of @ to default

\begin{document}
\Dotfill
\end{document}
```


### Easy view names

My PhD thesis was all about ultrasonic imaging using the multi-view total focusing method. This is well beyond the scope of this guide, but the point is that "Views" have names, which are written as strings of letters which are joined by subscripted letters. Originally I was approaching this by writing ``L$_\text{S}$L--L$_\text{S}$L``. While it does result in the correct formatting, it is slow to write and is difficult to read in the code editor. I put together the following solution to make it nicer.

```
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
