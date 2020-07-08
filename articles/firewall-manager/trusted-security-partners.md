---
title: Co to są dostawcy usługi Azure firewall Manager Security Partners?
description: Informacje o dostawcach partnera zabezpieczeń usługi Azure firewall Manager
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 34da82510f96ef7bde65ceec397b048c941e3234
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85563600"
---
# <a name="what-are-security-partner-providers"></a>Kim są dostawcy partnerów w zakresie zabezpieczeń?

*Dostawcy usług partnerskich zabezpieczeń* w Menedżerze zapory platformy Azure mogą używać znanych, najlepszych w swojej organizacji ofert zabezpieczeń jako usługi (SECaaS) w celu ochrony dostępu do Internetu użytkownikom.

Dzięki szybkiej konfiguracji można zabezpieczyć koncentrator z obsługiwanym partnerem zabezpieczeń oraz skierować i przefiltrować ruch internetowy z sieci wirtualnych (sieci wirtualnych) lub lokalizacji rozgałęzień w obrębie regionu. Można to zrobić za pomocą zautomatyzowanego zarządzania trasami bez konieczności konfigurowania tras zdefiniowanych przez użytkownika (UDR) i zarządzania nimi.

Można wdrożyć zabezpieczone centra skonfigurowane z wybranym przez partnera zabezpieczeń w wielu regionach świadczenia usługi Azure, aby uzyskać łączność i bezpieczeństwo użytkowników w dowolnym miejscu na świecie w tych regionach. Dzięki możliwości korzystania z oferty partnera zabezpieczeń na potrzeby ruchu aplikacji internetowych/SaaS oraz zapory platformy Azure dla ruchu prywatnego w zabezpieczonych centrach możesz teraz rozpocząć tworzenie swojej krawędzi zabezpieczeń na platformie Azure, która jest blisko globalnie dystrybuowanych użytkowników i aplikacji.

Obsługiwane partnerzy zabezpieczeń to **rozwiązania Zscaler**, **Check Point** (wersja zapoznawcza) i **iboss** (wersja zapoznawcza).

![Dostawcy partnerów w zakresie zabezpieczeń](media/trusted-security-partners/trusted-security-partners.png)

## <a name="key-scenarios"></a>Najważniejsze scenariusze

Korzystając z partnerów zabezpieczeń, można filtrować ruch internetowy w następujących scenariuszach:

- Virtual Network (VNet) do Internetu

   Korzystaj z zaawansowanej, opartej na użytkownikach internetowej ochrony obciążeń w chmurze działających na platformie Azure.

- Rozgałęzienie do Internetu

   Korzystaj z łączności z platformą Azure i globalnej dystrybucji, aby łatwo dodać filtrowanie NSaaS innych firm do scenariuszy internetowych. Możesz skompilować globalną sieć tranzytową i krawędź zabezpieczeń przy użyciu wirtualnej sieci WAN platformy Azure.

Obsługiwane są następujące scenariusze:
- Sieć wirtualna/gałąź do Internetu za pośrednictwem dostawcy usługi Security partner oraz inny ruch (szprych do rozgałęzienia, gałąź do szprych) za pośrednictwem zapory platformy Azure.
- Sieć wirtualna/gałąź do Internetu przez dostawcę partnera zabezpieczeń

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

Ponadto połączenia pakietu Office 365 są szyfrowane w celu zachowania poufności i używania wydajnych protokołów ze względów wydajnościowych. To sprawia, że nie jest to praktyczne i ma wpływ na te połączenia z tradycyjnymi rozwiązaniami zabezpieczeń na poziomie sieci. Z tego względu zdecydowanie zalecamy, aby klienci wysyłali ruch pakietu Office 365 bezpośrednio z gałęzi przed wysłaniem reszty ruchu przez platformę Azure. Firma Microsoft współpracuje z kilkoma dostawcami rozwiązań SD-WAN, którzy integrują się z platformą Azure i pakietem Office 365 i ułatwiają klientom korzystanie z pakietu Office 365 Direct i lokalnego zagadnień internetowego. Aby uzyskać szczegółowe informacje, zobacz [Jak mogę ustawić moje zasady usługi O365 za pośrednictwem wirtualnej sieci WAN?](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-office365-overview)

## <a name="next-steps"></a>Następne kroki

[Wdróż ofertę partnera zabezpieczeń w zabezpieczonym centrum przy użyciu Menedżera zapory platformy Azure](deploy-trusted-security-partner.md).
