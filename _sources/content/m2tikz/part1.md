# Workarounds

There are a few issues which may arise when using matlab2tikz for the first time.

- Filenames included as an argument (e.g. `matlab2tikz(filename)`) must be character arrays rather than strings. Either explicitly convert to `char` or make sure to use ' rather than ".
- Latex may throw errors if plots include high-resolution colormaps (e.g. `imagesc` or 3D surfaces) - files will be too big if they are more than a few kB. Use the `cleanfigure()` function included with the add-on to reduce content. Check the arguments if the wrong kind of features are being removed - e.g. use `cleanfigure('pruneText', false)` if annotations are being removed that shouldn't be.
- If your Latex document has anything other than an a4 layout with normal margins, the size of the final image may be wrong. Use argument pair `'width', '0.8\linewidth'` in the call to `matlab2tikz` to set it to 80% of the linewidth - determined by Latex on compilation.
- matlab2tikz will try to make all text look as it does in Matlab. If you have math-rendered content, make sure it looks right in Matlab first (e.g. change the interpreter to `tex`) rather than trying to edit the `.tex` file to make it look right.
- If you are not saving your figures in the same directory as your `main.tex`, any sub-includes (e.g. if matlab2tikz makes a `.png` file) must point to the right place. You can either edit the `.tex` file directly, or include the argument pair `'relativeDataPath', path` where `path` is a character array pointing to the correct location of your `.png` relative to `main.tex`.

There are solutions to more common problems and pitfalls on matlab2tikz's own [GitHub page](https://github.com/matlab2tikz/matlab2tikz) - see there if you're having other problems.

I've compiled my workarounds to all of this in the [following function](https://github.com/mgchandler/SoftwareTutorials/tree/main/content/m2tikz/m2tikz.m):
```matlab
function m2tikz(filename, varargin)
% Wrapper for matlab2tikz which solves the problems I have had. Leaves all
% text in the file, sets the figure size to 80% of linewidth, and includes
% the option for a relative path to figure location.
%
% Syntax:
%   m2tikz(filename)
%   m2tikz(filename, path)
%   m2tikz(___, Name, Value)
%
% Parameters:
%   filename : char or string
%   path: char or string
%   Name-Value Arguments:
%       Passed directly into matlab2tikz - see their documentation.

if ~isempty(findobj('type', 'figure'))
    filename = char(filename);
    cleanfigure('pruneText', false)
    % Do the default process
    if nargin == 1
        matlab2tikz(filename, 'width', '0.8\linewidth', 'mathmode', false)
    % More options provided on top, unpack using defaults.
    elseif nargin > 1
        % Make a structure which contains the default arguments. Then
        % overwrite them later if they are specified.
        args = struct('width', '0.8\linewidth', 'mathmode', false);
        % Path may or may not be listed with its argument handle name -
        % check if it is or not.
        if mod(nargin, 2) == 0
            % Path must be listed without argument name
            args.relativeDataPath = char(varargin{1});
            start = 1;
        else
            % Else path must be listed with argument name
            start = 0;
        end
        for arg = 1:(nargin-start-1)/2
            args.(varargin{1 + start + 2*(arg-1)}) = char(varargin{1 + start + 2*(arg-1) + 1});
        end
        unpack = {};
        names = fieldnames(args);
        vals = struct2cell(args);
        for arg = 1:size(names, 1)
            unpack = {unpack{:}, names{arg}, vals{arg}};
        end
        matlab2tikz(filename, unpack{:})
    end
else
    return
end
end
```
