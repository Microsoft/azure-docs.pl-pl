---
title: Modele DTDL
titleSuffix: Azure Digital Twins
description: Dowiedz się, Azure Digital Twins używa modeli niestandardowych do opisywania jednostek w twoim środowisku.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 8942262c2e02670d57b1db324eb154dcc38f00f8
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575398"
---
# <a name="understand-twin-models-in-azure-digital-twins"></a>Opis modeli cyfrowej reprezentacji bliźniaczej w usłudze Azure Digital Twins

Kluczową cechą Azure Digital Twins jest możliwość definiowania własnego słownictwa i tworzenia grafu bliźniaczych reprezentacji w zdefiniowanych samodzielnie terminach firmy. Ta możliwość jest zapewniana za pośrednictwem modeli dostarczanych przez **użytkownika.** Modele można myśleć jak o rzeczownikach w opisie swojego świata. 

Model jest podobny do klasy **w** języku programowania zorientowanym obiektowo, definiując kształt danych dla jednej konkretnej koncepcji w rzeczywistym środowisku pracy. Modele mają nazwy (takie jak *Room* lub *TemperatureSensor)* i zawierają elementy, takie jak właściwości, dane telemetryczne/zdarzenia i polecenia, które opisują, co ten typ jednostki może zrobić w środowisku. Później użyjemy tych modeli do utworzenia [**bliźniaczych**](concepts-twins-graph.md) reprezentacji cyfrowych reprezentujących określone jednostki spełniające opis tego typu.

Azure Digital Twins są reprezentowane w języku **DTDL (Digital Twin Definition Language)** opartym na technologii JSON-LD.  

## <a name="digital-twin-definition-language-dtdl-for-models"></a>Digital Twin Definition Language (DTDL) dla modeli

Modele dla Azure Digital Twins są definiowane przy użyciu języka Digital Twins Definition Language (DTDL). 

Pełną specyfikację języka DTDL można wyświetlić w witrynie GitHub: [**Digital Twins Definition Language (DTDL) — wersja 2.**](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)

Język DTDL jest oparty na formacie JSON-LD i nie jest zależny od języka programowania. DtDL nie jest wyłącznym Azure Digital Twins, ale jest również używany do reprezentowania danych urządzenia w innych usługach IoT, takich [jak IoT Plug and Play.](../iot-pnp/overview-iot-plug-and-play.md) Azure Digital Twins języka DTDL **w wersji 2** (użycie języka DTDL w wersji 1 z Azure Digital Twins jest już przestarzałe). 

W pozostałej części tego artykułu podsumowano sposób, w jaki język jest używany w Azure Digital Twins.

### <a name="azure-digital-twins-dtdl-implementation-specifics"></a>Azure Digital Twins implementacji DTDL

Nie wszystkie usługi, które używają języka DTDL, implementują dokładnie te same funkcje języka DTDL. Na przykład IoT Plug and Play funkcji DTDL, które są dla grafów, podczas gdy Azure Digital Twins obecnie nie implementuje poleceń DTDL. 

Aby model DTDL był zgodny z Azure Digital Twins, musi spełniać następujące wymagania:

* Wszystkie elementy DTDL najwyższego poziomu w modelu muszą być interfejsem *typu*. Wynika to z Azure Digital Twins API modelu mogą odbierać obiekty JSON reprezentujące interfejs lub tablicę interfejsów. W związku z tym żadne inne typy elementów DTDL nie są dozwolone na najwyższym poziomie.
* DtDL dla Azure Digital Twins nie może definiować żadnych *poleceń*.
* Azure Digital Twins tylko jeden poziom zagnieżdżania składników. Oznacza to, że interfejs używany jako składnik nie może mieć żadnych składników. 
* Interfejsów nie można zdefiniować w tekście w innych interfejsach DTDL; Muszą być zdefiniowane jako oddzielne jednostki najwyższego poziomu z własnymi identyfikatorami. Następnie, gdy inny interfejs chce dołączyć ten interfejs jako składnik lub za pośrednictwem dziedziczenia, może odwoływać się do jego identyfikatora.

Azure Digital Twins również atrybut nie jest `writable` obserwowany we właściwościach lub relacjach. Mimo że można to ustawić zgodnie ze specyfikacjami DTDL, ta wartość nie jest używana przez Azure Digital Twins. Zamiast tego są one zawsze traktowane jako zapisywalne przez klientów zewnętrznych, którzy mają ogólne uprawnienia do zapisu Azure Digital Twins usługi.

## <a name="elements-of-a-model"></a>Elementy modelu

W ramach definicji modelu element kodu najwyższego poziomu jest **interfejsem**. To hermetyzuje cały model, a reszta modelu jest definiowana w interfejsie. 

Interfejs modelu DTDL może zawierać zero, jeden lub wiele z następujących pól:
* **Właściwość** — właściwości to pola danych reprezentujące stan jednostki (takie jak właściwości w wielu językach programowania obiektowego). Właściwości mają magazyn zapasowy i mogą być odczytywane w dowolnym momencie.
* **Telemetria** — pola telemetrii reprezentują miary lub zdarzenia i są często używane do opisywania odczytów czujników urządzeń. W przeciwieństwie do właściwości telemetria nie jest przechowywana w bliźniaczej reprezentacji cyfrowej; Jest to szereg zdarzeń danych ograniczonych czasem, które muszą być obsługiwane w czasie ich wystąpienia. Aby uzyskać więcej informacji na temat różnic między właściwościami i telemetrią, zobacz [*sekcję Właściwości a telemetria*](#properties-vs-telemetry) poniżej.
* **Składnik** — składniki umożliwiają tworzenie interfejsu modelu jako zestawu innych interfejsów, jeśli chcesz. Przykładem składnika jest interfejs *frontCamera* (i inny interfejs składnika *backCamera),* który jest używany do definiowania modelu dla *telefonu*. Najpierw należy zdefiniować interfejs dla *frontCamera,* tak jakby był to własny model, a następnie można się do niego odwoływać podczas definiowania *telefonu*.

    Użyj składnika do opisania czegoś, co jest integralną częścią rozwiązania, ale nie wymaga oddzielnej tożsamości i nie musi być niezależnie tworzone, usuwane ani zmieniane w grafie bliźniaczej reprezentacji. Jeśli chcesz, aby jednostki były niezależne w grafie bliźniaczych reprezentacji, reprezentują je jako oddzielne cyfrowe bliźniacze reprezentacje różnych modeli połączone relacjami *(zobacz* następny punktor).
    
    >[!TIP] 
    >Składniki mogą być również używane w organizacji do grupowania zestawów powiązanych właściwości w interfejsie modelu. W takiej sytuacji każdy składnik można nazwać przestrzenią nazw lub "folderem" w interfejsie.
* **Relacja** — relacje umożliwiają reprezentację sposobu, w jaki cyfrowa reprezentacja może być zaangażowana w inne bliźniacze reprezentacje cyfrowe. Relacje mogą reprezentować różne znaczenie semantyczne, takie jak *contains* ("floor contains room"), *cools* ("hvac cools room"), *isBilledTo* ("na rachunku jest rozliczany użytkownik") itp. Relacje umożliwiają rozwiązaniu dostarczenie grafu powiązanych jednostek.

> [!NOTE]
> Specyfikacja [języka DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) definiuje również polecenia **,** czyli metody, które mogą być wykonywane w bliźniaczej reprezentacji cyfrowej (na przykład polecenie resetowania lub polecenie umożliwiające włączanie lub wyłączanie wentylatora). Jednak polecenia *nie są obecnie obsługiwane w Azure Digital Twins.*

### <a name="properties-vs-telemetry"></a>Właściwości a telemetria

Oto kilka dodatkowych wskazówek dotyczących rozróżniania właściwości **DTDL** i pól **telemetrii** w Azure Digital Twins.

Różnica między właściwościami i telemetrią dla Azure Digital Twins jest następująca:
* **Oczekuje** się, że właściwości będą mieć magazyn zapasowy. Oznacza to, że można odczytać właściwość w dowolnym momencie i pobrać jej wartość. Jeśli właściwość jest zapisywalna, możesz również zapisać wartość we właściwości .  
* **Telemetria** przypomina bardziej strumień zdarzeń. Jest to zestaw komunikatów danych, które mają krótkie cykle życia. Jeśli nie skonfigurujemy nasłuchiwania zdarzenia i akcji do podjęcia w przypadku jego wystąpienia, w późniejszym czasie nie będzie śladu zdarzenia. Nie możesz wrócić do niego i przeczytać go później. 
  - W języku C# telemetria przypomina zdarzenie języka C#. 
  - W kontekście IoT telemetria jest zwykle pojedynczym pomiarem wysyłanym przez urządzenie.

**Telemetria** jest często używana z urządzeniami IoT, ponieważ wiele urządzeń nie jest w stanie lub nie jest zainteresowanych przechowywaniem generowanych wartości pomiarów. Wysyłają je jako strumień zdarzeń "telemetrii". W takim przypadku nie można w żadnym momencie na urządzeniu uzyskać najnowszej wartości pola telemetrii. Zamiast tego należy nasłuchiwać komunikatów z urządzenia i podjąć akcje w przypadku ich odsłuchiania. 

W związku z tym podczas projektowania modelu w Azure Digital Twins  prawdopodobnie użyjemy właściwości w większości przypadków do modelowania bliźniaczych reprezentacji. Dzięki temu można mieć magazyn zapasowy oraz możliwość odczytywania pól danych i wykonywania dotyczących ich zapytań.

Dane telemetryczne i właściwości często współpracują ze sobą w celu obsługi danych przychodzących z urządzeń. Ponieważ cały ruch przychodzący do usługi Azure Digital Twins odbywa się za pośrednictwem interfejsów [API,](how-to-use-apis-sdks.md)zazwyczaj użyjemy funkcji danych przychodzących do odczytywania danych telemetrycznych lub zdarzeń właściwości z urządzeń i ustawisz właściwość w Azure Digital Twins odpowiedzi. 

Możesz również opublikować zdarzenie telemetrii z interfejsu API Azure Digital Twins API. Podobnie jak w przypadku innych telemetrii, jest to krótkotrwałe zdarzenie, które wymaga obsługi odbiornika.

## <a name="model-inheritance"></a>Dziedziczenie modelu

Czasami może być konieczne dalsze specjalizowanie modelu. Na przykład przydatne może być ogólny model *Room*(Pomieszczenie) oraz wyspecjalizowane warianty *ConferenceRoom (Sal)* i *Jego ().* Aby wyrazić specjalizację, język DTDL obsługuje dziedziczenie: interfejsy mogą dziedziczyć z co najmniej jednego innego interfejsu. 

W poniższym przykładzie model *Planet* (Planet) z wcześniejszego przykładu DTDL jest ponownie wyobrażany jako podtyp większego modelu *Zbody.* Najpierw definiowany jest model "nadrzędny", a następnie model "podrzędny" jest na nim kompilowany przy użyciu pola `extends` .

:::code language="json" source="~/digital-twins-docs-samples/models/CelestialBody-Planet-Crater.json":::

W tym przykładzie *Dobbody* dodaje nazwę, masę i temperaturę *planecie*. Sekcja jest nazwą interfejsu lub tablicą nazw interfejsów (co umożliwia rozszerzanie interfejsu dziedziczenie z wielu modeli `extends` nadrzędnych w razie potrzeby).

Po zastosowaniu dziedziczenia interfejs rozszerzający uwidacznia wszystkie właściwości z całego łańcucha dziedziczenia.

Interfejs rozszerzający nie może zmienić żadnej definicji interfejsów nadrzędnych; Może tylko dodawać do nich. Nie może również ponownie zdefiniować możliwości już zdefiniowanej w żadnym z jej interfejsów nadrzędnych (nawet jeśli możliwości są zdefiniowane jako takie same). Jeśli na przykład interfejs nadrzędny definiuje masę właściwości , interfejs rozszerzający nie może zawierać deklaracji mas, nawet jeśli jest `double` to również   `double` .

## <a name="model-code"></a>Kod modelu

Modele typu bliźniaczej reprezentacji można zapisywać w dowolnym edytorze tekstów. Język DTDL jest zgodny ze składnią JSON, dlatego należy przechowywać modele z rozszerzeniem *.json.* Użycie rozszerzenia JSON umożliwi wielu edytorom tekstów programowania zapewnienie podstawowego sprawdzania składni i wyróżniania dokumentów DTDL. Dostępne jest również rozszerzenie [DTDL dla](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) [Visual Studio Code](https://code.visualstudio.com/).

### <a name="possible-schemas"></a>Możliwe schematy

Zgodnie z dtdl,  schemat  właściwości i telemetrii atrybuty mogą być standardowych typów pierwotnych — , , i — i innych typów, takich `integer` jak i `double` `string` `Boolean` `DateTime` `Duration` . 

Oprócz typów pierwotnych pola *Właściwości* i *Telemetria* mogą mieć następujące typy złożone:
* `Object`
* `Map`
* `Enum`

*Pola* telemetrii obsługują `Array` również .

### <a name="example-model"></a>Przykładowy model

Ta sekcja zawiera przykładowy typowy model napisany jako interfejs DTDL. Model opisuje **planetę**, każdą z nazwą, masą i temperaturą.
 
Należy wziąć pod uwagę, że planeta może również wchodzić w interakcje z **księżycami,** które są ich satelitami i mogą zawierać **księżyce.** W poniższym przykładzie model wyraża połączenia z tymi innymi jednostkami, odwołując się `Planet` do dwóch modeli zewnętrznych — i `Moon` `Crater` . Te modele są również zdefiniowane w poniższym przykładowym kodzie, ale są bardzo proste, aby nie umniejszać tego `Planet` przykładu podstawowego.

:::code language="json" source="~/digital-twins-docs-samples/models/Planet-Crater-Moon.json":::

Pola modelu to:

| Pole | Opis |
| --- | --- |
| `@id` | Identyfikator modelu. Musi mieć format `dtmi:<domain>:<unique model identifier>;<model version number>` . |
| `@type` | Określa rodzaj opisywanych informacji. W przypadku interfejsu typem jest *Interfejs*. |
| `@context` | Ustawia [kontekst](https://niem.github.io/json/reference/json-ld/context/) dokumentu JSON. Modele powinny `dtmi:dtdl:context;2` używać . |
| `displayName` | [opcjonalnie] Umożliwia nadaj modelowi przyjazną nazwę w razie potrzeby. |
| `contents` | Wszystkie pozostałe dane interfejsu są tutaj umieszczane jako tablica definicji atrybutów. Każdy atrybut musi zawierać element ( właściwość , telemetrię, polecenie , relację lub składnik ) w celu zidentyfikowania rodzaju informacji o interfejsie, które opisuje, a następnie zestaw właściwości definiujący rzeczywisty atrybut (na przykład i definiujący właściwość `@type`     `name` `schema` ).  |

> [!NOTE]
> Należy pamiętać, że interfejs składnika *(Crater* w tym przykładzie) jest zdefiniowany w tej samej tablicy co interfejs, który go używa (*Planet*). Składniki muszą być zdefiniowane w ten sposób w wywołaniach interfejsu API, aby można było znaleźć interfejs.

## <a name="best-practices-for-designing-models"></a>Najlepsze rozwiązania dotyczące projektowania modeli

Podczas projektowania modeli w celu odzwierciedlenia jednostek w środowisku przydatne [](concepts-query-language.md) może być spojrzenie w przyszłość i rozważenie implikacji zapytania w projekcie. Właściwości można projektować w taki sposób, aby uniknąć dużych zestawów wyników z przechodzenia grafu. Możesz również chcieć modelować relacje, na które będzie można odpowiedzieć w jednym zapytaniu jako relacje jednopoziomowe.

### <a name="validating-models"></a>Sprawdzania poprawności modeli

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="tools-for-models"></a>Narzędzia dla modeli 

Dostępnych jest kilka przykładów, które jeszcze bardziej ułatwiają radzenie sobie z modelami i onlogiami. Znajdują się one w tym repozytorium: [Tools for Digital Twins Definition Language (DTDL).](https://github.com/Azure/opendigitaltwins-tools)

W tej sekcji bardziej szczegółowo opisano bieżący zestaw przykładów.

### <a name="model-uploader"></a>Model uploader 

_**W przypadku przekazywania modeli do Azure Digital Twins**_

Po zakończeniu tworzenia, rozszerzania lub wybierania modeli można przekazać je do wystąpienia usługi Azure Digital Twins, aby były dostępne do użycia w rozwiązaniu. Odbywa się to przy użyciu [interfejsów API Azure Digital Twins,](how-to-use-apis-sdks.md)zgodnie z opisem w te tematu [*Jak zarządzać modelami DTDL.*](how-to-manage-model.md#upload-models)

Jeśli jednak masz wiele modeli do przekazania lub jeśli mają one wiele współzależności, które sprawiają, że kolejność pojedynczych przekazywania jest skomplikowana, możesz użyć tego przykładu do przekazywania wielu modeli jednocześnie: [**Azure Digital Twins Model Uploader**](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/ModelUploader). Postępuj zgodnie z instrukcjami podanymi w przykładzie, aby skonfigurować ten projekt i używać go do przekazywania modeli do własnego wystąpienia.

### <a name="model-visualizer"></a>Wizualizator modelu 

_**Wizualizowanie modeli**_

Po przesłaniu modeli do wystąpienia usługi Azure Digital Twins można wyświetlić modele w wystąpieniu usługi Azure Digital Twins, w tym wszelkie dziedziczenie i relacje modelu przy użyciu wizualizatora modelu [**Azure Digital Twins.**](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/AdtModelVisualizer) Ten przykład jest obecnie w stanie wersji roboczej. Zachęcamy społeczność programistów usługi Digital Twins do rozszerzania przykładu i współtworzenia go. 

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat tworzenia modeli opartych na standardowych onlogach branżowych: [ *Pojęcia: Co to jest ontologia?*](concepts-ontologies.md)

* Więcej informacji na temat zarządzania modelami za pomocą operacji interfejsu API: [ *Instrukcja: zarządzanie modelami DTDL*](how-to-manage-model.md)

* Dowiedz się, jak modele są używane do tworzenia bliźniaczych reprezentacji [ *cyfrowych: Pojęcia: bliźniacze reprezentacji i graf bliźniaczych reprezentacji*](concepts-twins-graph.md)

