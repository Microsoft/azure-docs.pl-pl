---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: trbye
ms.openlocfilehash: a41c9dc888e85baf021712e3302da7ae6106db47
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421772"
---
Aby ukończyć szybki start rozpoznawania intencji, musisz utworzyć konto usługi LUIS i projekt przy użyciu portalu usługi LUIS w wersji zapoznawczej. Ten szybki start wymaga tylko subskrypcji usługi LUIS. Subskrypcja usługi mowy *nie jest* wymagana.

Pierwszą rzeczą, którą musisz zrobić, to utworzyć konto usługi LUIS i aplikację przy użyciu portalu usługi LUIS w wersji zapoznawczej. Aplikacja usługi LUIS, który tworzysz użyje wstępnie utworzonej domeny do automatyzacji domowej, która zapewnia intencje, jednostki i wypowiedzi przykład. Po zakończeniu będziesz mieć punkt końcowy usługi LUIS uruchomiony w chmurze, który można wywołać przy użyciu SDK mowy. 

Postępuj zgodnie z poniższymi instrukcjami, aby utworzyć aplikację usługi LUIS:

* <a href="https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app" target="_blank">Szybki start: tworzenie wstępnie utworzonej aplikacji domeny<span class="docon docon-navigate-external x-hidden-focus"></span></a>

Po zakończeniu potrzebne są cztery rzeczy:

* Ponowne publikowanie z **włączeniem funkcji wysuwanie mowy**
* Klucz **podstawowy** usługi LUIS
* Twoja **lokalizacja** usługi LUIS
* Identyfikator **aplikacji usługi** LUIS

Oto, gdzie można znaleźć te informacje w [portalu wersji zapoznawczej usługi LUIS:](https://preview.luis.ai/)

1. W portalu podglądu usługi LUIS wybierz aplikację, a następnie wybierz przycisk **Publikuj.**

2. Wybierz gniazdo **Produkcyjne,** jeśli `en-US` używasz opcji **przełączania zasysania mowy** do pozycji **Włączone.** Następnie wybierz przycisk **Publikuj.**

    > [!IMPORTANT]
    > **Zasysanie mowy** jest wysoce zalecane, ponieważ poprawi dokładność rozpoznawania mowy.

    > [!div class="mx-imgBorder"]
    > ![Publikowanie usługi LUIS w punkcie końcowym](../../../media/luis/publish-app-popup.png)

3. W portalu usługi LUIS w wersji zapoznawczej wybierz pozycję **Zarządzaj,** a następnie wybierz pozycję **Zasoby platformy Azure**. Na tej stronie znajdziesz klucz usługi LUIS i lokalizację (czasami nazywane _regionem)._

   > [!div class="mx-imgBorder"]
   > ![Klucz i lokalizacja usługi LUIS](../../../media/luis/luis-key-region.png)

4. Po dojście do klucza i lokalizacji potrzebny jest identyfikator aplikacji. Wybierz **ustawienia aplikacji** — identyfikator aplikacji jest dostępny na tej stronie.

   > [!div class="mx-imgBorder"]
   > ![Identyfikator aplikacji usługi LUIS](../../../media/luis/luis-app-id.png)
