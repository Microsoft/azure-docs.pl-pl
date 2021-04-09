---
title: Testowanie penetracji | Microsoft Docs
description: Artykuł zawiera omówienie procesu testowania penetracji oraz sposób wykonywania testu pióra względem aplikacji działającej w infrastrukturze platformy Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: terrylan
ms.openlocfilehash: 9f65133f622c71189f35a1533e278ca09ab02085
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99550205"
---
# <a name="penetration-testing"></a>Testy penetracyjne

Jedną z zalet korzystania z platformy Azure do testowania i wdrażania aplikacji jest możliwość szybkiego uzyskiwania utworzonych środowisk. Nie musisz martwić się o rozmieszczenie, nabywanie i "rozmieszczenie" sprzętu lokalnego.

Szybkie tworzenie środowisk jest doskonałe, ale nadal musisz mieć pewność, że chcesz mieć normalne zachowanie z powodu zabezpieczeń. Jednym z elementów, które prawdopodobnie chcesz zrobić, jest przetestowanie aplikacji wdrażanych na platformie Azure.

Nie przeprowadzimy testów dla aplikacji, ale rozumiemy, że chcesz, aby przetestować swoje aplikacje. Jest to dobry efekt, ponieważ w przypadku zwiększenia bezpieczeństwa aplikacji pomaga zwiększyć bezpieczeństwo całego ekosystemu platformy Azure.

Od 15 czerwca 2017 r. firma Microsoft nie wymaga już wcześniejszej zgody na przeprowadzenie testu penetracyjnego zasobów platformy Azure. Ten proces dotyczy wyłącznie Microsoft Azure i nie ma zastosowania do żadnej innej usługi Microsoft Cloud.

>[!IMPORTANT]
>Chociaż powiadomienie firmy Microsoft o działaniach związanych z testowaniem pióra nie jest już wymagane, klienci muszą nadal przestrzegać [Microsoft Cloud ujednoliconych reguł testowania penetracji](https://technet.microsoft.com/mt784683).

Standardowe testy, które można wykonać, obejmują:

* Testuje punkty końcowe, aby odkryć [10 najważniejszych luk w zabezpieczeniach aplikacji sieci Web (OWASP)](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [Testowanie odporności na błędne dane](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) w punktach końcowych
* [Skanowanie portów](https://en.wikipedia.org/wiki/Port_scanner) w punktach końcowych

Jednym z typów testów pióra, których nie można wykonać, jest atak [typu "odmowa usługi" (DOS)](https://en.wikipedia.org/wiki/Denial-of-service_attack) . Ten test obejmuje zainicjowanie samego ataku systemu DoS lub wykonanie powiązanych testów, które mogą ustalić, przedstawić lub symulować ataki typu DoS.

>[!Note]
>Firma Microsoft połączyła się z chmurą BreakingPoint, aby utworzyć interfejs, w którym można generować ruch dla publicznych adresów IP z włączoną obsługą DDoS Protection na potrzeby symulacji. Aby dowiedzieć się więcej o symulacji chmury BreakingPoint, zobacz [testowanie za pomocą symulacji](../../ddos-protection/test-through-simulations.md).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat [reguł testowania penetracji](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2).
