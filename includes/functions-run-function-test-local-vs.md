---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.openlocfilehash: b4b2409928b6a4196738c7cc6c7040e781d34686
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80056616"
---
1. Aby uruchomić funkcję, naciśnij klawisz F5 w programie Visual Studio. Może być konieczne włączenie wyjątku zapory, aby narzędzia mogły obsługiwać żądania HTTP. Poziomy autoryzacji nigdy nie są wymuszane w przypadku lokalnego uruchamiania funkcji.

2. Skopiuj adres URL funkcji z danych wyjściowych środowiska uruchomieniowego usługi Azure Functions.

    ![Lokalne środowisko uruchomieniowe platformy Azure](./media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

3. Wklej adres URL żądania HTTP w pasku adresu przeglądarki. Dołącz ciąg `?name=<YOUR_NAME>` zapytania do tego adresu URL i uruchom żądanie. Na poniższej ilustracji przedstawiono odpowiedź w przeglądarce do lokalnego żądania GET zwróconego przez funkcję: 

    ![Odpowiedź hosta localhost funkcji wyświetlona w przeglądarce](./media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

4. Aby zatrzymać debugowanie, naciśnij klawisze Shift + F5 w programie Visual Studio.