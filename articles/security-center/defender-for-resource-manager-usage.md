---
title: Jak odpowiedzieć na alerty usługi Azure Defender Menedżer zasobów
description: Informacje o krokach, które należy wykonać w celu reagowania na alerty z usługi Azure Defender dla Menedżer zasobów
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 54790795aab8aac247e17198159130d7139dd38c
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754742"
---
# <a name="respond-to-azure-defender-for-resource-manager-alerts"></a>Reagowanie na alerty usługi Azure Defender Menedżer zasobów

Po otrzymaniu alertu z usługi Azure Defender dla Menedżer zasobów zalecamy badanie alertu i jego odpowiedź zgodnie z poniższym opisem. Usługa Azure Defender dla Menedżer zasobów chroni wszystkie połączone zasoby, więc nawet w przypadku znajomości aplikacji lub użytkownika, który wyzwolił alert, ważne jest, aby sprawdzić sytuacje związane z każdym alertem.  


## <a name="step-1-contact"></a>Krok 1. Kontakt

1. Skontaktuj się z właścicielem zasobu, aby określić, czy zachowanie było oczekiwane lub zamierzone.
1. Jeśli działanie jest oczekiwane, Odrzuć alert.
1. Jeśli działanie jest nieoczekiwane, należy traktować powiązane konta użytkowników, subskrypcje i maszyny wirtualne jako naruszone i rozwiązać problem zgodnie z opisem w poniższym kroku.

## <a name="step-2-immediate-mitigation"></a>Krok 2. Natychmiastowe łagodzenie 

1. Koryguj naruszone konta użytkowników:
    - Jeśli są one nieznane, usuń je, ponieważ mogły zostać utworzone przez aktora zagrożeń
    - Jeśli są znane, Zmień poświadczenia uwierzytelniania
    - Użyj dzienników aktywności platformy Azure, aby przejrzeć wszystkie działania wykonywane przez użytkownika i zidentyfikować podejrzane

1. Koryguj subskrypcje naruszone:
    - Usuń nieznane elementy Runbook ze złamanego konta usługi Automation
    - Przejrzyj uprawnienia usługi IAM dla subskrypcji i Usuń uprawnienia dla dowolnego nieznanego konta użytkownika
    - Przejrzyj wszystkie zasoby platformy Azure w subskrypcji i usuń te, które nie są znane
    - Przejrzyj i zbadaj alerty zabezpieczeń dla subskrypcji w Azure Security Center
    - Użyj dzienników aktywności platformy Azure, aby przejrzeć wszystkie działania wykonywane w ramach subskrypcji i zidentyfikować podejrzane

1. Koryguj zagrożone maszyny wirtualne
    - Zmień hasła dla wszystkich użytkowników
    - Uruchom pełne skanowanie w poszukiwaniu złośliwego oprogramowania na komputerze
    - Odtworzenie obrazu maszyn ze źródła wolnego od złośliwego oprogramowania


## <a name="next-steps"></a>Następne kroki

Ta strona wyjaśnia proces reagowania na alert z usługi Azure Defender dla Menedżer zasobów. Aby uzyskać powiązane informacje, zobacz następujące strony:

- [Wprowadzenie do usługi Azure Defender dla Menedżer zasobów](defender-for-resource-manager-introduction.md)
- [Pomijanie alertów z usługi Azure Defender](alerts-suppression-rules.md)
- [Ciągłe eksportowanie danych Security Center](continuous-export.md)