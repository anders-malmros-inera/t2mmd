# Samverkansinfrastrukturen

## Lösningsarkitektur

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

subgraph tk[Extern part]
    tkc(API-klient)
end

subgraph tp[Extern part]
    tpas(Åtkomstintygstjänst)
    tprtp(Regional tjänsteplattform)
    tpfs(FHIR server)
end

sias-->sifmk
sias-->sikk 

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
style sifmk stroke-dasharray: 5 5


%% Formatting for elk renderer
%% sifmk ~~~ tpas

%% Formatting for dagre (standard) renderer
vp ~~~ infra
tkc ~~~ itk
APIM ~~~ tp
```

## Komponenter med externa beroenden

```mermaid
graph TB
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%

subgraph inera[Inera]

    subgraph app[Teknik - applikation]
        sias(Åtkomstintygstjänst)
        sikk(Klientmetadatakatalog)
        sitk(Tjänstekatalog)
        siii(Informationsindex)
    end

end

subgraph ndi[Nationell Digital Infrastruktur, EHM]
    ntk(Nationell tjänstekatalog)
    pdi(Patientdataindex)
end

subgraph sib[Samordnad identitet och behörighet, Digg]
    oi(OpenID Connect-profil, oidc.se)
    o2(OAuth2-profil, Ena)
    of(OpenID Federation-profil, oidc.se)
end

sias -.-> o2 & oi
sikk -.-> of
sitk -.-> ntk
siii -.-> pdi


style inera fill:#FFFFFF,stroke:#000000
style app fill:#76b3e8,stroke:#000000
style ndi fill:#FFFFFF,stroke:#000000
style sib fill:#FFFFFF,stroke:#000000

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

