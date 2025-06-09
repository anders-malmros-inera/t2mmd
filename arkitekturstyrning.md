# Inledning
- Samverkansarkitekturen styr utformningen av interoperabla gränssnitt för samverkan över organisationsgränser (Inera-kund, kund-Inera, kund-kund) för interoperabla tjänster där Inera agerar tjänsteleverantör/federationsoperatör. 
- Samverkansarkitekturen stöttar realiseringar via anvisningar och vägledningar, samt tolkningsstöd av dessa.
- Inera tjänstearkitektur styr Ineras tjänsters realisering och säkerställer följsamhet mot interna riktlinjer och beslutad samverkansarkitektur.
- Arkitekturella beslut om avsteg från arkitekturella ramar ska dokumenteras i dels SAD (för avsteg från interna riktlinjer), dels som ett arkitekturellt beslut i interopspec/TKB (för avsteg från samverkansarkitekturella anvisningar). Beslut ska eskaleras minst till chefsarkitektsnivå.
- Ineras ledning beslutar om vilka tjänster som ska etableras, ofta baserat på intresseanmälan och avsiktsförklaring
- Ineras ledning beslutar om vilka tjänster som ska etableras, ofta baserat på intresseanmälan och avsiktsförklaring

# Alternativ 1
**NTjP & T2-stödtjänster ses som vilka andra tjänster som helst**

```mermaid
graph LR

k(Kunder)
rar(Regionernas Arkitekturråd)
kar(Kommunernas arkitekturråd)
ref(Referensgrupp tjänsteplattform)
pr(Programråden)
rg(Referensgrupper)

k --> rar & kar & ref & pr & rg

subgraph Inera
    subgraph iis[ Ineras tjänster ]
        säk(Säkerhetstjänster) & 1177(1177 e-tjänster) & x(...)
        ~~~
        ntjp(Nationell tjänsteplattform) & t2(T2-stödtjänster)

    end
    subgraph s[Samverkansarkitekturen]
    end
    ta(Tjänstearkitektur) -- styr realisering -->iis(Ineras tjänster) 
end

pr --beställer tjänster och extrafinansierade initiativ -->Inera
rar & kar & ref -- framför behov -->s 
ref -- framför förvaltningsbehov -->iis
rg -- framför förvaltningsbehov  ---> iis

s -- styr utformning av externa gränssnitt -->iis
s -- vägleder utformning av externa gränssnitt -->es(Interoperabla tjänster inom svensk välfärd)
iis--stödjer realisering-->es

```

# Alternativ 2
**NTjP och T2-stödtjänster ses inte som Inera-tjänster utan som "samverkansstödtjänster"**

Kan ej rekommenderas eftersom det skapar en otydlighet kring tjänster som HSA och SITHS som är centrala för samverkan men också är självstående tjänster.

```mermaid
graph LR

k(Kunder)
rar(Regionernas Arkitekturråd)
kar(Kommunernas arkitekturråd)
ref(Referensgrupp tjänsteplattform)
pr(Programråden)
rg(Referensgrupper)

k --> rar & kar & ref & pr & rg

subgraph Inera
    subgraph si[Samverkansstödtjänster]
        ntjp(Nationell tjänsteplattform) 
        t2(T2-stödtjänster)
    end
    subgraph iis[ Ineras tjänster ]
        1177(1177 e-tjänster)
        säk(Säkerhetstjänster)
        x(...)
    end
    subgraph s[Samverkansarkitektur]
    end
    s -- styr utformning av externa gränssnitt hos -->si
    s -- styr realisering av --> si
    ta(Tjänstearkitektur) -- styr realisering av -->iis(Ineras tjänster) 
    s -- styr utformning av externa gränssnitt hos -->iis
end

pr --beställer tjänster och extrafinansierade initiativ -->Inera
rar & kar & ref -- framför behov -->s 
ref -- framför behov -->si
rg -- framför förvaltningsbehov till ---> iis

s -- vägleder utformning av externa gränssnitt hos -->es(Interoperabla tjänster inom svensk välfärd)
iis --stödjer realisering av-->es
si --stödjer realisering av-->es


```
