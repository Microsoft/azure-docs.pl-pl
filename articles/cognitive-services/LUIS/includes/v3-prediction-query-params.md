---
title: Parametry ciągu zapytania interfejsu API v3
ms.topic: include
ms.date: 06/30/2020
ms.openlocfilehash: 47727f6df772669fa323a10cd099764a6d35cb6a
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2020
ms.locfileid: "85610793"
---
Parametry ciągu zapytania interfejsu API v3 obejmują:

|Parametr zapytania|Nazwa portalu LUIS|Typ|Wersja|Domyślny|Przeznaczenie|
|--|--|--|--|--|--|
|`log`|Zapisz dzienniki|wartość logiczna|WERSJA 2 & V3|fałsz|Zapisz zapytanie w pliku dziennika. Wartość domyślna to false.|
|`query`|-|ciąg|Tylko wersja 3|Brak domyślnej — jest to wymagane w żądaniu GET|**W wersji 2**wypowiedź do przewidywania jest `q` parametrem. <br><br>**W wersji 3**funkcja jest przenoszona do `query` parametru.|
|`show-all-intents`|Uwzględnij wyniki dla wszystkich intencji|wartość logiczna|Tylko wersja 3|fałsz|Zwróć wszystkie intencje z odpowiednim wynikiem w obiekcie **przewidywania. intencje** . Intencje są zwracane jako obiekty w obiekcie nadrzędnym `intents` . Pozwala to na dostęp programistyczny bez konieczności wyszukiwania zamiaru w tablicy: `prediction.intents.give` . W wersji 2 te zostały zwrócone w tablicy. |
|`verbose`|Dołącz więcej szczegółów jednostek|wartość logiczna|WERSJA 2 & V3|fałsz|**W wersji 2**, gdy ustawiono wartość true, wszystkie przewidywane intencje zostały zwrócone. Jeśli potrzebujesz wszystkich przewidywanych intencji, użyj parametru v3 `show-all-intents` .<br><br>**W wersji 3**ten parametr zawiera tylko szczegóły metadanych jednostki przewidywania jednostek.  |
|`timezoneOffset`|-|ciąg|Wersja 2|-|Strefa czasowa zastosowana do jednostek datetimeV2.|
|`datetimeReference`|-|ciąg|Czytanie|-|[Strefa czasowa](../luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) zastosowana do jednostek datetimeV2. Zamienia `timezoneOffset` z wersji 2.|