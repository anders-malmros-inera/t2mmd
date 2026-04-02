# Förenklad lösningsarkitektur

```mermaid
graph TB
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%

subgraph inera[Inera]

    subgraph app[Teknik - applikation]
        subgraph itk[Inera-tjänst]
            itk1(API-klient)
        end

        itp1(Syftesspecifikt API)

        sias(Åtkomstintygstjänst)
        sikk(Klientmetadatakatalog)
        sitk(Tjänstekatalog)
        siii(Informationsindex)
        sifmk(Federationsmedlemskatalog)
        sifk(Formatkonverterare)

        vp(Nationell tjänsteplattform)

    end

    subgraph infra[Teknik - infrastruktur]
        drift[Inera driftmiljö]
        APIM
    end
end

subgraph tk[Extern tjänst]
    tkc(API-klient)
end

subgraph tp[Extern tjänsteproducent]
    tpas(Åtkomstintygstjänst)
    tprtp(Regional tjänsteplattform)
    tpfs(FHIR server)
end

subgraph ndi[Nationell Digital Infrastruktur, EHM]
    ntk(Nationell tjänstekatalog)
    pdi(Patientdataindex)
end

subgraph sib[Samordnad identitet och behörighet, Digg]
    res(Resolver)
    oi(OpenID Connect-profil, oidc.se)
    o2(OAuth2-profil, Ena)
    of(OpenID Federation-profil, oidc.se)
end

sias-.->sikk & sifmk
sias -.-> o2
sikk -.-> of
sitk -.-> ntk
siii -.-> pdi

%% begär åtkomst
itk1 -->sias & itp1

%% hämta lokaliseringsinfo och logiskt adresserade producent-APIer
itp1 --> siii & sitk

%% slå upp anropsadress om man vill anropa producent-API direkt
itk1 --> sitk

%% Anropa externa REST-APIer
itp1 --> tpas & tpfs

%% Anropa externa BP2.1-tjänster
itp1 --> vp --> tprtp

%% anropa producent-API för att göra diret anrop eller hämta refererad data i aggregerat svar
itk1 --> tpas & tpfs & tprtp

%% extern konsument anropar Inera-API 
tkc --> sias & itp1

%% extern konsument anropar producent-API direkt för att hämta refererad data
tkc --> tpas & tpfs  & tprtp

%% Konvertera svar till det format (BP2.1/FHIR) som klienten önskar
itk1 & itp1 --> sifk



style inera fill:#FFFFFF,stroke:#000000
style app fill:#76b3e8,stroke:#000000
style infra fill:#76b3e8,stroke:#000000
style tk fill:#F8E5A0
style itk fill:#F8E5A0
style tp fill:#F8E5A0
style ndi fill:#FFFFFF,stroke:#000000
style sib fill:#FFFFFF,stroke:#000000
style sifmk stroke-dasharray: 5 5


%% Formatting for elk renderer
sifmk ~~~ tpas

%% Formatting for dagre (standard) renderer
```
# Scenarion
<b>Not:</b> Med "syftesspecifikt API" ned avses att förmedlad data filtreras och kompletteras utifrån en specifik tillämpning inom ett specifikt lagrum

1. <b>Inera-tjänst anropar logiskt adresserad tjänsteproducent-API direkt</b><br>
a) tjänsten begär åtkomst till Tjänstekatalogen från Ineras Åtkomstintygstjänst<br>
b) tjänsten hämtar metadata om tjänsteproducentens API från Tjänstekatalog<br>
c) tjänsten begär åtkomst till API från tjänsteproducents AS<br>
d) tjänsten anropar tjänsteproducents API<br>
e) tjänsten hämtar refererad information från tjänsteproducentens API<br>
f) tjänsten konverterar information till 
<br>

1. <b>Inera-tjänst anropar syftesspecifikt aggregerande API</b><br>
...TBC<br>
<br>
1. <b>Extern tjänst anropar syftesspecifik aggregerande API hos Inera</b><br>
...TBC<br>
<br>

1. <b>Extern tjänst anropar syftesspecifik API som inte aggregerar information</b><br>
...TBC<br>
<br>

# Version inför workshop 1/4
```mermaid
graph TB
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%

subgraph inera[Inera]

    subgraph app[Teknik - applikation]

        subgraph itk[Inera-tjänster]
            itk1(Annan syftesspecifik<br>Inera-tjänst)
            itk2(NPÖ)
            itk3(Journalen)
        end

        subgraph siiam[IAM-komponenter]
            sias(Åtkomstintygstjänst)
            sikk(Klientmetadatakatalog)
            sir(Inera-resolver)
        end

        subgraph siit[Informationsförsörjning]
            svodc(Aggregerande tjänst)
        end

        subgraph si1[T2-stödtjänster]
            sitk(Tjänstekatalog)
            siii(Informationsindex)
            sifmk(Federationsmedlemskatalog)
        end

        subgraph si2[FHIR/BP2.1]
            sifk(Formatkonverterare)
        end

        subgraph ntjp[Nationell tjänsteplattform]
            vp(Virtualiseringsplattform)
            ag(Aggregeringsplattform)
            anp(Anpassningsplattform)
            ei(Engagemangsindex)
            tak(Tjänsteadresseringskatalog)
        end

        subgraph itp[Inera-API:er]
            itp1(Formulärtjänsten)
            itp2(Födelseanmälan)
            itp3(Annat syftespecifikt<br>Inera-API)
            itp4(EHDS-brygga)
        end

        subgraph cp[WSO2 Kontrollplan]
            PUB[API Publisher]
            DEV[API Developer Portal]
            ADM[Admin Portal]
            KEY[Key Manager]
            ANA[API Analytics]
            SC[Service Catalog]
        end

    end

    subgraph infra[Teknik - infrastruktur]

        subgraph drift[Inera drift]
            kubernetes(Kubernetes-kluster)
            ~~~
            Servrar
            ~~~
            Databaser
            ~~~
            Nätverk
        end
    
        subgraph DP[WSO2 Data Plane]
            GW[API Gateway]
            TM[Traffic Manager]
        end

        subgraph IL[WSO2 Integration Layer]
            MI[Micro Integrator]
            SI[Streaming Integrator]
        end
    end
end

subgraph tk[Extern tjänstekonsument]
    tkc(Klient)
end

subgraph tp[Extern tjänsteproducent]
    tpas(Åtkomstintygstjänst)
    tprtp(Regional tjänsteplattform)
    tpfs(FHIR server)
end

subgraph ndi[Nationell Digital Infrastruktur, EHM]
    ntk(Nationell tjänstekatalog)
    pdi(Patientdataindex)
end

subgraph sib[Samordnad identitet och behörighet, Digg]
    res(Resolver)
    oi(OpenID Connect-profil, oidc.se)
    o2(OAuth2-profil, Ena)
    of(OpenID Federation-profil, oidc.se)
end


SC --> PUB
cp-->GW

GW --> MI
GW --> SI
TM --> GW

sias -.-> o2
sitk -.-> ntk
siii -.-> pdi
sikk -.-> of
sir -.-> res
sias --> sir
sir --> sikk
ag --> vp
ag --> ei
ag --> tak
vp --> anp
vp --> ag
vp --> ei
vp --> tak
svodc --> vp
svodc --> siii
svodc --> sifk
svodc --> sifmk
svodc --> sitk
vp ----> tprtp
tkc --> itp1 & itp2 & itp3 & itp4  --> svodc
itk1 & itk2 & itk3 --> svodc
svodc --> tpas
svodc --> tpfs
tkc --> sias
itk1 & itk2 & itk3 --> sias

style inera fill:#FFFFFF,stroke:#000000
style app fill:#76b3e8,stroke:#000000
style infra fill:#76b3e8,stroke:#000000
style tk fill:#F8E5A0
style tp fill:#F8E5A0
style ndi fill:#FFFFFF,stroke:#000000
style sib fill:#FFFFFF,stroke:#000000
style KEY stroke-dasharray: 5 5

%% Formatting for elk renderer

%% Formatting for dagre (standard) renderer
sias~~~~~~~~~ndi & sib & tp
tak~~~~GW
```

## Gammal översikt

```mermaid
graph TB
    subgraph tk[Tjänstekonsument]
        tkc(Klient)
    end
    

    subgraph si[Samverkansinfrastrukturen]
        direction TB
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
    tk ~~~ si ~~~ tp
```

