This document sets up the terminology for new experiment design. The language for the design is discussed in [ExDescLang].

We start from a class of experiments that we want to define - e.g., DDoS experiments.

## Metadescriptions

The highest level is *metadescription_' (Steve sometimes calls this '''model''') of that experiment class. It contains '_dimensions* that are important to be defined for this class of experiments. Metadescriptions are created by experts in the given research area. Users usually don't touch them or if they do they start from an existing one and modify it. If there's no metadescription in a given field there should be a nice user-friendly process that lets experts create it from scratch by defining the required dimensions and then slowly add to it. Each dimension should allow for multiple choices and their combinations, e.g., multiple topologies connected together, multiple traffic streams, etc.

Every metadescription has the following dimensions:

* objects that either act in the experiment or are acted upon (nodes that send traffic, caches that get changed, ...)
* event timeline (JTW calls this *workflow*)  
* invariants (statements that must be true for this class of experiments to be valid, and conditions for this to happen) 

Every dimension can have *constraints*:
* e.g., what topologies work for given objects, what kind of traffic generators can be used to generate traffic in the event timeline, etc.

Some metadescriptions contain more dimensions, like: 

* background traffic 
* visualization 
* post-processing 
* monitors 
* whatever else makes sense for that class of experiments

Each dimension can be output from multiple *generators_'. A generator can be as abstract as an equation, or it could be an output of a simulation, or a set of data points, or pretty much anything else. Each generator can have '''constraints''' pretty much describing its inputs and outputs such as "this equation creates Internet-like topologies only when they contain more than 50 nodes". Each generator has an '''input''' set of variables and an '''output''' one where the output is in some common format (or translatable into it) for that dimension. A generator belongs to a '_class* of generators depending on what it generates. So far we can foresee several classes:
* topology
* traffic
* routes
* malware


## Experiment Design

User comes to the testbed with some research hypothesis - "my worm can spread to all vulnerable machines in the network in 10 seconds provided that fan in/out is at least 3". 

Starting from metadescription the system should be able to select a set of generators for each dimension that satisfy relevant invariants. These generators are shown as options to the user to choose from and parametrize. This interaction with the user is called *experiment design_' (used to be '_template* in June'10). Almost any choice should be coupled with a default value so that users can just accept defaults and run the experiment, while other users may decide to change the selection or parametrize it differently. During the design phase a user may decide they need another dimension or another generator for an existing dimension. These should then be added to the metadescription.

Making a selection for one dimension may reduce choices for other dimensions - this is where the system checks constraints against each other and against invariants and only keeps valid combinations around. 

## Experiment Start

Based on user's choices the system generates the topology file and a *set of scripts_' (SEER or Eclipse or something else) that are directly runnable on the testbed (in June'10 we called this a '_recipe*).

## Experiment Runtime

A *validity monitor* (experiment health) is always collecting data and making sure invariants hold. Users may define an empty workflow and experiment manually like now, or start with a workflow and manipulate it on the fly or start with a workflow and insert manual events into the stream. Once an invariant is violated we may or may not be able to tell the user exactly why this happened but we should be able to localize time/location where the failure occurred and provide logs for further analysis.

Any monitors selected by the user in the design stage are also running, as is any processing of that data and visualization.

## Post Experiment

Any post-processing actions are performed, logs are collected and the set of scripts can be archived (with or without logs and experimental outputs) for future use.

## TODO

What language is each of the dimensions described in. What I/O we need for each dimension. 