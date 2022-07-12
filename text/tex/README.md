# TeX source of the Thesis

This Thesis uses [CTUstyle3](https://github.com/olsak/CTUstyle3) template. Refer to its documentation for more
info.

## Sources

The TeX sources of the Thesis consists of the following files:
```text
. text directory
├── tex - TeX sources and the CTUstyle3 template
│   ├── index.tex - the Thesis Root
│   ├── 1-introduction.tex
│   ├── 2-ros.tex
│   ├── 3-f1tenth.tex
│   ├── 4-follow-the-gap.tex
│   ├── 5-jetson.tex
│   ├── 6-conclusion.tex
│   ├── appendices.tex
│   └── ... CTUstyle3 template files
└── images - images and diagrams
    └── ...

```

The images and diagrams are in the upper level [images](../images) directory.


## Building

Requires [OpTeX](https://petr.olsak.net/optex/). On macOS, one can use [MacTeX](https://www.tug.org/mactex/)
which is a TeX distribution (TeX Live) that contains OpTeX.

Run the following command to build the `index.pdf` (which is the whole Thesis):
```bash
optex index.tex
```
Or you can the following shortcut:
```bash
make pdf
```

It may be useful to build and then open using `open`:
```bash
optex index.tex && open index.pdf
```
Or you can the following shortcut:
```bash
make open
```

To copy the resulting output to a final place:
```bash
cp index.pdf ../Endler-Martin-ROS-2-Bachelors-Thesis-Latest.pdf
```
Or you can the following shortcut:
```bash
make copy
```
