# matlab2tikz

[matlab2tikz](https://github.com/matlab2tikz/matlab2tikz) is an add-on for Matlab which produces .tex files for inclusion in Latex documents. When creating figures and saving them as .jpg or .png files, it is burdensome to determine the fontsize for axes, labels, etc within the figure to ensure they are readable in the final document. Additionally, I have never managed to get all font sizes the same size as figures with different widths will lead to slight changes in the font size of the final image.

A solution to this problem is matlab2tikz. It is a function in Matlab which draws out the figures as a `tikzpicture` object, allowing Latex to take care of the text processing. As it was a bit of a faff to get set up, the process it took me to get it working is documented here.

(Note - there is a [python version for matplotlib](https://pypi.org/project/matplotlib2tikz/). I intend to move to this eventually but haven't yet)

## Installation

Install [matlab2tikz](https://www.mathworks.com/matlabcentral/fileexchange/22022-matlab2tikz-matlab2tikz) from the Matlab FileExchange.

Plot your figures in Matlab. Note that I experienced a few issues when using certain features - the workarounds I came up with are listed on the next page.

Call `matlab2tikz` either in the script or in the command window. Save the file with some filename as `<filename>.tex` - this take the currently open figure and save a `.tex` file which can be included in your Latex document.

Move your `.tex` file and any accompanying files (e.g. sometimes a `.png` file may be generated as well) to your Latex project directory (or upload them to Overleaf, etc.).

Within the preamble of the Latex document, add in the following lines:

```
\usepackage{pgfplots}
\pgfplotsset{compat=newest}
\usetikzlibrary{plotmarks}
\usetikzlibrary{arrows.meta}
\usepgfplotslibrary{patchplots}
\usepackage{grffile}
```

In the body at the desired location, use the command `\input{<filename>.tex` to include the figure.
