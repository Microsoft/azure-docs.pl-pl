---
title: Architektury do wdrażania aplikacji Oracle na maszynach wirtualnych platformy Azure | Dokumenty firmy Microsoft
description: Architektury aplikacji do wdrażania aplikacji Oracle, w tym E-Business Suite, JD Edwards EnterpriseOne i PeopleSoft na maszynach wirtualnych platformy Microsoft Azure z bazami danych na platformie Azure lub w Oracle Cloud Infrastructure (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: gwallace
tags: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/18/2019
ms.author: borisb
ms.custom: ''
ms.openlocfilehash: f36dfe0092e3447053871ee0e5b4d659bb443779
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687484"
---
# <a name="architectures-to-deploy-oracle-applications-on-azure"></a>Architektury do wdrażania aplikacji Oracle na platformie Azure

Firmy Microsoft i Oracle współpracowały ze sobą, aby umożliwić klientom wdrażanie aplikacji Oracle, takich jak Oracle E-Business Suite, JD Edwards EnterpriseOne i PeopleSoft w chmurze. Wraz z wprowadzeniem sieci prywatnej w wersji zapoznawczej między [platformami](configure-azure-oci-networking.md) Microsoft Azure i Oracle Cloud Infrastructure (OCI) aplikacje Oracle można teraz wdrażać na platformie Azure z ich bazami danych zaplecza na platformie Azure lub OCI. Aplikacje Oracle można również zintegrować z usługą Azure Active Directory, umożliwiając skonfigurowanie logowania jednokrotnego, aby użytkownicy mogli logować się do aplikacji Oracle przy użyciu poświadczeń usługi Azure Active Directory (Azure AD).

OCI oferuje wiele opcji bazy danych Oracle dla aplikacji Oracle, w tym DBaaS, Exadata Cloud Service, Oracle RAC i Infrastructure-as-a-Service (IaaS). Obecnie autonomiczna baza danych nie jest obsługiwanym zapleczem dla aplikacji Oracle.

Istnieje [wiele opcji](oracle-overview.md) wdrażania aplikacji Oracle na platformie Azure, w tym w sposób wysoce dostępny i bezpieczny. Platforma Azure oferuje również [obrazy maszyn wirtualnych bazy danych Oracle,](oracle-vm-solutions.md) które można wdrożyć, jeśli zdecydujesz się uruchomić aplikacje Oracle w całości na platformie Azure.

W poniższych sekcjach przedstawiono zalecenia dotyczące architektury przedstawione przez firmy Microsoft i Oracle dotyczące wdrażania pakietu Oracle E-Business Suite, JD Edwards EnterpriseOne i PeopleSoft w konfiguracji między chmurami lub w całości na platformie Azure. Firmy Microsoft i Oracle przetestowały te aplikacje i potwierdziły, że wydajność spełnia standardy określone przez Oracle dla tych aplikacji.

## <a name="architecture-considerations"></a>Zagadnienia dotyczące architektury

Aplikacje Oracle składa się z wielu usług, które mogą być hostowane na tej samej lub wielu maszynach wirtualnych na platformie Azure i opcjonalnie w OCI. 

Wystąpienia aplikacji można skonfigurować za pomocą prywatnych lub publicznych punktów końcowych. Firmy Microsoft i Oracle zalecają skonfigurowanie *maszyny Wirtualnej hosta bastionu* z publicznym adresem IP w oddzielnej podsieci do zarządzania aplikacją. Następnie należy przypisać tylko prywatne adresy IP do innych komputerów, w tym warstwy bazy danych. 

Podczas konfigurowania aplikacji w architekturze między chmurami planowanie jest wymagane, aby upewnić się, że przestrzeń adresowa IP w sieci wirtualnej platformy Azure nie nakłada się na prywatną przestrzeń adresową IP w sieci wirtualnej chmury OCI.

Aby zwiększyć bezpieczeństwo, należy skonfigurować sieciowe grupy zabezpieczeń na poziomie podsieci, aby zapewnić dozwolony tylko ruch na określonych portach i adresach IP. Na przykład maszyny w warstwie środkowej powinny odbierać ruch tylko z sieci wirtualnej. Żaden ruch zewnętrzny nie powinien dotrzeć bezpośrednio do maszyn klasy środkowej.

Aby uzyskać wysoką dostępność, można skonfigurować nadmiarowe wystąpienia różnych serwerów w tym samym zestawie dostępności lub różnych strefach dostępności. Strefy dostępności umożliwiają osiągnięcie umowy SLA o 99,99% dostępności, a zestawy dostępności umożliwiają osiągnięcie umowy SLA o 99,95% czasu pracy bez przestojów w regionie. Przykładowe architektury pokazane w tym artykule są wdrażane w dwóch strefach dostępności.

Podczas wdrażania aplikacji przy użyciu połączenia między chmurami między chmurami można nadal używać istniejącego obwodu usługi ExpressRoute do łączenia środowiska platformy Azure z siecią lokalną. Do połączenia z OCI potrzebny jest jednak oddzielny obwód usługi ExpressRoute niż ten łączący się z siecią lokalną.

## <a name="e-business-suite"></a>Pakiet e-biznesowy

Oracle E-Business Suite (EBS) to zestaw aplikacji, w tym Zarządzanie Łańcuchem Dostaw (SCM) i Zarządzanie relacjami z klientami (CRM). Aby skorzystać z zarządzanego portfolio bazy danych OCI, ebs można wdrożyć przy użyciu wzajemnego połączenia między chmurami między platformą Microsoft Azure i OCI. W tej konfiguracji warstwy prezentacji i aplikacji są uruchamiane na platformie Azure, a warstwa bazy danych w OCI, jak pokazano na poniższym diagramie architektury (Rysunek 1).

![Architektura e-Business Suite między chmurami](media/oracle-oci-applications/ebs-arch-cross-cloud.png)

*Rysunek 1: Architektura między chmurami pakietu E-Business Suite* 

W tej architekturze sieć wirtualna na platformie Azure jest połączona z wirtualną siecią w chmurze w OCI przy użyciu połączenia między chmurami. Warstwa aplikacji jest skonfigurowana na platformie Azure, podczas gdy baza danych jest skonfigurowana w OCI. Zaleca się wdrożenie każdego składnika we własnej podsieci z sieciowymi grupami zabezpieczeń, aby zezwolić na ruch tylko z określonych podsieci na określonych portach.

Architekturę można również dostosować do wdrażania w całości na platformie Azure z wysoce dostępnymi bazami danych Oracle skonfigurowaną przy użyciu oracle data guard w dwóch strefach dostępności w regionie. Poniższy diagram (Rysunek 2) jest przykładem tego wzorca architektonicznego:

![Architektura pakietu E-Business Suite tylko dla platformy Azure](media/oracle-oci-applications/ebs-arch-azure.png)

*Rysunek 2: Architektura tylko do pakietu E-Business Suite na platformie Azure*

W poniższych sekcjach opisano różne składniki na wysokim poziomie.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-middle-tier"></a>Warstwa aplikacji (środkowa)

Warstwa aplikacji jest izolowana we własnej podsieci. Istnieje wiele maszyn wirtualnych skonfigurowanych w celu odporności na uszkodzenia i łatwego zarządzania poprawkami. Te maszyny wirtualne mogą być wspierane przez udostępniony magazyn, który jest oferowany przez usługi Azure NetApp Files i Ultra SSD. Ta konfiguracja umożliwia łatwiejsze wdrażanie poprawek bez przestojów. Maszyny w warstwie aplikacji powinny być frontowane przez moduł równoważenia obciążenia publicznego, dzięki czemu żądania do warstwy aplikacji EBS są przetwarzane, nawet jeśli jedna maszyna w warstwie jest w trybie offline z powodu błędu.

### <a name="load-balancer"></a>Moduł równoważenia obciążenia

Moduł równoważenia obciążenia platformy Azure umożliwia dystrybucję ruchu między wieloma wystąpieniami obciążenia, aby zapewnić wysoką dostępność. W takim przypadku skonfigurowany jest publiczny moduł równoważenia obciążenia, ponieważ użytkownicy mogą uzyskiwać dostęp do aplikacji EBS przez sieć Web. Moduł równoważenia obciążenia rozdziela obciążenie na obie maszyny w warstwie środkowej. Aby zwiększyć bezpieczeństwo, zezwalaj na ruch tylko od użytkowników uzyskujących dostęp do systemu z sieci firmowej przy użyciu sieci VPN typu lokacja-lokacja lub grupy zabezpieczeń Usługi ExpressRoute i sieci.

### <a name="database-tier"></a>Warstwa bazy danych

Ta warstwa obsługuje bazę danych Oracle i jest podzielona na własną podsieć. Zaleca się dodanie grup zabezpieczeń sieciowych, które zezwalają tylko na ruch z warstwy aplikacji do warstwy bazy danych na porcie bazy danych 1521 specyficzne dla oracle.

Firmy Microsoft i Oracle zalecają konfigurację o wysokiej dostępności. Wysoką dostępność na platformie Azure można osiągnąć, konfigurując dwie bazy danych Oracle w dwóch strefach dostępności za pomocą Oracle Data Guard lub korzystając z usługi Oracle Database Exadata Cloud Service w OCI. Podczas korzystania z usługi Oracle Database Exadata Cloud Service baza danych jest wdrażana w dwóch podsieciach. W oracle data guard można również skonfigurować bazę danych Oracle Database na maszynach wirtualnych w OCI w dwóch domenach dostępności.


### <a name="identity-tier"></a>Warstwa tożsamości

Warstwa tożsamości zawiera maszynę wirtualną EBS Asserter. Ebs Asserter umożliwia synchronizowanie tożsamości z Oracle Identity Cloud Service (IDCS) i Azure AD. Ebs Asserter jest potrzebne, ponieważ EBS nie obsługuje protokołów logowania jednokrotnego, takich jak SAML 2.0 lub OpenID Connect. Ebs Asserter zużywa token openid connect (generowane przez IDCS), sprawdza poprawność go, a następnie tworzy sesję dla użytkownika w EBS. 

Podczas gdy ta architektura pokazuje integrację IDCS, ujednolicony dostęp usługi Azure AD i logowanie jednokrotne można również włączyć za pomocą programu Oracle Access Manager z oracle internet directory lub Oracle Unified Directory. Aby uzyskać więcej informacji, zobacz oficjalnych dokumentów dotyczących [wdrażania Oracle EBS z integracją IDCS](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_idcs.pdf) lub [wdrażania Oracle EBS z integracją OAM](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_oam.pdf).

Aby uzyskać wysoką dostępność, zaleca się wdrożenie nadmiarowych serwerów aseratora EBS w wielu strefach dostępności z modułem równoważenia obciążenia przed nim.

Po skonfigurowaniu infrastruktury pakiet E-Business Suite można zainstalować zgodnie z instrukcją instalacji dostarczoną przez firmę Oracle.

## <a name="jd-edwards-enterpriseone"></a>JD Edwards EnterpriseOne

Oracle JD Edwards EnterpriseOne to zintegrowany pakiet aplikacji z kompleksowym oprogramowaniem do planowania zasobów przedsiębiorstwa. Jest to wielowarstwowa aplikacja, którą można skonfigurować za pomocą zaplecza bazy danych Oracle lub SQL Server. W tej sekcji omówiono szczegółowe informacje na temat wdrażania JD Edwards EnterpriseOne z zaplecza bazy danych Oracle albo w OCI lub na platformie Azure.

W poniższej zalecanej architekturze (Rysunek 3) administracji, prezentacji i warstw środkowych są wdrażane w sieci wirtualnej na platformie Azure. Baza danych jest wdrażana w sieci wirtualnej chmury w OCI.

Podobnie jak w przypadku pakietu E-Business Suite, można skonfigurować opcjonalną warstwę bastionu do bezpiecznych celów administracyjnych. Użyj hosta maszyny Wirtualnej bastionu jako serwera szybkiego dostępu do aplikacji i wystąpień bazy danych.

![JD Edwards EnterpriseOne architektura między chmurami](media/oracle-oci-applications/jdedwards-arch-cross-cloud.png)

*Rysunek 3: Architektura między chmurami JD Edwards EnterpriseOne*

W tej architekturze sieć wirtualna na platformie Azure jest połączona z siecią chmury wirtualnej w OCI przy użyciu połączenia między chmurami. Warstwa aplikacji jest skonfigurowana na platformie Azure, podczas gdy baza danych jest skonfigurowana w OCI. Zaleca się wdrożenie każdego składnika we własnej podsieci z sieciowymi grupami zabezpieczeń, aby zezwolić na ruch tylko z określonych podsieci na określonych portach.

Architekturę można również dostosować do wdrażania w całości na platformie Azure z wysoce dostępnymi bazami danych Oracle skonfigurowaną przy użyciu oracle data guard w dwóch strefach dostępności w regionie. Poniższy diagram (Rysunek 4) jest przykładem tego wzorca architektonicznego:

![Architektura JD Edwards EnterpriseOne tylko na platformie Azure](media/oracle-oci-applications/jdedwards-arch-azure.png)

*Rysunek 4: Architektura JD Edwards EnterpriseOne tylko na platformie Azure*

W poniższych sekcjach opisano różne składniki na wysokim poziomie.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="administrative-tier"></a>Warstwa administracyjna

Jak sama nazwa wskazuje, ta warstwa jest używana do zadań administracyjnych. Można wykroić oddzielną podsieć dla warstwy administracyjnej. Usługi i serwery w tej warstwie są używane głównie do instalacji i administrowania aplikacją. W związku z tym pojedyncze wystąpienia tych serwerów są wystarczające. Nadmiarowe wystąpienia nie są wymagane dla wysokiej dostępności aplikacji.

Składniki tej warstwy są następujące:
    
 - **Serwer inicjowania obsługi administracyjnej** — ten serwer jest używany do kompleksowego wdrażania różnych składników aplikacji. Komunikuje się z wystąpień w innych warstwach, w tym wystąpień w warstwie bazy danych, za port 22. Obsługuje konsolę Menedżera serwera dla JD Edwards EnterpriseOne.
 - **Serwer wdrażania** — ten serwer jest wymagany przede wszystkim do instalacji JD Edwards EnterpriseOne. Podczas procesu instalacji ten serwer działa jako centralne repozytorium wymaganych plików i pakietów instalacyjnych. Oprogramowanie jest dystrybuowane lub wdrażane na innych serwerach i klientach z tego serwera.
 - **Klient programistyczny** — ten serwer zawiera składniki, które są uruchamiane w przeglądarce sieci web, a także aplikacje macierzyste.

### <a name="presentation-tier"></a>Warstwa prezentacji

Ta warstwa zawiera różne składniki, takie jak usługi interfejsu aplikacji (AIS), struktura tworzenia aplikacji (ADF) i serwery aplikacji Java (JAS). Serwery w tej warstwie komunikują się z serwerami w warstwie środkowej. Są one frontowane przez moduł równoważenia obciążenia, który kieruje ruch do niezbędnego serwera na podstawie numeru portu i adresu URL, na których jest odbierany ruch. Zaleca się wdrożenie wielu wystąpień każdego typu serwera w celu zapewnienia wysokiej dostępności.

Poniżej przedstawiono składniki w tej warstwie:
    
- **Usługi interfejsu aplikacji (AIS)** — serwer AIS zapewnia interfejs komunikacyjny między aplikacjami mobilnymi JD Edwards EnterpriseOne dla przedsiębiorstw a JD Edwards EnterpriseOne.
- **Java Application Server (JAS)** — JAS odbiera żądania od modułu równoważenia obciążenia i przekazuje go do warstwy środkowej do wykonywania skomplikowanych zadań. JAS ma możliwość wykonywania prostej logiki biznesowej.
- **Bi Publisher Server (BIP)** — ten serwer przedstawia raporty na podstawie danych zebranych przez aplikację JD Edwards EnterpriseOne. Można zaprojektować i kontrolować sposób prezentowania danych w raporcie na podstawie różnych szablonów.
- **Business Services Server (BSS)** - BSS umożliwia wymianę informacji i współdziałanie z innymi aplikacjami Oracle.
- **Real-Time Events Server (RTE)** - RtE Server umożliwia konfigurowanie powiadomień do systemów zewnętrznych o transakcjach zachodzących w systemie JDE EnterpriseOne. Używa modelu subskrybenta i umożliwia systemom innych firm subskrybowanie zdarzeń. Aby załadować żądania równoważenia do obu serwerów RTE, upewnij się, że serwery znajdują się w klastrze.
- **Serwer ADF (Application Development Framework)** — serwer ADF służy do uruchamiania aplikacji JD Edwards EnterpriseOne opracowanych za pomocą usługi Oracle ADF. Jest to wdrażane na serwerze Oracle WebLogic ze środowiska wykonawczego ADF.

### <a name="middle-tier"></a>Warstwa środkowa

Warstwa środkowa zawiera serwer logiki i serwer wsadowy. W takim przypadku oba serwery są zainstalowane na tej samej maszynie wirtualnej. Jednak w scenariuszach produkcyjnych zaleca się wdrożenie serwera logiki i serwera wsadowego na oddzielnych serwerach. Wiele serwerów są wdrażane w warstwie środkowej w dwóch strefach dostępności dla większej dostępności. Należy utworzyć moduł równoważenia obciążenia platformy Azure i te serwery powinny być umieszczane w puli wewnętrznej bazy danych, aby upewnić się, że oba serwery są aktywne i żądania przetwarzania.

Serwery w warstwie środkowej odbierają żądania od serwerów w warstwie prezentacji i tylko publicznego modułu równoważenia obciążenia. Reguły sieciowej grupy zabezpieczeń muszą być skonfigurowane tak, aby odmawiać ruchu z dowolnego adresu innego niż podsieć warstwy prezentacji i modułu równoważenia obciążenia. Można również skonfigurować regułę nsg, aby umożliwić ruch na porcie 22 z hosta bastionu do celów zarządzania. Można użyć publicznego modułu równoważenia obciążenia do żądania równoważenia obciążenia między maszynami wirtualnymi w warstwie środkowej.

Następujące dwa składniki znajdują się w warstwie środkowej:

- **Serwer logiki** — zawierają logikę biznesową lub funkcje biznesowe.
- **Serwer wsadowy** — używany do przetwarzania wsadowego

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="peoplesoft"></a>Peoplesoft

Pakiet aplikacji PeopleSoft firmy Oracle zawiera oprogramowanie do zarządzania zasobami ludzkimi i finansami. Pakiet aplikacji jest wielopoziomowy, a aplikacje obejmują systemy zarządzania zasobami ludzkimi (HRMS), zarządzanie relacjami z klientami (CRM), finanse i zarządzanie łańcuchem dostaw (FSCM) oraz zarządzanie wydajnością przedsiębiorstwa (EPM).

Zaleca się, aby każda warstwa pakietu oprogramowania była wdrażana we własnej podsieci. Baza danych Oracle lub microsoft SQL Server jest wymagana jako baza danych wewnętrznej bazy danych dla aplikacji. W tej sekcji omówiono szczegółowe informacje na temat wdrażania PeopleSoft z zaplecza bazy danych Oracle.

Poniżej przedstawiono architekturę kanoniczną do wdrażania pakietu aplikacji PeopleSoft w architekturze między chmurami (Rysunek 5).

![Architektura między chmurami PeopleSoft](media/oracle-oci-applications/peoplesoft-arch-cross-cloud.png)

*Rysunek 5: Architektura między chmurami PeopleSoft*

W tej przykładowej architekturze sieć wirtualna na platformie Azure jest połączona z siecią chmury wirtualnej w OCI przy użyciu połączenia między chmurami. Warstwa aplikacji jest skonfigurowana na platformie Azure, podczas gdy baza danych jest skonfigurowana w OCI. Zaleca się wdrożenie każdego składnika we własnej podsieci z sieciowymi grupami zabezpieczeń, aby zezwolić na ruch tylko z określonych podsieci na określonych portach.

Architekturę można również dostosować do wdrażania w całości na platformie Azure z wysoce dostępnymi bazami danych Oracle skonfigurowaną przy użyciu oracle data guard w dwóch strefach dostępności w regionie. Poniższy diagram (Rysunek 6) jest przykładem tego wzorca architektonicznego:

![Architektura tylko dla platformy PeopleSoft](media/oracle-oci-applications/peoplesoft-arch-azure.png)

*Rysunek 6: Architektura tylko dla platformy Azure dla osób softowych*

W poniższych sekcjach opisano różne składniki na wysokim poziomie.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-tier"></a>Warstwa aplikacji

Warstwa aplikacji zawiera wystąpienia serwerów aplikacji PeopleSoft, serwerów sieci Web PeopleSoft, wyszukiwania elastycznego i harmonogramu procesów PeopleSoft. Moduł równoważenia obciążenia platformy Azure jest skonfigurowany do akceptowania żądań od użytkowników, które są kierowane do odpowiedniego serwera w warstwie aplikacji.

Aby uzyskać wysoką dostępność, należy rozważyć skonfigurowanie zbędnych wystąpień każdego serwera w warstwie aplikacji w różnych strefach dostępności. Moduł równoważenia obciążenia platformy Azure można skonfigurować z wieloma pulami zaplecza, aby kierować każde żądanie do odpowiedniego serwera.

### <a name="peopletools-client"></a>Klient PeopleTools

Klient PeopleTools służy do wykonywania działań administracyjnych, takich jak rozwój, migracja i uaktualnianie. Ponieważ klient PeopleTools nie jest wymagany do osiągnięcia wysokiej dostępności aplikacji, nadmiarowe serwery klienta PeopleTools nie są potrzebne.

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="next-steps"></a>Następne kroki

Skrypty Terraform umożliwia [konfigurowanie](https://github.com/microsoft/azure-oracle) aplikacji Oracle na platformie Azure i ustanawianie łączności między chmurami z OCI.

Aby uzyskać więcej informacji i oficjalnych dokumentów na temat OCI, zobacz dokumentację [Oracle Cloud.](https://docs.cloud.oracle.com/iaas/Content/home.htm)
