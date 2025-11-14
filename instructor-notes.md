---
title: Instructor Notes
---

## Datasets
Throughout this workshop, we use four primary data sets:

The Check-In Dataset is based on a 2018 state election. The data set tracks 
check-in times and lengths at ballot scanners across various locations, as well 
as the precinct that each device belongs to. Additionally, all identifiable 
information has been anonymized using pseudo-anonymization.
The direct download link for the data file is: [https://raw.githubusercontent.com/EngineeringForDemocracy/data-empowerment/main/episodes/data/checkin_data.csv](https://raw.githubusercontent.com/EngineeringForDemocracy/data-empowerment/main/episodes/data/checkin_data.csv)
The direct download link for the sampled data file (for ggplot2) is: [https://raw.githubusercontent.com/EngineeringForDemocracy/data-empowerment/main/episodes/data/checkin_sample_plotting.csv](https://raw.githubusercontent.com/EngineeringForDemocracy/data-empowerment/main/episodes/data/checkin_sample_plotting.csv)

The Messy Dataset is based on a real-life election example and tracks the 
amount of time individuals took to check-in to a voting location. For check-ins 
that took a longer amount of time than average, an explanation is given.
The direct download link for the data file is: [https://raw.githubusercontent.com/EngineeringForDemocracy/data-empowerment/main/episodes/data/messy_data.csv](https://raw.githubusercontent.com/EngineeringForDemocracy/data-empowerment/main/episodes/data/messy_data.csv)

The GoT Dataset is a fictional data set based on the Game of Thrones universe. 
It consists of graphing polygons and voting data representing the percentage of 
voters that voted for Jon Snow or Daenerys Targaryen.
The direct download link for the CSV file is: [https://raw.githubusercontent.com/EngineeringForDemocracy/data-empowerment/main/episodes/data/voting_GoT.csv](https://raw.githubusercontent.com/EngineeringForDemocracy/data-empowerment/main/episodes/data/voting_GoT.csv)
The direct download link for the GeoJSON file is: [https://raw.githubusercontent.com/EngineeringForDemocracy/data-empowerment/main/episodes/data/polygons_GoT.json](https://raw.githubusercontent.com/EngineeringForDemocracy/data-empowerment/main/episodes/data/polygons_GoT.json)

The Check-In Snippet is a JSON representation of a fictional data set based on the 
Anonymized Dataset. It includes information as to what precinct, polling location, 
and scanner was used, as well as the amount of arrivals, with the time of the 
first and last arrival.
The direct download link for the data file is: [https://raw.githubusercontent.com/EngineeringForDemocracy/data-empowerment/main/episodes/data/checkin_snippet.json](https://raw.githubusercontent.com/EngineeringForDemocracy/data-empowerment/main/episodes/data/checkin_snippet.json)



## Lesson Plans
The lesson contains significantly more material than can be taught in a day. 
Instructors should pick an appropriate subset of episodes to use in a standard 
one day course.

Suggested path for a half-day course:
- Before we Start
- Introduction to R
- Starting with Data
- Data Wranging with dplyr

Suggested path for a full-day course:
- Before we Start
- Introduction to R
- Starting with Data
- Data Wrangling with tidyr (OPTIONAL)
- Data Visualization with ggplot2

For a two-day workshop, it may be possible to cover all of the episodes. Feedback 
from the community on successful lesson plans is always appreciated!



## Technical Tips and Tricks
- Show learners how to use the 'zoom' button to blow up graphs without constantly 
  resizing RStudio windows.
  
- Sometimes a package will not install. You can try a different CRAN mirror by 
  following the path: "Tools > Global Options > Packages > CRAN Mirror"
  Alternatively you can go to CRAN and download the package and install from a ZIP
  file, following the path: "Tools > Install Packages > set to 'from Zip/TAR'""
  
- It's often easier to ensure learners have all the needed packages installed at 
  once, rather than dealing with these issues over and over. See the "Setup Instructions"
  section on the homepage of the course website for package installation instructions.

- In regards to the `|` character on Spanish keyboards, the Spanish Mac keyboard 
  does not have a `|` key. This character can be created using:
  ```
  `alt` + `1`
  ```



## Other Resources
If you encounter a problem during a workshop, feel free to contact the maintainers 
by email or [open an issue](link to issues page).

For a more in-depth coverage of topics of the workshops, you may want to read 
"[R for Data Science](https://r4ds.had.co.nz/)" by Hadley Wickham and Garrett 
Grolemund.


