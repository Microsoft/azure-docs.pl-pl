---
title: Precyzyjne odpowiadanie przy użyciu wykrywania zakresu odpowiedzi — QnA Maker
description: Poznaj precyzyjną funkcję odpowiedzi dostępną w QnA Maker zarządzane.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: 4f64bab698cb87e26fa4fd1587c4269acf99fa59
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2020
ms.locfileid: "94384108"
---
# <a name="precise-answering"></a>Precyzyjne odpowiadanie

Funkcja precyzyjnej odpowiedzi umożliwia uzyskanie dokładnej krótkiej odpowiedzi z najlepszej sugestii odpowiedzi w bazie wiedzy dla dowolnej kwerendy użytkownika. Ta funkcja używa modelu uczenia głębokiego, który znajduje się w środowisku uruchomieniowym, które rozumie zamierzenie zapytania użytkownika i wykrywa precyzyjne krótkie odpowiedzi z odniesień odpowiedzi, jeśli w odpowiedzi na odpowiedź występuje krótka odpowiedź. 

Ta funkcja jest domyślnie włączona w okienku test, dzięki czemu można testować funkcje specyficzne dla danego scenariusza. Ta funkcja jest niezwykle korzystna dla deweloperów zawartości oraz użytkowników końcowych. Teraz deweloperzy zawartości nie muszą ręcznie nadzorować określonych par QnA dla każdego faktu obecnego w bazie wiedzy, a użytkownik końcowy nie musi wyszukać całego wyniku odpowiedzi zwróconego przez usługę, aby znaleźć faktyczny fakt, który odpowiada na zapytanie użytkownika. 

## <a name="precise-answering-on-qna-maker-portal"></a>Precyzyjne odpowiadanie w portalu QnA Maker

W portalu QnA Maker po otwarciu okienka testowego zobaczysz opcję **wyświetlania krótkiej odpowiedzi** na górze. Ta opcja zostanie wybrana domyślnie. Po wprowadzeniu zapytania w okienku testu zostanie wyświetlona krótka odpowiedź wraz z odciskiem odpowiedzi, jeśli w pojściu odpowiedzi występuje krótka odpowiedź. 
 
![Okienko testów z włączonym zarządzaniem](../QnAMaker/media/conversational-context/test-pane-with-managed.png)

Możesz usunąć zaznaczenie opcji **Wyświetl krótką odpowiedź** , jeśli chcesz zobaczyć tylko przepustą odpowiedź w okienku testów. 

Usługa zwraca również wynik poufności dokładnej odpowiedzi jako **wynik zakresu odpowiedzi** , który można sprawdzić, wybierając opcję **Sprawdź** , która jest dostępna tuż poniżej zapytania w okienku test.

![Wynik zarządzanego zakresu odpowiedzi](../QnAMaker/media/conversational-context/managed-answer-span-score.png)

## <a name="publishing-a-qna-maker-bot"></a>Publikowanie QnA Maker Bot

Gdy publikujesz bot, w aplikacji jest domyślnie dostępne precyzyjne włączenie funkcji odpowiedzi, w której zobaczysz krótką odpowiedź wraz z przedostaniem odpowiedzi. Użytkownik może wybrać inne środowisko, aktualizując szablon za pomocą eBot App Service. 

## <a name="language-support"></a>Obsługa języków

Obecnie dokładna funkcja odpowiedzi jest obsługiwana tylko w języku angielskim.
