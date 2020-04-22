---
title: Architektura łączności dla wystąpienia zarządzanego
description: Dowiedz się więcej o komunikacji i architekturze komunikacji z wystąpieniami zarządzanymi usługi Azure SQL Database, a także o tym, jak składniki kierują ruch do wystąpienia zarządzanego.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: e4d6098b7b4de76461e924fc7d42d039046d7ce5
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677170"
---
# <a name="connectivity-architecture-for-a-managed-instance-in-azure-sql-database"></a>Architektura łączności dla wystąpienia zarządzanego w bazie danych SQL usługi Azure

W tym artykule opisano komunikację w wystąpieniu zarządzanym usługi Azure SQL Database. Opisano w nim również architekturę łączności i sposób kierowania przez składniki ruchu do wystąpienia zarządzanego.  

Wystąpienie zarządzane bazy danych SQL jest umieszczane wewnątrz sieci wirtualnej platformy Azure i podsieci, która jest dedykowana do wystąpieniami zarządzanymi. To wdrożenie zapewnia:

- Bezpieczny prywatny adres IP.
- Możliwość łączenia sieci lokalnej z wystąpieniem zarządzanym.
- Możliwość łączenia wystąpienia zarządzanego z serwerem połączonym lub innym lokalnym magazynem danych.
- Możliwość łączenia wystąpienia zarządzanego z zasobami platformy Azure.

## <a name="communication-overview"></a>Omówienie komunikacji

Na poniższym diagramie przedstawiono jednostki, które łączą się z wystąpieniem zarządzanym. Pokazuje również zasoby, które muszą komunikować się z wystąpieniem zarządzanym. Proces komunikacji w dolnej części diagramu reprezentuje aplikacje klientów i narzędzia, które łączą się z wystąpieniem zarządzanym jako źródła danych.  

![Jednostki w architekturze łączności](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

Wystąpienie zarządzane jest platformą jako usługą (PaaS). Firma Microsoft używa zautomatyzowanych agentów (zarządzania, wdrażania i konserwacji) do zarządzania tą usługą na podstawie strumieni danych telemetrycznych. Ponieważ firma Microsoft jest odpowiedzialna za zarządzanie, klienci nie mogą uzyskać dostępu do maszyn klastra wirtualnego wystąpienia zarządzanego za pośrednictwem protokołu RDP (Remote Desktop Protocol).

Niektóre operacje programu SQL Server uruchomione przez użytkowników końcowych lub aplikacje mogą wymagać wystąpienia zarządzanego do interakcji z platformą. Jednym z przypadków jest utworzenie bazy danych wystąpienia zarządzanego. Ten zasób jest udostępniany za pośrednictwem witryny Azure portal, powershell, interfejsu wiersza polecenia platformy Azure i interfejsu API REST.

Wystąpienia zarządzane zależą od usług platformy Azure, takich jak Usługa Azure Storage do tworzenia kopii zapasowych, usługi Azure Event Hubs dla telemetrii, usługa Azure Active Directory do uwierzytelniania, usługa Azure Key Vault for Transparent Data Encryption (TDE) i kilka usług platformy Azure, które zapewniają funkcje zabezpieczeń i obsługi. Wystąpienia zarządzane nawiązuje połączenia z tymi usługami.

Cała komunikacja jest szyfrowana i podpisywane przy użyciu certyfikatów. Aby sprawdzić wiarygodność komunikujących się stron, wystąpienia zarządzane stale weryfikują te certyfikaty za pomocą list odwołania certyfikatów. Jeśli certyfikaty zostaną odwołane, wystąpienie zarządzane zamyka połączenia w celu ochrony danych.

## <a name="high-level-connectivity-architecture"></a>Architektura łączności wysokiego poziomu

Na wysokim poziomie wystąpienie zarządzane jest zestaw składników usługi. Te składniki są hostowane na dedykowanym zestawie izolowanych maszyn wirtualnych, które działają wewnątrz podsieci sieci wirtualnej klienta. Te maszyny tworzą klaster wirtualny.

Klaster wirtualny może obsługiwać wiele wystąpień zarządzanych. W razie potrzeby klaster automatycznie rozszerza się lub zawiera umowy, gdy odbiorca zmieni liczbę aprowizowanych wystąpień w podsieci.

Aplikacje klientów mogą łączyć się z wystąpieniami zarządzanymi i mogą wysyłać zapytania i aktualizować bazy danych w sieci wirtualnej, sieci wirtualnej lub sieci połączonej siecią VPN lub azure expressroute. Ta sieć musi używać punktu końcowego i prywatnego adresu IP.  

![Diagram architektury łączności](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

Usługi zarządzania i wdrażania firmy Microsoft są uruchamiane poza siecią wirtualną. Wystąpienie zarządzane i usługi firmy Microsoft łączą się za pomocą punktów końcowych, które mają publiczne adresy IP. Gdy wystąpienie zarządzane tworzy połączenie wychodzące, po odebraniu końcowego translacji adresu sieciowego (NAT) sprawia, że połączenie wygląda tak, jakby pochodziło z tego publicznego adresu IP.

Ruch zarządzania przepływa przez sieć wirtualną klienta. Oznacza to, że elementy infrastruktury sieci wirtualnej może uszkodzić ruch zarządzania przez wystąpienie nie powiedzie się i stają się niedostępne.

> [!IMPORTANT]
> Aby poprawić jakość obsługi klienta i dostępność usług, firma Microsoft stosuje zasady intencji sieci w elementach infrastruktury sieci wirtualnej platformy Azure. Zasady mogą mieć wpływ na działanie wystąpienia zarządzanego. Ten mechanizm platformy w sposób przejrzysty komunikuje wymagania dotyczące sieci użytkowników. Głównym celem zasad jest zapobieganie błędnej konfiguracji sieci i zapewnienie normalnych operacji wystąpienia zarządzanego. Po usunięciu wystąpienia zarządzanego zasady intencji sieciowej są również usuwane.

## <a name="virtual-cluster-connectivity-architecture"></a>Architektura łączności klastra wirtualnego

Przyjmijmy głębsze spojrzenie na architekturę łączności dla wystąpień zarządzanych. Na poniższym diagramie przedstawiono układ koncepcyjny klastra wirtualnego.

![Architektura łączności klastra wirtualnego](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

Klienci łączą się z wystąpieniem zarządzanym przy `<mi_name>.<dns_zone>.database.windows.net`użyciu nazwy hosta, która ma formularz . Ta nazwa hosta jest rozpoznawana jako prywatny adres IP, mimo że jest zarejestrowana w publicznej strefie DNS (Domain Name System) i jest publicznie rozpoznawana. Jest `zone-id` generowany automatycznie podczas tworzenia klastra. Jeśli nowo utworzony klaster obsługuje pomocnicze wystąpienie zarządzane, udostępnia swój identyfikator strefy z klasterem podstawowym. Aby uzyskać więcej informacji, zobacz [Używanie automatycznych grup pracy awaryjnej w celu włączenia przezroczystej i skoordynowanej pracy awaryjnej wielu baz danych](sql-database-auto-failover-group.md#enabling-geo-replication-between-managed-instances-and-their-vnets).

Ten prywatny adres IP należy do wewnętrznego modułu równoważenia obciążenia wystąpienia zarządzanego. Moduł równoważenia obciążenia kieruje ruch do bramy wystąpienia zarządzanego. Ponieważ wiele wystąpień zarządzanych można uruchomić wewnątrz tego samego klastra, brama używa nazwy hosta wystąpienia zarządzanego, aby przekierować ruch do poprawnej usługi aparatu SQL.

Usługi zarządzania i wdrażania łączą się z wystąpieniem zarządzanym przy użyciu [punktu końcowego zarządzania,](#management-endpoint) który jest mapowyny do zewnętrznego modułu równoważenia obciążenia. Ruch jest kierowany do węzłów tylko wtedy, gdy jest odbierany na wstępnie zdefiniowanym zestawie portów, których używają tylko składniki zarządzania wystąpienia zarządzanego. Wbudowana zapora w węzłach jest skonfigurowana tak, aby zezwalać na ruch tylko z zakresów adresów IP firmy Microsoft. Certyfikaty wzajemnie uwierzytelniają całą komunikację między składnikami zarządzania a płaszczyzną zarządzania.

## <a name="management-endpoint"></a>Punkt końcowy zarządzania

Firma Microsoft zarządza wystąpieniem zarządzanym przy użyciu punktu końcowego zarządzania. Ten punkt końcowy znajduje się wewnątrz klastra wirtualnego wystąpienia. Punkt końcowy zarządzania jest chroniony przez wbudowaną zaporę na poziomie sieci. Na poziomie aplikacji jest chroniony przez wzajemną weryfikację certyfikatu. Aby znaleźć adres IP punktu końcowego, zobacz [Określanie adresu IP punktu końcowego zarządzania](sql-database-managed-instance-find-management-endpoint-ip-address.md).

Gdy połączenia rozpoczynają się wewnątrz wystąpienia zarządzanego (podobnie jak w przypadku kopii zapasowych i dzienników inspekcji), ruch wydaje się rozpoczynać od publicznego adresu IP punktu końcowego zarządzania. Dostęp do usług publicznych można ograniczyć z wystąpienia zarządzanego, ustawiając reguły zapory, aby zezwalać tylko na adres IP wystąpienia zarządzanego. Aby uzyskać więcej informacji, zobacz [Sprawdzanie wbudowanej zapory wystąpienia zarządzanego](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> Ruch, który przechodzi do usług platformy Azure, które znajdują się wewnątrz regionu wystąpienia zarządzanego jest zoptymalizowany i z tego powodu nie NATed do zarządzania wystąpieniem zarządzania wystąpieniami publicznego adresu IP. Z tego powodu, jeśli trzeba użyć reguł zapory opartej na adresie IP, najczęściej do przechowywania, usługa musi znajdować się w innym regionie niż wystąpienie zarządzane.

## <a name="service-aided-subnet-configuration"></a>Konfiguracja podsieci wspomagana przez usługę

Aby rozwiązać wymagania dotyczące zabezpieczeń i zarządzania klienta Wystąpienie zarządzane jest przejście z ręcznej do konfiguracji podsieci wspomaganej usługą.

Dzięki konfiguracji podsieci wspomaganej usługami użytkownik ma pełną kontrolę nad ruchem danych (TDS), podczas gdy wystąpienie zarządzane bierze odpowiedzialność za zapewnienie nieprzerwanego przepływu ruchu zarządzania w celu spełnienia umowy SLA.

Konfiguracja podsieci wspomagana usługami opiera się na funkcji [delegowania](../virtual-network/subnet-delegation-overview.md) podsieci sieci wirtualnej w celu zapewnienia automatycznego zarządzania konfiguracją sieci i włączania punktów końcowych usługi. Punkty końcowe usługi mogą służyć do konfigurowania reguł zapory sieci wirtualnej na kontach magazynu, które przechowują kopie zapasowe / dzienniki inspekcji.

### <a name="network-requirements"></a>Wymagania dotyczące sieci 

Wdrażanie wystąpienia zarządzanego w dedykowanej podsieci wewnątrz sieci wirtualnej. Podsieć musi mieć następujące właściwości:

- **Dedykowana podsieć:** Podsieć wystąpienia zarządzanego nie może zawierać żadnej innej usługi w chmurze, która jest z nią skojarzona i nie może być podsiecią bramy. Podsieć nie może zawierać żadnego zasobu, ale wystąpienie zarządzane, a później nie można dodać innych typów zasobów w podsieci.
- **Delegowanie podsieci:** Podsieć wystąpienia zarządzanego musi zostać `Microsoft.Sql/managedInstances` delegowana do dostawcy zasobów.
- **Grupa zabezpieczeń sieci (NSG):** Grupa sieciowa musi być skojarzona z podsiecią wystąpienia zarządzanego. Sieciowej sieciowej sieciowej można użyć do kontrolowania dostępu do punktu końcowego danych wystąpienia zarządzanego, filtrując ruch na porcie 1433 i portach 11000-11999, gdy wystąpienie zarządzane jest skonfigurowane do połączeń przekierowania. Usługa będzie automatycznie aprowizować i zachować [bieżące reguły](#mandatory-inbound-security-rules-with-service-aided-subnet-configuration) wymagane, aby umożliwić nieprzerwany przepływ ruchu zarządzania.
- **Tabela marszruty zdefiniowana przez użytkownika (UDR):** Tabela UDR musi być skojarzona z podsiecią wystąpienia zarządzanego. Można dodać wpisy do tabeli tras, aby kierować ruch, który ma lokalne prywatne zakresy adresów IP jako miejsce docelowe za pośrednictwem bramy sieci wirtualnej lub urządzenia sieci wirtualnej (NVA). Usługa będzie automatycznie aprowizować i przechowywać [bieżące wpisy](#user-defined-routes-with-service-aided-subnet-configuration) wymagane, aby umożliwić nieprzerwany przepływ ruchu zarządzania.
- **Wystarczające adresy IP:** Podsieć wystąpienia zarządzanego musi mieć co najmniej 16 adresów IP. Zalecane minimum to 32 adresy IP. Aby uzyskać więcej informacji, zobacz [Określanie rozmiaru podsieci dla wystąpień zarządzanych](sql-database-managed-instance-determine-size-vnet-subnet.md). Wystąpienia zarządzane w [istniejącej sieci](sql-database-managed-instance-configure-vnet-subnet.md) można wdrożyć po skonfigurowaniu jej w celu spełnienia [wymagań sieciowych dla wystąpień zarządzanych](#network-requirements). W przeciwnym razie utwórz [nową sieć i podsieć](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Podczas tworzenia wystąpienia zarządzanego w podsieci stosuje się zasadę intencji sieci, aby zapobiec niezgodnym zmianom w konfiguracji sieci. Po usunięciu ostatniego wystąpienia z podsieci zasady intencji sieci jest również usuwany.

### <a name="mandatory-inbound-security-rules-with-service-aided-subnet-configuration"></a>Obowiązkowe reguły zabezpieczeń przychodzących z konfiguracją podsieci wspomaganej usługą 

| Nazwa       |Port                        |Protocol (Protokół)|Element źródłowy           |Element docelowy|Akcja|
|------------|----------------------------|--------|-----------------|-----------|------|
|zarządzanie  |9000, 9003, 1438, 1440, 1452|TCP     |SqlManagement (Narzędzie sql)    |PODSIEĆ MI  |Zezwalaj |
|            |9000, 9003                  |TCP     |Piła corpnet       |PODSIEĆ MI  |Zezwalaj |
|            |9000, 9003                  |TCP     |CorpnetPublic    |PODSIEĆ MI  |Zezwalaj |
|mi_subnet   |Dowolne                         |Dowolne     |PODSIEĆ MI        |PODSIEĆ MI  |Zezwalaj |
|health_probe|Dowolne                         |Dowolne     |AzureLoadBalancer|PODSIEĆ MI  |Zezwalaj |

### <a name="mandatory-outbound-security-rules-with-service-aided-subnet-configuration"></a>Obowiązkowe reguły zabezpieczeń ruchu wychodzącego z konfiguracją podsieci wspomaganej usługą 

| Nazwa       |Port          |Protocol (Protokół)|Element źródłowy           |Element docelowy|Akcja|
|------------|--------------|--------|-----------------|-----------|------|
|zarządzanie  |443, 12000    |TCP     |PODSIEĆ MI        |AzureCloud |Zezwalaj |
|mi_subnet   |Dowolne           |Dowolne     |PODSIEĆ MI        |PODSIEĆ MI  |Zezwalaj |

### <a name="user-defined-routes-with-service-aided-subnet-configuration"></a>Trasy zdefiniowane przez użytkownika z konfiguracją podsieci wspomaganej usługą 

|Nazwa|Prefiks adresu|Następny przeskok|
|----|--------------|-------|
|podsieci do siecilokalnych|PODSIEĆ MI|Sieć wirtualna|
|mi-13-64-11-nexthop-internet|13.64.0.0/11|Internet|
|mi-13-104-14-nexthop-internet|13.104.0.0/14|Internet|
|mi-20-33-16-nexthop-internet|20.33.0.0/16|Internet|
|mi-20-34-15-nexthop-internet|20.34.0.0/15|Internet|
|mi-20-36-14-nexthop-internet|20.36.0.0/14|Internet|
|mi-20-40-13-nexthop-internet|20.40.0.0/13|Internet|
|mi-20-48-12-nexthop-internet|20.48.0.0/12|Internet|
|mi-20-64-10-nexthop-internet|20.64.0.0/10|Internet|
|mi-20-128-16-nexthop-internet|20.128.0.0/16|Internet|
|mi-20-135-16-nexthop-internet|20.135.0.0/16|Internet|
|mi-20-136-16-nexthop-internet|20.136.0.0/16|Internet|
|mi-20-140-15-nexthop-internet|20.140.0.0/15|Internet|
|mi-20-143-16-nexthop-internet|20.143.0.0/16|Internet|
|mi-20-144-14-nexthop-internet|20.144.0.0/14|Internet|
|mi-20-150-15-nexthop-internet|20.150.0.0/15|Internet|
|mi-20-160-12-nexthop-internet|20.160.0.0/12|Internet|
|mi-20-176-14-nexthop-internet|20.176.0.0/14|Internet|
|mi-20-180-14-nexthop-internet|20.180.0.0/14|Internet|
|mi-20-184-13-nexthop-internet|20.184.0.0/13|Internet|
|mi-20-192-10-nexthop-internet|20.192.0.0/10|Internet|
|mi-40-64-10-nexthop-internet|40.64.0.0/10|Internet|
|mi-51-4-15-nexthop-internet|51.4.0.0/15|Internet|
|mi-51-8-16-nexthop-internet|51.8.0.0/16|Internet|
|mi-51-10-15-nexthop-internet|51.10.0.0/15|Internet|
|mi-51-18-16-nexthop-internet|51.18.0.0/16|Internet|
|mi-51-51-16-nexthop-internet|51.51.0.0/16|Internet|
|mi-51-53-16-nexthop-internet|51.53.0.0/16|Internet|
|mi-51-103-16-nexthop-internet|51.103.0.0/16|Internet|
|mi-51-104-15-nexthop-internet|51.104.0.0/15|Internet|
|mi-51-132-16-nexthop-internet|51.132.0.0/16|Internet|
|mi-51-136-15-nexthop-internet|51.136.0.0/15|Internet|
|mi-51-138-16-nexthop-internet|51.138.0.0/16|Internet|
|mi-51-140-14-nexthop-internet|51.140.0.0/14|Internet|
|mi-51-144-15-nexthop-internet|51.144.0.0/15|Internet|
|mi-52-96-12-nexthop-internet|52.96.0.0/12|Internet|
|mi-52-112-14-nexthop-internet|52.112.0.0/14|Internet|
|mi-52-125-16-nexthop-internet|52.125.0.0/16|Internet|
|mi-52-126-15-nexthop-internet|52.126.0.0/15|Internet|
|mi-52-130-15-nexthop-internet|52.130.0.0/15|Internet|
|mi-52-132-14-nexthop-internet|52.132.0.0/14|Internet|
|mi-52-136-13-nexthop-internet|52.136.0.0/13|Internet|
|mi-52-145-16-nexthop-internet|52.145.0.0/16|Internet|
|mi-52-146-15-nexthop-internet|52.146.0.0/15|Internet|
|mi-52-148-14-nexthop-internet|52.148.0.0/14|Internet|
|mi-52-152-13-nexthop-internet|52.152.0.0/13|Internet|
|mi-52-160-11-nexthop-internet|52.160.0.0/11|Internet|
|mi-52-224-11-nexthop-internet|52.224.0.0/11|Internet|
|mi-64-4-18-nexthop-internet|64.4.0.0/18|Internet|
|mi-65-52-14-nexthop-internet|65.52.0.0/14|Internet|
|mi-66-119-144-20-nexthop-internet|66.119.144.0/20|Internet|
|mi-70-37-17-nexthop-internet|70.37.0.0/17|Internet|
|mi-70-37-128-18-nexthop-internet|70.37.128.0/18|Internet|
|mi-91-190-216-21-nexthop-internet|91.190.216.0/21|Internet|
|mi-94-245-64-18-nexthop-internet|94.245.64.0/18|Internet|
|mi-103-9-8-22-nexthop-internet|103.9.8.0/22|Internet|
|mi-103-25-156-24-nexthop-internet|103.25.156.0/24|Internet|
|mi-103-25-157-24-nexthop-internet|103.25.157.0/24|Internet|
|mi-103-25-158-23-nexthop-internet|103.25.158.0/23|Internet|
|mi-103-36-96-22-nexthop-internet|103.36.96.0/22|Internet|
|mi-103-255-140-22-nexthop-internet|103.255.140.0/22|Internet|
|mi-104-40-13-nexthop-internet|104.40.0.0/13|Internet|
|mi-104-146-15-nexthop-internet|104.146.0.0/15|Internet|
|mi-104-208-13-nexthop-internet|104.208.0.0/13|Internet|
|mi-111-221-16-20-nexthop-internet|111.221.16.0/20|Internet|
|mi-111-221-64-18-nexthop-internet|111.221.64.0/18|Internet|
|mi-129-75-16-nexthop-internet|129.75.0.0/16|Internet|
|mi-131-107-16-nexthop-internet|131.107.0.0/16|Internet|
|mi-131-253-1-24-nexthop-internet|131.253.1.0/24|Internet|
|mi-131-253-3-24-nexthop-internet|131.253.3.0/24|Internet|
|mi-131-253-5-24-nexthop-internet|131.253.5.0/24|Internet|
|mi-131-253-6-24-nexthop-internet|131.253.6.0/24|Internet|
|mi-131-253-8-24-nexthop-internet|131.253.8.0/24|Internet|
|mi-131-253-12-22-nexthop-internet|131.253.12.0/22|Internet|
|mi-131-253-16-23-nexthop-internet|131.253.16.0/23|Internet|
|mi-131-253-18-24-nexthop-internet|131.253.18.0/24|Internet|
|mi-131-253-21-24-nexthop-internet|131.253.21.0/24|Internet|
|mi-131-253-22-23-nexthop-internet|131.253.22.0/23|Internet|
|mi-131-253-24-21-nexthop-internet|131.253.24.0/21|Internet|
|mi-131-253-32-20-nexthop-internet|131.253.32.0/20|Internet|
|mi-131-253-61-24-nexthop-internet|131.253.61.0/24|Internet|
|mi-131-253-62-23-nexthop-internet|131.253.62.0/23|Internet|
|mi-131-253-64-18-nexthop-internet|131.253.64.0/18|Internet|
|mi-131-253-128-17-nexthop-internet|131.253.128.0/17|Internet|
|mi-132-245-16-nexthop-internet|132.245.0.0/16|Internet|
|mi-134-170-16-nexthop-internet|134.170.0.0/16|Internet|
|mi-134-177-16-nexthop-internet|134.177.0.0/16|Internet|
|mi-137-116-15-nexthop-internet|137.116.0.0/15|Internet|
|mi-137-135-16-nexthop-internet|137.135.0.0/16|Internet|
|mi-138-91-16-nexthop-internet|138.91.0.0/16|Internet|
|mi-138-196-16-nexthop-internet|138.196.0.0/16|Internet|
|mi-139-217-16-nexthop-internet|139.217.0.0/16|Internet|
|mi-139-219-16-nexthop-internet|139.219.0.0/16|Internet|
|mi-141-251-16-nexthop-internet|141.251.0.0/16|Internet|
|mi-146-147-16-nexthop-internet|146.147.0.0/16|Internet|
|mi-147-243-16-nexthop-internet|147.243.0.0/16|Internet|
|mi-150-171-16-nexthop-internet|150.171.0.0/16|Internet|
|mi-150-242-48-22-nexthop-internet|150.242.48.0/22|Internet|
|mi-157-54-15-nexthop-internet|157.54.0.0/15|Internet|
|mi-157-56-14-nexthop-internet|157.56.0.0/14|Internet|
|mi-157-60-16-nexthop-internet|157.60.0.0/16|Internet|
|mi-167-105-16-nexthop-internet|167.105.0.0/16|Internet|
|mi-167-220-16-nexthop-internet|167.220.0.0/16|Internet|
|mi-168-61-16-nexthop-internet|168.61.0.0/16|Internet|
|mi-168-62-15-nexthop-internet|168.62.0.0/15|Internet|
|mi-191-232-13-nexthop-internet|191.232.0.0/13|Internet|
|mi-192-32-16-nexthop-internet|192.32.0.0/16|Internet|
|mi-192-48-225-24-nexthop-internet|192.48.225.0/24|Internet|
|mi-192-84-159-24-nexthop-internet|192.84.159.0/24|Internet|
|mi-192-84-160-23-nexthop-internet|192.84.160.0/23|Internet|
|mi-192-197-157-24-nexthop-internet|192.197.157.0/24|Internet|
|mi-193-149-64-19-nexthop-internet|193.149.64.0/19|Internet|
|mi-193-221-113-24-nexthop-internet|193.221.113.0/24|Internet|
|mi-194-69-96-19-nexthop-internet|194.69.96.0/19|Internet|
|mi-194-110-197-24-nexthop-internet|194.110.197.0/24|Internet|
|mi-198-105-232-22-nexthop-internet|198.105.232.0/22|Internet|
|mi-198-200-130-24-nexthop-internet|198.200.130.0/24|Internet|
|mi-198-206-164-24-nexthop-internet|198.206.164.0/24|Internet|
|mi-199-60-28-24-nexthop-internet|199.60.28.0/24|Internet|
|mi-199-74-210-24-nexthop-internet|199.74.210.0/24|Internet|
|mi-199-103-90-23-nexthop-internet|199.103.90.0/23|Internet|
|mi-199-103-122-24-nexthop-internet|199.103.122.0/24|Internet|
|mi-199-242-32-20-nexthop-internet|199.242.32.0/20|Internet|
|mi-199-242-48-21-nexthop-internet|199.242.48.0/21|Internet|
|mi-202-89-224-20-nexthop-internet|202.89.224.0/20|Internet|
|mi-204-13-120-21-nexthop-internet|204.13.120.0/21|Internet|
|mi-204-14-180-22-nexthop-internet|204.14.180.0/22|Internet|
|mi-204-79-135-24-nexthop-internet|204.79.135.0/24|Internet|
|mi-204-79-179-24-nexthop-internet|204.79.179.0/24|Internet|
|mi-204-79-181-24-nexthop-internet|204.79.181.0/24|Internet|
|mi-204-79-188-24-nexthop-internet|204.79.188.0/24|Internet|
|mi-204-79-195-24-nexthop-internet|204.79.195.0/24|Internet|
|mi-204-79-196-23-nexthop-internet|204.79.196.0/23|Internet|
|mi-204-79-252-24-nexthop-internet|204.79.252.0/24|Internet|
|mi-204-152-18-23-nexthop-internet|204.152.18.0/23|Internet|
|mi-204-152-140-23-nexthop-internet|204.152.140.0/23|Internet|
|mi-204-231-192-24-nexthop-internet|204.231.192.0/24|Internet|
|mi-204-231-194-23-nexthop-internet|204.231.194.0/23|Internet|
|mi-204-231-197-24-nexthop-internet|204.231.197.0/24|Internet|
|mi-204-231-198-23-nexthop-internet|204.231.198.0/23|Internet|
|mi-204-231-200-21-nexthop-internet|204.231.200.0/21|Internet|
|mi-204-231-208-20-nexthop-internet|204.231.208.0/20|Internet|
|mi-204-231-236-24-nexthop-internet|204.231.236.0/24|Internet|
|mi-205-174-224-20-nexthop-internet|205.174.224.0/20|Internet|
|mi-206-138-168-21-nexthop-internet|206.138.168.0/21|Internet|
|mi-206-191-224-19-nexthop-internet|206.191.224.0/19|Internet|
|mi-207-46-16-nexthop-internet|207.46.0.0/16|Internet|
|mi-207-68-128-18-nexthop-internet|207.68.128.0/18|Internet|
|mi-208-68-136-21-nexthop-internet|208.68.136.0/21|Internet|
|mi-208-76-44-22-nexthop-internet|208.76.44.0/22|Internet|
|mi-208-84-21-nexthop-internet|208.84.0.0/21|Internet|
|mi-209-240-192-19-nexthop-internet|209.240.192.0/19|Internet|
|mi-213-199-128-18-nexthop-internet|213.199.128.0/18|Internet|
|mi-216-32-180-22-nexthop-internet|216.32.180.0/22|Internet|
|mi-216-220-208-20-nexthop-internet|216.220.208.0/20|Internet|
|mi-23-96-13-nexthop-internet|23.96.0.0/13|Internet|
|mi-42-159-16-nexthop-internet|42.159.0.0/16|Internet|
|mi-51-13-17-nexthop-internet|51.13.0.0/17|Internet|
|mi-51-107-16-nexthop-internet|51.107.0.0/16|Internet|
|mi-51-116-16-nexthop-internet|51.116.0.0/16|Internet|
|mi-51-120-16-nexthop-internet|51.120.0.0/16|Internet|
|mi-51-120-128-17-nexthop-internet|51.120.128.0/17|Internet|
|mi-51-124-16-nexthop-internet|51.124.0.0/16|Internet|
|mi-102-37-18-nexthop-internet|102.37.0.0/18|Internet|
|mi-102-133-16-nexthop-internet|102.133.0.0/16|Internet|
|mi-199-30-16-20-nexthop-internet|199.30.16.0/20|Internet|
|mi-204-79-180-24-nexthop-internet|204.79.180.0/24|Internet|
||||

\*PODSIEĆ MI odnosi się do zakresu adresów IP podsieci w postaci x.x.x.x/r. Informacje te można znaleźć w witrynie Azure portal, we właściwościach podsieci.

Ponadto można dodać wpisy do tabeli tras, aby kierować ruch, który ma lokalne prywatne zakresy adresów IP jako miejsce docelowe za pośrednictwem bramy sieci wirtualnej lub urządzenia sieci wirtualnej (NVA).

Jeśli sieć wirtualna zawiera niestandardowy system DNS, niestandardowy serwer DNS musi mieć możliwość rozpoznawania publicznych rekordów dns. Przy użyciu dodatkowych funkcji, takich jak uwierzytelnianie usługi Azure AD może wymagać rozwiązania dodatkowych sieci FQDN. Aby uzyskać więcej informacji, zobacz [Konfigurowanie niestandardowego systemu DNS](sql-database-managed-instance-custom-dns.md).

### <a name="networking-constraints"></a>Ograniczenia sieciowe

**TLS 1.2 jest wymuszany dla połączeń wychodzących:** w styczniu 2020 microsoft wymusił TLS 1.2 dla ruchu wewnątrz usługi we wszystkich usługach platformy Azure. W przypadku wystąpienia zarządzanego usługi Azure SQL Database spowodowało to wymuszanie protokołu TLS 1.2 w przypadku połączeń wychodzących używanych do replikacji i połączonych połączeń serwerowych z programem SQL Server. Jeśli używasz wersji programu SQL Server starszych niż 2016 z wystąpieniem zarządzanym, upewnij się, że zostały zastosowane [aktualizacje specyficzne dla protokołu TLS 1.2.](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server)

Następujące funkcje sieci wirtualnej nie są obecnie obsługiwane przez wystąpienie zarządzane:
- **Komunikacja równorzędna firmy Microsoft:** Włączenie [komunikacji równorzędnej](../expressroute/expressroute-faqs.md#microsoft-peering) firmy Microsoft w obwodach trasy ekspresowej równorzędnych bezpośrednio lub przechodnie z siecią wirtualną, w której znajduje się wystąpienie zarządzane, wpływa na przepływ ruchu między składnikami wystąpienia zarządzanego wewnątrz sieci wirtualnej a usługami, które zależą od spowodowania problemów z dostępnością. Oczekuje się, że wdrożenia wystąpienia zarządzanego w sieci wirtualnej z już włączonym programem Komunikacji równorzędnej firmy Microsoft nie powiodą się.
- **Globalna komunikacja równorzędna sieci wirtualnej:** łączność [równorzędna sieci wirtualnej](../virtual-network/virtual-network-peering-overview.md) w regionach platformy Azure nie działa w przypadku wystąpienia zarządzanego z powodu [udokumentowanych ograniczeń równoważenia obciążenia](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers).
- **AzurePlatformDNS**: Używanie [tagu usługi](../virtual-network/service-tags-overview.md) AzurePlatformDNS do blokowania rozpoznawania DNS platformy spowoduje, że wystąpienie zarządzane stanie się niedostępne. Chociaż wystąpienie zarządzane obsługuje zdefiniowany przez klienta system DNS dla rozpoznawania DNS wewnątrz aparatu, istnieje zależność od platformy DNS dla operacji platformy.
- **Brama NAT:** Używanie [translatora adresów sieci wirtualnych](../virtual-network/nat-overview.md) do kontrolowania łączności wychodzącej z określonym publicznym adresem IP spowodowałoby, że wystąpienie zarządzane stałoby się niedostępne. Usługa wystąpienia zarządzanego jest obecnie ograniczona do korzystania z podstawowego modułu równoważenia obciążenia, który nie zapewnia współistnienia przepływów przychodzących i wychodzących z siecią wirtualną NAT.

### <a name="deprecated-network-requirements-without-service-aided-subnet-configuration"></a>[Przestarzałe] Wymagania sieciowe bez konfiguracji podsieci wspomaganej usługą

Wdrażanie wystąpienia zarządzanego w dedykowanej podsieci wewnątrz sieci wirtualnej. Podsieć musi mieć następujące właściwości:

- **Dedykowana podsieć:** Podsieć wystąpienia zarządzanego nie może zawierać żadnej innej usługi w chmurze, która jest z nią skojarzona i nie może być podsiecią bramy. Podsieć nie może zawierać żadnego zasobu, ale wystąpienie zarządzane, a później nie można dodać innych typów zasobów w podsieci.
- **Grupa zabezpieczeń sieci (NSG):** Grupa zabezpieczeń sieciowych skojarzona z siecią wirtualną musi definiować [przychodzące reguły zabezpieczeń](#mandatory-inbound-security-rules) i [reguły zabezpieczeń wychodzących](#mandatory-outbound-security-rules) przed innymi regułami. Sieciowej sieciowej sieciowej można użyć do kontrolowania dostępu do punktu końcowego danych wystąpienia zarządzanego, filtrując ruch na porcie 1433 i portach 11000-11999, gdy wystąpienie zarządzane jest skonfigurowane do połączeń przekierowania.
- **Tabela marszruty zdefiniowana przez użytkownika (UDR):** Tabela UDR skojarzona z siecią wirtualną musi zawierać określone [wpisy](#user-defined-routes).
- **Brak punktów końcowych usługi:** Z podsiecią wystąpienia zarządzanego nie należy kojarzyć punktu końcowego usługi. Upewnij się, że opcja punktów końcowych usługi jest wyłączona podczas tworzenia sieci wirtualnej.
- **Wystarczające adresy IP:** Podsieć wystąpienia zarządzanego musi mieć co najmniej 16 adresów IP. Zalecane minimum to 32 adresy IP. Aby uzyskać więcej informacji, zobacz [Określanie rozmiaru podsieci dla wystąpień zarządzanych](sql-database-managed-instance-determine-size-vnet-subnet.md). Wystąpienia zarządzane w [istniejącej sieci](sql-database-managed-instance-configure-vnet-subnet.md) można wdrożyć po skonfigurowaniu jej w celu spełnienia [wymagań sieciowych dla wystąpień zarządzanych](#network-requirements). W przeciwnym razie utwórz [nową sieć i podsieć](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Nie można wdrożyć nowego wystąpienia zarządzanego, jeśli podsieć docelowa nie ma tych cech. Podczas tworzenia wystąpienia zarządzanego w podsieci stosuje się zasadę intencji sieci, aby zapobiec niezgodnym zmianom w konfiguracji sieci. Po usunięciu ostatniego wystąpienia z podsieci zasady intencji sieci jest również usuwany.

### <a name="mandatory-inbound-security-rules"></a>Obowiązkowe przychodzące reguły zabezpieczeń

| Nazwa       |Port                        |Protocol (Protokół)|Element źródłowy           |Element docelowy|Akcja|
|------------|----------------------------|--------|-----------------|-----------|------|
|zarządzanie  |9000, 9003, 1438, 1440, 1452|TCP     |Dowolne              |PODSIEĆ MI  |Zezwalaj |
|mi_subnet   |Dowolne                         |Dowolne     |PODSIEĆ MI        |PODSIEĆ MI  |Zezwalaj |
|health_probe|Dowolne                         |Dowolne     |AzureLoadBalancer|PODSIEĆ MI  |Zezwalaj |

### <a name="mandatory-outbound-security-rules"></a>Obowiązkowe reguły zabezpieczeń wychodzących

| Nazwa       |Port          |Protocol (Protokół)|Element źródłowy           |Element docelowy|Akcja|
|------------|--------------|--------|-----------------|-----------|------|
|zarządzanie  |443, 12000    |TCP     |PODSIEĆ MI        |AzureCloud |Zezwalaj |
|mi_subnet   |Dowolne           |Dowolne     |PODSIEĆ MI        |PODSIEĆ MI  |Zezwalaj |

> [!IMPORTANT]
> Upewnij się, że istnieje tylko jedna reguła ruchu przychodzącego dla portów 9000, 9003, 1438, 1440, 1452 i jedna reguła ruchu wychodzącego dla portów 443, 12000. Inicjowanie obsługi administracyjnej wystąpienia zarządzanego za pośrednictwem wdrożeń usługi Azure Resource Manager zakończy się niepowodzeniem, jeśli reguły przychodzące i wychodzące są skonfigurowane oddzielnie dla każdego portu. Jeśli te porty są w oddzielnych regułach, wdrożenie zakończy się niepowodzeniem z kodem błędu`VnetSubnetConflictWithIntendedPolicy`

\*PODSIEĆ MI odnosi się do zakresu adresów IP podsieci w postaci x.x.x.x/r. Informacje te można znaleźć w witrynie Azure portal, we właściwościach podsieci.

> [!IMPORTANT]
> Mimo że wymagane reguły zabezpieczeń przychodzących zezwalają na ruch z _dowolnego_ źródła na portach 9000, 9003, 1438, 1440 i 1452, porty te są chronione przez wbudowaną zaporę. Aby uzyskać więcej informacji, zobacz [Określanie adresu punktu końcowego zarządzania](sql-database-managed-instance-find-management-endpoint-ip-address.md).

> [!NOTE]
> Jeśli replikacja transakcyjna jest używana w wystąpieniu zarządzanym i jeśli używasz dowolnej bazy danych wystąpień jako wydawca lub dystrybutor, otwórz port 445 (TCP wychodzący) w regułach zabezpieczeń podsieci. Ten port umożliwi dostęp do udziału plików platformy Azure.

### <a name="user-defined-routes"></a>Trasy zdefiniowane przez użytkownika

|Nazwa|Prefiks adresu|Następny przeskok|
|----|--------------|-------|
|subnet_to_vnetlocal|PODSIEĆ MI|Sieć wirtualna|
|mi-13-64-11-nexthop-internet|13.64.0.0/11|Internet|
|mi-13-104-14-nexthop-internet|13.104.0.0/14|Internet|
|mi-20-33-16-nexthop-internet|20.33.0.0/16|Internet|
|mi-20-34-15-nexthop-internet|20.34.0.0/15|Internet|
|mi-20-36-14-nexthop-internet|20.36.0.0/14|Internet|
|mi-20-40-13-nexthop-internet|20.40.0.0/13|Internet|
|mi-20-48-12-nexthop-internet|20.48.0.0/12|Internet|
|mi-20-64-10-nexthop-internet|20.64.0.0/10|Internet|
|mi-20-128-16-nexthop-internet|20.128.0.0/16|Internet|
|mi-20-135-16-nexthop-internet|20.135.0.0/16|Internet|
|mi-20-136-16-nexthop-internet|20.136.0.0/16|Internet|
|mi-20-140-15-nexthop-internet|20.140.0.0/15|Internet|
|mi-20-143-16-nexthop-internet|20.143.0.0/16|Internet|
|mi-20-144-14-nexthop-internet|20.144.0.0/14|Internet|
|mi-20-150-15-nexthop-internet|20.150.0.0/15|Internet|
|mi-20-160-12-nexthop-internet|20.160.0.0/12|Internet|
|mi-20-176-14-nexthop-internet|20.176.0.0/14|Internet|
|mi-20-180-14-nexthop-internet|20.180.0.0/14|Internet|
|mi-20-184-13-nexthop-internet|20.184.0.0/13|Internet|
|mi-20-192-10-nexthop-internet|20.192.0.0/10|Internet|
|mi-40-64-10-nexthop-internet|40.64.0.0/10|Internet|
|mi-51-4-15-nexthop-internet|51.4.0.0/15|Internet|
|mi-51-8-16-nexthop-internet|51.8.0.0/16|Internet|
|mi-51-10-15-nexthop-internet|51.10.0.0/15|Internet|
|mi-51-18-16-nexthop-internet|51.18.0.0/16|Internet|
|mi-51-51-16-nexthop-internet|51.51.0.0/16|Internet|
|mi-51-53-16-nexthop-internet|51.53.0.0/16|Internet|
|mi-51-103-16-nexthop-internet|51.103.0.0/16|Internet|
|mi-51-104-15-nexthop-internet|51.104.0.0/15|Internet|
|mi-51-132-16-nexthop-internet|51.132.0.0/16|Internet|
|mi-51-136-15-nexthop-internet|51.136.0.0/15|Internet|
|mi-51-138-16-nexthop-internet|51.138.0.0/16|Internet|
|mi-51-140-14-nexthop-internet|51.140.0.0/14|Internet|
|mi-51-144-15-nexthop-internet|51.144.0.0/15|Internet|
|mi-52-96-12-nexthop-internet|52.96.0.0/12|Internet|
|mi-52-112-14-nexthop-internet|52.112.0.0/14|Internet|
|mi-52-125-16-nexthop-internet|52.125.0.0/16|Internet|
|mi-52-126-15-nexthop-internet|52.126.0.0/15|Internet|
|mi-52-130-15-nexthop-internet|52.130.0.0/15|Internet|
|mi-52-132-14-nexthop-internet|52.132.0.0/14|Internet|
|mi-52-136-13-nexthop-internet|52.136.0.0/13|Internet|
|mi-52-145-16-nexthop-internet|52.145.0.0/16|Internet|
|mi-52-146-15-nexthop-internet|52.146.0.0/15|Internet|
|mi-52-148-14-nexthop-internet|52.148.0.0/14|Internet|
|mi-52-152-13-nexthop-internet|52.152.0.0/13|Internet|
|mi-52-160-11-nexthop-internet|52.160.0.0/11|Internet|
|mi-52-224-11-nexthop-internet|52.224.0.0/11|Internet|
|mi-64-4-18-nexthop-internet|64.4.0.0/18|Internet|
|mi-65-52-14-nexthop-internet|65.52.0.0/14|Internet|
|mi-66-119-144-20-nexthop-internet|66.119.144.0/20|Internet|
|mi-70-37-17-nexthop-internet|70.37.0.0/17|Internet|
|mi-70-37-128-18-nexthop-internet|70.37.128.0/18|Internet|
|mi-91-190-216-21-nexthop-internet|91.190.216.0/21|Internet|
|mi-94-245-64-18-nexthop-internet|94.245.64.0/18|Internet|
|mi-103-9-8-22-nexthop-internet|103.9.8.0/22|Internet|
|mi-103-25-156-24-nexthop-internet|103.25.156.0/24|Internet|
|mi-103-25-157-24-nexthop-internet|103.25.157.0/24|Internet|
|mi-103-25-158-23-nexthop-internet|103.25.158.0/23|Internet|
|mi-103-36-96-22-nexthop-internet|103.36.96.0/22|Internet|
|mi-103-255-140-22-nexthop-internet|103.255.140.0/22|Internet|
|mi-104-40-13-nexthop-internet|104.40.0.0/13|Internet|
|mi-104-146-15-nexthop-internet|104.146.0.0/15|Internet|
|mi-104-208-13-nexthop-internet|104.208.0.0/13|Internet|
|mi-111-221-16-20-nexthop-internet|111.221.16.0/20|Internet|
|mi-111-221-64-18-nexthop-internet|111.221.64.0/18|Internet|
|mi-129-75-16-nexthop-internet|129.75.0.0/16|Internet|
|mi-131-107-16-nexthop-internet|131.107.0.0/16|Internet|
|mi-131-253-1-24-nexthop-internet|131.253.1.0/24|Internet|
|mi-131-253-3-24-nexthop-internet|131.253.3.0/24|Internet|
|mi-131-253-5-24-nexthop-internet|131.253.5.0/24|Internet|
|mi-131-253-6-24-nexthop-internet|131.253.6.0/24|Internet|
|mi-131-253-8-24-nexthop-internet|131.253.8.0/24|Internet|
|mi-131-253-12-22-nexthop-internet|131.253.12.0/22|Internet|
|mi-131-253-16-23-nexthop-internet|131.253.16.0/23|Internet|
|mi-131-253-18-24-nexthop-internet|131.253.18.0/24|Internet|
|mi-131-253-21-24-nexthop-internet|131.253.21.0/24|Internet|
|mi-131-253-22-23-nexthop-internet|131.253.22.0/23|Internet|
|mi-131-253-24-21-nexthop-internet|131.253.24.0/21|Internet|
|mi-131-253-32-20-nexthop-internet|131.253.32.0/20|Internet|
|mi-131-253-61-24-nexthop-internet|131.253.61.0/24|Internet|
|mi-131-253-62-23-nexthop-internet|131.253.62.0/23|Internet|
|mi-131-253-64-18-nexthop-internet|131.253.64.0/18|Internet|
|mi-131-253-128-17-nexthop-internet|131.253.128.0/17|Internet|
|mi-132-245-16-nexthop-internet|132.245.0.0/16|Internet|
|mi-134-170-16-nexthop-internet|134.170.0.0/16|Internet|
|mi-134-177-16-nexthop-internet|134.177.0.0/16|Internet|
|mi-137-116-15-nexthop-internet|137.116.0.0/15|Internet|
|mi-137-135-16-nexthop-internet|137.135.0.0/16|Internet|
|mi-138-91-16-nexthop-internet|138.91.0.0/16|Internet|
|mi-138-196-16-nexthop-internet|138.196.0.0/16|Internet|
|mi-139-217-16-nexthop-internet|139.217.0.0/16|Internet|
|mi-139-219-16-nexthop-internet|139.219.0.0/16|Internet|
|mi-141-251-16-nexthop-internet|141.251.0.0/16|Internet|
|mi-146-147-16-nexthop-internet|146.147.0.0/16|Internet|
|mi-147-243-16-nexthop-internet|147.243.0.0/16|Internet|
|mi-150-171-16-nexthop-internet|150.171.0.0/16|Internet|
|mi-150-242-48-22-nexthop-internet|150.242.48.0/22|Internet|
|mi-157-54-15-nexthop-internet|157.54.0.0/15|Internet|
|mi-157-56-14-nexthop-internet|157.56.0.0/14|Internet|
|mi-157-60-16-nexthop-internet|157.60.0.0/16|Internet|
|mi-167-105-16-nexthop-internet|167.105.0.0/16|Internet|
|mi-167-220-16-nexthop-internet|167.220.0.0/16|Internet|
|mi-168-61-16-nexthop-internet|168.61.0.0/16|Internet|
|mi-168-62-15-nexthop-internet|168.62.0.0/15|Internet|
|mi-191-232-13-nexthop-internet|191.232.0.0/13|Internet|
|mi-192-32-16-nexthop-internet|192.32.0.0/16|Internet|
|mi-192-48-225-24-nexthop-internet|192.48.225.0/24|Internet|
|mi-192-84-159-24-nexthop-internet|192.84.159.0/24|Internet|
|mi-192-84-160-23-nexthop-internet|192.84.160.0/23|Internet|
|mi-192-197-157-24-nexthop-internet|192.197.157.0/24|Internet|
|mi-193-149-64-19-nexthop-internet|193.149.64.0/19|Internet|
|mi-193-221-113-24-nexthop-internet|193.221.113.0/24|Internet|
|mi-194-69-96-19-nexthop-internet|194.69.96.0/19|Internet|
|mi-194-110-197-24-nexthop-internet|194.110.197.0/24|Internet|
|mi-198-105-232-22-nexthop-internet|198.105.232.0/22|Internet|
|mi-198-200-130-24-nexthop-internet|198.200.130.0/24|Internet|
|mi-198-206-164-24-nexthop-internet|198.206.164.0/24|Internet|
|mi-199-60-28-24-nexthop-internet|199.60.28.0/24|Internet|
|mi-199-74-210-24-nexthop-internet|199.74.210.0/24|Internet|
|mi-199-103-90-23-nexthop-internet|199.103.90.0/23|Internet|
|mi-199-103-122-24-nexthop-internet|199.103.122.0/24|Internet|
|mi-199-242-32-20-nexthop-internet|199.242.32.0/20|Internet|
|mi-199-242-48-21-nexthop-internet|199.242.48.0/21|Internet|
|mi-202-89-224-20-nexthop-internet|202.89.224.0/20|Internet|
|mi-204-13-120-21-nexthop-internet|204.13.120.0/21|Internet|
|mi-204-14-180-22-nexthop-internet|204.14.180.0/22|Internet|
|mi-204-79-135-24-nexthop-internet|204.79.135.0/24|Internet|
|mi-204-79-179-24-nexthop-internet|204.79.179.0/24|Internet|
|mi-204-79-181-24-nexthop-internet|204.79.181.0/24|Internet|
|mi-204-79-188-24-nexthop-internet|204.79.188.0/24|Internet|
|mi-204-79-195-24-nexthop-internet|204.79.195.0/24|Internet|
|mi-204-79-196-23-nexthop-internet|204.79.196.0/23|Internet|
|mi-204-79-252-24-nexthop-internet|204.79.252.0/24|Internet|
|mi-204-152-18-23-nexthop-internet|204.152.18.0/23|Internet|
|mi-204-152-140-23-nexthop-internet|204.152.140.0/23|Internet|
|mi-204-231-192-24-nexthop-internet|204.231.192.0/24|Internet|
|mi-204-231-194-23-nexthop-internet|204.231.194.0/23|Internet|
|mi-204-231-197-24-nexthop-internet|204.231.197.0/24|Internet|
|mi-204-231-198-23-nexthop-internet|204.231.198.0/23|Internet|
|mi-204-231-200-21-nexthop-internet|204.231.200.0/21|Internet|
|mi-204-231-208-20-nexthop-internet|204.231.208.0/20|Internet|
|mi-204-231-236-24-nexthop-internet|204.231.236.0/24|Internet|
|mi-205-174-224-20-nexthop-internet|205.174.224.0/20|Internet|
|mi-206-138-168-21-nexthop-internet|206.138.168.0/21|Internet|
|mi-206-191-224-19-nexthop-internet|206.191.224.0/19|Internet|
|mi-207-46-16-nexthop-internet|207.46.0.0/16|Internet|
|mi-207-68-128-18-nexthop-internet|207.68.128.0/18|Internet|
|mi-208-68-136-21-nexthop-internet|208.68.136.0/21|Internet|
|mi-208-76-44-22-nexthop-internet|208.76.44.0/22|Internet|
|mi-208-84-21-nexthop-internet|208.84.0.0/21|Internet|
|mi-209-240-192-19-nexthop-internet|209.240.192.0/19|Internet|
|mi-213-199-128-18-nexthop-internet|213.199.128.0/18|Internet|
|mi-216-32-180-22-nexthop-internet|216.32.180.0/22|Internet|
|mi-216-220-208-20-nexthop-internet|216.220.208.0/20|Internet|
|mi-23-96-13-nexthop-internet|23.96.0.0/13|Internet|
|mi-42-159-16-nexthop-internet|42.159.0.0/16|Internet|
|mi-51-13-17-nexthop-internet|51.13.0.0/17|Internet|
|mi-51-107-16-nexthop-internet|51.107.0.0/16|Internet|
|mi-51-116-16-nexthop-internet|51.116.0.0/16|Internet|
|mi-51-120-16-nexthop-internet|51.120.0.0/16|Internet|
|mi-51-120-128-17-nexthop-internet|51.120.128.0/17|Internet|
|mi-51-124-16-nexthop-internet|51.124.0.0/16|Internet|
|mi-102-37-18-nexthop-internet|102.37.0.0/18|Internet|
|mi-102-133-16-nexthop-internet|102.133.0.0/16|Internet|
|mi-199-30-16-20-nexthop-internet|199.30.16.0/20|Internet|
|mi-204-79-180-24-nexthop-internet|204.79.180.0/24|Internet|
||||

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać omówienie, zobacz [Sql Database advanced data security](sql-database-managed-instance.md).
- Dowiedz się, jak [skonfigurować nową sieć wirtualną platformy Azure](sql-database-managed-instance-create-vnet-subnet.md) lub [istniejącą sieć wirtualną platformy Azure,](sql-database-managed-instance-configure-vnet-subnet.md) w której można wdrażać wystąpienia zarządzane.
- [Oblicz rozmiar podsieci, w](sql-database-managed-instance-determine-size-vnet-subnet.md) której chcesz wdrożyć wystąpienia zarządzane.
- Dowiedz się, jak utworzyć wystąpienie zarządzane:
  - Z [portalu Azure](sql-database-managed-instance-get-started.md).
  - Za pomocą programu [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md).
  - Przy użyciu [szablonu Usługi Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).
  - Przy użyciu [szablonu usługi Azure Resource Manager (przy użyciu JumpBox, z dołączonym SSMS)](https://azure.microsoft.com/resources/templates/201-sqlmi-new-vnet-w-jumpbox/). 
