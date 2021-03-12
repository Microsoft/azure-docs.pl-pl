---
title: Ważne zmiany dotyczące Azure Security Center
description: Nadchodzące zmiany w Azure Security Center, które mogą być potrzebne, i dla których może być konieczne zaplanowanie
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 03/10/2021
ms.author: memildin
ms.openlocfilehash: 49141f7f11c0e8ead090459238e15b56f57b990b
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "102633720"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Ważne zmiany w Azure Security Center

> [!IMPORTANT]
> Informacje na tej stronie odnoszą się do produktów lub funkcji w wersji wstępnej, które mogą być znacząco modyfikowane przed ich udostępnieniem komercyjnemu. Firma Microsoft nie udziela żadnych zobowiązań ani gwarancji, wyraźnych ani dorozumianych, w odniesieniu do informacji podanych w tym miejscu.

Na tej stronie znajdziesz informacje o zmianach, które są planowane dla Security Center. Opisano w nim planowane modyfikacje produktu, które mogą mieć wpływ na takie rzeczy jak bezpieczny wynik lub przepływy pracy.

Jeśli szukasz najnowszych informacji o wersji, znajdziesz je w temacie [co nowego w Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Planowane zmiany

- [Zalecenia z usługi AWS zostaną wydane w celu uzyskania ogólnej dostępności (GA)](#recommendations-from-aws-will-be-released-for-general-availability-ga)
- [Dwa zalecenia dotyczące kontroli zabezpieczeń "Zastosuj aktualizacje systemu" są przestarzałe](#two-recommendations-from-apply-system-updates-security-control-being-deprecated)
- [Ulepszenia zalecenia klasyfikacji danych SQL](#enhancements-to-sql-data-classification-recommendation)
- [Wycofanie z 11 alertów usługi Azure Defender](#deprecation-of-11-azure-defender-alerts)


### <a name="recommendations-from-aws-will-be-released-for-general-availability-ga"></a>Zalecenia z usługi AWS zostaną wydane w celu uzyskania ogólnej dostępności (GA)

**Szacowana data zmiany:** Kwiecień 2021

Azure Security Center chroni obciążenia na platformie Azure, Amazon Web Services (AWS) i Google Cloud Platform (GCP).

Zalecenia pochodzące z Centrum zabezpieczeń AWS były dostępne w wersji zapoznawczej od momentu wprowadzenia łączników w chmurze. Zalecenia oflagowane jako **wersja zapoznawcza** nie są uwzględniane w obliczeniach bezpiecznego wyniku, ale powinny być nadal korygowane wszędzie tam, gdzie jest to możliwe, dzięki czemu po zakończeniu okresu zapoznawczego dojdzie do oceny.

Dzięki tej zmianie dwa zestawy zaleceń AWS zostaną przeniesione do wersji ogólnie dostępnej:

- [Kontrolki PCI DSS Centrum zabezpieczeń](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-pci-controls.html)
- [Formanty testu porównawczego w centrum zabezpieczeń CIS AWS](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-cis-controls.html)

Gdy są one dostępne, a oceny są uruchamiane w swoich zasobach AWS, wyniki będą miały wpływ na łączny, bezpieczny wynik dla wszystkich zasobów chmurowych i hybrydowych. 


### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Dwa zalecenia dotyczące kontroli zabezpieczeń "Zastosuj aktualizacje systemu" są przestarzałe 

**Szacowana data zmiany:** Marzec 2021

Następujące dwa zalecenia zaplanowano jako przestarzałe w lutym 2021:

- **Aby zastosować aktualizacje systemu, należy ponownie uruchomić maszyny**. Może to skutkować niewielkim wpływem na bezpieczny wynik.
- **Na maszynach należy zainstalować agenta monitorowania**. To zalecenie odnosi się tylko do maszyn lokalnych, a część jej logiki zostanie przetransferowana do innego zalecenia, **log Analytics na maszynach należy rozwiązać problemy z kondycją agenta**. Może to skutkować niewielkim wpływem na bezpieczny wynik.

Zalecamy sprawdzenie konfiguracji eksportu ciągłego i automatyzacji przepływu pracy, aby sprawdzić, czy te zalecenia są zawarte w nich. Ponadto należy odpowiednio zaktualizować wszystkie pulpity nawigacyjne lub inne narzędzia do monitorowania, które mogą z nich korzystać.

Więcej informacji na temat tych zaleceń znajduje się na [stronie informacje o zaleceniach dotyczących zabezpieczeń](recommendations-reference.md).


### <a name="enhancements-to-sql-data-classification-recommendation"></a>Ulepszenia zalecenia klasyfikacji danych SQL

**Szacowana data zmiany:** Q2 2021

Dane dotyczące rekomendacji **w bazach danych SQL powinny zostać sklasyfikowane** w kontroli zabezpieczeń **Zastosuj klasyfikację danych** w nowej wersji, która jest lepiej wyrównana przy użyciu strategii klasyfikacji danych firmy Microsoft. W efekcie identyfikator zalecenia również zostanie zmieniony (obecnie jest to b0df6f56-862d-4730-8597-38c0fd4ebd59).


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
