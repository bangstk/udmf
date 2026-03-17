# Universal Doom Map Format
Originally designed in 2008 by James "Quasar" Haley, et al, UDMF is a format specification for storing level data for idTech1 engine games, and derivatives.

This format supersedes existing formats by using a flexible and extensible text-based representation of geometry information, a lump called **`TEXTMAP`**, instead of the older structured binary formats of the original Doom-engine games.

In order to distinguish between different feature sets, UDMF maps come with a top-level `namespace` entry in each `TEXTMAP` entry used to determine which features are supported or not.

## Base specification
The latest version of the base specification is **1.1**. And defines only the most basic features needed for the original vanilla games of Doom, Heretic, Hexen and Strife, in their respective namespaces.

For the complete history, see the following:
* [UDMF version 1.1](./base/udmf11.txt)
* [UDMF version 1.0](./base/udmf10.txt)

## Extended namespaces
In order to add new features, it is imperative for engines to define new namespaces with their own specification.

Some of thase 'engine exclusive' namespaces can be found on [`extensions/`](./extensions/).

## Binary formats
To compare against the legacy formats, see the [map formats document](./formats/map.md).

For a developer-friendly descriptive section on the various BSP tree formats, see [here](./formats/bsp.md).

# Legalese
Copyright (c) 2008-2009 James Haley.

Permission is granted to copy, distribute and/or modify this document
under the terms of the GNU Free Documentation License, Version 1.2
or any later version published by the Free Software Foundation;
with no Invariant Sections, no Front-Cover Texts, and no Back-Cover Texts.
