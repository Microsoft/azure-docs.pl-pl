---
title: Integracja Microsoft Azure z usługą Oracle Cloud Infrastructure | Microsoft Docs
description: Informacje o rozwiązaniach, które integrują aplikacje Oracle działające w Microsoft Azure z bazami danych w infrastrukturze chmury firmy Oracle (OCI).
author: dbakevlar
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 06/01/2020
ms.author: kegorman
ms.reviewer: cynthn
ms.openlocfilehash: c8d0f32821bbb3957d7787b7c3bfd733a880325b
ms.sourcegitcommit: bbd66b477d0c8cb9adf967606a2df97176f6460b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93233920"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure"></a>Rozwiązania aplikacji Oracle integrujące Microsoft Azure i infrastrukturę chmurową Oracle

Firmy Microsoft i Oracle współpracują w celu zapewnienia małych opóźnień i wysokiej przepływności łączności między chmurami, co pozwala korzystać z zalet najlepszych chmur. 

Korzystając z tej łączności między chmurami, można podzielić aplikację wielowarstwową w celu uruchomienia warstwy bazy danych w infrastrukturze chmury firmy Oracle (OCI) oraz aplikacji i innych warstw na Microsoft Azure. Środowisko jest podobne do uruchamiania całego stosu rozwiązań w pojedynczej chmurze. 

Jeśli interesuje Cię uruchamianie oprogramowania pośredniczącego, w tym serwera WebLogic, w infrastrukturze platformy Azure, ale baza danych Oracle działa w ramach OCI, zobacz [WebLogic Server Azure Applications](oracle-weblogic.md).

Jeśli interesuje się wdrażanie rozwiązań Oracle w całości w infrastrukturze platformy Azure, zobacz [obrazy maszyn wirtualnych Oracle i ich wdrożenie na Microsoft Azure](oracle-vm-solutions.md).

## <a name="scenario-overview"></a>Omówienie scenariusza

Łączność między chmurami zapewnia rozwiązanie umożliwiające uruchamianie aplikacji wiodących w branży firmy Oracle oraz własnych aplikacji niestandardowych na maszynach wirtualnych platformy Azure, a jednocześnie korzysta z zalet hostowanych usług bazy danych w systemie OCI. 

Od maja 2020 następujące aplikacje są certyfikowane w konfiguracji obejmującej wiele chmur:

* Pakiet E-Business
* JD Edwards EnterpriseOne
* PeopleSoft
* Aplikacje sieci sprzedaży firmy Oracle
* Zarządzanie finansowe Oracle Hyperion

Poniższy diagram przedstawia ogólny przegląd połączonego rozwiązania. Dla uproszczenia diagram pokazuje tylko warstwę aplikacji i warstwę danych. W zależności od architektury aplikacji rozwiązanie może zawierać dodatkowe warstwy, takie jak klaster serwera WebLogic lub warstwa sieci Web na platformie Azure. Aby uzyskać więcej informacji, zobacz następujące sekcje.

![Omówienie rozwiązania OCI na platformie Azure](media/oracle-oci-overview/crosscloud.png)

## <a name="region-availability"></a>Dostępność regionów 

Łączność między różnymi lokalizacjami w chmurze jest ograniczona do następujących regionów:
* Azure Wschodnie stany USA (Wschodnie) & OCI Ashburn, VA (Wschodnie stany USA)
* Azure Południowe Zjednoczone Królestwo (UKSouth) & OCI Londyn (Południowe Zjednoczone Królestwo)
* Azure Kanada Środkowa (CanadaCentral) & OCI Toronto (Kanada Południowo-Wschodnia)
* Azure Europa Zachodnia (WestEurope) & OCI Amsterdam (Holandia zachodni)
* Azure Japonia Wschodnia (JapanEast) & OCI Tokio (Japonia Wschodnia)
* Azure zachodnie stany USA (Zachodnie) & OCI San Jose (Zachodnie stany USA)

## <a name="networking"></a>Sieć

Klienci korporacyjni często decydują się na zróżnicowanie i wdrażaniu obciążeń dla wielu chmur w różnych sytuacjach firmy i operacyjnych. W celu zróżnicowania klienci korzystają z Internetu, sieci VPN IPSec lub bezpośredniego rozwiązania do łączności dostawcy w chmurze za pośrednictwem sieci lokalnej. Połączenia między sieciami w chmurze mogą wymagać znaczących inwestycji w miarę czasu, pieniędzy, projektowania, zaopatrzenia, instalacji, testowania i operacji. 

Aby rozwiązać te problemy dla klientów, oprogramowanie Oracle i firma Microsoft umożliwiły zintegrowane środowisko z obsługą chmury. Sieci w chmurze są nawiązywane przez połączenie obwodu usługi [ExpressRoute](../../../expressroute/expressroute-introduction.md) w Microsoft Azure z obwodem usługi [FASTCONNECT](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) w systemie OCI. To połączenie jest możliwe, gdy lokalizacja komunikacji równorzędnej usługi Azure ExpressRoute znajduje się w pobliżu lub w tej samej lokalizacji komunikacji równorzędnej co OCI FastConnect. Ta konfiguracja umożliwia bezpieczną i szybką łączność między dwiema chmurami bez konieczności pośredniego dostawcy usług.

Korzystając z ExpressRoute i FastConnect, klienci mogą połączyć się z siecią wirtualną na platformie Azure z wirtualną siecią chmurową w systemie OCI, pod warunkiem, że przestrzeń prywatnych adresów IP nie nakłada się na siebie. Komunikacja równorzędna dwóch sieci umożliwia zasobom w sieci wirtualnej komunikowanie się z zasobem w sieci wirtualnej OCI, tak jakby znajdowały się one w tej samej sieci.

## <a name="network-security"></a>Bezpieczeństwo sieci

Zabezpieczenia sieci to kluczowy składnik dowolnej aplikacji korporacyjnej, który stanowi centralne rozwiązanie w chmurze. Każdy ruch przechodzący przez ExpressRoute i FastConnect przechodzi przez sieć prywatną. Ta konfiguracja umożliwia bezpieczną komunikację między siecią wirtualną platformy Azure a siecią chmurą wirtualną firmy Oracle. Nie musisz podawać publicznego adresu IP do żadnych maszyn wirtualnych na platformie Azure. Podobnie nie jest potrzebna Brama internetowa w OCI. Cała komunikacja odbywa się za pośrednictwem prywatnego adresu IP maszyn.

Ponadto można skonfigurować [listy zabezpieczeń](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm) w sieci i reguł zabezpieczeń w chmurze wirtualnej OCI (dołączone do [sieciowych grup zabezpieczeń](../../../virtual-network/network-security-groups-overview.md)platformy Azure). Za pomocą tych reguł można kontrolować ruch przepływający między maszynami w sieciach wirtualnych. Reguły zabezpieczeń sieci można dodawać na poziomie komputera, na poziomie podsieci, a także na poziomie sieci wirtualnej.

[Aplikacje platformy Azure WebLogic Server](oracle-weblogic.md) dla każdego z nich tworzą sieciową grupę zabezpieczeń, która została wstępnie skonfigurowana do pracy z konfiguracjami portów serwera WebLogic.
 
## <a name="identity"></a>Tożsamość

Tożsamość jest jednym z podstawowych filarów powiązania między firmą Microsoft i bazą danych Oracle. W celu zintegrowania [usługi Oracle Identity Cloud Service](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html) (IDCS) z usługą [Azure Active Directory](../../../active-directory/index.yml) (Azure AD) została wykonana znaczna pracy. Azure AD to usługa zarządzania tożsamościami i dostępem opartymi na chmurze firmy Microsoft. Użytkownicy mogą logować się i uzyskiwać dostęp do różnych zasobów, korzystając z pomocy z usługi Azure AD. Usługa Azure AD umożliwia również zarządzanie użytkownikami i ich uprawnieniami.

Obecnie ta Integracja umożliwia zarządzanie w jednej centralnej lokalizacji, która jest Azure Active Directory. Usługa Azure AD synchronizuje wszelkie zmiany w katalogu z odpowiednim katalogiem Oracle i służy do logowania jednokrotnego do wielochmurowych rozwiązań firmy Oracle.

## <a name="next-steps"></a>Następne kroki

Rozpocznij pracę z [międzychmurową siecią](configure-azure-oci-networking.md) między platformą Azure a OCI. 

Aby uzyskać więcej informacji i oficjalny dokument dotyczący OCI, zobacz dokumentację w [chmurze firmy Oracle](https://docs.cloud.oracle.com/iaas/Content/home.htm) .
