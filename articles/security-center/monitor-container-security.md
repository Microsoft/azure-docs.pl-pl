---
title: Monitorowanie zabezpieczeń kontenerów w Azure Security Center
description: Dowiedz się, jak sprawdzić stan zabezpieczeń kontenerów z Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: memildin
ms.openlocfilehash: 330cbc3f28f5e549d5a21417c3d7ccc1e5444769
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77919536"
---
# <a name="monitoring-the-security-of-your-containers"></a>Monitorowanie zabezpieczeń kontenerów

Na tej stronie wyjaśniono, jak korzystać z funkcji zabezpieczeń kontenerów opisanych w artykule dotyczącym [zabezpieczeń kontenera](container-security.md) w naszej sekcji pojęcia.

Azure Security Center obejmuje następujące trzy aspekty zabezpieczeń kontenera:

- **Zarządzanie lukami w zabezpieczeniach** — Jeśli korzystasz ze standardowej warstwy cenowej Security Center (zobacz [Cennik](/azure/security-center/security-center-pricing)), możesz skanować Azure Container Registry oparte na architekturze ARM przy każdym wypchnięciu nowego obrazu. Skaner (obsługiwany przez Qualys) przedstawia wyniki jako zalecenia dotyczące Security Center.
    Aby uzyskać szczegółowe instrukcje, zobacz [skanowanie rejestrów kontenerów pod kątem luk w zabezpieczeniach](#scanning-your-arm-based-container-registries-for-vulnerabilities) poniżej.

- **Ograniczanie funkcjonalności hostów platformy Docker kontenerów** — Security Center umożliwia znalezienie niezarządzanych kontenerów hostowanych na maszynach wirtualnych z systemem Linux lub na innych maszynach systemu Linux, na których działa system Docker, i ciągle porównuje konfiguracje kontenerów z testem porównawczym platformy Docker dla zabezpieczeń Internetu (CIS). Security Center alertów, jeśli kontenery nie spełniają żadnej z formantów. Ciągłe monitorowanie zagrożeń bezpieczeństwa spowodowanych nieprawidłowymi konfiguracjami jest kluczowym składnikiem dowolnego programu zabezpieczającego. 
    Aby uzyskać szczegółowe instrukcje, zobacz artykuł dotyczący [zabezpieczania kontenerów platformy Docker](#hardening-your-containers-docker-hosts) poniżej.

- **Zabezpieczanie klastrów usługi Azure Kubernetes Service** — Security Center zawiera zalecenia dotyczące znajdowania luk w zabezpieczeniach konfiguracji klastrów usługi Azure Kubernetes. Aby uzyskać szczegółowe informacje na temat konkretnych zaleceń, które mogą pojawić się, zobacz [zalecenia dotyczące usługi Kubernetes](recommendations-reference.md#recs-containers).

- **Ochrona środowiska uruchomieniowego** — Jeśli korzystasz ze standardowej warstwy cenowej Security Center, uzyskasz ochronę przed zagrożeniami w czasie rzeczywistym w środowiskach kontenerów. Security Center generuje alerty o podejrzanych działaniach na poziomie klastra hosta i AKS. Aby uzyskać szczegółowe informacje na temat odpowiednich alertów zabezpieczeń, które mogą się pojawić, zobacz sekcję alerty dotyczące klastrów i [alertów dotyczących poziomu](alerts-reference.md#alerts-containerhost) [usług Azure Kubernetes](alerts-reference.md#alerts-akscluster) w tabeli referencyjnej alertów.

## <a name="scanning-your-arm-based-container-registries-for-vulnerabilities"></a>Skanowanie rejestrów kontenerów opartych na usłudze ARM pod kątem luk w zabezpieczeniach 

1. Aby włączyć skanowanie Azure Container Registry obrazów:

    1. Upewnij się, że korzystasz z warstwy cenowej standardowa Azure Security Center.

    1. Na stronie **ustawienia & cen** Włącz opcjonalny pakiet rejestrów kontenerów dla subskrypcji: ![Włączanie pakietu rejestrów kontenerów](media/monitor-container-security/enabling-container-registries-bundle.png)

        Security Center jest teraz gotowa do skanowania obrazów, które są przekazywane do rejestru. 

        >[!NOTE]
        >Ta funkcja jest naliczana za obraz.


1. Aby wyzwolić skanowanie obrazu, wypchnij go do rejestru. 

    Po zakończeniu skanowania (zwykle po około 10 minutach) ustalenia są dostępne w Security Center zaleceniach.
    

1. Aby wyświetlić wyniki, przejdź do strony **zalecenia** . W przypadku znalezienia problemów zobaczysz następujące zalecenie:

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


## <a name="hardening-your-containers-docker-hosts"></a>Ograniczanie funkcjonalności hostów platformy Docker kontenerów

Security Center stale monitoruje konfigurację hostów platformy Docker i generuje zalecenia dotyczące zabezpieczeń, które odzwierciedlają standardy branżowe.

Aby wyświetlić zalecenia dotyczące zabezpieczeń Azure Security Center dla hostów platformy Docker kontenerów:

1. Na pasku nawigacyjnym Security Center Otwórz **& aplikacje obliczeniowe** i wybierz kartę **kontenery** .

1. Opcjonalnie można filtrować listę zasobów kontenera na hostach hostów kontenerów.

    ![Filtr zasobów kontenera](media/monitor-container-security/container-resources-filter.png)

1. Z listy maszyn hosta kontenerów wybierz jeden z nich, aby dokładniej zbadać.

    ![Zalecenia dotyczące hosta kontenera](media/monitor-container-security/container-resources-filtered-to-hosts.png)

    Zostanie otwarta **Strona informacji o hoście kontenera** zawierająca szczegóły dotyczące hosta i listę zaleceń.

1. Z listy rekomendacje wybierz zalecenie, aby dokładniej zbadać.

    ![Lista rekomendacji hosta kontenera](media/monitor-container-security/container-host-rec.png)

1. Opcjonalnie Przeczytaj opis, informacje, zagrożenia i kroki zaradcze. 

1. Wybierz **akcję Wypełnij** w dolnej części strony.

    [![Przycisk podejmowania akcji](media/monitor-container-security/host-security-take-action-button.png)](media/monitor-container-security/host-security-take-action.png#lightbox)

    Log Analytics otwarty z niestandardową operacją gotową do uruchomienia. Domyślne zapytanie niestandardowe zawiera listę wszystkich ocenionych reguł zakończonych niepowodzeniem, a także wskazówki ułatwiające rozwiązanie problemów.

    [![Akcja Log Analytics](media/monitor-container-security/log-analytics-for-action-small.png)](media/monitor-container-security/log-analytics-for-action.png#lightbox)

1. Dostosuj parametry zapytania i wybierz pozycję **Uruchom** , gdy wszystko jest gotowe do Twojego hosta. 



## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób korzystania z funkcji zabezpieczeń kontenerów Security Center. 

Inne powiązane materiały można znaleźć na następujących stronach: 

- [Security Center zalecenia dotyczące kontenerów](recommendations-reference.md#recs-containers)
- [Alerty dla poziomu klastra AKS](alerts-reference.md#alerts-akscluster)
- [Alerty na poziomie hosta kontenera](alerts-reference.md#alerts-containerhost)
