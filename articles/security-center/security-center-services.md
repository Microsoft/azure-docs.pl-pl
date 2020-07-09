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
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/27/2020
ms.author: memildin
ms.openlocfilehash: 4d048bb348b8093db7d3bf92ef0fc93aead35055
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84762296"
---
# <a name="feature-coverage-for-machines"></a>Pokrycie funkcji dla maszyn

W poniższych tabelach przedstawiono funkcje Azure Security Center, które są dostępne dla maszyn wirtualnych i serwerów.

## <a name="supported-features-for-virtual-machines-and-servers"></a>Obsługiwane funkcje dla maszyn wirtualnych i serwerów<a name="vm-server-features"></a>

### <a name="windows-machines"></a>[Maszyny z systemem Windows](#tab/features-windows)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**Funkcja**|**Azure Virtual Machines**|**Zestawy skalowania maszyn wirtualnych platformy Azure**|**Maszyny spoza platformy Azure**|**Cennik**
|[Integracja z programem Microsoft Defender ATP](security-center-wdatp.md)|✔</br>(w obsługiwanych wersjach)|✔</br>(w obsługiwanych wersjach)|✔|Standardowa (Standard)|
|[Analiza behawioralna maszyn wirtualnych (i alerty zabezpieczeń)](threat-protection.md)|✔|✔|✔|Zalecenia (bezpłatnie) </br></br> Alerty zabezpieczeń (standard)|
|[Alerty zabezpieczeń bezplikowych](alerts-reference.md#alerts-windows)|✔|✔|✔|Standardowa (Standard)|
|[Alerty zabezpieczeń oparte na sieci](threat-protection.md#network-layer)|✔|✔|-|Standardowa (Standard)|
|[Dostęp do maszyny wirtualnej just in Time](security-center-just-in-time.md)|✔|-|-|Standardowa (Standard)|
|[Ocena natywnych luk w zabezpieczeniach](built-in-vulnerability-assessment.md)|✔|-|-|Standardowa (Standard)|
|[Monitorowanie integralności plików](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standardowa (Standard)|
|[Funkcje adaptacyjnego sterowania aplikacjami](security-center-adaptive-application.md)|✔|-|✔|Standardowa (Standard)|
|[Mapa sieci](security-center-network-recommendations.md#network-map)|✔|✔|-|Standardowa (Standard)|
|[Adaptacyjne wzmocnienie zabezpieczeń sieci](security-center-adaptive-network-hardening.md)|✔|-|-|Standardowa (Standard)|
|Adaptacyjne kontrolki sieci|✔|✔|-|Standardowa (Standard)|
|[& raporty dotyczące pulpitu nawigacyjnego zgodności z przepisami](security-center-compliance-dashboard.md)|✔|✔|✔|Standardowa (Standard)|
|Zalecenia i ochrona przed zagrożeniami w kontenerach IaaS hostowanych przez platformę Docker|-|-|-|Standardowa (Standard)|
|Brak oceny poprawek systemu operacyjnego|✔|✔|✔|Bezpłatna|
|Ocena niezgodności konfiguracji zabezpieczeń|✔|✔|✔|Bezpłatna|
|[Ocena programu Endpoint Protection](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Bezpłatna|
|Ocena szyfrowania dysku|✔|✔|-|Bezpłatna|
|Ocena luk w zabezpieczeniach innych firm|✔|-|-|Bezpłatna|
|[Ocena zabezpieczeń sieci](security-center-network-recommendations.md)|✔|✔|-|Bezpłatna|


### <a name="linux-machines"></a>[Maszyny z systemem Linux](#tab/features-linux)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**Funkcja**|**Azure Virtual Machines**|**Zestawy skalowania maszyn wirtualnych platformy Azure**|**Maszyny spoza platformy Azure**|**Cennik**
|[Integracja z programem Microsoft Defender ATP](security-center-wdatp.md)|-|-|-|Standardowa (Standard)|
|[Analiza behawioralna maszyn wirtualnych (i alerty zabezpieczeń)](security-center-alerts-iaas.md)|✔</br>(w obsługiwanych wersjach)|✔</br>(w obsługiwanych wersjach)|✔|Zalecenia (bezpłatnie) </br></br> Alerty zabezpieczeń (standard)|
|[Alerty zabezpieczeń bezplikowych](alerts-reference.md#alerts-windows)|-|-|-|Standardowa (Standard)|
|[Alerty zabezpieczeń oparte na sieci](threat-protection.md#network-layer)|✔|✔|-|Standardowa (Standard)|
|[Dostęp do maszyny wirtualnej just in Time](security-center-just-in-time.md)|✔|-|-|Standardowa (Standard)|
|[Ocena natywnych luk w zabezpieczeniach](built-in-vulnerability-assessment.md)|✔|-|-|Standardowa (Standard)|
|[Monitorowanie integralności plików](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standardowa (Standard)|
|[Funkcje adaptacyjnego sterowania aplikacjami](security-center-adaptive-application.md)|✔|-|✔|Standardowa (Standard)|
|[Mapa sieci](security-center-network-recommendations.md#network-map)|✔|✔|-|Standardowa (Standard)|
|[Adaptacyjne wzmocnienie zabezpieczeń sieci](security-center-adaptive-network-hardening.md)|✔|-|-|Standardowa (Standard)|
|Adaptacyjne kontrolki sieci|✔|✔|-|Standardowa (Standard)|
|[& raporty dotyczące pulpitu nawigacyjnego zgodności z przepisami](security-center-compliance-dashboard.md)|✔|✔|✔|Standardowa (Standard)|
|Zalecenia i ochrona przed zagrożeniami w kontenerach IaaS hostowanych przez platformę Docker|✔|✔|✔|Standardowa (Standard)|
|Brak oceny poprawek systemu operacyjnego|✔|✔|✔|Bezpłatna|
|Ocena niezgodności konfiguracji zabezpieczeń|✔|✔|✔|Bezpłatna|
|[Ocena programu Endpoint Protection](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Bezpłatna|
|Ocena szyfrowania dysku|✔|✔|-|Bezpłatna|
|Ocena luk w zabezpieczeniach innych firm|✔|-|-|Bezpłatna|
|[Ocena zabezpieczeń sieci](security-center-network-recommendations.md)|✔|✔|-|Bezpłatna|

--- 


> [!TIP]
>Aby eksperymentować z funkcjami, które są dostępne tylko w warstwie cenowej standardowa, użytkownicy warstwy Bezpłatna mogą zarejestrować się w 30-dniowej wersji próbnej. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="supported-endpoint-protection-solutions"></a>Obsługiwane rozwiązania programu Endpoint Protection<a name="endpoint-supported"></a>

Poniższa tabela zawiera macierz:

 - Czy można użyć Azure Security Center, aby zainstalować każde rozwiązanie.
 - Security Center można odnaleźć rozwiązań programu Endpoint Protection. Jeśli zostanie odnalezione rozwiązanie programu Endpoint Protection z tej listy, Security Center nie zaleca się instalowania go.

Aby uzyskać informacje o tym, kiedy są generowane zalecenia dla każdej z tych ochrony, zobacz [Endpoint Protection oceny i rekomendacji](security-center-endpoint-protection.md).

| Ochrona punktu końcowego| Platformy | Instalacja usługi Security Center | Odnajdywanie usługi Security Center |
|------|------|-----|-----|
| Windows Defender (oprogramowanie firmy Microsoft chroniące przed złośliwym kodem)| Windows Server 2016| Nie, wbudowana w system operacyjny| Tak |
| System Center Endpoint Protection (oprogramowanie firmy Microsoft chroniące przed złośliwym kodem) | Windows Server 2012 R2, 2012, 2008 R2 (Zobacz uwagi poniżej) | Za pomocą rozszerzenia | Tak |
| Trend Micro — głębokie zabezpieczenia | Rodzina systemów Windows Server  | Nie | Tak |
| Symantec — w wersji co najmniej 12.1.1100| Rodzina systemów Windows Server  | Nie | Tak |
| McAfee — w wersji co najmniej 10 | Rodzina systemów Windows Server  | Nie | Tak |
| McAfee — w wersji co najmniej 10 | Rodzina serwerów z systemem Linux  | Nie | Opcję**\*** |
| Sophos V9 +| Rodzina serwerów z systemem Linux  | Nie | Opcję**\***  |

 **\*** Stan pokrycia i dane pomocnicze są obecnie dostępne tylko w obszarze roboczym Log Analytics skojarzonym z chronionymi subskrypcjami. Nie jest to odzwierciedlone w portalu Azure Security Center.

> [!NOTE]
> Wykrywanie programu System Center Endpoint Protection (SCEP) na maszynie wirtualnej z systemem Windows Server 2008 R2 wymaga zainstalowania protokołu SCEP po zainstalowaniu programu PowerShell (wersja 3.0 lub nowsza).


## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak Security Center zbiera dane i log Analytics agenta](security-center-enable-data-collection.md).
- Dowiedz się [, jak Security Center zarządza i zabezpiecza dane](security-center-data-security.md).
- Przejrzyj [platformy, które obsługują Centrum zabezpieczeń](security-center-os-coverage.md).