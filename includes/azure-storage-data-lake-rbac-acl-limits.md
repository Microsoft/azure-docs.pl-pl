---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 8b793a28c31df185555b9a548df52e4beeccf305
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131636"
---
| Mechanism (Mechanizm) | Zakres |Limity | Obsługiwany poziom uprawnień |
|---|---|---|---|
| Kontrola dostępu oparta na rolach | Konta magazynu, kontenery. <br>Przypisania ról RBAC między zasobami na poziomie subskrypcji lub grupy zasobów. | 2000 przypisań ról RBAC w ramach subskrypcji | Role RBAC (wbudowane lub niestandardowe) |
| STARSZ| Katalog, plik |32 wpisy listy ACL (efektywne 28 wpisów listy ACL) dla każdego pliku i dla katalogu. Dostęp i domyślne listy ACL mają własne limity wpisów listy kontroli dostępu 32. |Uprawnienie listy ACL|
