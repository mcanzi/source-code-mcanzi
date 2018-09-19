---
title: "academic skills"
date: 2017-09-22
tags: [ "r", "praat", "matlab", "erp", "ggplot"]
draft: false
---

__Speech Analysis:__		Praat (_sample script below_), Wavesurfer 

__Speech Manipulation:__		Praat, iZotope, Audacity

__ERP/EEG:__		Matlab, ERPlab/EEGlab

__Experimental Design:__	E-Prime, PsychoPy, Presentation (Neurobehavioral Systems)

__Statistical Analysis & Data Visualisation:__	RStudio, R markdown (_sample script below_)

__Other programming languages known:__	Python

__Text typesetting:__	TeX, Microsoft Word

## Data visualisation

Hi, here's some random plots I made in ggplot!


__F1-F2 vowel plots for male Italian speakers in neutral and loud speech.__

![plot1](/img/plot1.png)

![plot2](/img/plot2.png)

![plot3](/img/plot3.png)

## Sample of R script

The following sample of an R project is meant to read data, clean it, run mixed effect models and create visual representation of results with ggplot. The sample needs the dataset to work. The whole reproducible project is found [__here__](www.github.com/mcanzi/mfil)

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
library(tidyverse)
theme_set(theme_minimal())
library(lmerTest)
library(effects)

source("https://gist.githubusercontent.com/benmarwick/2a1bb0133ff568cbe28d/raw/fb53bd97121f7f9ce947837ef1a4c65a73bffb3f/geom_flat_violin.R")

cbPalette <- c("salmon", 'steelblue', 'goldenrod1')
cbPaletteCol <- c("salmon", 'steelblue', 'goldenrod2')
cbbPalette <- c("#000000", "#E69F00", "#56B4E9", "#009E73", "#F0E442", "#0072B2", "#D55E00", "#CC79A7")
```

<!-- Data is filtered and 'cleaned up'. -->

```{r filters}
data <- read_csv('MAdata.csv') %>%
  filter(F1 < 1500, Vowel != 'u') %>%
  mutate_if(is.character, as.factor) %>%
  mutate(Speaker = as.factor(Speaker)) %>%
  group_by(Speaker) %>%
  mutate(F0_z = scale(F0)) %>%
  ungroup() %>%
  filter(F0_z > -2)

durData <- read_csv('MAdata.csv') %>%
  mutate_if(is.character, as.factor)
```

<!-- Here's lmer tests for F0, F1, F2 and Duration throughout speech modes. Speaker is regarded as random effect. The 'Long' veriable for the duration model represents phonological length. -->

```{r F0lmer}
F0_lmer <- lmer(
  F0 ~ Vowel * Mode + (1+Mode|Speaker),
  data
)
summary(F0_lmer)

```
```{r F1lmer}
F1_lmer <- lmer(
  F1 ~ Mode + (1+Mode|Speaker),
  data
)
summary(F1_lmer)
```

```{r F2lmer }
F2_lmer <- lmer(
  F2 ~ Mode * Vowel + (1+Mode|Speaker),
  data
)
summary(F2_lmer)
```

<!-- Duration lmer contains information for 'u' as well, which was omitted in the previous models for formant tracking issues. -->

```{r durlmer}
Dur_lmer <- lmer(
  Duration ~ Long * Mode + (1+Mode|Speaker),
  durData
)
summary(Dur_lmer)
```

<!-- Sample effects plot for F1 -->

```{r allEffectsF1}
plot(allEffects(F2_lmer))
```

```{r rainCloudTheme}
raincloud_theme = theme(
text = element_text(size = 10),
axis.title.x = element_text(size = 16),
axis.title.y = element_text(size = 16),
axis.text = element_text(size = 14),
axis.text.x = element_text(angle = 45, vjust = 0.5),
legend.title=element_text(size=16),
legend.text=element_text(size=16),
legend.position = "right",
plot.title = element_text(lineheight=.8, face="bold", size = 16),
panel.border = element_blank(),
panel.grid.minor = element_blank(),
panel.grid.major = element_blank(),
axis.line.x = element_line(colour = 'black', size=0.5, linetype='solid'),
axis.line.y = element_line(colour = 'black', size=0.5, linetype='solid'))
```

<!-- Sample box/violin plots with jitter for F0 and Duration, faceted by gender and phonological lenth respectively -->

```{r f0density}
F0z_den <- ggplot(data, aes(x = F0_z, fill = Mode, alpha = 0.5)) + 
  geom_density(bw = 0.30, trim = FALSE) +
  scale_fill_manual(values = paletteFill)
F0z_den
```


```{r f0z-plot}
F0z_plot <- ggplot(data, aes(y = F0_z, x = Mode, fill = Mode)) +
  geom_flat_violin(position = position_nudge(x = .2, y = 0), alpha = .8) +
  geom_point(aes(y = F0_z, color = Mode), position = position_jitter(width = .15), size = .2, alpha = 0.5) +
  geom_boxplot(width = .1, outlier.shape = NA, alpha = 0.5) +
  expand_limits(x = 4) +
  #guides(fill = FALSE) +
  guides(color = FALSE) +
  coord_flip() +
  theme_bw() + scale_fill_manual(values = cbPalette) + 
  labs(title = "Scaled F0 in soft, neutral and loud speech") +
  scale_colour_manual(values = cbPaletteCol)

F0z_plot

ggsave('plots/F0zplot.png', F0z_plot)
```



```{r dur-Plot}
Dur_plot <- ggplot(durData, aes(y = Duration, x = Mode, fill = Mode)) +
  geom_flat_violin(position = position_nudge(x = .2, y = 0), alpha = .8) +
  geom_point(aes(y = Duration, color = Mode), position = position_jitter(width = .15), size = .5, alpha = 0.5) +
  geom_boxplot(width = .1, outlier.shape = NA, alpha = 0.5) +
  expand_limits(x = 4) +
  #guides(fill = FALSE) +
  guides(color = FALSE) +
  coord_flip() +
  theme_bw() + scale_fill_manual(values = cbPalette) + 
  facet_grid(~Long) +
  labs(title = 'Vowel Duration in soft, neutral and loud speech in Seconds') +
  scale_colour_manual(values = cbPalette)

Dur_plot

ggsave('plots/durPlot.png', Dur_plot)
```

<!-- F1-F2 vowel plots for M and F speakers faceted by vowel, coloured by mode -->

```{r vowelMalePlot}
FM_plot <- filter(data, Gen == "M") %>% filter(Mode != 'Soft') %>% filter(F2 < 2400) %>%
ggplot(aes(F2, F1, colour = Mode)) +
  #geom_point(alpha = 1.0) +
  stat_ellipse(geom = "polygon", alpha = 0.8, aes(fill = Mode)) +
  scale_x_reverse() +
  scale_y_reverse() + 
  facet_wrap(~Vowel, scales = 'free') + 
  scale_colour_manual(values = cbPalette) +
  scale_fill_manual(values = cbPalette)
  # + labs(title = "F1-F2 vowel plots for male speakers in neutral and and loud speech") 
 
FM_plot
ggsave('plots/vowelPlotMale.png', FM_plot)
```

```{r vowelFPlot}
FF_plot <- filter(data, Gen == "F") %>% filter(Mode != 'Soft') %>% filter(F2 < 2500) %>%
ggplot(aes(F2, F1, colour = Mode)) +
  #geom_point(alpha = 1) +
  stat_ellipse(geom = "polygon", alpha = 1/2, aes(fill = Mode)) +
  scale_x_reverse() +
  scale_y_reverse() + 
  facet_wrap(~Vowel, scales = 'free') +
  # labs(title = "F1-F2 vowel plots for female speakers in neutral and loud speech") +
  scale_colour_manual(values = paletteFill)
  
FF_plot
ggsave('plots/VowelPlotFemale.png', FF_plot)
```

```{r sampleIA}

sample_F1 <- filter(data, Gen == 'M') %>% filter(Vowel %in% c('i', 'a')) %>% 
  ggplot(aes(y = F1, x = Mode, fill = Mode)) +
  geom_flat_violin(position = position_nudge(x = .2, y = 0), alpha = 0.8) +
  geom_point(aes(y = F1, color = Mode), position = position_jitter(width = .15), size = .5, alpha = 0.5) + stat_ellipse() +
  geom_boxplot(width = .1, outlier.shape = NA, alpha = 0.5) +
  expand_limits(x = 4) +
  guides(fill = FALSE) +
  guides(color = FALSE) +
  coord_flip() +
  facet_grid(~Vowel) +
  theme_bw() + scale_fill_manual(values = cbPalette) +
  # labs(title = 'F1 for /a/ and /i/ by male speakers in soft, neutral and loud speech') +
  scale_colour_manual(values = cbPaletteCol)

sample_F1
ggsave('plots/sampleF1ai.png', sample_F1)
```

```{r sampleIAF2}

sample_F2 <- filter(data, Gen == 'M') %>% filter(Vowel %in% c('e')) %>% filter(Speaker %in% c('1', '2', '3')) %>% filter(F2 > 1750) %>%
  ggplot(aes(y = F2, x = Mode, fill = Mode)) +
  #geom_flat_violin(position = position_nudge(x = .2, y = 0), alpha = .8) +
  geom_point(aes(y = F2, color = Mode), position = position_jitter(width = .15), size = .5, alpha = 1) +
  geom_boxplot(width = .9, outlier.shape = NA, alpha = 0.8) +
  #expand_limits(x = 4) +
  guides(colour = FALSE) +
  #guides(fill = FALSE) +
  guides(color = FALSE) +
  #coord_flip() +
  facet_grid(~Speaker) +
  theme_bw() + scale_fill_manual(values = cbPalette) +
  # labs(title = 'F2 for /e/ by male speakers 1, 2 and 3 in soft, neutral and loud speech') +
  scale_colour_manual(values = cbPaletteCol)

sample_F2
ggsave('plots/sampleF2ai.png', sample_F2)
```

```{r}
betweenNeutral <- filter(data, Vowel == 'e') %>% 
  filter(Speaker %in% c('1', '2', '3')) %>%
  filter(Mode == 'Neutral') %>% 
  ggplot(aes(x = F2, fill = Speaker, alpha = 0.5)) +
  geom_density(trim = FALSE) +
  scale_fill_manual(values = cbPalette)

betweenNeutral
ggsave('plots/f2e123Neutral.png', betweenNeutral)
  
```

```{r}
betweenLoud <- filter(data, Vowel == 'e') %>% 
  filter(Speaker %in% c('1', '2', '3')) %>%
  filter(Mode == 'Loud') %>% 
  ggplot(aes(x = F2, fill = Speaker, alpha = 0.5)) +
  geom_density() + 
  scale_fill_manual(values = cbPalette)

betweenLoud
ggsave('plots/f2e123Loud.png', betweenLoud)
  
```

```{r interNeutralM123}
interNeutral <- filter(data, Mode =='Neutral') %>% filter(Speaker %in% c('1', '2', '3')) %>%
  filter(Vowel %in% c('i', 'e')) %>%
                    ggplot(aes(F2, F1, colour = Speaker)) +
                    geom_point(alpha = 1) + 
                    stat_ellipse(geom = "polygon", alpha = 1/2, aes(fill = Speaker)) +
                    scale_x_reverse() +
                    scale_y_reverse() + 
                    facet_wrap(~Vowel, scales = 'free') +
                    scale_fill_manual(values = cbPalette) +
                    scale_colour_manual(values = cbPaletteCol)

interNeutral
ggsave('plots/interNeutralF1F2123ei.png', interNeutral)

```

```{r interLoudM}
interLoud <- filter(data, Mode =='Loud') %>% filter(Speaker %in% c('1', '2', '3')) %>%
  filter(Vowel %in% c('i', 'e')) %>%
                    ggplot(aes(F2, F1, colour = Speaker)) +
                    geom_point(alpha = 1) + 
                    stat_ellipse(geom = 'polygon', alpha = 1/2, aes(fill = Speaker)) +
                    scale_x_reverse() +
                    scale_y_reverse() + 
                    facet_wrap(~Vowel, scales = 'free') +
                    scale_colour_manual(values = cbPalette) +
                    scale_fill_manual(values = cbPalette)

interLoud
ggsave('plots/interLoudF1F2123ei.png', interLoud)

```


## Sample of Praat script

The script loops through participant folders (derived data). It creates random chunks of speech of 6, 11 and 16 seconds for each .wav file. Fade-in and fade-out are applied at 0.5s and 0.5s to the end respectively. Clear speech for each chunk is then considered of 5, 10 or 15 seconds. A ../data/pilot/ folder is created. Manipulated files are saved in new participant subfolders of the pilot folder e.g. /data/pilot/m001/m001_wolf_5s.wav or /data/pilot/f002/f002_mary_10s.wav.

```
clearinfo
createDirectory("../data/pilot/")

wd$ = "../data/derived/"
inDir$ = wd$
part$ = "_part"

writeFileLine: "../data/pilot/phonemes_pilot_alt.csv", "sample,index,intervalStart,intervalEnd,phoneme"
csvAlt$ = "../data/pilot/phonemes_pilot_alt.csv"

writeFileLine: "../data/pilot/phonemes_pilot.csv", "sampleID,sampleStart,sampleEnd,phonemes"
csvFile$ = "../data/pilot/phonemes_pilot.csv"

speakList = Create Strings as directory list: "speakList", inDir$
selectObject: speakList

numDirectories = Get number of strings
for dirNum from 1 to numDirectories
	dirName$ = Get string: dirNum

	createDirectory("../data/pilot/" + dirName$ + "/")
	endDir$ = "..data/pilot/" + dirName$ + "/"

	inDirWavs$ = inDir$ + dirName$ + "/" + "*_mary.wav"
	wavList = Create Strings as file list: "wavList", inDirWavs$
	selectObject: wavList
	fileNum = Get number of strings

	fileName$ = Get string: fileNum

	woExtension = length(fileName$) - 4
	soundName$ = left$(fileName$, woExtension)

	Read from file: inDir$ + dirName$ + "/" + fileName$

# 1st sample

	start = randomInteger(1, 5)
	five = start + 6

	appendInfoLine: "..."

	selectObject: "Sound " + soundName$
	ending = Get end time
	if ending - 2 > five
		Extract part: start, five, "rectangular", 1, "no"
	else
		Extract part: ending - 2 - five, ending - 2, "rectangular", 1, "no"
	endif

	selectObject: "Sound " + soundName$ + part$
	Rename: soundName$ + "_sample1"

	soundStart = Get start time
	soundEnd = Get end time
	Fade in:  0, soundStart, soundStart + 0.5, "yes"
	Fade out: 0, soundEnd - 0.5, 0.5, "yes"

	nameToSave$ = soundName$ + "_sample1"
	selectObject: "Sound " + nameToSave$
	Save as WAV file: "../data/pilot/" + dirName$ + "/" + nameToSave$ + ".wav"

	participantCode$ = left$(nameToSave$, 4)

# 1st sample phoneme list

	currentTextGrid = Read from file: inDir$ + dirName$ + "/" + participantCode$ + "_mary.TextGrid"
	intNum = Get number of intervals: 3

	startInt = Get interval at time: 3, start
	endInt = Get interval at time: 3, five
	content$ = ""
	index = 0

	for interval from startInt to endInt
		label$ = Get label of interval: 3, interval
		timeIntStart = Get starting point: 3, interval
		timeIntEnd = Get end point: 3, interval
		if label$ != "<p:>"
			content$ = content$ + label$

		index = index + 1
		appendFileLine: csvAlt$, "'nameToSave$','index','timeIntStart','timeIntEnd','label$'"

		endif
	endfor

	appendFileLine: csvFile$, "'nameToSave$','start','five','content$'"

	removeObject: "Sound " + nameToSave$

# 2nd sample

	start = randomInteger(7, 11)
	five = start + 6

	selectObject: "Sound " + soundName$
	ending = Get end time

	if ending - 2 > five
		Extract part: start, five, "rectangular", 1, "no"
	else
		Extract part: ending - 2 - five, ending - 2, "rectangular", 1, "no"
	endif

	selectObject: "Sound " + soundName$ + part$
	Rename: soundName$ + "_sample2"

	soundStart = Get start time
	soundEnd = Get end time
	Fade in:  0, soundStart, soundStart + 0.5, "yes"
	Fade out: 0, soundEnd - 0.5, 0.5, "yes"

	nameToSave$ = soundName$ + "_sample2"
	selectObject: "Sound " + nameToSave$
	Save as WAV file: "../data/pilot/" + dirName$ + "/" + nameToSave$ + ".wav"

# 2nd sample phoneme list

	selectObject: currentTextGrid
	intNum = Get number of intervals: 3

	startInt = Get interval at time: 3, start
	endInt = Get interval at time: 3, five

	content$ = ""
  index = 0

	for interval from startInt to endInt
		label$ = Get label of interval: 3, interval
		timeIntStart = Get starting point: 3, interval
		timeIntEnd = Get end point: 3, interval
		if label$ != "<p:>"
			content$ = content$ + label$

			index = index + 1
			appendFileLine: csvAlt$, "'nameToSave$','index','timeIntStart','timeIntEnd','label$'"

		endif
	endfor

	appendFileLine: csvFile$, "'nameToSave$','start','five','content$'"

	removeObject: "Sound " + nameToSave$

# 3rd sample

	start = randomInteger(1, 11)
	five = start + 6

	selectObject: "Sound " + soundName$

	ending = Get end time
	if ending - 2 > five
		Extract part: start, five, "rectangular", 1, "no"
	else
		Extract part: ending - 2 - five, ending - 2, "rectangular", 1, "no"
	endif

	selectObject: "Sound " + soundName$ + part$
	Rename: soundName$ + "_sample3"

	soundStart = Get start time
	soundEnd = Get end time
	Fade in:  0, soundStart, soundStart + 0.5, "yes"
	Fade out: 0, soundEnd - 0.5, 0.5, "yes"

	nameToSave$ = soundName$ + "_sample3"
	selectObject: "Sound " + nameToSave$
	Save as WAV file: "../data/pilot/" + dirName$ + "/" + nameToSave$ + ".wav"

# 3rd sample phoneme list

		selectObject: currentTextGrid
	intNum = Get number of intervals: 3

	startInt = Get interval at time: 3, start
	endInt = Get interval at time: 3, five

	content$ = ""
	index = 0

	for interval from startInt to endInt
		label$ = Get label of interval: 3, interval
		timeIntStart = Get starting point: 3, interval
		timeIntEnd = Get end point: 3, interval
		if label$ != "<p:>"
			content$ = content$ + label$

			index = index + 1
			appendFileLine: csvAlt$, "'nameToSave$','index','timeIntStart','timeIntEnd','label$'"
		endif
	endfor

	appendFileLine: csvFile$, "'nameToSave$','start','five','content$'"
	removeObject: currentTextGrid
	removeObject: "Sound " + nameToSave$
	removeObject: "Sound " + soundName$
	removeObject: wavList
	selectObject: speakList
endfor

removeObject: speakList
```

