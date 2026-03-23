# Anropa tjänst

```mermaid

sequenceDiagram
    autonumber
    participant Klient
    box rgb(255,255,204) Lös koppling
        participant TC as Tjänstekatalog
    end
    box  rgb(255,255,204) Tillitshantering
        participant KM as Klientmetadatakatalog
    end
    box  rgb(255,255,204) Regel- och avtalsgrund 
        participant FM as Federationsmedlemskatalog
    end
    box  rgb(255,255,204) Åtkomstkontroll
        participant ÅT as Åtkomstintygstjänst
    end
    participant API
 
    Klient->>TC: Sök tjänst
    TC-->>Klient: Tjänsteinformation
    Klient->>ÅT: Begär åtkomstintyg
    ÅT->>FM: Verifiera <br>federationsmedlemskap
    FM-->>ÅT: true/false
    ÅT->>KM: Hämta klientmetadata
    KM-->>ÅT: metadata
    ÅT->>ÅT: ta åtkomstbeslut
    ÅT-->>Klient: åtkomstintygintyg
    Klient->>API: Fråga efter resurs
    API->>API: verifiera åtkomstintyg
    API-->>Klient: resurs
```

# Katalogsynkronisering

```mermaid
sequenceDiagram
    autonumber
    participant LK as Lokal Katalog
    participant CK as Central Katalog

    LK->>CK: Begär uppdateringar sedan viss tidpunkt
    CK-->>LK: Returnera ändringar (delta)
    LK->>LK: Applicera ändringar
    LK->>LK: Uppdatera lokal tidsstämpel
```

# Informationsförsörjningstjänst

```mermaid
sequenceDiagram
    autonumber
    participant K as Klient
    box  rgb(255,255,204) Datainhämtning
        participant AT as Aggregerande tjänst
        participant IT as Informationsindex
        participant FK as Formatkonverterare
    end
    box  rgb(255,255,204) Lös koppling
        participant TC as Tjänstekatalog
    end
    participant TP as API (SOAP alt FHIR)

    K->>AT: Begär data om visst subjekt (tex en invånare)<br>formatval: FHIR/SOAP<br>leverans: aggregerat/strömmat
    AT->>IT: Fråga vilka producenter <br>som har data
    IT-->>AT: Lista av producenter
    
    loop För varje producent
        AT->>TC: Sök efter tjänst som stödjer<br>aktuell information
        TC-->>AT: Tjänsteinformation
    end
    
    par Parallella anrop
        AT->>TP: Hämta data 
        TP-->>AT: data
        AT->>FK: konvertera data till önskat format
        FK-->>AT:konverterat data

        opt Strömmat svar önskat
            AT-->>K: delsvar
        end
    end
    
    opt Aggregerat svar önskat
        AT->>AT: Aggregera data från alla källor
        AT-->>K: aggregerat svar
    end
```

# Lösningsarkitektur

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
graph 
    subgraph tk[Tjänstekonsument]
        tkc(Klient)
    end
    subgraph si[Samverkansinfrastrukturen]
        sias(Åtkomstintygstjänst)
        sitk(Tjänstekatalog)
%%        siagg(Aggregerande tjänst)
%%        siii(Informationsindex)
        sifmk(Federationsmedlemskatalog)
        sikk(Klientmetadatakatalog)
%%        sifk(Formatkonverterare)
    end
    subgraph tp[Tjänsteproducent]
        tpas(Åtkomstintygstjänst)
        tpapi(API)
    end
    tkc-- 1. -->sias
    tkc-- 2. -->sitk
    tpas-- 3.2 -->sifmk
    tpas-- 3.1 -->sikk
    tkc-- 3. -->tpas
    tkc-- 4. -->tpapi  
    
```


```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
graph 
    subgraph tk[Tjänstekonsument]
        tkc(Klient)
    end
    

    subgraph si[Samverkansinfrastrukturen]
        subgraph si1[Komponenter]
            sias(Åtkomstintygstjänst) & sitk(Tjänstekatalog) & siagg(Aggregerande tjänst)
            ~~~
            siii(Informationsindex) & sifmk(Federationsmedlemskatalog) & sikk(Klientmetadatakatalog) & sifk(Formatkonverterare)
        end
        subgraph drift[Driftplattform]
            gw(API Gateway)~~~ap(API Management)

        end
        subgraph ntjp[Nationell tjänsteplattform]
            vp(Virtualisering) & ag(Aggregering) & anp(Anpassning)
            ~~~
            ei(Engagemangsindex) & tak(Tjänsteadressering)
        end
    end
    subgraph tp[Tjänsteproducent]
        tpas(Åtkomstintygstjänst)
        tprtp(Regional tjänsteplattform)
        tpfs(FHIR server)
    end
    %%tk --> si --> tp
    tkc-->si-->tp

```

# Total lösningsarkitektur
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%

```mermaid
graph LR

classDef extern fill:#F8E5A0
classDef box fill:#ffffff,stroke:#000000

subgraph i[Inera]
    subgraph itk[Inera-tjänster]
        NPÖ
        mmm(...)
        Journalen
    end

    subgraph si[Samverkansinfrastrukturen]
        subgraph siit[Inera informationsförsörjning]
            svodc[SVOD-tjänst]
            ehdsc[EHDS-tjänst]
            jc[Invånartjänst]
        end
        subgraph si1[T2-stödtjänster]
            sitk(Tjänstekatalog)
            siii(Informationsindex)
            sifmk(Federationsmedlemskatalog)
        end
        subgraph si2[FHIR<-->BP2.1]
            sifk(Formatkonverterare)
        end
        subgraph siiam[IAM-komponenter]
            sias(Åtkomstintygstjänst)
            sikk(Klientmetadatakatalog)
            sir(Inera-resolver)
        end
        subgraph ntjp[Nationell tjänsteplattform]
            vp(Virtualiserings-<br>plattform)
            ag(Aggregerings-<br>plattform)
            anp(Anpassnings-<br>plattform)
            ei(Engagemangsindex)
            tak(Tjänsteadresserings-<br>katalog)
        end
        subgraph apim[APIM]
            subgraph dp[Dataplan]
                gw(API Gateway)
            end
            subgraph cp[Kontrollplan]
                cp1(Utvecklarportal)
                cp2(API-regelverk)
                cp3(Uppföljning & analys)
                cp4(Livscykelhantering)
                cp5(Anslutning)
                cp6(Trafikbegränsning)
                cp7(Integrations- och governance‑kapabiliteter)
            end
        end
        cp1 & cp2 & cp4 & cp5 & cp6 & cp7 -->gw -->cp3
    end
    si:::box

    subgraph itp[Inera-tjänster]
        Formulärtjänsten
        mm(...)
        Födelseanmälan

    end

    subgraph drift[Ineras driftplattform]
        subgraph kk[Kubernetes kluster]
            s[...]
        end
    end
end
i:::box

subgraph tk[Tjänstekonsument]
    tkc(Klient)
end
tk:::extern

subgraph tp[Tjänsteproducent]
    tpas(Åtkomstintygstjänst)
    tprtp(Regional tjänsteplattform)
    tpfs(FHIR server)
end
tp:::extern


subgraph ndi[Nationell Digital Infrastruktur]
    ntk(Nationell tjänstekatalog)
    pdi(Patientdataindex)
end
ndi:::extern

subgraph sib[Samordnad identitet och behörighet]
    res(Resolver)
    oi([OpenID Connect-profil, oidc.se])
    o2([OAuth2-profil, Ena])
    of([OpenID Federation-profil, oidc.se])
end
sib:::extern


sias-.realiserar.->o2
sitk-.modelleras efter.-ntk
siii-.modelleras efter.-pdi
sikk-.linjerar med.->of
sir-.linjerar med.-res
sias-->sir-->sikk
ag-->vp & ei & tak
vp-->anp & ag & ei & tak
siit-->vp  & siii & sifk & sifmk & sitk
vp--anropar-->tprtp
si--driftas på-->drift 
tkc & itk -->siit--anropar-->tpas & tpfs
tkc & itk-->sias
tkc--anropar-->itp
si~~~itp
apim--integrerar med-->siiam

```
