# M Classes

## Description
As said in a previous [chapter](structure.md), the backend uses classes which are specifically created to represent data in the frontend. These classes are temporary and are only used to communicate between the front and the back end.

## Problem
For example, if in the front end you want to create a fault, you will create a Finite Fault or a Fault (Infinite). The front end will treat this process a two different objects.

However, the back end knows the faults as 4 different tables :
- Fault
- FiniteFault
- InfluencesSeries
- FaultInfluencesSeries

Since the changes in the front end are a real endeavor, we decided to create a MFault and MFiniteFault which contains all the datas to communicate with the front end.

In the end, the point is to get rid of this mechanics and to correctly communicate with the backend like a RESTFul API would.
