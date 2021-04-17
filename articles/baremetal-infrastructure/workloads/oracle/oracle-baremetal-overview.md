---
title: Co to jest BareMetal Infrastructure for Oracle?
description: Dowiedz się więcej o funkcjach zapewnianych przez infrastrukturę BareMetal dla obciążeń Oracle.
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: efb7506a0fa98ca93390bfe7d3cb1401cdbb77e2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559157"
---
# <a name="what-is-baremetal-infrastructure-for-oracle"></a>Co to jest BareMetal Infrastructure for Oracle?

Ten artykuł zawiera omówienie funkcji dostępnych w infrastrukturze baremetalowej dla obciążeń Oracle.

Platforma BareMetal Infrastructure for Oracle jest oparta na certyfikowanym przez firmę Oracle ujednoliconym systemie obliczeniowym (UCS) i urządzeniu FLexPod. Platforma FlexPod zapewnia wstępnie zweryfikowane technologie magazynowania, sieci i serwera. Oferuje magazyn NFS zapewniający integrację przy użyciu protokołu DirectNFS. Serwery BareMetal są przeznaczone dla Ciebie bez funkcji hypervisor w wystąpieniach bareMetal. 

Te wystąpienia są służące do uruchamiania aplikacji o znaczeniu krytycznym wymagających obciążenia Oracle. Wystąpienia programu BareMetal zapewniają małe opóźnienia (0,35 ms) dla aplikacji działających na maszynach wirtualnych platformy Azure. System BareMetal udostępnia udostępniony dysk magazynu i obsługuje multiemisję wymaganą do komunikacji między węzłami z dedykowaną prywatną siecią wzajemnego połączenia. 

Inne funkcje oprogramowania BareMetal Infrastructure for Oracle obejmują:

- Certyfikowane przez firmę Oracle serwery UCS — UCSB200-M5, UCSB460-M4, UCSB480-M5
- Komunikacja między węzłami (multiemisja) klastrów Oracle Real Application Clusters (RAC) przy użyciu prywatnej wirtualnej sieci LAN (VLAN) — 40 Gb.
- Sprzęt zarządzany przez firmę Microsoft
  - Magazyn nadmiarowy, sieć, zasilanie, zarządzanie
  - Monitorowanie infrastruktury, naprawy i wymiany
  - Zawiera Azure ExpressRoute do kontrolera domeny klienta
  - Zabezpieczone zabezpieczenia fizyczne i sieciowe, mogą uzyskać dostęp do wszystkich usług w chmurze platformy Azure

### <a name="supported-protocols"></a>Obsługiwane protokoły

Następujące protokoły są używane dla różnych punktów instalacji na serwerach BareMetal dla obciążenia Oracle.

- Instalacji systemu operacyjnego — iSCSI
- Dane/dziennik — NFSv3
- kopia zapasowa/archieve — NFSv4

### <a name="licensing"></a>Licencjonowanie

- Używasz własnego lokalnego systemu operacyjnego i licencji Oracle.

### <a name="operating-system"></a>System operacyjny

Serwery są wstępnie załadowane z systemem operacyjnym RHEL 7.6.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o jednostkach SKU dla obciążeń Oracle BareMetal.

> [!div class="nextstepaction"]
> [Jednostki SKU BareMetal dla obciążeń Oracle](oracle-baremetal-skus.md)
