# Agent Event Streams<a name="agent-event-streams"></a>

Amazon Connect agent event streams are Amazon Kinesis data streams that provide you with near real\-time reporting of agent activity within your Amazon Connect instance\. The events published to the stream include agent login, agent logout, agent answers a call, and agent status change\.

You can use the agent event streams to create dashboards that display agent information and events, integrate streams into workforce management \(WFM\) solutions, and configure alerting tools to trigger custom notifications of specific agent activity\. Agent event streams help you manage agent staffing and efficiency\.

## Enabling Agent Event Streams<a name="agent-event-streams-enable"></a>

Agent event streams are not enabled by default\. Before you can enable agent event streams in Amazon Connect, create a data stream in Amazon Kinesis Data Streams\. Then, choose the Kinesis stream as the stream to use for agent event streams\. Though you can use the same stream for both agent event streams and contact trace records, managing and getting data from the stream is much easier when you use a separate stream for each\. For more information, see the [Amazon Kinesis Data Streams Developer Guide](http://docs.aws.amazon.com/streams/latest/dev/)\.

**Note**  
If you enable server\-side encryption for the Kinesis stream you select for agent event streams, Amazon Connect cannot publish to the stream because it does not have permission to kms:GenerateDataKey\. No records are published to the stream\.

**To enable agent event streams**

1. Open the Amazon Connect console at [https://console\.aws\.amazon\.com/connect/](https://console.aws.amazon.com/connect/)\.

1. On the console, choose the name in the **Instance Alias** column of the instance for which to enable agent event streams\.

1. Choose **Data streaming**, then select **Enable data streaming**\.

1. Under **Agent Events**, select the Kinesis stream to use, and then choose **Save**\.

## Agent Event Streams Data Model<a name="agent-event-stream-model"></a>

Agent event streams are created in JavaScript Object Notation \(JSON\) format\. For each event type, a JSON blob is sent to the Kinesis data stream\. The following event types are included in agent event streams:
+ LOGIN—An agent login to the contact center\.
+ LOGOUT—An agent logout from the contact center\.
+ STATE\_CHANGE—One of the following changed:
  + Agent configuration, such as profile or the assigned hierarchy group\.
  + Agent state in the contact control panel, such as Available\.
  + Agent conversation state, such as on hold\.
+ HEART\_BEAT—This event is published every 120 seconds if there are no other events published during that interval\.

Each agent event type blob includes the following data about the event\.

### AgentEvent<a name="AgentEvent"></a>

The `AgentEvent` object includes the following properties:

**AgentARN**  
The Amazon Resource Name \(ARN\) for the agent\. To find the ARN for an agent, open the user settings for the user in Amazon Connect\. The ARN is displayed in the address bar\.  
Type: ARN

**AWSAccountId**  
The 12\-digit AWS account ID for the AWS account associated with the Amazon Connect instance\.  
Type: String

**CurrentAgentSnapshot**  
Contains agent configuration, such as username, first name, last name, routing profile, hierarchy groups, contacts, and agent status\.  
Type: `AgentSnapshot` object

**EventId**  
Universally unique identifier \(UUID\) for the event\.  
Type: String

**EventTimestamp**  
A time stamp for the event, in ISO 8601 standard format\.  
Type: String \(*yyyy*\-*mm*\-*dd*T*hh*:*mm*:*ss*Z\)

**EventType**  
The type of event\.   
Valid values: `STATE_CHANGE` \| `HEART_BEAT` \| `LOGIN` \| `LOGOUT` 

**InstanceARN**  
Amazon Resource Name for the Amazon Connect instance in which the agent’s user account is created\.  
Type: ARN

**PreviousAgentSnapshot**  
Contains agent configuration, such as username, first name, last name, routing profile, hierarchy groups\), contacts, and agent status\. Not applicable to LOGIN or LOGOUT events\.  
Type: `AgentSnapshot` object

**Version**  
The version of the agent event stream in date format, such as 2017\-10\-10\.  
Type: String

### AgentSnapshot<a name="AgentSnapshot"></a>

The `AgentSnapshot` object includes the following properties:

**AgentStatus**  
Agent status data, including:  
+ AgentARN—the ARN for the agent\.
+ Name—the name of the status, such as Available or Offline\.
Type: `AgentStatus` object\.

**Configuration**  
Information about the agent, including:   
+ FirstName—the agent's first name\.
+ HierarchyGroups—the hierarchy group the agent is assigned to, if any\.
+ LastName—the agent's last name\.
+ RoutingProfile—the routing profile the agent is assigned to\.
+ Username—the agent's Amazon Connect user name\.
Type: `Configuration` object

**Contacts**  
List of contacts  
Type: `ContactList` object

### Configuration<a name="Configuration"></a>

The `Configuration` object includes the following properties:

**FirstName**  
The first name entered in the agent's Amazon Connect account\.  
Type: String  
Length: 1\-100

**AgentHierarchyGroups**  
The hierarchy group, up to five levels of grouping, for the agent associated with the event\.  
Type: `AgentHierarchyGroups` object

**LastName**  
The last name entered in the agent's Amazon Connect account\.  
Type: String  
Length: 1\-100

**RoutingProfile**  
The routing profile assigned to the agent associated with the event\.  
Type: `RoutingProfile` object\.

**Username**  
The user name for the agent’s Amazon Connect user account\.  
Type: String  
Length: 1\-100

### Contact Object<a name="Contact"></a>

The `Contact` object includes the following properties:

**ContactId**  
UUID identifier for the contact  
Type: String  
Length: 1\-256

**InitialContactId**  
The `ContactId` of the original contact that was transferred\.  
Type: String  
Length: 1\-256

**Channel**  
Enumeration of the method of communication, such as Voice\.  
Valid values: `VOICE`

**InitiationMethod**  
How the contact was initiated\.  
Valid values: `INBOUND` \| `OUTBOUND` \| `TRANSFER` \| `CALLBACK` \| `API` 

**State**  
An enumeration of the state of the contact\.  
Valid values: `INCOMING` \| `PENDING` \| `CONNECTING` \| `CONNECTED` \| `CONNECTED_ONHOLD` \| `MISSED` \| `ERROR` \| `ENDED` 

**StateStartTimestamp**  
A time stamp for the time at which the contact entered the State\.  
Type: String \(*yyyy*\-*mm*\-*dd*T*hh*:*mm*:*ss*Z\)

**ConnectedToAgentTimestamp**  
A time stamp for the time the contact was connected to an agent\.   
Type: String \(*yyyy*\-*mm*\-*dd*T*hh*:*mm*:*ss*Z\)

**QueueTimestamp**  
A time stamp for the time at which the contact was put into a queue\.  
Type: String \(*yyyy*\-*mm*\-*dd*T*hh*:*mm*:*ss*Z\)

**Queue**  
The queue the contact was placed in\.  
Type: `Queue` object

### HierarchyGroup Object<a name="Hierarchygroup-object"></a>

The `HierarchyGroup` object includes the following properties:

ARN  
The Amazon Resource Name for the agent hierarchy\.  
Type: String

Name  
The name of the hierarchy group\.  
Type: String

### AgentHierarchyGroups Object<a name="Hierarchygroups-object"></a>

The `AgentHierarchyGroups` object includes the following properties:

Level1  
Includes details for Level1 of the hierarchy assigned to the agent\.  
Type: `HierarchyGroup` object

Level2  
Includes details for Level2 of the hierarchy assigned to the agent\.  
Type: `HierarchyGroup` object

Level3  
Includes details for Level3 of the hierarchy assigned to the agent\.  
Type: `HierarchyGroup` ob4ject

Level4  
Includes details for Level4 of the hierarchy assigned to the agent\.  
Type: `HierarchyGroup` object

Level5  
Includes details for Level5 of the hierarchy assigned to the agent\.  
Type: `HierarchyGroup` object

### Queue Object<a name="queue-object"></a>

The `Queue` object includes the following properties:

ARN  
Amazon Resource Name for the queue\.  
Type: String

Name  
The name of the queue\.  
Type: String

### RoutingProfile Object<a name="routingprofile"></a>

The `RoutingProfile` object includes the following properties:

ARN  
Amazon Resource Name for the agent's routing profile\.  
Type: String

Name  
The name of the routing profile\.  
Type: String

InboundQueues  
A list of `Queue` objects associated with the agent's routing profile\.  
Type: List of `Queue` object

DefaultOutboundQueue  
The default outbound queue for the agent's routing profile\.  
Type: `Queue` object