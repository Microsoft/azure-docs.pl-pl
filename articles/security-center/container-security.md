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
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 6b57428aeba702dc8cf06ec4ae7984854a94ac7a
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449173"
---
# <a name="container-security-in-security-center"></a>Zabezpieczenia kontenera w Security Center

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

:::image type="content" source="./media/container-security/container-security-tab.png" alt-text="Zasoby dotyczące kontenerów na stronie spisu zasobów Security Center" lightbox="./media/container-security/container-security-tab.png":::

## <a name="vulnerability-management---scanning-container-images"></a>Zarządzanie lukami w zabezpieczeniach — skanowanie obrazów kontenerów

Aby monitorować obrazy w rejestrach kontenerów platformy Azure opartych na Azure Resource Manager, Włącz [usługę Azure Defender dla rejestrów kontenerów](defender-for-container-registries-introduction.md). Security Center skanuje wszystkie obrazy pobrane w ciągu ostatnich 30 dni, wypchnięte do rejestru lub zaimportowane. Zintegrowany skaner jest dostarczany przez wiodące w branży oprogramowanie do skanowania dostawcy, Qualys.

Po znalezieniu problemów — według Qualys lub Security Center — otrzymasz powiadomienie na [pulpicie nawigacyjnym usługi Azure Defender](azure-defender-dashboard.md). W przypadku każdej luki w zabezpieczeniach Security Center zawiera zalecenia z możliwością wykonania akcji, a także klasyfikację ważności oraz wskazówki dotyczące sposobu korygowania problemu. Aby uzyskać szczegółowe informacje na temat zaleceń dotyczących Security Center kontenerów, zobacz [listę referencyjną zaleceń](recommendations-reference.md#recs-containers).

Security Center filtrów i klasyfikuje wyniki ze skanera. Gdy obraz jest w dobrej kondycji, Security Center oznacza go jako taki. Security Center generuje zalecenia dotyczące zabezpieczeń tylko dla obrazów, które mają problemy, które mają zostać rozwiązane. Powiadamiając tylko w przypadku problemów, Security Center zmniejsza możliwości niechcianych alertów informacyjnych.

## <a name="environment-hardening"></a>Ograniczanie funkcjonalności środowiska

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Ciągłe monitorowanie konfiguracji platformy Docker

Azure Security Center identyfikuje niezarządzane kontenery hostowane na maszynach wirtualnych z systemem Linux IaaS lub na innych maszynach systemu Linux z uruchomionymi kontenerami Docker Security Center stale ocenia konfiguracje tych kontenerów. Następnie porównuje je ze [wzorcem usługi Docker dla usługi Internet Security (CIS)](https://www.cisecurity.org/benchmark/docker/).

Security Center obejmuje cały zestaw reguł testu wydajnościowego usługi CIS Docker i generuje alert, jeśli kontenery nie spełniają żadnej z tych kontrolek. Po znalezieniu niepożądanych konfiguracji Security Center generuje zalecenia dotyczące zabezpieczeń. Użyj **strony zalecenia dotyczące** Security Center, aby wyświetlić zalecenia i rozwiązać problemy. Testy porównawcze usług CIS nie są uruchamiane w wystąpieniach zarządzanych przez AKS lub maszynach wirtualnych zarządzanych przez usługi.

Aby uzyskać szczegółowe informacje dotyczące odpowiednich Security Center zaleceń, które mogą pojawić się w przypadku tej funkcji, zobacz [sekcję Container](recommendations-reference.md#recs-containers) w tabeli referencyjnej zalecenia.

Podczas eksplorowania problemów z zabezpieczeniami maszyny wirtualnej Security Center zawiera dodatkowe informacje na temat kontenerów na komputerze. Takie informacje obejmują wersję platformy Docker i liczbę obrazów uruchomionych na hoście. 

Aby monitorować niezarządzane kontenery hostowane na maszynach wirtualnych z systemem IaaS Linux, Włącz opcjonalną [usługę Azure Defender dla serwerów](defender-for-servers-introduction.md).


### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>Ciągłe monitorowanie klastrów Kubernetes
Security Center współpracuje z usługą Azure Kubernetes Service (AKS), zarządzaną usługą aranżacji kontenerów firmy Microsoft na potrzeby opracowywania i wdrażania aplikacji kontenerowych oraz zarządzania nimi.

AKS zapewnia kontrolę zabezpieczeń i wgląd w zabezpieczenia stan klastrów. Security Center używa tych funkcji, aby:
* Stale monitoruj konfigurację klastrów AKS
* Generuj zalecenia dotyczące zabezpieczeń dostosowane do standardów branżowych

Aby uzyskać szczegółowe informacje dotyczące odpowiednich Security Center zaleceń, które mogą pojawić się w przypadku tej funkcji, zobacz [sekcję Container](recommendations-reference.md#recs-containers) w tabeli referencyjnej zalecenia.

###  <a name="workload-protection-best-practices-using-kubernetes-admission-control"></a>Najlepsze rozwiązania w zakresie ochrony obciążeń przy użyciu funkcji Kubernetes Admission Control

Zainstaluj  **dodatek Azure Policy dla Kubernetes** , aby uzyskać pakiet zaleceń dotyczących ochrony obciążeń kontenerów Kubernetes.

Jak wyjaśniono w [tym Azure Policy stronie Kubernetes](../governance/policy/concepts/policy-for-kubernetes.md), dodatek rozszerza element webhook kontrolera Open-Source [strażnik v3](https://github.com/open-policy-agent/gatekeeper)   dla programu [Open Policy Agent](https://www.openpolicyagent.org/). Kubernetes Admission Controls to wtyczki, które wymuszają sposób użycia klastrów. Dodatek, który jest rejestrem sieci Web, w celu Kubernetes kontroli wpływu i umożliwia stosowanie zasad wymuszania na skalę i zabezpieczeń w klastrach w scentralizowany, spójny sposób. 

Po zainstalowaniu dodatku w klastrze AKS każde żądanie do serwera interfejsu API Kubernetes będzie monitorowane względem wstępnie zdefiniowanego zestawu najlepszych rozwiązań przed utrwaleniem w klastrze. Następnie można skonfigurować w celu **wymuszenia** najlepszych rozwiązań i ich upoważnienia do przyszłych obciążeń. 

Na przykład można przystąpić do tego, że kontenery uprzywilejowane nie powinny być tworzone, a wszystkie przyszłe żądania, które należy wykonać, zostaną zablokowane.

Dowiedz się więcej w temacie [Ochrona obciążeń Kubernetes](kubernetes-workload-protections.md).


## <a name="run-time-protection-for-aks-nodes-and-clusters"></a>Ochrona w czasie wykonywania dla węzłów i klastrów AKS

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]



## <a name="next-steps"></a>Następne kroki

W tym przeglądzie przedstawiono podstawowe elementy zabezpieczeń kontenera w Azure Security Center. W przypadku pokrewnego materiału Zobacz:

- [Wprowadzenie do usługi Azure Defender dla Kubernetes](defender-for-kubernetes-introduction.md)
- [Wprowadzenie do usługi Azure Defender dla rejestrów kontenerów](defender-for-container-registries-introduction.md)