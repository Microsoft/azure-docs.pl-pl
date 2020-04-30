---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 03/04/2019
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: 73d4d4e1a5f148dce6099b2d747ee1c290bcf7c1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67183602"
---
## <a name="run-the-web-application"></a>Uruchamianie aplikacji internetowej

1. Dla Twojej wygody w witrynie GitHub umieszczono przykładową, jednostronicową aplikację internetową. Otwórz przeglądarkę w [https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/](https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/)programie.

    > [!NOTE]
    > Źródło pliku HTML znajduje się w lokalizacji [/docs/demo/Chat-v2/index.html](https://github.com/Azure-Samples/signalr-service-quickstart-serverless-chat/blob/master/docs/demo/chat-v2/index.html).

1. Po wyświetleniu monitu o podstawowy adres URL dla aplikacji funkcji wprowadź adres `http://localhost:7071`.

1. Po wyświetleniu monitu wprowadź nazwę użytkownika.

1. Aplikacja internetowa wywołuje funkcję *GetSignalRInfo* w aplikacji funkcji, aby pobrać informacje o połączeniu w celu nawiązania połączenia z usługą Azure SignalR Service. Po nawiązaniu połączenia zostanie wyświetlone pole wprowadzania komunikatu czatu.

1. Wpisz komunikat, a następnie naciśnij klawisz Enter. Aplikacja wysyła komunikat do funkcji *SendMessage* w aplikacji Azure Function, która następnie używa powiązania danych wyjściowych usługi SignalR, aby rozpowszechnić komunikat do wszystkich połączonych klientów. Jeśli wszystko działa prawidłowo, komunikat powinien pojawić się w aplikacji.

    ![Uruchamianie aplikacji](../media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-run-application.png)

1. Otwórz inne wystąpienie aplikacji internetowej w innym oknie przeglądarki. Zobaczysz, że wszystkie wysłane komunikaty pojawią się we wszystkich wystąpieniach aplikacji.

> [!IMPORTANT]
> Ponieważ strona HTML jest obsługiwana przy użyciu protokołu HTTPS, ale lokalne środowisko uruchomieniowe Azure Functions domyślnie korzysta z protokołu HTTP, przeglądarka (na przykład Firefox) może wymusić zasady zawartości mieszanej, które blokują żądania ze strony sieci Web do funkcji. Aby rozwiązać ten problem, użyj przeglądarki, która nie ma tego ograniczenia lub nie uruchamiaj lokalnego serwera HTTP, takiego jak [http-Server](https://www.npmjs.com/package/http-server) w katalogu */docs/demo/Chat-v2* . Upewnij się, że źródło zostało dodane `CORS` do ustawienia w pliku *Local. Settings. JSON*.