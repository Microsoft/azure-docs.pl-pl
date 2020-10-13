---
title: Konwertowanie standardowych modeli branżowych
titleSuffix: Azure Digital Twins
description: Opis wzorca konwersji modeli branżowych (RDF/OWL) na DTDL
author: baanders
ms.author: baanders
ms.date: 9/28/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 76d1fd91053216103ef6ace0e56979c57eca569f
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "92002650"
---
# <a name="convert-industry-standard-models-to-dtdl-for-azure-digital-twins"></a>Konwertowanie standardowych modeli branżowych na DTDL na potrzeby usługi Azure Digital bliźniaczych reprezentacji

Modele w usłudze Azure Digital bliźniaczych reprezentacji są reprezentowane w formacie [**Digital bliźniaczych reprezentacji Definition Language (DTDL)**](concepts-models.md)w formacie JSON. Jeśli masz istniejące modele poza usługą Azure Digital bliźniaczych reprezentacji, które są oparte na standardzie branżowym, takim jak RDF lub OWL, musisz **przekonwertować je na DTDL** , aby użyć ich w usłudze Azure Digital bliźniaczych reprezentacji. Wersja DTDL stanie się następnie źródłem prawdy dla modelu w ramach usługi Azure Digital bliźniaczych reprezentacji.

W tym artykule opisano wzorzec konwertowania branż opartych na RDF lub modeli niestandardowych na DTDL. Obejmuje:
* **Wskazówki dotyczące poziomu strategii** , które mogą być stosowane do różnych standardów i typów modelu
* [**Przykładowy kod** dla konwertera specyficznego dla RDF](#sample-converter-application)

## <a name="industry-models"></a>Modele branżowe  

Korzystanie z modeli branżowych zapewnia rozbudowany punkt początkowy podczas projektowania modelu bliźniaczych reprezentacji cyfrowych platformy Azure. Korzystanie z modeli branżowych ułatwia również standaryzację i udostępnianie informacji. 

Niektóre popularne modele branżowe obejmują:  

| Branża w pionie | Model |
| --- | --- | 
| Tworzenie i zarządzanie materiałami | [RealEstateCore](https://www.realestatecore.io/)<br>[Schemat kostki](https://brickschema.org/ontology/1.1/)<br>[Tworzenie topologii Ontology (BOT)](https://w3c-lbd-cg.github.io/bot/)<br>[Sieć czujników semantycznych](https://www.w3.org/TR/vocab-ssn/)<br>[Klasy buildingSmart Industry Foundation (IFC)](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/) |
| Miasta inteligentne | [ETSI NGSI-LD](https://www.etsi.org/deliver/etsi_gr/CIM/001_099/008/01.01.01_60/gr_CIM008v010101p.pdf)<br>[Dokumentacja inteligentnych aplikacji (SAREF)](https://saref.etsi.org/) |
| Siatka energii | [Model CIM](https://cimug.ucaiug.org/) / [IEC 61968](https://en.wikipedia.org/wiki/IEC_61968) | 

W zależności od potrzeb można także używać DTDL do dostosowywania lub rozszerzania modeli branżowych lub opracowywania własnych modeli niestandardowych od podstaw. 

## <a name="create-and-edit-models"></a>Tworzenie i edytowanie modeli

Pierwszy krok modelowania polega na tworzeniu modeli. Możesz tworzyć i dokończyć edycję modeli standardowych branżowych przed ich przekonwertowaniem na DTDL. Możesz też przekonwertować je na wczesne DTDL i kontynuować edytowanie ich jako dokumentów DTDL.

### <a name="with-industry-standards"></a>Ze standardami branżowymi

Większość modeli branżowych (nazywanych również **ontologie**) jest oparta na semantycznych standardach sieci Web, takich jak [Owl](https://www.w3.org/OWL/[), [RDF](https://www.w3.org/2001/sw/wiki/RDF)i [RDFS](https://www.w3.org/2001/sw/wiki/RDFS). 

W niektórych przypadkach możesz chcieć utworzyć lub edytować model przy użyciu narzędzi do modelowania opartych na OWL. Aby zaprojektować model oparty na OWL, można użyć dowolnej liczby narzędzi do tworzenia modelu, w tym następujących elementów.
* [Pulpity](https://protege.stanford.edu/products.php#desktop-protege) [WebProtégé](https://protege.stanford.edu/products.php#web-protege) i Protégé są popularnymi przykładami. Modele branżowe można importować w wielu formatach, edytować lub zwiększać model oraz eksportować model. 
* [WebVOWL](http://www.visualdataweb.de/webvowl/) to inne popularne narzędzie do wizualizacji modeli. 

Jeśli tworzysz model przy użyciu pewnego standardu branżowego, który nie jest DTDL, musisz przekonwertować go na DTDL i przekazać go do usługi Azure Digital bliźniaczych reprezentacji. 

#### <a name="common-model-file-formats"></a>Typowe formaty plików modelu 

RDF, OWL i RDFS są podstawowymi blokami konstrukcyjnymi semantycznej sieci Web. 

**RDF** zawiera strukturę koncepcyjną do opisywania elementów w postaci **potrójnych**. Potrójne składa się z trzech części: **tematu**, **predykatu**i **obiektu**. Obiekty mogą być wykonane z identyfikatorów URI. 

Oto kilka przykładów RDF:

```
<LogicalDevice> <hasCapabiity> <Temperature>
<Chiller> <locatedIn> <Level1>
<Asset> <isPartOf> <Asset>  
```

Te przykłady są prawidłowymi RDF, ale Ostatnia instrukcja jest semantycznie nieprawidłowa. Ta sytuacja polega na tym, że Specyfikacja OWL przechodzi do obrazu. **Owl** definiuje, co można napisać za pomocą RDF w celu uzyskania prawidłowych Ontology.

Oto przykład użycia OWL: 

```
<Asset> <isPartOf> <Building>
<TemperatureSensor> <isType> <Sensor>
```

**RDFS** zapewnia dodatkową semantykę słownika, która ułatwia definiowanie i opisywanie klas. Na przykład jedną z takich klas jest `rdfs:subClassOf` :

```
<Equipment> <subClassOf> <Asset>
```

Modele można zapisywać, importować i eksportować w wielu formatach plików, w tym:  
* RDF/XML 
* Żółw (TTL) 
* OWL/XML 

### <a name="with-dtdl"></a>Z DTDL

Usługa Azure Digital bliźniaczych reprezentacji korzysta ze standardu **DTDL (Digital bliźniaczy Definition Language) języka** JSON do modelowania. Każdy model, który będzie używany z usługą Azure Digital bliźniaczych reprezentacji, musi być pierwotnie wpisany lub przekonwertowany na DTDL.

Podczas pracy z modelami w DTDL można użyć [**rozszerzenia DTDL**](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl)   dostępnego dla [Visual Studio Code](https://code.visualstudio.com/), które zapewnia walidację składni i inne funkcje ułatwiające Zapisywanie modeli DTDL.

Więcej informacji o modelach i ich składnikach cyfrowych bliźniaczych reprezentacji na platformie Azure można znaleźć w temacie [*: modele niestandardowe*](concepts-models.md) i [*instrukcje: Zarządzanie modelami niestandardowymi*](how-to-manage-model.md).

## <a name="convert-models-to-dtdl"></a>Konwertuj modele na DTDL

Aby używać modelu z usługą Azure Digital bliźniaczych reprezentacji, musi on być w formacie DTDL. W tej sekcji opisano sposób konwertowania modeli opartych na RDF na DTDL, dzięki czemu mogą one być używane z usługą Azure Digital bliźniaczych reprezentacji.

Istnieje kilka bibliotek innych firm, których można używać podczas konwertowania modeli opartych na RDF na DTDL. Niektóre z tych bibliotek umożliwiają załadowanie pliku modelu do grafu. Możesz przechodzić przez Graf, szukając określonych konstrukcji RDFS i OWL, i przekonwertować je na DTDL.   

W poniższej tabeli przedstawiono przykład sposobu mapowania konstrukcji RDFS i OWL na DTDL. 

| Koncepcja RDFS/OWL | Konstrukcja RDFS/OWL | Koncepcja DTDL | Konstrukcja DTDL |
| --- | --- | --- | --- |
| Klasy | `owl:Class`<br>IRI sufiks<br>``rdfs:label``<br>``rdfs:comment`` | Interfejs | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment` 
| Podklasy | `owl:Class`<br>IRI sufiks<br>`rdfs:label`<br>`rdfs:comment`<br>`rdfs:subClassOf` | Interfejs | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment`<br>`extends` 
| Właściwości typu danych | `owl:DatatypeProperty`<br>`rdfs:label` lub `INode`<br>`rdfs:label`<br>`rdfs:range` | Właściwości interfejsu | `@type:Property`<br>`name`<br>`displayName`<br>`schema` 
| Właściwości obiektu | `owl:ObjectProperty`<br>`rdfs:label` lub `INode`<br>`rdfs:range`<br>`rdfs:comment`<br>`rdfs:label` | Relacja | `type:Relationship`<br>`name`<br>`target` (lub pomijane, jeśli nie `rdfs:range` )<br>`comment`<br>`displayName`<br>

Poniższy fragment kodu w języku C# pokazuje, jak plik modelu RDF jest ładowany do grafu i konwertowany na DTDL przy użyciu biblioteki [**dotNetRDF**](https://www.dotnetrdf.org/) . 

```csharp
using VDS.RDF.Ontology; 
using VDS.RDF.Parsing; 
using Microsoft.Azure.DigitalTwins.Parser; 

//...

Console.WriteLine("Reading file..."); 

FileLoader.Load(_ontologyGraph, rdfFile.FullName); 

// Start looping through for each owl:Class 
foreach (OntologyClass owlClass in _ontologyGraph.OwlClasses) 
{ 

    // Generate a DTMI for the owl:Class 
    string Id = GenerateDTMI(owlClass); 

    if (!String.IsNullOrEmpty(Id)) 
    { 

        Console.WriteLine($"{owlClass.ToString()} -> {Id}"); 

        // Create Interface
        DtdlInterface dtdlInterface = new DtdlInterface 
        { 
            Id = Id, 
            Type = "Interface", 
            DisplayName = GetInterfaceDisplayName(owlClass), 
            Comment = GetInterfaceComment(owlClass), 
            Contents = new List<DtdlContents>() 
        }; 

        // Use DTDL 'extends' for super classes 
        IEnumerable<OntologyClass> foundSuperClasses = owlClass.DirectSuperClasses; 

        //... 
     }

     // Add interface to the list of interfaces 
     _interfaceList.Add(dtdlInterface); 
} 

// Serialize to JSON 
var json = JsonConvert.SerializeObject(_interfaceList); 

//...
``` 

## <a name="validate-and-upload-dtdl-models"></a>Weryfikowanie i przekazywanie modeli DTDL

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

Po przekonwertowaniu i zweryfikowaniu modelu można **przekazać go do wystąpienia usługi Azure Digital bliźniaczych reprezentacji**. Aby uzyskać więcej informacji na temat tego procesu, zobacz sekcję [*przekazywanie modeli*](how-to-manage-model.md#upload-models) w temacie *How to: Manage Custom models*.

## <a name="sample-converter-application"></a>Przykładowa aplikacja konwertera 

Dostępna jest przykładowa aplikacja, która konwertuje RDF plik modelu na [DTDL (wersja 2)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) do użycia przez usługę Azure Digital bliźniaczych reprezentacji. Przykładem jest aplikacja wiersza polecenia platformy .NET Core o nazwie **RdfToDtdlConverter**.

Możesz pobrać przykład tutaj: [**RdfToDtdlConverter**](https://docs.microsoft.com/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/). 

Aby pobrać kod na komputer, naciśnij przycisk Pobierz plik *zip* poniżej tytułu na przykładowej stronie docelowej. Spowoduje to pobranie pliku *zip* pod nazwą *RdfToDtdlConverter_sample_application_to_convert_RDF_to_DTDL.zip*, którą można rozpakować i zbadać.

Możesz użyć tego przykładu, aby zobaczyć wzorce konwersji w kontekście i utworzyć jako blok konstrukcyjny dla własnych aplikacji wykonujących konwersje modeli zgodnie z własnymi potrzebami.

## <a name="next-steps"></a>Następne kroki 

Dowiedz się więcej na temat projektowania modeli cyfrowych bliźniaczych i zarządzania nimi:
 
* [*Koncepcje: modele niestandardowe*](concepts-models.md)
* [*Instrukcje: Zarządzanie modelami niestandardowymi*](how-to-manage-model.md)
* [*Instrukcje: analizowanie i weryfikowanie modeli*](how-to-parse-models.md)