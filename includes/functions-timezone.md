---
ms.openlocfilehash: b4fbeb6baa4516ca4cf3ca6194195fae2c688b07
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86165666"
---
Domyślna strefa czasowa używana z wyrażeniami firmy CRONUS jest uniwersalnym czasem koordynowanym (UTC). Aby wyrażenie firmy CRONUS było oparte na innej strefie czasowej, należy utworzyć ustawienie aplikacji dla aplikacji funkcji o nazwie `WEBSITE_TIME_ZONE` . 

Wartość tego ustawienia zależy od systemu operacyjnego oraz planu, w którym działa aplikacja funkcji.

|System operacyjny |Plan |Wartość |
|-|-|-|
| **Windows** |Wszystko | Ustaw wartość na nazwę żądanej strefy czasowej, jak pokazano w [indeks strefy czasowej firmy Microsoft] ( https://docs.microsoft.com/previous-versions/windows/it-pro/windows-vista/cc749073(v=ws.10) . |
| **Linux** |Premium<br/>Dedykowane |Ustaw wartość na nazwę żądanej strefy czasowej, jak pokazano w [bazie danych](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones). |

> [!NOTE]
> `WEBSITE_TIME_ZONE`nie jest obecnie obsługiwany w planie zużycia systemu Linux.

Na przykład: *Wschodni czas standardowy* (Windows) lub *Ameryka/New_York* (Linux) to UTC-05:00. Aby wyzwalacz czasomierza był wyzwalany codziennie o godzinie 10:00, należy użyć następującego wyrażenia NCRONTAB, które jest kontem dla strefy czasowej UTC:

```
"0 0 15 * * *"
``` 

Lub Utwórz ustawienia aplikacji dla aplikacji funkcji o nazwie `WEBSITE_TIME_ZONE` , ustaw wartość na `Eastern Standard Time` (Windows) lub `America/New_York` (Linux), a następnie użyj następującego wyrażenia NCRONTAB: 

```
"0 0 10 * * *"
``` 

Gdy używasz `WEBSITE_TIME_ZONE` , czas jest dostosowywany do zmian czasu w określonej strefie czasowej, na przykład czasu letniego. 
