---
title: Jak odpowiedzieć na alerty DNS w usłudze Azure Defender
description: Informacje o krokach, które należy wykonać w celu reagowania na alerty z usługi Azure Defender dla systemu DNS
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 375a81127a000741ca5e0397642800794610bcda
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754775"
---
# <a name="respond-to-azure-defender-for-dns-alerts"></a>Odpowiadanie na alerty DNS w usłudze Azure Defender

Po otrzymaniu alertu z usługi Azure Defender dla systemu DNS zalecamy badanie alertu i jego odpowiedź zgodnie z poniższym opisem. Usługa Azure Defender dla systemu DNS chroni wszystkie połączone zasoby, więc nawet w przypadku znajomości aplikacji lub użytkownika, który wyzwolił alert, ważne jest, aby sprawdzić sytuacje związane z każdym alertem.  


## <a name="step-1-contact"></a>Krok 1. Kontakt

1. Skontaktuj się z właścicielem zasobu, aby określić, czy zachowanie było oczekiwane lub zamierzone.
1. Jeśli działanie jest oczekiwane, Odrzuć alert.
1. Jeśli działanie jest nieoczekiwane, należy traktować zasób jako potencjalnie złamany i rozwiązać ten problem zgodnie z opisem w następnym kroku.

## <a name="step-2-immediate-mitigation"></a>Krok 2. Natychmiastowe łagodzenie 

1. Izoluj zasób od sieci, aby zapobiec przeniesieniu ruchu bocznego.
1. Uruchom pełne skanowanie chroniące przed złośliwym kodem na tym zasobie, wykonując wszelkie uzyskane porady dotyczące korygowania.
1. Zapoznaj się z zainstalowanym i uruchomionym oprogramowaniem w ramach zasobu, usuwając wszystkie nieznane lub niechciane pakiety.
1. Przywróć komputer do znanego dobrego stanu, ponownie zainstaluj system operacyjny, jeśli jest to wymagane, a następnie Przywróć oprogramowanie z zweryfikowanego źródła wolnego pod kątem złośliwego oprogramowania.
1. Rozwiąż wszelkie Azure Security Center zalecenia dotyczące maszyny, korygowaniem wyróżnione problemy z zabezpieczeniami, aby uniknąć przyszłych naruszeń.


## <a name="next-steps"></a>Następne kroki

Ta strona wyjaśnia proces reagowania na alert z usługi Azure Defender dla systemu DNS. Aby uzyskać powiązane informacje, zobacz następujące strony:

- [Wprowadzenie do usługi Azure Defender dla systemu DNS](defender-for-dns-introduction.md)
- [Pomijanie alertów z usługi Azure Defender](alerts-suppression-rules.md)
- [Ciągłe eksportowanie danych Security Center](continuous-export.md)