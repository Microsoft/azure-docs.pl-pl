---
title: Modele niestandardowe
titleSuffix: Azure Digital Twins
description: Dowiedz się, jak usługa Azure Digital bliźniaczych reprezentacji używa modeli zdefiniowanych przez użytkownika do opisywania jednostek w danym środowisku.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 30bf5d3eaa034c033cf32e26c3229a028f036313
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84613138"
---
# <a name="understand-twin-models-in-azure-digital-twins"></a>Zrozumienie modeli bliźniaczych w usłudze Azure Digital bliźniaczych reprezentacji

Kluczową cechą usługi Azure Digital bliźniaczych reprezentacji jest możliwość definiowania własnego słownictwa i tworzenia grafu bliźniaczyego w samodzielnych warunkach firmy. Ta możliwość jest dostępna za poorednictwem **modeli**zdefiniowanych przez użytkownika. Można traktować modele jako rzeczowniki w opisie świata. 

Model jest podobny do **klasy** w języku programowania zorientowanym obiektowo, definiując kształt danych dla jednej konkretnej koncepcji w rzeczywistym środowisku pracy. Modele mają nazwy (na przykład *pomieszczenie* lub *czujnik temperatury*) i zawierają takie elementy, jak właściwości, dane telemetryczne/zdarzenia i polecenia opisujące, co może zrobić ten typ jednostki w środowisku. Później te modele są używane do tworzenia [**cyfrowych bliźniaczych reprezentacji**](concepts-twins-graph.md) , które reprezentują konkretne jednostki, które spełniają opis tego typu.

Modele są zapisywane przy użyciu **języka Digital bliźniaczy (DTDL)** w formacie JSON.  

## <a name="digital-twin-definition-language-dtdl-for-writing-models"></a>Digital bliźniaczy Definition Language (DTDL) do pisania modeli

Modele dla usługi Azure Digital bliźniaczych reprezentacji są zdefiniowane przy użyciu języka Digital bliźniaczych reprezentacji Definition Language (DTDL). DTDL jest oparta na formacie JSON-LD i nie jest zależna od języka programowania. Usługa DTDL nie jest wyłączana wyłącznie do usługi Azure Digital bliźniaczych reprezentacji, ale jest również używana do reprezentowania danych urządzenia w innych usługach IoT, takich jak [IoT Plug and Play](../iot-pnp/overview-iot-plug-and-play.md). Usługa Azure Digital bliźniaczych reprezentacji używa DTDL w *wersji 2*.

> [!TIP] 
> Nie wszystkie usługi korzystające z DTDL implementują dokładnie te same funkcje programu DTDL. Na przykład usługa IoT Plug and Play nie korzysta z funkcji DTDL, które są dostępne w przypadku wykresów, podczas gdy program Azure Digital bliźniaczych reprezentacji nie implementuje obecnie poleceń DTDL. Aby uzyskać więcej informacji na temat funkcji DTDL, które są specyficzne dla usługi Azure Digital bliźniaczych reprezentacji, zobacz sekcję w dalszej części tego artykułu dotyczącej [specyfiki implementacji usługi Azure Digital bliźniaczych reprezentacji DTDL](#azure-digital-twins-dtdl-implementation-specifics).

Aby uzyskać więcej informacji na temat DTDL, zobacz dokumentację specyfikacji w witrynie GitHub: [Digital bliźniaczych reprezentacji Definition Language (DTDL) — wersja 2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).

## <a name="elements-of-a-model"></a>Elementy modelu

W ramach definicji modelu element kodu najwyższego poziomu jest **interfejsem**. To hermetyzuje cały model, a reszta modelu jest definiowana w interfejsie. 

Interfejs modelu DTDL może zawierać zero, jeden lub wiele z następujących pól:
* Właściwości **Właściwości** to pola danych, które reprezentują stan jednostki (na przykład właściwości w wielu językach programowania zorientowanego obiektowo). W przeciwieństwie do telemetrii, która jest zdarzeniem związanym z danymi, właściwości mają magazyn zapasowy i mogą być odczytywane w dowolnym momencie.
* Pola **telemetrii** danych telemetrycznych reprezentują pomiary lub zdarzenia i są często używane do opisywania odczytów czujników urządzeń. Dane telemetryczne nie są przechowywane w postaci cyfrowej dwuosiowej; jest to bardziej podobne do strumienia zdarzeń danych gotowych do wysłania w dowolnym miejscu. 
* Składniki **składnika** umożliwiają tworzenie interfejsu modelu jako zestawu innych interfejsów, jeśli chcesz. Przykładem składnika jest interfejs *frontCamera* (i inny interfejs *składnika),* który jest używany do definiowania modelu dla *telefonu*. Najpierw należy zdefiniować interfejs dla *frontCamera* , jakby był własnym modelem, a następnie można odwoływać się do niego przy definiowaniu *telefonu*.

    Użyj składnika, aby opisać element, który jest integralną częścią Twojego rozwiązania, ale nie wymaga oddzielnej tożsamości i nie musi być tworzony, usunięty ani ponownie rozmieszczenia w grafie bliźniaczym. Jeśli chcesz, aby jednostki miały niezależne istnienie na grafie bliźniaczym, reprezentują je jako oddzielne cyfrowe bliźniaczych reprezentacji różnych modeli, połączone przez *relacje* (Zobacz następny punktor).
    
    >[!TIP] 
    >Składników można także używać dla organizacji, aby grupować zestawy powiązanych właściwości w interfejsie modelu. W takiej sytuacji można traktować każdy składnik jako przestrzeń nazw lub "folder" w interfejsie.
* Relacje między **relacjami** umożliwiają prezentowanie sposobu, w jaki można polegać na cyfrowym przędze za pomocą innych bliźniaczych reprezentacji cyfrowych. Relacje mogą reprezentować różne orednie semantyczne, takie jak *Contains* ("piętro zawiera pomieszczenie"), *chłodnie* ("pomieszczenie chłodzenia HVAC"), *isBilledTo* ("kompresor jest rozliczany na użytkownika") itd. Relacje umożliwiają rozwiązanie udostępnienie grafu powiązanych jednostek.

> [!NOTE]
> Specyfikacja DTDL definiuje także **polecenia**, które są metodami, które mogą być wykonywane na dwucyfrowej sznurze (na przykład polecenie Reset lub polecenie w celu przełączenia lub wyłączenia wentylatora). *Polecenia nie są jednak obecnie obsługiwane w usłudze Azure Digital bliźniaczych reprezentacji.*

### <a name="azure-digital-twins-dtdl-implementation-specifics"></a>Specyficzne dla implementacji usługi Azure Digital bliźniaczych reprezentacji DTDL

Aby model DTDL był zgodny z usługą Azure Digital bliźniaczych reprezentacji, musi spełniać te wymagania.

* Wszystkie elementy DTDL najwyższego poziomu w modelu muszą być typu *Interface*. Wynika to z faktu, że interfejsy API modelu Digital bliźniaczych reprezentacji systemu Azure mogą odbierać obiekty JSON, które reprezentują interfejs lub tablicę interfejsów. W związku z tym żadne inne typy elementów DTDL nie są dozwolone na najwyższym poziomie.
* DTDL dla usługi Azure Digital bliźniaczych reprezentacji nie może definiować żadnych *poleceń*.
* Usługa Azure Digital bliźniaczych reprezentacji umożliwia tylko pojedynczy poziom zagnieżdżenia składnika. Oznacza to, że interfejs używany jako składnik nie może mieć samych składników. 
* Interfejsów nie można definiować w innych interfejsach DTDL; muszą być zdefiniowane jako osobne jednostki najwyższego poziomu z ich własnymi identyfikatorami. Następnie, gdy inny interfejs chce dołączyć ten interfejs jako składnik lub przez dziedziczenie, może odwoływać się do jego identyfikatora.

## <a name="example-model-code"></a>Przykładowy kod modelu

Modele typu sznurka można pisać w dowolnym edytorze tekstu. Język DTDL jest następujący: Składnia JSON, dlatego należy przechowywać modele z rozszerzeniem *JSON*. Użycie rozszerzenia JSON spowoduje włączenie wielu edytorów tekstu programistycznego, aby zapewnić podstawowe sprawdzanie składni i wyróżnianie dokumentów DTDL. Istnieje również [rozszerzenie DTDL](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) dostępne dla [Visual Studio Code](https://code.visualstudio.com/).

Oto przykład typowego modelu, który został zapisany jako interfejs DTDL. Model opisuje planety, każdy z nazwą, masą i temperaturą. Globalnej mogą mieć przyzwyczajki satelitarne i mogą zawierać kontenery.

```json
[
  {
    "@id": "dtmi:com:contoso:Planet;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "displayName": "Planet",
    "contents": [
      {
        "@type": "Property",
        "name": "name",
        "schema": "string"
      },
      {
        "@type": "Property",
        "name": "mass",
        "schema": "double"
      },
      {
        "@type": "Telemetry",
        "name": "Temperature",
        "schema": "double"
      },
      {
        "@type": "Relationship",
        "name": "satellites",
        "target": "dtmi:com:contoso:Moon;1"
      },
      {
        "@type": "Component",
        "name": "deepestCrater",
        "schema": "dtmi:com:contoso:Crater;1"
      }
    ]
  },
  {
    "@id": "dtmi:com:contoso:Crater;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2"
  }
]
```

Pola modelu są następujące:

| Pole | Opis |
| --- | --- |
| `@id` | Identyfikator dla modelu. Musi być w formacie `dtmi:<domain>:<unique model identifier>;<model version number>` . |
| `@type` | Określa rodzaj opisywanych informacji. Dla interfejsu typ jest *interfejs*. |
| `@context` | Ustawia [kontekst](https://niem.github.io/json/reference/json-ld/context/) dla dokumentu JSON. Powinny być używane modele `dtmi:dtdl:context;2` . |
| `displayName` | obowiązkowe Pozwala nadać modelowi przyjazną nazwę w razie potrzeby. |
| `contents` | Wszystkie pozostałe dane interfejsu są umieszczane w tym miejscu jako tablica definicji atrybutów. Każdy atrybut musi dostarczyć `@type` (*Właściwość*, dane *telemetryczne*, *polecenie*, *relacja*lub *składnik*), aby zidentyfikować informacje o interfejsie, które opisuje, a następnie zestaw właściwości, które definiują rzeczywisty atrybut (na przykład `name` i `schema` Aby zdefiniować *Właściwość*). |

> [!NOTE]
> Należy zauważyć, że interfejs składnika (*Crater* w tym przykładzie) jest zdefiniowany w tej samej tablicy co interfejs, który go używa (*globalnej*). Składniki muszą być zdefiniowane w ten sposób w wywołaniach interfejsu API, aby można było znaleźć interfejs.

### <a name="possible-schemas"></a>Możliwe schematy

Zgodnie z DTDL schemat dla atrybutów *Właściwości* i *telemetrii* może być standardowym typem pierwotnym — `integer` ,, `double` `string` , i `Boolean` — i innymi typami, takimi jak `DateTime` i `Duration` . 

Oprócz typów pierwotnych pola *Właściwości* i *telemetrii* mogą mieć następujące typy złożone:
* `Object`
* `Map`
* `Enum`

Również pola *telemetrii* obsługują `Array` .

### <a name="model-inheritance"></a>Dziedziczenie modelu

Czasami warto utworzyć bardziej wyspecjalizowany model. Na przykład może być przydatne w przypadku ogólnego *pokoju*modeli oraz wyspecjalizowanych wariantów *ConferenceRoom* i *treningów*. Do wyrażenia specjalizacji DTDL obsługuje dziedziczenie: interfejsy mogą dziedziczyć z jednego lub kilku innych interfejsów. 

Poniższy przykład ponownie Przypuść model *globalnej* z wcześniejszego przykładu DTDL jako podtyp większego modelu *CelestialBody* . Model "nadrzędny" jest definiowany jako pierwszy, a następnie model "podrzędny" kompiluje na nim przy użyciu pola `extends` .

```json
[
  {
    "@id": "dtmi:com:contoso:CelestialBody;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "displayName": "Celestial body",
    "contents": [
      {
        "@type": "Property",
        "name": "name",
        "schema": "string"
      },
      {
        "@type": "Property",
        "name": "mass",
        "schema": "double"
      },
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double"
      }
    ]
  },
  {
    "@id": "dtmi:com:contoso:Planet;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "displayName": "Planet",
    "extends": "dtmi:com:contoso:CelestialBody;1",
    "contents": [
      {
        "@type": "Relationship",
        "name": "satellites",
        "target": "dtmi:com:contoso:Moon;1"
      },
      {
        "@type": "Component",
        "name": "deepestCrater",
        "schema": "dtmi:com:contoso:Crater;1"
      }
    ]
  },
  {
    "@id": "dtmi:com:contoso:Crater;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2"
  }
]
```

W tym przykładzie *CelestialBody* współtworzy nazwę, masę i temperaturę do *globalnej*. `extends`Sekcja jest nazwą interfejsu lub tablicą nazw interfejsów (umożliwiając rozszerzanie interfejsu w razie potrzeby dziedziczenie z wielu modeli nadrzędnych).

Po zastosowaniu dziedziczenia interfejs rozszerzający udostępnia wszystkie właściwości z całego łańcucha dziedziczenia.

Interfejs rozszerzający nie może zmienić żadnej definicji interfejsów nadrzędnych. może tylko dodać do nich. Nie można również przedefiniować możliwości już zdefiniowanej w żadnym z jego interfejsów nadrzędnych (nawet jeśli możliwości są zdefiniowane jako takie same). Na przykład, jeśli interfejs nadrzędny definiuje `double` *masę*właściwości, interfejs rozszerzający nie może zawierać deklaracji *masy*nawet wtedy, gdy jest również `double` .

## <a name="validating-models"></a>Sprawdzanie poprawności modeli

Dostępny jest przykład do sprawdzania poprawności dokumentów modelu, aby upewnić się, że DTDL jest prawidłowy. Jest on oparty na bibliotece DTDL parser i ma język niezależny od. Znajdź tutaj: [DTDL moduł sprawdzania poprawności](https://github.com/Azure-Samples/DTDL-Validator).

Lub, aby uzyskać więcej informacji na temat biblioteki parsera, w tym przykład korzystania z niego bezpośrednio, zobacz [How to: Parse and Validate models](how-to-use-parser.md).

## <a name="next-steps"></a>Następne kroki

Zobacz, jak zarządzać modelami przy użyciu interfejsów API DigitalTwinsModels:
* [Instrukcje: Zarządzanie modelem bliźniaczym](how-to-manage-model.md)

Można też dowiedzieć się, jak są tworzone cyfrowe bliźniaczych reprezentacji na podstawie modeli:
* [Pojęcia: Digital bliźniaczych reprezentacji i wykres bliźniaczy](concepts-twins-graph.md)

