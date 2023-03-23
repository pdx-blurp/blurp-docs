---
layout: default
title: sidebar_form.jsx
parent: Frontend Components Documentation 
grand_parent: Document Resources
nav_order: 3
---

# Sidebar Form
Contains the actual forms that are displayed within the sidebar. This component
has it's own states for maintaining the data the user enters, and upon clicking
save it calls one of the change functions passed in as a prop to update the 
node/edge prop and save the data to the node/edge itself. 

## States
- **view:** Affects what form is shown by the sidebar. Takes an enum defined in
constants.ts
- **node:** Used for holding the node data within the sidebar_form component.
Gets filled in with a useEffect hook on the node prop and gets edited from the
form itself. The forms name and value fields correspond to fields stored within
the node state. 
- **edge:** Similar to the node state as its for holding edge data within the
sidebar for editing, works pretty much the same way as the node state except for
its fields, and it only being used in one view in the sidebar. 

## useEffect hooks

For useEffect hooks, sidebar form only uses one. It's specifically set up to 
monitor the passed in node and edge props, and upon a change occuring within 
those objects (like when a user clicks on a node or edge and it causes
GraphEvents to fill out the data of the node state in blurpmap), which causes
setData to be called, which is how the data from a node/edge gets into the
sidebar form itself. 

# Methods
- **handleChange:** Gets triggered whenever a user enters data into a form
field, with it using the event to get the target for the value and the 
value itself to store it within the state.
- **handleSubmit:** Fires when the user clicks the submit button within the
form, and it calls the change data functions that were passed through as props
and gives the entirety of the data in node/edge as arguments to it, so that way
the data gets changed outside of the sidebar.

