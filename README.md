# Content of [activeadmin.info](http://activeadmin.info)

This repository contains the content of [activeadmin.info](http://activeadmin.info).

## Contribution

For documentation updates, please contribute to [activeadmin/activeadmin](https://github.com/activeadmin/activeadmin).

## Refreshing activeadmin.info

The documentation is maintained in activeadmin/docs, but the website content is presented from activeadmin.info/docs

A rake task syncs documentation from the activeadmin/docs to activeadmin.info/docs

To sync the documentation:

* Check out activeadmin.info.git
* switch to the activeadmin.info folder
* run: rake update

This will clone activeadmin.git to the repo sub-folder (or git update it) and then update the docs 
folder with repo/docs, changing *.md links as appropriate.
