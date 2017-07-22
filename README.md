SecureCRT Scripts
==================
SecureCRT Python scripts for doing various tasks on Cisco equipment.

These scripts should work on any version of SecureCRT that supports python.  If you find that a script won't work on your machine, please post an issue to let us know!

## Running The Scripts:


To run any of the below scripts, do the following:

1) Clone this repository to your computer, or download the .zip file and extract it

2) **AFTER** connecting to a device in SecureCRT, go to the *Scripts* menu and select "Run"

3) Choose the script you want to run

4) When a script runs it will look for the global settings file (see the below section on settings), and possibly a local settings file for script specific items.  If this file doesn't exist (and it won't the first time you run a script) the script will create the file, but not execute the script logic.  Modify the settings to your preference and run the script again.

5) If the script produces an output, find the output file in the directory specified in the global settings file.

The output files are automatically named based on the hostname of the device connected to.   This name is taken from the prompt of the device, so these scripts will work whether you are directly connected, or connected via a jumpbox or other intermediate device.

## Settings:
All settings files are stored in the `settings` directory in the root of the scripts directory.

### Global Settings
Global settings that are used by all scripts are saved in the `global_settings.json` file.  The following options are available in the global settings file:

* 'save path': This is the path where you want the output from scripts to be saved.  *NOTE* For Windows systems, either use forward slashes (/) or double backslash (\\) to represent a single backslash.  If a single backslash is used, Python may interpret it as an escape character.
* 'date format': Default is '%Y-%m-%d-%H-%M-%S'.  This string specifies how the date stamp in output filenames is formatted.
  - %Y - 4-digit Year
  - %m - numeric month
  - %d - day of the month
  - %H - Hours
  - %M - Minutes
  - %S - Seconds
* 'modify term': True or False.  When True, the script will attempt to modify the terminal length and width to 0 so that output flows continuously.  When the output is complete the script will return the length and width to their original values.   If False, it will not change the values, but instead auto-advance when a "More" prompt is encountered.
* 'debug mode': True or False.  Currently not implemented (on the list of TODOs)

### Script-Specific Settings
Some scripts have local settings files that only apply to that script.  If such a file is needed, the script will create a `.json` file in the `settings` directory with the same name as the script.  Notes about the settings can be found in the script, or possibly as comment entries inside the settings file itself.

## Scripts:

* **cdp_to_csv.py** - Captures detailed CDP information and saves it to a CSV file.  A local settings file is used to specify strings (such as domain suffixes) to be stripped off hostnames.
* **create_intf_desc.py** - Outputs a config script to label interfaces based on CDP info.
* **create_sessions_from_cdp.py** - A script that pulls the CDP information from the connected device and creates a SecureCRT session for each device in the CDP table.  Save location (rooted in the SecureCRT sessions directory) is specified in the local settings file for this script.
* **document_device.py** - Saves multiple command outputs at once.  The list of commands that will be captured is saved in the local settings file for this script.
* **interface_stats.py** - Outputs a CSV file for a quick and easy view of some high level details about all interfaces that are "up", such as total packets in/out, packet rate in/out and errors in/out.
* **mac_to_csv.py** - Outputs the mac address table into a CSV file.
* **nexthop_summary.py** - Outputs a CSV file with all the next hops and a detailed breakdown of each type of route pointing at that next hop.
* **save_running.py** - Captures the running config to a file, using a name based on the device's name and current date.
* **save_output.py** - Generic script that prompts for a command and saves that output to a file.
* **securecrt_python_version.py** - A script that returns a pop-up window with the python version being used by SecureCRT (mostly for troubleshooting)

**Note:** The file **empty_script_template.py** can be used as a starting point for writing new scripts.  This code skeleton contains all the required function calls for the majority of the provided functions to work properly.

## Included Modules:

For anyone modifying or creating new scripts, a handful of different modules are used to store commonly used functions for interacting with CLI sessions to Cisco devices, process the outputs and read/write to files.  All modules are saved in the *imports* directory.

### Custom modules

* **imports.cisco_securecrt** - This module contains the functions used to interact with telnet/ssh sessions to Cisco devices.  There are functions for things like setting up and tearing down a session for interaction with the scripts, sending commands to a device, and capturing the output for use in other functions.  Most of these functions require the use of the session data to operate.
* **imports.cisco_tools** - This module contains functions for parsing or processing the output from Cisco commands into python data structures so that the data can be more easily used in the different scripts.
* **imports.py_utils** - This module contains small utility functions that are used to simply reading and writing data structures to file, getting the current date/time, provide human sorting, etc.

### 3rd Party modules

Two modules written by Google are leveraged in these scripts to simplify some of the operations.

* **ipaddress** - (https://pypi.python.org/pypi/py2-ipaddress) This module provides a special data structure for IP addresses and IP networks to be stored in a way that allows for easy manipulation and comparison of IPs/networks (overlap, subnet membership, etc).  It also allows for IP addresses to be represented in a variety of ways (IP only, bit length, subnet mask, etc).
* **TextFSM** - (https://github.com/google/textfsm) This module provides an easier way to process semi-structured data, such as the output of CLI commands.  A template file is used to describe the structure of the output and the variables that are interesting and this module will extract those variables.  All the templates used by these scripts are stored in the `textfsm-templates` directory.
