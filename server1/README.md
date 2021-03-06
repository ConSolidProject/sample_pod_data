# sample_pod_data
 ## Purpose
This repository contains some sample data to run in a Solid Community Server (CSS). It simulates a very simple building project, where some data is provided by a project architect, and some other data by a facility manager. To make sure everything is registered correctly, you need a running instance of the [Community Solid Server](https://github.com/solid/community-server) and register some new Web IDs (see "Using the identity provider" at the community server readme). The community server startup command that should be used for the CSS is the "/config/file.json". WebIds in the sample data have been registered at a server instance at port 5000. You can set a new npm startup command in package.json as follows:     

```"start:file": "node ./bin/server.js -c config/file.json -f ./consolid -p 5000"```

Where you can freely choose the folder where data should be stored (here ./consolid).

## copying the files
If the accounts you registered at the CSS correspond to the data pods in this repository (i.e., "projectarchitect", "fm", "projectpod"), you can copy the corresponding data pods to the data storage folder. Now the front-end container should be able to retrieve a single project (the IFC duplex) when one of the stakeholders (projectarchitect or fm) is logged in. 

## preliminary federated project structure
The following prefixes are used throughout this document:

```@prefix lbd: <https://lbdserver.org/vocabulary#>.
@prefix dcat: <http://www.w3.org/ns/dcat#> .
@prefix dct: <http://purl.org/dc/terms/> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#>.
```

This project structure is by no means "complete" and merely serves to have something to discuss about.

### Project discovery
At this point, an authenticated agent's LBD projects get discovered via an URL-chain that starts at their WebID card. The WebId is linked to the project registry with the following relationship:

```<http://localhost:5000/projectarchitect/profile/card> lbd:hasProjectRegistry <http://localhost:5000/projectarchitect/lbd/>.```

The object of this triple refers to a LDP container (here this corresponds with a regular folder in your CSS file system, a subfolder of your data pod). You can add some metadata to this folder by creating a .meta file at its root. The metadata that is necessary to use the projectregistry as a consolid project registry is to refer to a project as:

```<8338762f-14c4-41de-9015-c77009a97b08/>  a lbd:PartialProject .```

This metadata will then be added to the RDF document you get when carrying out a HTTP GET request to this LDP Container. The front end uses this to separate partial project repositories from possible other subfolders (although it is recommended to only store project subfolders in the project registry container). NOTE: Currently, this structure follows the LDP container structure (you can see the subject of the above triple is not an absolute URI, it gets resolved because of the LDP Container structure). There are, however, no limitations to just refer to a remote project as an lbd:PartialProject as well. 

The partial project container (here `<8338762f-14c4-41de-9015-c77009a97b08/>`) contains also an extended .meta file with the following data included: 
```
@prefix : <#> .     
@prefix proj: <./> .

proj: a lbd:PartialProject, dcat:Catalog ;
  dct:title "Partial project Architect"@en ;
  rdfs:label "Partial project Architect"@en ;
  rdfs:seeAlso <http://localhost:5000/projectpod/profile/card> ;
  lbd:hasRDFContainer <graphs/> ;
  lbd:hasNonRDFContainer <files/> .
  ```

The `rdfs:seeAlso` refers to the project pod, where general project information can be found. This project pod can be managed by one responsible stakeholder, or be a sub structure of their own pod. Alternatively, data about stakeholders, planning etc., which now resides in the project pod, can be duplicated amongst stakeholders and be synchronized with help of a satellite service. To be discussed.
 
The project pod now contains the following project data: 

```
<http://localhost:5000/projectpod/profile/card>
    solid:oidcIssuer <http://localhost:5000/>;
    a lbd:Project ;
    lbd:hasProjectId "8338762f-14c4-41de-9015-c77009a97b08";
    rdfs:label "8338762f-14c4-41de-9015-c77009a97b08";
    dct:title "Project Duplex"@en ;
    lbd:hasStakeholderNetwork <http://localhost:5000/projectpod/data/stakeholders> ;
    lbd:hasProjectPlanning <http://localhost:5000/projectpod/data/planning> .
```

For now, only the stakeholder network can be dereferenced to a file that contains the following information: 

```
<http://localhost:5000/projectpod/profile/card> 
    lbd:hasStakeholder <http://localhost:5000/fm/profile/card>, <http://localhost:5000/projectarchitect/profile/card> ;
    lbd:hasArchitect <http://localhost:5000/projectarchitect/profile/card> ;
    lbd:hasFacilityManager <http://localhost:5000/fm/profile/card> ;
    lbd:hasOwner <https://pod.inrupt.com/jwerbrouck/profile/card#me> .
```

This information is used by the front-end to find the other project participants and their partial project repositories for this LBD project. 

## questions:
* How should a project metadata structure be organised? Can we use existing vocabularies such as DCAT? 
* Most of the properties in the sample are now placeholder properties that have not been published yet. Which ones can be replaced by existing properties from published ontologies?
