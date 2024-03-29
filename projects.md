---
layout: page
title: Assignments
---

## Assignment 9: Final Paper and Code

The final paper is the main deliverable for this class. The expectation is a paper whose quality is consistent with a technical report or academic workshop paper. Undergraduates should aim for 6 pages and graduate students for 8 to 10. As compared to the full draft, this version should demonstrate a significant amount of polish. Please check for consistency in style, correct grammar and spelling, and proper LaTeX formatting. Proof read your paper several times, get input from others, submit when you're happy with the product and can't think of what else you might improve. 

All code, LaTeX source, and figures are a deliverable for this class as well. Your github repository should be sensibly organized, uncluttered with cruft, and well documented. A README.md file is a must, and ideally all other code will contain top-of-file documentation and inline documentation as appropriate. An outsider should be able to understand and entirely reproduce your work from what is assembled in this repository.

The majority of papers from this class will be assembled in a technical report and published throught CU CS Department and the CU Library. We withhold the right to withhold a paper if it does not meet the quality expectations for the class. If you would rather not have your paper published in this manner, please let us know.

## Assignment 8: Final Presentation

The final presentation is a key outcome of the class. The goal is to use 10 minutes to present the work you've done this semester to your instructors and peers. You'll be graded on the quality of the slides and the thoroughness of the presentation. The expectation is approximately 8 slides but you may consider giving a practice talk to a friend (or your cat) to see how long it takes and adjust accordingly. These slides should roughly follow the outline of your paper: introduce the concept and hypothesis, motivate the importance of the work, discuss the data, preprocessing and ETL, methods, and finally results and a discussion of their importance. Slides should be colorful, visually interesting, and not overburdoned with text.

## Assignment 7: Full Draft Paper and Slides

No surprises here -- you've written your data, methods and results sections -- in this assignment you'll write the remaining sections, put in final results and tie the whole document together into a presentable story. Undergraduate student papers should be about 6 pages, includes several figures and several citations. Graduate student papers should be about 8 pages, include at least half a dozen citations including a short summary of related work, and have several high quality figures. There's no upper limit for page length but it's unlikely there's cause for a paper that's more than 12 pages unless you have several full-page visualizations. Please submit your paper via github as a PDF.

For the draft slides, the goal is to prepare for a 10 minute talk, which means about 8 slides. These 8 slides should roughly follow the outline of your paper: introduce the concept and hypothesis, motivate the importance of the work, discuss the data, preprocessing and ETL, methods, and finally results and a discussion of their importance. Slides should be colorful, visually interesting, and not overburdoned with text. For this assignment, these slides may still be rough but should have the general shape they will eventually take and key figures included. Include these slides as a PDF in your github repository as well.

## Assignment 6: Results

For Assignment 6, you'll add on to the LaTeX template provided as part of the repository template to write the results section of your paper, building off of assignment 5. The results section should state and discuss findings from your methods section as well as supporting graphs and figures. If you'd like to you could also include a discussion to review how your research could be applied, and look at your work as a whole as well as next steps for this work or additional research that could compliment this work. If you decide not to include a discussion in this assignment, you will need to include it for the full draft. This should follow the general format, tone, and structure as the papers we've read in class. 

As in Assignment 5:

If you need to add citations, you can use either \bibitem commands in the references section or use BibTex and a tool like JabRef, Zotero or Mendeley to manage references. If you go the route of bibitem commands (as is shown in the template example), make sure the references are consistently formatted. If you use BibTex, make sure to commit your .bib file to the repository.

You can use command line tools (e.g., pdflatex) to compile the PDF or an online tool like Overleaf or Sharelatex, however it's important that you commit (1) the .tex source file (2) a compiled PDF (3) any figures and (4) the .bib file if relevant.

You'll be graded on the quality of the technical writing and description, which should be complete and use a professional tone similar to what would be found in an academic publication. 

## Assignment 5: Written Methods and Data

For Assignment 5, you'll use the LaTeX template provided as part of the repository template to write the data and methods section of your paper. The data section should describe all the data being used, how it was obtained and processed and give relevant citations. The methods section should describe the methods you intend to use, how you determine whether the hypothesis has been validated, similar to the papers we've been reading as part of the class. If you want, you're also welcome to fill in details in the introduction, abstract, or results, but those changes are not required for this assignment.

Citations will be required for data sources, or to explain/justify the use of particular methods. You can use either \bibitem commands in the references section or use BibTex and a tool like JabRef, Zotero or Mendeley to manage references. If you go the route of bibitem commands (as is shown in the template example), make sure the references are consistently formatted. If you use BibTex, make sure to commit your .bib file to the repository.

You can use command line tools (e.g., pdflatex) to compile the PDF or an online tool like Overleaf or Sharelatex, however it's important that you commit (1) the .tex source file (2) a compiled PDF (3) any figures and (4) the .bib file if relevant.

You'll be graded on the quality of the technical writing and description, which should be complete and use a professional tone similar to what would be found in an academic publication. Lastly, please give your paper a title (this can change later) and put your name where the author's name goes. You can leave sections you haven't written (e.g., abstract or methods) with the lorem ipsum text.

## Assignment 4: Draft Results

For assignment 4, you will push a version of your repository with initial results and code to produce those results. These results should follow logically from the methods you described in assignment 2 and should be easy to find for someone reviewing your repository. While your results may not be complete at this point, there should be significant progress and it should be clear to an outside reviewer what you've done already and what is left to do.

All supporting code including ETL and analysis should be similarly readable and sensible to an outside reviewer. To help orient those reviewing your submission, create a README.md file in your main repository directory to explain where to find the results, how to reproduce them, assumptions you've made, and how the repository is organized. 

For documentation of code, follow a consistent practice. For instance, python code would ideally be [PEP-8](https://www.python.org/dev/peps/pep-0008/) and stand-alone files (including modules and scripts) might use [Google-style docstrings](https://sphinxcontrib-napoleon.readthedocs.io/en/latest/example_google.html). For notebooks, plots and code should be supplemented with top-of file explanations and readable, more like a laboratory notebook than a scratch pad. For R, the [tidyverse style guide](https://style.tidyverse.org/documentation.html) is a good reference.

## Assignment 3: One Slide Project Pitch

For assignment 3, you'll prepare a 1-slide presentation about your project topic and present it to the class. 

There are examples of prior students' one slide summary in [The Lecture 5 Slide Deck](https://docs.google.com/presentation/d/1uMkUP0nezLKsI5AvFMoTfQ96WywtTgEvxPx4gKjRfVE/edit#slide=id.g63928dafca_0_0).

Create your 1-slide summary in Google Slides, then send it to Caleb and/or Lindy (make sure it is shared so we can view it).

During your presentation, you'll have 5 minutes during which you'll describe the topic, approach, and methods and take 1-2 questions from the class.

## Assignment 2: Methods Plan and Data Prep

For assignment 2 you'll submit a more detailed plan for your methods and initial code to obtain, clean and prepare data for initial exploratory data analysis.

For the methods plan, you should state the format of the data (likely a table), your plans for exploratory data analysis (EDA) and initial visualization. If there are specific research questions you aim to test, provide hypotheses along with the methods you intend to use to test them. For instance:

```
The data table I've prepared has these fields:

  * Year
  * Number of pikas observed
  * Number of observer/days
  * Pikas per observer/day
  * Average annual rainfall
  * Average day time high
  * Drought Severity Index
  
There are 702 observations that I've grouped into annualized bins spanning the last 10 years. I summed the data for each year and have also calculated a pika count normalized to the number of citizen observers to attempt to account for sampling bias.

EDA: To understand the trend of pika population size as and possible correlating factors, I'll plot
pika population over time. I'll also make a map of the data collected to understand the spatial distribution and potential for bias.

Hypothesis 1: The population size of pikas is declining in the last 20 years.
Proposed Methods: I will use a linear regression between year and measured FRPP pika population size to assess whether the slope of the trend is negative or positive and whether the year contributes significantly to this slope. If the slope is negative and the relationship significant, I'll affirm the hypothesis. In addition, I'll calculate a pearson's correlation coefficient and plot the relationship over time for visual inspection.

Hypothesis 2: The population size is correlated with weather trends, and e.g., there are fewer pikas when the weather is warmer and there is less rain.
Proposed Methods: Similar to the above, I will use a (multiple) linear regression to determine the relationship between Pika population size and the weather variables. In the initial model, I will exclude 'year' to see if these variables are independently correlated with the pika population. A complete predictive model that includes year will also be fitted.

All analyses will be performed with Python and ScikitLearn. I plan to use Pandas for data pre-processing, and Bokeh for visualization.
```

For the code, commit your ETL code and process to your Git Lab repository within the project directory. You can structure this however you like, but it should be navigable and have sufficient documentation for an outside code reviewer to see and understand what you're doing and potentially run it themselves.


## Assignment 1: Project Ideas & Research Questions

In this first checkpoint on your progress, you need to come up with one or more serious project ideas along with research questions that can be explored in the scope of this class. 

To complete this assignment:

  * Create a new file in your repo named 'ideas.md' in the project directory
  * Put in here a description of your project idea(s) and the research questions numbered like Q1, Q2, Q3, etc.
  * If possible, describe where and how you will get data in support of these research questions
  * Identify any barriers or risks in getting the data you need or completing the analysis 

For instance:

```
I would like to understand how weather and climate trends influence the population size of Pikas in the Colorado front range.

My research questions are:

Q1: Is the population size of pikas declining or increasing in the last 20 years?
Q2: Is the observed population size of pikas correlated with rainfall, average temperature, or drought?

The data I hope to use is collected by the Front Range Pika Projact (FRPP) at http://www.pikapartners.org/cwis438/websites/FRPP/Home.php?WebSiteID=18

I plan to use historic weather data from National Weather Service stations.

A potential risk in doing this analysis, is that it is not clear how much data on pikas is available or for how long. I have contacted FPPP to find out how much data they have and if they will share it with me.

If I'm unable to obtain the data I need for this study, I'm also interested in ...
```

During your 1-1 meeting with Caleb and Lindy, you'll discuss these ideas, research questions, data and the risks you see.


## Assignment 0: Fork Repo

At each project checkpoint students will provide a link to their working github.com repository. This repository can be private or public but must be shared with the instructors and GSS. The git repo must follow the basic template provided, which can be forked using the link below:

[https://git.cs.colorado.edu/phillict/csci-4831-7000](https://git.cs.colorado.edu/phillict/csci-4831-7000)

In particular, this must contain all analysis code, data (if applicable and non-sensitive), research paper draft (LaTeX format and PDF), and readings writeups (in markdown format).

The 'grademe' branch will be used for grading. If the grademe branch doesn't exist, the 'master' branch will be used instead.

To complete this assignment:

  * Login to git.cs.colorado.edu
  * Fork the repository linked above (click 'Fork')
  * Go to the settings of your new forked repo
  * Click 'members' and add Caleb, Lindy, Anirudh, and Mike
  



