# A global meta-analysis across species provenance

![PRISMA](https://github.com/user-attachments/assets/62745864-6dbb-4d51-8c98-f927d4c260d1)

## DATA DESCRIPTION AND SUMMARY

This project builds on a global meta-analysis I led as first author, published in Nature Communications. It compiles trait data from both plants and animals to investigate how competition between native and nonnative species shapes trait distributions. Data processing involved **trait standardization, effect size calculation, and mixed-effects modeling**. The workflow followed **PRISMA guidelines, incorporating systematic literature screening, extraction of text and figure-based data, trait categorization, and meta-analysis using metafor, multcomp, and bibliometrix in R**.

Results indicate that competition generally reduces trait means while increasing trait variation, with stronger impacts observed for interactions involving nonnative species. This project demonstrates expertise in large-scale data integration, ecological statistics, and reproducible research workflows.

## PREPARATION FOR THE DATASET

The dataset was prepared following PRISMA guidelines through a comprehensive literature search and screening process. Over **15,000 records** were initially retrieved from Web of Science using targeted search terms.

After applying strict inclusion criteria—such as:
- presence of **control vs. competition** treatments,
- availability of **trait means, variance, and sample size**, and
- evidence of **individual-level trait expression**,

we retained **346 studies** and **3,779 observations**.

Data were extracted from:
- article texts and tables,
- figures using [PlotDigitizer](https://plotdigitizer.com/app), and
- raw supplementary files.

Trait data were classified into **morphological**, **life-history**, and **chemical/physiological** categories. Competitor types were labeled as **intraspecific** or **interspecific**, and species provenance as **native** or **nonnative**, verified via external sources when needed.

We also performed **bibliometric analysis** using the R package [`bibliometrix`](https://www.bibliometrix.org/) to identify study trends and refine categories.

Effect sizes were calculated using the R package [`metafor`](https://www.metafor-project.org/):
- **lnRR** (log response ratio) for trait means,
- **lnCVR** (log coefficient of variation ratio) for trait variation.

We used **multi-level mixed-effects models** with random and fixed effects to analyze trait shifts, and conducted:
- **publication bias checks** using funnel plots and Egger's regression, and
- **sensitivity analyses** for outliers, variances, and ecosystem types.

All data processing and modeling were implemented in **R** and compiled in a fully reproducible **R Markdown** workflow.

## Results

<img width="896" height="276" alt="image" src="https://github.com/user-attachments/assets/b140bc92-8f6f-49d4-bcfa-34e60364a3d3" />

### Competition drives directional-disruptive pattern overall

<img width="883" height="430" alt="image" src="https://github.com/user-attachments/assets/89fd9351-43a0-421f-97bb-f6bfb38db178" />
