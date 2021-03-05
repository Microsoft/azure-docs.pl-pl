---
title: Samouczek — używanie Azure Time Series Insights do przechowywania i analizowania danych telemetrycznych dotyczących urządzeń usługi Azure IoT Plug and Play
description: Samouczek — Konfigurowanie środowiska Time Series Insights i łączenie Centrum IoT Hub w celu wyświetlania i analizowania danych telemetrycznych z urządzeń Plug and Play IoT.
author: lyrana
ms.author: lyhughes
ms.date: 10/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 55fa10cce038c83f0758a9537a916e2dca7e13f9
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181690"
---
# <a name="tutorial-create-and-configure-a-time-series-insights-gen2-environment"></a>Samouczek: Tworzenie i Konfigurowanie środowiska Time Series Insights Gen2

W ramach tego samouczka dowiesz się, jak utworzyć i skonfigurować środowisko [Azure Time Series Insights Gen2](../time-series-insights/overview-what-is-tsi.md) w celu integracji z rozwiązaniem Plug and Play IoT. Za pomocą Time Series Insights Zbieraj, Przetwarzaj, przechowuj, badaj i Wizualizuj dane szeregów czasowych w skali Internet rzeczy (IoT).

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Zainicjuj obsługę środowiska Time Series Insights i Połącz Centrum IoT Hub ze źródłem zdarzeń przesyłania strumieniowego.
> * Pracuj za poorednictwem synchronizacji modelu, aby utworzyć swój [model szeregów czasowych](../time-series-insights/concepts-model-overview.md).
> * Użyj przykładowych plików modelu [Digital bliźniaczych reprezentacji Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) , które są używane przez kontroler temperatury i urządzenia termostatu.

> [!NOTE]
> Ta integracja między Time Series Insights i IoT Plug and Play jest w wersji zapoznawczej. Sposób, w jaki modele urządzeń DTDL mapowanie na Time Series Insights model szeregów czasowych może ulec zmianie. 

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

W tym momencie masz:

* Usługa Azure IoT Hub.
* Wystąpienie usługi Device Provisioning (DPS) połączone z Centrum IoT Hub. Wystąpienie usługi DPS powinno mieć rejestrację indywidualną urządzenia dla urządzenia Plug and Play IoT.
* Połączenie z Centrum IoT Hub z jednego urządzenia lub urządzenia z wieloma składnikami, które przesyła strumieniowo symulowane dane.

Aby uniknąć konieczności instalacji interfejsu wiersza polecenia platformy Azure lokalnie, można użyć Azure Cloud Shell, aby skonfigurować usługi w chmurze.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-your-event-source"></a>Przygotowywanie źródła zdarzeń

Utworzone wcześniej Centrum IoT będzie [źródłem zdarzeń](../time-series-insights/concepts-streaming-ingestion-event-sources.md)środowiska Time Series Insights.

> [!IMPORTANT]
> Wyłącz wszystkie istniejące trasy IoT Hub. Istnieje znany problem związany z używaniem usługi IoT Hub z skonfigurowanym [routingiem](../iot-hub/iot-hub-devguide-messages-d2c.md#routing-endpoints) . Tymczasowo wyłącz wszystkie punkty końcowe routingu. Gdy Centrum IoT jest połączone z Time Series Insights, można ponownie włączyć routing punktów końcowych.

Na potrzeby usługi IoT Hub utwórz unikatową grupę odbiorców do użycia przez Time Series Insights. W poniższym przykładzie Zastąp `my-pnp-hub` wartość nazwą Centrum IoT używanego wcześniej.

```azurecli-interactive
az iot hub consumer-group create --hub-name my-pnp-hub --name tsi-consumer-group
```

## <a name="choose-a-time-series-id"></a>Wybieranie identyfikatora szeregów czasowych

Po zainicjowaniu obsługi środowiska Time Series Insights należy wybrać *Identyfikator szeregów czasowych*. Ważne jest, aby wybrać odpowiedni identyfikator szeregów czasowych. Ta właściwość jest niezmienna i nie można jej zmienić po jej ustawieniu. Identyfikator szeregów czasowych przypomina klucz partycji bazy danych. Identyfikator szeregów czasowych działa jako klucz podstawowy dla modelu szeregów czasowych. Aby uzyskać więcej informacji, zobacz [najlepsze rozwiązania dotyczące wybierania identyfikatora szeregów czasowych](../time-series-insights/how-to-select-tsid.md).

Jako użytkownik usługi IoT Plug and Play dla identyfikatora szeregów czasowych Określ _klucz złożony_ składający się z `iothub-connection-device-id` i `dt-subject` . Centrum IoT Hub dodaje te właściwości systemu, które zawierają odpowiednio identyfikator urządzenia Plug and Play IoT oraz nazwy składników urządzeń.

Nawet jeśli Twoje modele urządzeń Plug and Play IoT nie używają obecnie składników, należy dołączyć `dt-subject` jako część klucza złożonego, aby można było użyć składników w przyszłości. Ponieważ identyfikator szeregów czasowych jest niezmienny, firma Microsoft zaleca włączenie tej opcji na wypadek, gdyby była potrzebna w przyszłości.

> [!NOTE]
> Przykłady w tym artykule dotyczą urządzeń z wieloma składnikami `TemperatureController` . Jednak pojęcia są takie same dla urządzenia No-Component `Thermostat` .

## <a name="provision-your-time-series-insights-environment"></a>Inicjowanie obsługi administracyjnej środowiska Time Series Insights

W tej sekcji opisano sposób aprowizacji środowiska Azure Time Series Insights Gen2.

Uruchom następujące polecenie, aby:

* Utwórz konto usługi Azure Storage dla [chłodnego magazynu](../time-series-insights/concepts-storage.md#cold-store)środowiska. To konto jest przeznaczone do długoterminowego przechowywania i analizy danych historycznych.
  * W kodzie Zastąp `mytsicoldstore` wartość unikatową nazwą konta magazynu zimnego.
* Utwórz środowisko Azure Time Series Insights Gen2. Środowisko zostanie utworzone z rozgrzanym magazynem, który ma okres przechowywania wynoszący siedem dni. Konto magazynu zimnego zostanie dołączone do nieskończonego przechowywania.
  * W kodzie Zastąp `my-tsi-env` wartość unikatową nazwą środowiska Time Series Insights.
  * W kodzie Zastąp `my-pnp-resourcegroup` wartość nazwą grupy zasobów, która została użyta podczas instalacji.
  * Właściwość identyfikatora szeregów czasowych to `iothub-connection-device-id, dt-subject` .

```azurecli-interactive
storage=mytsicoldstore
rg=my-pnp-resourcegroup
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv)
az tsi environment gen2 create --name "my-tsi-env" --location eastus2 --resource-group $rg --sku name="L1" capacity=1 --time-series-id-properties name=iothub-connection-device-id type=String --time-series-id-properties name=dt-subject type=String --warm-store-configuration data-retention=P7D --storage-configuration account-name=$storage management-key=$key
```

Połącz źródło zdarzeń IoT Hub. Zastąp `my-pnp-resourcegroup` `my-pnp-hub` wartości, i `my-tsi-env` wartościami, które zostały wybrane. Następujące polecenie odwołuje się do grupy konsumentów dla Time Series Insights utworzonej wcześniej:

```azurecli-interactive
rg=my-pnp-resourcegroup
iothub=my-pnp-hub
env=my-tsi-env
es_resource_id=$(az iot hub create -g $rg -n $iothub --query id --output tsv)
shared_access_key=$(az iot hub policy list -g $rg --hub-name $iothub --query "[?keyName=='service'].primaryKey" --output tsv)
az tsi event-source iothub create --event-source-name iot-hub-event-source --environment-name $env --resource-group $rg --location eastus2 --consumer-group-name tsi-consumer-group --key-name iothubowner --shared-access-key $shared_access_key --event-source-resource-id $es_resource_id --iot-hub-name $iothub
```

W [Azure Portal](https://portal.azure.com)przejdź do grupy zasobów, a następnie wybierz nowe środowisko Time Series Insights. Przejdź do **adresu URL eksploratora Time Series Insights** wyświetlonego w temacie Omówienie wystąpienia:

![Zrzut ekranu przedstawiający stronę przegląd portalu.](./media/tutorial-configure-tsi/view-environment.png)

W Eksploratorze widoczne są trzy wystąpienia:

* &lt;Identyfikator urządzenia PNP &gt; , thermostat1
* &lt;Identyfikator urządzenia PNP &gt; , thermostat2
* &lt;Identyfikator urządzenia PNP &gt; , `null`

> [!NOTE]
> Trzeci tag reprezentuje dane telemetryczne `TemperatureController` , takie jak zestaw roboczy pamięci urządzenia. Ponieważ jest to Właściwość najwyższego poziomu, wartość dla nazwy składnika jest równa null. W późniejszym kroku należy wprowadzić tę nazwę więcej przyjaznych dla użytkownika.

![Zrzut ekranu przedstawiający trzy wystąpienia w Eksploratorze.](./media/tutorial-configure-tsi/tsi-env-first-view.png)

## <a name="configure-model-translation"></a>Konfigurowanie tłumaczenia modelu

Następnie należy przetłumaczyć model urządzenia DTDL na model zasobów w Azure Time Series Insights. W Time Series Insights model szeregów czasowych to semantyczne narzędzie modelowania dla contextualization danych. Model ma trzy podstawowe składniki:

* [Wystąpienia modelu szeregów czasowych](../time-series-insights/concepts-model-overview.md#time-series-model-instances) są wirtualnymi reprezentacjami szeregów czasowych. Wystąpienia są jednoznacznie identyfikowane przez identyfikator szeregów czasowych.
* [Hierarchie modelu szeregów czasowych](../time-series-insights/concepts-model-overview.md#time-series-model-hierarchies) organizują wystąpienia, określając nazwy właściwości i ich relacje.
* [Typy modeli szeregów czasowych](../time-series-insights/concepts-model-overview.md#time-series-model-types) pomagają definiować [zmienne](../time-series-insights/concepts-variables.md) lub formuły dla obliczeń. Typy są skojarzone z określonym wystąpieniem.

### <a name="define-your-types"></a>Definiowanie typów

Możesz rozpocząć pozyskiwanie danych do Azure Time Series Insights Gen2 bez wstępnie zdefiniowanego modelu. Po nadejściu telemetrii Time Series Insights próbuje automatycznie rozwiązać wystąpienia szeregów czasowych na podstawie wartości właściwości identyfikatora szeregów czasowych. Wszystkie wystąpienia są przypisane do *typu domyślnego*. Należy ręcznie utworzyć nowy typ, aby prawidłowo klasyfikować wystąpienia. 

Poniższe szczegóły przedstawiają najprostszą metodę synchronizowania modeli DTDL urządzeń z typami modeli szeregów czasowych:

* Identyfikator modelu sieci cyfrowej przędzy jest IDENTYFIKATORem typu.
* Nazwa typu może być nazwą modelu lub nazwą wyświetlaną.
* Opis modelu zmieni się na opis typu.
* Co najmniej jedna zmienna typu jest tworzona dla każdej telemetrii, która ma schemat liczbowy.
  * Tylko numeryczne typy danych mogą być używane dla zmiennych, ale jeśli wartość jest wysyłana jako inny typ, który można przekonwertować, `"0"` na przykład można użyć funkcji [konwersji](/rest/api/time-series-insights/reference-time-series-expression-syntax#conversion-functions) , takiej jak `toDouble` .
* Zmienna może być nazwą telemetrii lub nazwą wyświetlaną.
* Podczas definiowania zmiennej wyrażenia szeregów czasowych zapoznaj się z nazwą telemetrii w sieci oraz z typem danych telemetrii.

| DTDL JSON | Typ modelu szeregów czasowych JSON | Przykładowa wartość |
|-----------|------------------|-------------|
| `@id` | `id` | `dtmi:com:example:TemperatureController;1` |
| `displayName`    | `name`   |   `Temperature Controller`  |
| `description`  |  `description`  |  `Device with two thermostats and remote reboot.` |  
|`contents` macierzy| `variables` Stream  | Zobacz poniższy przykład.

![Zrzut ekranu przedstawiający Typ modelu D T d dla szeregów czasowych.](./media/tutorial-configure-tsi/DTDL-to-TSM-Type.png)

> [!NOTE]
> Ten przykład pokazuje trzy zmienne, ale każdy typ może mieć do 100 zmiennych. Różne zmienne mogą odwoływać się do tej samej wartości telemetrii w celu wykonywania różnych obliczeń stosownie do potrzeb. Aby uzyskać pełną listę filtrów, agregacje i funkcje skalarne, zobacz [Time Series Insights składnia wyrażeń szeregów czasowych Gen2](/rest/api/time-series-insights/reference-time-series-expression-syntax).

Otwórz Edytor tekstu i Zapisz następujący plik JSON na dysku lokalnym.

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

W Eksploratorze Time Series Insights wybierz ikonę modelu po lewej stronie, aby otworzyć kartę **model** . Wybierz pozycję **typy** , a następnie wybierz pozycję **Przekaż kod JSON**:

![Zrzut ekranu przedstawiający sposób przekazywania pliku JSON.](./media/tutorial-configure-tsi/upload-type.png)

Wybierz pozycję **Wybierz plik**, wybierz zapisany wcześniej kod JSON, a następnie wybierz pozycję **Przekaż**.

Zobaczysz nowo zdefiniowany typ **kontrolera temperatury** .

### <a name="create-a-hierarchy"></a>Tworzenie hierarchii

Utwórz hierarchię w celu zorganizowania tagów w obszarze `TemperatureController` nadrzędnym. Poniższy prosty przykład ma jeden poziom, ale rozwiązania IoT często mają wiele poziomów zagnieżdżenia do contextualize tagów w obrębie ich fizycznej i semantycznej pozycji w organizacji.

Wybierz **hierarchie** , a następnie wybierz pozycję **Dodaj hierarchię**. W polu Nazwa wprowadź wartość *flota urządzenia*. Utwórz jeden poziom o nazwie *urządzenia*. Następnie wybierz pozycję **Zapisz**.

![Zrzut ekranu przedstawiający sposób dodawania hierarchii.](./media/tutorial-configure-tsi/add-hierarchy.png)

### <a name="assign-your-instances-to-the-correct-type"></a>Przypisywanie wystąpień do poprawnego typu

Następnie zmień typ wystąpień i umieść je w hierarchii.

Wybierz kartę **wystąpienia** . Znajdź wystąpienie, które reprezentuje zestaw roboczy urządzenia, a następnie wybierz ikonę **Edytuj** po prawej stronie.

![Zrzut ekranu przedstawiający sposób edytowania wystąpienia.](./media/tutorial-configure-tsi/edit-instance.png)

Otwórz menu rozwijane **Typ** , a następnie wybierz pozycję **kontroler temperatury**. Wprowadź *defaultComponent, <your device name>* aby zaktualizować nazwę wystąpienia, które reprezentuje wszystkie Tagi najwyższego poziomu skojarzone z urządzeniem.

![Zrzut ekranu przedstawiający sposób zmiany typu wystąpienia.](./media/tutorial-configure-tsi/change-type.png)

Przed wybraniem opcji **Zapisz**, najpierw wybierz kartę **pola wystąpienia** , a następnie wybierz pozycję **flota urządzenia**. Aby pogrupować dane telemetryczne, wprowadź *\<your device name> -temp Controller*. Następnie wybierz pozycję **Zapisz**.

![Zrzut ekranu przedstawiający sposób przypisywania wystąpienia do hierarchii](./media/tutorial-configure-tsi/assign-to-hierarchy.png)

Powtórz poprzednie kroki, aby przypisać znaczniki termostatu do poprawnego typu i hierarchii.

## <a name="view-your-data"></a>Wyświetlanie danych

Wróć do okienka wykresy i rozwiń pozycję **flota urządzeń** > urządzeniu. Wybierz pozycję **thermostat1**, wybierz zmienną **temperatury** , a następnie wybierz pozycję Dodaj, aby **dodać** wykres do wartości. Wykonaj te same czynności dla **thermostat2** i **defaultComponent** wartość **zestawu roboczego** .

![Zrzut ekranu przedstawiający sposób zmiany typu wystąpienia dla thermostat2.](./media/tutorial-configure-tsi/charting-values.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [iot-pnp-clean-resources](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> Aby dowiedzieć się więcej na temat różnych opcji wykresów, w tym określania wielkości interwału i kontrolek osi y, zobacz [Azure Time Series Insights Explorer](../time-series-insights/concepts-ux-panels.md).
