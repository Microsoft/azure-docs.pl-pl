---
title: Architektura łączności
titleSuffix: Azure SQL Managed Instance
description: Dowiedz się więcej o architekturze komunikacji i architektury łączności wystąpienia zarządzanego Azure SQL oraz o tym, jak składniki kierują ruch do wystąpienia zarządzanego.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 03/17/2020
ms.openlocfilehash: 81d0731f6ea77325b3f33f91bf8d5d1386dab2fb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91283381"
---
# <a name="connectivity-architecture-for-azure-sql-managed-instance"></a>Architektura łączności dla usługi Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

W tym artykule opisano komunikację w wystąpieniu zarządzanym usługi Azure SQL. Opisano w nim również architekturę łączności i sposób, w jaki składniki kierują ruch do wystąpienia zarządzanego.  

Wystąpienie zarządzane SQL jest umieszczane w sieci wirtualnej platformy Azure i podsieć, która jest przeznaczona dla wystąpień zarządzanych. To wdrożenie oferuje następujące informacje:

- Bezpieczny prywatny adres IP.
- Możliwość połączenia sieci lokalnej z wystąpieniem zarządzanym SQL.
- Możliwość połączenia wystąpienia zarządzanego SQL z serwerem połączonym lub innym lokalnym magazynem danych.
- Możliwość połączenia wystąpienia zarządzanego SQL z zasobami platformy Azure.

## <a name="communication-overview"></a>Omówienie komunikacji

Na poniższym diagramie przedstawiono jednostki, które łączą się z wystąpieniem zarządzanym SQL. Przedstawiono w nim również zasoby, które muszą komunikować się z wystąpieniem zarządzanym. Proces komunikacji w dolnej części diagramu reprezentuje aplikacje i narzędzia klienta, które łączą się z wystąpieniem zarządzanym SQL jako źródłami danych.  

![Jednostki w architekturze łączności](./media/connectivity-architecture-overview/connectivityarch001.png)

Wystąpienie zarządzane SQL to oferta platformy jako usługi (PaaS). Platforma Azure używa zautomatyzowanych agentów (zarządzania, wdrażania i konserwacji) do zarządzania tą usługą w oparciu o strumienie danych telemetrii. Ponieważ platforma Azure jest odpowiedzialna za zarządzanie, klienci nie mogą uzyskać dostępu do maszyn wirtualnych klastra wystąpienia zarządzanego SQL za pomocą Remote Desktop Protocol (RDP).

Niektóre operacje uruchomione przez użytkowników końcowych lub aplikacje mogą wymagać wystąpienia zarządzanego SQL w celu współdziałania z platformą. Jednym z przypadków jest utworzenie bazy danych wystąpienia zarządzanego SQL. Ten zasób jest dostępny za pomocą Azure Portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure i interfejsu API REST.

Wystąpienie zarządzane SQL jest zależne od usług platformy Azure, takich jak Azure Storage, na potrzeby tworzenia kopii zapasowych, Event Hubs platformy Azure do obsługi telemetrii, Azure Active Directory (Azure AD) do uwierzytelniania, Azure Key Vault dla Transparent Data Encryption (TDE) oraz kilka usług platformy Azure, które zapewniają funkcje zabezpieczeń i obsługi. Wystąpienie zarządzane SQL udostępnia połączenia z tymi usługami.

Cała komunikacja jest zaszyfrowana i podpisana przy użyciu certyfikatów. Aby sprawdzić wiarygodność komunikujących się stron, wystąpienie zarządzane SQL ciągle weryfikuje te certyfikaty za pomocą list odwołania certyfikatów. Jeśli certyfikaty zostaną odwołane, wystąpienie zarządzane SQL zamknie połączenia w celu ochrony danych.

## <a name="high-level-connectivity-architecture"></a>Architektura łączności wysokiego poziomu

Na wysokim poziomie wystąpienie zarządzane SQL jest zestawem składników usługi. Te składniki są hostowane w dedykowanym zestawie izolowanych maszyn wirtualnych, które działają w podsieci sieci wirtualnej klienta. Te maszyny tworzą klaster wirtualny.

Klaster wirtualny może obsługiwać wiele wystąpień zarządzanych. W razie konieczności klaster automatycznie rozszerza lub kontraktuje, gdy klient zmieni liczbę wystąpień zainicjowanych w podsieci.

Aplikacje klienta mogą łączyć się z wystąpieniem zarządzanym SQL, a także wysyłać zapytania i aktualizować bazy danych wewnątrz sieci wirtualnej, równorzędnej sieci wirtualnej lub sieci połączonej przez sieć VPN lub usługę Azure ExpressRoute. Ta sieć musi używać punktu końcowego i prywatnego adresu IP.  

![Diagram architektury łączności](./media/connectivity-architecture-overview/connectivityarch002.png)

Usługi zarządzania i wdrażania platformy Azure działają poza siecią wirtualną. Wystąpienia zarządzane SQL i usługi platformy Azure łączą się za pośrednictwem punktów końcowych, które mają publiczne adresy IP. Gdy wystąpienie zarządzane SQL tworzy połączenie wychodzące, na adresie IP, który otrzyma translacji adresów sieciowych (NAT), sprawia, że połączenie będzie wyglądać tak, jak pochodzi z tego publicznego adresu.

Ruch związany z zarządzaniem odbywa się za pomocą sieci wirtualnej klienta. Oznacza to, że elementy infrastruktury sieci wirtualnej mogą uszkodzić ruch związany z zarządzaniem, co sprawia, że wystąpienie nie powiedzie się i stanie się niedostępne.

> [!IMPORTANT]
> Aby ulepszyć obsługę klienta i dostępność usług, platforma Azure stosuje zasady dotyczące opcji sieci w elementach infrastruktury sieci wirtualnej platformy Azure. Zasady mogą mieć wpływ na sposób działania wystąpienia zarządzanego SQL. Ten mechanizm platformy w sposób przezroczysty komunikuje wymagania sieciowe z użytkownikami. Głównym celem zasad jest uniknięcie nieprawidłowej konfiguracji sieci i zapewnienia normalnych operacji wystąpienia zarządzanego SQL. Usunięcie wystąpienia zarządzanego spowoduje również usunięcie zasad dotyczących opcji sieci.

## <a name="virtual-cluster-connectivity-architecture"></a>Architektura łączności klastra wirtualnego

Przyjrzyjmy się bardziej szczegółowo szczegółowe architektury łączności dla wystąpienia zarządzanego SQL. Na poniższym diagramie przedstawiono układ koncepcyjny klastra wirtualnego.

![Architektura łączności klastra wirtualnego](./media/connectivity-architecture-overview/connectivityarch003.png)

Klienci łączą się z wystąpieniem zarządzanym SQL przy użyciu nazwy hosta, która ma formularz `<mi_name>.<dns_zone>.database.windows.net` . Ta nazwa hosta jest rozpoznawana jako prywatny adres IP, mimo że jest zarejestrowana w publicznej strefie systemu nazw domen (DNS) i jest publicznie rozpoznawalna. `zone-id`Jest generowany automatycznie podczas tworzenia klastra. Jeśli nowo utworzony klaster obsługuje pomocnicze wystąpienie zarządzane, jego identyfikator strefy jest udostępniany klastrowi podstawowemu. Aby uzyskać więcej informacji, zobacz [Korzystanie z grup autotrybu failover w celu zapewnienia przezroczystej i skoordynowanej pracy w trybie failover wielu baz danych](../database/auto-failover-group-overview.md#enabling-geo-replication-between-managed-instances-and-their-vnets).

Ten prywatny adres IP należy do wewnętrznego modułu równoważenia obciążenia dla wystąpienia zarządzanego SQL. Moduł równoważenia obciążenia kieruje ruch do bramy wystąpienia zarządzanego SQL. Ponieważ wiele wystąpień zarządzanych może działać w tym samym klastrze, brama używa nazwy hosta wystąpienia zarządzanego SQL do przekierowywania ruchu do poprawnej usługi aparatu SQL.

Usługi zarządzania i wdrażania nawiązują połączenie z wystąpieniem zarządzanym SQL przy użyciu [punktu końcowego zarządzania](#management-endpoint) , który jest mapowany na zewnętrzny moduł równoważenia obciążenia. Ruch jest kierowany do węzłów tylko wtedy, gdy jest odbierany we wstępnie zdefiniowanym zestawie portów, które są używane tylko przez składniki zarządzania wystąpienia zarządzanego SQL. Wbudowana zapora w węzłach jest skonfigurowana tak, aby zezwalała na ruch tylko z zakresów adresów IP firmy Microsoft. Certyfikaty wzajemnie uwierzytelniają całą komunikację między składnikami zarządzania a płaszczyzną zarządzania.

## <a name="management-endpoint"></a>Punkt końcowy zarządzania

Platforma Azure zarządza wystąpieniem zarządzanym SQL przy użyciu punktu końcowego zarządzania. Ten punkt końcowy znajduje się w klastrze wirtualnym wystąpienia. Punkt końcowy zarządzania jest chroniony przez wbudowaną zaporę na poziomie sieci. Na poziomie aplikacji jest on chroniony przez wzajemne weryfikację certyfikatu. Aby znaleźć adres IP punktu końcowego, zobacz [Określanie adresu IP punktu końcowego zarządzania](management-endpoint-find-ip-address.md).

Po rozpoczęciu połączeń wewnątrz wystąpienia zarządzanego SQL (podobnie jak w przypadku kopii zapasowych i dzienników inspekcji) ruch zaczyna się od publicznego adresu IP punktu końcowego zarządzania. Możesz ograniczyć dostęp do usług publicznych z wystąpienia zarządzanego SQL, ustawiając reguły zapory tak, aby zezwalały tylko na adres IP dla wystąpienia zarządzanego SQL. Aby uzyskać więcej informacji, zobacz [Weryfikowanie wbudowanej zapory wystąpienia zarządzanego SQL](management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> Ruch kierowany do usług platformy Azure, które znajdują się w regionie wystąpienia zarządzanego SQL, jest zoptymalizowany i z tego powodu nie jest to spowodowane publicznym adresem IP dla punktu końcowego zarządzania. Z tego powodu, jeśli musisz użyć reguł zapory opartych na protokole IP, najczęściej dla magazynu, usługa musi znajdować się w innym regionie niż wystąpienie zarządzane SQL.

## <a name="service-aided-subnet-configuration"></a>Konfiguracja podsieci wspomagana przez usługę

Aby rozwiązać wymagania dotyczące zabezpieczeń i możliwości zarządzania przez klienta, wystąpienie zarządzane SQL przechodzi z ręcznego konfigurowania podsieci z obsługą usługi.

W przypadku konfigurowania podsieci z obsługą usług użytkownik ma pełną kontrolę nad ruchem danych (TDS), natomiast wystąpienie zarządzane SQL jest odpowiedzialne za zapewnienie nieprzerwanego przepływu ruchu związanego z zarządzaniem w celu spełnienia warunków umowy SLA.

Konfiguracja podsieci wspomagana przez usługę jest oparta na funkcji [delegowania podsieci](../../virtual-network/subnet-delegation-overview.md) sieci wirtualnej w celu zapewnienia automatycznego zarządzania konfiguracją sieci i włączania punktów końcowych usługi. 

Punkty końcowe usługi mogą służyć do konfigurowania reguł zapory sieci wirtualnej na kontach magazynu, które przechowują kopie zapasowe i dzienniki inspekcji. Nawet z włączonymi punktami końcowymi usługi klienci są zachęcani do korzystania z [prywatnego linku](../../private-link/private-link-overview.md) zapewniającego dodatkowe zabezpieczenia przed punktami końcowymi usługi.

> [!IMPORTANT]
> Ze względu na specyfikę konfiguracji płaszczyzny kontroli, konfiguracja podsieci z obsługą usług nie powoduje włączenia punktów końcowych usługi w chmurach krajowych. 

### <a name="network-requirements"></a>Wymagania dotyczące sieci

Wdróż wystąpienie zarządzane SQL w dedykowanej podsieci w sieci wirtualnej. Podsieć musi mieć następujące właściwości:

- **Dedykowana podsieć:** Podsieć wystąpienia zarządzanego SQL nie może zawierać żadnej innej usługi w chmurze, która jest skojarzona z nią, i nie może być podsiecią bramy. Podsieć nie może zawierać żadnego zasobu, ale wystąpienia zarządzanego SQL i nie można później dodać innych typów zasobów w podsieci.
- **Delegowanie podsieci:** Podsieć wystąpienia zarządzanego SQL musi być delegowana do `Microsoft.Sql/managedInstances` dostawcy zasobów.
- **Sieciowa Grupa zabezpieczeń (sieciowej grupy zabezpieczeń):** SIECIOWEJ grupy zabezpieczeń musi być skojarzona z podsiecią wystąpienia zarządzanego SQL. Można użyć sieciowej grupy zabezpieczeń, aby kontrolować dostęp do punktu końcowego danych wystąpienia zarządzanego SQL przez filtrowanie ruchu na porcie 1433 i port 11000-11999, gdy wystąpienie zarządzane SQL jest skonfigurowane do przekierowania połączeń. Usługa będzie automatycznie udostępniać i utrzymywać bieżące [reguły](#mandatory-inbound-security-rules-with-service-aided-subnet-configuration) wymagane do umożliwienia nieprzerwanego przepływu ruchu zarządzania.
- **Tabela zdefiniowana przez użytkownika trasa (UDR):** Tabela UDR musi być skojarzona z podsiecią wystąpienia zarządzanego SQL. Można dodać wpisy do tabeli tras, aby skierować ruch, który ma lokalne prywatne zakresy adresów IP jako lokalizację docelową, za pomocą bramy sieci wirtualnej lub wirtualnego urządzenia sieciowego (urządzenia WUS). Usługa będzie automatycznie aprowizować i utrzymywać bieżące wymagane [wpisy](#user-defined-routes-with-service-aided-subnet-configuration), aby umożliwić nieprzerwany przepływ ruchu związanego z zarządzaniem.
- **Wystarczające adresy IP:** Podsieć wystąpienia zarządzanego SQL musi mieć co najmniej 16 adresów IP. Zalecana minimalna liczba adresów IP to 32. Aby uzyskać więcej informacji, zobacz [Określanie rozmiaru podsieci dla wystąpienia zarządzanego SQL](vnet-subnet-determine-size.md). Wystąpienia zarządzane można wdrożyć w [istniejącej sieci](vnet-existing-add-subnet.md) po jej skonfigurowaniu w taki sposób, aby spełniały [wymagania dotyczące sieci dla wystąpienia zarządzanego SQL](#network-requirements). W innym przypadku utwórz [nową sieć i podsieć](virtual-network-subnet-create-arm-template.md).

> [!IMPORTANT]
> Podczas tworzenia wystąpienia zarządzanego w podsieci są stosowane zasady konwersji sieci, które uniemożliwiają niezgodne zmiany konfiguracji sieci. Po usunięciu ostatniego wystąpienia z podsieci zostaną również usunięte zasady dotyczące opcji sieci.

### <a name="mandatory-inbound-security-rules-with-service-aided-subnet-configuration"></a>Obowiązkowe reguły zabezpieczeń dla ruchu przychodzącego z konfiguracją podsieci z obsługą usług

| Nazwa       |Port                        |Protokół|Element źródłowy           |Element docelowy|Akcja|
|------------|----------------------------|--------|-----------------|-----------|------|
|zarządzanie  |9000, 9003, 1438, 1440, 1452|TCP     |Xmlmanagement    |MI PODSIEĆ  |Zezwalaj |
|            |9000, 9003                  |TCP     |CorpnetSaw       |MI PODSIEĆ  |Zezwalaj |
|            |9000, 9003                  |TCP     |CorpnetPublic    |MI PODSIEĆ  |Zezwalaj |
|mi_subnet   |Dowolne                         |Dowolne     |MI PODSIEĆ        |MI PODSIEĆ  |Zezwalaj |
|health_probe|Dowolne                         |Dowolne     |AzureLoadBalancer|MI PODSIEĆ  |Zezwalaj |

### <a name="mandatory-outbound-security-rules-with-service-aided-subnet-configuration"></a>Obowiązkowe reguły zabezpieczeń dla ruchu wychodzącego z konfiguracją podsieci z obsługą usług

| Nazwa       |Port          |Protokół|Element źródłowy           |Element docelowy|Akcja|
|------------|--------------|--------|-----------------|-----------|------|
|zarządzanie  |443, 12000    |TCP     |MI PODSIEĆ        |AzureCloud |Zezwalaj |
|mi_subnet   |Dowolne           |Dowolne     |MI PODSIEĆ        |MI PODSIEĆ  |Zezwalaj |

### <a name="user-defined-routes-with-service-aided-subnet-configuration"></a>Trasy zdefiniowane przez użytkownika z konfiguracją podsieci z obsługą usług

|Nazwa|Prefiks adresu|Narzędzie Następny przeskok|
|----|--------------|-------|
|podsieć do vnetlocal|MI PODSIEĆ|Sieć wirtualna|
|mi-13-64-11-skoku — Internet|13.64.0.0/11|Internet|
|mi-13-104-14-skoku — Internet|13.104.0.0/14|Internet|
|mi-20-33-16-skoku — Internet|20.33.0.0/16|Internet|
|mi-20-34-15-skoku — Internet|20.34.0.0/15|Internet|
|mi-20-36-14-skoku — Internet|20.36.0.0/14|Internet|
|mi-20-40-13-skoku — Internet|20.40.0.0/13|Internet|
|mi-20-48-12-skoku — Internet|20.48.0.0/12|Internet|
|mi-20-64-10-skoku — Internet|20.64.0.0/10|Internet|
|mi-20-128-16-skoku — Internet|20.128.0.0/16|Internet|
|mi-20-135-16-skoku — Internet|20.135.0.0/16|Internet|
|mi-20-136-16-skoku — Internet|20.136.0.0/16|Internet|
|mi-20-140-15-skoku — Internet|20.140.0.0/15|Internet|
|mi-20-143-16-skoku — Internet|20.143.0.0/16|Internet|
|mi-20-144-14-skoku — Internet|20.144.0.0/14|Internet|
|mi-20-150-15-skoku — Internet|20.150.0.0/15|Internet|
|mi-20-160-12-skoku — Internet|20.160.0.0/12|Internet|
|mi-20-176-14-skoku — Internet|20.176.0.0/14|Internet|
|mi-20-180-14-skoku — Internet|20.180.0.0/14|Internet|
|mi-20-184-13-skoku — Internet|20.184.0.0/13|Internet|
|mi-20-192-10-skoku — Internet|20.192.0.0/10|Internet|
|mi-40-64-10-skoku — Internet|40.64.0.0/10|Internet|
|mi-51-4-15-skoku — Internet|51.4.0.0/15|Internet|
|mi-51-8-16-skoku — Internet|51.8.0.0/16|Internet|
|mi-51-10-15-skoku — Internet|51.10.0.0/15|Internet|
|mi-51-18-16-skoku — Internet|51.18.0.0/16|Internet|
|mi-51-51-16-skoku — Internet|51.51.0.0/16|Internet|
|mi-51-53-16-skoku — Internet|51.53.0.0/16|Internet|
|mi-51-103-16-skoku — Internet|51.103.0.0/16|Internet|
|mi-51-104-15-skoku — Internet|51.104.0.0/15|Internet|
|mi-51-132-16-skoku — Internet|51.132.0.0/16|Internet|
|mi-51-136-15-skoku — Internet|51.136.0.0/15|Internet|
|mi-51-138-16-skoku — Internet|51.138.0.0/16|Internet|
|mi-51-140-14-skoku — Internet|51.140.0.0/14|Internet|
|mi-51-144-15-skoku — Internet|51.144.0.0/15|Internet|
|mi-52-96-12-skoku — Internet|52.96.0.0/12|Internet|
|mi-52-112-14-skoku — Internet|52.112.0.0/14|Internet|
|mi-52-125-16-skoku — Internet|52.125.0.0/16|Internet|
|mi-52-126-15-skoku — Internet|52.126.0.0/15|Internet|
|mi-52-130-15-skoku — Internet|52.130.0.0/15|Internet|
|mi-52-132-14-skoku — Internet|52.132.0.0/14|Internet|
|mi-52-136-13-skoku — Internet|52.136.0.0/13|Internet|
|mi-52-145-16-skoku — Internet|52.145.0.0/16|Internet|
|mi-52-146-15-skoku — Internet|52.146.0.0/15|Internet|
|mi-52-148-14-skoku — Internet|52.148.0.0/14|Internet|
|mi-52-152-13-skoku — Internet|52.152.0.0/13|Internet|
|mi-52-160-11-skoku — Internet|52.160.0.0/11|Internet|
|mi-52-224-11-skoku — Internet|52.224.0.0/11|Internet|
|mi-64-4-18-skoku — Internet|64.4.0.0/18|Internet|
|mi-65-52-14-skoku — Internet|65.52.0.0/14|Internet|
|mi-66-119-144-20-skoku — Internet|66.119.144.0/20|Internet|
|mi-70-37-17-skoku — Internet|70.37.0.0/17|Internet|
|mi-70-37-128-18-skoku — Internet|70.37.128.0/18|Internet|
|mi-91-190-216-21-skoku — Internet|91.190.216.0/21|Internet|
|mi-94-245-64-18-skoku — Internet|94.245.64.0/18|Internet|
|mi-103-9-8-22-skoku — Internet|103.9.8.0/22|Internet|
|mi-103-25-156-24-skoku — Internet|103.25.156.0/24|Internet|
|mi-103-25-157-24-skoku — Internet|103.25.157.0/24|Internet|
|mi-103-25-158-23-skoku — Internet|103.25.158.0/23|Internet|
|mi-103-36-96-22-skoku — Internet|103.36.96.0/22|Internet|
|mi-103-255-140-22-skoku — Internet|103.255.140.0/22|Internet|
|mi-104-40-13-skoku — Internet|104.40.0.0/13|Internet|
|mi-104-146-15-skoku — Internet|104.146.0.0/15|Internet|
|mi-104-208-13-skoku — Internet|104.208.0.0/13|Internet|
|mi-111-221-16-20-skoku — Internet|111.221.16.0/20|Internet|
|mi-111-221-64-18-skoku — Internet|111.221.64.0/18|Internet|
|mi-129-75-16-skoku — Internet|129.75.0.0/16|Internet|
|mi-131-107-16-skoku — Internet|131.107.0.0/16|Internet|
|mi-131-253-1-24-skoku — Internet|131.253.1.0/24|Internet|
|mi-131-253-3-24-skoku — Internet|131.253.3.0/24|Internet|
|mi-131-253-5-24-skoku — Internet|131.253.5.0/24|Internet|
|mi-131-253-6-24-skoku — Internet|131.253.6.0/24|Internet|
|mi-131-253-8-24-skoku — Internet|131.253.8.0/24|Internet|
|mi-131-253-12-22-skoku — Internet|131.253.12.0/22|Internet|
|mi-131-253-16-23-skoku — Internet|131.253.16.0/23|Internet|
|mi-131-253-18-24-skoku — Internet|131.253.18.0/24|Internet|
|mi-131-253-21-24-skoku — Internet|131.253.21.0/24|Internet|
|mi-131-253-22-23-skoku — Internet|131.253.22.0/23|Internet|
|mi-131-253-24-21-skoku — Internet|131.253.24.0/21|Internet|
|mi-131-253-32-20-skoku — Internet|131.253.32.0/20|Internet|
|mi-131-253-61-24-skoku — Internet|131.253.61.0/24|Internet|
|mi-131-253-62-23-skoku — Internet|131.253.62.0/23|Internet|
|mi-131-253-64-18-skoku — Internet|131.253.64.0/18|Internet|
|mi-131-253-128-17-skoku — Internet|131.253.128.0/17|Internet|
|mi-132-245-16-skoku — Internet|132.245.0.0/16|Internet|
|mi-134-170-16-skoku — Internet|134.170.0.0/16|Internet|
|mi-134-177-16-skoku — Internet|134.177.0.0/16|Internet|
|mi-137-116-15-skoku — Internet|137.116.0.0/15|Internet|
|mi-137-135-16-skoku — Internet|137.135.0.0/16|Internet|
|mi-138-91-16-skoku — Internet|138.91.0.0/16|Internet|
|mi-138-196-16-skoku — Internet|138.196.0.0/16|Internet|
|mi-139-217-16-skoku — Internet|139.217.0.0/16|Internet|
|mi-139-219-16-skoku — Internet|139.219.0.0/16|Internet|
|mi-141-251-16-skoku — Internet|141.251.0.0/16|Internet|
|mi-146-147-16-skoku — Internet|146.147.0.0/16|Internet|
|mi-147-243-16-skoku — Internet|147.243.0.0/16|Internet|
|mi-150-171-16-skoku — Internet|150.171.0.0/16|Internet|
|mi-150-242-48-22-skoku — Internet|150.242.48.0/22|Internet|
|mi-157-54-15-skoku — Internet|157.54.0.0/15|Internet|
|mi-157-56-14-skoku — Internet|157.56.0.0/14|Internet|
|mi-157-60-16-skoku — Internet|157.60.0.0/16|Internet|
|mi-167-105-16-skoku — Internet|167.105.0.0/16|Internet|
|mi-167-220-16-skoku — Internet|167.220.0.0/16|Internet|
|mi-168-61-16-skoku — Internet|168.61.0.0/16|Internet|
|mi-168-62-15-skoku — Internet|168.62.0.0/15|Internet|
|mi-191-232-13-skoku — Internet|191.232.0.0/13|Internet|
|mi-192-32-16-skoku — Internet|192.32.0.0/16|Internet|
|mi-192-48-225-24-skoku — Internet|192.48.225.0/24|Internet|
|mi-192-84-159-24-skoku — Internet|192.84.159.0/24|Internet|
|mi-192-84-160-23-skoku — Internet|192.84.160.0/23|Internet|
|mi-192-197-157-24-skoku — Internet|192.197.157.0/24|Internet|
|mi-193-149-64-19-skoku — Internet|193.149.64.0/19|Internet|
|mi-193-221-113-24-skoku — Internet|193.221.113.0/24|Internet|
|mi-194-69-96-19-skoku — Internet|194.69.96.0/19|Internet|
|mi-194-110-197-24-skoku — Internet|194.110.197.0/24|Internet|
|mi-198-105-232-22-skoku — Internet|198.105.232.0/22|Internet|
|mi-198-200-130-24-skoku — Internet|198.200.130.0/24|Internet|
|mi-198-206-164-24-skoku — Internet|198.206.164.0/24|Internet|
|mi-199-60-28-24-skoku — Internet|199.60.28.0/24|Internet|
|mi-199-74-210-24-skoku — Internet|199.74.210.0/24|Internet|
|mi-199-103-90-23-skoku — Internet|199.103.90.0/23|Internet|
|mi-199-103-122-24-skoku — Internet|199.103.122.0/24|Internet|
|mi-199-242-32-20-skoku — Internet|199.242.32.0/20|Internet|
|mi-199-242-48-21-skoku — Internet|199.242.48.0/21|Internet|
|mi-202-89-224-20-skoku — Internet|202.89.224.0/20|Internet|
|mi-204-13-120-21-skoku — Internet|204.13.120.0/21|Internet|
|mi-204-14-180-22-skoku — Internet|204.14.180.0/22|Internet|
|mi-204-79-135-24-skoku — Internet|204.79.135.0/24|Internet|
|mi-204-79-179-24-skoku — Internet|204.79.179.0/24|Internet|
|mi-204-79-181-24-skoku — Internet|204.79.181.0/24|Internet|
|mi-204-79-188-24-skoku — Internet|204.79.188.0/24|Internet|
|mi-204-79-195-24-skoku — Internet|204.79.195.0/24|Internet|
|mi-204-79-196-23-skoku — Internet|204.79.196.0/23|Internet|
|mi-204-79-252-24-skoku — Internet|204.79.252.0/24|Internet|
|mi-204-152-18-23-skoku — Internet|204.152.18.0/23|Internet|
|mi-204-152-140-23-skoku — Internet|204.152.140.0/23|Internet|
|mi-204-231-192-24-skoku — Internet|204.231.192.0/24|Internet|
|mi-204-231-194-23-skoku — Internet|204.231.194.0/23|Internet|
|mi-204-231-197-24-skoku — Internet|204.231.197.0/24|Internet|
|mi-204-231-198-23-skoku — Internet|204.231.198.0/23|Internet|
|mi-204-231-200-21-skoku — Internet|204.231.200.0/21|Internet|
|mi-204-231-208-20-skoku — Internet|204.231.208.0/20|Internet|
|mi-204-231-236-24-skoku — Internet|204.231.236.0/24|Internet|
|mi-205-174-224-20-skoku — Internet|205.174.224.0/20|Internet|
|mi-206-138-168-21-skoku — Internet|206.138.168.0/21|Internet|
|mi-206-191-224-19-skoku — Internet|206.191.224.0/19|Internet|
|mi-207-46-16-skoku — Internet|207.46.0.0/16|Internet|
|mi-207-68-128-18-skoku — Internet|207.68.128.0/18|Internet|
|mi-208-68-136-21-skoku — Internet|208.68.136.0/21|Internet|
|mi-208-76-44-22-skoku — Internet|208.76.44.0/22|Internet|
|mi-208-84-21-skoku — Internet|208.84.0.0/21|Internet|
|mi-209-240-192-19-skoku — Internet|209.240.192.0/19|Internet|
|mi-213-199-128-18-skoku — Internet|213.199.128.0/18|Internet|
|mi-216-32-180-22-skoku — Internet|216.32.180.0/22|Internet|
|mi-216-220-208-20-skoku — Internet|216.220.208.0/20|Internet|
|mi-23-96-13-skoku — Internet|23.96.0.0/13|Internet|
|mi-42-159-16-skoku — Internet|42.159.0.0/16|Internet|
|mi-51-13-17-skoku — Internet|51.13.0.0/17|Internet|
|mi-51-107-16-skoku — Internet|51.107.0.0/16|Internet|
|mi-51-116-16-skoku — Internet|51.116.0.0/16|Internet|
|mi-51-120-16-skoku — Internet|51.120.0.0/16|Internet|
|mi-51-120-128-17-skoku — Internet|51.120.128.0/17|Internet|
|mi-51-124-16-skoku — Internet|51.124.0.0/16|Internet|
|mi-102-37-18-skoku — Internet|102.37.0.0/18|Internet|
|mi-102-133-16-skoku — Internet|102.133.0.0/16|Internet|
|mi-199-30-16-20-skoku — Internet|199.30.16.0/20|Internet|
|mi-204-79-180-24-skoku — Internet|204.79.180.0/24|Internet|
||||

\* Podsieć MI odnosi się do zakresu adresów IP podsieci w postaci x. x. x. x/y. Te informacje można znaleźć w Azure Portal, we właściwościach podsieci.

Ponadto można dodać pozycje do tabeli tras, aby skierować ruch, który ma lokalne prywatne zakresy adresów IP jako miejsce docelowe za pomocą bramy sieci wirtualnej lub urządzenia sieci wirtualnej (urządzenie WUS).

Jeśli sieć wirtualna zawiera niestandardowy system DNS, niestandardowy serwer DNS musi być w stanie rozpoznać publiczne rekordy DNS. Korzystanie z dodatkowych funkcji, takich jak uwierzytelnianie w usłudze Azure AD, może wymagać rozpoznawania dodatkowych nazw FQDN. Aby uzyskać więcej informacji, zobacz [Konfigurowanie niestandardowego serwera DNS](custom-dns-configure.md).

### <a name="networking-constraints"></a>Ograniczenia sieci

Protokoły **tls 1,2 są wymuszane dla połączeń wychodzących**: w styczniu 2020 firma Microsoft wymusi TLS 1,2 dla ruchu wewnątrz usługi we wszystkich usługach platformy Azure. W przypadku wystąpienia zarządzanego usługi Azure SQL nastąpiło wymuszenie protokołu TLS 1,2 dla połączeń wychodzących używanych na potrzeby replikacji i połączeń połączonych z serwerem do SQL Server. Jeśli używasz wersji SQL Server starszej niż 2016 z wystąpieniem zarządzanym SQL, upewnij się, że zastosowano [określone aktualizacje protokołu TLS 1,2](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) .

Następujące funkcje sieci wirtualnej nie są obecnie obsługiwane w przypadku wystąpienia zarządzanego SQL:

- **Komunikacja równorzędna firmy Microsoft**: włączenie komunikacji [równorzędnej firmy Microsoft](../../expressroute/expressroute-faqs.md#microsoft-peering) w obwodach usługi ExpressRoute bezpośrednio lub przechodniej z siecią wirtualną, w której znajduje się wystąpienie zarządzane SQL, wpływa na przepływ ruchu między składnikami wystąpienia zarządzanego SQL w sieci wirtualnej i usług, od których jest zależna, co powoduje problemy z dostępnością. Wdrożenia wystąpienia zarządzanego SQL w sieci wirtualnej z usługą komunikacji równorzędnej firmy Microsoft są już niepowodzeniem.
- **Globalna komunikacja równorzędna sieci wirtualnych**: połączenie [komunikacji równorzędnej sieci wirtualnej](../../virtual-network/virtual-network-peering-overview.md) w regionach platformy Azure nie działa dla wystąpienia zarządzanego SQL z powodu [udokumentowanych ograniczeń modułu równoważenia obciążenia](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers).
- **AzurePlatformDNS**: użycie [znacznika usługi](../../virtual-network/service-tags-overview.md) AzurePlatformDNS do blokowania rozpoznawania nazw DNS platformy spowoduje, że wystąpienie zarządzane SQL nie jest dostępne. Chociaż zarządzane przez klienta wystąpienie systemu DNS obsługuje rozpoznawanie nazw DNS w ramach aparatu, istnieje zależność od systemu DNS platformy dla operacji na platformie.
- **Brama translatora adresów sieciowych**: użycie [usługi Azure Virtual Network NAT](../../virtual-network/nat-overview.md) do kontrolowania łączności wychodzącej z określonym publicznym adresem IP spowoduje, że wystąpienie zarządzane SQL nie jest dostępne. Usługa wystąpienia zarządzanego SQL jest obecnie ograniczona do korzystania z podstawowego modułu równoważenia obciążenia, który nie zapewnia współistnienia przepływów ruchu przychodzącego i wychodzącego z Virtual Network translatora adresów sieciowych.

### <a name="deprecated-network-requirements-without-service-aided-subnet-configuration"></a>Przestarzałe Wymagania sieci bez konfiguracji podsieci z obsługą usług

Wdróż wystąpienie zarządzane SQL w dedykowanej podsieci w sieci wirtualnej. Podsieć musi mieć następujące właściwości:

- **Dedykowana podsieć:** Podsieć wystąpienia zarządzanego SQL nie może zawierać żadnej innej usługi w chmurze, która jest skojarzona z nią, i nie może być podsiecią bramy. Podsieć nie może zawierać żadnego zasobu, ale wystąpienia zarządzanego SQL i nie można później dodać innych typów zasobów w podsieci.
- **Sieciowa Grupa zabezpieczeń (sieciowej grupy zabezpieczeń):** SIECIOWEJ grupy zabezpieczeń, który jest skojarzony z siecią wirtualną, musi definiować [reguły zabezpieczeń dla ruchu przychodzącego](#mandatory-inbound-security-rules) i [reguły zabezpieczeń dla ruchu wychodzącego](#mandatory-outbound-security-rules) przed innymi regułami. Można użyć sieciowej grupy zabezpieczeń, aby kontrolować dostęp do punktu końcowego danych wystąpienia zarządzanego SQL przez filtrowanie ruchu na porcie 1433 i port 11000-11999, gdy wystąpienie zarządzane SQL jest skonfigurowane do przekierowania połączeń.
- **Tabela zdefiniowana przez użytkownika trasa (UDR):** Tabela UDR, która jest skojarzona z siecią wirtualną, musi zawierać określone [wpisy](#user-defined-routes).
- **Brak punktów końcowych usługi:** Z podsiecią wystąpienia zarządzanego SQL nie powinien być skojarzony żaden punkt końcowy usługi. Upewnij się, że opcja punkty końcowe usługi jest wyłączona podczas tworzenia sieci wirtualnej.
- **Wystarczające adresy IP:** Podsieć wystąpienia zarządzanego SQL musi mieć co najmniej 16 adresów IP. Zalecana minimalna liczba adresów IP to 32. Aby uzyskać więcej informacji, zobacz [Określanie rozmiaru podsieci dla wystąpienia zarządzanego SQL](vnet-subnet-determine-size.md). Wystąpienia zarządzane można wdrożyć w [istniejącej sieci](vnet-existing-add-subnet.md) po jej skonfigurowaniu w taki sposób, aby spełniały [wymagania dotyczące sieci dla wystąpienia zarządzanego SQL](#network-requirements). W innym przypadku utwórz [nową sieć i podsieć](virtual-network-subnet-create-arm-template.md).

> [!IMPORTANT]
> Nie można wdrożyć nowego wystąpienia zarządzanego, jeśli podsieć docelowa nie ma tych cech. Podczas tworzenia wystąpienia zarządzanego w podsieci są stosowane zasady konwersji sieci, które uniemożliwiają niezgodne zmiany konfiguracji sieci. Po usunięciu ostatniego wystąpienia z podsieci zostaną również usunięte zasady dotyczące opcji sieci.

### <a name="mandatory-inbound-security-rules"></a>Obowiązkowe reguły zabezpieczeń dla ruchu przychodzącego

| Nazwa       |Port                        |Protokół|Element źródłowy           |Element docelowy|Akcja|
|------------|----------------------------|--------|-----------------|-----------|------|
|zarządzanie  |9000, 9003, 1438, 1440, 1452|TCP     |Dowolne              |MI PODSIEĆ  |Zezwalaj |
|mi_subnet   |Dowolne                         |Dowolne     |MI PODSIEĆ        |MI PODSIEĆ  |Zezwalaj |
|health_probe|Dowolne                         |Dowolne     |AzureLoadBalancer|MI PODSIEĆ  |Zezwalaj |

### <a name="mandatory-outbound-security-rules"></a>Obowiązkowe reguły zabezpieczeń dla ruchu wychodzącego

| Nazwa       |Port          |Protokół|Element źródłowy           |Element docelowy|Akcja|
|------------|--------------|--------|-----------------|-----------|------|
|zarządzanie  |443, 12000    |TCP     |MI PODSIEĆ        |AzureCloud |Zezwalaj |
|mi_subnet   |Dowolne           |Dowolne     |MI PODSIEĆ        |MI PODSIEĆ  |Zezwalaj |

> [!IMPORTANT]
> Upewnij się, że istnieje tylko jedna Reguła ruchu przychodzącego dla portów 9000, 9003, 1438, 1440 i 1452 oraz jednej reguły ruchu wychodzącego dla portów 443 i 12000. Inicjowanie obsługi wystąpień zarządzanych przez program SQL za Azure Resource Manager wdrożeń nie powiedzie się, jeśli reguły ruchu przychodzącego i wychodzącego są konfigurowane osobno dla każdego portu. Jeśli te porty są w oddzielnych regułach, wdrożenie zakończy się niepowodzeniem z kodem błędu `VnetSubnetConflictWithIntendedPolicy` .

\* Podsieć MI odnosi się do zakresu adresów IP podsieci w postaci x. x. x. x/y. Te informacje można znaleźć w Azure Portal, we właściwościach podsieci.

> [!IMPORTANT]
> Chociaż wymagane reguły zabezpieczeń dla ruchu przychodzącego zezwalają na ruch z _dowolnego_ źródła na portach 9000, 9003, 1438, 1440 i 1452, te porty są chronione przez wbudowaną zaporę. Aby uzyskać więcej informacji, zobacz [Określanie adresu punktu końcowego zarządzania](management-endpoint-find-ip-address.md).

> [!NOTE]
> W przypadku korzystania z replikacji transakcyjnej w wystąpieniu zarządzanym SQL, a jeśli jako Wydawca lub dystrybutora jest używana jakakolwiek baza danych wystąpienia, otwórz port 445 (ruch wychodzący TCP) w regułach zabezpieczeń podsieci. Ten port zezwoli na dostęp do udziału plików platformy Azure.

### <a name="user-defined-routes"></a>Trasy zdefiniowane przez użytkownika

|Nazwa|Prefiks adresu|Narzędzie Następny przeskok|
|----|--------------|-------|
|subnet_to_vnetlocal|MI PODSIEĆ|Sieć wirtualna|
|mi-13-64-11-skoku — Internet|13.64.0.0/11|Internet|
|mi-13-104-14-skoku — Internet|13.104.0.0/14|Internet|
|mi-20-33-16-skoku — Internet|20.33.0.0/16|Internet|
|mi-20-34-15-skoku — Internet|20.34.0.0/15|Internet|
|mi-20-36-14-skoku — Internet|20.36.0.0/14|Internet|
|mi-20-40-13-skoku — Internet|20.40.0.0/13|Internet|
|mi-20-48-12-skoku — Internet|20.48.0.0/12|Internet|
|mi-20-64-10-skoku — Internet|20.64.0.0/10|Internet|
|mi-20-128-16-skoku — Internet|20.128.0.0/16|Internet|
|mi-20-135-16-skoku — Internet|20.135.0.0/16|Internet|
|mi-20-136-16-skoku — Internet|20.136.0.0/16|Internet|
|mi-20-140-15-skoku — Internet|20.140.0.0/15|Internet|
|mi-20-143-16-skoku — Internet|20.143.0.0/16|Internet|
|mi-20-144-14-skoku — Internet|20.144.0.0/14|Internet|
|mi-20-150-15-skoku — Internet|20.150.0.0/15|Internet|
|mi-20-160-12-skoku — Internet|20.160.0.0/12|Internet|
|mi-20-176-14-skoku — Internet|20.176.0.0/14|Internet|
|mi-20-180-14-skoku — Internet|20.180.0.0/14|Internet|
|mi-20-184-13-skoku — Internet|20.184.0.0/13|Internet|
|mi-20-192-10-skoku — Internet|20.192.0.0/10|Internet|
|mi-40-64-10-skoku — Internet|40.64.0.0/10|Internet|
|mi-51-4-15-skoku — Internet|51.4.0.0/15|Internet|
|mi-51-8-16-skoku — Internet|51.8.0.0/16|Internet|
|mi-51-10-15-skoku — Internet|51.10.0.0/15|Internet|
|mi-51-18-16-skoku — Internet|51.18.0.0/16|Internet|
|mi-51-51-16-skoku — Internet|51.51.0.0/16|Internet|
|mi-51-53-16-skoku — Internet|51.53.0.0/16|Internet|
|mi-51-103-16-skoku — Internet|51.103.0.0/16|Internet|
|mi-51-104-15-skoku — Internet|51.104.0.0/15|Internet|
|mi-51-132-16-skoku — Internet|51.132.0.0/16|Internet|
|mi-51-136-15-skoku — Internet|51.136.0.0/15|Internet|
|mi-51-138-16-skoku — Internet|51.138.0.0/16|Internet|
|mi-51-140-14-skoku — Internet|51.140.0.0/14|Internet|
|mi-51-144-15-skoku — Internet|51.144.0.0/15|Internet|
|mi-52-96-12-skoku — Internet|52.96.0.0/12|Internet|
|mi-52-112-14-skoku — Internet|52.112.0.0/14|Internet|
|mi-52-125-16-skoku — Internet|52.125.0.0/16|Internet|
|mi-52-126-15-skoku — Internet|52.126.0.0/15|Internet|
|mi-52-130-15-skoku — Internet|52.130.0.0/15|Internet|
|mi-52-132-14-skoku — Internet|52.132.0.0/14|Internet|
|mi-52-136-13-skoku — Internet|52.136.0.0/13|Internet|
|mi-52-145-16-skoku — Internet|52.145.0.0/16|Internet|
|mi-52-146-15-skoku — Internet|52.146.0.0/15|Internet|
|mi-52-148-14-skoku — Internet|52.148.0.0/14|Internet|
|mi-52-152-13-skoku — Internet|52.152.0.0/13|Internet|
|mi-52-160-11-skoku — Internet|52.160.0.0/11|Internet|
|mi-52-224-11-skoku — Internet|52.224.0.0/11|Internet|
|mi-64-4-18-skoku — Internet|64.4.0.0/18|Internet|
|mi-65-52-14-skoku — Internet|65.52.0.0/14|Internet|
|mi-66-119-144-20-skoku — Internet|66.119.144.0/20|Internet|
|mi-70-37-17-skoku — Internet|70.37.0.0/17|Internet|
|mi-70-37-128-18-skoku — Internet|70.37.128.0/18|Internet|
|mi-91-190-216-21-skoku — Internet|91.190.216.0/21|Internet|
|mi-94-245-64-18-skoku — Internet|94.245.64.0/18|Internet|
|mi-103-9-8-22-skoku — Internet|103.9.8.0/22|Internet|
|mi-103-25-156-24-skoku — Internet|103.25.156.0/24|Internet|
|mi-103-25-157-24-skoku — Internet|103.25.157.0/24|Internet|
|mi-103-25-158-23-skoku — Internet|103.25.158.0/23|Internet|
|mi-103-36-96-22-skoku — Internet|103.36.96.0/22|Internet|
|mi-103-255-140-22-skoku — Internet|103.255.140.0/22|Internet|
|mi-104-40-13-skoku — Internet|104.40.0.0/13|Internet|
|mi-104-146-15-skoku — Internet|104.146.0.0/15|Internet|
|mi-104-208-13-skoku — Internet|104.208.0.0/13|Internet|
|mi-111-221-16-20-skoku — Internet|111.221.16.0/20|Internet|
|mi-111-221-64-18-skoku — Internet|111.221.64.0/18|Internet|
|mi-129-75-16-skoku — Internet|129.75.0.0/16|Internet|
|mi-131-107-16-skoku — Internet|131.107.0.0/16|Internet|
|mi-131-253-1-24-skoku — Internet|131.253.1.0/24|Internet|
|mi-131-253-3-24-skoku — Internet|131.253.3.0/24|Internet|
|mi-131-253-5-24-skoku — Internet|131.253.5.0/24|Internet|
|mi-131-253-6-24-skoku — Internet|131.253.6.0/24|Internet|
|mi-131-253-8-24-skoku — Internet|131.253.8.0/24|Internet|
|mi-131-253-12-22-skoku — Internet|131.253.12.0/22|Internet|
|mi-131-253-16-23-skoku — Internet|131.253.16.0/23|Internet|
|mi-131-253-18-24-skoku — Internet|131.253.18.0/24|Internet|
|mi-131-253-21-24-skoku — Internet|131.253.21.0/24|Internet|
|mi-131-253-22-23-skoku — Internet|131.253.22.0/23|Internet|
|mi-131-253-24-21-skoku — Internet|131.253.24.0/21|Internet|
|mi-131-253-32-20-skoku — Internet|131.253.32.0/20|Internet|
|mi-131-253-61-24-skoku — Internet|131.253.61.0/24|Internet|
|mi-131-253-62-23-skoku — Internet|131.253.62.0/23|Internet|
|mi-131-253-64-18-skoku — Internet|131.253.64.0/18|Internet|
|mi-131-253-128-17-skoku — Internet|131.253.128.0/17|Internet|
|mi-132-245-16-skoku — Internet|132.245.0.0/16|Internet|
|mi-134-170-16-skoku — Internet|134.170.0.0/16|Internet|
|mi-134-177-16-skoku — Internet|134.177.0.0/16|Internet|
|mi-137-116-15-skoku — Internet|137.116.0.0/15|Internet|
|mi-137-135-16-skoku — Internet|137.135.0.0/16|Internet|
|mi-138-91-16-skoku — Internet|138.91.0.0/16|Internet|
|mi-138-196-16-skoku — Internet|138.196.0.0/16|Internet|
|mi-139-217-16-skoku — Internet|139.217.0.0/16|Internet|
|mi-139-219-16-skoku — Internet|139.219.0.0/16|Internet|
|mi-141-251-16-skoku — Internet|141.251.0.0/16|Internet|
|mi-146-147-16-skoku — Internet|146.147.0.0/16|Internet|
|mi-147-243-16-skoku — Internet|147.243.0.0/16|Internet|
|mi-150-171-16-skoku — Internet|150.171.0.0/16|Internet|
|mi-150-242-48-22-skoku — Internet|150.242.48.0/22|Internet|
|mi-157-54-15-skoku — Internet|157.54.0.0/15|Internet|
|mi-157-56-14-skoku — Internet|157.56.0.0/14|Internet|
|mi-157-60-16-skoku — Internet|157.60.0.0/16|Internet|
|mi-167-105-16-skoku — Internet|167.105.0.0/16|Internet|
|mi-167-220-16-skoku — Internet|167.220.0.0/16|Internet|
|mi-168-61-16-skoku — Internet|168.61.0.0/16|Internet|
|mi-168-62-15-skoku — Internet|168.62.0.0/15|Internet|
|mi-191-232-13-skoku — Internet|191.232.0.0/13|Internet|
|mi-192-32-16-skoku — Internet|192.32.0.0/16|Internet|
|mi-192-48-225-24-skoku — Internet|192.48.225.0/24|Internet|
|mi-192-84-159-24-skoku — Internet|192.84.159.0/24|Internet|
|mi-192-84-160-23-skoku — Internet|192.84.160.0/23|Internet|
|mi-192-197-157-24-skoku — Internet|192.197.157.0/24|Internet|
|mi-193-149-64-19-skoku — Internet|193.149.64.0/19|Internet|
|mi-193-221-113-24-skoku — Internet|193.221.113.0/24|Internet|
|mi-194-69-96-19-skoku — Internet|194.69.96.0/19|Internet|
|mi-194-110-197-24-skoku — Internet|194.110.197.0/24|Internet|
|mi-198-105-232-22-skoku — Internet|198.105.232.0/22|Internet|
|mi-198-200-130-24-skoku — Internet|198.200.130.0/24|Internet|
|mi-198-206-164-24-skoku — Internet|198.206.164.0/24|Internet|
|mi-199-60-28-24-skoku — Internet|199.60.28.0/24|Internet|
|mi-199-74-210-24-skoku — Internet|199.74.210.0/24|Internet|
|mi-199-103-90-23-skoku — Internet|199.103.90.0/23|Internet|
|mi-199-103-122-24-skoku — Internet|199.103.122.0/24|Internet|
|mi-199-242-32-20-skoku — Internet|199.242.32.0/20|Internet|
|mi-199-242-48-21-skoku — Internet|199.242.48.0/21|Internet|
|mi-202-89-224-20-skoku — Internet|202.89.224.0/20|Internet|
|mi-204-13-120-21-skoku — Internet|204.13.120.0/21|Internet|
|mi-204-14-180-22-skoku — Internet|204.14.180.0/22|Internet|
|mi-204-79-135-24-skoku — Internet|204.79.135.0/24|Internet|
|mi-204-79-179-24-skoku — Internet|204.79.179.0/24|Internet|
|mi-204-79-181-24-skoku — Internet|204.79.181.0/24|Internet|
|mi-204-79-188-24-skoku — Internet|204.79.188.0/24|Internet|
|mi-204-79-195-24-skoku — Internet|204.79.195.0/24|Internet|
|mi-204-79-196-23-skoku — Internet|204.79.196.0/23|Internet|
|mi-204-79-252-24-skoku — Internet|204.79.252.0/24|Internet|
|mi-204-152-18-23-skoku — Internet|204.152.18.0/23|Internet|
|mi-204-152-140-23-skoku — Internet|204.152.140.0/23|Internet|
|mi-204-231-192-24-skoku — Internet|204.231.192.0/24|Internet|
|mi-204-231-194-23-skoku — Internet|204.231.194.0/23|Internet|
|mi-204-231-197-24-skoku — Internet|204.231.197.0/24|Internet|
|mi-204-231-198-23-skoku — Internet|204.231.198.0/23|Internet|
|mi-204-231-200-21-skoku — Internet|204.231.200.0/21|Internet|
|mi-204-231-208-20-skoku — Internet|204.231.208.0/20|Internet|
|mi-204-231-236-24-skoku — Internet|204.231.236.0/24|Internet|
|mi-205-174-224-20-skoku — Internet|205.174.224.0/20|Internet|
|mi-206-138-168-21-skoku — Internet|206.138.168.0/21|Internet|
|mi-206-191-224-19-skoku — Internet|206.191.224.0/19|Internet|
|mi-207-46-16-skoku — Internet|207.46.0.0/16|Internet|
|mi-207-68-128-18-skoku — Internet|207.68.128.0/18|Internet|
|mi-208-68-136-21-skoku — Internet|208.68.136.0/21|Internet|
|mi-208-76-44-22-skoku — Internet|208.76.44.0/22|Internet|
|mi-208-84-21-skoku — Internet|208.84.0.0/21|Internet|
|mi-209-240-192-19-skoku — Internet|209.240.192.0/19|Internet|
|mi-213-199-128-18-skoku — Internet|213.199.128.0/18|Internet|
|mi-216-32-180-22-skoku — Internet|216.32.180.0/22|Internet|
|mi-216-220-208-20-skoku — Internet|216.220.208.0/20|Internet|
|mi-23-96-13-skoku — Internet|23.96.0.0/13|Internet|
|mi-42-159-16-skoku — Internet|42.159.0.0/16|Internet|
|mi-51-13-17-skoku — Internet|51.13.0.0/17|Internet|
|mi-51-107-16-skoku — Internet|51.107.0.0/16|Internet|
|mi-51-116-16-skoku — Internet|51.116.0.0/16|Internet|
|mi-51-120-16-skoku — Internet|51.120.0.0/16|Internet|
|mi-51-120-128-17-skoku — Internet|51.120.128.0/17|Internet|
|mi-51-124-16-skoku — Internet|51.124.0.0/16|Internet|
|mi-102-37-18-skoku — Internet|102.37.0.0/18|Internet|
|mi-102-133-16-skoku — Internet|102.133.0.0/16|Internet|
|mi-199-30-16-20-skoku — Internet|199.30.16.0/20|Internet|
|mi-204-79-180-24-skoku — Internet|204.79.180.0/24|Internet|
||||

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem, zobacz [co to jest wystąpienie zarządzane Azure SQL?](sql-managed-instance-paas-overview.md).
- Dowiedz się, jak [skonfigurować nową sieć wirtualną platformy Azure](virtual-network-subnet-create-arm-template.md) lub [istniejącą sieć wirtualną platformy Azure](vnet-existing-add-subnet.md) , w której można wdrożyć wystąpienie zarządzane SQL.
- [Oblicz rozmiar podsieci](vnet-subnet-determine-size.md) , w której chcesz wdrożyć wystąpienie zarządzane SQL.
- Dowiedz się, jak utworzyć wystąpienie zarządzane:
  - Z [Azure Portal](instance-create-quickstart.md).
  - Za pomocą [programu PowerShell](scripts/create-configure-managed-instance-powershell.md).
  - Przy użyciu [szablonu Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).
  - Przy użyciu [szablonu Azure Resource Manager (za pomocą serwera przesiadkowego, z uwzględnieniem programu SSMS)](https://azure.microsoft.com/resources/templates/201-sqlmi-new-vnet-w-jumpbox/).
