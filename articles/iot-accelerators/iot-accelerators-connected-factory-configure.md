---
title: Konfigurowanie topologii połączonej fabryki — Azure | Microsoft Docs
description: W tym artykule opisano sposób konfigurowania akceleratora rozwiązania połączonej fabryki, w tym jego topologii.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: e015c6761b920ef37af2bbfd67ced5fc3218d532
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002242"
---
# <a name="configure-the-connected-factory-solution-accelerator"></a>Skonfiguruj Akcelerator rozwiązania połączonej fabryki

> [!IMPORTANT]
> Gdy aktualizujemy ten artykuł, zobacz [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) , aby uzyskać najbardziej aktualną zawartość.

Akcelerator rozwiązania połączonej fabryki pokazuje symulowany pulpit nawigacyjny dla fikcyjnej firmy Contoso. Ta firma ma fabryki w wielu globalnych lokalizacjach globalnie.

W tym artykule zawarto informacje dotyczące sposobu konfigurowania topologii rozwiązania połączonej fabryki przy użyciu firmy Contoso.

## <a name="simulated-factories-configuration"></a>Konfiguracja symulowanych fabryk

Każda fabryka firmy Contoso ma linie produkcyjne, które składają się z trzech stacji. Każda stacja jest rzeczywistym serwerem OPC UA z określoną rolą:

* Stacja montażowa
* Stacja testowa
* Stacja pakowania

Te serwery OPC UA mają węzły OPC UA i [OPC Publisher](overview-opc-publisher.md) wysyłają wartości tych węzłów do połączonej fabryki. Możliwości obejmują:

* Bieżący stan operacyjny, na przykład bieżące zużycie mocy.
* Informacje o produkcji, takie jak liczba produkowanych produktów.

Możesz użyć pulpitu nawigacyjnego do przejścia do topologii fabryki firmy Contoso z widoku globalnego w dół do widoku poziomu stacji. Pulpit nawigacyjny połączonej fabryki umożliwia:

* Wizualizacja wyników OEE i KPI dla każdej warstwy w topologii.
* Wizualizacja bieżących wartości węzłów OPC UA na stacjach.
* Agregacja wartości OEE i wskaźnika KPI z poziomu stacji do poziomu globalnego.
* Wizualizacja alertów i akcji do wykonania, jeśli wartości osiągną określone progi.

## <a name="connected-factory-topology"></a>Topologia połączonej fabryki

Topologia fabryk, linii produkcyjnych i stacji jest hierarchiczna:

* Globalny poziom ma węzły fabryki jako elementy podrzędne.
* Fabryki mają węzły linii produkcyjnej jako elementy podrzędne.
* Linie produkcyjne mają węzły stacji jako elementy podrzędne.
* Stacje (serwery OPC UA) mają węzły OPC UA jako elementy podrzędne.

Każdy węzeł w topologii ma wspólny zestaw właściwości, które definiują:

* Unikatowy identyfikator węzła topologii.
* Nazwa.
* Opis.
* Obraz.
* Elementy podrzędne węzła topologii.
* Minimalne, docelowe i maksymalne wartości dla OEE i wskaźników KPI oraz akcje alertów do wykonania.

## <a name="topology-configuration-file"></a>Plik konfiguracji topologii

Aby skonfigurować właściwości wymienione w poprzedniej sekcji, rozwiązanie połączonej fabryki używa pliku konfiguracji o nazwie [ContosoTopologyDescription.json](https://github.com/Azure/azure-iot-connected-factory/blob/master/WebApp/Contoso/Topology/ContosoTopologyDescription.json).

Ten plik można znaleźć w kodzie źródłowym rozwiązania w `WebApp/Contoso/Topology` folderze.

Poniższy fragment kodu przedstawia konspekt `ContosoTopologyDescription.json` pliku konfiguracji:

```json
{
  <global_configuration>,
  "Factories": [
    <factory_configuration>,
    "ProductionLines": [
      <production_line_configuration>,
      "Stations": [
        <station_configuration>,
        <more station_configurations>
      ],
      <more production_line_configurations>
    ]
    <more factory_configurations>
  ]
}
```

Typowe właściwości,, `<global_configuration>` `<factory_configuration>` `<production_line_configuration>` i `<station_configuration>` są:

* **Nazwa** (ciąg typu)

  Definiuje nazwę opisową, która powinna mieć tylko jeden wyraz dla węzła topologii do wyświetlenia na pulpicie nawigacyjnym.

* **Opis** (ciąg typu)

  Opisuje węzeł topologii bardziej szczegółowo.

* **Obraz** (ciąg typu)

  Ścieżka do obrazu w rozwiązaniu WebApp, aby pokazać, kiedy informacje o węźle topologii są wyświetlane na pulpicie nawigacyjnym.

* **OeeOverall**, **OeePerformance**, **OeeAvailability**, **OeeQuality**, **Kpi1**, **Kpi2** (typ `<performance_definition>` )

  Te właściwości definiują minimalne, docelowe i maksymalne wartości na rysunku operacyjnym używanym do generowania alertów. Te właściwości definiują także akcje do wykonania w przypadku wykrycia alertu.

`<factory_configuration>`Elementy i `<production_line_configuration>` mają właściwość:

* **Identyfikator GUID** (ciąg typu)

  Jednoznacznie identyfikuje węzeł topologii.

`<factory_configuration>` ma właściwość:

* **Lokalizacja** (typ `<location_definition>` )

  Określa miejsce, w którym znajduje się fabryka.

`<station_configuration>` ma właściwości:

* **OpcUri** (ciąg typu)

  Ta właściwość musi być ustawiona na identyfikator URI aplikacji OPC UA serwera OPC UA.
  Ponieważ musi być globalnie unikatowa przez specyfikację OPC UA, ta właściwość jest używana do identyfikowania węzła topologii stacji.

* **OpcNodes**, które są tablicą węzłów OPC UA (typ `<opc_node_description>` )

`<location_definition>` ma właściwości:

* **Miasto** (ciąg typu)

  Nazwa miasta znajdującego się najbliżej lokalizacji

* **Country** (ciąg typu)

  Kraj lokalizacji

* **Szerokość geograficzna** (typ Double)

  Szerokość geograficzna lokalizacji

* **Długość geograficzna** (typ Double)

  Długość geograficzna lokalizacji

`<performance_definition>` ma właściwości:

* **Minimum** (typ Double)

  Dolny próg, do którego może dotrzeć wartość. Jeśli bieżąca wartość jest niższa od tego progu, generowany jest alert.

* **Target** (typ Double)

  Idealna wartość docelowa.

* **Maksimum** (typ Double)

  Górny próg, do którego może dotrzeć wartość. Jeśli bieżąca wartość przekracza ten próg, generowany jest alert.

* **MinimumAlertActions** (typ `<alert_action>` )

  Definiuje zestaw akcji, które można wykonać jako odpowiedź na minimalny alert.

* **MaximumAlertActions** (typ `<alert_action>` )

  Definiuje zestaw akcji, które można wykonać jako odpowiedź na maksymalny alert.

`<alert_action`> ma właściwości:

* **Typ** (ciąg typu)

  Typ akcji alertu. Znane są następujące typy:

  * **AcknowledgeAlert**: stan alertu powinien zostać zmieniony na potwierdzony.
  * **CloseAlert**: wszystkie starsze alerty tego samego typu nie powinny być już wyświetlane na pulpicie nawigacyjnym.
  * **CallOpcMethod**: Metoda OPC UA powinna być wywoływana.
  * **OpenWebPage**: należy otworzyć okno przeglądarki zawierające dodatkowe informacje kontekstowe.

* **Opis** (ciąg typu)

  Opis akcji pokazanej na pulpicie nawigacyjnym.

* **Parameter** (ciąg typu)

  Parametry wymagane do wykonania akcji. Wartość zależy od typu akcji.

  * **AcknowledgeAlert**: żaden parametr nie jest wymagany.
  * **CloseAlert**: żaden parametr nie jest wymagany.
  * **CallOpcMethod**: informacje o węźle i parametry metody OPC UA do wywołania w formacie "nodeID węzła nadrzędnego, NodeId metody do wywołania, identyfikator URI serwera OPC UA".
  * **OpenWebPage**: adres URL, który ma być wyświetlany w oknie przeglądarki.

`<opc_node_description>` zawiera informacje o węzłach OPC UA na stacji (OPC UA). Węzły, które nie reprezentują istniejących węzłów OPC UA, ale są używane jako magazyn w logiki obliczeniowej połączonej fabryki, są również prawidłowe. Ma następujące właściwości:

* **NodeId** (ciąg typu)

  Adres węzła OPC UA w przestrzeni adresowej serwera (OPC UA). Składnia musi być określona w specyfikacji OPC UA dla NodeId.

* **Symbolicznname** (ciąg typu)

  Nazwa, która ma być wyświetlana na pulpicie nawigacyjnym, gdy zostanie wyświetlona wartość tego węzła OPC UA.

* **Istotność** (tablica typu String)

  Wskazuje, dla którego obliczenia OEE lub kluczowego wskaźnika wydajności ma zastosowanie wartość węzła OPC UA. Każdy element tablicy może być jedną z następujących wartości:

  * **OeeAvailability_Running**: wartość jest istotna dla obliczenia dostępności OEE.
  * **OeeAvailability_Fault**: wartość jest istotna dla obliczenia dostępności OEE.
  * **OeePerformance_Ideal**: wartość jest istotna dla obliczenia wydajności OEE i jest zwykle wartością stałą.
  * **OeePerformance_Actual**: wartość jest istotna dla obliczenia wydajności OEE.
  * **OeeQuality_Good**: wartość jest istotna dla obliczenia jakości OEE.
  * **OeeQuality_Bad**: wartość jest istotna dla obliczenia jakości OEE.
  * **Kpi1**: wartość jest istotna dla obliczenia elementu Kpi1.
  * **Kpi2**: wartość jest istotna dla obliczenia elementu Kpi2.

* **Opcode** (ciąg typu)

  Wskazuje, w jaki sposób wartość węzła OPC UA jest obsługiwana w zapytaniach usługi Time Series Insights oraz obliczeń OEE/KPI. Każde zapytanie usługi Time Series Insights wskazuje określony przedział czasu, który jest parametrem zapytania i dostarcza wynik. Kod operacji kontroluje sposób obliczania wyniku i może być jedną z następujących wartości:

  * **Diff**: różnica między ostatnią i pierwszą wartością w przedziale czasu.
  * **Średnia**: średnią wszystkich wartości w obiekcie TimeSpan.
  * **Sum**: suma wszystkich wartości w obiekcie TimeSpan.
  * **Ostatni**: obecnie nie jest używany.
  * **Liczba**: liczba wartości w obiekcie TimeSpan.
  * **Max**: maksymalna wartość w obiekcie TimeSpan.
  * **Min**: wartość minimalna w obiekcie TimeSpan.
  * **Const**: wynik jest wartością określoną przez właściwość ConstValue.
  * **SubMaxMin**: różnica między wartością maksymalną i minimalną.
  * **TimeSpan**: TimeSpan.

* **Jednostki** (ciąg typu)

  Definiuje jednostkę wartości do wyświetlenia na pulpicie nawigacyjnym.

* **Visible** (typ Boolean)

  Określa, czy wartość powinna być wyświetlana na pulpicie nawigacyjnym.

* **ConstValue** (typ Double)

  Jeśli **opcode** ma wartość **const**, ta właściwość jest wartością węzła.

* **Minimum** (typ Double)

  Jeśli bieżąca wartość spadnie poniżej tej wartości, zostanie wygenerowany alert minimalny.

* **Maksimum** (typ Double)

  Jeśli bieżąca wartość zwiększa się powyżej tej wartości, generowany jest maksymalny alert.

* **MinimumAlertActions** (typ `<alert_action>` )

  Definiuje zestaw akcji, które można wykonać jako odpowiedź na minimalny alert.

* **MaximumAlertActions** (typ `<alert_action>` )

  Definiuje zestaw akcji, które można wykonać jako odpowiedź na maksymalny alert.

Na poziomie stacji widoczne są również obiekty **symulacji** . Te obiekty są używane tylko do konfigurowania symulacji połączonej fabryki i nie powinny być używane do konfigurowania rzeczywistej topologii.

## <a name="how-the-configuration-data-is-used-at-runtime"></a>Jak dane konfiguracji są używane w czasie wykonywania

Wszystkie właściwości używane w pliku konfiguracji można grupować w różne kategorie w zależności od sposobu ich użycia. Są to następujące kategorie:

### <a name="visual-appearance"></a>Wygląd wizualizacji

Właściwości w tej kategorii definiują wygląd pulpitu nawigacyjnego połączonej fabryki. Przykłady obejmują:

* Nazwa
* Opis
* Obraz
* Lokalizacja
* Lekcji
* Widoczne

### <a name="internal-topology-tree-addressing"></a>Adresowanie drzewa topologii wewnętrznej

WebApp utrzymuje wewnętrzny słownik danych zawierający informacje o wszystkich węzłach topologii. **Identyfikatory GUID** i **OpcUri** są używane jako klucze do uzyskiwania dostępu do tego słownika i muszą być unikatowe.

### <a name="oeekpi-computation"></a>Obliczanie OEE/KPI

Wartości OEE/KPI dla symulacji połączonej fabryki są sparametryzowane przez:

* Wartości węzła OPC UA do uwzględnienia w obliczeniu.
* Sposób obliczania rysunku z wartości telemetrycznych.

Połączona fabryka używa formuł OEE jako opublikowanych przez [http://www.oeefoundation.org](http://www.oeefoundation.org) .

Obiekty węzłów OPC UA na stacjach umożliwiają znakowanie użycia w obliczeniach OEE/KPI. Właściwość **istotność** wskazuje, dla którego rysunku OEE/KPI należy użyć wartości węzła OPC UA. Właściwość **opcode** definiuje, w jaki sposób wartość jest uwzględniona w obliczeniach.

### <a name="alert-handling"></a>Obsługa alertów

Połączona fabryka obsługuje prosty, minimalny/maksymalny mechanizm generowania alertów opartych na progach. Istnieje wiele wstępnie zdefiniowanych akcji, które można skonfigurować w odpowiedzi na te alerty. Następujące właściwości sterują tym mechanizmem:

* Maksimum
* Minimum
* MaximumAlertActions
* MinimumAlertActions

## <a name="correlating-to-telemetry-data"></a>Korelacja z danymi telemetrycznymi

W przypadku niektórych operacji, takich jak wizualizowanie ostatniej wartości lub tworzenie zapytań w usłudze Time Series Insights, WebApp wymaga schematu adresowania dla danych telemetrycznych. Dane telemetryczne wysyłane do połączonej fabryki również muszą być przechowywane w wewnętrznych strukturach danych. Dwie właściwości włączające te operacje są na stacji (OPC UA Server) i na poziomie węzła OPC UA:

* **OpcUri**

  Identyfikuje (globalnie unikatowy) serwer OPC UA, z którego pochodzą dane telemetryczne. W wiadomościach pozyskiwanych ta właściwość jest wysyłana jako **ApplicationUri**.

* **NodeId**

  Identyfikuje wartość węzła na serwerze OPC UA. Format właściwości musi być określony w specyfikacji OPC UA. W wiadomościach pozyskiwanych ta właściwość jest wysyłana jako **NodeId**.

Zobacz, [co to jest Wydawca OPC](overview-opc-publisher.md) , aby uzyskać więcej informacji o sposobie pozyskiwania danych telemetrycznych do połączonej fabryki.

## <a name="example-how-kpi1-is-calculated"></a>Przykład: jak obliczana jest KPI1

Konfiguracja w `ContosoTopologyDescription.json` pliku kontroluje sposób obliczania wartości OEE/KPI. Poniższy przykład pokazuje, jak właściwości w tym pliku kontrolują obliczenia KPI1.

W połączonej fabryce KPI1 służy do mierzenia liczby pomyślnie wyprodukowanych produktów w ciągu ostatniej godziny. Każda stacja (OPC UA Server) w symulacji połączonej fabryki udostępnia węzeł OPC UA ( `NodeId: "ns=2;i=385"` ), który udostępnia dane telemetryczne do obliczenia tego wskaźnika KPI.

Konfiguracja tego węzła OPC UA wygląda podobnie do następującego fragmentu kodu:

```json
{
  "NodeId": "ns=2;i=385",
  "SymbolicName": "NumberOfManufacturedProducts",
  "Relevance": [ "Kpi1", "OeeQuality_Good" ],
  "OpCode": "SubMaxMin"
},
```

Ta konfiguracja umożliwia wykonywanie zapytań dotyczących wartości telemetrycznych tego węzła przy użyciu Time Series Insights. Zapytanie Time Series Insights pobiera:

* Liczba wartości.
* Wartość minimalna.
* Wartość maksymalna.
* Średnia wszystkich wartości.
* Suma wszystkich wartości dla wszystkich unikatowych **OpcUri** (**ApplicationUri**), par **NodeId** w danym przedziałze czasu.

Jedną cechą wartości węzła **NumberOfManufactureredProducts** jest to, że tylko zwiększa. Aby obliczyć liczbę produktów wyprodukowanych w TimeSpan, połączona fabryka używa SubMaxMin **kodu operacji** **SubMaxMin**. Obliczenie Pobiera wartość minimalną na początku przedziału czasu i wartość maksymalną na końcu przedziału czasu.

**Kod operacji** w konfiguracji konfiguruje logikę obliczeń, aby obliczyć wynik różnicy wartości maksymalnej i minimalnej. Te wyniki są następnie sumowane na poziomie głównym (globalnym) i wyświetlane na pulpicie nawigacyjnym.

## <a name="next-steps"></a>Następne kroki

Sugerowany następny krok to Dowiedz się, jak [dostosować rozwiązanie połączonej fabryki](iot-accelerators-connected-factory-customize.md).
