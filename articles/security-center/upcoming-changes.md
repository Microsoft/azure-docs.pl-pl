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
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 375e8a748e8833e9483d92353ed04add287e90fb
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101705096"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Ważne zmiany w Azure Security Center

> [!IMPORTANT]
> Informacje na tej stronie odnoszą się do produktów lub funkcji w wersji wstępnej, które mogą być znacząco modyfikowane przed ich udostępnieniem komercyjnemu. Firma Microsoft nie udziela żadnych zobowiązań ani gwarancji, wyraźnych ani dorozumianych, w odniesieniu do informacji podanych w tym miejscu.

Na tej stronie znajdziesz informacje o zmianach, które są planowane dla Security Center. Opisano w nim planowane modyfikacje produktu, które mogą mieć wpływ na takie rzeczy jak bezpieczny wynik lub przepływy pracy.

Jeśli szukasz najnowszych informacji o wersji, znajdziesz je w temacie [co nowego w Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Planowane zmiany

- [Dwa starsze rekomendacje nie będą już zapisywać danych bezpośrednio w dzienniku aktywności platformy Azure](#two-legacy-recommendations-will-no-longer-write-data-directly-to-azure-activity-log)
- [Dwa zalecenia dotyczące kontroli zabezpieczeń "Zastosuj aktualizacje systemu" są przestarzałe](#two-recommendations-from-apply-system-updates-security-control-being-deprecated)
- [Ulepszenia zalecenia klasyfikacji danych SQL](#enhancements-to-sql-data-classification-recommendation)
- [Wycofanie z 11 alertów usługi Azure Defender](#deprecation-of-11-azure-defender-alerts)


### <a name="two-legacy-recommendations-will-no-longer-write-data-directly-to-azure-activity-log"></a>Dwa starsze rekomendacje nie będą już zapisywać danych bezpośrednio w dzienniku aktywności platformy Azure 

**Szacowana data zmiany:** Marzec 2021

Security Center przekazuje dane dla niemal wszystkich zaleceń dotyczących zabezpieczeń, aby Azure Advisor, które z kolei zapisują je do [dziennika aktywności platformy Azure](../azure-monitor/essentials/activity-log.md).

W przypadku dwóch zaleceń dane są zapisywane równocześnie bezpośrednio w dzienniku aktywności platformy Azure. W przypadku tej zmiany Security Center przestaną zapisywać dane dla tych starszych zaleceń dotyczących zabezpieczeń bezpośrednio do dziennika aktywności. Zamiast tego eksportuje dane do Azure Advisor jak w przypadku wszystkich innych zaleceń. 

Istnieją dwa starsze zalecenia:
- Na maszynach należy rozwiązać problemy dotyczące kondycji programu Endpoint Protection
- Luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach należy skorygować

Jeśli uzyskano dostęp do informacji dotyczących tych dwóch zaleceń w kategorii "zalecenia dotyczące typu TaskDiscovery", nie będzie to już możliwe.

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
