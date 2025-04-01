```mermaid
flowchart 
nkrr--GA, GD, GMH, GLOO-->proxy--GA & GD-->Cosmic
proxy--GMH & GD-->PIX2
Cosmic-.->PIX2
proxy--GLOO--->ROS

```