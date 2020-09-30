---
title: Ręczne uruchamianie usługi Azure Functions niewyzwalanej przez protokół HTTP
description: Uruchamianie usługi Azure Functions niewyzwalanej przez protokół HTTP za pomocą żądania HTTP
author: craigshoemaker
ms.topic: article
ms.date: 04/23/2020
ms.author: cshoe
ms.openlocfilehash: 79aebf7ed80fea370ff7a5d5cc40911da4144414
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91537705"
---
# <a name="manually-run-a-non-http-triggered-function"></a>Ręczne uruchamianie funkcji niewyzwalanej przez protokół HTTP

W tym artykule pokazano, jak ręcznie uruchomić funkcję niewyzwalaną przez protokół HTTP za pomocą specjalnie sformatowanego żądania HTTP.

W niektórych kontekstach może być konieczne uruchomienie „na żądanie” funkcji platformy Azure, która jest wyzwalana pośrednio.  Przykłady wyzwalaczy pośrednich to między innymi [funkcje zgodnie z harmonogramem](./functions-create-scheduled-function.md) lub funkcje, które są uruchamiane w wyniku [akcji innego zasobu](./functions-create-storage-blob-triggered-function.md). 

W poniższym przykładzie jest używane narzędzie [Postman](https://www.getpostman.com/), ale możesz używać narzędzia [cURL](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler) lub dowolnego innego narzędzia do wysyłania żądań HTTP.

## <a name="define-the-request-location"></a>Definiowanie lokalizacji żądania

Aby uruchomić funkcję niewyzwalaną przez protokół HTTP, należy wysłać żądanie do platformy Azure w celu uruchomienia funkcji. Adres URL używany do wykonania tego żądania przyjmuje określoną postać.

![Definiowanie lokalizacji żądania: nazwa hosta + ścieżka folderu + nazwa funkcji](./media/functions-manually-run-non-http/azure-functions-admin-url-anatomy.png)

- **Nazwa hosta:** Publiczna lokalizacja aplikacji funkcji, która składa się z nazwy aplikacji funkcji i *azurewebsites.NET* lub domeny niestandardowej.
- **Ścieżka folderu:** Aby uzyskać dostęp do funkcji wyzwalanych przez protokół HTTP za pośrednictwem żądania HTTP, należy wysłać żądanie za pośrednictwem folderu *admin/Functions*.
- **Nazwa funkcji:** Nazwa funkcji, która ma zostać uruchomiona.

Ta lokalizacja żądania w narzędziu Postman wraz z kluczem głównym funkcji w żądaniu do platformy Azure służą do uruchamiania funkcji.

> [!NOTE]
> W przypadku uruchamiania lokalnie klucz główny funkcji nie jest wymagany. [Funkcję można wywołać](#call-the-function) bezpośrednio, pomijając nagłówek `x-functions-key`.

## <a name="get-the-functions-master-key"></a>Uzyskiwanie klucza głównego funkcji

1. Przejdź do aplikacji funkcji w [Azure Portal](https://portal.azure.com), wybierz pozycję **klucze aplikacji**, a następnie `_master` klucz. 

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-functions-master-key.png" alt-text="Zlokalizuj klucz główny, który ma zostać skopiowany." border="true":::

1. W sekcji **Edytuj klucz** skopiuj wartość klucza do schowka, a następnie wybierz przycisk **OK**.

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-functions-master-key-copy.png" alt-text="Zlokalizuj klucz główny, który ma zostać skopiowany." border="true":::

1. Po skopiowaniu klucza *_master* wybierz pozycję **Kod + test**, a następnie wybierz pozycję **dzienniki**. Zostaną wyświetlone komunikaty z funkcji rejestrowane w tym miejscu po ręcznym uruchomieniu funkcji z narzędzia Postman.

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-function-log.png" alt-text="Zlokalizuj klucz główny, który ma zostać skopiowany." border="true":::

> [!CAUTION]  
> Ze względu na podwyższony poziom uprawnień w aplikacji funkcji przyznany przez klucz główny nie należy udostępniać tego klucza osobom trzecim ani rozpowszechniać go w aplikacji. Klucz powinien być wysyłany tylko do punktu końcowego HTTPS.

## <a name="call-the-function"></a>Wywołanie funkcji

Otwórz narzędzie Postman i wykonaj następujące kroki:

1. Wprowadź **lokalizację żądania w polu tekstowym adresu URL**.
1. Upewnij się, że jako metodę HTTP ustawiono wartość **POST**.
1. Wybierz kartę **nagłówki** .
1. Wpisz **x-Functions-Key** jako pierwszy klucz i Wklej klucz główny (ze schowka) jako wartość.
1. Wpisz typ **Content-Type** jako drugi klucz i wpisz wartość w polu **Application/JSON** .

    :::image type="content" source="./media/functions-manually-run-non-http/functions-manually-run-non-http-headers.png" alt-text="Zlokalizuj klucz główny, który ma zostać skopiowany." border="true":::

1. Wybierz kartę **Treść**.
1. Wpisz **{"Input": "test"}** jako treść żądania.

    :::image type="content" source="./media/functions-manually-run-non-http/functions-manually-run-non-http-body.png" alt-text="Zlokalizuj klucz główny, który ma zostać skopiowany." border="true":::

1. Wybierz pozycję **Wyślij**.
        
    :::image type="content" source="./media/functions-manually-run-non-http/functions-manually-run-non-http-send.png" alt-text="Zlokalizuj klucz główny, który ma zostać skopiowany." border="true":::

    Narzędzie Postman zgłosi stan **202 Zaakceptowano**.

1. Następnie wróć do swojej funkcji w witrynie Azure Portal. Przejrzyj dzienniki i zobaczysz komunikaty pochodzące z ręcznego wywołania funkcji.

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-functions-master-key-logs.png" alt-text="Zlokalizuj klucz główny, który ma zostać skopiowany." border="true":::

## <a name="next-steps"></a>Następne kroki

- [Strategie testowania kodu w usłudze Azure Functions](./functions-test-a-function.md)
- [Debugowanie lokalnego wyzwalacza siatki zdarzeń funkcji platformy Azure](./functions-debug-event-grid-trigger-local.md)
