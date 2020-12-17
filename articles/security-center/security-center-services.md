---
title: Funkcje Azure Security Center w zależności od systemu operacyjnego, typu komputera i chmury
description: Informacje o tym, które funkcje Azure Security Center są dostępne w zależności od systemu operacyjnego, typu i wdrożenia w chmurze.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 870ebc8d-1fad-435b-9bf9-c477f472ab17
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/17/2020
ms.author: memildin
ms.openlocfilehash: b3671a57b8371c47b1690b4bf6be008d695d1ca0
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97654748"
---
# <a name="feature-coverage-for-machines"></a>Pokrycie funkcji dla maszyn

Na dwóch poniższych kartach przedstawiono funkcje Azure Security Center, które są dostępne dla maszyn wirtualnych i serwerów z systemem Windows i Linux.

## <a name="supported-features-for-virtual-machines-and-servers"></a>Obsługiwane funkcje dla maszyn wirtualnych i serwerów <a name="vm-server-features"></a>

### <a name="windows-machines"></a>[**Maszyny z systemem Windows**](#tab/features-windows)

|**Funkcja**|**Azure Virtual Machines**|**Zestawy skalowania maszyn wirtualnych platformy Azure**|**Maszyny z obsługą usługi Azure Arc**|**Usługa Azure Defender jest wymagana**
|----|:----:|:----:|:----:|:----:|
|[Integracja usługi Microsoft Defender for Endpoint](security-center-wdatp.md)|✔</br>(w obsługiwanych wersjach)|✔</br>(w obsługiwanych wersjach)|✔|Tak|
|[Analiza behawioralna maszyn wirtualnych (i alerty zabezpieczeń)](alerts-reference.md)|✔|✔|✔|Tak|
|[Alerty zabezpieczeń bezplikowych](alerts-reference.md#alerts-windows)|✔|✔|✔|Tak|
|[Alerty zabezpieczeń oparte na sieci](other-threat-protections.md#network-layer)|✔|✔|-|Tak|
|[Dostęp just in time do maszyny wirtualnej](security-center-just-in-time.md)|✔|-|-|Tak|
|[Ocena natywnych luk w zabezpieczeniach](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|✔|Tak|
|[Monitorowanie integralności plików](security-center-file-integrity-monitoring.md)|✔|✔|✔|Tak|
|[Funkcje adaptacyjnego sterowania aplikacjami](security-center-adaptive-application.md)|✔|-|✔|Tak|
|[Mapa sieci](security-center-network-recommendations.md#network-map)|✔|✔|-|Tak|
|[Adaptacyjne wzmocnienie zabezpieczeń sieci](security-center-adaptive-network-hardening.md)|✔|-|-|Tak|
|[& raporty dotyczące pulpitu nawigacyjnego zgodności z przepisami](security-center-compliance-dashboard.md)|✔|✔|✔|Tak|
|Zalecenia i ochrona przed zagrożeniami w kontenerach IaaS hostowanych przez platformę Docker|-|-|-|Tak|
|Brak oceny poprawek systemu operacyjnego|✔|✔|✔|Azure: nie<br><br>Łuk włączony: tak|
|Ocena niezgodności konfiguracji zabezpieczeń|✔|✔|✔|Azure: nie<br><br>Łuk włączony: tak|
|[Ocena programu Endpoint Protection](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Azure: nie<br><br>Łuk włączony: tak|
|Ocena szyfrowania dysku|✔</br>(dla [obsługiwanych scenariuszy](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios))|✔|-|Nie|
|Ocena luk w zabezpieczeniach innych firm|✔|-|✔|Nie|
|[Ocena zabezpieczeń sieci](security-center-network-recommendations.md)|✔|✔|-|Nie|


### <a name="linux-machines"></a>[**Maszyny z systemem Linux**](#tab/features-linux)

|**Funkcja**|**Azure Virtual Machines**|**Zestawy skalowania maszyn wirtualnych platformy Azure**|**Maszyny z obsługą usługi Azure Arc**|**Usługa Azure Defender jest wymagana**
|----|:----:|:----:|:----:|:----:|
|[Integracja usługi Microsoft Defender for Endpoint](security-center-wdatp.md)|-|-|-|Tak|
|[Analiza behawioralna maszyn wirtualnych (i alerty zabezpieczeń)](./azure-defender.md)|✔</br>(w obsługiwanych wersjach)|✔</br>(w obsługiwanych wersjach)|✔|Tak|
|[Alerty zabezpieczeń bezplikowych](alerts-reference.md#alerts-windows)|-|-|-|Tak|
|[Alerty zabezpieczeń oparte na sieci](other-threat-protections.md#network-layer)|✔|✔|-|Tak|
|[Dostęp just in time do maszyny wirtualnej](security-center-just-in-time.md)|✔|-|-|Tak|
|[Ocena natywnych luk w zabezpieczeniach](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|✔|Tak|
|[Monitorowanie integralności plików](security-center-file-integrity-monitoring.md)|✔|✔|✔|Tak|
|[Funkcje adaptacyjnego sterowania aplikacjami](security-center-adaptive-application.md)|✔|-|✔|Tak|
|[Mapa sieci](security-center-network-recommendations.md#network-map)|✔|✔|-|Tak|
|[Adaptacyjne wzmocnienie zabezpieczeń sieci](security-center-adaptive-network-hardening.md)|✔|-|-|Tak|
|[& raporty dotyczące pulpitu nawigacyjnego zgodności z przepisami](security-center-compliance-dashboard.md)|✔|✔|✔|Tak|
|Zalecenia i ochrona przed zagrożeniami w kontenerach IaaS hostowanych przez platformę Docker|✔|✔|✔|Tak|
|Brak oceny poprawek systemu operacyjnego|✔|✔|✔|Azure: nie<br><br>Łuk włączony: tak|
|Ocena niezgodności konfiguracji zabezpieczeń|✔|✔|✔|Azure: nie<br><br>Łuk włączony: tak|
|[Ocena programu Endpoint Protection](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Nie|
|Ocena szyfrowania dysku|✔</br>(dla [obsługiwanych scenariuszy](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios))|✔|-|Nie|
|Ocena luk w zabezpieczeniach innych firm|✔|-|✔|Nie|
|[Ocena zabezpieczeń sieci](security-center-network-recommendations.md)|✔|✔|-|Nie|

--- 


> [!TIP]
>Aby eksperymentować z funkcjami, które są dostępne tylko w usłudze Azure Defender, możesz zarejestrować się w 30-dniowej wersji próbnej. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="supported-endpoint-protection-solutions"></a>Obsługiwane rozwiązania programu Endpoint Protection <a name="endpoint-supported"></a>

Poniższa tabela zawiera macierz:

 - Czy można użyć Azure Security Center, aby zainstalować każde rozwiązanie.
 - Security Center można odnaleźć rozwiązań programu Endpoint Protection. Jeśli zostanie odnalezione rozwiązanie programu Endpoint Protection z tej listy, Security Center nie zaleca się instalowania go.

Aby uzyskać informacje o tym, kiedy są generowane zalecenia dla każdej z tych ochrony, zobacz [Endpoint Protection oceny i rekomendacji](security-center-endpoint-protection.md).

| Ochrona punktu końcowego| Platformy | Instalacja usługi Security Center | Odnajdywanie usługi Security Center |
|------|------|-----|-----|
| Program antywirusowy Microsoft Defender| System Windows Server 2016 lub nowszy| Nie, wbudowana w system operacyjny| Tak |
| System Center Endpoint Protection (oprogramowanie firmy Microsoft chroniące przed złośliwym kodem) | Windows Server 2012 R2, 2012, 2008 R2 (Zobacz uwagi poniżej) | Za pomocą rozszerzenia | Tak |
| Trend Micro — głębokie zabezpieczenia | Rodzina systemów Windows Server  | Nie | Tak |
| Symantec — w wersji co najmniej 12.1.1100| Rodzina systemów Windows Server  | Nie | Tak |
| McAfee — w wersji co najmniej 10 | Rodzina systemów Windows Server  | Nie | Tak |
| McAfee — w wersji co najmniej 10 | Rodzina serwerów z systemem Linux  | Nie | Tak * *\** _ |
| Sophos V9 +| Rodzina serwerów z systemem Linux  | Nie | Opcję  _*\**_  |

 _*\**_ Stan pokrycia i dane pomocnicze są obecnie dostępne tylko w obszarze roboczym Log Analytics skojarzonym z chronionymi subskrypcjami. Nie jest to odzwierciedlone w portalu Azure Security Center.

> [!NOTE]
> Wykrywanie programu System Center Endpoint Protection (SCEP) na maszynie wirtualnej z systemem Windows Server 2008 R2 wymaga zainstalowania protokołu SCEP po zainstalowaniu programu PowerShell (wersja 3.0 lub nowsza).



## <a name="feature-support-in-government-clouds"></a>Obsługa funkcji w chmurach dla instytucji rządowych

| Usługa/funkcja | US Gov | Chiny gov |
|------|:----:|:----:|
|[Dostęp do maszyny wirtualnej just in Time](security-center-just-in-time.md) (1)|✔|✔|
|[Monitorowanie integralności plików](security-center-file-integrity-monitoring.md) (1)|✔|-|
|[Adaptacyjne kontrolki aplikacji](security-center-adaptive-application.md) (1)|✔|✔|
|[Adaptacyjne ograniczanie sieci](security-center-adaptive-network-hardening.md) (1)|-|-|
|[Ograniczanie funkcjonalności hosta platformy Docker](harden-docker-hosts.md) (1)|✔|✔|
|[Zintegrowana Ocena luk w zabezpieczeniach dla maszyn](deploy-vulnerability-assessment-vm.md) (1)|-|-|
|[Microsoft Defender dla punktu końcowego](harden-docker-hosts.md) (1)|✔|-|
|[Połącz konto AWS](quickstart-onboard-aws.md) (1)|-|-|
|[Połącz konto GCP](quickstart-onboard-gcp.md) (1)|-|-|
|[Eksport ciągły](continuous-export.md)|✔|✔|
|[Automatyzacja przepływu pracy](workflow-automation.md)|✔|✔|
|[Reguły zwolnienia z rekomendacji](exempt-resource.md)|-|-|
|[Reguły pomijania alertów](alerts-suppression-rules.md)|✔|✔|
|[Powiadomienia e-mail dotyczące alertów zabezpieczeń](security-center-provide-security-contact-details.md)|✔|✔|
|[Spis zasobów](asset-inventory.md)|-|-|
|[Usługa Azure Defender dla usługi App Service](defender-for-app-service-introduction.md)|-|-|
|[Usługa Azure Defender dla usługi Storage](defender-for-storage-introduction.md)|✔|-|
|[Azure Defender for SQL](defender-for-sql-introduction.md)|✔|✔ (2)|
|[Usługa Azure Defender dla usługi Key Vault](defender-for-key-vault-introduction.md)|-|-|
|[Usługa Azure Defender dla usługi Resource Manager](defender-for-resource-manager-introduction.md)|-|-|
|[Usługa Azure Defender dla usługi DNS](defender-for-dns-introduction.md)|-|-|
|[Usługa Azure Defender dla platformy Kubernetes](defender-for-kubernetes-introduction.md)|✔|✔|
|[Usługa Azure Defender dla rejestrów kontenerów](defender-for-container-registries-introduction.md)|✔ (2)|-|
|||

(1) wymaga *usługi Azure Defender dla serwerów**

(2) częściowe


## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak Security Center zbiera dane przy użyciu agenta log Analytics](security-center-enable-data-collection.md).
- Dowiedz się [, jak Security Center zarządza i zabezpiecza dane](security-center-data-security.md).
- Przejrzyj [platformy, które obsługują Centrum zabezpieczeń](security-center-os-coverage.md).