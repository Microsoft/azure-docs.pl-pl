---
title: Testowanie piórem | Microsoft Docs
description: Artykuł zawiera omówienie procesu testowania penetracji (Pentest) i sposobu, w jaki Pentest aplikacje działające w infrastrukturze platformy Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2018
ms.author: terrylan
ms.openlocfilehash: 413ec8b121838a4ffac4119421ec3266e141618b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80549251"
---
# <a name="penetration-testing"></a>Testowanie penetracji
Jedną z zalet korzystania z platformy Azure do testowania i wdrażania aplikacji jest możliwość szybkiego uzyskiwania utworzonych środowisk. Nie musisz martwić się o rozmieszczenie, nabywanie i "rozmieszczenie" sprzętu lokalnego.

Jest to doskonałe rozwiązanie, ale nadal trzeba upewnić się, że wykonywane jest normalne zachowanie zabezpieczeń. Jednym z elementów, które prawdopodobnie chcesz zrobić, jest przetestowanie aplikacji wdrażanych na platformie Azure.

Być może wiesz już, że firma Microsoft przeprowadza [testowanie penetracji środowiska platformy Azure](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e). Ułatwia to rozwiązanie ulepszeń platformy Azure.

Nie przejdziemy do przetestowania aplikacji, ale zdajemy sobie sprawę, że będziesz mieć możliwość testowania własnych aplikacji. Jest to dobry efekt, ponieważ w przypadku zwiększenia bezpieczeństwa aplikacji pomaga zwiększyć bezpieczeństwo całego ekosystemu platformy Azure.

Od 15 czerwca 2017 firma Microsoft nie wymaga już wstępnego zatwierdzenia, aby przeprowadzić test penetracji zasobów platformy Azure. Klienci, którzy chcą formalnie udokumentować zaangażowanie w testowanie penetracji w odniesieniu do Microsoft Azure są zachęcani do wypełniania [formularza powiadomienia o testowaniu usługi platformy Azure](https://portal.msrc.microsoft.com/en-us/engage/pentest). Ten proces dotyczy wyłącznie Microsoft Azure i nie ma zastosowania do żadnej innej usługi Microsoft Cloud.

>[!IMPORTANT]
>Chociaż powiadomienie firmy Microsoft o działaniach związanych z testowaniem pióra nie jest już wymagane, klienci muszą nadal przestrzegać [Microsoft Cloud ujednoliconych reguł testowania penetracji](https://technet.microsoft.com/mt784683).

Standardowe testy, które można wykonać, obejmują:

* Testuje punkty końcowe, aby odkryć [10 najważniejszych luk w zabezpieczeniach aplikacji sieci Web (OWASP)](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [Testy rozmyte](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) dla punktów końcowych
* [Skanowanie portów](https://en.wikipedia.org/wiki/Port_scanner) punktów końcowych

Jednym z typów testów, których nie można wykonać, jest atak [typu "odmowa usługi" (DOS)](https://en.wikipedia.org/wiki/Denial-of-service_attack) . Obejmuje to zainicjowanie samego ataku systemu DoS lub wykonanie powiązanych testów, które mogą ustalić, przedstawić lub symulować ataki typu DoS.

## <a name="next-steps"></a>Następne kroki

- Jeśli chcesz formalnie udokumentować nadchodzące testy penetracji dla aplikacji hostowanych w Microsoft Azure, przejdź do sekcji [reguły testowania penetracji zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2) i wypełnij formularz powiadomienia o testowaniu.
