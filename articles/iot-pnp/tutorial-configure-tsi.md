---
title: Używanie Time Series Insights do przechowywania i analizowania danych telemetrycznych urządzeń Plug and Play IoT | Microsoft Docs
description: Skonfiguruj środowisko Time Series Insights i Połącz IoT Hub, aby wyświetlić i przeanalizować dane telemetryczne z urządzeń Plug and Play IoT.
author: lyrana
ms.author: lyhughes
ms.date: 10/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 0d4c24ed771b4d112a448ceec0e9fc2f9e8ac359
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146403"
---
# <a name="tutorial-create-and-connect-to-time-series-insights-gen2-to-store-visualize-and-analyze-iot-plug-and-play-device-telemetry"></a>Samouczek: Tworzenie i łączenie Time Series Insights Gen2 w celu przechowywania, wizualizacji i analizowania danych telemetrycznych dotyczących urządzeń Plug and Play IoT

W tym samouczku dowiesz się, jak utworzyć i prawidłowo skonfigurować środowisko [Azure Time Series Insights Gen2](https://docs.microsoft.com/azure/time-series-insights/overview-what-is-tsi) (TSI) do integracji z rozwiązaniem Plug and Play IoT. Przy użyciu TSI można zbierać, przetwarzać, zapisywać i wizualizować dane szeregów czasowych w skali Internet rzeczy (IoT).

Najpierw należy udostępnić środowisko TSI i połączyć IoT Hub jako źródło zdarzeń przesyłania strumieniowego. Następnie przeprowadzisz przez funkcję synchronizacji modeli, aby utworzyć model szeregów czasowych środowiska TSI na podstawie przykładowych plików modelu [języka bliźniaczych reprezentacji Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) , które zostały użyte dla kontrolera temperatury i urządzeń termostatu.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Aby uniknąć konieczności instalacji interfejsu wiersza polecenia platformy Azure lokalnie, można użyć Azure Cloud Shell, aby skonfigurować usługi w chmurze.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="time-series-id-selection"></a>Wybór identyfikatora szeregów czasowych

Podczas aprowizacji środowiska TSI musisz wybrać identyfikator szeregów czasowych. Wybór odpowiedniego identyfikatora szeregów czasowych ma krytyczne znaczenie, ponieważ właściwość jest niezmienna i nie można jej zmienić po ustawieniu. Wybór identyfikatora szeregów czasowych jest taki sam jak wybór klucza partycji dla bazy danych. Zazwyczaj identyfikator TS powinien być węzłem liścia modelu zasobów. Innymi słowy, zazwyczaj chcesz wybrać Właściwość identyfikatora najbardziej szczegółowego zasobu lub czujnika, który emituje dane telemetryczne.

Jako użytkownik usługi IoT Plug and Play, odpowiednie pytanie dotyczące wyboru identyfikatora TS jest obecność [składników](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#component) w modelach urządzeń. 

![Wybór identyfikatora TS](./media/tutorial-configure-tsi/ts-id-selection-pnp.png)

* Jeśli korzystasz z przewodnika Szybki Start, a urządzenie IoT Hub reprezentuje [termostat](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/Thermostat.json), użyj `iot-hub-connection-device-id` jako identyfikatora TS.

* Jeśli wykonano jeden z samouczków dotyczących [TemperatureController](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/TemperatureController.json)wielu składników, użyj klucza złożonego w poniższej sekcji, który zapisano jako  `iot-hub-connection-device-id, dt-subject`

## <a name="provision-your-azure-time-series-insights-gen2-environment"></a>Inicjowanie obsługi środowiska Azure Time Series Insights Gen2

Poniższe polecenie wykonuje następujące czynności:

* Tworzy konto usługi Azure Storage dla [chłodnego magazynu](https://docs.microsoft.com/azure/time-series-insights/concepts-storage#cold-store)środowiska, zaprojektowane do długoterminowego przechowywania i analizy danych historycznych.
  * Zamień na `mytsicoldstore` unikatową nazwę konta.
* Tworzy Azure Time Series Insights środowisko Gen2, w tym magazyn ciepły z okresem przechowywania wynoszącym 7 dni i chłodny magazyn do nieskończonego przechowywania. 
  * Zamień na `my-tsi-env` unikatową nazwę środowiska TSI 
  * Zamień na `my-pnp-resourcegroup` nazwę grupy zasobów, która została użyta podczas konfigurowania
  * Zamień na `my-ts-id-property` wartość właściwości identyfikatora TS na podstawie podanych kryteriów wyboru

```azurecli-interactive
storage=mytsicoldstore
rg=my-pnp-resourcegroup
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv)
az timeseriesinsights environment longterm create --name my-tsi-env --resource-group $rg --time-series-id-properties my-ts-id-property --sku-name L1 --sku-capacity 1 --data-retention 7 --storage-account-name $storage --storage-management-key $key --location eastus2
```

Teraz skonfigurujesz IoT Hub utworzoną wcześniej jako [Źródło zdarzeń](https://docs.microsoft.com/azure/time-series-insights/concepts-streaming-ingestion-event-sources)środowiska. Po nawiązaniu połączenia ze źródłem zdarzeń TSI rozpocznie indeksowanie zdarzeń z centrum, rozpoczynając od najwcześniejszego zdarzenia w kolejce.

Najpierw utwórz unikatową grupę odbiorców na IoT Hub dla środowiska TSI. Zamień na `my-pnp-hub` nazwę użytego wcześniej IoT Hub.

```azurecli-interactive
az iot hub consumer-group create --hub-name my-pnp-hub --name tsi-consumer-group 
```

Połącz IoT Hub. Zamień `my-pnp-resourcegroup` , `my-pnp-hub` i `my-tsi-env` z odpowiednimi wartościami.

```azurecli-interactive
rg=my-pnp-resourcegroup
iothub=my-pnp-hub
env=my-tsi-env
es_resource_id=$(az iot hub create -g $rg -n $iothub --query id --output tsv)
shared_access_key=$(az iot hub policy list -g $rg --hub-name $iothub --query "[?keyName=='service'].primaryKey" --output tsv)
az timeseriesinsights event-source iothub create -g $rg --environment-name $env -n iot-hub-event-source --consumer-group-name tsi-consumer-group  --key-name iothubowner --shared-access-key $shared_access_key --event-source-resource-id $es_resource_id
```
Przejdź do grupy zasobów w [Azure Portal](https://portal.azure.com) i wybierz nowo utworzone środowisko Time Series Insights. Odwiedź *adres URL eksploratora Time Series Insights* widoczny w przeglądzie wystąpienia.

![Strona omówienia w portalu](./media/tutorial-configure-tsi/view-environment.png)

W Eksploratorze powinny być widoczne dwa termostaty w obszarze "wszystkie hierarchie". Następnie należy zastanowić się nad modelem szeregów czasowych opartym na modelu urządzenia.

![Widok Eksploratora 1](./media/tutorial-configure-tsi/tsi-env-first-view.png)

## <a name="model-synchronization-between-digital-twins-definition-language-and-time-series-insights-gen2"></a>Synchronizacja modeli między językiem definicji Digital bliźniaczych reprezentacji i Time Series Insights Gen2

Następnie Przetłumacz model urządzenia DTDL na model zasobów w Azure Time Series Insights (TSI). Model szeregów czasowych w TSI to semantyczne narzędzie modelowania do contextualization danych w ramach TSI. Model szeregów czasowych ma trzy podstawowe składniki:

* [Wystąpienia modelu szeregów czasowych](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview#time-series-model-instances). Wystąpienia są wirtualnymi reprezentacjami szeregów czasowych. Wystąpienia będą jednoznacznie identyfikowane przez identyfikator TS.
* [Hierarchie modelu szeregów czasowych](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview#time-series-model-hierarchies). Hierarchie organizują wystąpienia przez określenie nazw właściwości i ich relacji.
* [Typy modeli szeregów czasowych](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview#time-series-model-types). Typy pomagają definiować [zmienne](https://docs.microsoft.com/azure/time-series-insights/concepts-variables) lub formuły do wykonywania obliczeń. Typy są skojarzone z określonym wystąpieniem.

> [!NOTE]
> Poniższe przykłady dotyczą elementów wieloskładnikowych TemperatureController.

### <a name="define-your-types"></a>Definiowanie typów

Możesz rozpocząć pozyskiwanie danych do Azure Time Series Insights Gen2 bez wstępnie zdefiniowanego modelu. Po nadejściu danych telemetrycznych funkcja TSI podejmie próbę rozwiązania automatycznie Rozwiązuj wystąpienia szeregów czasowych na podstawie wartości właściwości identyfikator TS. Wszystkie wystąpienia zostaną przypisane do *typu domyślnego* . Należy ręcznie utworzyć nowy typ reprezentujący modele. Na poniższym obrazie przedstawiono prostą metodę synchronizacji modelu DTDL i typu TSM:

![DTDL do typu TSM](./media/tutorial-configure-tsi/DTDL-to-TSM-Type.png)

* Identyfikator modelu (DTMI) cyfrowego przędzy będzie IDENTYFIKATORem typu
* Nazwa typu może być nazwą modelu lub nazwą wyświetlaną
* Opis modelu zmieni się na opis typu
* Co najmniej jedna zmienna typu jest tworzona dla każdego składnika telemetrii, który ma schemat liczbowy. 
  * Tylko numeryczne typy danych mogą być używane dla zmiennych, ale jeśli wartość jest wysyłana jako ciąg, który jest możliwy do przeanalizowania, `"0"` na przykład można użyć funkcji [konwersji](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax#conversion-functions) , takiej jak `toDouble`
* Zmienna może być nazwą telemetrii lub nazwą wyświetlaną
* Podczas definiowania zmiennej "Expression szeregów czasowych (TSX)" Zapoznaj się z nazwą telemetrii w sieci, a jej typem danych.

> [!NOTE]
> W tym przykładzie przedstawiono tylko dwie zmienne — wartość zagregowana i liczbowa, ale każdy typ może mieć do 100. Różne zmienne mogą odwoływać się do tej samej wartości telemetrii, aby w razie potrzeby wykonać różne calucaultions. Aby zapoznać się z pełną listą filtrów, agregacji i funkcji skalarnych, zobacz dokumentację [składni wyrażeń szeregów czasowych w Time Series Insights Gen2](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax) .

Otwórz wybrany edytor tekstu i Zapisz Poniższy kod JSON na dysku lokalnym:

```JSON
{
  "put": [
    {
      "id": "dtmi:com:example:Thermostat;1",
      "name": "Thermostat",
      "description": "Reports current temperature and provides desired temperature control.",
      "variables": {
        "EventCount": {
          "kind": "aggregate",
          "aggregation": {
            "tsx": "count()"
          }
        },
        "Temperature": {
          "kind": "numeric",
          "value": {
            "tsx": "$event.temperature.Double"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        }
      }
    }
  ]
}
```

W Eksploratorze Time Series Insights przejdź do karty model, klikając ikonę modelu po lewej stronie. Kliknij pozycję **typy** i kliknij opcję **Przekaż kod JSON** :

![Przekazywanie](./media/tutorial-configure-tsi/upload-type.png)

Wybierz pozycję **Wybierz plik** , wybierz zapisany wcześniej kod JSON, a następnie kliknij pozycję **Przekaż** .

Powinien zostać wyświetlony nowo zdefiniowany typ termostatu.

### <a name="optional---create-a-hierarchy"></a>Opcjonalne — tworzenie hierarchii

Opcjonalnie można utworzyć hierarchię, aby zorganizować dwa składniki termostatu w ramach ich elementu nadrzędnego TemeraptureController.

Kliknij *hierarchie* i wybierz pozycję *Dodaj hierarchię* . Wprowadź `Device Fleet` jako nazwę i Utwórz jeden poziom o nazwie `Device Name` , a następnie kliknij przycisk *Zapisz* .

![Dodawanie hierarchii](./media/tutorial-configure-tsi/add-hierarchy.png)

### <a name="assign-your-instances-to-the-correct-type"></a>Przypisywanie wystąpień do poprawnego typu

Następnie zmienisz typ wystąpień i opcjonalnie situate je w hierarchii

Wybierz kartę *wystąpienia* i kliknij ikonę *Edytuj* po prawej stronie.

![Edytuj wystąpienia](./media/tutorial-configure-tsi/edit-instance.png)

Kliknij listę rozwijaną typ i wybierz pozycję `Thermostat` . 

![Zmień typ wystąpienia](./media/tutorial-configure-tsi/change-type.png)

Jeśli została utworzona hierarchia, zaznacz *pola wystąpienia* i sprawdź, czy `Device Fleet` pole wyboru. Wprowadź `Temperature Controller` wartość jako jej urządzenie nadrzędne, a następnie kliknij przycisk *Zapisz* .

![Przypisz do hierarchii](./media/tutorial-configure-tsi/assign-to-hierarchy.png)

Powtórz powyższe kroki dla drugiego termostatu.

### <a name="view-your-data"></a>Wyświetlanie danych

Przejdź z powrotem do okienka wykresy i rozwiń pozycję flota urządzenia i TemperatureController. Kliknij pozycję thermostat1, wybierz `Temperature` zmienną, a następnie kliknij pozycję *Dodaj* , aby uzyskać wykres wartości. Wykonaj te same czynności dla thermostat2.

![Zmień typ wystąpienia](./media/tutorial-configure-tsi/charting-values.png)

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat różnych opcji wykresów, w tym określania wielkości interwału i kontrolek osi Y, zobacz dokumentację programu [Azure Time Series Insights Explorer ](https://docs.microsoft.com/azure/time-series-insights/concepts-ux-panels) .

* Aby zapoznać się z szczegółowym omówieniem modelu szeregów czasowych w środowisku, zobacz [ten](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview) artykuł.

* Aby szczegółowe do interfejsów API zapytań i składni wyrażenia szeregów czasowych, [Wybierz opcję](https://docs.microsoft.com/rest/api/time-series-insights/reference-query-apis).




