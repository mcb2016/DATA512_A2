{
 "cells": [
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "# A2: Bias in Data\n",
    "\n",
    "## Project Goal\n",
    "\n",
    "The goal of the project is to provide a reproducible project that serves to construct, analyze the Wikipedia articles dataset to explore the concept of bias in data. Link to the problem statement\n",
    "\n",
    "## Data Source\n",
    "\n",
    "Data from the following sources were used for this analysis:\n",
    "\n",
    "2020 World Population Datasheet by Population Research Bureau. This datset contains provides the 2020 population estimates for countries across the world. The data was prepared by the Population Research Bureau. To ensure reproducibility and convenience, the data has been saved to dropbox [link](https://www.prb.org/international/indicator/population/table/). The data is also available in this repo as a csv file: raw_data/WPDS_2020_data.csv.\n",
    "For more details about the data and estimation methods please visit the PRB website.\n",
    "\n",
    "Politicians by Country from the English-language Wikipedia This data was downloaded from Figshare [link](https://figshare.com/articles/dataset/Untitled_Item/5513449). It contains a subset of metadata info of articles on politicians published in the English version of Wikipedia. The data was retrieved using Wikimedia API and stored on Figshare. It is also made available here as raw_data/page_data.csv.\n",
    "\n",
    "Article Quality estimation using ORES A measure of quality of each of the articles is obtained using the ORES (\"Objective Revision Evaluation Service\") machine learning service. Given an article revision id, the service will return its estimated quality as one of the following classes:\n",
    "\n",
    "- FA - Featured article\n",
    "- GA - Good article\n",
    "- B - B-class article\n",
    "- C - C-class article\n",
    "- Start - Start-class article\n",
    "- Stub - Stub-class article\n",
    "\n",
    "Here FA and GA are considered to measure of high quality. For more information about the scores, visit this link.\n",
    "For this particular project we are using the wp10 machine learning model to predict the article quality.\n",
    "\n",
    "[ORES Documentation](https://ores.wikimedia.org/v3/#!/scoring/get_v3_scores_context_revid_model)\n",
    "\n",
    "[ORES Endpoint](https://ores.wikimedia.org/v3/scores/{project}/?models={model}&revids={revids})\n",
    "\n",
    "The estimated article by country results are stored locally as final_data/wp_wpds_politicians_by_country.csv."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Data\n",
    "\n",
    "All data files that are used for the analysis are stored in the raw_data and final_data folders. Some of them are the raw data that were downloaded from the source, whereas others are intermediate files saved during processing to for convenience and to ensure reproducibility.\n",
    "\n",
    "WPDS_2020_data.csv (Population raw data) : Population data downloaded from dropbox server (link provided above). \n",
    "\n",
    "page_data.csv (Article raw data) : Data of articles about politicians in Wikipedia English. The data was collected using the Wikimedia APIs and stored on Figshare (link given above). \n",
    "\n",
    "wp_wpds_politicians_by_country.csv (Final analytical dataset) : This is the final dataset that was used for the analysis. It was obtained by merging the population dataset with the article dataset. Since several locations in the article dataset are not present in the population dataset, we lost about approx. 500 articles while merging.\n",
    "\n",
    "wp_wpds_countries-no_match.csv: This dataset is composed of the countries in the WPDS_2020_data.csv that failed to merge with the page_data.csv to create the final dataset."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Code\n",
    "\n",
    "All the code involved in this project are saved as a Jupyter notebook : A2_code.ipynb.\n",
    "\n",
    "The notebook was divided into 3 sections:\n",
    "\n",
    "Getting Article and Population data.\n",
    "Getting the article scores using ORES.\n",
    "Creating a final dataset composing of both the Article and Population.\n",
    "\n",
    "Analysis of articles by country and region. This purpose of this step was to explore the article to check for possible biases in data and also to answer the following questions:\n",
    "- 10 highest-ranked countries in terms of number of politician articles as a proportion of country population.\n",
    "- 10 lowest-ranked countries in terms of number of politician articles as a proportion of country population.\n",
    "- 10 highest-ranked countries in terms of number of GA and FA-quality articles as a proportion of all articles about politicians from that country.\n",
    "- 10 lowest-ranked countries in terms of number of GA and FA-quality articles as a proportion of all articles about politicians from that country.\n",
    "- Ranking of geographic regions (in descending order) in terms of the total count of politician articles from countries in each region as a proportion of total regional population.\n",
    "- Ranking of geographic regions (in descending order) in terms of the relative proportion of politician articles from countries in each region that are of GA and FA-quality\n",
    "\n",
    "The notebook runs on Python 3.6 and the following packages are needed to run:\n",
    "\n",
    "- json\n",
    "- string\n",
    "- numpy\n",
    "- pandas\n",
    "- requests"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Reflection\n",
    "\n",
    "The exercise was an excellent illustration regarding the complexities involved in dealing with data from multiple sources. Going into this project, I knew that because several countries present in the article data were not present in the population data, those countries wouldn't be included in our final dataset and subsequent analyses. There were also several confusing aspects such as oveseas territories. Because these regions were excluded from the final dataset and its subsequent analyses, we've already introduced bias in our observations in that we don't account for these missing countries and territories. This is especially important here because even the presence of a single wikipedia article can change the results we observe and which countries/territories are included in our analyses. If we were to rework this assignment, I recommend classifying the missing countries/territories at the subregional level then performing all of the analyses at the subregion level, rather than the country level, for more accurate representation and to reduce bias. \n",
    "\n",
    "The politicians themselves introduce bias in our data. Each country has several hundred politicians on average, yet not all of them would necessarily be added on Wikipedia; it's more likely for a politician to be added on Wikipedia if that person was really popular or controversial in that country and internationally. This could skew our results towards countries that may have a controversial history, such as North Korea. Articles on controversial personalities would most likely be subjected to several reviews before being published. As we can see in our results, North Korea is on the top of the list of countries with the highest proportion of high quality articles. \n",
    "\n",
    "Another source of bias is related to the ORES service/API. Upon further research into the algorithm, I learned that the article quality measure is actually based on the structure of the article, not on its content. Unless the editors of the articles are seasoned experts, I doubt whether everyone who had added these articles would have taken care to fulfill all the criteria to qualify as a high quality article. This could bias the data towards countries with editors who are more familiar with the scoring criteria (i.e. editors from more developed countries).\n",
    "\n",
    "Taking these biases into account, it seems that articles surrounding a controversial or popular political figures and are at least moderately structured tend to be featured the most on wikipedia. For example, hardly anyone outside of Bosnia knows about its president, but you can find dozens of articles surrounding former president Trump or Angela Merkel from Germany on wikipedia. This also coincides with Wikipedia's bias towards articles of politicians in more developed, western countries compared to second or third wold nations. One way for scientists to reduce this bias is to provide better resources/training for writing and structuring articles for the editors in these lesser developed countries. Doing so will enable them to publish higher quality articles that will be more likely to gain traction amongst readers on wikipedia. "
   ]
  }
 ],
 "metadata": {
  "kernelspec": {
   "display_name": "Python 3",
   "language": "python",
   "name": "python3"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 3
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython3",
   "version": "3.7.4"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 2
}
