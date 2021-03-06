iGUESS - integrated Geospatial Software Framework
===============================================================================

Copyright 2010 - 2016 Luxembourg Institute of Science and Technology. 
All rights reserved. 
Any use of this software constitutes full acceptance of all terms of the 
software's license.

Abstract
-------------------------------------------------------------------------------
iGUESS provides a novel framework development and implementation based on OGC standards. It uses the WFS, WMS, WCS, and WPS standards. IGUESS has implemented a system to integrate any data set in the world using OGC standards. Furthermore, iGUESS has a Python based WPS client implementation which allows easy configuration of WPS based analysis and simulation services and to manage the entire processing chain from configuration, input data handling, data processing, output data processing, data visualisation and data provision as web service in a fully automated way. iGUESS overcomes current deficiencies semi-manual and non-standardised ways of data handling in complex processing/simulation chains and allows for integration of any OGC compliant data source or analysis/simulation service to be integrated.


Resources
-------------------------------------------------------------------------------

A peer reviewed article describing iGUESS and its components:
http://www.iemss.org/iemss2012/proceedings/A3_0783_Sousa_et_al.pdf

The MUSIC project website:
http://www.themusicproject.eu/
  

License
-------------------------------------------------------------------------------

This code is released under the GNU General Public License, version 3, 
please consult the LICENSE file for details.


Requirements
-------------------------------------------------------------------------------
* Rails 3.x, with all the gems specifed in Gemfile
* Python 2.7, with the following libraries
	* psycopg2
	* mapscript

* GDAL
* MapScript
* MapServer
* Postgres (multiple processes access the database, so sqlite is not advisable)


Installation
-------------------------------------------------------------------------------
* Check out code
* Install web application
* Basic configuration
* Set up cron jobs
* Create password files (harvester_pwd, etc.)


Architecture
-------------------------------------------------------------------------------
>>> Need an overview of the Rails application structure here, especially the data/process registration stuff 

In addition to the web application, there are several Python scripts that play an important role in
the operation of iGUESS.  

*   harvester.py

    The harvester is a script responsible for verifying that information about datasets and processes stored in the data base is up-to-date.  Because of the distributed nature of iGUESS, it is possible that datasets are changed, servers go off-line (and come back online), and the names or descriptions of processes are modified.  

    The harvester systematically visits each of the resources and updates all metadata stored in the system.  If a dataset or process is no longer available, the harvester will mark it as dead in the database, but will not delete it.  "Dead" resources cannot be used by iGUESS, but the harvester will continue to check these "dead" resources, and if one becomes avaialable again, the harvester will mark it as alive and it will be available for use once again.

    The harvester should be scheduled to run periodically via cron.  How often it should be run will depend on the number of resources in the database (more resources mean longer running time, which suggests running the harvester less frequently), and the anticipated liklihood that resources will change over time.

    Note that if a dataset is updated, the new data will be immediately available to modules (as iGUESS only stores pointers to the data), but if metadata changes (including bounding box), those changes will not be reflected in iGUESS until the harvester has been run.  This could potentially cause a problem if datasets are used to denote Areas of Interest for a module.

*   wpsstart.py

    wpsstart.py is responsible for initiating a module run.  When a module is run by clicking the Run button on the web interface, a message is set to the Rails server, which in turn calls wpsstart, passing a list of all the parameters which the WPS server needs to run the module.  The primary task of wpsstart is to assemble these parameters into the format required by WPSClient, which makes the actual call to the WPS server.  Therefore, wpsstart almost entirely consists of rather ugly parameter munging code.

*   wpscheck.py

    When wpscheck is run, it will visit each running module, check its status, and make any necessary updates to the database, including registering any datasets created by the module.  wpscheck should be run from cron, and should be scheduled to run frequently, say once per minute.  The more frequently the process runs, the more responsive the system will feel.

*   deleteDataset.py

    When the user unregisters a dataset, deleteDataset gets called.  deleteDataset's primary job is to ensure that any abandoned datasets on the iGUESS server are deleted.


Support
-------------------------------------------------------------------------------
Details to be provided


Wiki
-------------------------------------------------------------------------------
Be sure to check the project Wiki (hosted here on GitHub) for more detailed instructions and documentation.
