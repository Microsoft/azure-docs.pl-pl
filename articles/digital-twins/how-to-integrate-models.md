---
title: Integrowanie modeli zgodnych ze standardami branżowymi
titleSuffix: Azure Digital Twins
description: Dowiedz się, jak zintegrować modele standardowe w branży z DTDL for Azure Digital bliźniaczych reprezentacji przy użyciu specjalnych DTDL ontologie lub konwertowania istniejących ontologie
author: baanders
ms.author: baanders
ms.date: 11/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 3376f5d5e207a33ad39cd7506998e2ee90e084ad
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98051551"
---
# <a name="integrate-industry-standard-models-with-dtdl-for-azure-digital-twins"></a>Integruj modele branżowe ze standardami branżowymi i DTDL for Azure Digital bliźniaczych reprezentacji

Korzystanie z modeli opartych na standardach branżowych lub użycie standardowej reprezentacji Ontology, takiej jak RDF lub OWL, zapewnia rozbudowany punkt początkowy podczas projektowania modeli bliźniaczych reprezentacji cyfrowych platformy Azure. Korzystanie z modeli branżowych ułatwia również standaryzację i udostępnianie informacji.

Aby można było korzystać z usługi Azure Digital bliźniaczych reprezentacji, model musi być przedstawiony w formacie JSON-LD-based [**Digital bliźniaczych reprezentacji Definition Language (DTDL)**](concepts-models.md). W związku z tym w tym artykule opisano sposób reprezentowania modeli branżowych w programie DTDL, które integrują istniejące koncepcje branżowe z semantyką DTDL, tak aby usługa Azure Digital bliźniaczych reprezentacji mogła z nich korzystać. Model DTDL służy następnie jako źródło prawdy dla modelu w usłudze Azure Digital bliźniaczych reprezentacji.

Istnieją trzy możliwe ścieżki do integrowania modeli standardowych branżowych z DTDL:
* **Przyjęcie**: możesz uruchomić rozwiązanie za pomocą DTDL Ontology, które zostało utworzone na powszechnie przyjętych standardach branżowych. 
* **Konwertuj**: Jeśli masz już istniejące modele, musisz przekonwertować je na DTDL.
* **Autor**: zawsze możesz opracowywać własne niestandardowe modele DTDL od podstaw, zgodnie z opisem w temacie [*How to: Manage Custom models*](how-to-manage-model.md).

## <a name="adopt-an-open-source-dtdl-ontology"></a>Wdrażanie DTDLa Open Source Ontology

Często łatwiej zacząć od typu open source DTDL Ontology niż na podstawie pustej strony. 

Na przykład rozwiązania do obsługi budynków inteligentnych mogą korzystać z [**DTDL RealEstateCore Ontology opartego**](https://github.com/Azure/opendigitaltwins-building)na protokole Open Source, który zapewnia wspólną podstawę do modelowania inteligentnych budynków przy użyciu standardów branżowych, aby zapobiec odłożeniu. 

Te DTDL ontologie typu open source oferują również najlepsze rozwiązania w zakresie używania i prawidłowego zwiększania modeli. 

## <a name="convert-existing-models-to-dtdl"></a>Konwertuj istniejące modele na DTDL

Większość modeli branżowych (nazywanych również **ontologie**) jest oparta na semantycznych standardach sieci Web, takich jak [Owl](https://www.w3.org/OWL/), [RDF](https://www.w3.org/2001/sw/wiki/RDF)i [RDFS](https://www.w3.org/2001/sw/wiki/RDFS). 

Aby używać modelu z usługą Azure Digital bliźniaczych reprezentacji, musi on być w formacie DTDL. W tej sekcji opisano ogólne wskazówki dotyczące projektowania w postaci **wzorca konwersji** służącego do konwertowania modeli opartych na RDF na DTDL, dzięki czemu mogą one być używane z usługą Azure Digital bliźniaczych reprezentacji. 

Zawiera również [ **przykładowy kod konwertera** dla konwertera RDF](#sample-converter-application), który został sprawdzony pod kątem schematu [kostki](https://brickschema.org/ontology/) i można go rozszerzyć dla innych schematów w branży budowlanej.

### <a name="conversion-pattern"></a>Wzorzec konwersji

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

### <a name="sample-converter-application"></a>Przykładowa aplikacja konwertera 

Dostępna jest przykładowa aplikacja, która konwertuje RDF plik modelu na [DTDL (wersja 2)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) do użycia przez usługę Azure Digital bliźniaczych reprezentacji. Została sprawdzona pod kątem schematu [kostki](https://brickschema.org/ontology/) i może zostać rozszerzona o inne schematy w branży budowlanej (na przykład [Budowanie topologii Ontology (bot)](https://w3c-lbd-cg.github.io/bot/), [Sieć czujników semantycznych](https://www.w3.org/TR/vocab-ssn/)lub [buildingSmart Industry Foundation Classes (IFC)](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/)).

Przykładem jest aplikacja wiersza polecenia platformy .NET Core o nazwie **RdfToDtdlConverter**.

Możesz pobrać przykład tutaj: [**RdfToDtdlConverter**](/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/). 

Aby pobrać kod na komputer, naciśnij przycisk Pobierz plik *zip* poniżej tytułu na przykładowej stronie docelowej. Spowoduje to pobranie pliku *zip* pod nazwą *RdfToDtdlConverter_sample_application_to_convert_RDF_to_DTDL.zip*, którą można rozpakować i zbadać.

Możesz użyć tego przykładu, aby zobaczyć wzorce konwersji w kontekście i utworzyć jako blok konstrukcyjny dla własnych aplikacji wykonujących konwersje modeli zgodnie z własnymi potrzebami.

## <a name="validate-and-upload-dtdl-models"></a>Weryfikowanie i przekazywanie modeli DTDL

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

Po przekonwertowaniu i zweryfikowaniu modelu można **przekazać go do wystąpienia usługi Azure Digital bliźniaczych reprezentacji**. Aby uzyskać więcej informacji na temat tego procesu, zobacz sekcję [*przekazywanie modeli*](how-to-manage-model.md#upload-models) w temacie *How to: Manage Custom models*.

## <a name="next-steps"></a>Następne kroki 

Dowiedz się więcej na temat projektowania modeli cyfrowych bliźniaczych i zarządzania nimi:
 
* [*Koncepcje: modele niestandardowe*](concepts-models.md)
* [*Instrukcje: Zarządzanie modelami niestandardowymi*](how-to-manage-model.md)
* [*Instrukcje: analizowanie i weryfikowanie modeli*](how-to-parse-models.md)