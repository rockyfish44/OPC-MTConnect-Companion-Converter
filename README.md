# OPC-MTConnect-Companion-Converter

## Overview
The following repository probes an MTConnect file and produces an OPC-UA/MTConnect Companion format that can be uploaded to a server. Data is then streamed to a folder for use by the OPC-UA server. "Live" data is taken by continuously retrieving data from the MTConnect agent. The server used is the .NET Standard provided by The OPC Foundation. A model compiler, also provided by The OPC Foundation, is used to generate nodes for the server. Follow instructions carefully to properly set up the converter along with data streaming.

### Please go to the following two links and download the files:
1. https://github.com/OPCFoundation/UA-.NETStandard
2. https://github.com/OPCFoundation/UA-ModelCompiler
These files are necessary for the execution of the testing of companion specification.

## SERVER SET UP:
The following set up is specific to Visual Studios.
1.	Find the Server folder in the UA-.NET Standard. File path: …\UA-.NETStandard-master \SampleApplications\Workshop\Boiler\Server. 
2.	Replace this Server folder with Server folder from this repository. 
3.	Open the UA Quickstart Application solution in the UA-.NETStandard-master folder. Right click on Boiler Server (under Boiler folder) and Set as StartUp Project. 
4.	Ensure the build action of Opc.Ua.MTConnect.PredefinedNodes.uanodes is set to Embedded Resource.
5.	In the project properties, ensure the configuration is set to Active (Debug).  
6.	Open the BoilerNodeManager.cs in the server. Set the “filepath” to the location of the DataFiles being streamed. 
7.	Press F5 to run the server.

## STREAMING DATA TO THE SERVER:
The url in "xml_mtconnect_continuous.py" can be altered to extract data from other web sources. For simply accessing data from XML files, the data must be imported and sent to the “get_root” function.
-- Setting up file locations for Memex_3Axis Streaming -- 
1.	Open "xml_mtconnect_continuous.py". Locate the "write_ouptut_file" function, and change the file path to the location of where the stream files are to be outputted. (Should be the DataFiles folder)
2.	Ensure that the modules are installed/updated so they properly import. Methods such a pip install can be used.
3.	Run the python "xml_mtconnect_continuous.py". The data will now stream, and the server will find the data.
4.	Return back to the NodeManager, and press F5 to run the server.
5.	Use UAExpert as a client to connect to the server. 

Note: Unless the proper data files already exist in the DataFiles location, you must start the stream before starting the server, as there will be an error while searching for files. 

## GENERATING NODES:
Nodes can either be generated by probing xml from a website or by probing a local xml file. One of these two options can be selected by simply editing/commenting code in the “SETUP” section of the “xml_parsing_mtconnect_nodeset.py”. 
Note: Only certain components have been developed in the XML converter/probe: Linear Axes, Rotary Axes, EmergencyStop, Message, Line, and XLoad. 
1.	Open “xml_parsing_mtconnect_nodeset.py” and locate the “Set Location of the MTConnectModel”. Either ensure the MTConnectModel is in the same file location as  “xml_parsing_mtconnect_nodeset.py”, or set the file path for the MTConnectModel.
2.	Locate the “Output File” section and set the file path to ".../UA-ModelCompiler-master/ModelCompiler/Design/".
3.	Find “Executes Batch File” and set the file path to ".../UA-ModelCompiler-master/"
4.	Locate the “Move Files” section and set the source and destination locations. Examples are given in the region.

## STREAMING ADDITIONAL DEVICES:
The server address space will automatically populate with additional devices (nodes) after completing the “GENERATING NODES” steps. To stream to these nodes, the “BoilerNodeManager.cs” must be edited:
1.	Find the “Private Fields” region and add a private variable of “….State” type. Ex: BoilerState
2.	Locate the public “CreateAddressSpace” function, and find the region “Defining Nodes for Memex_3”: 
a.	Create a passive node for the new object by editing: “Opc.Ua.MTConnect.Objects.Memex_3Axis”. Remove Memex_3Axis and add your own object. 
b.	In the same region, change the variable Memex_3 to your variable name from step 1. 
3.	In the “DoSimulation” function, find the “Manual Streaming via Text File” region. Edit the “Streaming for Memex_3 Device” region to find and stream data. Using this method, text files are located and the string is uploaded to the node. If the node is of a type other than string, the “Convert.To” method must be used. Ex: Convert.ToDouble(). To access the value of a node, the “.Value” method must be used at the end of each node.
Note: If additional/different components are added to a device, you must find the individual variables through the node hierarchy. Ex: “Memex_3.Axes.X.Value”

## ADDING ADDITIONAL COMPONENTS:
To add additional components, the OPC-UA structure, the MTConnect structure, and the input model-compiler file, must be thoroughly understood. In the “xml_parsing_mtconnect_nodeset.py” file, additional component objects must be created using the ElementTree module, with in the deviceObjectType definitions. An additional componentObjectType must then be created for the new component, if it is not already defined.
-	Instructions for using ElementTree module can be found at: https://docs.python.org/2/library/xml.etree.elementtree.html
-	Example OPC-UA model can be found: \UA-ModelCompiler-master\ModelCompiler\UA Model Design.xsd
-	Example MTConnect file structure can be found at: http://simulator.memexoee.com/
-	Example input model-compiler file can be found in this repository: MTConnectModel.xml

## GUI’s:
The GUI’s are created using Dash by Plotly. Ensure the correct modules are imported to run the program. 
-	GUI Server: This GUI accesses a CSV data file, provided by the Data Logger in UA Expert, via the “get_csv_data” function. The CSV file is configured to the current folder. This can be changed by establishing a file path when importing via the “open” method.
-	GUI_one_data_set: This GUI access one set of data: an x and y text file. In the “get_data” function, set “xfile” and “yfile” to the file and its location.
-	GUI_main: This GUI combines the previous two GUI’s into one application. As well, it can access more than one data file. By altering the “keywords” list in the “get_data” function, data sets can be added or removed from the GUI. Again, the data files must all be in the same path. Set the “filepath” in the “get_data” function.
