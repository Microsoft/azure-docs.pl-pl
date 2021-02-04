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
ms.openlocfilehash: a2c29049decc056f0d3c8083d21574456610c124
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99555147"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Ważne zmiany w Azure Security Center

> [!IMPORTANT]
> Informacje na tej stronie odnoszą się do produktów lub funkcji w wersji wstępnej, które mogą być znacząco modyfikowane przed ich udostępnieniem komercyjnemu. Firma Microsoft nie udziela żadnych zobowiązań ani gwarancji, wyraźnych ani dorozumianych, w odniesieniu do informacji podanych w tym miejscu.

Na tej stronie znajdziesz informacje o zmianach, które są planowane dla Security Center. Opisano w nim planowane modyfikacje produktu, które mogą mieć wpływ na takie rzeczy jak bezpieczny wynik lub przepływy pracy.

Jeśli szukasz najnowszych informacji o wersji, znajdziesz je w temacie [co nowego w Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Planowane zmiany

- [Dwa zalecenia dotyczące kontroli zabezpieczeń "Zastosuj aktualizacje systemu" są przestarzałe](#two-recommendations-from-apply-system-updates-security-control-being-deprecated)
- [Ulepszenia zalecenia klasyfikacji danych SQL](#enhancements-to-sql-data-classification-recommendation)
- [Wycofanie z 11 alertów usługi Azure Defender](#deprecation-of-11-azure-defender-alerts)

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


### <a name="deprecation-of-11-azure-defender-alerts"></a>Wycofanie z 11 alertów usługi Azure Defender

**Szacowana data zmiany:** Marzec 2021

W następnym miesiącu jedenaście alertów usługi Azure Defender wymienionych poniżej zostanie wycofana.

- Nowe alerty zastępują te dwa alerty i zapewniają lepsze pokrycie:

    | AlertType                | AlertDisplayName                                                         |
    |--------------------------|--------------------------------------------------------------------------|
    | ARM_MicroBurstDomainInfo | WERSJA zapoznawcza — wykryto uruchomienie funkcji "Get-AzureDomainInfo" zestawu narzędzi mikroserii |
    | ARM_MicroBurstRunbook    | WERSJA zapoznawcza — wykryto uruchomienie funkcji "Get-AzurePasswords" zestawu narzędzi mikroserii  |
    |                          |                                                                          |

- Te dziewięć alertów odnoszą się do łącznika Azure Active Directory Identity Protection, który został już uznany za przestarzały:

    | AlertType           | AlertDisplayName              |
    |---------------------|-------------------------------|
    | UnfamiliarLocation  | Nieznane właściwości logowania |
    | AnonymousLogin      | Anonimowy adres IP          |
    | InfectedDeviceLogin | Połączony adres IP złośliwego oprogramowania     |
    | ImpossibleTravel    | Nietypowa podróż               |
    | MaliciousIP         | Złośliwy adres IP          |
    | LeakedCredentials   | Ujawnione poświadczenia            |
    | PasswordSpray       | Rozpylanie hasła                |
    | LeakedCredentials   | Analiza zagrożeń usługi Azure AD  |
    | AADAI               | Usługa Azure AD AI                   |
    |                     |                               |
 



## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z najnowszymi zmianami w produkcie, zobacz [co nowego w programie Azure Security Center?](release-notes.md).
