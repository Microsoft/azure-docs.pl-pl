---
title: Zabezpieczenia kontenera w Azure Security Center | Microsoft Docs
description: Dowiedz się więcej o funkcjach zabezpieczeń kontenerów Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/07/2021
ms.author: memildin
ms.openlocfilehash: 3b5204f1d390388c2dc9a10ac2ca0234f6b0499b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102101345"
---
# <a name="container-security-in-security-center"></a>Zabezpieczenia kontenerów w usłudze Security Center

Azure Security Center to rozwiązanie natywne platformy Azure służące do zabezpieczania kontenerów.

Security Center może chronić następujące typy zasobów kontenera:

| Typ zasobu | Zabezpieczenia oferowane przez Security Center |
|:--------------------:|-----------|
| ![Usługa Kubernetes](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png)<br>**Klastry usługi Azure Kubernetes Service (AKS)** | — Ciągła Ocena konfiguracji klastrów AKS w celu zapewnienia wglądu w błędy konfiguracji oraz wskazówek ułatwiających rozwiązywanie problemów.<br>[Dowiedz się więcej o ograniczaniu funkcjonalności środowiska poprzez zalecenia dotyczące zabezpieczeń](#environment-hardening).<br><br>-Ochrona przed zagrożeniami dla klastrów AKS i węzłów systemu Linux. Alerty dotyczące podejrzanych działań są udostępniane przez opcjonalną  [usługę Azure Defender for Kubernetes](defender-for-kubernetes-introduction.md).<br>[Dowiedz się więcej o ochronie w czasie wykonywania dla węzłów i klastrów AKS](#run-time-protection-for-aks-nodes-and-clusters).|
| ![Host kontenerów](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png)<br>**Hosty kontenerów**<br>(Maszyny wirtualne z systemem Docker) | — Ciągła Ocena konfiguracji platformy Docker w celu zapewnienia wglądu w błędy konfiguracji oraz wskazówki pomagające w rozwiązaniu wszystkich odnalezionych problemów z opcjonalną  [usługą Azure Defender dla serwerów](defender-for-servers-introduction.md).<br>[Dowiedz się więcej o ograniczaniu funkcjonalności środowiska poprzez zalecenia dotyczące zabezpieczeń](#environment-hardening).|
| ![Rejestr kontenerów](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png)<br>**Rejestry Azure Container Registry (ACR)** | — Oceny luk w zabezpieczeniach i narzędziach do zarządzania dla obrazów w rejestrach ACR opartych na Azure Resource Manager z opcjonalnym [usługą Azure Defender dla rejestrów kontenerów](defender-for-container-registries-introduction.md).<br>[Dowiedz się więcej o skanowaniu obrazów kontenerów pod kątem luk w zabezpieczeniach](#vulnerability-management---scanning-container-images). |
|||

W tym artykule opisano, jak można użyć Security Center wraz z opcjonalnymi planami usługi Azure Defender dla rejestrów kontenerów, serwerów i Kubernetes, aby usprawnić, monitorować i obsługiwać zabezpieczenia kontenerów oraz ich aplikacji.

Dowiesz się, jak Security Center ułatwiają następujące podstawowe aspekty zabezpieczeń kontenera:

- [Zarządzanie lukami w zabezpieczeniach — skanowanie obrazów kontenerów](#vulnerability-management---scanning-container-images)
- [Ograniczanie funkcjonalności środowiska](#environment-hardening)
- [Ochrona w czasie wykonywania dla węzłów i klastrów AKS](#run-time-protection-for-aks-nodes-and-clusters)

Poniższy zrzut ekranu przedstawia stronę spisu zasobów i różne typy zasobów kontenera chronione przez Security Center.

:::image type="content" source="./media/container-security/inventory-container-resources.png" alt-text="Zasoby dotyczące kontenerów na stronie spisu zasobów Security Center" lightbox="./media/container-security/inventory-container-resources.png":::

## <a name="vulnerability-management---scanning-container-images"></a>Zarządzanie lukami w zabezpieczeniach — skanowanie obrazów kontenerów

Aby monitorować obrazy w rejestrach kontenerów platformy Azure opartych na Azure Resource Manager, Włącz [usługę Azure Defender dla rejestrów kontenerów](defender-for-container-registries-introduction.md). Security Center skanuje wszystkie obrazy pobrane w ciągu ostatnich 30 dni, wypchnięte do rejestru lub zaimportowane. Zintegrowany skaner jest dostarczany przez wiodące w branży oprogramowanie do skanowania dostawcy, Qualys.

Po znalezieniu problemów — według Qualys lub Security Center — otrzymasz powiadomienie na [pulpicie nawigacyjnym usługi Azure Defender](azure-defender-dashboard.md). W przypadku każdej luki w zabezpieczeniach Security Center zawiera zalecenia z możliwością wykonania akcji, a także klasyfikację ważności oraz wskazówki dotyczące sposobu korygowania problemu. Aby uzyskać szczegółowe informacje na temat zaleceń dotyczących Security Center kontenerów, zobacz [listę referencyjną zaleceń](recommendations-reference.md#recs-compute).

Security Center filtrów i klasyfikuje wyniki ze skanera. Gdy obraz jest w dobrej kondycji, Security Center oznacza go jako taki. Security Center generuje zalecenia dotyczące zabezpieczeń tylko dla obrazów, które mają problemy, które mają zostać rozwiązane. Powiadamiając tylko w przypadku problemów, Security Center zmniejsza możliwości niechcianych alertów informacyjnych.

## <a name="environment-hardening"></a>Ograniczanie funkcjonalności środowiska

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Ciągłe monitorowanie konfiguracji platformy Docker

Azure Security Center identyfikuje niezarządzane kontenery hostowane na maszynach wirtualnych z systemem Linux IaaS lub na innych maszynach systemu Linux z uruchomionymi kontenerami Docker Security Center stale ocenia konfiguracje tych kontenerów. Następnie porównuje je ze [wzorcem usługi Docker dla usługi Internet Security (CIS)](https://www.cisecurity.org/benchmark/docker/).

Security Center obejmuje cały zestaw reguł testu wydajnościowego usługi CIS Docker i generuje alert, jeśli kontenery nie spełniają żadnej z tych kontrolek. Po znalezieniu niepożądanych konfiguracji Security Center generuje zalecenia dotyczące zabezpieczeń. Użyj **strony zalecenia dotyczące** Security Center, aby wyświetlić zalecenia i rozwiązać problemy. Testy porównawcze usług CIS nie są uruchamiane w wystąpieniach zarządzanych przez AKS lub maszynach wirtualnych zarządzanych przez usługi.

Aby uzyskać szczegółowe informacje dotyczące odpowiednich Security Center zaleceń, które mogą pojawić się w przypadku tej funkcji, zobacz [sekcję obliczenia](recommendations-reference.md#recs-compute) w tabeli referencyjnej rekomendacji.

Podczas eksplorowania problemów z zabezpieczeniami maszyny wirtualnej Security Center zawiera dodatkowe informacje na temat kontenerów na komputerze. Takie informacje obejmują wersję platformy Docker i liczbę obrazów uruchomionych na hoście. 

Aby monitorować niezarządzane kontenery hostowane na maszynach wirtualnych z systemem IaaS Linux, Włącz opcjonalną [usługę Azure Defender dla serwerów](defender-for-servers-introduction.md).


### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>Ciągłe monitorowanie klastrów Kubernetes
Security Center współpracuje z usługą Azure Kubernetes Service (AKS), zarządzaną usługą aranżacji kontenerów firmy Microsoft na potrzeby opracowywania i wdrażania aplikacji kontenerowych oraz zarządzania nimi.

AKS zapewnia kontrolę zabezpieczeń i wgląd w zabezpieczenia stan klastrów. Security Center używa tych funkcji, aby stale monitorować konfigurację klastrów AKS i generować zalecenia dotyczące zabezpieczeń dostosowane do standardów branżowych.

Jest to diagram wysokiego poziomu interakcji między Azure Security Center, usługą Azure Kubernetes i Azure Policy:

:::image type="content" source="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png" alt-text="Architektura wysokiego poziomu interakcji między Azure Security Center, usługą Azure Kubernetes i Azure Policy" lightbox="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png":::

Można zobaczyć, że elementy otrzymane i przeanalizowane przez Security Center obejmują:

- Inspekcja dzienników z serwera interfejsu API
- nieprzetworzone zdarzenia zabezpieczeń z agenta Log Analytics

    > [!NOTE]
    > Obecnie nie obsługujemy instalacji agenta Log Analytics w klastrach usługi Azure Kubernetes, które działają w ramach zestawów skalowania maszyn wirtualnych.

- Informacje o konfiguracji klastra z klastra AKS
- Konfiguracja obciążenia z Azure Policy (za pośrednictwem **Azure Policy dodatku dla Kubernetes**)

Aby uzyskać szczegółowe informacje dotyczące odpowiednich Security Center zaleceń, które mogą pojawić się w przypadku tej funkcji, zobacz [sekcję obliczenia](recommendations-reference.md#recs-compute) w tabeli referencyjnej rekomendacji.


###  <a name="workload-protection-best-practices-using-kubernetes-admission-control"></a>Najlepsze rozwiązania w zakresie ochrony obciążeń przy użyciu funkcji Kubernetes Admission Control

Aby uzyskać pakiet zaleceń dotyczących ochrony obciążeń kontenerów usługi Kubernetes, zainstaluj  **dodatek Azure Policy dla Kubernetes**. Ten dodatek można również wdrożyć w sposób opisany w temacie [Włączanie obsługi autoaprowizacji agenta log Analytics i rozszerzeń](security-center-enable-data-collection.md#auto-provision-mma). Gdy funkcja autoaprowizacji dla dodatku jest ustawiona na wartość "on", rozszerzenie jest domyślnie włączone we wszystkich istniejących i przyszłych klastrach (które spełniają wymagania instalacji dodatku).

Jak wyjaśniono w [tym Azure Policy stronie Kubernetes](../governance/policy/concepts/policy-for-kubernetes.md), dodatek rozszerza element webhook kontrolera Open-Source [strażnik v3](https://github.com/open-policy-agent/gatekeeper)   dla programu [Open Policy Agent](https://www.openpolicyagent.org/). Kubernetes Admission Controls to wtyczki, które wymuszają sposób użycia klastrów. Dodatek, który jest rejestrem sieci Web, w celu Kubernetes kontroli wpływu i umożliwia stosowanie zasad wymuszania na skalę i zabezpieczeń w klastrach w scentralizowany, spójny sposób. 

Wraz z dodatkiem w klastrze AKS każde żądanie do serwera interfejsu API Kubernetes będzie monitorowane względem wstępnie zdefiniowanego zestawu najlepszych rozwiązań przed utrwaleniem w klastrze. Następnie można skonfigurować w celu **wymuszenia** najlepszych rozwiązań i ich upoważnienia do przyszłych obciążeń. 

Na przykład można przystąpić do tego, że kontenery uprzywilejowane nie powinny być tworzone, a wszystkie przyszłe żądania, które należy wykonać, zostaną zablokowane.

Dowiedz się więcej w temacie [Ochrona obciążeń Kubernetes](kubernetes-workload-protections.md).


## <a name="run-time-protection-for-aks-nodes-and-clusters"></a>Ochrona w czasie wykonywania dla węzłów i klastrów AKS

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]



## <a name="next-steps"></a>Następne kroki

W tym przeglądzie przedstawiono podstawowe elementy zabezpieczeń kontenera w Azure Security Center. W przypadku pokrewnego materiału Zobacz:

- [Wprowadzenie do usługi Azure Defender dla Kubernetes](defender-for-kubernetes-introduction.md)
- [Wprowadzenie do usługi Azure Defender dla rejestrów kontenerów](defender-for-container-registries-introduction.md)