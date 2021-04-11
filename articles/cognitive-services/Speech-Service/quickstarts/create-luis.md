---
title: 'Szybki Start: Tworzenie klucza LUIS'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start dowiesz się, jak utworzyć aplikację LUIS i uzyskać klucz.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 06/25/2020
ms.author: trbye
ms.openlocfilehash: 45c509ea2391c370aa0a399b2f0c621238fabc55
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107209684"
---
# <a name="quickstart-getting-a-luis-endpoint-key"></a>Szybki Start: Pobieranie klucza punktu końcowego LUIS

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka upewnij się, że masz następujące elementy:

* Konto usługi LUIS. Możesz uzyskać je bezpłatnie za pośrednictwem [portalu usługi LUIS](https://www.luis.ai/home).

## <a name="luis-and-speech"></a>Usługa LUIS i mowa

Usługa LUIS integruje się z usługą rozpoznawania mowy, aby rozpoznawać intencje z mowy. Nie potrzebujesz subskrypcji usługi rozpoznawania mowy, tylko usługi LUIS.

LUIS używa trzech rodzajów kluczy:

|Typ klucza|Przeznaczenie|
|--------|-------|
|Tworzenie|Umożliwia programowe tworzenie i modyfikowanie aplikacji LUIS|
|Starter (początkowy)|Umożliwia testowanie aplikacji LUIS przy użyciu tylko tekstu|
|Punkt końcowy |Autoryzuje dostęp do określonej aplikacji LUIS|

W tym samouczku potrzebny jest typ klucza punktu końcowego. W tym samouczku jest używana przykładowa aplikacja LUIS Automation, którą można utworzyć, korzystając z [prebudowanego](../../luis/luis-get-started-create-app.md) przewodnika Szybki Start dla aplikacji do automatyzacji domowej. Jeśli utworzono własną aplikację LUIS, można jej użyć zamiast niej.

Gdy tworzysz aplikację LUIS, LUIS automatycznie generuje klucz początkowy, aby można było przetestować aplikację przy użyciu zapytań tekstowych. Ten klucz nie umożliwia integracji usługi mowy i nie współpracuje z tym samouczkiem. Utwórz zasób LUIS na pulpicie nawigacyjnym platformy Azure i przypisz go do aplikacji LUIS. Na potrzeby tego samouczka możesz używać subskrypcji warstwy Bezpłatna.

Po utworzeniu zasobu LUIS na pulpicie nawigacyjnym platformy Azure Zaloguj się do [portalu Luis](https://www.luis.ai/home), wybierz aplikację na stronie **Moje aplikacje** , a następnie przejdź do strony **zarządzania** aplikacji. Na koniec wybierz pozycję **klucze i punkty końcowe** na pasku bocznym.

![Ustawienia kluczy i punktów końcowych usługi LUIS](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-endpoints-page.png)

Na stronie **Ustawienia kluczy i punktu końcowego** :

1. Przewiń w dół do sekcji **zasoby i klucze** , a następnie wybierz pozycję **Przypisz zasób**.
1. W oknie dialogowym **przypisywanie klucza do aplikacji** wprowadź następujące zmiany:

   * W obszarze **dzierżawca** wybierz pozycję **Microsoft**.
   * W obszarze **Nazwa subskrypcji** wybierz subskrypcję platformy Azure zawierającą zasób Luis, którego chcesz użyć.
   * W obszarze **klucz** wybierz zasób Luis, który ma być używany z aplikacją.

   Za chwilę w tabeli w dolnej części strony zostanie wyświetlona nowa subskrypcja.

1. Wybierz ikonę obok klawisza, aby skopiować go do Schowka. (Możesz użyć dowolnego klucza).

![Klucze subskrypcji aplikacji usługi LUIS](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-assigned.png)


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Rozpoznawanie intencji](~/articles/cognitive-services/Speech-Service/get-started-intent-recognition.md)