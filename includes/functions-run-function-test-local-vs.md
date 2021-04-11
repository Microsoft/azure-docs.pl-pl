---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.openlocfilehash: bff2f05a95faf9c475189cb5a8003cb7fd9f69be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101701410"
---
1. Aby uruchomić funkcję, naciśnij klawisz <kbd>F5</kbd> w programie Visual Studio. Może być konieczne włączenie wyjątku zapory, aby narzędzia mogły obsługiwać żądania HTTP. Poziomy autoryzacji nigdy nie są wymuszane w przypadku lokalnego uruchamiania funkcji.

2. Skopiuj adres URL funkcji z danych wyjściowych środowiska uruchomieniowego usługi Azure Functions.

    ![Lokalne środowisko uruchomieniowe platformy Azure](./media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

3. Wklej adres URL żądania HTTP w pasku adresu przeglądarki. Dołącz ciąg zapytania `?name=<YOUR_NAME>` do tego adresu URL i uruchom żądanie. Na poniższej ilustracji przedstawiono odpowiedź w przeglądarce do lokalnego żądania GET zwróconego przez funkcję: 

    ![Odpowiedź hosta localhost funkcji wyświetlona w przeglądarce](./media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

4. Aby zatrzymać debugowanie, naciśnij klawisz <kbd>SHIFT</kbd> + <kbd>F5</kbd> w programie Visual Studio.
