---
title: Monitorowanie zdarzeń Azure Media Services przy użyciu Event Grid z portalem
description: W tym artykule pokazano, jak subskrybować Event Grid w celu monitorowania zdarzeń Azure Media Services.
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: azure media services, stream, broadcast, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 65459dbdf8eaa92525fa20eda158f4beacf58780
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89294377"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-portal"></a>Tworzenie i monitorowanie zdarzeń usługi Media Services za pomocą usługi Event Grid przy użyciu witryny Azure Portal

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Event Grid to usługa obsługi zdarzeń dla chmury. Ta usługa używa [subskrypcji zdarzeń](../../event-grid/concepts.md#event-subscriptions) do kierowania komunikatów o zdarzeniach do subskrybentów. Zdarzenia Media Services zawierają wszystkie informacje potrzebne do reagowania na zmiany danych. Możesz zidentyfikować zdarzenie Media Services, ponieważ właściwość eventType zaczyna się od "Microsoft. Media.". Aby uzyskać więcej informacji, zobacz [Media Services schematów zdarzeń](media-services-event-schemas.md).

W tym artykule zasubskrybujesz zdarzenia dla Azure Media Services konta za pomocą Azure Portal. Następnie Wyzwalasz zdarzenia w celu wyświetlenia wyniku. Zazwyczaj użytkownik wysyła zdarzenia do punktu końcowego, w którym następuje przetwarzanie danych zdarzenia i są wykonywane akcje. W tym artykule wysyłamy zdarzenia do aplikacji sieci Web, która gromadzi i wyświetla komunikaty.

Po zakończeniu przekonasz się, że dane zdarzenia zostały wysłane do aplikacji internetowej.

## <a name="prerequisites"></a>Wymagania wstępne 

* Aktywna subskrypcja platformy Azure.
* Utworzenie nowego konta usługi Azure Media Services zgodnie z opisem w [tym przewodniku Szybki start](./create-account-howto.md).

## <a name="create-a-message-endpoint"></a>Tworzenie punktu końcowego komunikatów

Przed zasubskrybowaniem zdarzeń dla konta Media Services Utwórzmy punkt końcowy dla komunikatu o zdarzeniu. Zazwyczaj w punkcie końcowym akcje są wykonywane na podstawie danych zdarzenia. W tym artykule opisano wdrożenie [wstępnie skompilowanej aplikacji sieci Web](https://github.com/Azure-Samples/azure-event-grid-viewer) , która wyświetla komunikaty o zdarzeniach. Wdrożone rozwiązanie zawiera plan usługi App Service, aplikację internetową usługi App Service i kod źródłowy z repozytorium GitHub.

1. Wybierz pozycję **Wdróż na platformie Azure** w celu wdrożenia rozwiązania w subskrypcji. W witrynie Azure Portal podaj wartości parametrów.

   [![Obraz przedstawiający przycisk "wdróż na platformie Azure".](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json)

1. Wdrożenie może potrwać kilka minut. Po pomyślnym wdrożeniu należy wyświetlić aplikację sieci Web i upewnić się, że jest uruchomiona. W przeglądarce sieci Web przejdź do: `https://<your-site-name>.azurewebsites.net`

Jeśli przełączysz się do witryny "Azure Event Grid Viewer", zobaczysz, że nie ma jeszcze żadnych zdarzeń.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-media-services-events"></a>Subskrybowanie zdarzeń Media Services

Subskrybowanie tematu ma poinformować usługę Event Grid o tym, które zdarzenia chcesz śledzić i gdzie mają być one wysyłane.

1. W portalu wybierz konto Media Services i wybierz pozycję **zdarzenia**.
1. W celu wysłania zdarzeń do aplikacji przeglądarki użyj elementu webhook w punkcie końcowym. 

   ![Wybieranie elementu webhook](./media/monitor-events-portal/select-web-hook.png)

1. Subskrypcja zdarzeń jest wstępnie wypełniona wartościami dla konta Media Services. 
1. Wybierz pozycję "element webhook" dla **typu punktu końcowego**.
1. W tym temacie pozostawiamy zaznaczone pole **subskrybowanie wszystkich typów zdarzeń** . Można jednak usunąć jego zaznaczenie i odfiltrować dla określonych typów zdarzeń. 
1. Kliknij link **Wybierz punkt końcowy** .

    Dla punktu końcowego elementu webhook podaj adres URL aplikacji internetowej i dodaj element `api/updates` do adresu URL strony głównej. 

1. Naciśnij pozycję **Potwierdź wybór**.
1. Kliknij przycisk **Utwórz**.
1. Nadaj swojej subskrypcji nazwę.

   ![Wybieranie dzienników](./media/monitor-events-portal/create-subscription.png)

1. Wyświetl aplikację sieci Web ponownie i zwróć uwagę, że zdarzenie sprawdzania poprawności subskrypcji zostało do niej wysłane. 

    Usługa Event Grid wysyła zdarzenie weryfikacji, aby w punkcie końcowym mogło nastąpić sprawdzenie, czy dane zdarzenia mają być odbierane. Punkt końcowy musi mieć ustawioną wartość `validationResponse` `validationCode` . Aby uzyskać więcej informacji, zobacz [Event Grid zabezpieczenia i uwierzytelnianie](../../event-grid/security-authentication.md). Możesz wyświetlić kod aplikacji sieci Web, aby zobaczyć, w jaki sposób sprawdzać poprawność subskrypcji.

Teraz Wyzwólmy zdarzenia, aby zobaczyć, jak Event Grid dystrybuuje komunikat do punktu końcowego.

## <a name="send-an-event-to-your-endpoint"></a>Wysyłanie zdarzenia do punktu końcowego

Zdarzenia dla konta Media Services można wyzwolić, uruchamiając zadanie kodowania. Możesz skorzystać z [tego przewodnika Szybki Start](stream-files-dotnet-quickstart.md) , aby kodować plik i zacząć wysyłać zdarzenia. Jeśli zasubskrybujesz wszystkie zdarzenia, zobaczysz ekran podobny do poniższego:

> [!TIP]
> Wybierz ikonę oka, aby rozwinąć dane zdarzenia. Nie odświeżaj strony, jeśli chcesz wyświetlić wszystkie zdarzenia.

![Wyświetlanie zdarzenia subskrypcji](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>Następne kroki

[Przekazywanie, kodowanie i przesyłanie strumieniowe](stream-files-tutorial-with-api.md)
