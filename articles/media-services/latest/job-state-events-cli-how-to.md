---
title: Monitorowanie zdarzeń Azure Media Services za pomocą Event Grid przy użyciu interfejsu wiersza polecenia | Microsoft Docs
description: W tym artykule pokazano, jak subskrybować Event Grid w celu monitorowania zdarzeń Azure Media Services za pomocą interfejsu wiersza polecenia platformy Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: b646965be03b5d3f57483887e256d33262192375
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92013291"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-cli"></a>Tworzenie i monitorowanie zdarzeń Media Services za pomocą Event Grid przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Event Grid to usługa obsługi zdarzeń dla chmury. Ta usługa używa [subskrypcji zdarzeń](../../event-grid/concepts.md#event-subscriptions) do kierowania komunikatów o zdarzeniach do subskrybentów. Zdarzenia Media Services zawierają wszystkie informacje potrzebne do reagowania na zmiany danych. Możesz zidentyfikować zdarzenie Media Services, ponieważ właściwość eventType zaczyna się od "Microsoft. Media.". Aby uzyskać więcej informacji, zobacz [Media Services schematów zdarzeń](media-services-event-schemas.md).

W tym artykule opisano korzystanie z interfejsu wiersza polecenia platformy Azure w celu subskrybowania zdarzeń dla konta Azure Media Services. Następnie Wyzwalasz zdarzenia w celu wyświetlenia wyniku. Zazwyczaj użytkownik wysyła zdarzenia do punktu końcowego, w którym następuje przetwarzanie danych zdarzenia i są wykonywane akcje. Ten artykuł zawiera informacje o wysyłaniu zdarzeń do aplikacji sieci Web, która gromadzi i wyświetla komunikaty.

## <a name="prerequisites"></a>Wymagania wstępne

- Aktywna subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Zainstaluj interfejs wiersza polecenia i korzystaj z niego lokalnie. Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, z jakiej wersji korzystasz. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 

    Obecnie nie wszystkie polecenia [interfejsu wiersza polecenia usługi Media Services w wersji 3](/cli/azure/ams) działają w usłudze Azure Cloud Shell. Zaleca się używanie interfejsu wiersza polecenia lokalnie.

- [Utwórz konto Media Services](./create-account-howto.md).

    Koniecznie zapamiętaj wartości, które zostały użyte jako nazwa grupy zasobów i nazwa konta usługi Media Services.

## <a name="create-a-message-endpoint"></a>Tworzenie punktu końcowego komunikatów

Przed zasubskrybowaniem zdarzeń dla konta Media Services Utwórzmy punkt końcowy dla komunikatu o zdarzeniu. Zazwyczaj w punkcie końcowym akcje są wykonywane na podstawie danych zdarzenia. W tym artykule opisano wdrożenie [wstępnie skompilowanej aplikacji sieci Web](https://github.com/Azure-Samples/azure-event-grid-viewer) , która wyświetla komunikaty o zdarzeniach. Wdrożone rozwiązanie zawiera plan usługi App Service, aplikację internetową usługi App Service i kod źródłowy z repozytorium GitHub.

1. Wybierz pozycję **Wdróż na platformie Azure** w celu wdrożenia rozwiązania w subskrypcji. W witrynie Azure Portal podaj wartości parametrów.

   [![Obraz przedstawiający przycisk "wdróż na platformie Azure".](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json)

1. Wdrożenie może potrwać kilka minut. Po pomyślnym wdrożeniu należy wyświetlić aplikację sieci Web i upewnić się, że jest uruchomiona. W przeglądarce sieci Web przejdź do: `https://<your-site-name>.azurewebsites.net`

Jeśli przełączysz się do witryny "Azure Event Grid Viewer", zobaczysz, że nie ma jeszcze żadnych zdarzeń.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="set-the-azure-subscription"></a>Ustawianie subskrypcji platformy Azure

W poniższym poleceniu podaj identyfikator subskrypcji platformy Azure, który ma być używany dla konta usługi Media Services. Listę subskrypcji, do których masz dostęp, możesz wyświetlić, przechodząc do obszaru [Subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

```azurecli
az account set --subscription mySubscriptionId
```

## <a name="subscribe-to-media-services-events"></a>Subskrybowanie zdarzeń Media Services

Zasubskrybujesz artykuł, aby poinformować Event Grid, które zdarzenia mają być śledzone. Poniższy przykład subskrybuje utworzone konto Media Services i przekazuje adres URL z witryny sieci Web utworzonej jako punkt końcowy dla powiadomienia o zdarzeniu. 

Zamień na `<event_subscription_name>` unikatową nazwę subskrypcji zdarzenia. W przypadku `<resource_group_name>` i `<ams_account_name>` Użyj wartości użytych podczas tworzenia konta Media Services. W polu `<endpoint_URL>` Podaj adres URL aplikacji sieci Web i Dodaj adres `api/updates` URL strony głównej. Określając punkt końcowy podczas subskrybowania, Event Grid obsługuje routing zdarzeń do tego punktu końcowego. 

1. Pobieranie identyfikatora zasobu

    ```azurecli
    amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)
    ```

    Na przykład:

    ```
    amsResourceId=$(az ams account show --name amsaccount --resource-group amsResourceGroup --query id --output tsv)
    ```

2. Subskrybowanie zdarzeń

    ```azurecli
    az eventgrid event-subscription create \
    --source-resource-id $amsResourceId \
    --name <event_subscription_name> \
    --endpoint <endpoint_URL>
    ```

    Na przykład:

    ```
    az eventgrid event-subscription create --source-resource-id $amsResourceId --name amsTestEventSubscription --endpoint https://amstesteventgrid.azurewebsites.net/api/updates/
    ```    

    > [!TIP]
    > Możesz uzyskać ostrzeżenie dotyczące uzgadniania walidacji. Daj to kilka minut, a uzgadnianie powinno być weryfikowane.

Teraz Wyzwólmy zdarzenia, aby zobaczyć, jak Event Grid dystrybuuje komunikat do punktu końcowego.

## <a name="send-an-event-to-your-endpoint"></a>Wysyłanie zdarzenia do punktu końcowego

Zdarzenia dla konta Media Services można wyzwolić, uruchamiając zadanie kodowania. Możesz skorzystać z [tego przewodnika Szybki Start](stream-files-dotnet-quickstart.md) , aby kodować plik i zacząć wysyłać zdarzenia. 

Wyświetl aplikację sieci Web ponownie i zwróć uwagę, że zdarzenie sprawdzania poprawności subskrypcji zostało do niej wysłane. Usługa Event Grid wysyła zdarzenie weryfikacji, aby w punkcie końcowym mogło nastąpić sprawdzenie, czy dane zdarzenia mają być odbierane. Punkt końcowy musi mieć ustawioną wartość `validationResponse` `validationCode` . Aby uzyskać więcej informacji, zobacz [Event Grid zabezpieczenia i uwierzytelnianie](../../event-grid/security-authentication.md). Możesz wyświetlić kod aplikacji sieci Web, aby zobaczyć, w jaki sposób sprawdzać poprawność subskrypcji.

> [!TIP]
> Wybierz ikonę oka, aby rozwinąć dane zdarzenia. Nie odświeżaj strony, jeśli chcesz wyświetlić wszystkie zdarzenia.

![Wyświetlanie zdarzenia subskrypcji](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>Następne kroki

[Przekazywanie, kodowanie i przesyłanie strumieniowe](stream-files-tutorial-with-api.md)