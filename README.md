# Events

## UI

## Index

![Index](./images/Events.Index.png)

### Rules

- List of all events where `Resolved IS NULL`
- `Duration` is `Today - Reported`, formatted in days and hours

### Features

- Accessible via `Events` menu
- Allow `Type`, `Reported`, `Duration` to be sorted by clicking the column header
- Allow events to be filtered by `Type`, `Employees`, `Vehicles`, `Reported`, `Tags`


## Edit/Create

![Edit](./images/Events.Edit.png)

### Features

- Toggling any switch (`AtFault`,`InYard`,`Owned`,`Damaged`,`Scratched`,`Towed`,`Injury`,`Preventable`,`Recordable`) recalculates `Points`

### Point System

AtFault|InYard|Owned|Damaged|Scratched|Towed|Injury|Preventable|Recordable|Points
---|---|---|---|---|---|---|---|---|---
||||||||||0
|||||||||X|1.5
||||||||X||0.5
|||||||X|||1.0
||||||X||||0.5
||||X||||||0.34

## Model (logical)

### `Event`
Abstracted model for persisting events
name|data type|comments
---|---|---
Id|`int`|unique identifier
Type|`EventType`|`Accident`, `Customer Service`, `Vehicle Breakdown`
AtFault|`bool`|
InYard|`bool`|
Owned|`bool`|
Damaged|`bool`|
Scratched|`bool`|
Towed|`bool`|
Injury|`bool`|
Preventable|`bool`|
Recordable|`bool`|
Occurred|`date`|
Reported|`date`| `>= Occcurred`
ReportedBy|`Employee`|`Employee` that reported the event
EstimatedCost|`float`|`>0` or `null`
Issue|`string`|
Priviledged|`bool`|
Employees|`Employee[]`|zero or more `Employee`s
Vehicles|`Vehicle[]`|zero or more `Vehicle`s
Tags|`Tag[]`|zero or more `Tags`
Location|`string`|free-entry
Latitude|`float`|geocoded [wgs84] `Location`
Longitude|`float`|geocoded [wgs84] `Location`
Resolved|`date`|`>= Reported`
ResolvedBy|`Employee`|`Employee` that resolved the incident
Resolution|`string`|
ActualCost|`float`|`>0` or `null`
Attachments|`Attachment[]`|zero or more `Attachment`s

### `Attachment`
Binary files (audio|video|pictures|documents) related to the `Event`
name|data type|comments
---|---|---
Name|`string`|
Data|`byte[]`|
MimeType|`string`|example: `application/pdf`

### `Employee`
The BambooHr employee model

### `EventType`
name|data type|comments
---|---|---
Name|`string`|

### `Tag`
Flexible categorization
name|data type|comments
---|---|---
Name|`string`|

### `Vehicle`
Bus, truck, etc.
name|data type|comments
---|---|---
Name|`string`|
Active|`bool`|

## Model (physical; relational)

### `Event`
- `Event >-- Employee` (Reported)
- `Event >-- Employee` (Resolved)
- `Event --< EventAttachment`
- `Event --< EventEmployee`
- `Event --< EventTag`
- `Event >-- EventType`
- `Event --< EventVehicle`

name|data type|comments
---|---|---
Id|`int`|primary key
EventTypeId|`int`|foreign key
ReportedById|`int`|foreign key
ResolvedById|`int`|foreign key

### `EventType`
- `EventType --< Event`

name|data type|comments
---|---|---
Id|`int`|primary key
Name|`string`|primary key

### `EventTag`
- `EventTag >-- Event`
- `EventTag >-- Tag`

name|data type|comments
---|---|---
EventId|`int`|primary key
TagId|`int`|primary key

### `Tag`
- `Tag --< EventTag`

name|data type|comments
---|---|---
Id|`int`|primary key
Name|`string`|

### `EventAttachment`
- `EventAttachment >-- Event`

name|data type|comments
---|---|---
Id|`int`|primary key
EventId|`int`|foreign key
Name|`string`|
MimeType|`string`|`application/pdf`,`image/png`
Data|`byte[]`|byte-encoded data

### `EventEmployee`
- `EventEmployee >-- Event`
- `EventEmployee >-- Employee`

name|data type|comments
---|---|---
IncidentId|`int`|primary key
EmployeeId|`int`|primary key; (BambooHr key)

### `Employee`
- `Employee --< EventEmployee`
- `Employee --< Event` (Reported)
- `Employee --< Event` (Resolved)

name|data type|comments
---|---|---
Id|`int`|primary key

### `EventVehicle`
- `EventVehicle >-- Event`
- `EventVehicle >-- Vehicle`

name|data type|comments
---|---|---
EventId|`int`|primary key
VehicleId|`int`|primary key

### `Vehicle`
- `Vehicle --< EventVehicle`

name|data type|comments
---|---|---
Id|`int`|primary key
Name|`string`|

## Questions

- workflow triggers based on data values?
- webhooks?
- events?
