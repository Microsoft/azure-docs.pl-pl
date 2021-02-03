---
title: Ważne zmiany dotyczące Azure Security Center
description: Nadchodzące zmiany w Azure Security Center, które mogą być potrzebne, i dla których może być konieczne zaplanowanie
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/25/2021
ms.author: memildin
ms.openlocfilehash: 8ef0fedcbf7ce06063db22fb345a0711acddcb4d
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526385"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Ważne zmiany w Azure Security Center

> [!IMPORTANT]
> Informacje na tej stronie odnoszą się do produktów lub funkcji w wersji wstępnej, które mogą być znacząco modyfikowane przed ich udostępnieniem komercyjnemu. Firma Microsoft nie udziela żadnych zobowiązań ani gwarancji, wyraźnych ani dorozumianych, w odniesieniu do informacji podanych w tym miejscu.

Na tej stronie znajdziesz informacje o zmianach, które są planowane dla Security Center. Opisano w nim planowane modyfikacje produktu, które mogą mieć wpływ na takie rzeczy jak bezpieczny wynik lub przepływy pracy.

Jeśli szukasz najnowszych informacji o wersji, znajdziesz je w temacie [co nowego w Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Planowane zmiany

- [Dwa zalecenia dotyczące kontroli zabezpieczeń "Zastosuj aktualizacje systemu" są przestarzałe](#two-recommendations-from-apply-system-updates-security-control-being-deprecated)
- [Ulepszenia zalecenia klasyfikacji danych SQL](#enhancements-to-sql-data-classification-recommendation)

### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Dwa zalecenia dotyczące kontroli zabezpieczeń "Zastosuj aktualizacje systemu" są przestarzałe 

**Szacowana data zmiany:** Luty 2021

Następujące dwa zalecenia zaplanowano jako przestarzałe w lutym 2021:

- **Aby zastosować aktualizacje systemu, należy ponownie uruchomić maszyny**. Może to skutkować niewielkim wpływem na bezpieczny wynik.
- **Na maszynach należy zainstalować agenta monitorowania**. To zalecenie odnosi się tylko do maszyn lokalnych, a część jej logiki zostanie przetransferowana do innego zalecenia, **log Analytics na maszynach należy rozwiązać problemy z kondycją agenta**. Może to skutkować niewielkim wpływem na bezpieczny wynik.

Zalecamy sprawdzenie konfiguracji eksportu ciągłego i automatyzacji przepływu pracy, aby sprawdzić, czy te zalecenia są zawarte w nich. Ponadto należy odpowiednio zaktualizować wszystkie pulpity nawigacyjne lub inne narzędzia do monitorowania, które mogą z nich korzystać.

Więcej informacji na temat tych zaleceń znajduje się na [stronie informacje o zaleceniach dotyczących zabezpieczeń](recommendations-reference.md).


### <a name="enhancements-to-sql-data-classification-recommendation"></a>Ulepszenia zalecenia klasyfikacji danych SQL

**Szacowana data zmiany:** Q2 2021

Dane dotyczące rekomendacji **w bazach danych SQL powinny zostać sklasyfikowane** w kontroli zabezpieczeń **Zastosuj klasyfikację danych** w nowej wersji, która jest lepiej wyrównana przy użyciu strategii klasyfikacji danych firmy Microsoft. W wyniku tego zostanie również zmieniony identyfikator zalecenia (obecnie b0df6f56-862d-4730-8597-38c0fd4ebd59).



## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z najnowszymi zmianami w produkcie, zobacz [co nowego w programie Azure Security Center?](release-notes.md).
