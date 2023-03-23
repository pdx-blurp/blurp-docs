---
layout: default
title: Blurpmap.jsx
parent: Frontend Pages Documentation 
grand_parent: Document Resources
nav_order: 1
---

# Blurpmap

## The Quirks of Sigma
Sigma has some quirks that should be brought up as they can be confusing, and
we've had to implement non intuitive solutions to the problems we've encountered
due to the default behavior of Sigma. 

- **Node Size:** As it turns out, node size isn't linear in Sigma. Values can be
really close together and size wise they would look radically different, and 
other values can be far apart and look similar in size. To get around this, we
had to set up the size to be done by running the size value we set up through a
log function to make the scale seem more linear, and better distinguish between
the different sizes. We wanted the user to be able to select a size from 1-10 so
it looks nice and doesn't have any weird numbers within it, so we decided to put
the value that we get from the user into a formula to make the scaling look better.
The formula we had to go with looks like:
```
// From GraphEvents in blurpmap.jsx
const newNodeSize = Math.log(nodeSize + 1) * 30;
```
- **Camera Zooming:** By default, Sigma upon a user double clicking, centers the 
camera on the spot the user double clicked, and zooms the camera in as well. The
fix for this was fairly simple, as in GraphEvents we just needed to get the event
from doubleClick and run a method from it called "preventSigmaDefault()" which in
that context makes doubleClick do nothing. 
- **Node Movement:** In order for us to get node movement to work properly, we had
to disable camera movement by running the method "sigma.getCamera().disable()" 
from within GraphEvents while a node is being moved around, and re-enable it when
the user has stopped moving the node. 
- **Coordinates:** There are two different sets of coordinates within Sigma, one
set for the view that the user sees within their web browser, and another for the
graph itself. You can get the coordinates of the window from GraphEvents, which
the documentation linked in there goes into detail about how to obtain that. In
order for us to be able to create nodes at the spot a user clicked, we have to 
run the sigma method called "viewportToGraph" which converts the coordinates for
us from the window coordinates to the graph coordinates. 

## Sigma Settings
For our project, we had to change some of the default settings for Sigma to get it
to work how we wanted. Within the SigmaContainer HTML tag is an attribute called
"settings" where these values are being set. Most of these were found by looking at
Sigma's source code to see what they provided us to change. 

- **Camera Zooming:** In order to prevent the user from zooming in or out forever, we
set a minimum and maximum camera ratio, with their values being pulled from Constants.ts.
- **Autoscaling:** By default Sigma will scale the nodes as the user zooms the camera, or 
moves it around. For our project this has been set to false to turn that feature off.
- **Image Rendering in Nodes:** This was done by importing a sigma program for drawing
the images inside of the "nodeProgramClasses" setting, and afterwards we had to set 
"defaultNodeType" to "image". From there we were able to set up a node attribute for 
getting the nodes to display an image, and another for the location to the image.

## States

- **profile:** used to store when a user is logged in, and contains the 
sessionID (obtained from cookies and gets sent to the backend upon each request)
 and the mapID, which also gets sent to the backend with most requests. 
- **loadMapModal:** used for controlling the view of the modal component in 
src/components/select_map_modal.jsx. Open when set to true causes the modal to 
open on the next state update, and view controls what type of view gets 
displayed. This takes an enum value defined in the constants file which has two 
different options: START and NOTLOGGEDIN. 
- **node:** used for sending node data to the sidebar. The data gets filled in
upon a user clicking on a specific node (look at GraphEvents for that code) and
that change then triggers the data within sidebar form to be updated so when it
opens, the form fields are populated with node's data. The data gets updated
within the changeNodeData function, which gets called from the sidebar form. 
- **edge:** used for sending edge data to the sidebar. Works pretty much the
same way as node, with it having a function called changeEdgeData for updating
edge data after a user has edited it from the sidebar. 
- **nodes:** (deprecated) used for maintaining a list of nodes currently in 
the map. Was used for the relationship modal, but isn't used anymore. 
- **node1 & node2:** Stores the ids for nodes that are selected. Used only for
edge creation, with it only being set when a user clicks on a nodd

## Methods

- **SaveToDB:** Used for the case where a user who's logged in loads a map 
from a file and wants it saved to their account. It takes in a title and makes 
a request to the database, and afterwards gathers all the data currently within
 graph and packages it up into a JSON to send to the backend, with it sending it 
to the new mapID it got as a response to the initial request
- **LoadFromDB:** Used in select_map_modal for letting users select the map 
they want and load it in. Takes in mapID and profileSet, as there were issues 
with having it check the profile directly due to the profile’s state not being 
changed at the time of LoadFromDB being called. It makes a request to the 
backend and when it gets valid data back it clears the current graph and goes 
through the response and adds every node and edge, and sets setNodes to contain 
an up to date list of all the nodes in the graph. 

## GraphEvents
While GraphEvents is a method within blurpmap, it deserves its own section as it
handles any action done on the map itself. All interactive actions involving nodes
or edges ends up triggering some code within GraphEvents. 
[Sim51's](https://sim51.github.io/react-sigma/docs/example/events) events 
documentation goes into great detail about how graph events occur, so it's
recommendeed to take a look at that. For more specific pieces of information you 
may need to look at the sigma or react-sigma source code/tickets on GitHub, as 
sigma is missing a lot of documentation on how it works. For this section, we'll
be giving an overview of how everything works in relation to blurp itself, and 
point out workarounds we had to do in order to get sigma to work the way we wanted.

For node creation, that occurs under the click event after the user has selected one
of three different node types from the map toolbar on the right side of the screen. 
It will create a new node at the location where the cursor was. In the case that a 
user is logged in, the data will get sent to the backend through a post request.

For edge creation, that occurs within the clickNode event. Essentially if node1 
is null the id of the node that was clicked will be stored there. For the other 
node being selected, if there isn't already an edge between the first and 
second nodes, it will set node2 to the id of the selected node and will open the
edge creation modal. 

For node/edge deletion, they occur within the clickNode and clickEdge events
respectively, and only occur if the eraser tool is selected. Erasing will send
a delete request to the backend with the node/edge id. 
