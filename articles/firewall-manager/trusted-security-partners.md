---
title: Co to są zaufani partnerzy usługi Azure firewall Manager (wersja zapoznawcza)
description: Informacje o zaufanych partnerach usługi Azure firewall Manager
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: victorh
ms.openlocfilehash: b92242ce9086579d0397f78853402cfc08453f68
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75436769"
---
# <a name="what-are-trusted-security-partners-preview"></a>Co to są zaufani partnerzy w zakresie zabezpieczeń (wersja zapoznawcza)?

> [!IMPORTANT]
> Ten podgląd publiczny nie jest objęty umową dotyczącą poziomu usług i nie należy korzystać z niego w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

*Zaufani partnerzy zabezpieczeń (wersja zapoznawcza)* w Menedżerze zapory platformy Azure umożliwiają używanie znanych, najlepszych w swojej organizacji ofert zabezpieczeń jako usługi (SECaaS) w celu ochrony dostępu do Internetu użytkownikom.

Dzięki szybkiej konfiguracji można zabezpieczyć koncentrator z obsługiwanym partnerem zabezpieczeń oraz skierować i przefiltrować ruch internetowy z sieci wirtualnych (sieci wirtualnych) lub lokalizacji rozgałęzień w obrębie regionu. Odbywa się to za pomocą zautomatyzowanego zarządzania trasami bez konieczności konfigurowania tras zdefiniowanych przez użytkownika i zarządzania nimi (UDR).

Można wdrożyć zabezpieczone centra skonfigurowane z wybranym przez partnera zabezpieczeń w wielu regionach świadczenia usługi Azure, aby uzyskać łączność i bezpieczeństwo użytkowników w dowolnym miejscu na świecie w tych regionach. Dzięki możliwości korzystania z oferty partnera zabezpieczeń na potrzeby ruchu aplikacji internetowych/SaaS oraz zapory platformy Azure dla ruchu prywatnego w zabezpieczonych centrach możesz teraz rozpocząć tworzenie swojej krawędzi zabezpieczeń na platformie Azure, która jest blisko globalnie dystrybuowanych użytkowników i aplikacji.

W przypadku tej wersji zapoznawczej obsługiwane partnerzy zabezpieczeń to **rozwiązania Zscaler** i **iboss**. Obsługiwane regiony to WestCentralUS, NorthCentralUS, zachodnie, WestUS2 i wschód.

![Zaufani partnerzy w zakresie zabezpieczeń](media/trusted-security-partners/trusted-security-partners.png)

## <a name="key-scenarios"></a>Najważniejsze scenariusze

Korzystając z partnerów zabezpieczeń, można filtrować ruch internetowy w następujących scenariuszach:

- Virtual Network (VNet) do Internetu

   Korzystaj z zaawansowanej, opartej na użytkownikach internetowej ochrony obciążeń w chmurze działających na platformie Azure.

- Rozgałęzienie do Internetu

   Korzystaj z łączności z platformą Azure i globalnej dystrybucji, aby łatwo dodać filtrowanie NSaaS innych firm do scenariuszy internetowych. Możesz skompilować globalną sieć tranzytową i krawędź zabezpieczeń przy użyciu wirtualnej sieci WAN platformy Azure.

Obsługiwane są następujące scenariusze:
-   Sieć wirtualna z Internetem za pośrednictwem oferty partnera innej firmy.
-   Rozgałęzianie do Internetu za pośrednictwem oferty partnera innej firmy.
-   Rozgałęzienie do Internetu za pośrednictwem oferty partnera innej firmy, pozostałej części ruchu prywatnego (szprych-to-szprych, szprychy do gałęzi) za pośrednictwem zapory platformy Azure.

Następujący scenariusz nie jest obsługiwany:

- Sieci wirtualnej z Internetem za pośrednictwem oferty partnerskiej nie można łączyć z zaporą platformy Azure dla ruchu prywatnego. Zobacz następujące ograniczenia.

## <a name="current-limitations"></a>Bieżące ograniczenia

- W przypadku połączeń wirtualnych z Internetem nie można mieszać dodawania zapory platformy Azure dla ruchu prywatnego i oferty partnera dla ruchu internetowego. Można wysłać ruch internetowy do zapory platformy Azure lub oferty partnera zabezpieczeń innej firmy w zabezpieczonym koncentratorze wirtualnym, ale nie w obu tych przypadkach. 
- W każdym koncentratorze wirtualnym można wdrożyć maksymalnie jednego partnera zabezpieczeń. Jeśli musisz zmienić dostawcę, musisz usunąć istniejącego partnera i dodać nowy.

## <a name="best-practices-for-internet-traffic-filtering-in-secured-virtual-hubs"></a>Najlepsze rozwiązania dotyczące filtrowania ruchu internetowego w zabezpieczonych centrach wirtualnych

Ruch internetowy zazwyczaj obejmuje ruch internetowy. Obejmuje to również ruch przeznaczony do aplikacji SaaS, takich jak Office 365 (O365) i publiczne usługi PaaS platformy Azure, takie jak Azure Storage, Azure SQL i tak dalej. Poniżej zawarto zalecenia dotyczące obsługi ruchu do tych usług:

### <a name="handling-azure-paas-traffic"></a>Obsługa ruchu usługi Azure PaaS
 
- Użyj zapory platformy Azure do ochrony, jeśli ruch składa się głównie z usługi Azure PaaS, a dostęp do zasobów dla aplikacji może być filtrowany przy użyciu adresów IP, nazw FQDN, tagów usługi lub tagów FQDN.

- Użyj rozwiązania partnerskiego innej firmy w centrach, jeśli ruch składa się z SaaS dostępu do aplikacji lub potrzebujesz filtrowania z obsługą użytkownika (na przykład dla obciążeń infrastruktury pulpitu wirtualnego (VDI) lub potrzebujesz zaawansowanych możliwości filtrowania Internetu.

![Wszystkie scenariusze dla Menedżera zapory platformy Azure](media/trusted-security-partners/all-scenarios.png)

## <a name="handling-office-365-o365-traffic"></a>Obsługa ruchu pakietu Office 365 (O365)

W przypadku scenariuszy rozproszonej globalnie rozgałęzień należy przekierować ruch pakietu Office 365 bezpośrednio w gałęzi przed wysłaniem pozostałego ruchu internetowego do usługi Azure Secure Hub.

W przypadku pakietu Office 365 opóźnienie sieci i wydajność mają kluczowe znaczenie dla pomyślnego środowiska użytkownika. Aby osiągnąć te cele dotyczące optymalnej wydajności i środowiska użytkownika, klienci muszą zaimplementować pakiet Office 365 Direct i lokalnych ucieczki przed rozważeniem routingu reszty ruchu internetowego za pośrednictwem platformy Azure.

[Zasady łączności sieciowej z pakietem office 365](https://docs.microsoft.com/office365/enterprise/office-365-network-connectivity-principles) są wywoływane w przypadku połączeń sieciowych z pakietem Office 365, które mają być kierowane lokalnie z gałęzi użytkownika lub urządzenia przenośnego i bezpośrednio przez Internet do najbliższego punktu sieci firmy Microsoft.

Ponadto połączenia pakietu Office 365 są silnie zaszyfrowane w celu zachowania poufności i używania wydajnych protokołów ze względów wydajnościowych. To sprawia, że nie jest to praktyczne i ma wpływ na te połączenia z tradycyjnymi rozwiązaniami zabezpieczeń na poziomie sieci. Z tego względu zdecydowanie zalecamy, aby klienci wysyłali ruch pakietu Office 365 bezpośrednio z gałęzi przed wysłaniem reszty ruchu przez platformę Azure. Firma Microsoft współpracuje z kilkoma dostawcami rozwiązań SD-WAN, którzy integrują się z platformą Azure i pakietem Office 365 i ułatwiają klientom korzystanie z pakietu Office 365 Direct i lokalnego zagadnień internetowego. Aby uzyskać szczegółowe informacje, zobacz [Jak mogę ustawić moje zasady usługi O365 za pośrednictwem wirtualnej sieci WAN?](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-office365-overview)


## <a name="next-steps"></a>Następne kroki

[Wdróż zaufaną ofertę zabezpieczeń w zabezpieczonym centrum przy użyciu Menedżera zapory platformy Azure](deploy-trusted-security-partner.md).
