---
title: Jak używać usługi Azure Defender do rejestrów kontenerów
description: Dowiedz się więcej na temat używania usługi Azure Defender do rejestrów kontenerów do skanowania obrazów systemu Linux w rejestrach hostowanych w systemie Linux
author: memildin
ms.author: memildin
ms.date: 10/21/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: a5d66e43485ec66b6297ef11ed382e8fb82b7cb3
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "96014582"
---
# <a name="use-azure-defender-for-container-registries-to-scan-your-images-for-vulnerabilities"></a>Korzystanie z usługi Azure Defender dla rejestrów kontenerów w celu skanowania obrazów pod kątem luk w zabezpieczeniach

Na tej stronie opisano sposób użycia wbudowanego skanera luk w zabezpieczeniach do skanowania obrazów kontenerów przechowywanych w Azure Container Registry opartych na Azure Resource Manager.

Gdy usługa **Azure Defender dla rejestrów kontenerów** jest włączona, wszystkie obrazy wypychane do rejestru zostaną natychmiast przeskanowane. Ponadto zostanie również zeskanowany dowolny obraz pobrany w ciągu ostatnich 30 dni. 

Gdy skaner zgłasza luki w zabezpieczeniach Security Center, Security Center przedstawia wyniki i powiązane informacje jako zalecenia. Ponadto, ustalenia obejmują powiązane informacje, takie jak czynności zaradcze, odpowiednie CVEs, wyniki CVSS i inne. Zidentyfikowane luki w zabezpieczeniach można wyświetlić dla jednej lub kilku subskrypcji lub dla określonego rejestru.

[!INCLUDE [Defender for container registries availability info](../../includes/security-center-availability-defender-for-container-registries.md)]


## <a name="identify-vulnerabilities-in-images-in-azure-container-registries"></a>Identyfikowanie luk w zabezpieczeniach obrazów w rejestrach kontenerów platformy Azure 

Aby włączyć skanowanie obrazów przechowywanych w Azure Container Registry opartych na Azure Resource Managerach:

1. Włącz **usługę Azure Defender dla rejestrów kontenerów** dla Twojej subskrypcji. Security Center jest teraz gotowa do skanowania obrazów w rejestrach.

    >[!NOTE]
    > Ta funkcja jest naliczana za obraz.

1. Skanowanie obrazów jest wyzwalane na każdym wypchnięciu lub zaimportowaniu, a jeśli obraz został usunięty w ciągu ostatnich 30 dni. 

    Po zakończeniu skanowania (zwykle po około 2 minutach, ale może to potrwać do 15 minut), ustalenia są dostępne jako zalecenia Security Center.

1. [Przejrzyj i skoryguj wyniki, jak wyjaśniono poniżej](#view-and-remediate-findings).

## <a name="identify-vulnerabilities-in-images-in-other-container-registries"></a>Identyfikowanie luk w zabezpieczeniach obrazów w innych rejestrach kontenerów 

1. Użyj narzędzi ACR, aby przenieść obrazy do rejestru z usługi Docker Hub lub Microsoft Container Registry.  Po zakończeniu importowania zaimportowane obrazy są skanowane przez usługę Azure Defender. 

    Dowiedz się więcej w temacie [Importowanie obrazów kontenera do rejestru kontenerów](../container-registry/container-registry-import-images.md)

    Po zakończeniu skanowania (zwykle po około 2 minutach, ale może to potrwać do 15 minut), ustalenia są dostępne jako zalecenia Security Center.

1. [Przejrzyj i skoryguj wyniki, jak wyjaśniono poniżej](#view-and-remediate-findings).


## <a name="view-and-remediate-findings"></a>Wyświetlanie i korygowanie wyników

1. Aby wyświetlić wyniki, przejdź do strony **zalecenia** . Jeśli wystąpiły problemy, zobaczysz **luki w zabezpieczeniach Azure Container Registry obrazów, które należy skorygować**

    ![Zalecenie dotyczące korygowania problemów ](media/monitor-container-security/acr-finding.png)

1. Wybierz zalecenie. 

    Zostanie otwarta strona szczegóły rekomendacji z dodatkowymi informacjami. Te informacje obejmują listę rejestrów z zagrożonymi obrazami ("zasoby objęte problemem") oraz czynności zaradcze. 

1. Wybierz konkretny rejestr, aby zobaczyć repozytoria, które mają zagrożone repozytoria.

    ![Wybierz rejestr](media/monitor-container-security/acr-finding-select-registry.png)

    Zostanie otwarta strona szczegóły rejestru z listą odpowiednich repozytoriów.

1. Wybierz konkretne repozytorium, aby zobaczyć w nim repozytoria, które mają podatne na ataki.

    ![Wybieranie repozytorium](media/monitor-container-security/acr-finding-select-repository.png)

    Zostanie otwarta strona szczegóły repozytorium. Wyświetla listę narażonych obrazów wraz z oceną ważności wyników.

1. Wybierz konkretny obraz, aby wyświetlić luki w zabezpieczeniach.

    ![Wybierz obrazy](media/monitor-container-security/acr-finding-select-image.png)

    Zostanie otwarta lista wyników dla wybranego obrazu.

    ![Lista wyników](media/monitor-container-security/acr-findings.png)

1. Aby dowiedzieć się więcej na temat wyszukiwania, wybierz pozycję Znajdowanie. 

    Zostanie otwarte okienko Szczegóły wyników.

    [![Okienko szczegółów wyników](media/monitor-container-security/acr-finding-details-pane.png)](media/monitor-container-security/acr-finding-details-pane.png#lightbox)

    To okienko zawiera szczegółowy opis problemu i linki do zasobów zewnętrznych, aby pomóc w ograniczeniu zagrożeń.

1. Wykonaj kroki opisane w sekcji korygowanie tego okienka.

1. Po wykonaniu czynności wymaganych do skorygowania problemu zabezpieczeń Zastąp obraz w rejestrze:

    1. Wypchnij zaktualizowany obraz. Spowoduje to wyzwolenie skanowania. 
    
    1. Zapoznaj się ze stroną zalecenia dotyczącej "luk w zabezpieczeniach Azure Container Registry obrazów należy skorygować". 
    
        Jeśli zalecenie nadal pojawia się, a obraz, który został obsłużony, nadal pojawia się na liście zagrożonych obrazów, należy ponownie sprawdzić kroki zaradcze.

    1. Jeśli masz pewność, że zaktualizowany obraz został wypchnięci, przeskanowany i nie jest już wyświetlany w zaleceniu, Usuń z rejestru "stary" obraz z zagrożeniem.


## <a name="disable-specific-findings-preview"></a>Wyłącz określone wyniki (wersja zapoznawcza)

> [!NOTE]
> [!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]

Jeśli istnieje organizacja, która ma być ignorowana, zamiast skorygować ją, możesz ją wyłączyć. Wyłączone wyniki nie wpływają na swój Bezpieczny wynik ani nie generują niechcianych szumów.

Gdy wyszukiwanie jest zgodne z kryteriami zdefiniowanymi w regułach wyłączania, nie będą wyświetlane na liście wyników. Typowe scenariusze obejmują:

- Wyłącz wnioski o ważności poniżej średniej
- Wyłącz wnioski, które nie są zgodne z poprawkami
- Wyłącz wnioski z wynikiem CVSS poniżej 6,5
- Wyłącz wnioski z określonym tekstem w kontroli zabezpieczeń lub w kategorii (na przykład "RedHat", "CentOS Security Update for sudo")

> [!IMPORTANT]
> Aby utworzyć regułę, musisz mieć uprawnienia do edytowania zasad w Azure Policy.
>
> Dowiedz się więcej na temat [uprawnień kontroli RBAC platformy Azure w Azure Policy](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy).

Można użyć dowolnego z następujących kryteriów: 

- Znajdowanie identyfikatora 
- Kategoria
- Sprawdzanie zabezpieczeń 
- Wyniki z CVSS v3
- Ważność 
- Stan z poprawkami 

Aby utworzyć regułę:

1. Na stronie Szczegóły rekomendacji dotyczącej **luk w zabezpieczeniach w Azure Container Registry obrazów należy skorygować** pozycję **Wyłącz regułę**.
1. Wybierz odpowiedni zakres.
1. Zdefiniuj kryteria.
1. Wybierz pozycję **Zastosuj regułę**.

    :::image type="content" source="./media/defender-for-container-registries-usage/new-disable-rule-for-registry-finding.png" alt-text="Tworzenie reguły wyłączania dla ustaleń o usłudze VA w rejestrze":::

1. Aby wyświetlić, przesłonić lub usunąć regułę: 
    1. Wybierz pozycję **Wyłącz regułę**.
    1. Z listy zakres subskrypcje z aktywnymi regułami są wyświetlane jako **zastosowana reguła**.
        :::image type="content" source="./media/remediate-vulnerability-findings-vm/modify-rule.png" alt-text="Modyfikowanie lub usuwanie istniejącej reguły":::
    1. Aby wyświetlić lub usunąć regułę, wybierz menu wielokropka ("...").


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o usłudze Azure Defender](azure-defender.md)