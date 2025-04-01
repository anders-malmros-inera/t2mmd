```mermaid
sequenceDiagram

participant c as tjänstekonsument
participant agg as Central tjänst
participant idx as Engagemangsindex
participant p1 as tjänsteproducent 1
participant p2 as tjänsteproducent 2
participant p3 as tjänsteproducent 3
participant p4 as tjänsteproducent 4


c->>+agg: callService(searchKey, callback)
agg->>+idx: findInfo(searchKey)
idx-->>-agg: [API1, API2, API3, API4]

note over agg, p4: Alla tjänsteproducenter anropas parallellt
agg->>agg: skapa sessionId
note over agg,p1: Scenario 1: tjänstekonsumentens anrop avses besvaras<br>av tjänsteproducent 1
agg->>+p1: callService(sessionId, searchKey, callback)
p1->>p1: authorize
p1-->>agg: 200 OK
p1->>p1: start processing
note over agg,p2: Scenario 2: tjänstekonsumentens anrop avses besvaras<br>av tjänsteproducent 2
agg->>+p2: callService(sessionId, searchKey, callback)
p2->>p2: authorize
p2-->>agg: 200 OK
p2->>p2:start processing
note over agg,p3: Scenario 3: tjänstekonsumenten bedöms sakna behörighet och<br>anropet nekas
agg->>+p3: callService(sessionId, searchKey, callback)
p3->>p3: authorize
p3-->>-agg: 403 FORBIDDEN
note over agg,p4: Scenario 4: tjänsteproducent 4 lyckas aldrig utvärdera och<br> återkoppla om anropet alls avses besvaras
agg->>p4: callService(sessionId, searchKey, callback)
activate p4
p4->>p4: authorize
note over p4:  auktoriseringen tar för lång tid
note over agg: TIMEOUT för anrop till tjänsteproducent 4
agg->>agg: timeout ~3 sekunder
agg-->>c: Result(sessionId=X, callbacks=2, timeoutby=1)
deactivate agg
deactivate p4

note over c: Nu kan tjänstekonsument ta<br> ställning till timoeuten och<br>välja att begära informationen<br> igen senare
note over p1: Nu levererar tjänsteproducent 1 svar
p1-->>c: callback(sessionId, result1)
deactivate p1
note over p2: Nu levererar tjänsteproducent 2 svar
p2-->>c: callback(sessionId, result2)
deactivate p2
```