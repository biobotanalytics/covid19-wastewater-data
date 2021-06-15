Nationwide Wastewater Monitoring Network
========================================

This GitHub repo provides the underlying source data for the Nationwide
Wastewater Monitoring Network data visualizations at
[biobot.io/data](http://biobot.io/data).
We provide both Biobot-generated wastewater SARS-CoV-2 concentrations
and the associated clinical data used as comparison.

About the data
--------------

Biobot\'s data is currently the largest publicly available dataset on
SARS-CoV-2 concentrations in wastewater.

We first launched a *pro bono* campaign to monitor COVID-19 in
wastewater across the United States in March 2020, in collaboration with
the [Alm lab](https://web.mit.edu/almlab/) at MIT 
[(Wu et al., 2020)](https://journals.asm.org/doi/full/10.1128/mSystems.00614-20) 
[(Wu et al., 2021)](https://www.medrxiv.org/content/10.1101/2021.03.10.21253235v2)
In June 2020, we transitioned to a full-service offering and
currently have over 50 participating locations regularly monitoring
SARS-CoV-2 wastewater concentrations in their communities on a weekly or
monthly basis. As a result of this effort, we have generated a
wastewater SARS-CoV-2 dataset consisting of over 6,000 samples.

Recently, Biobot
[was selected](https://biobotanalytics.medium.com/biobot-launches-national-wastewater-monitoring-project-2968a5997af)
by the US Department of Health and Human Services to scale
wastewater-based monitoring nationwide. As part of this program, Biobot
will expand its wastewater monitoring efforts to 30% of the US
population, including many additional wastewater utilities which are not
already participating in wastewater surveillance.

Wastewater data
---------------

The wastewater data shows SARS-CoV-2 normalized virus concentrations
measured by Biobot in samples from wastewater treatment facilities
across the United States. To preserve the anonymity of our participating
utilities and to improve their representativeness, we present aggregated
data:

1.  For each sampling location, if there is more than one sample in a
    week, we aggregate the samples within each week using an unweighted
    average.

2.  For each geographic unit (county, region, or nation) and week, we
    aggregate across sampling locations within the geographic unit using
    a weighted average. The weight for a sampling location is the
    relevant sewershed population, or 300,000, whichever is smaller.
    When a sampling location serves multiple counties, the location is
    associated with the single county that the wastewater operator has
    provided as being the plant's primary service area.

3.  For each geographic unit, we smooth the weekly values using a
    3-value rolling unweighted average. If the geographic unit does not
    have a sample every week, then this 3-value window can include
    samples outside of a 3-week window.

We use the weighted average scheme for aggregating values across
sampling locations as a compromise to achieve multiple goals:

-   Population-weighted averaging means that each person contributes
    approximately equally to the resulting value.

-   Because smaller catchments have more intrinsic variability,
    population-weighted averaging improves the statistical properties of
    the average.

-   Capping the weights increases the relative contribution of small and
    medium catchments, which aids both geographic representativeness and
    statistical properties of the average.

We include data from all Biobot's sampling locations in the nationwide
and regional aggregate results. In the individual county plots, we
report data from counties that have sampling locations with at least 22
weeks of data and whose sampling locations represent a total of more
than 10,000 people.

Clinical data
-------------

The clinical data is sourced from [USA
Facts](https://www.google.com/url?q=https://usafacts.org/visualizations/coronavirus-covid-19-spread-map/&sa=D&source=editors&ust=1623189474225000&usg=AOvVaw0UVchIrhVeYBWKG-XBFI7_),
which provides [programmatic
access](https://www.google.com/url?q=https://static.usafacts.org/public/data/covid-19/covid_county_population_usafacts.csv&sa=D&source=editors&ust=1623189474225000&usg=AOvVaw3-r61NPBZ1km7Pgkp0E39l)
to cumulative COVID-19 cases at the county level. We calculated daily
new cases as the difference in cumulative cases between consecutive
days. To prevent negative new cases, any days with a decrease in
cumulative cases (or with missing or non-numeric values) were replaced
with the prior valid cumulative case number. The reported case counts
are then smoothed using a centered 7-day rolling average of new cases
and reported per 100,000 population.

When aggregating case data at the nationwide and region levels, we
include all counties in the USA Facts dataset. Thus, divergences between
the wastewater and clinical time courses can be due to incomplete
wastewater sampling. For example, if a surge in Covid-19 activity
occurred in a part of a region where there was no wastewater testing,
then the clinical time course would increase but the wastewater time
course would not.

Data dictionary
---------------

### Regional aggregate data

**cases\_by\_region.csv**

-   date: date as reported by USA Facts (YYYY-MM-DD)

-   rolling\_average\_cases\_per\_100k: centered 7-day rolling average
    of new cases per 100,000 population (float)

-   region: the US Census region that each data point is associated
    with. These regions are derived from Census regions (str,
    categorical)

**wastewater\_by\_region.csv**

-   sampling\_week: Wednesday of the week of sampling. We compare
    Wednesday to centered rolling cases because Wednesday is in the
    middle of the work week, and most of our samples are collected
    between Monday and Friday. (YYYY-MM-DD)

-   normalized\_concentration\_rolling\_average: three-sample average of
    weekly normalized concentrations (normalized
    genome copies per mL of wastewater)

-   population: sum of sewershed populations for all constituent
    sampling locations, binned to preserve anonymity (str, categorical)

-   region: the US Census region that each data point is associated
    with. These regions are derived from Census regions. (str,
    categorical)

### County data

**cases\_by\_county.csv**

-   date: date as reported by USA Facts (YYYY-MM-DD)

-   rolling\_average\_cases\_per\_100k: centered 7-day rolling average
    of new cases per 100,000 population (float)

-   region: the US Census region that each data point is associated
    with. These regions are derived from Census regions (str,
    categorical)

-   state: two-letter abbreviation of the US state (str)

-   county\_fips\_code: county FIPS code provided by USA facts (str)

-   county\_name: Name of the county (str)

**wastewater\_by\_county.csv**

-   sampling\_week: Wednesday of the week of sampling. We compare
    Wednesday to centered rolling cases because Wednesday is in the
    middle of the work week, and most of our samples are collected
    between Monday and Friday. (YYYY-MM-DD)

-   normalized\_concentration\_rolling\_average: three-sample average of
    weekly normalized concentrations (normalized
    genome copies per mL of wastewater)

-   population: sum of sewershed populations for all constituent
    sampling locations, binned to preserve anonymity (str, categorical)

-   region: the US Census region that each data point is associated
    with. These regions are derived from Census regions (str,
    categorical)

-   state: two-letter abbreviation of the US state (str)

-   county\_fips\_code: county FIPS code provided by USA facts (str)

-   county\_name: Name of the county (str)

Lab methods
-----------

Our methods for detecting SARS-CoV-2 in sewage are adapted from CDC
protocols. Our approach relies on detecting genetic fragments of the
virus that are excreted in stool by qPCR analysis. Here, we show
SARS-CoV-2 concentrations after normalization to a fecal virus marker
(PMMoV).

Our lab methods have changed over time to improve sample processing
time, throughput, and sensitivity, also accounting for supply-chain
availability. In our first method, samples for analysis were first
filtered to remove large particulate matter using a 0.2uM vacuum-driven
filter and used PEG-salt precipitation to concentrate viruses from 40mL
of wastewater, as described in Wu et al. 2020. Resulting pellets were
resuspended in TRIzol, and RNA was purified via phenol-chloroform
extraction. RNA samples were first reverse transcribed and then assayed
by qPCR using SARS-CoV-2 nucleocapsid gene (N1 and N2 regions), and
PMMoV amplicons 
([CDC 2020](https://www.cdc.gov/coronavirus/2019-ncov/lab/rt-pcr-panel-primer-probes.html); 
[Zhang et al., 2006](https://doi.org/10.1371/journal.pbio.0040003)).

In June 2020, we switched to our current protocol which uses Amicon
Ultra-15 centrifugal ultrafiltration units to concentrate 15mL of
wastewater approximately 100x. Viral particles in this concentrate are
immediately lysed by adding AVL Buffer containing carrier RNA to the
Amicon unit before transfer and \>10-minute incubation. To adjust
binding conditions, 100% ethanol was added to the lysate, and RNA was
extracted using RNeasy Mini columns or cassettes. For a subset of
samples, we processed 30 mL of wastewater across two separate Amicon
units, extracted each separately, and pooled the duplicate RNA extracts
together prior to analysis. RNA samples eluted from the RNeasy kit were
subjected to one-step RT-qPCR analysis in triplicate for N1, N2, and
PMMoV amplicons. Cts were called from raw fluorescence data using the
Cy0 algorithm from the qpcR package (v1.4-1) in R 
([Guescini et al., 2008](https://doi.org/10.1186/1471-2105-9-326)),
and manually inspected for agreement with the raw traces in the
native Biorad software.

In both methods, we ran a variety of laboratory controls. Positive
synthetic SARS-CoV-2 RNA controls were included on every qPCR plate. Two
no-template controls were included on every qPCR plate. For positive and
negative controls, Ct values outside the expected ranges triggered a
re-run of the qPCR plate. One set of extraction blank controls was also
run each day. Matrix inhibition was assessed manually by reviewing the
raw qPCR curves. Finally, we used PMMoV as a proxy measure for per-plate
recovery and flagged any qPCR plates with unusually low PMMoV values for
further review and potential plate re-run. Only results which passed all
quality controls are included in this dataset.

In addition to these laboratory controls, we implemented a thorough data
review process, in which results were manually reviewed if they failed
to meet certain additional data-driven criteria. During the manual
review process, we inspected individual replicates of qPCR and timelines
of SARS-CoV-2 and PMMV concentrations. A small fraction of samples that
underwent the review process were flagged for a lab rerun. Reruns were
done in duplicates if capacity allowed, and always using a different 50
mL tube of sewage. About 120 samples (approximately 2% of all samples)
were re-run through that process. If a rerun differed substantially from
the original result, the original result was discarded (10 out of 120
reruns) and the rerun results are reported; if the rerun recapitulated
the original result, the average of all results are reported.

Authors and acknowledgment
--------------------------
This work could not have been possible without the support of the participating wastewater utilities; we thank the sampling operators and treatment plant facilities for their efforts and collaboration as we established and scaled our processes, and for agreeing to make this data available to the community. 


License
-------

This work is licensed under [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/). This license requires that reusers give credit to the creator. It allows reusers to distribute, remix, adapt, and build upon the material in any medium or format, for noncommercial purposes only.

Collaborations
--------------

If you are interested in an academic collaboration using this data,
please reach out to
[mariana\@biobot.io](mailto:mariana@biobot.io) and
[claire\@biobot.io](mailto:claire@biobot.io).

Support & questions
-------------------

For any questions related to this data, please contact [hello\@biobot.io](mailto:hello\@biobot.io).

About Biobot Analytics
----------------------

Biobot Analytics is a wastewater epidemiology company and uses
technology developed at MIT to measure human health in sewage to
understand population health in cities. We are actively engaged in using
wastewater-based epidemiology to help our customers manage the Covid-19
pandemic.

Inspired by the potential of wastewater epidemiology, we are the first
company in the world to bring wastewater epidemiology to market. We are
VC backed by top investors including The Engine, DCVC, Y Combinator and
Homebrew. Battling the opioid epidemic and now the Covid19 outbreak is
just the beginning - we are transforming sewage into a data asset and
building a public health database. Eventually, Biobot will be an early
warning system for disease, a map of nutrition disparities, and more.
Headquartered in the Boston area with an office in NYC, we aim to create
the bedrock of human health infrastructure and smart cities in countries
across all six continents.

Learn more about our work on our website: [www.biobot.io](http://www.biobot.io).
