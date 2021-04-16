---
title: Rozszerzanie nalogi
titleSuffix: Azure Digital Twins
description: Poznaj przyczyny i strategie rozszerzania ontologii
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: b38b4910773c433ed63fd2082c5cbefce81e0e9e
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480234"
---
# <a name="extending-ontologies"></a>Rozszerzanie nalogi 

Standardowa w branży [ontologia,](concepts-ontologies.md)taka jak [ontologia RealEstateCore](https://github.com/Azure/opendigitaltwins-building)oparta na standardach DTDL dla inteligentnych budynków, to doskonały sposób na rozpoczęcie tworzenia rozwiązania IoT. Branżowe ontologie zapewniają bogaty zestaw podstawowych interfejsów, które są przeznaczone dla Twojej domeny i zaprojektowane do pracy od podstaw w usługach Azure IoT, takich jak Azure Digital Twins. 

Jednak rozwiązanie może mieć określone potrzeby, które nie są objęte ontologią branżową. Na przykład możesz chcieć połączyć swoje bliźniacze reprezentacjiki cyfrowe z modelami 3D przechowywanymi w oddzielnym systemie. W takim przypadku można rozszerzyć jedną z tych nalogi, aby dodać własne możliwości przy jednoczesnym zachowaniu wszystkich zalet oryginalnej ontologii.

W tym artykule użyto ontologii [RealEstateCore](https://www.realestatecore.io/) opartej na dtdl jako podstawy przykładów rozszerzania nalogi przy użyciu nowych właściwości DTDL. Techniki opisane w tym miejscu są jednak ogólne i można je stosować do dowolnej części ontologii opartej na dtdl z dowolną możliwością języka DTDL (telemetrii, właściwości, relacji, składnika lub polecenia). 

## <a name="realestatecore-space-hierarchy"></a>RealEstateCore, hierarchia przestrzeni 

W ontologii RealEstateCore opartej na DTDL hierarchia Space służy do definiowania różnych rodzajów przestrzeni: pomieszczeń, budynków, strefy itp. Hierarchia rozszerza każdy z tych modeli, definiując różne rodzaje pomieszczeń, budynków i stref. 

Część hierarchii wygląda jak na poniższym diagramie. 

:::image type="content" source="media/concepts-ontologies-extend/real-estate-core-original.png" alt-text="Diagram przepływu ilustrujący część hierarchii przestrzeni RealEstateCore. Na najwyższym poziomie znajduje się element o nazwie Spacja. Jest on połączony strzałką &quot;rozszerza&quot; poziomą w dół do pozycji Pomieszczenie; Pomieszczenie jest połączone dwoma strzałkami &quot;rozszerza&quot; w dół poziomu do sali konferencyjnej i biura."::: 

Aby uzyskać więcej informacji na temat ontologii RealEstateCore, zobacz [*Concepts: Adopting industry-standard ontologies (Pojęcia: adoptowanie standardowych w branży ontologi).*](concepts-ontologies-adopt.md#realestatecore-smart-building-ontology)

## <a name="extending-the-realestatecore-space-hierarchy"></a>Rozszerzanie hierarchii przestrzeni RealEstateCore 

Czasami twoje rozwiązanie ma określone potrzeby, które nie są objęte ontologią branżową. W takim przypadku rozszerzenie hierarchii umożliwia kontynuowanie korzystania z ontologii branżowej podczas dostosowywania jej do własnych potrzeb. 

W tym artykule omówiono dwa różne przypadki, w których rozszerzanie hierarchii ontologii jest przydatne: 

* Dodanie nowych interfejsów dla pojęć, które nie są związane z ontologią branżową. 
* Dodawanie dodatkowych właściwości (lub relacji, składników, telemetrii lub poleceń) do istniejących interfejsów.

### <a name="add-new-interfaces-for-new-concepts"></a>Dodawanie nowych interfejsów dla nowych pojęć 

W tym przypadku chcesz dodać interfejsy dla koncepcji potrzebnych do rozwiązania, ale które nie są obecne w ontologii branżowej. Jeśli na przykład twoje rozwiązanie ma inne typy pomieszczeń, które nie są reprezentowane w ontologii RealEstateCore opartej na języku DTDL, możesz je dodać, rozszerzając je bezpośrednio z interfejsów RealEstateCore. 

W poniższym przykładzie przedstawiono rozwiązanie, które musi reprezentować "pomieszczenia fokusu", które nie są obecne w ontologii RealEstateCore. Pomieszczenie koncentracji uwagi to mała przestrzeń przeznaczona dla osób, które mogą skupić się na zadaniu przez kilka godzin na raz. 

Aby rozszerzyć ontologię branżową o tę [](concepts-models.md#model-inheritance) nową koncepcję, utwórz nowy interfejs, który będzie rozszerzać interfejsy w ontologii branżowej. 

Po dodaniu interfejsu pokoju fokusu hierarchia rozszerzona pokazuje nowy typ pomieszczenia. 

:::image type="content" source="media/concepts-ontologies-extend/real-estate-core-extended-1.png" alt-text="Diagram przepływu ilustrujący hierarchię przestrzeni RealEstateCore z nową wartością. Na dolnym poziomie, w przypadku sali konferencyjnej i pakietu Office, istnieje nowy element o nazwie FocusRoom (połączony również za pośrednictwem strzałki &quot;rozszerza&quot; z okna Room)"::: 

### <a name="add-additional-capabilities-to-existing-interfaces"></a>Dodawanie dodatkowych możliwości do istniejących interfejsów 

W takim przypadku chcesz dodać więcej właściwości (lub relacji, składników, telemetrii lub poleceń) do interfejsów, które znajdują się w ontologii branżowej.

W tej sekcji zobaczysz dwa przykłady: 
* W przypadku tworzenia rozwiązania, które wyświetla rysunki 3D miejsc, które już znajdują się w istniejącym systemie, warto skojarzyć każdą cyfrową bliźniacę z jej rysunkiem 3D (według identyfikatora), aby gdy rozwiązanie wyświetla informacje o przestrzeni, mogło również pobrać rysunek 3D z istniejącego systemu. 
* Jeśli rozwiązanie musi śledzić stan online/offline sal konferencyjnych, warto śledzić stan sali konferencyjnej do użycia w wyświetlaniu lub zapytaniach. 

Oba przykłady można zaimplementować z nowymi właściwościami: właściwością, która kojarzy rysowanie 3D z bliźniakiem cyfrowym, i właściwością "online", która wskazuje, czy salę konferencyjną jest `drawingId` w trybie online, czy nie. 

Zazwyczaj nie chcesz bezpośrednio modyfikować ontologii branżowej, ponieważ chcesz mieć możliwość dołączania aktualizacji do rozwiązania w przyszłości (co zastąpiłoby Twoje dodatki). Zamiast tego tego rodzaju dodatki można dodać w hierarchii interfejsu, która stanowi rozszerzenie na podstawie dtdl realEstateCore ontology. Każdy interfejs, który tworzysz, używa wielu dziedziczenia interfejsu w celu rozszerzenia jego nadrzędnego interfejsu RealEstateCore i jego interfejsu nadrzędnego z hierarchii rozszerzonego interfejsu. Takie podejście umożliwia wykorzystanie razem ontologii branżowej i dodatków. 

Aby rozszerzyć ontologię branżową, tworzysz własne interfejsy, które rozszerzają się z interfejsów w ontologii branżowej i dodają nowe możliwości do rozszerzonych interfejsów. Dla każdego interfejsu, który chcesz rozszerzyć, należy utworzyć nowy interfejs. Interfejsy rozszerzone są napisane w języku DTDL (zobacz sekcję DTDL for Extended Interfaces w dalszej części tego dokumentu). 

Po rozszerzeniu części hierarchii przedstawionej powyżej hierarchia rozszerzona wygląda jak na poniższym diagramie. W tym miejscu interfejs rozszerzonej przestrzeni dodaje właściwość , która będzie zawierać identyfikator, który kojarzy cyfrową bliźniacze reprezentacji `drawingId` z rysunkiem 3D. Ponadto interfejs ConferenceRoom dodaje właściwość "online", która będzie zawierać stan online sali konferencyjnej. Dzięki dziedziczeniu interfejs ConferenceRoom zawiera wszystkie możliwości interfejsu RealEstateCore ConferenceRoom, a także wszystkie możliwości interfejsu rozszerzonej przestrzeni. 

:::image type="content" source="media/concepts-ontologies-extend/real-estate-core-extended-2.png" alt-text="Diagram przepływu ilustrujący rozszerzoną hierarchię przestrzeni RealEstateCore z powyższymi większej liczby nowych dodatków. Poziom pomieszczenia jest teraz połączony z elementem Space, który łączy się ze strzałką &quot;rozszerza&quot; w dół o poziom do nowego elementu Room (Pomieszczenie) obok pozycji ConferenceRoom (Pomieszczenie) i Office (Biuro).  Nowe elementy są połączone z istniejącą ontologią przy użyciu bardziej &quot;rozszerzanych&quot; relacji."::: 

## <a name="using-the-extended-space-hierarchy"></a>Korzystanie z hierarchii rozszerzonego miejsca 

Podczas tworzenia bliźniaczych reprezentacji cyfrowych przy użyciu hierarchii rozszerzonej przestrzeni każdy model bliźniaczej reprezentacji cyfrowej będzie pochodził z hierarchii rozszerzonej przestrzeni (a nie oryginalnej ontologii branżowej) i będzie zawierać wszystkie możliwości z ontologii branżowej i rozszerzonych interfejsów za pośrednictwem dziedziczenia interfejsów.

Każdy model bliźniaczej reprezentacji cyfrowej będzie interfejsem z hierarchii rozszerzonej, jak pokazano na poniższym diagramie. 
 
:::image type="content" source="media/concepts-ontologies-extend/ontology-with-models.png" alt-text="Fragment rozszerzonej hierarchii przestrzeni RealEstateCore, w tym space (najwyższy poziom), jeden pokój (środkowy poziom) i ConferenceRoom, Office i FocusRoom (niższy poziom). Nazwy modeli są połączone z każdym elementem (na przykład room jest połączony z modelem o nazwie Room101)."::: 

Podczas wykonywania zapytań o cyfrowe bliźniacze reprezentacji przy użyciu identyfikatora modelu (operatora) należy użyć identyfikatorów modelu z `IS_OF_MODEL` hierarchii rozszerzonej. Na przykład `SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:com:example:Office;1')`. 

## <a name="contributing-back-to-the-original-ontology"></a>Współtworowanie oryginalnej ontologii 

W niektórych przypadkach rozszerzysz ontologię branżową w sposób, który będzie szeroko przydatny dla większości użytkowników ontologii. W takim przypadku należy rozważyć współtwoowanie rozszerzeń z powrotem do oryginalnej ontologii. Każda ontologia ma inny proces współtwożu, dlatego sprawdź repozytorium ontology w serwisie GitHub, aby uzyskać szczegółowe informacje o współtwożu. 

## <a name="dtdl-for-new-interfaces"></a>Język DTDL dla nowych interfejsów 

Kod DTDL dla nowych interfejsów, które rozszerzają się bezpośrednio z ontologii branżowej, będzie wyglądać tak. 

```json
{
  "@id": "dtmi:com:example:FocusRoom;1", 
  "@type": "interface", 
  "extends": "dtmi:digitaltwins:rec_3_3:building:Office;1", 
  "@context": "dtmi:dtdl:context;2" 
} 
```

## <a name="dtdl-for-extended-interfaces"></a>DtDL dla interfejsów rozszerzonych 

Kod DTDL dla interfejsów rozszerzonych ograniczony do omówionej powyżej części będzie wyglądać tak. 

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

Kontynuuj ścieżkę tworzenia modeli w oparciu o ontologie: [*Używanie strategii ontologii w ścieżce projektowania modelu.*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path)