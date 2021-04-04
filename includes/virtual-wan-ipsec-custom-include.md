---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/07/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 83f0ce27172879a37de9488499e46de30b8e112c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98147411"
---
Podczas pracy z niestandardowymi zasadami protokołu IPsec należy pamiętać o następujących wymaganiach:

* **IKE** -dla protokołu IKE można wybrać dowolny parametr z szyfrowania IKE oraz dowolny parametr z integralności IKE i dowolny parametr z grupy DH.
* **IPSec** — w przypadku protokołu IPSec można wybrać dowolny parametr z szyfrowania IPSec oraz dowolny parametr z integralności IPSec i PFS. W przypadku GCM dowolnego z parametrów szyfrowania IPsec lub integralności protokołu IPsec parametry obu ustawień muszą mieć wartość GCM.

>[!NOTE]
> W przypadku niestandardowych zasad protokołu IPsec nie ma koncepcji obiektu odpowiadającego ani inicjatora (w przeciwieństwie do domyślnych zasad protokołu IPsec). Obie strony (lokalna i Brama sieci VPN platformy Azure) będą używać tych samych ustawień dla usług IKE phase 1 i IKE Phase 2. Obsługiwane są zarówno protokoły IKEv1, jak i IKEv2. Brak obsługi platformy Azure jako obiektu odpowiadającego.
>

**Dostępne ustawienia i parametry**

| Ustawienie | Parametry |
|--- |--- |
| Szyfrowanie IKE | GCMAES256, GCMAES128, AES256, AES128 |
| Integralność IKE | SHA384, SHA256 |
| Grupa DH | ECP384, ECP256, DHGroup24, DHGroup14 |
| Szyfrowanie IPsec | GCMAES256, GCMAES128, AES256, AES128, brak |
| Integralność IPsec | GCMAES256, GCMAES128, SHA256 |
| Grupa PFS | ECP384, ECP256, PFS24, PFS14, brak |
| Okres istnienia skojarzeń zabezpieczeń |całkowitą min. 300/domyślnie 27000 s |
