# sample_pod_data
This repository contains some sample data to run in a Solid Community Server. It simulates a very simple building project, where some data is provided by a project architect, and some other data by a facility mangager. To make sure everything is registered correctly, you need a running instance of the [Community Solid Server](https://github.com/solid/community-server) and register some new Web IDs (see "Using the identity provider" at the community server readme). The community server startup command that should be used for the CSS is the "/config/file.json". WebIds in the sample data have been registered at a server instance at port 5000. You can set a new npm startup command in package.json as follows:     

"start:file": "node ./bin/server.js -c config/file.json -f ./consolid -p 5000"

Where you can freely choose the folder where data should be stored (here ./consolid).
