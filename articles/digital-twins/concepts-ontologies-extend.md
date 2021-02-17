---
title: Rozszerzanie ontologie
titleSuffix: Azure Digital Twins
description: Zapoznaj się z przyczynami i strategiami związanymi z rozszerzaniem Ontology
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: e5973f58887b212919ad739232faafddcf9e735c
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561544"
---
# <a name="extending-ontologies"></a>Rozszerzanie ontologie 

Standardowe [Ontology](concepts-ontologies.md), takie jak [DTDL RealEstateCore Ontology dla budynków inteligentnych](https://github.com/Azure/opendigitaltwins-building), to doskonały sposób na rozpoczęcie tworzenia rozwiązania IoT. Branżowe ontologie zapewniają bogaty zestaw podstawowych interfejsów, które są zaprojektowane dla Twojej domeny i zaprojektowane do pracy z usługą Azure IoT Services, takich jak Azure Digital bliźniaczych reprezentacji. 

Istnieje jednak możliwość, że rozwiązanie może mieć określone wymagania, które nie są objęte ontologyem branżowym. Na przykład możesz chcieć połączyć bliźniaczych reprezentacji cyfrowy z modelami 3D przechowywanymi w osobnym systemie. W takim przypadku można zwiększyć jeden z tych ontologie, aby dodać własne funkcje, zachowując jednocześnie wszystkie korzyści wynikające z oryginalnego Ontology.

W tym artykule jest stosowana DTDL [RealEstateCore](https://www.realestatecore.io/) Ontology, jako podstawa dla przykładów rozszerzania ontologie z nowymi właściwościami DTDL. Techniki opisane tutaj są ogólne, ale mogą być stosowane do dowolnej części Ontology opartej na DTDL z jakąkolwiek funkcją DTDL (telemetrii, właściwości, relacji, składnika lub polecenia). 

## <a name="realestatecore-space-hierarchy"></a>Hierarchia przestrzeni RealEstateCore 

W DTDL RealEstateCore Ontology, hierarchia miejsca służy do definiowania różnych rodzajów spacji: pokojów, budynków, strefy itp. Hierarchia programu rozciąga się od każdego z tych modeli w celu zdefiniowania różnych rodzajów pokojów, budynków i stref. 

Część hierarchii wygląda jak na poniższym diagramie. 

:::image type="content" source="media/concepts-extending-ontologies/RealEstateCore-original.png" alt-text="Diagram przepływu ilustrujący część hierarchii przestrzeni RealEstateCore. Na najwyższego poziomu występuje element o nazwie Space; jest on połączony przez strzałkę &quot;rozszerza&quot; w dół do pokoju; Pomieszczenie jest połączone dwoma strzałkami &quot;extends&quot; w dół o poziomie do ConferenceRoom i Office."::: 

Aby uzyskać więcej informacji na temat RealEstateCore Ontology, zobacz [*pojęcia: przyjmowanie standardowych ontologie*](concepts-ontologies-adopt.md#realestatecore-smart-building-ontology).

## <a name="extending-the-realestatecore-space-hierarchy"></a>Rozszerzanie hierarchii przestrzeni RealEstateCore 

Czasami Twoje rozwiązanie ma konkretne potrzeby, które nie są objęte ontologyem branżowym. W takim przypadku rozszerzenie hierarchii pozwala nadal korzystać z Ontology branżowych, a jednocześnie dostosowywać je do Twoich potrzeb. 

W tym artykule omówiono dwa różne przypadki, w których rozszerzanie hierarchii Ontology jest przydatne: 

* Dodawanie nowych interfejsów dla koncepcji nieznajdujących się w ontologyach branżowych. 
* Dodawanie dodatkowych właściwości (lub relacji, składników, telemetrii lub poleceń) do istniejących interfejsów.

### <a name="add-new-interfaces-for-new-concepts"></a>Dodawanie nowych interfejsów dla nowych koncepcji 

W takim przypadku należy dodać interfejsy dla koncepcji potrzebnych do rozwiązania, ale nie są one obecne w branży Ontology. Na przykład jeśli rozwiązanie ma inne typy pokojów, które nie są reprezentowane w DTDL RealEstateCore Ontology, można je dodać, rozszerzając bezpośrednio z interfejsów RealEstateCore. 

W poniższym przykładzie przedstawiono rozwiązanie, które musi reprezentować "pokoje ostrości", które nie są obecne w RealEstateCore Ontology. Pomieszczenie do koncentracji uwagi to małe miejsce przeznaczone do skoncentrowania się na zadaniach przez kilka godzin jednocześnie. 

Aby rozszerzyć branżowe Ontology z tą nową koncepcją, Utwórz nowy interfejs, który [rozciąga się od](concepts-models.md#model-inheritance) interfejsów w branży Ontology. 

Po dodaniu interfejsu pokoju fokusu rozszerzona hierarchia pokazuje nowy typ pokoju. 

:::image type="content" source="media/concepts-extending-ontologies/RealEstateCore-extended-1.png" alt-text="Diagram przepływu ilustrujący hierarchię obszaru RealEstateCore z powyżej z nowym dodaniem. Na najniższym poziomie z ConferenceRoom i pakietem Office istnieje nowy element o nazwie FocusRoom (połączony również za pomocą strzałki &quot;extends&quot; z pokoju)"::: 

### <a name="add-additional-capabilities-to-existing-interfaces"></a>Dodawanie dodatkowych możliwości do istniejących interfejsów 

W takim przypadku należy dodać więcej właściwości (lub relacje, składniki, dane telemetryczne lub polecenia) do interfejsów, które znajdują się w branży Ontology.

W tej sekcji zobaczysz dwa przykłady: 
* Jeśli tworzysz rozwiązanie, które wyświetla rysunki 3D miejsc, które znajdują się już w istniejącym systemie, możesz chcieć skojarzyć poszczególne sznury cyfrowe z rysunkiem 3D (według identyfikatora), aby gdy rozwiązanie wyświetli informacje o tym miejscu, można również pobrać rysunek 3W z istniejącego systemu. 
* Jeśli Twoje rozwiązanie wymaga śledzenia stanu trybu online/offline pokojów konferencyjnych, możesz chcieć śledzić stan pokoju konferencyjnego na potrzeby wyświetlania lub zapytań. 

Oba przykłady można zaimplementować z nowymi właściwościami: `drawingId` Właściwość, która kojarzy rysunek 3W z dwuosiową i właściwością "online" wskazującą, czy pokój konferencyjny jest w trybie online, czy nie. 

Zwykle nie chcesz modyfikować Ontology branżowych bezpośrednio, ponieważ chcesz mieć możliwość dołączania do nich aktualizacji w rozwiązaniu w przyszłości (co spowoduje zastąpienie dodatków). Zamiast tego rodzaje dodatków można wprowadzać we własnej hierarchii interfejsów, która rozciąga się od DTDL RealEstateCore Ontology. Każdy tworzony interfejs używa wielu dziedziczenia interfejsów, aby rozszerzyć swój nadrzędny interfejs RealEstateCore i jego interfejs nadrzędny z hierarchii interfejsu rozszerzonego. Takie podejście umożliwia korzystanie z Ontology branżowych i dodatków. 

Aby rozszerzyć branżowe Ontology, należy utworzyć własne interfejsy, które rozszerzają się z interfejsów w branży Ontology i dodają nowe funkcje do rozszerzonych interfejsów. Dla każdego interfejsu, który ma zostać rozszerzona, należy utworzyć nowy interfejs. Rozszerzone interfejsy są zapisywane w DTDL (zobacz sekcję DTDL for Extended Interfaces w dalszej części tego dokumentu). 

Po rozszerzeniu części hierarchii pokazanej powyżej rozszerzona hierarchia wygląda jak poniżej diagramu. W tym miejscu interfejs Extended Space dodaje `drawingId` Właściwość, która będzie zawierać identyfikator, który kojarzy dwuosiową cyfrę z rysunkiem 3D. Ponadto interfejs ConferenceRoom dodaje właściwość "online", która będzie zawierać stan online sali konferencyjnej. Dzięki dziedziczeniu interfejs ConferenceRoom zawiera wszystkie możliwości z interfejsu RealEstateCore ConferenceRoom, a także wszystkie możliwości z interfejsu rozszerzonego miejsca. 

:::image type="content" source="media/concepts-extending-ontologies/RealEstateCore-extended-2.png" alt-text="Diagram przepływu pokazujący rozszerzoną hierarchię przestrzeni RealEstateCore z powyżej, z nowymi dodatkowymi dodatkami. Pomieszczenie udostępnia teraz swój poziom za pomocą elementu spacji, który łączy się ze strzałką &quot;rozszerza&quot; w dół do nowego elementu pokoju obok ConferenceRoom i pakietu Office.  Nowe elementy są połączone z istniejącym Ontology o większej liczbie relacji &quot;extends&quot;."::: 

## <a name="using-the-extended-space-hierarchy"></a>Korzystanie z rozszerzonej hierarchii odstępów 

Po utworzeniu bliźniaczych reprezentacji cyfrowej przy użyciu rozszerzonej hierarchii odstępów każdy model cyfrowej przędzy będzie miał jedną z rozszerzonych hierarchii przestrzeni (a nie pierwotną branżową Ontology) i będzie obejmować wszystkie możliwości z Ontology branżowych i rozszerzonych interfejsów, Chociaż dziedziczenie interfejsu.

Każda z modeli cyfrowych przędzy będzie interfejsem z hierarchii rozszerzonej, pokazane na poniższym diagramie. 
 
:::image type="content" source="media/concepts-extending-ontologies/ontology-with-models.png" alt-text="Fragment z rozszerzonej hierarchii RealEstateCore Space, w tym miejsce (najwyższy poziom), jeden pokój (poziom środkowy), ConferenceRoom, Office i FocusRoom (niższy poziom). Nazwy modeli są połączone z każdym elementem (na przykład pomieszczenie jest połączone z modelem o nazwie Room101)."::: 

Podczas wykonywania zapytań dotyczących bliźniaczych reprezentacji cyfrowych przy użyciu identyfikatora modelu ( `IS_OF_MODEL` operatora) należy używać identyfikatorów modelu z hierarchii rozszerzonej. Na przykład `SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:com:example:Office;1')`. 

## <a name="contributing-back-to-the-original-ontology"></a>Współtworzenie z powrotem do oryginalnego Ontology 

W niektórych przypadkach można zwiększyć branżowe Ontology w sposób szeroko przydatny dla większości użytkowników Ontology. W takim przypadku należy rozważyć Współtworzenie rozszerzeń z powrotem do oryginalnego Ontology. Każdy Ontology ma inny proces do współtworzenia, więc Sprawdź repozytorium GitHub Ontology, aby uzyskać szczegółowe informacje dotyczące udziału. 

## <a name="dtdl-for-new-interfaces"></a>DTDL dla nowych interfejsów 

DTDL dla nowych interfejsów, które są rozbudowane bezpośrednio z branży Ontology, będzie wyglądać następująco. 

```json
{
  "@id": "dtmi:com:example:FocusRoom;1", 
  "@type": "interface", 
  "extends": "dtmi:digitaltwins:rec_3_3:building:Office;1", 
  "@context": "dtmi:dtdl:context;2" 
} 
```

## <a name="dtdl-for-extended-interfaces"></a>DTDL dla rozszerzonych interfejsów 

DTDL dla rozszerzonych interfejsów, ograniczone do części omówionej powyżej, będzie wyglądać następująco. 

```json
[
  {
    "@id": "dtmi:com:example:Space;1",
    "@type": "Interface",
    "extends": "dtmi:digitaltwins:rec_3_3:core:Space;1",
    "contents": [
      {
        "@type": "Property",
        "name": "drawingid",
        "schema": "string"
      }
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:Room;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:core:Room;1",
      "dtmi:com:example:Space;1"
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:ConferenceRoom;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:building:ConferenceRoom;1",
      "dtmi:com:example:Room;1"
    ],
    "contents": [
      {
        "@type": "Property",
        "name": "online",
        "schema": "boolean"
      }
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:Office;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:building:Office;1", 
      "dtmi:com:example:Room;1" 
    ],
    "@context": "dtmi:dtdl:context;2" 
  }, 
  {
    "@id": "dtmi:com:example:FocusRoom;1", 
    "@type": "Interface", 
    "extends": "dtmi:com:example:Office;1", 
    "@context": "dtmi:dtdl:context;2" 
  }
]
``` 

## <a name="next-steps"></a>Następne kroki

Kontynuuj na ścieżce tworzenia modeli opartych na ontologie: [*Używanie strategii Ontology w ścieżce projektowania modelu*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).