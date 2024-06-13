---
title: 'Schrodingers Interface: NPE crashed the (Go) server'
tags: [rca-stories,golang, interface, NPE]
date:  "2024-06-13"
---

I once crashed a service because of my misunderstanding of one of the gotchas about how interfaces work in golang.


Simplified version: https://go.dev/play/p/fbOcTx3B-a5

An unassigned instance of an interface is nil. However, once you assign an uninitialized implementation of the interface to an interface variable, the variable is no longer nil. 


In Golang(possibly in other languages as well), nil check for interface does not imply the underlying value(or implementation) of the interface is nil.



Submitted by: [https://github.com/subedipiyush](Piyush Subedi)

