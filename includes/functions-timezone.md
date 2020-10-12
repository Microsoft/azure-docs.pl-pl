---
ms.openlocfilehash: dba7a3cc7a68d360fd6e56511b71ae364f624646
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89569286"
---
Domyślna strefa czasowa używana z wyrażeniami firmy CRONUS jest uniwersalnym czasem koordynowanym (UTC). Aby wyrażenie firmy CRONUS było oparte na innej strefie czasowej, należy utworzyć ustawienie aplikacji dla aplikacji funkcji o nazwie `WEBSITE_TIME_ZONE` . 

Wartość tego ustawienia zależy od systemu operacyjnego oraz planu, w którym działa aplikacja funkcji.

|System operacyjny |Planowanie |Wartość |
|-|-|-|
| **Windows** |Wszystkie | Ustaw wartość na nazwę żądanej strefy czasowej, jak pokazano w [indeksie strefy czasowej firmy Microsoft](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-vista/cc749073(v=ws.10)). |
| **Linux** |Premium<br/>Dedykowane |Ustaw wartość na nazwę żądanej strefy czasowej, jak pokazano w [bazie danych](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones). |

> [!NOTE]
> `WEBSITE_TIME_ZONE` nie jest obecnie obsługiwany w planie zużycia systemu Linux.

Na przykład: *Wschodni czas standardowy* (Windows) lub *Ameryka/New_York* (Linux) to UTC-05:00. Aby wyzwalacz czasomierza był wyzwalany codziennie o godzinie 10:00, należy użyć następującego wyrażenia NCRONTAB, które jest kontem dla strefy czasowej UTC:

```
"0 0 15 * * *"
``` 

Lub Utwórz ustawienia aplikacji dla aplikacji funkcji o nazwie `WEBSITE_TIME_ZONE` , ustaw wartość na `Eastern Standard Time` (Windows) lub `America/New_York` (Linux), a następnie użyj następującego wyrażenia NCRONTAB: 

```
"0 0 10 * * *"
``` 

Gdy używasz `WEBSITE_TIME_ZONE` , czas jest dostosowywany do zmian czasu w określonej strefie czasowej, na przykład czasu letniego. 
