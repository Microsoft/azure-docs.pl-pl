---
title: Obsługiwane funkcje dostępne w Azure Security Center | Microsoft Docs
description: Ten dokument zawiera listę usług obsługiwanych przez Azure Security Center.
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
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: 40d70c7bd740d8c72ddb72047a3bae3586e1aa1f
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445255"
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

 _ *\** * Stan pokrycia i dane pomocnicze są obecnie dostępne tylko w obszarze roboczym log Analytics skojarzonym z chronionymi subskrypcjami. Nie jest to odzwierciedlone w portalu Azure Security Center.

> [!NOTE]
> Wykrywanie programu System Center Endpoint Protection (SCEP) na maszynie wirtualnej z systemem Windows Server 2008 R2 wymaga zainstalowania protokołu SCEP po zainstalowaniu programu PowerShell (wersja 3.0 lub nowsza).


## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak Security Center zbiera dane przy użyciu agenta log Analytics](security-center-enable-data-collection.md).
- Dowiedz się [, jak Security Center zarządza i zabezpiecza dane](security-center-data-security.md).
- Przejrzyj [platformy, które obsługują Centrum zabezpieczeń](security-center-os-coverage.md).
