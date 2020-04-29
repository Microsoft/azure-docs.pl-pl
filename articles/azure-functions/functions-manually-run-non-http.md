---
title: Ręczne uruchamianie usługi Azure Functions niewyzwalanej przez protokół HTTP
description: Uruchamianie usługi Azure Functions niewyzwalanej przez protokół HTTP za pomocą żądania HTTP
author: craigshoemaker
ms.topic: article
ms.date: 12/12/2018
ms.author: cshoe
ms.openlocfilehash: 6571482d738549d2708fd8ab23eaf8c9f6fb1f70
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80892363"
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

Przejdź do funkcji w witrynie Azure Portal, kliknij pozycję **Zarządzaj** i znajdź sekcję **Klucze hosta**. Kliknij przycisk **Kopiuj** w wierszu *_master*, aby skopiować klucz główny do schowka.

![Kopiowanie klucza głównego z ekranu Zarządzanie funkcjami](./media/functions-manually-run-non-http/azure-portal-functions-master-key.png)

Po skopiowaniu klucza głównego kliknij nazwę funkcji, aby wrócić do okna pliku kodu. Następnie kliknij kartę **dzienniki** . W przypadku ręcznego uruchomienia funkcji z poziomu programu post zostaną wyświetlone komunikaty z funkcji zarejestrowanej w tym miejscu.

> [!CAUTION]  
> Ze względu na podwyższony poziom uprawnień w aplikacji funkcji przyznany przez klucz główny nie należy udostępniać tego klucza osobom trzecim ani rozpowszechniać go w aplikacji.

## <a name="call-the-function"></a>Wywołanie funkcji

Otwórz narzędzie Postman i wykonaj następujące kroki:

1. Wprowadź **lokalizację żądania w polu tekstowym adresu URL**.
2. Upewnij się, że jako metodę HTTP ustawiono wartość **POST**.
3. **Kliknij** kartę **Nagłówki**.
4. Wprowadź **x-functions-key** jako pierwszy **klucz** i wklej klucz główny (ze schowka) w polu **wartości**.
5. Wprowadź **Content-Type** jako drugi **klucz** i wprowadź **application/json** jako **wartość**.

    ![Ustawienia nagłówków narzędzia Postman](./media/functions-manually-run-non-http/functions-manually-run-non-http-headers.png)

6. **Kliknij** kartę **Treść**.
7. Wprowadź **{ "input": "test" }** jako treść żądania.

    ![Ustawienia treści narzędzia Postman](./media/functions-manually-run-non-http/functions-manually-run-non-http-body.png)

8. Kliknij pozycję **Wyślij**.

    ![Wysyłanie żądania za pomocą narzędzia Postman](./media/functions-manually-run-non-http/functions-manually-run-non-http-send.png)

Narzędzie Postman zgłosi stan **202 Zaakceptowano**.

Następnie wróć do swojej funkcji w witrynie Azure Portal. Znajdź okno *Dzienniki*, aby zobaczyć komunikaty pochodzące z ręcznego wywołania funkcji.

![Wyniki dziennika funkcji z ręcznego wywołania](./media/functions-manually-run-non-http/azure-portal-function-log.png)

## <a name="next-steps"></a>Następne kroki

- [Strategie testowania kodu w usłudze Azure Functions](./functions-test-a-function.md)
- [Debugowanie lokalnego wyzwalacza siatki zdarzeń funkcji platformy Azure](./functions-debug-event-grid-trigger-local.md)
