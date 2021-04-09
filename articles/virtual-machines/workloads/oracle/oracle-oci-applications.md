---
title: Architektury do wdrażania aplikacji Oracle na platformie Azure Virtual Machines | Microsoft Docs
description: Architektury aplikacji do wdrażania aplikacji Oracle, w tym pakietów elektronicznych, JD Edwards EnterpriseOne i PeopleSoft na Microsoft Azure maszynach wirtualnych z bazami danych na platformie Azure lub w infrastrukturze chmury firmy Oracle (OCI).
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 07/18/2019
ms.author: kegorman
ms.openlocfilehash: 862bb886c7ec5dfd40c7acdbae2f70f6698a711b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101669934"
---
# <a name="architectures-to-deploy-oracle-applications-on-azure"></a>Architektury do wdrażania aplikacji Oracle na platformie Azure

Firmy Microsoft i Oracle współpracowały ze sobą, aby umożliwić klientom wdrażanie aplikacji Oracle, takich jak Oracle webbusiness Suite, JD Edwards EnterpriseOne i PeopleSoft w chmurze. Wraz z wprowadzeniem [łączności](configure-azure-oci-networking.md) między Microsoft Azure i infrastrukturą chmury firmy Oracle (OCI) w ramach wersji zapoznawczej można teraz wdrażać aplikacje Oracle na platformie Azure z bazami danych zaplecza na platformie Azure lub OCI. Aplikacje Oracle można także zintegrować z Azure Active Directory, co pozwala na skonfigurowanie logowania jednokrotnego, dzięki czemu użytkownicy będą mogli logować się do aplikacji Oracle przy użyciu poświadczeń Azure Active Directory (Azure AD).

OCI oferuje wiele opcji bazy danych Oracle dla aplikacji Oracle, w tym DBaaS, Exadata Cloud Service, Oracle RAC i infrastruktura jako usługa (IaaS). Obecnie autonomiczna baza danych nie jest obsługiwanym zapleczem dla aplikacji Oracle.

Istnieje [wiele opcji](oracle-overview.md) wdrażania aplikacji Oracle na platformie Azure, w tym wysoce dostępny i bezpieczny sposób. Platforma Azure oferuje również [obrazy maszyn wirtualnych bazy danych Oracle](oracle-vm-solutions.md) , które można wdrożyć w celu całkowitego uruchamiania aplikacji Oracle na platformie Azure.

W poniższych sekcjach zamieszczono zalecenia dotyczące architektury firmy Microsoft i programu Oracle, które umożliwiają wdrożenie Oracle E-Business Suite, JD Edwards EnterpriseOne i PeopleSoft w konfiguracji wielu chmur lub w całości na platformie Azure. Firmy Microsoft i Oracle przetestowały te aplikacje i potwierdzają, że wydajność jest zgodna ze standardami ustawionymi przez firmę Oracle dla tych aplikacji.

## <a name="architecture-considerations"></a>Zagadnienia dotyczące architektury

Aplikacje Oracle składają się z wielu usług, które mogą być hostowane na tej samej lub wielu maszynach wirtualnych na platformie Azure i opcjonalnie w systemie OCI. 

Wystąpienia aplikacji można skonfigurować za pomocą prywatnych lub publicznych punktów końcowych. Firma Microsoft i Oracle zaleca skonfigurowanie *maszyny wirtualnej hosta usługi bastionu* z publicznym adresem IP w oddzielnej podsieci na potrzeby zarządzania aplikacją. Następnie przypisz tylko prywatne adresy IP do innych maszyn, włącznie z warstwą bazy danych. 

Podczas konfigurowania aplikacji w architekturze międzychmurowej wymagane jest zagwarantowanie, że przestrzeń adresów IP w sieci wirtualnej platformy Azure nie nakłada się na prywatną przestrzeń adresów IP w sieci w chmurze wirtualnej OCI.

Aby zwiększyć bezpieczeństwo, należy skonfigurować sieciowe grupy zabezpieczeń na poziomie podsieci w celu zapewnienia, że dozwolony jest tylko ruch na określonych portach i adresach IP. Na przykład maszyny w warstwie środkowej powinny odbierać tylko ruch z sieci wirtualnej. Żaden ruch zewnętrzny nie powinien bezpośrednio dotrzeć do maszyn warstwy środkowej.

Aby zapewnić wysoką dostępność, można skonfigurować nadmiarowe wystąpienia różnych serwerów w tym samym zestawie dostępności lub w różnych strefach dostępności. Strefy dostępności umożliwiają korzystanie z umowy SLA na 99,99% czasu, podczas gdy zestawy dostępności umożliwiają osiągnięcie 99,95% umowy SLA w regionie. Przykładowe architektury wyświetlane w tym artykule są wdrażane w dwóch strefach dostępności.

Podczas wdrażania aplikacji przy użyciu połączenia międzychmurowego można nadal używać istniejącego obwodu usługi ExpressRoute do łączenia środowiska platformy Azure z siecią lokalną. Niemniej jednak potrzebny jest oddzielny obwód usługi ExpressRoute dla połączenia, który jest podłączany do sieci lokalnej.

## <a name="e-business-suite"></a>Pakiet E-Business

Oracle E-Business Suite (EBS) to pakiet aplikacji, w tym zarządzanie łańcuchem dostaw (SCM) i zarządzanie relacjami z klientami (CRM). Aby skorzystać z portfolio zarządzanej bazy danych OCI, EBS można wdrożyć przy użyciu międzychmurowego połączenia między Microsoft Azure i OCI. W tej konfiguracji prezentacje i warstwy aplikacji są uruchamiane na platformie Azure i w warstwie bazy danych OCI, jak pokazano na poniższym diagramie architektury (rysunek 1).

![Architektura Międzychmurowa dla wielu firm](media/oracle-oci-applications/ebs-arch-cross-cloud.png)

*Rysunek 1. Architektura międzychmurowego zestawu E-Business* 

W tej architekturze Sieć wirtualna na platformie Azure jest połączona z wirtualną siecią chmurową w OCI przy użyciu połączenia międzychmurowego. Warstwa aplikacji jest skonfigurowana na platformie Azure, natomiast baza danych jest skonfigurowana w systemie OCI. Zaleca się wdrożenie każdego składnika w jego własnej podsieci z grupami zabezpieczeń sieci w celu zezwolenia tylko na ruch z określonych podsieci na określonych portach.

Architekturę można również dostosować do wdrożenia całkowicie na platformie Azure z bazami danych Oracle o wysokiej dostępności skonfigurowanymi przy użyciu usługi Oracle Data Guard w dwóch strefach dostępności w regionie. Poniższy diagram (rysunek 2) jest przykładem tego wzorca architektury:

![Architektura online platformy Azure](media/oracle-oci-applications/ebs-arch-azure.png)

*Rysunek 2: architektura online platformy Azure*

W poniższych sekcjach opisano różne składniki na wysokim poziomie.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-middle-tier"></a>Warstwa aplikacji (środkowej)

Warstwa aplikacji jest izolowana w własnej podsieci. Istnieje wiele maszyn wirtualnych skonfigurowanych pod kątem odporności na uszkodzenia i łatwego zarządzania poprawkami. Te maszyny wirtualne mogą być obsługiwane przez magazyn udostępniony, który jest oferowany przez Azure NetApp Files i dysków SSD. Ta konfiguracja umożliwia łatwiejsze wdrażanie poprawek bez przestojów. Maszyny w warstwie aplikacji powinny być poprzedzone przez publiczny moduł równoważenia obciążenia, dzięki czemu żądania do warstwy aplikacji EBS są przetwarzane, nawet jeśli jedna maszyna w warstwie jest w trybie offline z powodu błędu.

### <a name="load-balancer"></a>Moduł równoważenia obciążenia

Moduł równoważenia obciążenia platformy Azure umożliwia dystrybucję ruchu między wieloma wystąpieniami obciążenia w celu zapewnienia wysokiej dostępności. W takim przypadku jest skonfigurowany publiczny moduł równoważenia obciążenia, ponieważ użytkownicy mogą uzyskiwać dostęp do aplikacji EBS za pośrednictwem sieci Web. Moduł równoważenia obciążenia dystrybuuje obciążenie obu maszyn w warstwie środkowej. Aby zwiększyć bezpieczeństwo, Zezwól na ruch tylko od użytkowników uzyskujących dostęp do systemu z sieci firmowej przy użyciu sieci VPN typu lokacja-lokacja lub ExpressRoute i sieciowych grup zabezpieczeń.

### <a name="database-tier"></a>Warstwa bazy danych

Ta warstwa obsługuje bazę danych Oracle i jest oddzielona do własnej podsieci. Zaleca się dodanie sieciowych grup zabezpieczeń, które zezwalają na ruch z warstwy aplikacji tylko do warstwy bazy danych w porcie bazy danych specyficznym dla platformy Oracle 1521.

Firmy Microsoft i Oracle zalecają konfigurację wysokiej dostępności. Wysoką dostępność na platformie Azure można osiągnąć, konfigurując dwie bazy danych Oracle w dwóch strefach dostępności przy użyciu funkcji Oracle Data Guard lub korzystając z usługi Oracle Database Exadata w chmurze w systemie OCI. W przypadku korzystania z usługi Oracle Database Exadata w chmurze baza danych jest wdrażana w dwóch podsieciach. Możesz również skonfigurować Oracle Database na maszynach wirtualnych w systemie OCI w dwóch domenach dostępności przy użyciu funkcji Oracle Data Guard.


### <a name="identity-tier"></a>Warstwa tożsamości

Warstwa tożsamości zawiera maszynę wirtualną EBS Asserter. Program EBS Assert umożliwia synchronizowanie tożsamości z usługi Oracle Identity Cloud Service (IDCS) i usługi Azure AD. EBS Assert jest wymagany, ponieważ EBS nie obsługuje protokołów logowania jednokrotnego, takich jak SAML 2,0 lub OpenID Connect Connect. Program EBS Assert używa tokenu połączenia OpenID Connect (wygenerowanego przez IDCS), sprawdza jego poprawność, a następnie tworzy sesję dla użytkownika w EBS. 

Chociaż ta architektura pokazuje integrację z usługą IDCS, można także włączyć funkcję dostępu jednokrotnego usługi Azure AD i jednokrotne logowanie za pomocą programu Oracle Access Manager z katalogiem internetowym Oracle lub Oracle Unified Directory. Aby uzyskać więcej informacji, zobacz dokumenty dotyczące [wdrażania programu Oracle EBS z integracją IDCS](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_idcs.pdf) lub [wdrażania programu Oracle EBS z integracją OAM](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_oam.pdf).

Aby zapewnić wysoką dostępność, zaleca się wdrożenie nadmiarowych serwerów EBS Assert w wielu strefach dostępności przy użyciu modułu równoważenia obciążenia przed nim.

Po skonfigurowaniu infrastruktury można zainstalować pakiet online, wykonując instrukcje podane przez firmę Oracle.

## <a name="jd-edwards-enterpriseone"></a>JD Edwards EnterpriseOne

JD Edwards EnterpriseOne firmy Oracle to zintegrowany pakiet aplikacji z kompleksowym oprogramowaniem do planowania zasobów przedsiębiorstwa. Jest to wielowarstwowa aplikacja, którą można skonfigurować przy użyciu zaplecza bazy danych Oracle lub SQL Server. W tej sekcji omówiono wdrażanie JD Edwards EnterpriseOne z zapleczem bazy danych Oracle w systemie OCI lub na platformie Azure.

Zgodnie z poniższą zalecaną architekturą (rysunek 3), administracja, prezentacja i warstwy środkowe są wdrażane w sieci wirtualnej na platformie Azure. Baza danych jest wdrażana w wirtualnej sieci chmurowej w bazie OCI.

Podobnie jak w przypadku pakietu online dla firm, można skonfigurować opcjonalną warstwę bastionu na potrzeby bezpiecznych celów administracyjnych. Użyj hosta maszyny wirtualnej bastionu jako serwera skoku, aby uzyskać dostęp do wystąpień aplikacji i bazy danych.

![Architektura Międzychmurowa JD Edwards EnterpriseOne](media/oracle-oci-applications/jdedwards-arch-cross-cloud.png)

*Rysunek 3: JD Edwards EnterpriseOne — architektura Międzychmurowa*

W tej architekturze Sieć wirtualna na platformie Azure jest połączona z wirtualną siecią chmurową w OCI przy użyciu połączenia międzychmurowego. Warstwa aplikacji jest skonfigurowana na platformie Azure, natomiast baza danych jest skonfigurowana w systemie OCI. Zaleca się wdrożenie każdego składnika w jego własnej podsieci z grupami zabezpieczeń sieci w celu zezwolenia tylko na ruch z określonych podsieci na określonych portach.

Architekturę można również dostosować do wdrożenia całkowicie na platformie Azure z bazami danych Oracle o wysokiej dostępności skonfigurowanymi przy użyciu usługi Oracle Data Guard w dwóch strefach dostępności w regionie. Poniższy diagram (rysunek 4) jest przykładem tego wzorca architektury:

![JD Edwards EnterpriseOne architektura platformy Azure](media/oracle-oci-applications/jdedwards-arch-azure.png)

*Rysunek 4: JD Edwards EnterpriseOne architektura platformy Azure*

W poniższych sekcjach opisano różne składniki na wysokim poziomie.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="administrative-tier"></a>Warstwa administracyjna

Jak sugeruje nazwa, ta warstwa jest używana na potrzeby zadań administracyjnych. Można wydzielenie oddzielną podsieć dla warstwy administracyjnej. Usługi i serwery w tej warstwie są używane głównie do instalacji i administrowania aplikacją. W związku z tym są wystarczające pojedyncze wystąpienia tych serwerów. Nadmiarowe wystąpienia nie są wymagane do zapewnienia wysokiej dostępności aplikacji.

Składniki tej warstwy są następujące:
    
 - **Serwer aprowizacji** — ten serwer służy do kompleksowego wdrażania różnych składników aplikacji. Komunikuje się z wystąpieniami w innych warstwach, w tym wystąpieniami w warstwie bazy danych, na porcie 22. Obsługuje on konsolę Menedżer serwera JD Edwards EnterpriseOne.
 - **Serwer wdrażania** — ten serwer jest przede wszystkim wymagany do zainstalowania programu JD Edwards EnterpriseOne. W trakcie procesu instalacji ten serwer działa jako centralne repozytorium dla wymaganych plików i pakietów instalacyjnych. Oprogramowanie jest dystrybuowane lub wdrażane na innych serwerach i klientach z tego serwera.
 - **Klient programistyczny** — ten serwer zawiera składniki, które działają w przeglądarce internetowej, a także aplikacje natywne.

### <a name="presentation-tier"></a>Warstwa prezentacji

Ta warstwa zawiera różne składniki, takie jak Application Interface Services (AIS), Application Development Framework (ADF) i serwery aplikacji Java (AGENTY zadań). Serwery w tej warstwie komunikują się z serwerami w warstwie środkowej. Są one poprzedzone przez moduł równoważenia obciążenia, który kieruje ruch do niezbędnego serwera na podstawie numeru portu i adresu URL, na którym jest odbierany ruch. Zaleca się wdrożenie wielu wystąpień każdego typu serwera w celu zapewnienia wysokiej dostępności.

Poniżej wymieniono składniki w tej warstwie:
    
- **Application Interface Services (AIS)** — serwer AIS udostępnia interfejs komunikacyjny między aplikacjami JD Edwards EnterpriseOne Mobile Enterprise Applications i JD Edwards EnterpriseOne.
- **Serwer aplikacji Java (Agenty zadań)** — usługa Agenty zadań odbiera żądania z modułu równoważenia obciążenia i przekazuje je do warstwy środkowej w celu wykonywania skomplikowanych zadań. AGENTY zadań ma możliwość wykonywania prostej logiki biznesowej.
- **Serwer programu BI Publisher (BIP)** — ten serwer przedstawia raporty na podstawie danych zebranych przez aplikację JD Edwards EnterpriseOne. Można projektować i kontrolować sposób prezentowania danych w raporcie na podstawie różnych szablonów.
- **Serwer usług Business Services (BSS)** — zestaw BSS umożliwia wymianę informacji i współdziałanie z innymi aplikacjami Oracle.
- **Serwer zdarzeń w czasie rzeczywistym (RTE)** — serwer RTE umożliwia Konfigurowanie powiadomień do systemów zewnętrznych o transakcjach w systemie JDE EnterpriseOne. Używa modelu subskrybenta i umożliwia systemom innych firm subskrybowanie zdarzeń. Aby równoważyć obciążenie żądaniami do obu serwerów RTE, upewnij się, że serwery znajdują się w klastrze.
- **Serwer programu Application Development Framework (ADF)** — serwer ADF jest używany do uruchamiania aplikacji JD Edwards EnterpriseOne opracowanych za pomocą narzędzia Oracle ADF. Ten element jest wdrażany na serwerze Oracle WebLogic i w środowisku uruchomieniowym ADF.

### <a name="middle-tier"></a>Warstwa środkowa

Warstwa środkowa zawiera serwer logiki i serwer wsadowy. W takim przypadku oba serwery są zainstalowane na tej samej maszynie wirtualnej. Jednak w przypadku scenariuszy produkcyjnych zaleca się wdrożenie serwera logiki i serwera usługi Batch na oddzielnych serwerach. W warstwie środkowej w dwóch strefach dostępności jest wdrażanych wiele serwerów w celu zapewnienia lepszej dostępności. Należy utworzyć moduł równoważenia obciążenia platformy Azure, a te serwery należy umieścić w puli zaplecza, aby zapewnić, że oba serwery są aktywne i przetwarzają żądania.

Serwery w warstwie środkowej odbierają żądania z serwerów znajdujących się w warstwie prezentacji i w publicznym module równoważenia obciążenia. Reguły sieciowej grupy zabezpieczeń muszą zostać skonfigurowane tak, aby odmówić ruchu z dowolnego adresu innego niż podsieć warstwy prezentacji i modułu równoważenia obciążenia. Regułę sieciowej grupy zabezpieczeń można także skonfigurować tak, aby zezwalała na ruch na porcie 22 z hosta bastionu na potrzeby zarządzania. Do równoważenia obciążenia między maszynami wirtualnymi w warstwie środkowej może być możliwe użycie publicznego modułu równoważenia obciążenia.

Następujące dwa składniki znajdują się w warstwie środkowej:

- **Serwer logiki** — zawiera logikę biznesową lub funkcje biznesowe.
- **Serwer wsadowy** — używany do przetwarzania wsadowego

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="peoplesoft"></a>PeopleSoft

Pakiet aplikacji PeopleSoft firmy Oracle zawiera oprogramowanie do zarządzania zasobami ludzkimi i zarządzaniem finansami. Pakiet aplikacji to wiele warstw i aplikacje obejmują systemy zarządzania zasobami ludzkimi (HRMS), zarządzanie relacjami z klientami (CRM), finanse i zarządzanie łańcuchem dostaw (FSCM) oraz zarządzanie wydajnością przedsiębiorstwa (EPM).

Zaleca się, aby każda warstwa pakietu oprogramowania była wdrażana we własnej podsieci. Baza danych programu Oracle lub Microsoft SQL Server jest wymagana jako baza danych zaplecza dla aplikacji. W tej części omówiono wdrażanie PeopleSoft z zapleczem bazy danych Oracle.

Poniżej znajduje się Architektura kanoniczna służąca do wdrażania pakietu aplikacji PeopleSoft w architekturze międzychmurowej (rysunek 5).

![Architektura wielu chmur PeopleSoft](media/oracle-oci-applications/peoplesoft-arch-cross-cloud.png)

*Rysunek 5: PeopleSoft architektura Międzychmurowa*

W tej przykładowej architekturze Sieć wirtualna na platformie Azure jest połączona z wirtualną siecią chmurową w OCI przy użyciu połączenia międzychmurowego. Warstwa aplikacji jest skonfigurowana na platformie Azure, natomiast baza danych jest skonfigurowana w systemie OCI. Zaleca się wdrożenie każdego składnika w jego własnej podsieci z grupami zabezpieczeń sieci w celu zezwolenia tylko na ruch z określonych podsieci na określonych portach.

Architekturę można również dostosować do wdrożenia całkowicie na platformie Azure z bazami danych Oracle o wysokiej dostępności skonfigurowanymi przy użyciu usługi Oracle Data Guard w dwóch strefach dostępności w regionie. Poniższy diagram (rysunek 6) jest przykładem tego wzorca architektury:

![PeopleSoft architektury platformy Azure](media/oracle-oci-applications/peoplesoft-arch-azure.png)

*Ilustracja 6. PeopleSoft architektury platformy Azure*

W poniższych sekcjach opisano różne składniki na wysokim poziomie.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-tier"></a>Warstwa aplikacji

Warstwa aplikacji zawiera wystąpienia serwerów aplikacji PeopleSoft, serwery sieci Web PeopleSoft, elastyczne wyszukiwanie i harmonogram procesów PeopleSoft. Moduł równoważenia obciążenia platformy Azure jest skonfigurowany do akceptowania żądań od użytkowników, które są kierowane do odpowiedniego serwera w warstwie aplikacji.

Aby zapewnić wysoką dostępność, należy rozważyć skonfigurowanie nadmiarowych wystąpień każdego serwera w warstwie aplikacji w różnych strefach dostępności. Moduł równoważenia obciążenia platformy Azure można skonfigurować z wieloma pulami zaplecza, aby skierować każde żądanie do właściwego serwera.

### <a name="peopletools-client"></a>Klient PeopleTools

Klient PeopleTools służy do wykonywania działań administracyjnych, takich jak programowanie, migracja i uaktualnianie. Ponieważ klient PeopleTools nie jest wymagany do osiągnięcia wysokiej dostępności aplikacji, nadmiarowe serwery programu PeopleTools Client nie są potrzebne.

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="next-steps"></a>Następne kroki

Używaj [skryptów Terraform](https://github.com/microsoft/azure-oracle) do konfigurowania aplikacji Oracle na platformie Azure i ustanawiania łączności między chmurą za pomocą OCI.

Aby uzyskać więcej informacji i oficjalny dokument dotyczący OCI, zobacz dokumentację w [chmurze firmy Oracle](https://docs.cloud.oracle.com/iaas/Content/home.htm) .
