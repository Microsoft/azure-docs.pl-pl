---
title: Publikowanie aplikacji — LUIS
titleSuffix: Azure Cognitive Services
description: Po zakończeniu kompilowania i testowania aktywnej aplikacji LUIS Udostępnij ją aplikacji klienckiej, publikując ją w punkcie końcowym.
services: cognitive-services
author: aahill
manager: nitinme
ms.author: aahi
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 01/12/2021
ms.openlocfilehash: 8e78fc5bd49aaf2b31fdc83ced132e2a39ca83d5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100558892"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>Publikowanie aktywnej, przeszkolonej aplikacji do tymczasowego lub produkcyjnego punktu końcowego

Po zakończeniu kompilowania, uczenia i testowania aktywnej aplikacji LUIS Udostępnij ją aplikacji klienckiej, publikując ją w punkcie końcowym.

## <a name="publishing"></a>Publikowanie
1. Zaloguj się do [portalu Luis](https://www.luis.ai)i wybierz swoją **subskrypcję** i **zasób tworzenia** , aby wyświetlić aplikacje przypisane do tego zasobu.
1. Otwórz aplikację, wybierając jej nazwę na stronie **Moje aplikacje** .
1. Aby opublikować w punkcie końcowym, wybierz pozycję **Publikuj** w prawym górnym panelu.

    ![Przycisk Publikuj na górze, prawy pasek nawigacyjny](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

1. Wybierz ustawienia dla opublikowanego punktu końcowego przewidywania, a następnie wybierz pozycję **Publikuj**.

    ![Wybierz pozycję Publikuj ustawienia, a następnie wybierz przycisk Publikuj.](./media/luis-how-to-publish-app/publish-pop-up.png)

### <a name="publishing-slots"></a>Publikowanie miejsc

Zaznacz odpowiednie miejsce po wyświetleniu okna podręcznego:

* Podział na etapy
* Produkcja

Korzystając z obu miejsc publikowania, można w ten sposób mieć dwie różne wersje aplikacji dostępnych w opublikowanych punktach końcowych lub w tej samej wersji w dwóch różnych punktach końcowych.

### <a name="publishing-regions"></a>Publikowanie regionów

Aplikacja zostanie opublikowana we wszystkich regionach skojarzonych z zasobami punktu końcowego przewidywania Luis dodanych w portalu Luis ze strony **Zarządzanie**  ->  **[zasobami platformy Azure](luis-how-to-azure-subscription.md#assign-a-resource-to-an-app)** .

Na przykład dla aplikacji utworzonej w usłudze [www.Luis.AI](https://www.luis.ai), jeśli UTWORZYSZ zasób Luis w dwóch regionach, **zachodnim** i **wschodnim**, a następnie dodasz je do aplikacji jako zasoby, aplikacja zostanie opublikowana w obu regionach. Aby uzyskać więcej informacji na temat regionów LUIS, zobacz [regiony](luis-reference-regions.md).

> [!TIP]
> Istnieją 3 regiony tworzenia. Należy utworzyć w regionie, w którym ma zostać opublikowana. Jeśli zachodzi potrzeba opublikowania we wszystkich regionach, należy zarządzać procesem tworzenia i modelem, który został przeszkolony, we wszystkich 3 regionach tworzenia.


## <a name="configuring-publish-settings"></a>Konfigurowanie ustawień publikowania

Po wybraniu miejsca Skonfiguruj ustawienia publikowania dla:

* Analiza tonacji
* Napełnianiu mowy

Po opublikowaniu te ustawienia są dostępne do przeglądu na stronie **ustawień publikowania** sekcji **Zarządzanie** . Można zmienić ustawienia za pomocą każdej publikacji. Jeśli anulujesz publikowanie, wszelkie zmiany wprowadzone podczas publikowania również zostaną anulowane.

### <a name="when-your-app-is-published"></a>Po opublikowaniu aplikacji

Po pomyślnym opublikowaniu aplikacji w górnej części przeglądarki pojawia się powiadomienie o powodzeniu. Powiadomienie zawiera również link do punktów końcowych.

Jeśli potrzebujesz adresu URL punktu końcowego, wybierz link. Możesz również przejść do adresów URL punktów końcowych, wybierając pozycję **Zarządzaj** w górnym menu, a następnie wybierając pozycję **zasoby platformy Azure** w menu po lewej stronie.

## <a name="sentiment-analysis"></a>Analiza tonacji

<a name="enable-sentiment-analysis"></a>

Analiza tonacji umożliwia integrację LUIS z [Analiza tekstu](https://azure.microsoft.com/services/cognitive-services/text-analytics/) w celu zapewnienia analizy tonacji i kluczowych fraz.

Nie musisz podawać klucza analiza tekstu i nie ma opłat za rozliczenie dla tej usługi na Twoje konto platformy Azure.

Dane tonacji to wynik z zakresu od 1 do 0 wskazujący dodatnie (bliżej 1) lub ujemne (bliżej 0) tonacji danych. Etykieta tonacji `positive` , `neutral` i `negative` jest na obsługiwaną kulturę. Obecnie tylko język angielski obsługuje etykiety tonacji.

Aby uzyskać więcej informacji na temat odpowiedzi punktu końcowego JSON z analizą tonacji, zobacz [tonacji Analysis](luis-reference-prebuilt-sentiment.md)

## <a name="speech-priming"></a>Napełnianiu mowy

Speech napełnianiu to proces polegający na wysyłaniu modelu LUIS do usługi mowy przed konwersją tekstu na mowę. Dzięki temu usługa mowy może dokładniej zapewnić konwersję mowy dla modelu. Umożliwia to bot LUIS i reagowanie żądań i odpowiedzi w jednym wywołaniu przez jedno wywołanie mowy i przejęcie odpowiedzi na LUIS. Zapewnia ona mniej czasu na ogół.

## <a name="next-steps"></a>Następne kroki

* Zobacz [Zarządzanie](./luis-how-to-azure-subscription.md) kluczami, aby dodać klucze do klucza subskrypcji platformy Azure do Luis i jak ustawić klucz sprawdzanie pisowni Bing i uwzględnić wszystkie intencje w wynikach.
* Aby uzyskać instrukcje dotyczące testowania opublikowanej aplikacji w konsoli testowej, zobacz temat [uczenie i testowanie aplikacji](luis-interactive-test.md) .

