---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: trbye
ms.openlocfilehash: a41c9dc888e85baf021712e3302da7ae6106db47
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "81421772"
---
Aby ukończyć funkcję szybkiego startu rozpoznawania intencji, musisz utworzyć konto LUIS i projekt przy użyciu portalu LUIS w wersji zapoznawczej. Ten przewodnik Szybki Start wymaga tylko subskrypcji LUIS. Subskrypcja usługi mowy *nie jest* wymagana.

Pierwszą czynnością, którą należy wykonać, jest utworzenie konta LUIS i aplikacji za pomocą portalu LUIS w wersji zapoznawczej. Utworzona aplikacja LUIS będzie używać prekompilowanej domeny do automatyzacji domowej, która zapewnia intencje, jednostki i przykład wyrażenia długości. Po zakończeniu będziesz mieć punkt końcowy LUIS uruchomiony w chmurze, który można wywołać przy użyciu zestawu Speech SDK. 

Postępuj zgodnie z poniższymi instrukcjami, aby utworzyć aplikację LUIS:

* <a href="https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app" target="_blank">Szybki Start: Tworzenie wbudowanej aplikacji domeny <span class="docon docon-navigate-external x-hidden-focus"></span></a>

Gdy skończysz, będziesz potrzebować czterech rzeczy:

* Zastąp ponownie Publikowanie przy użyciu narzędzia **Speech napełnianiu**
* **Klucz podstawowy** Luis
* **Lokalizacja** Luis
* **Identyfikator aplikacji** Luis

Oto, gdzie można znaleźć te informacje w [portalu Luis w wersji zapoznawczej](https://preview.luis.ai/):

1. W portalu LUIS w wersji zapoznawczej wybierz aplikację, a następnie wybierz przycisk **Publikuj** .

2. Wybierz miejsce **produkcyjne** , jeśli używasz `en-US` opcji Przełącz pozycję **Speech napełnianiu** do pozycji **wł** . Następnie wybierz przycisk **Publikuj** .

    > [!IMPORTANT]
    > **Napełnianiu mowy** jest zdecydowanie zalecane, ponieważ poprawi dokładność rozpoznawania mowy.

    > [!div class="mx-imgBorder"]
    > ![Publikuj LUIS w punkcie końcowym](../../../media/luis/publish-app-popup.png)

3. W portalu LUIS w wersji zapoznawczej wybierz pozycję **Zarządzaj**, a następnie wybierz pozycję **zasoby platformy Azure**. Na tej stronie znajdziesz klucz LUIS i lokalizację (czasami określany jako _region_).

   > [!div class="mx-imgBorder"]
   > ![Klucz LUIS i lokalizacja](../../../media/luis/luis-key-region.png)

4. Po uzyskaniu klucza i lokalizacji będziesz potrzebować identyfikatora aplikacji. Wybierz pozycję **Ustawienia aplikacji** — na tej stronie jest dostępny identyfikator aplikacji.

   > [!div class="mx-imgBorder"]
   > ![Identyfikator aplikacji LUIS](../../../media/luis/luis-app-id.png)
