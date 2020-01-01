# ServiceNow + Microsoft Skype For Business

One click solution for a fulfiller to initiate a skype chat with the impacted end user

## Background

An agent tries to get in touch with an end user regarding a reported issue raised through our ServiceNow platform. The agent has to switch between their browser and the Skype application, search for the user in the search bar before they can even initiate a chat.

### Technology

1. ServiceNow. A powerful and robust platform that we use internally for our ITSM needs.
2. Microsoft Skype. A communcation channel that is utilized by many organizations not only for its voice capabilities but also for its chat function.

## User Story

As an ITIL fulfiller, I want to initiate a Skype chat with the impacted from the Incident form to communicate directly on the reported issue.

### Acceptance Criteria

Given that the fulfiller and the user has Skype provisioned for their Active Directory account and are logged in to Skype;

* While on the incident ticket form, the fulfiller can click on a chat icon to trigger the chat.
* The icon should be placed at the same level as the "Raised For" field.
* For easy identification, the chat icon must be unique and not reused on the same page.
* The fulfiller will be prompted with a "Chat" pop up message when the mouse is hovered over the icon

## Implemented Solution

1. Update the caller_id attributes to include ";popup_chat" values

Original values
```
encode_utf8=false,ref_contributions=user_show_incidents
```

Updated values
```
encode_utf8=false,ref_contributions=user_show_incidents;popup_chat
```

2. Create a new UI Macro

Code
```
<?xml version="1.0" encoding="utf-8" ?>
<j:jelly trim="false" xmlns:j="jelly:core" xmlns:g="glide" xmlns:j2="null" xmlns:g2="null">
<g:evaluate var="jvar_guid" expression="gs.generateGUID(this);"/>
<j:set var="jvar_n" value="show_incidents_${jvar_guid}:${ref}"/>	
	
<button id="${jvar_n}"
type="button"
aria-haspopup="true"
onclick="invokeChat('${ref}')"
name="${jvar_n}"
class="btn btn-ref btn-default icon-comment"
title="${gs.getMessage('Chat')}">
</button>
	
<script>
function invokeChat(reference) {
var s = reference.split('.');
var tableName = s[0];
var referenceField = s[1];
var v = g_form.getValue(referenceField);
var email;
var gr = new GlideRecord('sys_user');
if (gr.get(v)) {
email = gr.email;
 }
var url = 'sip:'+email; 
  
var w = getTopWindow(); 
w.open(url);
}
</script>
</j:jelly>
```

## Compatibility

ServiceNow: Tested on New York instance

## Screenshots

Please refer to [SCREENSHOTS.md](https://github.com/naudiri/servicenow_skype/blob/master/SCREENSHOTS.md)
