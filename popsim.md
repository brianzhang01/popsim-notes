# PopSim 2019

## <a name="top"></a> Table of Contents
* [Welcome by Jerome](#welcome)
* [Andrew Kern, State of PopSim](#kern)
* [Richard Durbin, Model misspecification](#durbin)
* [Adam Siepel, What's next for PopSim](#siepel)
* [Afternoon introduction by Jerome](#afternoon)
* [Simon Myers, What's so great about ARGs?](#myers)
* [Jerome Kelleher, ARG formats and software](#kelleher)
* [Yan Wong, ARG metrics](#wong)
* [Peter Ralph, Benchmark simulations for ARG inference](#ralph)
* [General discussion](#discussion)
* [Summary and agreed actions](#summary)

## <a name="welcome"></a> Welcome by Jerome

## <a name="kern"></a> Andrew Kern, State of PopSim (1 hour 15 mins)
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
- `tskit provenance`

## <a name="durbin"></a> Richard Durbin, Model misspecification (30 mins)
Intro
- Saw that the whole program was about ARGs, but that's not representative of PopSim. This is something different.
- So far we have simulated from a model that is in the space being fit by the methods.
- But real data doesn't correspond to this.
- What happens when we simulate from a model outside the space of the method?
    - "True structure" is spatial -- model in admixture tree
    - Gene conversion is rampant -- models ignore it

Spatial structure
- Ancient DNA across Asia, de Barros Damgaard P et al Science 2018
- momi2, Kamm et al. JASA 2019
- Plots show a subpopulation tree (tree bifurcating model), but in actuality, there's admixture everywhere
- There's two types of bootstrap (relates to identifiability discussion earlier).
    + Block bootstrap: non-parametric, randomly resample data
    + Parametric bootstrap: take your final fit model, and simulate from that model. How well do you reconstruct the same thing? Shows identifiability of the model.
    + Adam Siepel comment: you could still have a non-identifiable model that looks identifiable
    + Simon Myers comment: it's an ill-posed statistical problem, if you use the site frequency spectrum. There's a very similar site frequency spectrum you can get from a very different demographic model.
    + Richard response: regularization process may encourage the true result
- Discussion
    - Adam: the model could fit the data badly, and you could still give low variance when using the non-parametric bootstrap
    - Audience member (DF, Bath?): plot the residuals
        - Richard: F4 stats are one type of diagnostic
        - Simon: linkage disequilibrium
        - Andrew: F4 is just a transformation of the joint site frequency spectrum, which is the original input to momi
        - Guy Sella: suppose a demon gave you the true ARG, what would you consider a good demographic model for that?
    - Konrad: never understood what we can learn from the block bootstrap
        - Richard: I actually think they're different things. Let's say you had two datasets from the same situation, you'd get different results. Bootstrap gives you an estimate of that variance
        - Konrad: but there's no recombination
        - Audience member: doesn't tell you anything about bias
        - Konrad: but the data is all from one tree
        - Richard: want to push us to think about simulating from a richer model space. What sort of bias do we see? Do we know how well we're fitting?
    - Simon: what bits of the genome do we need? Should the population tree be the same for different parts of the genome?
    - Andrew: can investigate how much variation we'll see in different parts of the tree under a neutral model

Gene conversion
- Slide has Palamara et al. 2015, Narasimhan et al. 2017
- A lot of our methods, especially SMC-based, take a pair of genomes and compare them, looking at differences. Every mutation means if you go back to common ancestor, there's been a mutation. If you look at the differences that actually occur, ...
    - It could be a very recent mutation, but we know for SNPs that's quite rare
    - It could be an error that's occurred, but we assume not (joke about our SNP calling software being good)
    - Third possibility is gene conversion! We know this absolutely happens. Ratio of 10:1 or more than (more gene conversions) 10:1
- We did this from consanguinity (close family)
- All our models that use this data -- half the things we observe are not due to mutations!
- So why do our things work?
    + I'd like msprime to simulate gene conversion
    + This doesn't affect site frequency spectrum-based methods, but it does affect LD structure
    + Andrew: ... unless there's bias
    + Richard: selection around sites. Gene conversion is more common around recombination hotspots. I've always thought we should mask out the kilobase around recombination hotspots.
    + Pier: yes, it is correlated to recombination
- Richard: PSMC overestimates recent population size in the presence of gene conversion (but not sure about the exact details, masters project)
    + Can come discuss with me later
- Python version of msprime can already simulate gene conversion, coming to C, will also come to stdpopsim
    + Neutral gene conversion, not biased
    + SLiM can also do gene conversion

Summary: model misspecification is an issue, and we ought to explore it as a field

## <a name="siepel"></a> Adam Siepel, What's next for PopSim (45 mins)
Intro
- This will be unstructured, no slides
- Which areas are most important, what resources do we want to add
- Two categories we might want to talk about
- Logistics
    + Initial paper
        * How to promote use
        * Further dev benchmarking
        * Challenges (DREAM)
        * Standardization of input
    + Andrew: How do we get funding? In the absence of funding, how do we get devlopers?
        * Jerome: developers is what we need
    + Georgia Tsambos: we can convert from SLiM, but it sounds like your model checking process depends on things that happen in msprime
        * Andrew, Andy: we should be agnostic to simulator?
    + Arielle Goldstein propose that developers have to submit a Docker image
    + Jedidiah Carlson: how do we plan on publishing these extensions? Another big paper, or multiple small individual contributions?
- Extensions
    + Selection
        - Richard: Varying Ne along the genome is a proxy for this, since selection changes the coalescence rates
            - Adam: but we have to model it based on what we know about selection, because it interacts with mutation
        - Adam: two motivating questions: how does it impact demography inference, but also getting at the distribution of fitness effects (DFE)
            + Positive selection scans, characterizing damanging mutations
        - Demographic, DFE (positive selection scans, deleterious mutation), complex traits
    + Spatial
        - Sohini Ramachandran: Spatial processes of selection. A group like this might want to come up with an ontology of models to pursue. You can imagine local geographic selection. Could also lead to a natural way to think about residuals. Once you get multiple populations, there's the question about whether DFE is the same for different populations. Sex-biased bottlenecks. Maybe we should standardize this first, and then think about selection later.
    + Mutation models (rate variation, context dependency)
        * Jedidiah Carlson: I will volunteer myself and possibly the rest of the [Kelley] Harris lab to do mutation model stuff
    + Gene conversion
    + Overlapping generations
        * Audience question: what do you mean by this? Seems like it doesn't make much of a difference. Low-priority.
    + Sex bias
    + Ancient samples
    + Data issues
        * Low-coverage data, missing data, phasing errors
    + Jerome: there's developer bandwidth, and we'll need to prioritize. If you volunteer to help with testing, it'll happen much quicker.
    + Jerome: if you have SLiM code, we can write a backend for it (in stdpopsim?) in 20 minutes
    + Georgia has started to write some code that translates a tskit model to SLiM
    + Taking a vote: selection (25), spatial (12), mutation models (12), gene conversion (12), overlapping generations (0), sex bias (0), ancient samples (6)

## <a name="afternoon"></a> Afternoon introduction by Jerome
PopSim for Trees
- Two new methods: tsinfer and Relate, plus Harris 2019 commentary, in Nature Genetics
- We need to think, what infrastucture and methodology do we need to push the field forward?
- We want more methods for inferring ARGs
- We also need to think carefully about evaluation
- Community standards: 1. Efficient interchange format and library support. 2. A set of comparison metrics capturing "important" features of ARGs and standard comparison infrastrcuture. 3. Standard benchmark simulations to test against.
- Let's cooperate on infrastrucutre and compete on methods.

## <a name="myers"></a> Simon Myers, What's so great about ARGs? (45 mins)
Overview
- Some examples of questions we might answer using ARGs
- Some of my own ideas, hope we get some other ideas

ARG inference
- margarita, ARGweaver
- tsinfer, Relate, >10,000 samples
- Human data, ongoing work in mouse
- tsinfer, Relate, and full ARG are slightly different representations. Sometimes we need the recombination events (Relate doesn't give these).

What do trees and ARGS provide?
- ARGweaver: balancing, background selection via TMRCA variation, sweeps
- Motivation: ARGs can be a "one stop shop", the answers you get are also self-consistent
- The real ARG vs. the one we infer
- Downstream applications vs. ones integrated into ARG / tree building (e.g. demography, population sizes)
- The latter will be harder to solve, e.g. Relate does not have multiple populations, admixture
- Data compression (tsinfer and tskit)
- Data cleaning
    + Adam Siepel: phasing. We're looking to phase as part of the ARG-building
- Robustness of results
- Identify mutations occuring more than once

Potential discussion areas
- Population genetic inference
- Ancient DNA
- Phasing, and imputation
    + Richard Durbin: in our original work with Margarita, used it for imputation
    + Guy Sella: Post-imputation, does the ARG give you anything besides the LD matrix?
    + Richard: I don't think imputation's that good, we care about rare variants, and it's not good there
    + Yan Wong: the ARG could help with imputing ancient sequences, doesn't assume copying from someone current
- The extension of methods to e.g. bacterial genomes that recombine differently
- Inter-species ARGs (with models unclear)
- Duplications and rearranging
- Selfing and partially selfing

Terminology (proposed by Richard)
- Stochastic process = coalescent with recombination
- Data structure = ARG (all recombination events), or tree sequence (observable recombination events)
- Cactus graph from another group = ARG with structural information
- Audience question: does the ARG have to have branch lengths?
    + Not necessarily, but tsinfer is working on adding them
    + Which questions do we need branch lengths for?

What are ARGs or trees NOT good for?
- Questions that depend on the data itself directly and/or which are non-evolutionary
- De novo mutations, mosaicism
- GWAS discovery?
    + Adam Siepel: helps you figure out which tests to do
    + Andrew Kern: additional power
- Fine-mapping (?), beyond the question of annotating variants

ARG-based statistics
- We can play a game, try to compute statistics from the ARG / trees (Field et al. (Science 2016))
- We found that tree-based statistics can give extra power

Examples
- Population size through time, Relate fits it well, 200 samples
    + Audience question: is it perfect data?
    + Leo Spiedel: Perfectly phased data, ends up overestimating recent population sizes
- Multiple population inference (mice)
    - Build genealogies together for two (or more) populations, randomly take pairs and measure their coalescence rate
    - We have good methods for estimating population sizes, but I don't think we have good methods for these more complicated analyses
- Figure from tsinfer paper, population structure through topology, taking the genealogy and finding closest sample to get a relatedness matrix
- Introgression
    + We look for a long branch in the tree, which coalesces far back
    + In non-African populations, these long branches matched Neanderthal or Denisovan
    + In African populations, there are other events (not Neanderthal or Denisovan)
- Mutation rate evolution
    + Studied this with Relate, 96 triplet mutations
    + TCC to TTC mutation rate in EUR has largest change (Kelley Harris, PNAS, 2015)
    + Richard: it could be environmental, e.g. cooking over carcinogenic fires
    + Mice: ones that mutate GC to AT are overrepresented in the past? However, this is NOT really mutation rate, but GC-biased gene conversion
    + Richard: new insertions are more common in GC-rich regions, but they get eliminated
- Selection using trees
    + EDAR mutation
    + Statistical test that gives a p-value, more power than any of the existing methods in both YRI and GBR (Relate Selection Test vs. SDS, trSDS, iHS)

## <a name="kelleher"></a> Jerome Kelleher, ARG formats and software (45 mins)
[[top](#top)]

Overview: nuts and bolts of ARGs
- Survey existing formats and software
- Overview of tskit
- Thoughts on community standardisation

What's an ARG?
- I mean a Griffiths (1991) ARG (going until one grand TMRCA)
- Representable as a directed acyclic graph
- ArgML (McGill et al. 2013), GraphML specializations, but died out
- Extended Newick (Cardona et al. 2008), all sorts of problems
- ARGweaver: text-based ARG format and Python API
- Other programs (e.g. FastARG) output various text based ARG formats, without providing API

Tree sequences
- Can encode the same information as ARGs
- Think about ancestral haplotypes and events relating these haplotypes
- We store nodes and edges

## <a name="wong"></a> Yan Wong, ARG metrics (45 mins)
[[top](#top)]

## <a name="ralph"></a> Peter Ralph, Benchmark simulations for ARG inference (45 mins)
[[top](#top)]

## <a name="discussion"></a> General discussion
[[top](#top)]

## <a name="summary"></a> Summary and agreed actions
[[top](#top)]
