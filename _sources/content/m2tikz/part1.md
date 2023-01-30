# Workarounds

There are a few issues which may arise when using matlab2tikz for the first time.

- Filenames included as an argument (e.g. `matlab2tikz(filename)`) must be character arrays rather than strings. Either explicitly convert to `char` or make sure to use ' rather than ".
- Latex may throw errors if plots include high-resolution colormaps (e.g. `imagesc` or 3D surfaces) - files will be too big if they are more than a few kB. Use the `cleanfigure()` function included with the add-on to reduce content. Check the arguments if the wrong kind of features are being removed - e.g. use `cleanfigure('pruneText', false)` if annotations are being removed that shouldn't be.
- If your Latex document has anything other than an a4 layout with normal margins, the size of the final image may be wrong. Use argument pair `'width', '0.8\linewidth'` in the call to `matlab2tikz` to set it to 80% of the linewidth - determined by Latex on compilation.
- matlab2tikz will try to make all text look as it does in Matlab. If you have math-rendered content, make sure it looks right in Matlab first (e.g. change the interpreter to `tex`) rather than trying to edit the `.tex` file to make it look right.
- If you are not saving your figures in the same directory as your `main.tex`, any sub-includes (e.g. if matlab2tikz makes a `.png` file) must point to the right place. You can either edit the `.tex` file directly, or include the argument pair `'relativeDataPath', path` where `path` is a character array pointing to the correct location of your `.png` relative to `main.tex`.

I've compiled my workarounds to all of this in the following function:
```
function m2tikz(filename, varargin)
% Wrapper for matlab2tikz which solves the problems I have had. Leaves all
% text in the file, sets the figure size to 80% of linewidth, and includes
% the option for a relative path to figure location.

if ~isempty(findobj('type', 'figure'))
    filename = char(filename);
    cleanfigure('pruneText', false)
    if nargin == 1
        matlab2tikz(filename, 'width', '0.8\linewidth', 'mathmode', false)
    else
        path = char(varargin{1});
        matlab2tikz(filename, 'width', '0.8\linewidth', 'mathmode', false, 'relativeDataPath', path)
    end
else
    return
end
end
```