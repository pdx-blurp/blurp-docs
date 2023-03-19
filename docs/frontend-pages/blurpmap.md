---
layout: default
title: Blurpmap.jsx
parent: Frontend Pages Documentation 
grand_parent: Document Resources
nav_order: 1
---

# Blurpmap

## States

*   **profile:** used to store when a user is logged in, and contains the 
sessionID (obtained from cookies and gets sent to the backend upon each request)
 and the mapID, which also gets sent to the backend with most requests. 
*   **loadMapModal:** used for controlling the view of the modal component in 
src/components/select_map_modal.jsx. Open when set to true causes the modal to 
open on the next state update, and view controls what type of view gets 
displayed. This takes an enum value defined in the constants file which has two 
different options: START and NOTLOGGEDIN. 

## Methods

*   **SaveToDB:** Used for the case where a user who's logged in loads a map 
from a file and wants it saved to their account. It takes in a title and makes 
a request to the database, and afterwards gathers all the data currently within
 graph and packages it up into a JSON to send to the backend, with it sending it 
to the new mapID it got as a response to the initial request
*   **LoadFromDB:** Used in select_map_modal for letting users select the map 
they want and load it in. Takes in mapID and profileSet, as there were issues 
with having it check the profile directly due to the profile’s state not being 
changed at the time of LoadFromDB being called. It makes a request to the 
backend and when it gets valid data back it clears the current graph and goes 
through the response and adds every node and edge, and sets setNodes to contain 
an up to date list of all the nodes in the graph. 

