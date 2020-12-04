---
title: Używanie Time Series Insights do przechowywania i analizowania danych telemetrycznych dotyczących urządzeń usługi Azure IoT Plug and Play | Microsoft Docs
description: Skonfiguruj środowisko Time Series Insights i Połącz Centrum IoT Hub, aby wyświetlić i przeanalizować dane telemetryczne z urządzeń Plug and Play IoT.
author: lyrana
ms.author: lyhughes
ms.date: 10/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: e1eeb85980430e33446329b5f25ff963ab1c8027
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2020
ms.locfileid: "96600833"
---
# <a name="preview-tutorial-create-and-connect-to-time-series-insights-gen2-to-store-visualize-and-analyze-iot-plug-and-play-device-telemetry"></a>Samouczek dotyczący wersji zapoznawczej: Tworzenie i łączenie Time Series Insights Gen2 w celu przechowywania, wizualizacji i analizowania danych telemetrycznych dotyczących urządzeń Plug and Play IoT

W ramach tego samouczka nauczysz się, jak utworzyć i prawidłowo skonfigurować środowisko [Azure Time Series Insights Gen2](../time-series-insights/overview-what-is-tsi.md) (TSI) do integracji z rozwiązaniem Plug and Play IoT. Za pomocą funkcji TSI Zbieraj, Przetwarzaj, przechowuj, badaj i Wizualizuj dane szeregów czasowych w skali Internet rzeczy (IoT).

Najpierw należy zainicjować środowisko TSI i połączyć centrum IoT Hub ze źródłem zdarzeń przesyłania strumieniowego. Następnie przeprowadzisz przez proces synchronizacji modelu, aby utworzyć [model szeregów czasowych](../time-series-insights/concepts-model-overview.md) na podstawie przykładowych plików modelu [Digital bliźniaczych reprezentacji Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) używanych dla kontrolera temperatury i termostatu.

> [!NOTE]
> Ta integracja jest w wersji zapoznawczej. Sposób mapowania modeli urządzeń DTDL na model szeregów czasowych może ulec zmianie.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

W tym momencie masz:

* Usługa Azure IoT Hub.
* Wystąpienie usługi DPS połączone z Centrum IoT Hub z konkretną rejestracją urządzenia dla urządzenia Plug and Play IoT.
* Połączenie z Centrum IoT Hub z pojedynczego lub wieloskładnikowego urządzenia, przesyłane strumieniowo dane są symulowane.

Aby uniknąć konieczności instalacji interfejsu wiersza polecenia platformy Azure lokalnie, można użyć Azure Cloud Shell, aby skonfigurować usługi w chmurze.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-your-event-source"></a>Przygotowywanie źródła zdarzeń

Utworzone wcześniej Centrum IoT będzie [źródłem zdarzeń](../time-series-insights/concepts-streaming-ingestion-event-sources.md)środowiska TSI.

> [!IMPORTANT]
> Wyłącz wszystkie istniejące trasy IoT Hub. Istnieje znany problem w przypadku używania Centrum IoT jako źródła zdarzeń TSI ze skonfigurowanym [routingiem](../iot-hub/iot-hub-devguide-messages-d2c.md#routing-endpoints) . Tymczasowo wyłącz wszystkie punkty końcowe routingu i gdy Centrum IoT jest połączone z TSI, możesz je ponownie włączyć.

Utwórz unikatową grupę odbiorców w centrum IoT Hub, aby można było użyć TSI. Zamień na `my-pnp-hub` nazwę Centrum IoT używanego wcześniej:

```azurecli-interactive
az iot hub consumer-group create --hub-name my-pnp-hub --name tsi-consumer-group
```

## <a name="choose-your-time-series-id"></a>Wybierz identyfikator szeregów czasowych

W przypadku aprowizacji środowiska TSI należy wybrać *Identyfikator szeregów czasowych*. Ważne jest, aby wybrać odpowiedni identyfikator szeregów czasowych, ponieważ ta właściwość jest niemodyfikowalna i nie można jej zmienić po ustawieniu. Identyfikator szeregów czasowych przypomina klucz partycji bazy danych. Identyfikator szeregów czasowych działa jako klucz podstawowy dla modelu szeregów czasowych. Aby dowiedzieć się więcej, zobacz [najlepsze rozwiązania dotyczące wybierania identyfikatora szeregów czasowych](../time-series-insights/how-to-select-tsid.md).

Jako użytkownik Plug and Play IoT Podaj _klucz złożony_ , który składa się z `iothub-connection-device-id` i `dt-subject` jako identyfikator szeregów czasowych. IoT Hub dodaje te właściwości systemu, które zawierają identyfikator urządzenia IoT Plug and Play i odpowiednio nazwy składników urządzeń.

Nawet jeśli Twoje modele urządzeń IoT Plug and Play nie używają obecnie składników, należy dołączyć `dt-subject` jako część klucza złożonego, aby można było używać ich w przyszłości. Ponieważ identyfikator szeregów czasowych jest niezmienny, firma Microsoft zaleca włączenie tej opcji na wypadek, gdyby była potrzebna w przyszłości.

> [!NOTE]
> Poniższe przykłady są przeznaczone dla wieloskładnikowego urządzenia **TemperatureController** , ale koncepcje są takie same dla urządzenia z **termostatem** No-Component.

## <a name="provision-your-tsi-environment"></a>Inicjowanie obsługi administracyjnej środowiska TSI

W tej sekcji opisano sposób aprowizacji środowiska Azure Time Series Insights Gen2.

Następujące polecenie:

* Tworzy konto usługi Azure Storage dla [chłodnego magazynu](../time-series-insights/concepts-storage.md#cold-store)środowiska, zaprojektowane do długoterminowego przechowywania i analizy danych historycznych.
  * Zamień na `mytsicoldstore` unikatową nazwę konta magazynu zimnego.
* Tworzy Azure Time Series Insights środowisko Gen2, w tym przechowywanie w pamięci masowej z upływem siedmiu dni i przechowywaniem w chłodnym magazynie.
  * Zamień na `my-tsi-env` unikatową nazwę środowiska TSI.
  * Zamień `my-pnp-resourcegroup` na nazwę grupy zasobów, która została użyta podczas konfigurowania.
  * `iothub-connection-device-id, dt-subject` jest właściwością identyfikatora szeregów czasowych.

```azurecli-interactive
storage=mytsicoldstore
rg=my-pnp-resourcegroup
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv)
az timeseriesinsights environment longterm create --name my-tsi-env --resource-group $rg --time-series-id-properties iothub-connection-device-id, dt-subject --sku-name L1 --sku-capacity 1 --data-retention 7 --storage-account-name $storage --storage-management-key $key --location eastus2
```

Połącz źródło zdarzeń IoT Hub. Zastąp `my-pnp-resourcegroup` `my-pnp-hub` wartości, i `my-tsi-env` wartościami, które zostały wybrane. Następujące polecenie odwołuje się do grupy konsumentów dla opisanej wcześniej TSI:

```azurecli-interactive
rg=my-pnp-resourcegroup
iothub=my-pnp-hub
env=my-tsi-env
es_resource_id=$(az iot hub create -g $rg -n $iothub --query id --output tsv)
shared_access_key=$(az iot hub policy list -g $rg --hub-name $iothub --query "[?keyName=='service'].primaryKey" --output tsv)
az timeseriesinsights event-source iothub create -g $rg --environment-name $env -n iot-hub-event-source --consumer-group-name tsi-consumer-group  --key-name iothubowner --shared-access-key $shared_access_key --event-source-resource-id $es_resource_id
```

Przejdź do grupy zasobów w [Azure Portal](https://portal.azure.com) i wybierz nowe środowisko Time Series Insights. Odwiedź *adres URL eksploratora Time Series Insights* przedstawiony w temacie Omówienie wystąpienia:

![Strona omówienia w portalu](./media/tutorial-configure-tsi/view-environment.png)

W Eksploratorze widoczne są trzy wystąpienia:

* &lt;Identyfikator urządzenia PNP &gt; , thermostat1
* &lt;Identyfikator urządzenia PNP &gt; , thermostat2
* &lt;Identyfikator urządzenia PNP &gt; , `null`

> [!NOTE]
> Trzeci tag reprezentuje dane telemetryczne z samego **TemperatureController** , takie jak zestaw roboczy pamięci urządzenia. Ponieważ jest to Właściwość najwyższego poziomu, wartość dla nazwy składnika jest równa null. W późniejszym kroku należy zaktualizować ten element do bardziej przyjaznej nazwy użytkownika.

![Widok Eksploratora 1](./media/tutorial-configure-tsi/tsi-env-first-view.png)

## <a name="configure-model-translation"></a>Konfigurowanie tłumaczenia modelu

Następnie należy przetłumaczyć model urządzenia DTDL na model zasobów w Azure Time Series Insights (TSI). Model szeregów czasowych w TSI to semantyczne narzędzie modelowania do contextualization danych w ramach TSI. Model szeregów czasowych ma trzy podstawowe składniki:

* [Wystąpienia modelu szeregów czasowych](../time-series-insights/concepts-model-overview.md#time-series-model-instances). Wystąpienia są wirtualnymi reprezentacjami szeregów czasowych. Wystąpienia są jednoznacznie identyfikowane przez identyfikator szeregów czasowych.
* [Hierarchie modelu szeregów czasowych](../time-series-insights/concepts-model-overview.md#time-series-model-hierarchies). Hierarchie organizują wystąpienia przez określenie nazw właściwości i ich relacji.
* [Typy modeli szeregów czasowych](../time-series-insights/concepts-model-overview.md#time-series-model-types). Typy pomagają definiować [zmienne](../time-series-insights/concepts-variables.md) lub formuły dla obliczeń. Typy są skojarzone z określonym wystąpieniem.

### <a name="define-your-types"></a>Definiowanie typów

Możesz rozpocząć pozyskiwanie danych do Azure Time Series Insights Gen2 bez wstępnie zdefiniowanego modelu. Po nadejściu telemetrii próbuje automatycznie rozwiązać wystąpienia szeregów czasowych na podstawie wartości właściwości identyfikatora szeregów czasowych. Wszystkie wystąpienia są przypisane do *typu domyślnego*. Należy ręcznie utworzyć nowy typ, aby prawidłowo klasyfikować wystąpienia. Poniższe szczegóły przedstawiają najprostszą metodę synchronizowania modeli DTDL urządzeń z typami modeli szeregów czasowych:

* Identyfikator modelu sieci cyfrowej przędzy jest IDENTYFIKATORem typu.
* Nazwa typu może być nazwą modelu lub nazwą wyświetlaną.
* Opis modelu zmieni się na opis typu.
* Co najmniej jedna zmienna typu jest tworzona dla każdej telemetrii ze schematem liczbowym.
  * Tylko numeryczne typy danych mogą być używane dla zmiennych, ale jeśli wartość jest wysyłana jako inny typ, który można przekonwertować, `"0"` na przykład można użyć funkcji [konwersji](/rest/api/time-series-insights/reference-time-series-expression-syntax#conversion-functions) , takiej jak `toDouble` .
* Zmienna może być nazwą telemetrii lub nazwą wyświetlaną.
* Definiując zmienne wyrażenie szeregów czasowych, zapoznaj się z nazwą telemetrii w sieci, a jej typem danych.

| DTDL JSON | Typ modelu szeregów czasowych JSON | Przykładowa wartość |
|-----------|------------------|-------------|
| `@id` | `id` | `dtmi:com:example:TemperatureController;1` |
| `displayName`    | `name`   |   `Temperature Controller`  |
| `description`  |  `description`  |  `Device with two thermostats and remote reboot.` |  
|`contents` macierzy| `variables` Stream  | Zapoznaj się z poniższym przykładem

![DTDL do typu modelu szeregów czasowych](./media/tutorial-configure-tsi/DTDL-to-TSM-Type.png)

> [!NOTE]
> Ten przykład pokazuje trzy zmienne, ale każdy typ może mieć do 100. Różne zmienne mogą odwoływać się do tej samej wartości telemetrii w celu wykonywania różnych obliczeń stosownie do potrzeb. Aby uzyskać pełną listę filtrów, agregacje i funkcje skalarne, zobacz [Time Series Insights Gen2 wyrażenie szeregów czasowych](/rest/api/time-series-insights/reference-time-series-expression-syntax).

Otwórz Edytor tekstu i Zapisz następujący plik JSON na dysku lokalnym:

```JSON
{
  "put": [
    {
      "id": "dtmi:com:example:TemperatureController;1",
      "name": "Temperature Controller",
      "description": "Device with two thermostats and remote reboot.",
      "variables": {
        "workingSet": {
          "kind": "numeric",
          "value": {
            "tsx": "coalesce($event.workingSet.Long, toLong($event.workingSet.Double))"
          }, 
          "aggregation": {
            "tsx": "avg($value)"
          }
        },
        "temperature": {
          "kind": "numeric",
          "value": {
            "tsx": "coalesce($event.temperature.Long, toLong($event.temperature.Double))"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        },
        "eventCount": {
          "kind": "aggregate",
          "aggregation": {
            "tsx": "count()"
          }
        }
      }
    }
  ]
}
```

W Eksploratorze Time Series Insights przejdź do karty **model** , wybierając ikonę modelu po lewej stronie. Wybierz pozycję **typy** , a następnie wybierz pozycję **Przekaż kod JSON**:

![Przekazywanie](./media/tutorial-configure-tsi/upload-type.png)

Wybierz pozycję **Wybierz plik**, wybierz zapisany wcześniej kod JSON, a następnie wybierz pozycję **Przekaż** .

Zobaczysz nowo zdefiniowany typ **kontrolera temperatury** .

### <a name="create-a-hierarchy"></a>Tworzenie hierarchii

Utwórz hierarchię w celu zorganizowania tagów w ich elemencie nadrzędnym **TemperatureController** . Poniższy prosty przykład ma jeden poziom, ale rozwiązania IoT często mają wiele poziomów zagnieżdżenia do contextualize tagów w obrębie ich fizycznej i semantycznej pozycji w organizacji.

Wybierz **hierarchie** i wybierz pozycję **Dodaj hierarchię**. Wprowadź *flotę urządzenia* jako nazwę i Utwórz jeden poziom o nazwie *Nazwa urządzenia*. Następnie wybierz pozycję **Zapisz**.

![Dodawanie hierarchii](./media/tutorial-configure-tsi/add-hierarchy.png)

### <a name="assign-your-instances-to-the-correct-type"></a>Przypisywanie wystąpień do poprawnego typu

Następnie zmień typ wystąpień i umieść je w hierarchii.

Wybierz kartę **wystąpienia** , Znajdź wystąpienie, które reprezentuje zestaw roboczy urządzenia, a następnie wybierz ikonę **edycji** po prawej stronie:

![Edytuj wystąpienia](./media/tutorial-configure-tsi/edit-instance.png)

Wybierz listę rozwijaną **Typ** i wybierz pozycję **kontroler temperatury**. Wprowadź *defaultComponent, <your device name>* aby zaktualizować nazwę wystąpienia, które reprezentuje wszystkie Tagi najwyższego poziomu skojarzone z urządzeniem.

![Zmień typ wystąpienia](./media/tutorial-configure-tsi/change-type.png)

Przed wybraniem opcji Zapisz wybierz kartę **pola wystąpienia** i zaznacz pole wyboru **flota urządzenia** . Wprowadź `<your device name> - Temp Controller` , aby pogrupować dane telemetryczne razem, a następnie wybierz pozycję **Zapisz**.

![Przypisz do hierarchii](./media/tutorial-configure-tsi/assign-to-hierarchy.png)

Powtórz poprzednie kroki, aby przypisać znaczniki termostatu do poprawnego typu i hierarchii.

## <a name="view-your-data"></a>Wyświetlanie danych

Przejdź z powrotem do okienka wykresy i rozwiń pozycję **flota urządzenia > urządzeniu**. Wybierz pozycję **thermostat1**, wybierz zmienną **temperatury** , a następnie wybierz pozycję Dodaj, aby **dodać** wykres do wartości. Wykonaj te same czynności dla **thermostat2** i **defaultComponent** wartość **zestawu roboczego** .

![Zmień typ wystąpienia dla thermostat2](./media/tutorial-configure-tsi/charting-values.png)

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat różnych opcji wykresów, w tym określania wielkości interwału i kontrolek osi Y, zobacz [Azure Time Series Insights Explorer](../time-series-insights/concepts-ux-panels.md).

* Aby zapoznać się z szczegółowym omówieniem modelu szeregów czasowych w środowisku, zobacz [model szeregów czasowych w Azure Time Series Insights artykule Gen2](../time-series-insights/concepts-model-overview.md) .

* Aby szczegółowe do interfejsów API zapytań i składni wyrażeń szeregów czasowych, zobacz [Azure Time Series Insights Gen2 zapytań API](/rest/api/time-series-insights/reference-query-apis).
