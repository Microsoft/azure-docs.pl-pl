---
title: Konwertowanie ontologie
titleSuffix: Azure Digital Twins
description: Poznaj proces konwersji modeli branżowych na DTDL for Azure Digital bliźniaczych reprezentacji
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: aa4dde51c077152dd5c8a938ad64ad0a051f89ad
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100561753"
---
# <a name="convert-industry-standard-ontologies-to-dtdl-for-azure-digital-twins"></a>Konwertuj Standard Industry ontologie na DTDL for Azure Digital bliźniaczych reprezentacji

Większość [ontologie](concepts-ontologies.md) jest oparta na semantycznych standardach sieci Web, takich jak [Owl](https://www.w3.org/OWL/), [RDF](https://www.w3.org/2001/sw/wiki/RDF)i [RDFS](https://www.w3.org/2001/sw/wiki/RDFS). 

Aby używać modelu z usługą Azure Digital bliźniaczych reprezentacji, musi on być w formacie DTDL. W tym artykule opisano ogólne wskazówki dotyczące projektowania w postaci **wzorca konwersji** służącego do konwertowania modeli opartych na RDF na DTDL, dzięki czemu mogą one być używane z usługą Azure Digital bliźniaczych reprezentacji. 

Artykuł zawiera również [**przykładowy kod konwertera**](#converter-samples) dla konwerterów RDF i Owl, które można rozszerzyć dla innych schematów w branży budowlanej.

## <a name="conversion-pattern"></a>Wzorzec konwersji

Istnieje kilka bibliotek innych firm, których można używać podczas konwertowania modeli opartych na RDF na DTDL. Niektóre z tych bibliotek umożliwiają załadowanie pliku modelu do grafu. Możesz przechodzić przez Graf, szukając określonych konstrukcji RDFS i OWL, i przekonwertować je na DTDL.   

W poniższej tabeli przedstawiono przykład sposobu mapowania konstrukcji RDFS i OWL na DTDL. 

| Koncepcja RDFS/OWL | Konstrukcja RDFS/OWL | Koncepcja DTDL | Konstrukcja DTDL |
| --- | --- | --- | --- |
| Klasy | `owl:Class`<br>IRI sufiks<br>``rdfs:label``<br>``rdfs:comment`` | Interfejs | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment` 
| Podklasy | `owl:Class`<br>IRI sufiks<br>`rdfs:label`<br>`rdfs:comment`<br>`rdfs:subClassOf` | Interfejs | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment`<br>`extends` 
| Właściwości typu danych | `owl:DatatypeProperty`<br>`rdfs:label` lub `INode`<br>`rdfs:label`<br>`rdfs:range` | Właściwości interfejsu | `@type:Property`<br>`name`<br>`displayName`<br>`schema` 
| Właściwości obiektu | `owl:ObjectProperty`<br>`rdfs:label` lub `INode`<br>`rdfs:range`<br>`rdfs:comment`<br>`rdfs:label` | Relacja | `type:Relationship`<br>`name`<br>`target` (lub pomijane, jeśli nie `rdfs:range` )<br>`comment`<br>`displayName`<br>

Poniższy fragment kodu w języku C# pokazuje, jak plik modelu RDF jest ładowany do grafu i konwertowany na DTDL przy użyciu biblioteki [**dotNetRDF**](https://www.dotnetrdf.org/) . 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/convertRDF.cs":::

## <a name="converter-samples"></a>Przykłady konwerterów

### <a name="rdf-converter-application"></a>Aplikacja konwertera RDF 

Dostępna jest przykładowa aplikacja, która konwertuje RDF plik modelu na [DTDL (wersja 2)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) do użycia przez usługę Azure Digital bliźniaczych reprezentacji. Została sprawdzona pod kątem schematu [kostki](https://brickschema.org/ontology/) i może zostać rozszerzona o inne schematy w branży budowlanej (na przykład [Budowanie topologii Ontology (bot)](https://w3c-lbd-cg.github.io/bot/), [Sieć czujników semantycznych](https://www.w3.org/TR/vocab-ssn/)lub [buildingSmart Industry Foundation Classes (IFC)](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/)).

Przykładem jest aplikacja wiersza polecenia platformy .NET Core o nazwie **RdfToDtdlConverter**.

Możesz pobrać przykład tutaj: [**RdfToDtdlConverter**](/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/). 

Aby pobrać kod na komputer, naciśnij przycisk Pobierz plik *zip* poniżej tytułu na przykładowej stronie docelowej. Spowoduje to pobranie pliku *zip* pod nazwą *RdfToDtdlConverter_sample_application_to_convert_RDF_to_DTDL.zip*, którą można rozpakować i zbadać.

Możesz użyć tego przykładu, aby zobaczyć wzorce konwersji w kontekście i utworzyć jako blok konstrukcyjny dla własnych aplikacji wykonujących konwersje modeli zgodnie z własnymi potrzebami.

### <a name="owl2dtdl-converter"></a>Konwerter OWL2DTDL 

[**Konwerter OWL2DTDL**](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/OWL2DTDL) to przykład, który tłumaczy Owl Ontology na zestaw deklaracji interfejsu DTDL, które mogą być używane z usługą Azure Digital bliźniaczych reprezentacji. Działa również w przypadku sieci ontologyowych, które zostały wykonane z jednego głównego ontologyu, przy użyciu innych ontologie za pomocą `owl:imports` deklaracji.

Ten konwerter został użyty do przetłumaczenia [podstawowego Ontologyu nieruchomości](https://doc.realestatecore.io/3.1/full.html) na DTDL i może być używany dla dowolnego Ontology opartego na OWL.

## <a name="next-steps"></a>Następne kroki 

* Dowiedz się więcej na temat rozszerzania standardowych ontologie branżowych, aby sprostać Twoim wymaganiom: [*koncepcje: rozszerzanie branżowych ontologie*](concepts-ontologies-extend.md).

* Lub Kontynuuj na ścieżce tworzenia modeli opartych na ontologie: [*Używanie strategii Ontology w ścieżce projektowania modelu*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).