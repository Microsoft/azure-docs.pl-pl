---
title: Azure SQL Database funkcje zabezpieczeń
description: Ten artykuł zawiera ogólny opis sposobu, w jaki Azure SQL Database chroni dane klientów na platformie Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/09/2020
ms.author: terrylan
ms.openlocfilehash: e0e7089e7c674f324c2c3d293661c518b41731b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84021861"
---
# <a name="azure-sql-database-security-features"></a>Azure SQL Database funkcje zabezpieczeń    
Azure SQL Database zapewnia usługi relacyjnej bazy danych na platformie Azure. W celu ochrony danych klienta i zapewnienia mocnych funkcji zabezpieczeń, których klienci oczekują od usługi relacyjnej bazy danych, SQL Database mają własne zestawy funkcji zabezpieczeń. Te możliwości są kompilowane na kontrolkach, które są dziedziczone z platformy Azure.

## <a name="security-capabilities"></a>Możliwości zabezpieczeń

### <a name="usage-of-the-tds-protocol"></a>Użycie protokołu TDS
Azure SQL Database obsługuje tylko protokół strumienia danych tabelarycznych (TDS), który wymaga dostępu do bazy danych tylko przez domyślny port TCP/1433.

### <a name="azure-sql-database-firewall"></a>Zapora Azure SQL Database
Aby chronić dane klientów, Azure SQL Database obejmuje funkcje zapory, które domyślnie uniemożliwiają cały dostęp do SQL Database, jak pokazano poniżej.

![Zapora Azure SQL Database](./media/infrastructure-sql/sql-database-firewall.png)

Zapora bramy może ograniczyć adresy, co pozwala klientom szczegółowo kontrolować Określanie zakresów akceptowalnych adresów IP. Zapora przyznaje dostęp na podstawie źródłowego adresu IP każdego żądania.

Klienci mogą uzyskać konfigurację zapory przy użyciu portalu zarządzania lub programowo przy użyciu interfejsu API REST zarządzania Azure SQL Database. Zapora Azure SQL Database Gateway domyślnie uniemożliwia wszystkim klientom dostęp do Azure SQL Database. Klienci muszą skonfigurować dostęp przy użyciu list kontroli dostępu (ACL), aby zezwalać na połączenia Azure SQL Database przez źródłowe i docelowe adresy internetowe, protokoły i numery portów.

### <a name="dosguard"></a>DoSGuard
Atakom typu „odmowa usługi” (DoS) przeciwdziała usługa bramy usługi SQL Database o nazwie DoSGuard. Usługa DoSGuard aktywnie śledzi nieudane logowania z adresów IP. W przypadku wystąpienia wielu nieudanych logowań z określonego adresu IP w danym okresie adres IP nie ma dostępu do dowolnych zasobów usługi w ramach wstępnie zdefiniowanego okresu.

Ponadto Brama Azure SQL Database wykonuje następujące czynności:

- Negocjacje możliwości bezpiecznego kanału w celu zaimplementowania zaszyfrowanego połączenia protokołu TDS w trybie FIPS 140-2 podczas łączenia się z serwerami baz danych.
- Stanowa Inspekcja pakietów TDS podczas akceptowania połączeń od klientów. Brama weryfikuje informacje o połączeniu i przekazuje je do odpowiedniego serwera fizycznego na podstawie nazwy bazy danych określonej w parametrach połączenia.

Nadrzędną zasadą dotyczącą zabezpieczeń sieci w ramach oferty usługi Azure SQL Database jest zezwalanie wyłącznie na połączenia i komunikację, która jest niezbędna do działania usługi. Wszystkie inne porty, protokoły i połączenia są domyślnie blokowane. Wirtualne sieci lokalne (VLAN) i listy kontroli dostępu są używane do ograniczania komunikacji sieciowej według sieci źródłowej i docelowej, protokołów i numerów portów.

Mechanizmy zatwierdzone do implementacji list ACL opartych na sieci obejmują listy ACL na routerach i modułach równoważenia obciążenia. Te mechanizmy są zarządzane przez sieć platformy Azure, zaporę maszyny wirtualnej gościa i reguły zapory Azure SQL Database Gateway, które są konfigurowane przez klienta.

## <a name="data-segregation-and-customer-isolation"></a>Podział danych i izolacja klienta
Sieć produkcyjna platformy Azure jest strukturalna w taki sposób, że publicznie dostępne składniki systemu są segregowane z zasobów wewnętrznych. Między serwerami sieci Web istnieją granice fizyczne i logiczne, które zapewniają dostęp do Azure Portal publicznego i podstawowej infrastruktury wirtualnej platformy Azure, w której znajdują się wystąpienia aplikacji klienta i dane klienta.

Wszystkie publicznie dostępne informacje są zarządzane w sieci produkcyjnej platformy Azure. W przypadku sieci produkcyjnej podlegają uwierzytelnianie dwuskładnikowe i mechanizmy ochrony granic, program korzysta z zestawu funkcji Zapora i zabezpieczenia, który został opisany w poprzedniej sekcji, i używa funkcji izolacji danych, jak to zanotowano w następnych sekcjach.

### <a name="unauthorized-systems-and-isolation-of-the-fc"></a>Nieautoryzowane systemy i izolacja FC
Ze względu na to, że kontroler sieci szkieletowej (FC) jest centralnym koordynatorem sieci szkieletowej Azure, są stosowane znaczące środki ograniczające zagrożenie, zwłaszcza z potencjalnie naruszonych FAs w aplikacjach klientów. FC nie rozpoznaje żadnego sprzętu, którego informacje o urządzeniu (na przykład adres MAC) nie są wstępnie załadowane w ramach FC. Serwery DHCP na FC zostały skonfigurowane listy adresów MAC węzłów, które chcą przeprowadzić rozruch. Nawet jeśli nieautoryzowane systemy są połączone, nie są one włączone do spisu sieci szkieletowej i w związku z tym nie są połączone ani autoryzowane do komunikacji z jakimkolwiek systemem w ramach spisu sieci szkieletowej. Zmniejsza to ryzyko, że nieautoryzowane systemy komunikują się z FC i uzyskują dostęp do sieci VLAN i platformy Azure.

### <a name="vlan-isolation"></a>Izolacja sieci VLAN
Sieć produkcyjna platformy Azure jest logicznie segregowana na trzy podstawowe sieci VLAN:

- Główna sieć VLAN: wzajemnie nawiązuje połączenie z niezaufanymi węzłami klienta.
- Sieć VLAN FC: zawiera zaufane systemy FCs i pomocnicze.
- Urządzenie VLAN: zawiera zaufane sieci i inne urządzenia infrastruktury.

### <a name="packet-filtering"></a>Filtrowanie pakietów
IPFilter i zapory oprogramowania zaimplementowane w głównym systemie operacyjnym i systemie operacyjnym gościa węzłów wymuszają ograniczenia łączności i uniemożliwiają nieautoryzowany ruch między maszynami wirtualnymi.

### <a name="hypervisor-root-os-and-guest-vms"></a>Funkcja hypervisor, główny system operacyjny i maszyny wirtualne gościa
Izolacja głównego systemu operacyjnego z maszyn wirtualnych gościa i maszyn wirtualnych gościa jest zarządzana przez funkcję hypervisor i główny system operacyjny.

### <a name="types-of-rules-on-firewalls"></a>Typy reguł na zaporach
Reguła jest definiowana jako:

{SRC IP, port src, docelowy adres IP, port docelowy, protokół docelowy, ruch przychodzący/out, stanowy/bezstanowy, limit czasu przepływu Stanów}.

Asynchroniczny znak bezczynności (SYN) jest dozwolony tylko w przypadku, gdy jedna z reguł zezwala na to. W przypadku protokołu TCP platforma Azure korzysta z reguł bezstanowych, w których zasada jest, że umożliwia tylko wszystkie Niesyn pakietów do lub z maszyny wirtualnej. Lokalne zabezpieczenia polegają na tym, że każdy stos hosta jest odporny na ignorowanie, jeśli nie zostanie użyty wcześniej pakiet SYN. Sam protokół TCP jest stanowy, a w połączeniu z regułą bezstanowej SYN pozwala uzyskać ogólne zachowanie implementacji stanowej.

W przypadku protokołu UDP (User Datagram Protocol) platforma Azure używa reguły stanowej. Za każdym razem, gdy pakiet UDP jest zgodny z regułą, w drugim kierunku jest tworzony odwrócony przepływ. Ten przepływ ma wbudowany limit czasu.

Klienci są zobowiązani do konfigurowania własnych zapór na platformie Azure. Tutaj klienci mogą definiować reguły dla ruchu przychodzącego i wychodzącego.

### <a name="production-configuration-management"></a>Zarządzanie konfiguracją produkcyjną
Standardowe bezpieczne konfiguracje są obsługiwane przez odpowiednie zespoły operacji na platformie Azure i Azure SQL Database. Wszystkie zmiany konfiguracji w systemach produkcyjnych są udokumentowane i śledzone za pomocą centralnego systemu śledzenia. Zmiany oprogramowania i sprzętu są śledzone za pomocą centralnego systemu śledzenia. Zmiany w sieci, które odnoszą się do listy ACL, są śledzone za pomocą usługi zarządzania listą ACL.

Wszystkie zmiany konfiguracji platformy Azure są opracowywane i testowane w środowisku przejściowym, a następnie wdrażane w środowisku produkcyjnym. Kompilacje oprogramowania są analizowane w ramach testowania. Sprawdzanie zabezpieczeń i prywatności jest analizowane w ramach kryteriów listy kontrolnej wpisów. Zmiany są wdrażane w zaplanowanych odstępach czasu przez odpowiedni zespół wdrożeniowy. Wersje są analizowane i wypisywane przez odpowiedniego pracownika zespołu wdrożeniowego przed wdrożeniem ich w środowisku produkcyjnym.

Zmiany są monitorowane pod kątem sukcesu. W scenariuszu awarii zmiana zostanie wycofana do poprzedniego stanu lub poprawka zostanie wdrożona w celu rozwiązania problemu z zatwierdzeniem przez wyznaczoną kadr. Magazyn źródłowy, Git, TFS, Master Data Services (MDS), moduły uruchamiające, monitorowanie zabezpieczeń platformy Azure, FC i platforma WinFabric umożliwiają centralne zarządzanie, stosowanie i weryfikowanie ustawień konfiguracji w środowisku wirtualnym platformy Azure.

Podobnie zmiany sprzętu i sieci zostały ustalone w celu ocenienia ich zgodności z wymaganiami kompilacji. Wersje są przeglądane i autoryzowane za pośrednictwem skoordynowanej tablicy doradcy zmian (CAB) odpowiednich grup na stosie.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat sposobu zabezpieczania infrastruktury platformy Azure przez firmę Microsoft, zobacz:

- [Funkcje platformy Azure, lokalne i zabezpieczenia fizyczne](physical-security.md)
- [Dostępność infrastruktury platformy Azure](infrastructure-availability.md)
- [Składniki i granice systemu informacji platformy Azure](infrastructure-components.md)
- [Architektura sieci platformy Azure](infrastructure-network.md)
- [Sieć produkcyjna platformy Azure](production-network.md)
- [Operacje produkcyjne platformy Azure i zarządzanie nimi](infrastructure-operations.md)
- [Monitorowanie infrastruktury platformy Azure](infrastructure-monitoring.md)
- [Integralność infrastruktury platformy Azure](infrastructure-integrity.md)
- [Ochrona danych klienta platformy Azure](protection-customer-data.md)
