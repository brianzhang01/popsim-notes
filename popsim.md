# PopSim 2019

## Table of Contents

## Welcome by Jerome

## Andrew Kern, State of PopSim (1 hour 15 mins)
Intro
- Pool et al. 2012, Population genomic samples from African Drosophila melanogaster. WGS, haploid, well-phased
- stairwayplot, SMC++, MSMC, different methods give different results

How do we know our tools work well? (Overview)
- As a community, popgen lacks benchmarking. What do we need?
- Answers so far
- Standardized simulation platform (msprime / SLiM)
- Standardized simulation scenarios
- Benchmarked analysis
- Community effort (buy-in)
- We need to generate enthusiasm for standardization

PopSim Project
- stdpopsim
- Catalog of 3 species: homo sapiens, drosophila, arabidopsis
- Genetic maps: Decode 2010, HapMap 2
- Population models
    + Published models, (6 for humans, 3 for drosophila, 1 for arabidopsis), e.g. three population out-of-Africa
    + Sanity check models (constant size)
- Reproducible analysis workflows
- Richard Durbin comment: MSMC is out-of-date, should use MSMC2
    + Andrew: sticking to published methods. There's also SMCSMC by Chris Cole
    + Richard: MSMC2 been on bioRxiv for 6 months
    + Andrew: everyone guilty of making their own method look good
- Adam Siepel comment: maybe include a set of guidelines for publication, we as reviewers can refer to them
- Richard Durbin comment: standardized input format, so people don't need to wrangle the data to do different evaluation
    + Andrew: great idea. Also missing data, masked data
    + Jerome: that's 90% of what's in stdpopsim

Results
- Constant population size
- Human genome (Gutenkunst et al. 2009, Ragsdale and Gravel 2019): YRI, CEU, CHB. Andrew claims that YRI is harder than the others
    + Jeffrey Spence comment: that's a visual effect, YRI only varies by a factor of 10^2, the others vary by a lot more. Andrew: yes
    + Andrew: methods seem to be doing well.
- Masking low-recombination regions seem to be crucial to analysis? Chromosome 20 (?) low-recombining, 1/3 of the whole chromosome. 1000 Genomes does not include data for these regions. Repeats, transposable elements, recombination rate
    + Richard Durbin disagrees, should think of them as unresequenced regions
    + Andrew: but there's a very strong correlation
    + Adam: we should think of these as areas we can't map?
- Drosophila
    + Sheehan and Song (2015) model. Methods do better or worse.
    + Audience comment: won't the metrics you use have a huge impact on what methods people develop? People will overfit to the metrics.
- Arabidopsis
    + Durvasula et al. (2017)
    + Very bad job
- Overall
    + Results vary by genome depending on size and shape of the genome, shape of the map, population-level parameters
    + This is the first look we've had of several methods, standardized comparison
    + Audience comment: x-axis might want generations instead of years ago

What next?
- What are the benchmarks that people use?
- Against what would you want to calculate MSE?
- Arielle Gladstein comment: what about multiparameter inference? Does getting everything matter, or just something like population size?
    - Andrew: don't want to alienate any approaches / choices at the beginning
- We're using the right mutation rate, generation time. Not misspecified.
- Konrad Lohse comment: what about identifiability of models? Could you get a model pretty different from truth that predicts the data equally well?
    + Simon Myers: good question, somewhat unidentifiable, at least from site frequency spectrum
- Richard Durbin comment: actually the thing you can measure is the coalescence rate. We invert that into other parameters. There's an argument that we should be more explicit about the rate itself instead of N (population size)
    + Andrew: in current simulations, Ne really is coalescence rate
- Peter Ralph: what else would be useful to do?
- Jerome: simulation paradigms, how to engage others?
    + Noah Dukler: did stuff with models, people can submit models using a PR, one of us then reproduces from your paper and does a QC. Note: many times we've learned things from contacting the authors that weren't clear from the paper.
    + Andrew: lot of good QC discussions that ensue over GitHub
- Arielle question: what does it mean for two models to be equal / how is that checked?
    + Jerome: we have a pretty hashed out definition in msprime, compares underlying objects within double precision
    + Noah: sometimes you can have two independent events whose order is switched, that needs to be verified by eye
    + Andrew: we decided not to go with summaries. Instead going with parameters.
- Audience question: these standard models use parameters from papers, would you be able to change them? Shouldn’t we make parameter changes transparent to users?
    + Andrew: uses Python right now so you should be able to edit that
- Audience comment: it could also be a great tool to know what's the best model in my case
    + Jerome: don't want to bloat the codebase, keeping it focused to simulations
- Andrew: we should anticipate the need to change assemblies. Currently only assembly 37

Initial manuscript
- Whitepaper, small publication that introduces the framework / resources. Open if anyone wants to be more involved
- Adam: we're avoiding it to be a benchmarking paper, more about introducing a resource. Forward-looking, discussion here today can be captured in the paper

Jerome demoed the CLI
- `tskit info temp.trees`
- `tskit provenanc`

## Richard Durbin, Model misspecification (30 mins)
Intro
- Saw that the whole program was about ARGs, but that's not representative of PopSim. This is something different.
- So far we have simulated from a model that is in the space being fit by the methods.
- But real data doesn't correspond to this.
- What happens when we simulate from a model outside the space of the method?

## Adam Siepel, What's next for PopSim (45 mins)

## Simon Myers, What's so great about ARGs? (45 mins)

## Jerome Kelleher, ARG formats and software (45 mins)

## Yan Wong, ARG metrics (45 mins)

## Peter Ralph, Benchmark simulations for ARG inference (45 mins)

## General discussion

## Summary and agreed actions
