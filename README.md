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

Geographically, most study authors' affiliations were situated in Asia, with representation from all continents except Antarctica. The predominant research approach was experimental, while only a small number of articles involved observation.


### Competition drives directional-disruptive pattern overall

<img width="883" height="430" alt="image" src="https://github.com/user-attachments/assets/89fd9351-43a0-421f-97bb-f6bfb38db178" />


### Asymmetrical competition between native and nonnative species

<img width="789" height="401" alt="image" src="https://github.com/user-attachments/assets/a7b59cf4-47b1-4a45-a91a-0ccdc50a9b6c" />


### Plants and animals show different trait distribution patterns

<img width="782" height="408" alt="image" src="https://github.com/user-attachments/assets/e5a017c5-71f1-4230-a018-2a9c5cb0f7ac" />


### Chemical/physiological traits show weak overall effects

<img width="822" height="398" alt="image" src="https://github.com/user-attachments/assets/ed06c8c6-424f-4761-94f2-8a37d146d95b" />


### Intraspecific competition exceeds interspecific competition

<img width="860" height="398" alt="image" src="https://github.com/user-attachments/assets/912c4aa8-892c-4203-8921-518d33e60fe9" />


###  Publication bias
#### Identifying publication bias
##### Funnel plots
```r
funnel(rmod.mix0, level=c(90, 95, 99), shade=c("white", "gray55", "gray75"), yaxis="seinv", refline = 0, legend = TRUE); title(main = "Effect Size for Mean", col.main = "black", font.main = 1)

funnel(rmod.mix0.v, level=c(90, 95, 99), shade=c("white", "gray55", "gray75"), yaxis="seinv", refline = 0, legend = TRUE); title(main = "Effect Size for Variation", col.main = "black", font.main = 1)
```
<img width="507" height="388" alt="image" src="https://github.com/user-attachments/assets/fed536d0-90bd-470c-9386-40b5bd50a941" />

<img width="507" height="388" alt="image" src="https://github.com/user-attachments/assets/582843a0-b0c9-47fc-9cce-a5c01c95ee93" />

> Funnel plots provide an intuitive visualization that helps us quickly understand the distribution of study results and identify small-study effects. However, funnel plots themselves do not offer specific statistical significance, so caution is needed when interpreting results. It is typically necessary to complement them with other methods and tests for a more comprehensive assessment of publication bias. Next, we will conduct testing using an extending Egger’s regression method for multilevel meta-regression (Nakagawa et al. 2022).

#### Testing for Publication Bias with Multilevel Meta-Regression
##### Publication Bias for mean
```r
# Define the multivariate meta-regression model to examine publication bias
publication.bias.model.rr.srin <- rma.mv(yi, vi, 
                   mods= ~ + Pub_Year + Study_Test_Type, 
                   random = list(~|Study_Authors/id),
                   method = "ML", 
                   test = "t", 
                   data = ddd.new)

summary(publication.bias.model.rr.srin)
# No publication bias was detected based on the meta-regression with publication year and study test type as moderators
```

##### Publication Bias for variation
```r
# Define the multivariate meta-regression model to examine publication bias
publication.bias.model.cvr.srin <- rma.mv(yi, vi, 
                   mods= ~ + Pub_Year + Study_Test_Type, 
                   random = list(~|Study_Authors/id),
                   method = "ML", 
                   test = "t", 
                   data = rd)

summary(publication.bias.model.cvr.srin)
# No publication bias was detected based on the meta-regression with publication year and study test type as moderators
```

##### Figures for publication bias
```r
# Time-lag for mean
ggplot(ddd.new, aes(x = Pub_Year, y = yi)) + 
  geom_point(
    shape = 21, 
    fill = rgb(0, 0, 0, 0.1), 
    color = rgb(0, 0, 0, 0.2)
  ) +
  geom_smooth(
    method = "lm", 
    se = TRUE, 
    fill = rgb(104/255, 34/255, 139/255, 0.5),
    color = "darkorchid4",
    linewidth = 1.2
  ) +
  geom_hline(yintercept = 0, linetype = "dashed", linewidth = 1) +
  labs(
    x = "Publication year", 
    y = "Effect size", 
    size = "N ="
  ) +
  scale_size_area(max_size = 10) +
  scale_x_continuous(
    breaks = seq(min(ddd.new$Pub_Year), max(ddd.new$Pub_Year), by = 2),
    expand = expansion(mult = c(0.02, 0.02))
  ) +
  scale_y_continuous(
    breaks = round(seq(-3, 2, 0.5), 1),
    expand = expansion(mult = c(0.02, 0.02))
  ) +
  theme_bw() +
  theme(
    text = element_text(size = 15),
    legend.position = "bottom",
    legend.direction = "horizontal",
    panel.grid = element_blank(),
    axis.text.x = element_text(angle = 45, hjust = 1, size = 10),
    axis.text.y = element_text(size = 10),
    axis.title = element_text(size = 14),
    plot.margin = margin(10, 10, 10, 10)
  )

# This visualization shows no time lag in the mean

# Time-lag for variation
ggplot(rd, aes(x = Pub_Year, y = yi)) + 
  geom_point(
    shape = 21, 
    fill = rgb(0, 0, 0, 0.1), 
    color = rgb(0, 0, 0, 0.2)
  ) +
  geom_smooth(
    method = "lm",
    se = TRUE,
    fill = rgb(104/255, 34/255, 139/255, 0.5),
    color = "darkorchid4",
    linewidth = 1.2
  ) +
  geom_hline(yintercept = 0, linetype = "dashed", linewidth = 1) +
  labs(
    x = "Publication year",
    y = "Effect size",
    size = "N ="
  ) +
  scale_size_area(max_size = 10) +
  scale_x_continuous(
    breaks = seq(min(rd$Pub_Year), max(rd$Pub_Year), by = 2),
    expand = expansion(mult = c(0.02, 0.02))
  ) +
  scale_y_continuous(
    breaks = round(seq(-3, 2, 0.5), 1),
    expand = expansion(mult = c(0.02, 0.02))
  ) +
  theme_bw() +
  theme(
    text = element_text(size = 15),
    legend.position = "bottom",
    legend.direction = "horizontal",
    panel.grid = element_blank(),
    axis.text.x = element_text(angle = 45, hjust = 1, size = 10),
    axis.text.y = element_text(size = 10),
    axis.title = element_text(size = 14),
    plot.margin = margin(10, 10, 10, 10)
  )

# This visualization shows no time lag in the variation
```
<img width="506" height="388" alt="image" src="https://github.com/user-attachments/assets/b7a85785-e606-4bed-a34a-b572c5c90c9b" />

<img width="506" height="388" alt="image" src="https://github.com/user-attachments/assets/252ec18f-c1ff-4f51-82e6-fa86a7872f5b" />


###  Sensitivity analysis
#### Remove influential outliers
##### Overall mean
```r
# Calculate hat values and the average hat value following Habeck and Schultz (2015)
hat_values <- hatvalues(rmod.mix0)
average_hat_value <- mean(hat_values)

# Extract residuals
residuals <- residuals(rmod.mix0)

# Identify indices of influential outliers
influential_outliers<- which(hat_values > 2 * average_hat_value & abs(residuals) > 3.0)

# Trait variation
hat_values <- hatvalues(rmod.mix0.v)
average_hat_value <- mean(hat_values)

# Extract residuals
residuals <- residuals(rmod.mix0.v)

# Identify indices of influential outliers
influential_outliers2 <- which(hat_values > 2 * average_hat_value & abs(residuals) > 3.0)

# Remove influential outliers from the data
data_clean<- ddd.new[-influential_outliers1, ]; data_clean<- data_clean1[-influential_outliers2, ]

data_clean2 <- rd[-influential_outliers1, ]; data_clean2 <- data_clean2[-influential_outliers2, ]

# Run the rma.mv model using the cleaned data
rmod.mix0.ms <- rma.mv(yi ~ 1, V = vi, random = list(~|Study_Authors/id), method = "ML", data = data_clean1)
rmod.mix0.ms
```

##### Overall variation
```r
# Run the rma.mv model using the cleaned data
rmod.mix0.vs <- rma.mv(yi ~ 1, V = vi, random = list(~|Study_Authors/id), method = "ML", data = data_clean2)
rmod.mix0.vs
```

#### Replace extreme vi values
##### Overall mean
```r
# There are extreme values for vi in the models, and this analysis examines whether these values have a substantial impact on the model estimates.
ddn <- ddd.new

# Set the 2.5% and 97.5% quantiles
quantile(ddn$vi, probs = c(0.025, 0.975), na.rm = TRUE)
lower_bound <- 0.0001313575
upper_bound <- 1.1218487040

# Replace extreme values in vi with the corresponding quantiles
ddn$vi[ddn$vi < lower_bound] <- lower_bound
ddn$vi[ddn$vi > upper_bound] <- upper_bound

# Model after replacing extreme vi values
m.ddnew <- rma.mv(yi, vi, random = ~ | Study_Authors/id, data = ddn, tdist = TRUE, method = "ML")
m.ddnew
```

##### Overall variation
```r
dd <- rd

# Set the 2.5% and 97.5% quantiles
quantile(rd$vi, probs = c(0.025, 0.975), na.rm = TRUE)
lower_bound <- 0.04408487
upper_bound <- 1.87853024

# Replace extreme values in vi with the corresponding quantiles
dd$vi[dd$vi < lower_bound] <- lower_bound
dd$vi[dd$vi > upper_bound] <- upper_bound

# Model after replacing extreme vi values
m.rdnew <- rma.mv(yi, vi, random = ~ | Study_Authors/id, data = dd, tdist = TRUE, method = "ML")
m.rdnew
```

#### Distinguishing between Ecosystems
##### Overall mean
```r
# Exclude data for Ecosystem "aquatic"
m.excl.ea <- rma.mv(yi, vi, random = ~ | Study_Authors/id, data = ddd.new[ddd.new$Ecosystem != "aquatic",], tdist = TRUE, method = "ML")
m.excl.ea

# Exclude data for Ecosystem "terrestrial"
m.excl.et <- rma.mv(yi, vi, random = ~ | Study_Authors/id, data = ddd.new[ddd.new$Ecosystem != "terrestrial",], tdist = TRUE, method = "ML")
m.excl.et

# Exclude data for Ecosystem "model organism"
m.excl.eo <- rma.mv(yi, vi, random = ~ | Study_Authors/id, data = ddd.new[ddd.new$Ecosystem != "model organism",], tdist = TRUE, method = "ML")
m.excl.eo
```

##### Overall variation
```r
# Exclude data for Ecosystem "aquatic"
m.excl.eav <- rma.mv(yi, vi, random = ~ | Study_Authors/id, data = rd[rd$Ecosystem != "aquatic",], tdist = TRUE, method = "ML")
m.excl.eav

# Exclude data for Ecosystem "terrestrial"
m.excl.etv <- rma.mv(yi, vi, random = ~ | Study_Authors/id, data = rd[rd$Ecosystem != "terrestrial",], tdist = TRUE, method = "ML")
m.excl.etv

# Exclude data for Ecosystem "model organism"
m.excl.eov <- rma.mv(yi, vi, random = ~ | Study_Authors/id, data = rd[rd$Ecosystem != "model organism",], tdist = TRUE, method = "ML")
m.excl.eov
```

> These sensitivity studies generated results that are largely similar with those reported in the main article.

