---
layout: post
category: Demos
tags: [pravega, flink, gateway, telegraf]
subtitle: Dell EMC Streaming Data Analytics - Hands-on
technologies: [SDP]
img: post/distance-calculator-sdp/architecture.png
license: Apache
support: Community
author: 
    name: Adwaith Moothezhath
    description: I am a nautilus app developer.
    image:
css: 
js: 
---

This post demos the power of ingesting, analyzing and correlating data across different data and event streams on the Streaming Data Platform.
<!--more-->

## Purpose 
The objective of this code sample is to let users easily setup a useable example project of the most common use case on SDP. The use case we are targeting is showing the power of ingesting, analyzing and correlating data across different data and event streams on SDP. The following code sample lets the user run an install script to easily setup a project on SDP with their preferred Pravega streams and Flink jobs they want to run.

## Design/Details  
![Demo Design]({{site.baseurl}}/assets/heliumjk/images/post/telecom-demo-automation/architecture.png)

This high-level diagram shows all the components which are used in this demo. 
- The first component is the data sources which are iDRAC, VMware and k8s data on a server. 
- The second component is the telegraf agent which is used to collect the data and output it. 
- The third component is the Pravega rest gateway which is a rest endpoint that receives data from telegraf   and writes to Pravega streams. 
- The fourth component is the Pravega streams which is used to read the continuous data. 
- The fifth component is the Apache Flink which is used to do some data analysis and transformations. 
- Finally, the last component is the Influxdb database with Grafana to view the data. 
The data source and telegraf agent is set outside of SDP and the Pravega ingest rest gateway, Pravega streams, Apache Flink, Influxdb and Grafana are all part of SDP. 

The End-to-End flow of this code sample is first the telegraf agent collects data from different input systems which the user can configure. The telegraf agent then outputs the data to Pravega rest gateway which is a rest API endpoint that receives the data and writes it into Pravega streams. Then after deploying some Flink Jobs provided by the user the Flink jobs can read the data from the Pravega streams and execute the Job. In the example Job provided it will read the stream data and run the map metrics job then setup and display the data on Grafana dashboards. 


### Prerequisites
- Clone [sdp-starter-kit](https://github.com/vangork/sdp-starter-kit)
- [Python 3](https://www.oracle.com/java/technologies/downloads/#java11)
- [Java 11](https://www.oracle.com/java/technologies/downloads/#java11)
- [Gradle 7.2](https://gradle.org/install/)
- [Helm v2 or later](https://helm.sh/docs/intro/install/)
- SDP Running in a Cluster
- Flink 1.15.2 runtime

### Setup Instructions 

##### 1. Setting up code sample
- Go into the telcom-demo-app-bundle folder and open the sample.yaml file.
- Under global.streams[0].Inputs change the inputs to your data sources. Use current Inputs as reference. For each input include name of the stream, prometheusCheck and input to your data source. Example of an input template is:
![Input Template]({{site.baseurl}}/assets/heliumjk/images/post/telecom-demo-automation/input_stream_template.png)
Input plugins accepted by telegraf can be found at [https://docs.influxdata.com/telegraf/v1.14/plugins/plugin-list/](https://docs.influxdata.com/telegraf/v1.14/plugins/plugin-list/).

##### 2. Run default Flink Jobs
- If you want to setup quickly you can run the pre-existing flink jobs, Map Metrics Final and Map Metrics Dashboard by adding install_map_metrics_final and install_map_metrics_dashboard under global. flinkJobs
![Default FlinkJobs]({{site.baseurl}}/assets/heliumjk/images/post/telecom-demo-automation/default_flinkJobs.png)

##### 3. Create your own Flink Jobs
- If you are adding your own Flink Jobs add a name in this format under flinkJobs:
![FlinkJob Template]({{site.baseurl}}/assets/heliumjk/images/post/telecom-demo-automation/flinkJob_template.png)
- Next go into the folder telecom demo copy and then into charts and create a new folder with the yaml files for the new flink job. Use map-metrics as reference.
- Next go into the folder telecom demo copy and then into scripts and create a new shell script with the same name you added in the sample.yaml file. Use install_map_metrics.sh as reference. 
- Finally If you want to send data to influxdb go into the folder telecom demo copy and then into scripts and flinkJobSetup.py and modify the script to update influx db details. Refer to the code block #updating map-metrics-values.yaml influxdb details in the flinkJobSetup.py for reference. 

##### 4. Install the Project
- Finally, after configuring the sample.yaml file to add your inputs and creating new flinkJobs or using the default ones provided run the install shell script.
- To run the install.sh script go to telcom-demo-app-bundle folder and run the command `./install.sh`. This sets up the project with the streams provided from the input and runs the flink jobs. 


### Source
[https://github.com/vangork/sdp-starter-kit/tree/master/distance-calculator](https://github.com/vangork/sdp-starter-kit/tree/master/distance-calculator)