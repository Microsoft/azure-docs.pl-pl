---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/01/2020
ms.author: glenga
ms.openlocfilehash: 55a75651b724a4fe975f655958e36fbd40e35db7
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96748269"
---
## <a name="run-the-function-in-azure"></a>Uruchamianie funkcji na platformie Azure

1. Wróć do obszaru **Azure: Functions** na pasku bocznym, a następnie rozwiń nową aplikację funkcji w ramach subskrypcji. Rozwiń węzeł **funkcje**, kliknij prawym przyciskiem myszy (Windows) lub <kbd>Ctrl-</kbd> kliknij (macOS) w **HttpExample**, a następnie wybierz polecenie **Kopiuj adres URL funkcji**.

    ![Skopiuj adres URL funkcji dla nowego wyzwalacza HTTP](./media/functions-vs-code-run-remote/function-copy-endpoint-url.png)

1. Wklej ten adres URL żądania HTTP na pasku adresu przeglądarki, Dodaj `name` ciąg zapytania na `?name=Functions` końcu tego adresu URL, a następnie wykonaj żądanie. Adres URL, który wywołuje funkcję wyzwalaną przez protokół HTTP, powinien mieć następujący format:

    ```http
    http://<FUNCTION_APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions
    ```

    Poniższy przykład przedstawia odpowiedź w przeglądarce do zdalnego żądania GET zwróconego przez funkcję:

    ![Odpowiedź funkcji wyświetlona w przeglądarce](./media/functions-vs-code-run-remote/functions-test-remote-browser.png)
