---
title: Modele DTDL
titleSuffix: Azure Digital Twins
description: Dowiedz się, w jaki sposób usługa Azure Digital bliźniaczych reprezentacji używa modeli niestandardowych do opisywania jednostek w środowisku.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: d3570a22fdd935237e673ea3e43ab5e463b66456
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104590538"
---
# <a name="understand-twin-models-in-azure-digital-twins"></a>Opis modeli cyfrowej reprezentacji bliźniaczej w usłudze Azure Digital Twins

Kluczową cechą usługi Azure Digital bliźniaczych reprezentacji jest możliwość definiowania własnego słownictwa i tworzenia grafu bliźniaczyego w samodzielnych warunkach firmy. Ta możliwość jest dostępna za poorednictwem **modeli** udostępnianych przez użytkownika. Można traktować modele jako rzeczowniki w opisie świata. 

Model jest podobny do **klasy** w języku programowania zorientowanym obiektowo, definiując kształt danych dla jednej konkretnej koncepcji w rzeczywistym środowisku pracy. Modele mają nazwy (na przykład *pomieszczenie* lub *czujnik temperatury*) i zawierają takie elementy, jak właściwości, dane telemetryczne/zdarzenia i polecenia opisujące, co może zrobić ten typ jednostki w środowisku. Później te modele są używane do tworzenia [**cyfrowych bliźniaczych reprezentacji**](concepts-twins-graph.md) , które reprezentują konkretne jednostki, które spełniają opis tego typu.

Modele bliźniaczych reprezentacji cyfrowych platformy Azure są reprezentowane w języku JSON-LD-based **Digital (DTDL)**.  

## <a name="digital-twin-definition-language-dtdl-for-models"></a>Digital bliźniaczy Definition Language (DTDL) for models

Modele dla usługi Azure Digital bliźniaczych reprezentacji są zdefiniowane przy użyciu języka Digital bliźniaczych reprezentacji Definition Language (DTDL). 

Możesz wyświetlić pełne specyfikacje języka dla DTDL w usłudze GitHub: [**Digital bliźniaczych reprezentacji Definition Language (DTDL) — wersja 2**](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).

Język DTDL jest oparty na formacie JSON-LD i nie jest zależny od języka programowania. Usługa DTDL nie jest wyłączana wyłącznie do usługi Azure Digital bliźniaczych reprezentacji, ale jest również używana do reprezentowania danych urządzenia w innych usługach IoT, takich jak [IoT Plug and Play](../iot-pnp/overview-iot-plug-and-play.md). Usługa Azure Digital bliźniaczych reprezentacji korzysta z usługi DTDL w **wersji 2** (użycie DTDL w wersji 1 z usługą Azure Digital bliźniaczych reprezentacji jest już przestarzałe). 

Pozostała część tego artykułu zawiera podsumowanie sposobu używania języka w usłudze Azure Digital bliźniaczych reprezentacji.

> [!NOTE] 
> Nie wszystkie usługi korzystające z DTDL implementują dokładnie te same funkcje programu DTDL. Na przykład usługa IoT Plug and Play nie korzysta z funkcji DTDL, które są dostępne w przypadku wykresów, podczas gdy program Azure Digital bliźniaczych reprezentacji nie implementuje obecnie poleceń DTDL.
>
> Aby uzyskać więcej informacji na temat funkcji DTDL, które są specyficzne dla usługi Azure Digital bliźniaczych reprezentacji, zobacz sekcję w dalszej części tego artykułu dotyczącej [specyfiki implementacji usługi Azure Digital bliźniaczych reprezentacji DTDL](#azure-digital-twins-dtdl-implementation-specifics).

## <a name="elements-of-a-model"></a>Elementy modelu

W ramach definicji modelu element kodu najwyższego poziomu jest **interfejsem**. To hermetyzuje cały model, a reszta modelu jest definiowana w interfejsie. 

Interfejs modelu DTDL może zawierać zero, jeden lub wiele z następujących pól:
* Właściwości **Właściwości** to pola danych, które reprezentują stan jednostki (na przykład właściwości w wielu językach programowania zorientowanego obiektowo). Właściwości mają magazyn zapasowy i mogą być odczytywane w dowolnym momencie.
* Pola **telemetrii** danych telemetrycznych reprezentują pomiary lub zdarzenia i są często używane do opisywania odczytów czujników urządzeń. W przeciwieństwie do właściwości, dane telemetryczne nie są przechowywane w postaci cyfrowej przędzy; jest to seria zdarzeń związanych z danymi, które muszą być obsługiwane w miarę ich występowania. Aby uzyskać więcej informacji na temat różnic między właściwością i telemetrią, zobacz sekcję [*Właściwości a Telemetria*](#properties-vs-telemetry) poniżej.
* Składniki **składnika** umożliwiają tworzenie interfejsu modelu jako zestawu innych interfejsów, jeśli chcesz. Przykładem składnika jest interfejs *frontCamera* (i inny interfejs *składnika),* który jest używany do definiowania modelu dla *telefonu*. Najpierw należy zdefiniować interfejs dla *frontCamera* , jakby był własnym modelem, a następnie można odwoływać się do niego przy definiowaniu *telefonu*.

    Użyj składnika, aby opisać element, który jest integralną częścią Twojego rozwiązania, ale nie wymaga oddzielnej tożsamości i nie musi być tworzony, usunięty ani ponownie rozmieszczenia w grafie bliźniaczym. Jeśli chcesz, aby jednostki miały niezależne istnienie na grafie bliźniaczym, reprezentują je jako oddzielne cyfrowe bliźniaczych reprezentacji różnych modeli, połączone przez *relacje* (Zobacz następny punktor).
    
    >[!TIP] 
    >Składników można także używać dla organizacji, aby grupować zestawy powiązanych właściwości w interfejsie modelu. W takiej sytuacji można traktować każdy składnik jako przestrzeń nazw lub "folder" w interfejsie.
* Relacje między **relacjami** umożliwiają prezentowanie sposobu, w jaki można polegać na cyfrowym przędze za pomocą innych bliźniaczych reprezentacji cyfrowych. Relacje mogą reprezentować różne orednie semantyczne, takie jak *Contains* ("piętro zawiera pomieszczenie"), *chłodnie* ("pomieszczenie chłodzenia HVAC"), *isBilledTo* ("kompresor jest rozliczany na użytkownika") itd. Relacje umożliwiają rozwiązanie udostępnienie grafu powiązanych jednostek.

> [!NOTE]
> [Specyfikacja DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) definiuje także **polecenia**, które są metodami, które mogą być wykonywane na dwucyfrowej sznurze (na przykład polecenie Reset lub polecenie w celu przełączenia lub wyłączenia wentylatora). *Polecenia nie są jednak obecnie obsługiwane w usłudze Azure Digital bliźniaczych reprezentacji.*

### <a name="properties-vs-telemetry"></a>Właściwości a Telemetria

Poniżej znajdują się dodatkowe wskazówki dotyczące rozróżniania pól **Właściwości** DTDL i **telemetrii** w usłudze Azure Digital bliźniaczych reprezentacji.

Różnica między właściwościami i telemetrią dla modeli usługi Azure Digital bliźniaczych reprezentacji jest następująca:
* **Właściwości** powinny mieć magazyn zapasowy. Oznacza to, że można odczytać właściwość w dowolnym momencie i pobrać jej wartość. Jeśli właściwość jest zapisywalna, można również zapisać wartość we właściwości.  
* Dane **telemetryczne** są bardziej podobne do strumienia zdarzeń; jest to zestaw komunikatów danych, które mają krótki lifespans. Jeśli nie skonfigurowano nasłuchiwania dla zdarzenia i akcji, które mają być podejmowane w momencie wystąpienia, nie ma śledzenia zdarzenia w późniejszym czasie. Nie można wrócić do niego i przeczytać go później. 
  - W terminologii w języku C# dane telemetryczne przypominają zdarzenie w języku C#. 
  - W przypadku rzeczy IoT dane telemetryczne są zazwyczaj pojedynczej miary wysyłanej przez urządzenie.

Dane **telemetryczne** są często używane w przypadku urządzeń IoT, ponieważ wiele urządzeń nie jest w stanie, ani interesuje, przechowywanie wartości miary, które generują. Po prostu wysyłają je jako strumień zdarzeń "telemetrii". W takim przypadku nie można w dowolnym momencie wykonać zapytania o urządzenie w celu uzyskania najnowszej wartości pola telemetrii. Zamiast tego należy nasłuchiwać komunikatów z urządzenia i podejmować działania w miarę nadejścia komunikatów. 

W związku z tym podczas projektowania modelu w usłudze Azure Digital bliźniaczych reprezentacji prawdopodobnie będziesz używać **Właściwości** w większości przypadków, aby modelować bliźniaczych reprezentacji. Pozwala to na przechowywanie kopii zapasowych oraz możliwość odczytywania i wykonywania zapytań dotyczących pól danych.

Dane telemetryczne i właściwości często współpracują ze sobą, aby obsługiwać ruch przychodzący z urządzeń. Ponieważ wszystkie dane przychodzące do usługi Azure Digital bliźniaczych reprezentacji są realizowane za pośrednictwem [interfejsów API](how-to-use-apis-sdks.md), zazwyczaj używana jest funkcja transferu danych przychodzących do odczytywania zdarzeń telemetrii lub właściwości z urządzeń oraz ustawiania właściwości w usłudze Azure Digital bliźniaczych reprezentacji w odpowiedzi. 

Możesz również opublikować wydarzenie telemetryczne z interfejsu API Digital bliźniaczych reprezentacji systemu Azure. Podobnie jak w przypadku innych danych telemetrycznych, to jest zdarzenie krótkoterminowe wymagające odbiornika do obsłużenia.

### <a name="azure-digital-twins-dtdl-implementation-specifics"></a>Specyficzne dla implementacji usługi Azure Digital bliźniaczych reprezentacji DTDL

Aby model DTDL był zgodny z usługą Azure Digital bliźniaczych reprezentacji, musi spełniać te wymagania.

* Wszystkie elementy DTDL najwyższego poziomu w modelu muszą być typu *Interface*. Wynika to z faktu, że interfejsy API modelu Digital bliźniaczych reprezentacji systemu Azure mogą odbierać obiekty JSON, które reprezentują interfejs lub tablicę interfejsów. W związku z tym żadne inne typy elementów DTDL nie są dozwolone na najwyższym poziomie.
* DTDL dla usługi Azure Digital bliźniaczych reprezentacji nie może definiować żadnych *poleceń*.
* Usługa Azure Digital bliźniaczych reprezentacji umożliwia tylko pojedynczy poziom zagnieżdżenia składnika. Oznacza to, że interfejs używany jako składnik nie może mieć samych składników. 
* Interfejsów nie można definiować w innych interfejsach DTDL; muszą być zdefiniowane jako osobne jednostki najwyższego poziomu z ich własnymi identyfikatorami. Następnie, gdy inny interfejs chce dołączyć ten interfejs jako składnik lub przez dziedziczenie, może odwoływać się do jego identyfikatora.

Usługa Azure Digital bliźniaczych reprezentacji również nie jest zgodna z `writable` atrybutem właściwości ani relacji. Chociaż można ją ustawić zgodnie ze specyfikacją DTDL, wartość nie jest używana przez usługę Azure Digital bliźniaczych reprezentacji. Zamiast tego są one zawsze traktowane jako zapisywalne przez klientów zewnętrznych, którzy mają ogólne uprawnienia do zapisu w usłudze Azure Digital bliźniaczych reprezentacji.

## <a name="example-model-code"></a>Przykładowy kod modelu

Modele typu sznurka można pisać w dowolnym edytorze tekstu. Język DTDL jest następujący: Składnia JSON, dlatego należy przechowywać modele z rozszerzeniem *JSON*. Użycie rozszerzenia JSON spowoduje włączenie wielu edytorów tekstu programistycznego, aby zapewnić podstawowe sprawdzanie składni i wyróżnianie dokumentów DTDL. Istnieje również [rozszerzenie DTDL](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) dostępne dla [Visual Studio Code](https://code.visualstudio.com/).

Ta sekcja zawiera przykład typowego modelu, który został zapisany jako interfejs DTDL. Model opisuje **planety**, każdy z nazwą, masą i temperaturą.
 
Należy wziąć pod uwagę, że planety może także wchodzić w pracę z **przyzwyczajami** , które są swoimi satelitami i mogą zawierać **kontenery**. W poniższym przykładzie `Planet` model wyraża połączenia z tymi innymi jednostkami, odwołując się do dwóch modeli zewnętrznych — `Moon` i `Crater` . Te modele są również zdefiniowane w przykładowym kodzie poniżej, ale są bardzo proste, aby nie rozciągać się z podstawowego `Planet` przykładu.

:::code language="json" source="~/digital-twins-docs-samples/models/Planet-Crater-Moon.json":::

Pola modelu są następujące:

| Pole | Opis |
| --- | --- |
| `@id` | Identyfikator dla modelu. Musi być w formacie `dtmi:<domain>:<unique model identifier>;<model version number>` . |
| `@type` | Określa rodzaj opisywanych informacji. Dla interfejsu typ jest *interfejs*. |
| `@context` | Ustawia [kontekst](https://niem.github.io/json/reference/json-ld/context/) dla dokumentu JSON. Powinny być używane modele `dtmi:dtdl:context;2` . |
| `displayName` | obowiązkowe Pozwala nadać modelowi przyjazną nazwę w razie potrzeby. |
| `contents` | Wszystkie pozostałe dane interfejsu są umieszczane w tym miejscu jako tablica definicji atrybutów. Każdy atrybut musi dostarczyć `@type` (*Właściwość*, dane *telemetryczne*, *polecenie*, *relacja* lub *składnik*), aby zidentyfikować informacje o interfejsie, które opisuje, a następnie zestaw właściwości, które definiują rzeczywisty atrybut (na przykład `name` i `schema` Aby zdefiniować *Właściwość*). |

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

Czasami warto utworzyć bardziej wyspecjalizowany model. Na przykład może być przydatne w przypadku ogólnego *pokoju* modeli oraz wyspecjalizowanych wariantów *ConferenceRoom* i *treningów*. Do wyrażenia specjalizacji DTDL obsługuje dziedziczenie: interfejsy mogą dziedziczyć z jednego lub kilku innych interfejsów. 

Poniższy przykład ponownie Przypuść model *globalnej* z wcześniejszego przykładu DTDL jako podtyp większego modelu *CelestialBody* . Model "nadrzędny" jest definiowany jako pierwszy, a następnie model "podrzędny" kompiluje na nim przy użyciu pola `extends` .

:::code language="json" source="~/digital-twins-docs-samples/models/CelestialBody-Planet-Crater.json":::

W tym przykładzie *CelestialBody* współtworzy nazwę, masę i temperaturę do *globalnej*. `extends`Sekcja jest nazwą interfejsu lub tablicą nazw interfejsów (umożliwiając rozszerzanie interfejsu w razie potrzeby dziedziczenie z wielu modeli nadrzędnych).

Po zastosowaniu dziedziczenia interfejs rozszerzający udostępnia wszystkie właściwości z całego łańcucha dziedziczenia.

Interfejs rozszerzający nie może zmienić żadnej definicji interfejsów nadrzędnych. może tylko dodać do nich. Nie można również przedefiniować możliwości już zdefiniowanej w żadnym z jego interfejsów nadrzędnych (nawet jeśli możliwości są zdefiniowane jako takie same). Na przykład, jeśli interfejs nadrzędny definiuje `double` *masę* właściwości, interfejs rozszerzający nie może zawierać deklaracji *masy* nawet wtedy, gdy jest również `double` .

## <a name="best-practices-for-designing-models"></a>Najlepsze rozwiązania dotyczące projektowania modeli

Podczas projektowania modeli w celu odzwierciedlenia jednostek w środowisku przydatne może być poszukiwanie i rozpatrywanie implikacji związanych z [zapytaniami](concepts-query-language.md) . Można zaprojektować właściwości w taki sposób, aby uniknąć dużego zestawu wyników z przechodzenia do wykresu. Możesz również chcieć modelować relacje, które będą odpowiadały w pojedynczym zapytaniu jako relacje jednego poziomu.

### <a name="validating-models"></a>Sprawdzanie poprawności modeli

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="tools-for-models"></a>Narzędzia dla modeli 

Dostępnych jest kilka przykładów, które ułatwiają korzystanie z modeli i ontologie. Znajdują się one w tym repozytorium: [Narzędzia dla języka Digital bliźniaczych reprezentacji Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-tools).

W tej sekcji opisano bieżący zestaw przykładów w bardziej szczegółowy sposób.

### <a name="model-uploader"></a>Obiektu przekazującego modelu 

_**Przekazywanie modeli do usługi Azure Digital bliźniaczych reprezentacji**_

Po zakończeniu tworzenia, rozszerzania lub wybierania modeli można przekazać je do wystąpienia usługi Azure Digital bliźniaczych reprezentacji, aby udostępnić je do użycia w rozwiązaniu. Odbywa się to za pomocą [cyfrowych interfejsów API usługi Azure bliźniaczych reprezentacji](how-to-use-apis-sdks.md), zgodnie z opisem w temacie [*How to: Manage DTDL models*](how-to-manage-model.md#upload-models).

Jeśli jednak masz wiele modeli do przekazania — lub jeśli masz wiele współzależności, które spowodują porządkowanie poszczególnych przeciążeń, możesz użyć tego przykładu do przekazania wielu modeli jednocześnie: [**Azure Digital bliźniaczych reprezentacji model obiektu przekazującego**](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/ModelUploader). Postępuj zgodnie z instrukcjami podanymi w przykładzie, aby skonfigurować i użyć tego projektu do przekazywania modeli do własnego wystąpienia.

### <a name="model-visualizer"></a>Wizualizator modelu 

_**Dla modeli wizualizacji**_

Po przekazaniu modeli do wystąpienia usługi Azure Digital bliźniaczych reprezentacji można wyświetlić modele w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji, w tym wszelkie dziedziczenie i relacje z modelami, przy użyciu [**wizualizatora modeli cyfrowych bliźniaczych reprezentacji platformy Azure**](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/AdtModelVisualizer). Ten przykład jest obecnie w stanie wersji roboczej. Zachęcamy społeczność ds. projektowania Digital bliźniaczych reprezentacji do rozwinięcia i współtworzenia przykładu. 

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o tworzeniu modeli opartych na standardach branżowych ontologie: [ *koncepcje: co to jest Ontology?*](concepts-ontologies.md)

* Szczegółowe bardziej szczegółowe Zarządzanie modelami przy użyciu operacji interfejsu API: [ *How to: Manage DTDL models*](how-to-manage-model.md)

* Dowiedz się, jak modele są używane do tworzenia cyfrowych bliźniaczych reprezentacji: [ *pojęcia: Digital bliźniaczych reprezentacji i wykres bliźniaczy*](concepts-twins-graph.md)

