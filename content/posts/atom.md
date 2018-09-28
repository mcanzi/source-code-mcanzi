---
title: "Atom: useful packages"
date: 2018-09-28
tags: ["atom", "R", "Praat"]
draft: false
---

<center>![Atom-logo](/img/atom-logo.png)</center>

__[Atom](https://atom.io)__ is a powerful open-source text editor with embedded Git control. Atom is unique because of its 7000+ plugins (packages) which are usually license-free and community-built. Atom supports syntax highlighting natively for more than 30 programming languages. The number is esponentially increased when external packages are installed. 

If you have Atom installed, you can search packages simply by opening up a new terminal window and typing __apm search keyword__

```
massimiliano@Massimilianos-MacBook-Pro ~> apm search language-r
Search Results For 'language-r' (29)
├── language-r A language description and snippets for R (67671 downloads, 83 stars)
├── atom-language-r A language description and snippets for R (2487 downloads, 8 stars)
├── language-cup A short description of your language package (621 downloads, 0 stars)
├── language-hcl Syntax support for the HashiCorp Configuration Language (9578 downloads, 25 stars)
├── language-jcm JaCaMo Project Language (114 downloads, 0 stars)
├── language-v0 Language package for Imparato internal v0 source files. (26 downloads, 0 stars)
├── language-rid RID language support in ATOM (180 downloads, 1 star)
├── language-bel BEL language support in Atom (44 downloads, 2 stars)
├── language-tjs TJS language support in Atom (230 downloads, 0 stars)
├── language-jai JAI language support for Atom (112 downloads, 0 stars)
├── language-ohm Ohm language support in Atom (198 downloads, 0 stars)
...
```
You can then install the packages you want by typing

```
apm install package-name-one package-name-two
```

In this post, I included a brief list a and description of packages I use on a daily basis. Depending on the type of work you do, some might not be relevant to you. 

- __LaTeX, R__ and __Praat__ syntax highlighting:

```
apm install language-latex language-r language-praat
```
- __Hydrogen__ can be used to run (not just edit) code in R and Python through Atom. A guide to set up R and Python kernels can be found __[here](https://jstaf.github.io/2018/03/25/atom-ide.html)__

- __[Minimap](https://atom.io/packages/minimap)__ allows the user to visualise a smaller version of the entire code on the side of the editor, allowing fast movements between blocks of code. Really useful while editing or commenting. 

```
apm install minimap
```

- __[Multi cursor](https://atom.io/packages/multi-cursor)__ is used to expand the cursor over more than one line in order to type the same thing across multiple lines. Really useful for defining arrays in some languages (e.g. Presentation's SDL/PCL). 

```
apm install multi-cursor
```
- __[Teletype](https://atom.io/packages/teletype)__ allows more people to share a workspace and edit the same code in real time. 

```
apm install teletype
```

- __[File icons](https://atom.io/packages/file-icons)__ provides custom file icons depending on file type. 

```
apm install file-icons
```

- __[Auto update packages](https://atom.io/packages/auto-update-packages)__ automatically updates all your other Atom packages.

```
apm install auto-update-packages
```

- __[Atom beaitufy](https://atom.io/packages/atom-beautify)__ auto-indents your code, although indentation is something that should always be present from the start. 

```
apm install atom-beautify
```

Some themes too..

- __[Material UI](https://atom.io/themes/atom-material-ui)__ is a dark theme based on Google's material design principles. 

```
apm install atom-material-ui
```

- __[Monokai](https://atom.io/themes/monokai)__ is a synthax theme characterised by bright colours. It works best with dark UI themes. 

```
apm install monokai
```
