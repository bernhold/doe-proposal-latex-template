# doe-proposal-latex-template

A LaTeX template for proposals to the U.S. Department of Energy Office of Science.  This template produces the project summary and proposal documents as PDFs.  Other documents required for submission can obviously be added to a repository based on this template for convenience of storage (bio sketches, current & pending support, budget justifications, etc.)

*Last updated to meet the requirements of DE-FOA-0002844 (FY2023 open call).*

## Requirements

* pdflatex
* latexmk
* many LaTeX packages (see `preamble.tex`)

## Basic usage

The `.latexmkrc` file specifies two "default files" to build:
* `project-summary.tex` -- the project summary or abstract document, which is submitted separately from the main proposal document.
* `proposal.tex` -- the main proposal document, including the title page(s), narrative, and appendices that are required.

To build, simply invoke `latexmk -pdf`
and to clean up files, invoke `latexmk -c` or `latexmk -C` (also removes the generated PDF).

## Configuration

There are a number of places information needs to be entered to configure the template.  In many cases, for things to build properly, you'll need to provide placeholders if the data is not known initially.  However in some cases, leaving things blank is okay.

### `CONFIG-proposal.tex`

Contains the proposal title, information about the FOA you're responding to, and information about prior awards and preproposals that may need to be included on the title page of the proposal if relevant.

### `CONFIG-institutions.csv`

Contains information about the participating institutions.  Fields include the full name of the institution, the abbreviated name or acronym, and the key to use when defining an acronym (using the `glossaries` package).  

Additionally, this file contains information about the institution needed for the title page, including the administrative point of contact, their contact information, and the street and postal addresses of the institution.  This adds up to a lot of fields.  Initially, it should be okay for them to be blank, but they must be present (i.e., the right number of commas to delineate all of the fields).

### `CONFIG-investigators.csv`

Lists all of the investigators on the proposal, their institutions (by the acronym key defined in `CONFIG-institutions.csv`), their role in the proposal, and contact info.  

The roles are latex macros defined in `CONFIG-roles.tex`.  The default is to use the roles that DOE recognizes, but you can change this if you want to do things differently.

The contact info is needed only for lead- or co-PIs.  For others it must be present, but can be left blank (the right number of commas).

### `CONFIG-roles.tex`

Defines the various roles that investigators can have in the proposal.  Traditionally, DOE recognizes exactly one Lead Principal Investigator, exactly one co-PI (or institutional PI) per participating institution, and everyone else named in the proposal is Senior Personel.

The role macros defined here should be used to designate each person's role in `CONFIG-investigators.csv`.  The `\rolelpi` and `\rolecpi` roles specifically are used in a number of places where the list of investigators is processed where only the PIs are desired. If you want to define different roles, you may also need to modify to the code where they are processed to ensure that the right people are listed.  Currently, that includes the following files:
* `frontmatter/collaborative-supplement.tex`
* `utilities/make-pi-acronyms.tex`
* `proposal.tex`
* `project-summary/cover.tex`

## Adding content

Once the configuration is complete, you'll obviously need to add the actual content of the proposal. Based on the structure of the template, here are some notes/suggestions on where content should go.

### Project summary

The project summary consists of a cover page with the title and participants and a one-page abstract.  `project-summary.tex` is the top level document for the project summary.  The files are in the `project-summary` directory.  `cover.tex` should be fully automated.  Add your abstract to `abstract.tex`.

### Main proposal

### Frontmatter

`frontmatter/FRONTMATTER.tex` is setup to generate one titlepage per institution and the collaborative supplement.  Each submitting institution will need to remove the titlepages other than theirs (e.g., using Adobe Acrobat).

`frontmatter/collaborative-supplement.tex` is required for collaborative proposals.  The template generates a budget table (as required) based on `CONFIG-budget.csv`.  The template works for three year proposals.  If your period of performance is anything else, you'll need to modify section where the budget table is generated.

### Narrative

`narrative/NARRATIVE.tex` is the main file for the narrative.  To keep things sensible, we recommend that this file consist simply of a series of `\input` commands referencing files in the `narrative/` directory for the real content.

### Graphics

Graphics should be collected in the `graphics/` directory, no matter where they are used in the document.

### Bibliography

`.bib` files should be collected in the `bib/` directory.  All of the `.bib` files must be listed in the `\bibliography` command in `proposal.tex`.

### Facilities and equipment appendices

You may not need both. Currently, `equipment/EQUIPMENT.tex` is setup to refer to the facilities appendix (since it is nominally required, you don't want to leave it out).  But you can switch to using the institutional includes by changing what's commented in the file.  You can also it manually if you prefer.  `facilities/FACILITIES.tex` is setup similarly, but the default is to use the institutional includes.

These appendices are normally constructed as a section per participating institution, in the directories `facilities/` or `equipment/`.  In this template, we iterate over the institutions (as defined in `CONFIG-institutions.csv`) and to a "smart include" of one file per institution with the same name as the acronym key given in the configuration file.

The smart include will look for either a `.tex` file or a `.pdf` file, in that order.  So for `ornl` it would first look for `ornl.tex` and if that's not found, `ornl.pdf`.  If neither is found, a warning message is displayed at that location in the `proposal.pdf`. **Note: filenames are case-sensitive on most filesystems.**

The expectation is that each institution's contribution will be at the `\section` level (in LaTeX) and will start on a new page.  **PDF documents should *not* have page numbers** -- they will be added by the smart include process to be consistent with the numbering of the overall proposal.

### Data management plan and PIER plan appendices

Content for these appendices can be added directly to `data-plan/DATA-PLAN.tex` and `pier-plan/PIER-PLAN.tex`, respectively.

## Modifications

Of course you're free to modify anything and everything however you like.  But a few notes that might help.

### `acronyms.tex`

Using a LaTeX package to help with acronyms can ensure that they get expanded on first use, wherever that happens.  This template is set up to use the `glossaries` package for ths.  But its use is entirely optional.  I've seeded `acronyms.tex` with some DOE-related acronyms, but you're free to change or remove them.

If you do use it, you should add acronym definitions to `acronyms.tex`.  It is read in both the proposal and the project summary.  If you don't want to use it, you can ignore it or delete it.


### `preamble.tex`

Contains all of the package inclusions, many macro definitions (e.g., the smart includes used in the facilities and equipment appendices).  This includes many of the commands needed to tweak the spacing and other formatting of the LaTeX engine.  In so far as reasonable, I've tried to stick (close) to the defaults for a standard LaTeX report in 11pt.  But if (when?) you need to start tweaking for space, you'll find most of what you can play with here, but commented out.

This is also the place to define new macros that are used elsewhere in the document.

## Potential Problems

The last time I used this template, we were working both locally and on Overleaf.  We experienced a number of problems on Overleaf and on one of the local systems which appear to be related to package versions.  We ended up incorporating key packages into the working repository to ensure that we were using the same versions everywhere, and that they were the latest.  The following packages presented problems:

* csvsimple
* glossaries
* pst-xkey
* xkeyval

It is worth noting that Overleaf was not absolutely up to date w.r.t. package versions.
