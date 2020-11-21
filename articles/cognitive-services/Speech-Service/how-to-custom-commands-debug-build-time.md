---
title: Debuguj błędy podczas tworzenia aplikacji poleceń niestandardowych (wersja zapoznawcza)
titleSuffix: Azure Cognitive Services
description: W tym artykule dowiesz się, jak debugować błędy podczas tworzenia aplikacji poleceń niestandardowych.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: aeb90e8e064c44f4d17f920261ed58310f0e55f0
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025705"
---
# <a name="debug-errors-when-authoring-a-custom-commands-application"></a>Debuguj błędy podczas tworzenia niestandardowej aplikacji poleceń

W tym artykule opisano sposób debugowania podczas wyświetlania błędów podczas kompilowania aplikacji poleceń niestandardowych. 

## <a name="errors-when-creating-an-application"></a>Błędy podczas tworzenia aplikacji
Polecenia niestandardowe tworzą również aplikację w [Luis](https://www.luis.ai/) podczas tworzenia aplikacji poleceń niestandardowych. 

[Luis ogranicza 500 aplikacji na zasób autorstwa](../luis/luis-limits.md). Tworzenie aplikacji LUIS może zakończyć się niepowodzeniem, jeśli używasz zasobu tworzenia, który ma już 500 aplikacji. 

Upewnij się, że wybrany zasób LUIS Authoring ma mniej niż 500 aplikacji. Jeśli nie, możesz utworzyć nowy zasób tworzenia LUIS, przełączyć się do innego lub spróbować wyczyścić aplikacje LUIS.  

## <a name="errors-when-deleting-an-application"></a>Błędy podczas usuwania aplikacji
### <a name="cant-delete-luis-application"></a>Nie można usunąć aplikacji LUIS
W przypadku usuwania niestandardowej aplikacji poleceń niestandardowe polecenia mogą także próbować usunąć aplikację LUIS skojarzoną z aplikacją poleceń niestandardowych.

Jeśli usuwanie aplikacji LUIS nie powiodło się, przejdź do swojego konta [Luis](https://www.luis.ai/) , aby usunąć je ręcznie.

### <a name="toomanyrequests"></a>TooManyRequests
Gdy użytkownik próbuje usunąć dużą liczbę aplikacji jednocześnie, prawdopodobnie zobaczysz błędy "TooManyRequests". Te błędy oznaczają, że żądania usunięcia są ograniczone przez platformę Azure. 

Odśwież stronę i spróbuj usunąć mniejszą liczbę aplikacji.

## <a name="errors-when-modifying-an-application"></a>Błędy podczas modyfikowania aplikacji

### <a name="cant-delete-a-parameter-or-a-web-endpoint"></a>Nie można usunąć parametru lub punktu końcowego sieci Web
Nie można usunąć parametru, gdy jest on używany. Usuń wszystkie odwołania do parametru w odpowiedzi na mowę, przykładowe zdania, warunki, akcje i spróbuj ponownie.

### <a name="cant-delete-a-web-endpoint"></a>Nie można usunąć punktu końcowego sieci Web
Nie można usunąć punktu końcowego sieci Web, gdy jest on używany. Usuń wszystkie akcje **internetowego punktu końcowego wywołania** , które korzystają z tego punktu końcowego sieci Web przed usunięciem punktu końcowego sieci Web.

## <a name="errors-when-training-an-application"></a>Błędy podczas uczenia aplikacji
### <a name="built-in-intents"></a>Built-In intencje
LUIS ma wbudowane opcje tak/nie. Gdy przykładowe zdania mają tylko wartość "yes", wartość "No" spowoduje niepowodzenie szkolenia. 

| Słowo kluczowe | Warianty | 
| ------- | --------- | 
| Tak | Upewnij się, że przycisk OK |
| Nie | Zgadza, nie | 

### <a name="common-sample-sentences"></a>Typowe zdania przykładowe
Polecenia niestandardowe nie zezwalają na wspólne zdania przykładowe wspólne dla różnych poleceń. Szkolenie aplikacji może się nie powieść, jeśli niektóre zdania przykładowe w jednym poleceniu są już zdefiniowane w innym poleceniu. 

Upewnij się, że nie masz wspólnych zdań przykładowych udostępnianych między różnymi poleceniami. 

Aby uzyskać najlepsze rozwiązanie dotyczące równoważenia przykładowych zdań w różnych poleceniach, należy zapoznać się z [najlepszymi](../luis/luis-concept-best-practices.md)rozwiązaniami Luis.

### <a name="empty-sample-sentences"></a>Puste zdania przykładowe
Musisz mieć co najmniej jedno zdanie przykładowe dla każdego polecenia.

### <a name="undefined-parameter-in-sample-sentences"></a>Niezdefiniowany parametr w zdaniach przykładowych
Co najmniej jeden parametr jest używany w przykładowych zdaniach, ale nie jest zdefiniowany.

### <a name="training-takes-too-long"></a>Szkolenie trwa zbyt długo
Szkolenia LUIS są przeznaczone do szybkiego uczenia się z mniejszą liczbą przykładów. Nie dodawaj zbyt wielu przykładowych zdań. 

Jeśli masz wiele przykładowych zdań, zdefiniuj parametr, Podziel go na wzorzec i dodaj go do przykładowych zdań.

Na przykład można zdefiniować parametr {pojazd} dla przykładowych zdań poniżej i dodać tylko "książka" {pojazd} "do przykładu zdań.

| Przykładowe zdania | Wzorce | 
| ------- | ------- | 
| Książka samochodu | Książka a {pojazd} | 
| Książka a samolotem | Książka a {pojazd} |
| Książka z taksówką | Książka a {pojazd} |

Najlepsze rozwiązanie z zakresu szkoleń LUIS można znaleźć w [Luis najlepszych](../luis/luis-concept-best-practices.md)rozwiązań.

## <a name="cant-update-luis-key"></a>Nie można zaktualizować klucza LUIS
### <a name="reassign-to-e0-authoring-resource"></a>Przypisz ponownie do zasobu E0 Authoring
Usługa LUIS nie obsługuje ponownego przypisywania aplikacji LUIS do zasobu tworzenia E0.

Jeśli zachodzi potrzeba zmiany zasobu tworzenia z F0 na E0 lub zmiany na inny zasób E0, należy ponownie utworzyć aplikację. 

Aby szybko wyeksportować istniejącą aplikację i zaimportować ją do nowej aplikacji, zobacz [ciągłe wdrażanie za pomocą usługi Azure DevOps](./how-to-custom-commands-deploy-cicd.md).

### <a name="save-button-is-disabled"></a>Przycisk Zapisz jest wyłączony
Jeśli nie przypiszesz do aplikacji zasobu przewidywania LUIS, przycisk Zapisz zostanie wyłączony podczas próby zmiany zasobu tworzenia bez dodawania zasobów predykcyjnych.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zobacz przykłady w witrynie GitHub](https://aka.ms/speech/cc-samples)