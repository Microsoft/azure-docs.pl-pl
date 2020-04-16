---
title: 'Szybki start: tworzenie klucza usługi LUIS'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start dowiesz się, jak utworzyć aplikację usługi LUIS i uzyskać klucz.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: trbye
ms.openlocfilehash: df12b51020083489d431d0ebcd7eb506ef97caa2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400853"
---
# <a name="quickstart-getting-a-luis-endpoint-key"></a>Szybki start: uzyskiwanie klucza końcowego usługi LUIS

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka upewnij się, że masz następujące elementy:

* Konto usługi LUIS. Możesz uzyskać je bezpłatnie za pośrednictwem [portalu usługi LUIS](https://www.luis.ai/home).

## <a name="luis-and-speech"></a>Usługa LUIS i mowa

Usługa LUIS integruje się z usługą rozpoznawania mowy, aby rozpoznawać intencje z mowy. Nie potrzebujesz subskrypcji usługi rozpoznawania mowy, tylko usługi LUIS.

Usługa LUIS używa trzech rodzajów kluczy:

|Typ klucza|Przeznaczenie|
|--------|-------|
|Tworzenie|Umożliwia programowe tworzenie i modyfikowanie aplikacji usługi LUIS|
|Starter (początkowy)|Umożliwia testowanie aplikacji usługi LUIS przy użyciu tylko tekstu|
|Endpoint |Autoryzuje dostęp do określonej aplikacji usługi LUIS|

W tym samouczku potrzebny jest typ klucza punktu końcowego. Samouczek używa przykładowej aplikacji usługi LUIS automatyzacji domowej, którą można utworzyć, wykonując szybki start [aplikacji Użyj wstępnie utworzonej aplikacji automatyki](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app) domowej. Jeśli utworzono własną aplikację usługi LUIS, możesz jej użyć.

Podczas tworzenia aplikacji usługi LUIS usługa LUIS automatycznie generuje klucz startowy, dzięki czemu można przetestować aplikację przy użyciu zapytań tekstowych. Ten klucz nie włącza integracji usługi mowy i nie będzie działać z tym samouczkiem. Utwórz zasób usługi LUIS na pulpicie nawigacyjnym platformy Azure i przypisz go do aplikacji usługi LUIS. Na potrzeby tego samouczka możesz używać subskrypcji warstwy Bezpłatna.

Po utworzeniu zasobu usługi LUIS na pulpicie nawigacyjnym platformy Azure zaloguj się do [portalu usługi LUIS](https://www.luis.ai/home), wybierz aplikację na stronie Moje **aplikacje,** a następnie przełącz się do strony **Zarządzanie aplikacją.** Na koniec wybierz **klawisze i punkty końcowe** na pasku bocznym.

![Ustawienia kluczy i punktów końcowych usługi LUIS](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-endpoints-page.png)

Na stronie **Ustawienia kluczy i punktu końcowego:**

1. Przewiń w dół do sekcji **Zasoby i klucze** i wybierz pozycję **Przypisz zasób**.
1. W oknie **dialogowym Przypisywanie klucza do aplikacji** wykonuj następujące zmiany:

   * W obszarze **Dzierżawa**wybierz pozycję **Microsoft**.
   * W obszarze **Nazwa subskrypcji**wybierz subskrypcję platformy Azure zawierającą zasób usługi LUIS, którego chcesz użyć.
   * W obszarze **Klucz**wybierz zasób usługi LUIS, którego chcesz użyć z aplikacją.

   Za chwilę w tabeli w dolnej części strony zostanie wyświetlona nowa subskrypcja.

1. Wybierz ikonę obok klawisza, aby skopiować ją do schowka. (Możesz użyć dowolnego klucza).

![Klucze subskrypcji aplikacji usługi LUIS](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-assigned.png)


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Rozpoznawanie intencji](~/articles/cognitive-services/Speech-Service/quickstarts/intent-recognition.md)
