# The Report

As described on [LearnIT](https://learnit.itu.dk/local/coursebase/view.php?ciid=1896), the exam for this course is a _D22-type_ exam, i.e., submission of written work for groups with following oral exam.
The exam has an internal censor and the grade is Pass/Fail.


## Requirements

All of the following sections are requirements.
That is, you have to do something about each of them.

- [1.) Assure Information Correctness](#1-assure-information-correctness)
- [2.) Polish Project Repositories and Documentation](#2-polish-project-repositories-and-documentation)
  - [2.1.) Create a `.mailmap` file in the root of your repositories](#21-create-a-mailmap-file-in-the-root-of-your-repositories)
  - [2.2.) Update the main readme file](#22-update-the-main-readme-file)
- [3.) Write a Report](#3-write-a-report)
  - [3.1.) Formal Requirements](#31-formal-requirements)
  - [3.2) What to include in the report?](#32-what-to-include-in-the-report)
    - [System's Perspective](#systems-perspective)
    - [Process' perspective](#process-perspective)
    - [Reflection Perspective](#reflection-perspective)
    - [Use of Generative AI](#use-of-generative-ai)
  - [3.3) How to hand-in?](#33-how-to-hand-in)

------------------------------------------------------------------------------------------------------------------------

### 1.) Assure Information Correctness

- Make sure that the [information about group members](https://ituniversity.sharepoint.com/:x:/r/sites/2026MScDevOpsSoftwareEvolutionandSoftwareMaintenance/Shared%20Documents/General/Groups.xlsx?d=w75f28a88ccae434ba16465ed310e9192&csf=1&web=1&e=B0Uqbp) is correct.
- Make sure that you correctly registered all relevant repositories and URLs in [`repositories.py`](https://github.com/itu-devops/MSc_lecture_notes/blob/master/repositories.py).
- Make sure that the URLs to your monitoring and logging dashboards are correct in [`misc_urls.py`](https://github.com/itu-devops/MSc_lecture_notes/blob/master/misc_urls.py).


### 2.) Polish Project Repositories and Documentation

#### 2.1.) Create a `.mailmap` file in the root of your repositories

[Git mailmap](https://git-scm.com/docs/gitmailmap) is a tool to map author's names or e-mail addresses to single proper values.

Likely, you worked on the project from various computers, all with different Git configurations.
That leads to a Git history in which single authors appear under various names with various email addresses in a `git log`.
You can double check the issue, e.g., by running `git shortlog -sne` in the root of your repository.
Likely, each of your group members appears more than once.
For example, after cloning group b's repository and printing commit frequencies per author, one would see the following output.
(Note, I only chose group b for illustration. The issue is the same for all groups in this course.)

```bash
git clone https://github.com/DevOps26-RE/DevOps26_RE_minitwit.git
cd minitwit_project/
git shortlog -sne
   105  Aiting Lee <aile@itu.dk>
    61  eliappo <237402327+eliappo@users.noreply.github.com>
    45  jgaj <jgaj@itu.dk>
    33  Aiting Lee <87257634+AitingLee@users.noreply.github.com>
    20  alimacunha <amanda.lima.cunha@alumni.usp.br>
    11  Johannes Hackl <johannes.hackl@live.com>
     5  Jakub1996PL <45341106+Jakub1996PL@users.noreply.github.com>
     3  Elias Poggi <237402327+eliappo@users.noreply.github.com>
     3  JohannesHackl <johannes.hackl@live.com>
     2  Aiting Lee <michelle723@gmail.com>
     1  Elias Poggi <elias.illeris@pm.me>
     1  Johannes Alexander Hackl <131956206+JohannesHackl@users.noreply.github.com>
     1  JohannesHackl <131956206+JohannesHackl@users.noreply.github.com>
     1  github-actions[bot] <github-actions[bot]@users.noreply.github.com>
```

For example, one can see that Elias appears as three different authors and Johannes as four.
That is a problem when attributing work to authors.
With a `.mailmap` file, you can "clean up" these different authors and map each of them to a proper author.
For example, to map all different "versions" of Elias to one proper author `Elias <elpo@itu.dk>` and all "versions" of Johannes to one proper author `Johannes <jhac@itu.dk>`, one could create the following `.mailmap` file:

```bash
echo "Elias <elpo@itu.dk> eliappo <237402327+eliappo@users.noreply.github.com>" >> .mailmap
echo "Elias <elpo@itu.dk> Elias Poggi <237402327+eliappo@users.noreply.github.com>" >> .mailmap
echo "Elias <elpo@itu.dk> Elias Poggi <elias.illeris@pm.me>" >> .mailmap
echo "Johannes <jhac@itu.dk> Johannes Hackl <johannes.hackl@live.com>" >> .mailmap
echo "Johannes <jhac@itu.dk> JohannesHackl <johannes.hackl@live.com>" >> .mailmap
echo "Johannes <jhac@itu.dk> Johannes Alexander Hackl <131956206+JohannesHackl@users.noreply.github.com>" >> .mailmap
echo "Johannes <jhac@itu.dk> JohannesHackl <131956206+JohannesHackl@users.noreply.github.com>" >> .mailmap
```

If in doubt about the `.mailmap` file syntax, read the [documentation](https://git-scm.com/docs/gitmailmap#_syntax).

To create a `.mailmap` file manually, you can list all authors and co-authors from the entire history of your repository and dump them to a draft `.mailmap` file as illustrated in the following script:

```bash
cd <your_repository>
git log --format='%an <%ae>' | sort | uniq > .mailmap
git log --format='%B' | grep "^Co-authored-by:" | cut -d" " -f2,3 | sort | uniq >> .mailmap
sort .mailmap -uo .mailmap
```

After doing that, you have to manually edit the automatically generated draft `.mailmap` file by adding a new first column with the proper author names and email addresses.
Please make sure that all proper authors (to be inserted first column) are of the form `Name <itu_id@itu.dk>`, e.g., `Elias <elpo@itu.dk>`, `Johannes <jhac@itu.dk>`, `Helge <ropf@itu.dk>`, `Mircea <mlun@itu.dk>` etc.

Note, in case you were/are using more than on repository, you have to create respective `.mailmap` files in each of them.

**OBS**: Since you added LLM tooling as co-authors, you have to map them in your `.mailmap` file too.
Please make the proper author of any LLM tool that you used and registered as co-author `LLM <none>`.
For example, for Group b's `.mailmap` file, it could be:

```bash
LLM <none> Gemini 3 Pro
```

#### 2.2.) Update the main readme file

Make sure that the main readme files in the root of your repositories are up-to-date and correct.
Likely, these are called `README.md`.
All information in there has to be correct.
They should contain a short description of the project, how to work with it, how to spin up a production system, how to contribute changes into a production, etc.

Check [here](https://www.makeareadme.com/#suggestions-for-a-good-readme) and [here](https://medium.com/@fulton_shaun/readme-rules-structure-style-and-pro-tips-faea5eb5d252) for recommendations on the structure and contents of readme files.


### 3.) Write a Report

The report to be handed in, is a document describing what you have done during the term in this course with regards to everything around your _ITU-MiniTwit_ systems.
Reports are written in some kind of markup language versioned in your repository.
They have to be handed-in in PDF format on WISEflow.

In the following, you find a description of all the practicalities concerning your report.


### 3.1.) Formal Requirements

Your final report should be maximum 2500 words long.
So, try to be brief and concise, but be sure to include all necessary information listed below.
Note, images do not count as words.

Your main project repository shall contain a directory called `report` containing either a single large or a set of linked files containing the sources of your report in markup languages, such as, ASCIIDOC, MarkDown, or LaTeX files.
All images appearing in the report should be collected in a directory called `images`, a sub-directory of `report`.

You convert your report sources to a PDF file via a build step in your CI chain.
For example, in case of ASCIIDOC, you may want to convert your report with [Asciidoctor](https://asciidoctor.org/docs/asciidoctor-pdf/) or [Pandoc](https://pandoc.org/).
In case of LaTeX, you may want to use a GitHub Action like [`xu-cheng/latex-action@v3`](https://github.com/marketplace/actions/github-action-for-latex).
The report has to be build as a single PDF file and has to be stored in directory `report/build`.
The naming convention for the PDF file is (as regular expression): `MSc_group_[a-z]\.pdf`.
That is, valid file names are, e.g., `MSc_group_a.pdf`, `MSc_group_e.pdf`, etc.

Make sure that you link all artifacts that you consider constitutional to your projects together with short descriptions of the linked artifacts from your reports, i.e., link all necessary repositories, issue trackers, monitoring/logging systems, etc.

Note, in case you are writing your report with LaTeX, you might want to write it avoiding Overleaf.
Instead, for local writing, install a LaTex compiler with respective tools and edit LaTeX sources with a text editor of your choice.
On Debian derived Linuxes, you can install the required tools via `sudo apt get install texlive`, for other systems check the [documentation](https://tug.org/texlive/quickinstall.html).
In case you need a collaborative editor, configure your text editor accordingly, see e.g., VSCode [Live Share](https://marketplace.visualstudio.com/items?itemName=MS-vsliveshare.vsliveshare) or [Coedit](https://learn.microsoft.com/en-us/visualstudio/liveshare/use/coedit-follow-focus-visual-studio-code).
That is, treat the sources of your report just like any other source code.


### 3.2) What to include in the report?

#### System's Perspective

A description and illustration of the:

  - Design and architecture of your _ITU-MiniTwit_ systems.
  - All dependencies of your _ITU-MiniTwit_ systems on all levels of abstraction and development stages. That is, list and briefly describe all technologies and tools you applied and depend on.
  - Describe the current state of your systems, for example using results of static analysis and quality assessments.


#### Process' perspective

This perspective should clarify how code or other artifacts come from idea into the running system and everything that happens on the way.

In particular, the following descriptions should be included:

  - A complete description and illustration of stages and tools included in the CI/CD pipelines, including deployment and release of your systems.
  - How do you monitor your systems and what precisely do you monitor?
  - What do you log in your systems and how do you aggregate logs?
  - Brief description of how you security hardened your systems.
  - How do you handle availability and scaling in your systems?


#### Reflection Perspective

Describe the biggest issues, how you solved them, and which are major lessons learned with regards to:

  - evolution and refactoring
  - operation, and
  - maintenance

of your _ITU-MiniTwit_ systems.
Link back to respective commit messages, issues, tickets, etc. to illustrate these.

Also reflect and describe what was the "DevOps" style of your work.
For example, what did you do differently to previous development projects and how did it work?


#### Use of Generative AI

ITU's rules on the use of generative AI apply for this report too.
They are described [here](https://itustudent.itu.dk/Study%20Administration/Generative%20AI#Guidelines) and [in detail here](https://itustudent.itu.dk/-/media/ITU-Student/Study-Administration/GAI/Generative-AI-guidelines-for-students-Spring-2026-pdf.pdf).
Please follow them.
For your report that means that you have to state which generative AI tools have been used for which task(s) in your projects.
Additionally, describe _how_ generative AI tools have been used and briefly reflect and discuss how they supported or hindered your work process.


### 3.3) How to hand-in?

Send a pull request to the final release of your _ITU-MiniTwit_, which includes your complete report in source form and built PDF too, to the file [`final_report_urls.py`](https://github.com/itu-devops/MSc_lecture_notes/blob/master/final_report_urls.py) in https://github.com/itu-devops/MSc_lecture_notes.

**Additionally**, submit the PDF with your report via LeanIT/WISEflow _before_ Monday *18/5/2026 14:00*.
You will find the link to the WISEflow hand-in on LearnIT.
The naming convention for the PDF file that is handed in via WISEflow is the same as above, i.e., `MSc_group_[a-z]\.pdf` (as regular expression).
That is, valid file names are, e.g., `MSc_group_a.pdf`, `MSc_group_e.pdf`, etc.
