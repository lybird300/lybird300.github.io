---
layout: post
title: "COSI Overview"
date: 2015-04-02
---

The Cosi program (Schaffner et al., 2005) implements a coalescent model similar to the MS program (Hudson, 1990) but
allowing for complex demographic histories and variable recombination rates. Both MS and Cosi implement the standard coalescent approach that simulates genealogical events backward in time. Simulated events typically include the coalescence of two sequences into a single ancestral lineage, recombination within a sequence, or migration between populations. Since all these events are typically rare, coalescent simulators assume that they never occur simultaneously and assume many generations pass between consecutive events. Time between events is explicitly modeled and used to skip over generations with no genealogical events of interest. The algorithm proceeds until all sequences coalesce to their most recent common ancestor and the resulting genealogy is used to place mutation events along the various sequences. 

Assumptions/Theoretical foundations

Capabilities/Limiations
It cannot generate chromosomal length regions

Performances
cosi is suitable for short genomic segments (<2-3Mb) but become very slow for larger regions (> 100Mb)

The bestfit model

This model has been calibrated using genome-wide human population data for different populations. It uses the large-scale variation in recombination rates obtained from the deCODE genetic map along with fine-scale variation in recombination rates. The default parameters of this model are listed in Table 1 of the paper describing the method (Schaffner et al., 2005). The program generates data for four populations, each with its own demographic scenario.

