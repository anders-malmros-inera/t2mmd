```mermaid
sequenceDiagram
autoNumber
participant u as Kalle<br>PNR=790909-5678
box 1177.se
    participant c as Journalen
end

box NTjP
    participant agg as AgP - GALFP
    participant ei as EI
end
box Norrbotten
    participant rtp as RTP
    participant vas as VAS
    participant lp as LogPoint
end
note over ei, lp: Engagemangsindex uppdateras löpande
par Vid loggningshändelser
    vas->>rtp: Update(GALFP, 790909-5678)
    rtp->>ei: Update(GALFP, 790909-5678)
    ei-->>rtp: 
    rtp-->>vas: 
and
    lp->>rtp: Update(GALFP, 790909-5678)
    rtp->>ei: Update(GALFP, 790909-5678)
    ei-->>rtp: 
    rtp-->>lp: 
end

note over c, lp: Visning av åtkomstloggar via 1177.se
u->>c: Visa åtkomster
c->>agg: GALFP(790909-5678)
agg->>ei: findContent(GALFP, 790909-5678)
ei-->>agg: [SE1623-VAS, SE1623-LogPoint]
par parallella anrop
    agg->>rtp: GLAFP(790909-5678)
    rtp->>vas: GLAFP(790909-5678)
    vas-->>rtp: [vasloggar]
    rtp-->>agg: [vasloggar]
and
    agg->>rtp: GLAFP(790909-5678)
    rtp->>lp: GLAFP(790909-5678)
    lp-->>rtp: [logpointloggar]
    rtp-->>agg: [logpointloggar]
end
agg->>agg: aggregera svar
agg-->>c: [vasloggar, logpointloggar]
c-->>u: åtkomster visas
```
1. Norrbotten har anslutit sin RTP som tjänstekonsument till EI:Update. Detta betyder att vi utifrån TAKningarna inte ser vilka bakomliggande system som skriver EI-poster.
1. Vi antar att Norrbottens RTP idag skriver EI-poster för GALFP på uppdrag av VAS
1. Norrbotten kan utan ny TAKning börja skriva EI-poster för GALFP på uppdrag av LogPoint 
1. Norrbotten - VAS är redan ansluten som producent av GALFP
1. Norrbotten LogPoint behöver anslutas som producent av GALFP och då med ett HSA-Id för det källsystemet

```mermaid
sequenceDiagram
autoNumber
participant u as Kalle<br>PNR=790909-5678
box Inera
    participant c as Journalen
    participant tak as TAK
    participant l as Inera Loggtjänst
end
box Norrbotten
    participant rtp as RTP
    participant vas as VAS
    participant lp as LogPoint
end
participant o as Övriga loggproducenter

note over c, lp: Visning av åtkomstloggar via 1177.se
u->>c: Visa åtkomster
c->>tak: getLogicalAddressesByServiceContract(GALFP)
tak-->>c: [SE1623-VAS, SE1623-LogPoint, Logg-Inera, SE2321-Västmanland, ...osv] 
c->>c: intern filtrering av<br>tjänsteproducenter
par parallella anrop
    c->>rtp: GLAFP(790909-5678)
    rtp->>vas: GLAFP(790909-5678)
    vas-->>rtp: [vasloggar]
    rtp-->>c: [vasloggar]
and
    c->>rtp: GLAFP(790909-5678)
    rtp->>lp: GLAFP(790909-5678)
    lp-->>rtp: [logpointloggar]
    rtp-->>c: [logpointloggar]
and
    c->>l: GLAFP(790909-5678)
    l-->>c: [ineraloggar]
and Övriga loggproducenter
    c->>o: GALFP(790909-5678)
    o-->>c: [] (tomt svar)
end
c-->>u: åtkomster visas
```
1. Norrbotten - VAS är redan ansluten som producent av GALFP
1. Norrbotten LogPoint behöver anslutas som producent av GALFP och då med ett HSA-Id för det källsystemet
1. LogPoint behöver registreras att inkluderas av Joournalens aggregering
1. Journalen kommer ställa förfrågningar till både VAS och LogPoint