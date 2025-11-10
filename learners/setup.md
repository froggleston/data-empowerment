---
title: Setup
---

## Setup Instructions
First, it's important to understand that **R** and **RStudio** are two different 
programs that need to be downloaded and installed separately. R serves as the 
underlying statistical computing environment, but using R by itself is very 
difficult. TO simplify the experience of using R, RStudio (a graphical integrated 
development environment, or IDE) is used, as it is much simpler and more interactive. 
However, before you install RStudio, you still need to install R, as it depends 
on the underlying processing of R to run. Additionally, there is no need to manually 
run R, as RStudio will automatically start it and run it in the background.

After ensuring the installation of both programs, you will need to install the 
**`tidyverse`** and **`here`** packages from within RStudio. The **`tidyverse`** 
package provides a powerful collection of data science tools within **R** (you can 
see the see the [**`tidyverse`** website](https://tidyverse.tidyverse.org) for 
more details), and the **`here`** package simplifies file access.

Follow the instructions below to install/update R and RStudio for your operating 
system, and then follow the instructions at the end to install **`tidyverse`** and 
**`here`**.

::: tab

### Windows

#### If R and RStudio are not installed on your device:
- Go to the [RStudio download page](https://posit.co/download/rstudio-desktop/).
- Under *1: Install R*, select **Download and Install R**.
- After redirection, under *Download and Install R*, select **Download R for Windows**, 
  then **base**, and finally, **Download R-x.y.z for Windows** (where x, y, and 
  z represent version numbers).
- Run the `.exe` file that was downloaded, and follow all prompts to install R.
- Return to the [RStudio download page](https://posit.co/download/rstudio-desktop/).
- Under *2. Install RStudio*, select **Download RStudio Desktop for Windows**.
- Run the `.exe` file that was downloaded, and follow all prompts to install RStudio.
- After installation, open RStudio to ensure it works and no error messages appear.

#### If you already have R and RStudio installed:
- Open RStudio, and click on "Help" > "Check for Updates". If a new version is 
  available, either follow the prompts or quit RStudio and follow the instructions 
  above to download the latest version of RStudio.
- After ensuring RStudio is running the latest version, re-open RStudio.
- The first thing that appears in the console indicates the version of R you are 
  running. Alternatively, you can type `sessionInfo()` into the console, which will 
  also display the version of R currently running.
- Go to the [CRAN website](https://cran.r-project.org/bin/windows/base/) and check
  whether a more recent version is available. If so, you can update R using
  the `installr` package, by running:
  ```r
  if( !("installr" %in% installed.packages()) ){install.packages("installr")}
  installr::updateR(TRUE)
  ```


### macOS

#### If R and RStudio are not installed on your device:
- Go to the [RStudio download page](https://posit.co/download/rstudio-desktop/).
- Under *1: Install R*, select **Download and Install R**.
- After redirection, under *Download and Install R*, select **Download R for macOS**. 
  Then, select **R-x.y.z-arm64.pkg** if you have an Apple Silicon Mac, or **R-x.y.z-x86_64.pkg** 
  if you have an older Intel Mac (where x, y, and z represent version numbers).
- Run the `.pkg` file that was downloaded, and follow all prompts to install R.
- Return to the [RStudio download page](https://posit.co/download/rstudio-desktop/).
- If you have a computer that runs MacOS 13+, under *2. Install RStudio*, select 
  **Download RStudio Desktop for MacOS 13+**.
- If you have a computer that runs MacOS 12, under *2. Install RStudio*, select 
  **download a previous version**. After redirection, select **View supported installers and guides**. 
  Then, under *2024.09.1*, click the **Installers** drop-down, and select **Installers**.
  Finally, in the second row (labelled "MacOS 12+"), select **RStudio-2024.09.1-394.dmg**.
- Run the `.dmg` file that was downloaded, and follow all prompts to install RStudio.
- Additionally, it is also a good idea to install [XQuartz](https://www.xquartz.org/), 
  as it is needed by some packages.
- After installation, open RStudio to ensure it works and no error messages appear.

#### If you already have R and RStudio installed:
- Open RStudio, and click on "Help" > "Check for Updates". If a new version is 
  available, either follow the prompts or quit RStudio and follow the instructions 
  above to download the latest version of RStudio.
- After ensuring RStudio is running the latest version, re-open RStudio.
- The first thing that appears in the console indicates the version of R you are 
  running. Alternatively, you can type `sessionInfo()` into the console, which will 
  also display the version of R currently running.
- Go to the [CRAN website](https://cran.r-project.org/bin/macosx/) and check
  whether a more recent version is available. If so, download and install R using 
  the instructions above.


### Linux

#### If R and RStudio are not installed on your device:
- Follow the instructions for your distribution from [CRAN](https://cloud.r-project.org/bin/linux), 
  as they provide information to get the most recent version of R for common 
  distributions. For most distributions, you *could* use your package manager 
  (e.g., for Debian/Ubuntu run `sudo apt-get install r-base`, and for Fedora 
  `sudo yum install R`), but we don't recommend this approach as the versions 
  provided by this approach are usually out of date. Regardless of your method, 
  ensure you download R version 3.2 (or higher).
- Go to the [RStudio download page](https://posit.co/download/rstudio-desktop/).
- Under *All Installers and Tarballs*, select the version that matches your 
  distribution, and install it with your preferred method (e.g., with Debian/Ubuntu 
  `sudo dpkg -i rstudio-x.yy.zzz-amd64.deb` at the terminal).
- After installation, open RStudio to ensure it works and no error messages appear.

#### If you already have R and RStudio installed:
- Open RStudio, and click on "Help" > "Check for Updates". If a new version is 
  available, either follow the prompts or quit RStudio and follow the instructions 
  above to download the latest version of RStudio.
- After ensuring RStudio is running the latest version, re-open RStudio.
- The first thing that appears in the console indicates the version of R you are 
  running. Alternatively, you can type `sessionInfo()` into the console, which will 
  also display the version of R currently running.
- Go to the [CRAN website](https://cloud.r-project.org/bin/linux) and check
  whether a more recent version is available. If so, download and install R using 
  the instructions above.

::: 

## After installing R and RStudio:
- If you are running Linux, before installing the `tidyverse` package, **Ubuntu** 
  (and related) users may need to install the following dependencies: 
  `libcurl4-openssl-dev libssl-dev libxml2-dev` (e.g. `sudo apt install libcurl4-openssl-dev libssl-dev libxml2-dev`).
- To install the **`tidyverse`** package, in the console, type `install.packages("tidyverse")`, 
  followed by the enter key.
- To install the **`here`** package, in the console, type `install.packages("here")`, 
  followed by the enter key.
- To ensure both packages are installed, select *Packages* on the right, under 
  **User Library**, check that **`tidyverse`** and **`here`** are listed.



## Datasets
Throughout this workshop, we use four primary data sets:

We recommend that you download a [single zip file](https://github.com/EngineeringForDemocracy/data-empowerment/raw/refs/heads/main/episodes/data/data_file.zip) 
with all of the files and then unzip it. Move the unzipped folder to somewhere on your system that you can find (e.g. Desktop or Documents). 

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
