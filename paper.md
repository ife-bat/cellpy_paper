---
title: 'Cellpy – an open-source library for processing and analysis of battery testing data'

tags:
  - python
  - battery cycling
  - electrochemistry
  - data processing
  - data analysis
  - battery testing

authors:
  - firstname: Julia
    surname: Wind
    orcid: 0000-0001-6325-4727
    affiliation: '1'
  - firstname: Asbjørn
    surname: Ulvestad
    orcid: 0000-0001-9771-4808
    affiliation: '1'
  - firstname: Muhammad
    surname: Abdelhamid
    orcid: 0000-0002-1666-6398
    affiliation: '1'
  - firstname: Jan Petter
    surname: Mæhlen
    orcid: 0000-0001-7662-4707
    affiliation: '1'

affiliations:
  - name: Institute for Energy Technology, 2007 Kjeller, Norway
    index: 1

date: 10 October 2023

bibliography: paper.bib
---

# Summary

Recent years have witnessed an exponential increase in battery research, driven by the need to develop efficient and sustainable energy storage systems. One of the main tools in battery research are battery cycling experiments, providing insights into performance lifetime and quality of the battery. Due to the large variety of battery testing equipment and the resulting multitude of different and often proprietary data formats, combined with the large number of parameters involved, managing and processing battery testing data has often been a difficult and tedious task.

The Python library `cellpy` assists in solving these problems by

1. providing the tools to read different data formats,
2. converting those into one common data format that also includes relevant battery-specific meta-data, and
3. providing a data structure equipped with a set of methods that helps the user to easily perform simple and in-depth analysis of both single data sets and collections of data sets.

# Statement of need

Typically, a battery-testing data set consists of simple time series data with voltage, current and capacity. Most cell cycling instruments provide these data in a format which is in principle straightforward to plot and adjust. <!--- The most common way of structuring battery data is to split the time series data into so-called battery cycles(charge & discharge), where each cycle consists of several steps. \autoref{fig:1} illustrates a typical data set containing one full cycle consisting of four steps (“s1” to “s4”). Analysing individual cycles and steps allows to gain a detailed understanding of the battery cell. ![Illustration of battery cell test data.\label{fig:1}](./Figures/Cell-test-data.jpg) -->
However, data from different equipment are measured and handled in different ways and stored in different, often proprietary formats. Consequently, a direct and meaningful comparison of several cells tested under a variety of conditions can be challenging and requires more advanced data handling methodologies.
Several open-source libraries focus on battery test-data extraction. However, most of them are dedicated to specific battery testing equipment: notably galvani [@galvani] parses the proprietary [BioLogic](https://www.biologic.net/) format, neware_reader [@neware_reader] parsing several versions of [Neware](https://newarebattery.com/) data, and galv (formerly Galvanalyser) [@galv] supporting [Maccor](http://www.maccor.com/), [Ivium](https://www.ivium.com/) and BioLogic formats. BEEP (Battery Evaluation and Early Prediction [@beep]) provides a structured interphase for collecting and processing battery test data and exports to text format.

`cellpy` provides powerful and versatile tools for the simple and efficient handling of battery testing data originating from different battery cell testers, all the way from data collection to data analysis and visualisation, ensuring consistancy, accuracy and comparability. `cellpy` can directly parse the data from most common commercial battery testing units and formats ([Arbin](http://www.arbin.com/), Maccor, [PEC](https://www.peccorp.com/battery-testing-solutions/), Neware, BioLogic), in addition to offering full flexibility by allowing the user to provide other file format specifications (in YAML format). The data is converted into and saved in a common format, accommodating not only data from diverse testers, but also thoughtfully embeding battery-specific metadata (e.g., type of cell, type of chemistry, electrode properties, etc.). This makes subsequent data handling considerably easier and proves invaluable in interpreting and comparing results across tests and conditions. In addition to translating data to a common format, `cellpy` has a range of utilities for studying and analysing the data. These include methods for the extraction of key characteristics from tests, cell comparison, plotting and statistical analysis, as well as advanced tools such as incremental-capacity analysis (dQ/dV), OCV relaxation analysis and batch processing of results from many battery test [@2019and,@2020ulv,@2023hul,@2023spi].

The `cellpy` library provides a valuable toolset and has been in frequent use for both, every-day and advanced tasks in battery research. The ability to effortlessly import and process the data through a simple but highly flexible API allows for quick and simple comparison of different cells in everyday battery research. At the same time, `cellpy` functions as an excellent starting point for researchers leaning towards advanced analysis: `cellpy` can automatically convert data with different units, summarize and perform statistical evaluations all the way down to the individual cycle and step level, while giving the user fine-grained control of the behaviour through setting parameters or directly by using a more advanced, deeper API. This eases further use of the data e.g., as features for machine learning algorithm, and promotes reproducibility and traceability throughout the entire process.

# Implementation and architecture

`cellpy` is implemented in python and can be used as either a library within python scripts, or as a stand-alone application for analysing battery cell test data. Internally, `cellpy` utilises the rich ecosystem of scientific tools available for python. In particular, `cellpy` uses pandas DataFrames as the “storage containers” for the collected data within the `cellpy` Data object. This offers full flexibility and makes it easy for the user to apply advanced methods, analyses or transformations of and to the data in addition to the features implemented in `cellpy`.

The core of `cellpy` is the CellpyCell object (\autoref{fig:2}). This object contains the important and central methods required to read and process the data. Additional classes and methods are used by CellpyCell, such as different readers for importing data in different formats.

![The core object in `cellpy` is the CellpyCell object. This object contains the data (and meta data) as well as methods for working with the data, and provides the appropriate interface and coordination of the resources needed, such as readers for different data formats, or exporters for saving data.\label{fig:2}](Figures/CellpyCell.jpg)

Each CellpyCell object contains a Data object storing both, the battery test data as well as the corresponding meta data (\autoref{fig:3}). The `cellpy`-file format (usually stored in hdf5 format) contains all the data contained in the Data object together with additional relevant meta data such as information about the file version.

![Each CellpyCell object contains its own Data object. While in memory, this Data object stores the raw data as a pandas DataFrame. In addition, the two pandas DataFrames summary and steps, contains data vs. cycle or step respectively. Examples of summary data are gravimetric charge capacity, coulombic efficiency, and charging-rates vs. cycle number. Examples of steps data are maximum current, mean voltage, and type-of-step (categorical) vs. step number.\label{fig:3}](Figures/CellpyData.jpg)

The `cellpy` library also consists of a rich set of utilities (\autoref{fig:4}) that can be used for further processing the data, both individually and in batch routines.

![The `cellpy` library contains multiple utilities that assists in data analysis. A utility can work on (A) - a single CellpyCell object, such as for example the ICA (Incremental Capacity Analysis) utility that assists in creating for example dQ/dV graphs, or (B) – a set of CellpyCell objects such as the Batch utility that helps the user in automating and comparing results from many data sets.\label{fig:4}](Figures/Cellpy-Utils.jpg)

# Acknowledgements

The development of `cellpy` was supported by the Research Council of Norway through the ENERGIX Projects No. 280985 ("KPN Silicon on the Road"), No. 324077 ("KSP MoreIsLess"),  No. 320760 ("KSP SecondLife"), No. 326866 ("KSP LongLife"), No. 344317 ("KSP CellMap), and FME-MoZEES, co-sponsored by the Research Council of Norway (project No. 257653) and 40 partners from research, industry, and the public sector. The development was also supported through the European Union (EU)-funded SIMBA project, in the frame of HORIZON 2020 (GA No. 963542).

The authors are thankful to the numerous inputs and comments from our colleagues and collaborators, and in particular Dr. Preben J.S. Vie and Dr. Martin Kirkengen.

# References