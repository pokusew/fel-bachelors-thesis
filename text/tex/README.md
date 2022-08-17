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
│   ├── 3-tracing.tex
│   ├── 4-f1tenth.tex
│   ├── 5-migration.tex
│   ├── 6-evaluation.tex
│   ├── 7-conclusion.tex
│   ├── appendices.tex
│   ├── glossary.tex
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
cp index.pdf ../Using_ROS_2_for_High-Speed_Maneuvering_in_Autonomous_Driving.pdf
```
Or you can the following shortcut:
```bash
make copy
```
