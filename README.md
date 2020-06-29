# ml-boilerplate-brainstorm
A repository to document and test recommended practices for a machine learning boilerplate repository.

__Note:__ Much of what is explored here is inspired by the [Data Science cookiecutter boilerplate](https://drivendata.github.io/cookiecutter-data-science).

# Ideas

## Jupyter Notebooks

### Importing From src
Suppose the folder structure is something like:

```
project-folder
|- notebooks/
|-- some-notebook.ipynb
|- src/
|-- data/
|-- features/
|--- build_features.py
```

- It would be desirable to import classes, functions, etc. from ```src``` directly inside of a jupyter notebook.
- However, using top-level module style imports in python, the jupter notebooks would need to be above ```src``` in the directory structure.
  - This would be inconvenient and not conducive to good organization (the contents of ```src``` and ```notebooks``` are separate concerns, src should not be nested inside of ```notebooks```)
- Further, it is undesirable to modify the python system path; this is a hacky solution to a problem for which there exists an intended solution.
- The recommended approach is to include a ```setup.py``` file in the project root, and to install it as an editable package from inside the project's virtual environment.
  - i.e. ```pip install -e .``` in project root while the project's virtual environment is activated.
  - Use editable mode because (presumably) modules will be added to ```src``` throughout the life of a project and it would be inconvenient to update ```src``` every time a new module is added.
- As long as Jupyter notebook (Jupter lab) is launched from the same virtual environment that the link to ```src``` was installed into, then you can import from src without modifying the system path.
- This works because a link to src is added to the site packages folder of that virtual environment, so all of the modules in ```src``` are available directly through site packages.

- As far as I can tell, the recommended practice is to include a ```setup.py``` file in the project root, and [install the project into the project's virtual environment in editable mode](https://stackoverflow.com/a/35064498). I.e., when the virtual environment is activated, run ```pip install -e .``` in the project root.
  - This adds a link to ```src``` to the site packages in your python virtual environment.
  - If jupyter notebook (lab) is launched from the same virtual environment, when you run something like ```from src.features.build_features import simple_function```, python will automatically look for ```src``` in the site packages folder.
  - This means you can easily import anything from src, even if your notebook is nested in a different location, 

### Version Control Ideas
1. Add a git hook to automatically [surpress jupyter notebook output](https://medium.com/somosfit/version-control-on-jupyter-notebooks-6b67a0cf12a3) when a commit is issued.
    __Pros__
   - Goal is to reduce clutter and simplify version control (fewer potential lines of jupyter notebook config to cause conflicts).
   - The assumption here would be that all relevant artifacts (e.g. visualizations, statistics, etc.) are deliberately included in the markdown portion of the notebook, and any irrelevant output is discarded by default.

    __Cons__
    - It can be time consuming to replicate analysis, esp. if it requires loading/training a model, running predictions, and running an evaluation algorithm.
2. Alternative approach is to [automatically create HTML and python files](https://www.svds.com/jupyter-notebook-best-practices-for-data-science/) for every notebook.
    - This group distinguishes between __lab__ notebooks and __deliverable notebooks__.
      - The former is for experimentation and each data scientist creates their own book following a naming convention.
      - The latter are the fully polished notebooks that deliver analysis results.

### Notebook template
- Another idea that has been proposed is to have default templates that all notebooks are based off of. This could be auto-created with a CLI tool.

__Features__
- Options included for convenience. 

## CLI Utilities

### Click
- [click](https://click.palletsprojects.com/en/7.x/) seems to be a common choice for python CLI tools
- 