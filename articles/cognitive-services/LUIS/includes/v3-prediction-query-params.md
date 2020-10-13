---
title: Parametry ciągu zapytania interfejsu API v3
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 06/30/2020
ms.openlocfilehash: 14380dd781fc1eebfe7edb0a816ff8af0f2f17b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91309436"
---
Parametry ciągu zapytania interfejsu API v3 obejmują:

|Parametr zapytania|Nazwa portalu LUIS|Type|Wersja|Domyślne|Przeznaczenie|
|--|--|--|--|--|--|
|`log`|Zapisz dzienniki|boolean|WERSJA 2 & V3|fałsz|Zapisz zapytanie w pliku dziennika. Wartość domyślna to false.|
|`query`|-|ciąg|Tylko wersja 3|Brak domyślnej — jest to wymagane w żądaniu GET|**W wersji 2**wypowiedź do przewidywania jest `q` parametrem. <br><br>**W wersji 3**funkcja jest przenoszona do `query` parametru.|
|`show-all-intents`|Uwzględnij wyniki dla wszystkich intencji|boolean|Tylko wersja 3|fałsz|Zwróć wszystkie intencje z odpowiednim wynikiem w obiekcie **przewidywania. intencje** . Intencje są zwracane jako obiekty w obiekcie nadrzędnym `intents` . Pozwala to na dostęp programistyczny bez konieczności wyszukiwania zamiaru w tablicy: `prediction.intents.give` . W wersji 2 te zostały zwrócone w tablicy. |
|`verbose`|Dołącz więcej szczegółów jednostek|boolean|WERSJA 2 & V3|fałsz|**W wersji 2**, gdy ustawiono wartość true, wszystkie przewidywane intencje zostały zwrócone. Jeśli potrzebujesz wszystkich przewidywanych intencji, użyj parametru v3 `show-all-intents` .<br><br>**W wersji 3**ten parametr zawiera tylko szczegóły metadanych jednostki przewidywania jednostek.  |
|`timezoneOffset`|-|ciąg|Wersja 2|-|Strefa czasowa zastosowana do jednostek datetimeV2.|
|`datetimeReference`|-|ciąg|Czytanie|-|[Strefa czasowa](../luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) zastosowana do jednostek datetimeV2. Zamienia `timezoneOffset` z wersji 2.|