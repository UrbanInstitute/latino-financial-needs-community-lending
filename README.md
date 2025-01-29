# How Latino-Serving CDFIs Can Shape a Thriving American Economy

As the Latino population in the United States continues to grow in the coming decades, community development financial institutions will play a crucial role in ensuring these communities have access to affordable and effective financial services.

This apps project is X-charts style with sections of text scrolling past individual toggle charts, where the charts are made with LayerCake and the tables are built in Datawrapper. 

## Project Details

The production URL is [https://www.urban.org/stories/latino-financial-needs-community-lending](https://www.urban.org/stories/latino-financial-needs-community-lending)

The apps URL is [https://apps.urban.org/features/latino-financial-needs-community-lending/](https://apps.urban.org/features/latino-financial-needs-community-lending/)

Design: Brittney Spinner

Dataviz and Development: Rachel Marconi

Writing: Wes Jenkins

## Data

The data files in this project are stored in the static folder and there is one file per chart set, including all toggles and buttons to subset. Chart 4 data is instead a JSON listing the Datawrapper IDs assigned to each subset.

Site content is stored in an ArchieML file in the data directory and is imported with the rollup-plugin-archieml npm package. 

## Page layout

Everything is laid out in the +page.svelte file. Blocks of content are processed from the archie data and built based on the type of block, so the intro has a separate processing from a scrolly block of chart/text. Each Chart component is specific to its presentation and includes all tab groups and toggles needed for that chart. The Chart component passes necessary info and subset data to LayerCake, based on highly tweaked Layercake Bar and Column components to allow animations between subsets and variable labeling styles. 

## Updating

If it comes to updating this project, ensure that all data matches the format in current data files. Some columns rarely have special treatement in the code; watch for column name changes. 
