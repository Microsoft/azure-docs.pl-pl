---
title: Konwertowanie ontologi
titleSuffix: Azure Digital Twins
description: Opis procesu konwertowania standardowych modeli branżowych na język DTDL na Azure Digital Twins
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 22b41fce59bf7dbe9db1186036c5ed44f07a4aad
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484487"
---
# <a name="convert-industry-standard-ontologies-to-dtdl-for-azure-digital-twins"></a>Konwertowanie standardowych w branży na dtdl na Azure Digital Twins

Większość [onlogi opiera](concepts-ontologies.md) się na semantycznych standardach sieci Web, takich jak [OWL,](https://www.w3.org/OWL/) [RDF](https://www.w3.org/2001/sw/wiki/RDF)i [RDFS.](https://www.w3.org/2001/sw/wiki/RDFS) 

Aby można było używać modelu z Azure Digital Twins, musi on być w formacie DTDL. W tym artykule opisano ogólne  wskazówki dotyczące projektowania w postaci wzorca konwersji modeli opartych na technologii RDF na dtdl, aby można było ich używać z Azure Digital Twins. 

Artykuł zawiera również przykładowy kod [**konwertera**](#converter-samples) dla konwerterów RDF i OWL, który można rozszerzyć dla innych schematów w branży budynku.

## <a name="conversion-pattern"></a>Wzorzec konwersji

Istnieje kilka bibliotek innych firm, które mogą być używane podczas konwertowania modeli opartych na technologii RDF na dtdl. Niektóre z tych bibliotek umożliwiają załadowanie pliku modelu do grafu. Możesz przejść przez graf w pętli, szukając konkretnych konstrukcji RDFS i OWL, i przekonwertować je na DTDL.   

W poniższej tabeli przedstawiono przykład sposobu, w jaki można mapować konstrukcje RDFS i OWL na dtdl. 

| Koncepcja RDFS/OWL | Konstrukcja RDFS/OWL | Koncepcja DTDL | Konstrukcja DTDL |
| --- | --- | --- | --- |
| Klasy | `owl:Class`<br>Sufiks IRI<br>``rdfs:label``<br>``rdfs:comment`` | Interfejs | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment` 
| Podklasy | `owl:Class`<br>Sufiks IRI<br>`rdfs:label`<br>`rdfs:comment`<br>`rdfs:subClassOf` | Interfejs | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment`<br>`extends` 
| Właściwości typu danych | `owl:DatatypeProperty`<br>`rdfs:label` lub `INode`<br>`rdfs:label`<br>`rdfs:range` | Właściwości interfejsu | `@type:Property`<br>`name`<br>`displayName`<br>`schema` 
| Właściwości obiektu | `owl:ObjectProperty`<br>`rdfs:label` lub `INode`<br>`rdfs:range`<br>`rdfs:comment`<br>`rdfs:label` | Relacja | `type:Relationship`<br>`name`<br>`target` (lub pominięty, jeśli nie `rdfs:range` )<br>`comment`<br>`displayName`<br>

Poniższy fragment kodu w języku C# pokazuje, jak plik modelu RDF jest ładowany do grafu i konwertowany na dtdl przy użyciu [**biblioteki dotNetRDF.**](https://www.dotnetrdf.org/) 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/convertRDF.cs":::

## <a name="converter-samples"></a>Przykłady konwerterów

### <a name="rdf-converter-application"></a>Aplikacja konwertera RDF 

Dostępna jest przykładowa aplikacja, która konwertuje plik modelu oparty na funkcji RDF na język [DTDL (wersja 2)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) do użycia przez usługę Azure Digital Twins RDF. Został on zweryfikowany pod względem schematu [Brick](https://brickschema.org/ontology/) i można go rozszerzyć na inne schematy w branży budynku (takie jak BOT [(Building Topology Ontology),](https://w3c-lbd-cg.github.io/bot/) [Semantic Sensor Network](https://www.w3.org/TR/vocab-ssn/)lub [buildingSmart Industry Foundation Classes (IFC)](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/)).

Przykład to aplikacja wiersza polecenia .NET Core o nazwie **RdfToDtdlConverter.**

Przykład można pobrać tutaj: [**RdfToDtdlConverter.**](/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/) 

Aby pobrać kod na komputer,  wybierz przycisk Przeglądaj kod pod tytułem na stronie przykładu, co spowoduje pobranie przykładu do repozytorium GitHub. Wybierz przycisk **Kod** i pobierz **plik ZIP,** aby pobrać przykład jako *plik . Plik ZIP* o *nazwieRdfToDtdlConverter-main.zip*. Następnie możesz rozpakować plik i eksplorować kod.

:::image type="content" source="media/concepts-ontologies-convert/download-repo-zip.png" alt-text="Zrzut ekranu przedstawiający repozytorium RdfToDtdlConverter w witrynie GitHub. Po wybraniu przycisku Kod zostanie wyświetlone małe okno dialogowe, w którym wyróżniony jest przycisk Pobierz plik ZIP." lightbox="media/concepts-ontologies-convert/download-repo-zip.png":::

Możesz użyć tego przykładu, aby zobaczyć wzorce konwersji w kontekście i mieć jako blok blokowy dla własnych aplikacji wykonujących konwersje modeli zgodnie z własnymi potrzebami.

### <a name="owl2dtdl-converter"></a>Konwerter OWL2DTDL 

Konwerter [**OWL2DTDL**](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/OWL2DTDL) to przykład, który tłumaczy ontologię OWL na zestaw deklaracji interfejsu DTDL, których można używać z usługą Azure Digital Twins OWL. Działa ona również w przypadku sieci ontologii, które są wykonane z jednej głównej ontologii, ponownie przy użyciu innych nalogi za pośrednictwem `owl:imports` deklaracji.

Ten konwerter został użyty do tłumaczenia [ontologii](https://doc.realestatecore.io/3.1/full.html) rdzenia nieruchomości na dtdl i może być używany do dowolnej ontologii opartej na OWL.

## <a name="next-steps"></a>Następne kroki 

* Dowiedz się więcej na temat rozszerzania standardowych w branży ontologi w celu spełnienia twoich wymagań: [*Concepts: Extending industry ontologies (Pojęcia: rozszerzanie nalogi branżowe).*](concepts-ontologies-extend.md)

* Możesz też kontynuować ścieżkę tworzenia modeli na podstawie ontologi: [*Używanie strategii ontologii w ścieżce projektowania modelu*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).