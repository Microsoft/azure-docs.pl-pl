---
title: Integracja z usługą Azure Maps
titleSuffix: Azure Digital Twins
description: Zapoznaj się z tematem jak używać Azure Functions, aby utworzyć funkcję, która może korzystać z grafu sznurów i powiadomień Digital bliźniaczych reprezentacji na platformie Azure w celu zaktualizowania Azure Maps mapy pomieszczeń.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 1/19/2021
ms.topic: how-to
ms.service: digital-twins
ms.reviewer: baanders
ms.openlocfilehash: 990a0ee73bd91ccb748c948b5fcf0e6124d84a03
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102201434"
---
# <a name="use-azure-digital-twins-to-update-an-azure-maps-indoor-map"></a>Użyj usługi Azure Digital bliźniaczych reprezentacji, aby zaktualizować mapę pomieszczeń Azure Maps

W tym artykule przedstawiono kroki wymagane do korzystania z danych Digital bliźniaczych reprezentacji systemu Azure w celu zaktualizowania informacji wyświetlanych na *mapie pomieszczeń* przy użyciu [Azure Maps](../azure-maps/about-azure-maps.md). Usługa Azure Digital bliźniaczych reprezentacji przechowuje Graf relacji urządzeń IoT i przekierowuje dane telemetryczne do różnych punktów końcowych, co sprawia, że jest to idealne rozwiązanie do aktualizowania nakładów informacyjnych na mapach.

Ta procedura obejmuje następujące zagadnienia:

1. Konfigurowanie wystąpienia usługi Azure Digital bliźniaczych reprezentacji do wysyłania do funkcji w [Azure Functions](../azure-functions/functions-overview.md).
2. Tworzenie funkcji w celu zaktualizowania Azure Maps funkcji map stateset.
3. Jak przechowywać identyfikator map i identyfikator stateset funkcji w grafie Digital bliźniaczych reprezentacji Azure.

### <a name="prerequisites"></a>Wymagania wstępne

* Postępuj zgodnie z samouczkiem Digital bliźniaczych reprezentacji na platformie Azure [*: Połącz kompleksowe rozwiązanie*](./tutorial-end-to-end.md).
    * Będziesz rozszerzać ten dwuosiowy z dodatkowym punktem końcowym i trasą. Możesz również dodać kolejną funkcję do aplikacji funkcji z tego samouczka. 
* Postępuj zgodnie z Azure Maps [*samouczkiem: Użyj kreatora Azure Maps, aby utworzyć mapy wewnętrzne*](../azure-maps/tutorial-creator-indoor-maps.md) , aby utworzyć Azure Maps mapę pomieszczeń z *funkcją stateset*.
    * [Funkcja statesets](../azure-maps/creator-indoor-maps.md#feature-statesets) to kolekcje właściwości dynamicznych (Stanów) przypisanych do funkcji zestawu danych, takich jak pokoje lub sprzęt. W powyższym samouczku Azure Maps funkcja stateset przechowuje stan pokoju, który będzie wyświetlany na mapie.
    * Wymagany jest *Identyfikator stateset* Azure Maps i *klucz subskrypcji* usługi.

### <a name="topology"></a>Topologia

Na poniższym obrazie pokazano, gdzie elementy integracji usługi Maps w tym samouczku mieszczą się w większym, kompleksowym scenariuszu usługi Azure Digital bliźniaczych reprezentacji.

:::image type="content" source="media/how-to-integrate-maps/maps-integration-topology.png" alt-text="Widok usług platformy Azure w kompleksowym scenariuszu, wyróżnianie części integracji z mapami pomieszczeń" lightbox="media/how-to-integrate-maps/maps-integration-topology.png":::

## <a name="create-a-function-to-update-a-map-when-twins-update"></a>Utwórz funkcję, aby zaktualizować mapę podczas aktualizacji bliźniaczych reprezentacji

Najpierw utworzysz trasę w usłudze Azure Digital bliźniaczych reprezentacji, aby przekazywać wszystkie pojedyncze zdarzenia aktualizacji do tematu usługi Event Grid. Następnie użyjesz funkcji, aby odczytać te komunikaty aktualizacji i zaktualizować funkcję stateset w Azure Maps. 

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>Tworzenie trasy i filtrowanie powiadomień o aktualizacjach reprezentacji bliźniaczej

Usługa Azure Digital bliźniaczych reprezentacji Instances może emitować wieloosiowe zdarzenia aktualizacji za każdym razem, gdy stanem splotu jest zaktualizowany. Samouczek Digital bliźniaczych reprezentacji na platformie Azure [*: łączenie kompleksowego rozwiązania*](./tutorial-end-to-end.md) połączonego powyżej instruktaże w scenariuszu, w którym termometr jest używany do aktualizowania atrybutu temperatury dołączonego do sznurka pokoju. To rozwiązanie zostanie rozbudowane, subskrybując powiadomienia o aktualizacjach powiadomień dotyczących usługi bliźniaczych reprezentacji i korzystając z tych informacji w celu zaktualizowania map.

Ten wzorzec odczytuje ze źródła salonu bezpośrednio, a nie urządzenia IoT, co zapewnia elastyczność zmiany bazowego Yródła danych dla temperatury bez konieczności aktualizowania logiki mapowania. Na przykład można dodać wiele termometrów lub ustawić ten pokój, aby udostępnić termometr w innym pokoju, bez konieczności aktualizowania logiki mapy.

1. Utwórz temat usługi Event Grid, który będzie odbierać zdarzenia z wystąpienia bliźniaczych reprezentacji cyfrowych platformy Azure.
    ```azurecli-interactive
    az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
    ```

2. Utwórz punkt końcowy, aby połączyć swój temat z siatką zdarzeń z usługą Azure Digital bliźniaczych reprezentacji.
    ```azurecli-interactive
    az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
    ```

3. Utwórz trasę w usłudze Azure Digital Twins, aby wysyłać zdarzenia aktualizacji reprezentacji bliźniaczej do punktu końcowego.

    >[!NOTE]
    >Obecnie istnieje **znany problem** w usłudze Cloud Shell, który wpływa na następujące grupy poleceń: `az dt route`, `az dt model`, `az dt twin`.
    >
    >Aby rozwiązać ten problem, uruchom polecenie `az login` w usłudze Cloud Shell przed uruchomieniem danego polecenia lub użyj [lokalnego interfejsu wiersza polecenia](/cli/azure/install-azure-cli) zamiast usługi Cloud Shell. Aby uzyskać szczegółowe informacje na ten temat, zobacz [*Rozwiązywanie problemów: znane problemy w usłudze Azure Digital bliźniaczych reprezentacji*](troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell).

    ```azurecli-interactive
    az dt route create -n <your-Azure-Digital-Twins-instance-name> --endpoint-name <Event-Grid-endpoint-name> --route-name <my_route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

## <a name="create-a-function-to-update-maps"></a>Utwórz funkcję do aktualizowania map

Zamierzasz utworzyć **funkcję wyzwalającą Event Grid** w aplikacji funkcji z kompleksowego samouczka ([*Samouczek: łączenie kompleksowego rozwiązania*](./tutorial-end-to-end.md)). Ta funkcja spowoduje rozpakowanie tych powiadomień i wysłanie aktualizacji do Azure Maps funkcji stateset w celu zaktualizowania temperatury jednego pokoju.

Zapoznaj się z następującym dokumentem, aby uzyskać informacje referencyjne: [*wyzwalacz Azure Event Grid dla Azure Functions*](../azure-functions/functions-bindings-event-grid-trigger.md).

Zastąp kod funkcji poniższym kodem. Spowoduje to odfiltrowanie tylko aktualizacji bliźniaczych reprezentacji miejsca, odczytanie zaktualizowanej temperatury i wysłanie tych informacji do Azure Maps.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateMaps.cs":::

Musisz ustawić dwie zmienne środowiskowe w aplikacji funkcji. Jednym z nich jest [Azure Maps podstawowy klucz subskrypcji](../azure-maps/quick-demo-map-app.md#get-the-primary-key-for-your-account), a drugi to [Azure Maps identyfikator stateset](../azure-maps/tutorial-creator-indoor-maps.md#create-a-feature-stateset).

```azurecli-interactive
az functionapp config appsettings set --name <your-App-Service-(function-app)-name> --resource-group <your-resource-group> --settings "subscription-key=<your-Azure-Maps-primary-subscription-key>"
az functionapp config appsettings set --name <your-App-Service-(function-app)-name>  --resource-group <your-resource-group> --settings "statesetID=<your-Azure-Maps-stateset-ID>"
```

### <a name="view-live-updates-on-your-map"></a>Wyświetlanie aktualizacji na żywo na mapie

Aby wyświetlić temperaturę aktualizacji na żywo, wykonaj następujące czynności:

1. Rozpocznij wysyłanie symulowanych danych IoT przez uruchomienie projektu **DeviceSimulator** z samouczka Digital bliźniaczych reprezentacji platformy Azure [*: łączenie kompleksowego rozwiązania*](tutorial-end-to-end.md). Instrukcje dla tego programu znajdują się w sekcji [*Konfigurowanie i uruchamianie symulacji*](././tutorial-end-to-end.md#configure-and-run-the-simulation) .
2. Użyj [ **Azure Mapsowego** modułu,](../azure-maps/how-to-use-indoor-module.md) aby renderować mapy wewnętrzne utworzone w programie Azure Maps Creator.
    1. Skopiuj kod HTML z [*przykładu: Użyj sekcji modułów Maps (moduły*](../azure-maps/how-to-use-indoor-module.md#example-use-the-indoor-maps-module) ) w [*Azure Maps samouczku*](../azure-maps/how-to-use-indoor-module.md) dotyczącej Maps
    1. Zastąp *klucz subskrypcji*, *tilesetId* i *statesetID*  w lokalnym pliku HTML wartościami.
    1. Otwórz ten plik w przeglądarce.

Oba próbki wysyłają temperaturę w zgodnym zakresie, dlatego należy zobaczyć kolor pokoju 121 aktualizacji na mapie co 30 sekund.

:::image type="content" source="media/how-to-integrate-maps/maps-temperature-update.png" alt-text="Mapa biura pokazująca Pokój 121 kolor pomarańczowy":::

## <a name="store-your-maps-information-in-azure-digital-twins"></a>Przechowywanie informacji o mapach w usłudze Azure Digital bliźniaczych reprezentacji

Teraz, gdy masz już rozwiązanie stałe do aktualizowania informacji o mapach, możesz użyć wykresu cyfrowego bliźniaczych reprezentacji Azure, aby przechowywać wszystkie informacje niezbędne do zaktualizowania mapy pomieszczeń. Obejmuje to identyfikator stateset, Identyfikator subskrypcji Maps oraz identyfikator funkcji każdej mapy i lokalizacji. 

Rozwiązanie w tym konkretnym przykładzie wiąże się z aktualizacją każdego miejsca najwyższego poziomu w celu posiadania identyfikatora stateset oraz mapowania identyfikatora subskrypcji i przeszukania każdego pokoju z IDENTYFIKATORem funkcji. Przed zainicjowaniem wykresu dwuosiowego należy ustawić te wartości, a następnie wykonać zapytanie o te wartości dla każdego zdarzenia aktualizacji z przędzą.

W zależności od konfiguracji topologii, będzie można przechowywać te trzy atrybuty na różnych poziomach skorelowanych z szczegółowością mapy.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o zarządzaniu, uaktualnianiu i pobieraniu informacji z grafu bliźniaczych reprezentacji, zobacz następujące odwołania:

* [*Instrukcje: Zarządzanie bliźniaczych reprezentacji cyfrowym*](./how-to-manage-twin.md)
* [*Instrukcje: zapytanie o wykres bliźniaczy*](./how-to-query-graph.md)
