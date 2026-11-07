---
name: UFO Sightings with Altair
tools: [Python, Altair, vega-lite]
image: assets/pngs/ufo_shapes.png
description: Two interactive plots of about 80,000 UFO reports, made with Altair.
custom_js:
  - vega.min
  - vega-lite.min
  - vega-embed.min
  - justcharts
---

# UFO sightings, 1950-2014

For this homework I used the UFO sightings dataset which has about 80,000 reports that each come with a date, a location, a written description and the shape the person said they saw. I made 2 plots in Altair. The first looks at how the shapes people report have changed over the years, and the second looks at what time of year and time of day people actually see them.

## What shape was it?

<vegachart schema-url="{{ site.baseurl }}/assets/json/ufo_shapes_over_time.json" style="width: 100%"></vegachart>

So what do people say they saw? This plot shows the share of reports for each shape in 5-year periods from 1950 to 2014. The x axis is the start of each period and it's encoded as temporal (T). The y axis is the percent of that period's reports, encoded as quantitative (Q) and stacked so every period adds up to 100. Color is the shape, which is nominal (N), and I went with the tableau10 categorical scheme because the shapes don't have any order to them, so I wanted 7 colors that are easy to tell apart, and a sequential scheme would make it look like light is somehow "less" than disk. On the Python side, the file doesn't have a header, so I gave it column names and read the date column in as timestamps with parse_dates. I dropped the 1,932 reports with no shape listed and the handful from before 1950. There are 29 shapes in the data and most of them barely show up, so I kept 6 that have a clear trend (light, fireball, circle, triangle, disk and cigar) and put everything else, including the dataset's own "other" and "unknown," into one "all other shapes" group. Single years were way too noisy; before the mid-90s there are only a couple hundred reports a year. I grouped the years into 5-year periods, counted reports per shape with a groupby and unstack, and divided by each period's total to get percents. Honestly the trend is pretty wild. Disk, the classic flying saucer, goes from 36% of reports in 1950-54 to under 4% in 2010-14, while light goes from 6% to 22% and fireball jumps to 12% in the last period. The last period only runs through May 2014 since that's where the data stops.

## When do people see them?

<vegachart schema-url="{{ site.baseurl }}/assets/json/ufo_time_heatmap.json" style="width: 100%"></vegachart>

So when do these sightings actually happen? The second plot is a heatmap with the hour of the day on the x axis and the month on the y axis. Both are encoded as ordinal (O) since they come in a set order and I wanted every hour and month to get its own block. I kept the months as numbers (1 is January) so they stay in calendar order. The color is the total number of reports in each block (quantitative, Q) using the yelloworangered sequential scheme. I picked a sequential scheme because the count only goes one way, from a few reports to a lot, and with the low end being a pale yellow the quiet daytime hours fade into the page while the busy hours show up dark red. For the data, I pulled the month and hour out of the timestamps and counted reports for every month and hour, split by shape group. Then I added an "all shapes" column with the total and melted it back into a long table, which comes out to about 2,300 rows compared to 78,000 and keeps the JSON file under 200 KB. The pattern is super consistent. Over half the reports (56%) come in between 7pm and midnight, and the busiest block is 10pm in July with 1,990 reports. What I didn't expect was the peak hour moving through the year. In November and December the busiest hour is 7pm, but from June through August it's 10pm, which I think just lines up with when it gets dark.

## Interactivity

Both plots are interactive. On the shape plot you can hover over a band and every other shape fades out (a selection_point on mousemove with a condition on opacity), and double-clicking brings everything back. There's also a tooltip with the exact percent and the years for that period. I added this because in a stacked area chart only the bottom band has a flat baseline, so following something like disk through the middle is hard without it. The heatmap has a dropdown that starts on "all shapes" and filters it down to one shape group using a selection_point bound to binding_select and a transform_filter. The color scale resets for whatever shape you pick, so a small group like cigar (about 2,000 reports) still shows its own pattern. Try fireball. July pulls even further ahead of every other month, and the notebook shows 193 fireball reports on July 4th alone, so my guess is a lot of those are fireworks.

<!-- these are written in a combo of html and liquid -->

<div class="left">
{% include elements/button.html link="https://raw.githubusercontent.com/UIUC-iSchool-DataViz/is445_data/main/ufo-scrubbed-geocoded-time-standardized-00.csv" text="The Data" %}
</div>

<div class="right">
{% include elements/button.html link="https://github.com/AirlessEA/AirlessEA.github.io/blob/main/python_notebooks/ufo_altair_hw.ipynb" text="The Analysis" %}
</div>
