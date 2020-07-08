---
title: Zabezpieczenia kontenera w Azure Security Center | Microsoft Docs
description: Dowiedz się więcej o funkcjach zabezpieczeń kontenerów Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2020
ms.author: memildin
ms.openlocfilehash: c01ed6dbbd6e1f7febfb99df11d2ee67cb1e5465
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800611"
---
# <a name="container-security-in-security-center"></a>Zabezpieczenia kontenera w Security Center

Azure Security Center to rozwiązanie natywne platformy Azure służące do zabezpieczania kontenerów. Security Center może chronić następujące typy zasobów kontenera:



|Zasób |Nazwa  |Szczegóły  |
|:---------:|---------|---------|
|![Host kontenerów](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png)|Hosty kontenerów (maszyny wirtualne z systemem Docker)|Usługa Security Center skanuje konfiguracje platformy Docker i zapewnia wgląd w błędy konfiguracji poprzez dostarczenie listy wszystkich ocenionych reguł zakończonych niepowodzeniem. Security Center zawiera wytyczne ułatwiające szybkie rozwiązywanie tych problemów i oszczędność czasu. Usługa Security Center stale ocenia konfiguracje platformy Docker i udostępnia Ci ich najnowszy stan.|
|![Usługa Kubernetes](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png)|Klastry usługi Azure Kubernetes Service (AKS)|Uzyskaj lepszy wgląd w węzły AKS, ruch w chmurze i kontrolę zabezpieczeń Security Center dzięki [opcjonalnemu pakietowi AKS](azure-kubernetes-service-integration.md) dla użytkowników w warstwie Standardowa.|
|![Rejestr kontenerów](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png)|Rejestry Azure Container Registry (ACR)|Zyskaj lepszy wgląd w luki w zabezpieczeniach obrazów w rejestrach ACR opartych na architekturze ARM [Security Center z opcjonalnym pakietem ACR](azure-kubernetes-service-integration.md) dla użytkowników w warstwie Standardowa.|
||||


W tym artykule opisano, jak można używać tych pakietów do ulepszania, monitorowania i konserwacji zabezpieczeń kontenerów i ich aplikacji. Dowiesz się, jak Security Center ułatwiają następujące podstawowe aspekty zabezpieczeń kontenera:

- [Zarządzanie lukami w zabezpieczeniach — skanowanie obrazów kontenerów](#vulnerability-management---scanning-container-images)
- [Ograniczanie funkcjonalności środowiska — ciągłe monitorowanie konfiguracji platformy Docker i klastrów Kubernetes](#environment-hardening)
- [Ochrona w czasie wykonywania — wykrywanie zagrożeń w czasie rzeczywistym](#run-time-protection---real-time-threat-detection)

[![Karta Zabezpieczenia kontenera Azure Security Center](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

Aby uzyskać instrukcje dotyczące korzystania z tych funkcji, zobacz [monitorowanie zabezpieczeń kontenerów](monitor-container-security.md).

## <a name="vulnerability-management---scanning-container-images"></a>Zarządzanie lukami w zabezpieczeniach — skanowanie obrazów kontenerów
Aby monitorować Azure Container Registry oparte na usłudze ARM, upewnij się, że korzystasz z warstwy Standardowa Security Center (zobacz [Cennik](/azure/security-center/security-center-pricing)). Następnie Włącz opcjonalny pakiet rejestrów kontenerów. Po wypchnięciu nowego obrazu Security Center skanuje obraz przy użyciu skanera od wiodącego w branży skanowania dostawcy, Qualys.

Po znalezieniu problemów — według Qualys lub Security Center — otrzymasz powiadomienie na pulpicie nawigacyjnym Security Center. W przypadku każdej luki w zabezpieczeniach Security Center zawiera zalecenia z możliwością wykonania akcji, a także klasyfikację ważności oraz wskazówki dotyczące sposobu korygowania problemu. Aby uzyskać szczegółowe informacje na temat zaleceń dotyczących Security Center kontenerów, zobacz [listę referencyjną zaleceń](recommendations-reference.md#recs-containers).

Security Center filtrów i klasyfikuje wyniki ze skanera. Gdy obraz jest w dobrej kondycji, Security Center oznacza go jako taki. Security Center generuje zalecenia dotyczące zabezpieczeń tylko dla obrazów, które mają problemy, które mają zostać rozwiązane. Powiadamiając tylko w przypadku problemów, Security Center zmniejsza możliwości niechcianych alertów informacyjnych.

## <a name="environment-hardening"></a>Ograniczanie funkcjonalności środowiska

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Ciągłe monitorowanie konfiguracji platformy Docker
Azure Security Center identyfikuje niezarządzane kontenery hostowane na maszynach wirtualnych z systemem Linux IaaS lub na innych maszynach systemu Linux z uruchomionymi kontenerami Docker Security Center stale ocenia konfiguracje tych kontenerów. Następnie porównuje je ze [wzorcem usługi Docker dla usługi Internet Security (CIS)](https://www.cisecurity.org/benchmark/docker/).

Security Center obejmuje cały zestaw reguł testu wydajnościowego usługi CIS Docker i generuje alert, jeśli kontenery nie spełniają żadnej z tych kontrolek. Po znalezieniu niepożądanych konfiguracji Security Center generuje zalecenia dotyczące zabezpieczeń. Na **stronie zalecenia** można przeglądać zalecenia i rozwiązywać problemy. Zobaczysz również zalecenia na karcie **kontenery** , w których są wyświetlane wszystkie maszyny wirtualne wdrożone przy użyciu platformy Docker. 

Aby uzyskać szczegółowe informacje dotyczące odpowiednich Security Center zaleceń, które mogą pojawić się w przypadku tej funkcji, zobacz [sekcję Container](recommendations-reference.md#recs-containers) w tabeli referencyjnej zalecenia.

Podczas eksplorowania problemów z zabezpieczeniami maszyny wirtualnej Security Center zawiera dodatkowe informacje na temat kontenerów na komputerze. Takie informacje obejmują wersję platformy Docker i liczbę obrazów uruchomionych na hoście. 

>[!NOTE]
> Te testy porównawcze usług CIS nie będą uruchamiane w wystąpieniach zarządzanych przez AKS lub maszynach wirtualnych zarządzanych przez program.

### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>Ciągłe monitorowanie klastrów Kubernetes
Security Center współpracuje z usługą Azure Kubernetes Service (AKS), zarządzaną usługą aranżacji kontenerów firmy Microsoft na potrzeby opracowywania i wdrażania aplikacji kontenerowych oraz zarządzania nimi.

AKS zapewnia kontrolę zabezpieczeń i wgląd w zabezpieczenia stan klastrów. Security Center używa tych funkcji, aby:
* Stale monitoruj konfigurację klastrów AKS
* Generuj zalecenia dotyczące zabezpieczeń dostosowane do standardów branżowych

Aby uzyskać szczegółowe informacje dotyczące odpowiednich Security Center zaleceń, które mogą pojawić się w przypadku tej funkcji, zobacz [sekcję Container](recommendations-reference.md#recs-containers) w tabeli referencyjnej zalecenia.

## <a name="run-time-protection---real-time-threat-detection"></a>Ochrona w czasie wykonywania — wykrywanie zagrożeń w czasie rzeczywistym

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]




## <a name="next-steps"></a>Następne kroki

W tym przeglądzie przedstawiono podstawowe elementy zabezpieczeń kontenera w Azure Security Center. Kontynuuj [monitorowanie zabezpieczeń kontenerów](monitor-container-security.md).
> [!div class="nextstepaction"]
> [Monitorowanie zabezpieczeń kontenerów](monitor-container-security.md)