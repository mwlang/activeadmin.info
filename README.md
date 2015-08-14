# Content of [activeadmin.info](http://activeadmin.info)

This repository contains the content of [activeadmin.info](http://activeadmin.info).

## Contribution

For documentation updates, please contribute to [activeadmin/activeadmin](https://github.com/activeadmin/activeadmin).

## Refreshing activeadmin.info

The documentation is maintained in activeadmin/docs, but the website content is presented from activeadmin.info/docs

A rake task syncs documentation from the activeadmin/docs to activeadmin.info/docs

To sync the documentation:

* Check out activeadmin.git and activeadmin.info.git to a shared sub-folder
* switch to the activeadmin.info folder
* run: rake update
