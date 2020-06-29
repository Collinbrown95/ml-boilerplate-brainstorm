# ML Boilerplate and Practices (First Draft)

__Overview:__ This document outlines some ideas with respect to how an ML boilerplate might be organized, and suggests recommended practices. Each proposal is accompanied with a justification for doing so. This document focuses specifically on the semantics of each decision; the actual implementation of this will probably be largely automated with a Command Line Interface (CLI) tool. 

# Project Organization

At a minimum, the following folder structure is proposed:

```
project-root
|- LICENSE
|- README.md
|- environment.yml  # To reproduce virtual environment for this project
|- setup.py         # To make this project installable in the virtual environment
|- notebooks/       # Jupyter notebooks
|- src/             # Source code used in the project
```


# Jupyter Notebooks



## Imports and System Path
Since Jupyter notebooks are used in ad-hoc ways for experimentation purposes, it would be desirable if Data Scientists could easily import source code artifacts located in the ```src``` folder throughout the lifetime of a project.

In the use case described above, Jupyter notebooks should treat ```src``` as an importable python package. However, using the directory structure proposed earlier in this document, the jupyter notebooks will be unable to locate the ```src``` folder because ```src``` resides in a parent folder to where the notebooks are located.

One way to fix the above error is to modify the ```PYTHONPATH``` environment variable to include the path to find the ```src``` directory (or to modify it from within python by appending to ```sys.path```). This can be made to work correctly if all collaborators across all notebooks remember to correctly set this environment variable, or always append to the system path before running their program.

Although the above technique will work correctly, the intended way to accommodate this is to make the project installable with a ```setup.py``` file, and install an editable link of the ```src``` folder into the ```site-packages``` folder of the virtual environment of the project. This is accomplished by running ```pip install -e .``` from the project's virtual environment in the project root that contains ```setup.py```. As long as the Jupyter server is installed and launched from this same virtual environment, the user can import from ```src``` just like they would with any other third party package. 

In practice, this would probably be automated with a CLI utility. So from the user's perspective, imports from ```src``` inside of Jupyter notebooks would just work by default without any issue.

## Version Control with Notebooks

While Jupyter notebooks are great for data exploration and analysis, they are difficult to perform version control on. This is because the ```.ipynb``` files are represented as very large JSON files containing the specification to render a human-readable view through the Jupyer notebook interface. For example, in this [article on version control in Jupyter](https://nextjournal.com/schmudde/how-to-version-control-jupyter), the author shows how a simple change to an inline matplotlib visualization leads to an unwieldy change in the underlying ```.ipynb``` file.

Although there doesn't appear to be a single accepted answer on how to address this, multiple ideas have been proposed on how to make version control on Jupyter notebooks more tractable.

### Automatically Surpress Jupyter Notebook Output

It is possible to add a Git hook to [automatically surpress jupyter notebook](https://medium.com/somosfit/version-control-on-jupyter-notebooks-6b67a0cf12a3) output when a git commit is issued. If this approach is used, it is assumed that data scientists do not rely on the output of the cells in any methodology or code review that takes place.

__Pros:__
- Each notebook file that gets pushed to the remote repository contains only changes made to the cells themselves, which makes each ```.ipynb``` file that gets committed significantly smaller.
- Collaboration on individual notebooks is feasible due to smaller and interpretable ```.ipynb``` files.
- Data Scientists need to explicitly document their results in the notebook (e.g. using markdown cells) since output is surpressed.

__Cons:__
- If the output of the cells is crucial in methodology or code review (e.g. it is not feasible to store cell output in another format), then this process wouldn't work.
- More manual work is required to summarize results in markdown since the output is surpressed.
- If an analysis takes a very long time to replicate (e.g. each cell takes > 1 hour to run), then this approach could be inefficient.

### Lab Notebooks and Deliverable Notebooks
A Silicon Valley Data Science [article on jupyter notebook version control practices](https://www.svds.com/jupyter-notebook-best-practices-for-data-science/) suggests splitting Jupyter notebooks into one of two categories: __lab notebooks__ and __deliverable notebooks__. 

__Lab Notebooks__
- Lab notebooks are designed to be authored separately by individual data scientists, and are intended only for experimentation and development.
- The purpose is to keep a historical (and dated) record of analysis so that the author of the notebook can easily find their work several months later.
- If the owner of a lab notebook produces something of value to the project (e.g. a code artifact or a visualization), this item should be moved to the appropriate part of the project (e.g. a module in the ```src``` folder or saved as a ```png``` file in a ```figures``` folder).
- The notebooks should be split in some logical way (E.g. by topic)
- All notebooks should follow a standard naming convention that all team members agree to. E.g. ```yyyy-mm-dd-<topic>-<author initials>.ipynb```

__Deliverable Notebooks__
- Deliverable notebooks are designed to be authored by the team working on the project.
- Alternatively, if deliverable notebooks are not applicable, then team members could agree that another part of the repository is intended for collaborative output. Examples:
  - The scripts to run the entire data processing pipeline contained in ```src```.
  - A markdown document in a ```reports``` folder.


## Template and Configurations
Jupyter notebooks could be automatically created based on templates using a CLI tool.

# Source Code

# Model Versioning

## Useful Resources
- [Cookiecutter Data Science](https://drivendata.github.io/cookiecutter-data-science) provides a useful starting point with some recommended organization practices and guiding principles.