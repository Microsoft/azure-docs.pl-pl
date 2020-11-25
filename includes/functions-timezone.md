---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/20/2020
ms.author: glenga
ms.openlocfilehash: 7d1bf8dd2d1c8feab8b051a8edad7d5e570ee11b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96028092"
---
Domyślna strefa czasowa używana z wyrażeniami firmy CRONUS jest uniwersalnym czasem koordynowanym (UTC). Aby wyrażenie firmy CRONUS było oparte na innej strefie czasowej, należy utworzyć ustawienie aplikacji dla aplikacji funkcji o nazwie `WEBSITE_TIME_ZONE` . 

Wartość tego ustawienia zależy od systemu operacyjnego oraz planu, w którym działa aplikacja funkcji.

|System operacyjny |Planowanie |Wartość |
|-|-|-|
| **Windows** |Wszystko | Ustaw wartość na nazwę żądanej strefy czasowej określoną przez drugi wiersz z każdej pary podanej przez polecenie systemu Windows `tzutil.exe /L` |
| **Linux** |Premium<br/>Dedykowane |Ustaw wartość na nazwę żądanej strefy czasowej, jak pokazano w [bazie danych](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones). |

> [!NOTE]
> `WEBSITE_TIME_ZONE` nie jest obecnie obsługiwany w planie zużycia systemu Linux.

Na przykład czas wschodni w Stanach Zjednoczonych (reprezentowane przez `Eastern Standard Time` System (Windows) lub `America/New_York` (Linux)) obecnie używa czasu utc-05:00 w czasie standardowym i UTC-04:00 podczas czasu letniego. Aby wyzwolić wyzwalacz czasomierza o godzinie 10:00 czasu wschodniego, Utwórz ustawienia aplikacji dla aplikacji funkcji o nazwie `WEBSITE_TIME_ZONE` , ustaw wartość na `Eastern Standard Time` (Windows) lub `America/New_York` (Linux), a następnie użyj następującego wyrażenia NCRONTAB: 

```
"0 0 10 * * *"
``` 

Kiedy używasz `WEBSITE_TIME_ZONE` czasu, zmiany czasu w określonej strefie czasowej, w tym czas letni i zmiany w czasie standardowym.
