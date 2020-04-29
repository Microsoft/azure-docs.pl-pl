---
author: vhorne
ms.service: dns
ms.topic: include
ms.date: 11/25/2018
ms.author: victorh
ms.openlocfilehash: 9cc650cea17acb8d89933c819c4ca60e2c459d1c
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/26/2020
ms.locfileid: "67183862"
---
Rekordy struktury zasad (SPF) nadawcy służą do określania, które serwery poczty e-mail mogą wysyłać wiadomości e-mail w imieniu nazwy domeny. Poprawna konfiguracja rekordów SPF jest ważna, aby uniemożliwić odbiorcom oznaczenie wiadomości e-mail jako wiadomości-śmieci.

Specyfikacje RFC systemu DNS pierwotnie wprowadziły nowy typ rekordu SPF do obsługi tego scenariusza. Aby zapewnić obsługę starszych serwerów nazw, można także użyć typu rekordu TXT do określenia rekordów SPF. Ta niejednoznaczność doprowadziła do pomyłki, która została rozwiązana przez [RFC 7208](http://tools.ietf.org/html/rfc7208#section-3.1). Stwierdza, że rekordy SPF muszą być tworzone przy użyciu typu rekordu TXT. Stwierdza również, że typ rekordu SPF jest przestarzały.

**Rekordy SPF są obsługiwane przez Azure DNS i muszą zostać utworzone przy użyciu typu rekordu TXT.** Przestarzały typ rekordu SPF nie jest obsługiwany. Podczas [importowania pliku strefy DNS](../articles/dns/dns-import-export.md)wszystkie rekordy programu SPF, które używają typu rekordu SPF, są konwertowane na typ rekordu TXT.
