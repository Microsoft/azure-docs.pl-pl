---
title: Informacje o odzyskiwaniu po awarii dla aplikacji lokalnych za pomocą Azure Site Recovery
description: W tym artykule opisano obciążenia, które można chronić za pomocą funkcji odzyskiwania po awarii w usłudze Azure Site Recovery.
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 1a5d20e6feacfe72052142c07dc45753b9bc3138
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599115"
---
# <a name="about-disaster-recovery-for-on-premises-apps"></a>Informacje o odzyskiwaniu po awarii dla aplikacji lokalnych

W tym artykule opisano lokalne obciążenia i aplikacje, które można chronić na potrzeby odzyskiwania po [awarii](site-recovery-overview.md) za pomocą Azure Site Recovery service.

## <a name="overview"></a>Omówienie

Organizacje potrzebują strategii ciągłości działania i odzyskiwania po awarii (BCDR, business continuity and disaster recovery), aby zapewnić bezpieczeństwo obciążeń i danych oraz ich dostęp podczas planowanych i nieplanowanych przestojów. Odzyskiwanie do normalnych warunków roboczych.

Usługa Site Recovery na platformie Azure ułatwia realizację strategii BCDR. Za pomocą usługi Site Recovery można wdrożyć replikację obsługującą aplikacje w chmurze lub lokacji dodatkowej. Za pomocą usługi Site Recovery można zarządzać replikacją, testować odzyskiwanie po awarii oraz uruchamiać tryb failover i powrót po awarii. Aplikacje można uruchamiać na komputerach z systemem Windows lub Linux, serwerach fizycznych, programie VMware lub funkcji Hyper-V.

Site Recovery integruje się z aplikacjami firmy Microsoft, takimi jak SharePoint, Exchange, Dynamics, SQL Server i Active Directory. Firma Microsoft ściśle współpracuje z wiodącymi dostawcami, w tym Oracle, SAP i Red Hat. Rozwiązania replikacji można dostosować do poszczególnych aplikacji.

## <a name="why-use-site-recovery-for-application-replication"></a>Dlaczego warto używać usługi Site Recovery do replikacji aplikacji?

Usługa Site Recovery przyczynia się do ochrony i odzyskiwania na poziomie aplikacji w następujący sposób:

- Nie zależy od aplikacji i zapewnia replikację dla wszystkich obciążeń uruchomionych na obsługiwanej maszynie.
- Niemal synchroniczna replikacja z celami punktu odzyskiwania (RPO) nawet przez 30 sekund w celu spełnienia wymagań większości krytycznych aplikacji biznesowych.
- Spójne migawki jednowarstwowych lub wielowarstwowych aplikacji.
- Integracja z SQL Server AlwaysOn i współpraca z innymi technologiami replikacji na poziomie aplikacji. Na przykład replikacja usługi Active Directory, zawsze w trybie SQL AlwaysOn i grupy dostępności bazy danych programu Exchange (DAG).
- Elastyczne plany odzyskiwania, które umożliwiają odzyskanie całego stosu aplikacji jednym kliknięciem oraz uwzględnianie w planie zewnętrznych skryptów i akcji ręcznych.
- Zaawansowane zarządzanie siecią w Site Recovery i na platformie Azure w celu uproszczenia wymagań sieci aplikacji. Zarządzanie siecią, takie jak możliwość rezerwowania adresów IP, konfigurowania równoważenia obciążenia i integracji z usługą Azure Traffic Manager w celu przełączania sieci o niskim czasie odzyskiwania (RTO).
- Bogata biblioteka automatyzacji, która zapewnia gotowe do zastosowania w środowisku produkcyjnym skrypty dopasowane do danych aplikacji, które można pobrać i zintegrować z planami odzyskiwania.

## <a name="workload-summary"></a>Podsumowanie obciążenia

Usługa Site Recovery może replikować dowolną aplikację uruchomioną na obsługiwanej maszynie. Współpracujemy z zespołami produktu, aby wykonać dodatkowe testy dla aplikacji określonych w poniższej tabeli.

| **Obciążenie** |**Replikacja maszyn wirtualnych platformy Azure na platformę Azure** |**Replikacja maszyn wirtualnych funkcji Hyper-V do lokacji dodatkowej** | **Replikacja maszyn wirtualnych funkcji Hyper-V do platformy Azure** | **Replikacja maszyn wirtualnych VMware do lokacji dodatkowej** | **Replikowanie maszyn wirtualnych VMware na platformę Azure** |
| --- | --- | --- | --- | --- |---|
| Active Directory, DNS |Tak |Tak |Tak |Tak |Tak|
| Aplikacje internetowe (IIS, SQL) |Tak |Tak |Tak |Tak |Tak|
| System Center Operations Manager |Tak |Tak |Tak |Tak |Tak|
| SharePoint |Tak |Tak |Tak |Tak |Tak|
| SAP<br/><br/>Replikacja lokacji SAP do platformy Azure, bez klastra |Tak (przetestowane przez firmę Microsoft) |Tak (przetestowane przez firmę Microsoft) |Tak (przetestowane przez firmę Microsoft) |Tak (przetestowane przez firmę Microsoft) |Tak (przetestowane przez firmę Microsoft)|
| Exchange (bez grupy DAG) |Tak |Tak |Tak |Tak |Tak|
| Pulpit zdalny/VDI |Tak |Tak |Tak |Tak |Tak|
| Linux (system operacyjny i aplikacje) |Tak (przetestowane przez firmę Microsoft) |Tak (przetestowane przez firmę Microsoft) |Tak (przetestowane przez firmę Microsoft) |Tak (przetestowane przez firmę Microsoft) |Tak (przetestowane przez firmę Microsoft)|
| Dynamics AX |Tak |Tak |Tak |Tak |Tak|
| Serwer plików systemu Windows |Tak |Tak |Tak |Tak |Tak|
| Citrix XenApp i XenDesktop |Nie|Nie dotyczy |Nie |Nie dotyczy |Nie |

## <a name="replicate-active-directory-and-dns"></a>Replikacja usługi Active Directory i DNS

Usługa Active Directory i infrastruktura DNS są istotne dla większości aplikacji firmowych. Podczas odzyskiwania po awarii należy chronić i odzyskiwać te składniki infrastruktury przed odzyskaniem obciążeń i aplikacji.

Usługa Site Recovery umożliwia utworzenie całkowicie zautomatyzowanego planu odzyskiwania po awarii dla usługi Active Directory i infrastruktury DNS. Na przykład w celu trybu fail over SharePoint i SAP z lokacji głównej do lokacji dodatkowej można skonfigurować plan odzyskiwania, który najpierw przejmie usługę Active Directory w tryb fail over. Następnie użyj dodatkowego planu odzyskiwania specyficznego dla aplikacji, aby przesłonić w tryb fail over inne aplikacje, które korzystają z usługi Active Directory.

[Dowiedz się więcej o](site-recovery-active-directory.md) odzyskiwaniu po awarii dla usług Active Directory i DNS.

## <a name="protect-sql-server"></a>Ochrona programu SQL Server

SQL Server stanowi podstawę usług danych dla wielu aplikacji biznesowych w lokalnym centrum danych. Site Recovery można używać z technologiami SQL Server HA/DR, aby chronić wielowarstwowe aplikacje przedsiębiorstwa, które używają SQL Server.

Usługa Site Recovery zapewnia:

- Proste i ekonomiczne rozwiązanie do odzyskiwania po awarii dla programu SQL Server. Replikacja wielu wersji i edycji autonomicznych serwerów i klastrów programu SQL Server do platformy Azure lub lokacji dodatkowej.
- Integracja z zawsze włączonymi grupami dostępności programu SQL w celu zarządzania pracą w trybie failover i powrotem po awarii przy użyciu planów odzyskiwania usługi Azure Site Recovery.
- Kompleksowe plany odzyskiwania dla wszystkich warstw w aplikacji, w tym dla baz danych programu SQL Server.
- Skalowanie maszyn SQL Server obciążeń szczytowych za Site Recovery  przez zwiększenie ich do większych rozmiarów maszyn wirtualnych IaaS na platformie Azure.
- Łatwe testowanie odzyskiwania po awarii programu SQL Server. Możliwość uruchomienia testowego trybu failover w celu przeanalizowania danych i przeprowadzenia kontroli zgodności bez wpływu na środowisko produkcyjne.

[Dowiedz się więcej](site-recovery-sql.md) o odzyskiwaniu po awarii dla programu SQL Server.

## <a name="protect-sharepoint"></a>Ochrona programu SharePoint

Usługa Azure Site Recovery pomaga w ochronie wdrożeń programu SharePoint w następujący sposób:

- Eliminuje potrzebę stosowania farmy rezerwowej (oraz koszty związane z infrastrukturą) na potrzeby odzyskiwania po awarii. Użyj Site Recovery całej farmy (warstwy sieci Web, aplikacji i bazy danych) na platformę Azure lub do lokacji dodatkowej.
- Upraszcza wdrażanie aplikacji i zarządzanie nimi. Aktualizacje wdrożone w lokacji głównej są automatycznie replikowane. Aktualizacje są dostępne po zakończeniu pracy w trybie failover i odzyskiwaniu farmy w lokacji dodatkowej. Obniża złożoność zarządzania i koszty związane z utrzymywaniem aktualnej farmy autonomicznej.
- Upraszcza projektowanie i testowanie aplikacji programu SharePoint poprzez tworzenie na żądanie repliki środowiska z kopią przypominającą środowisko produkcyjne, co umożliwia testowanie i debugowanie.
- Upraszcza przejście do chmury przez użycie usługi Site Recovery do migrowania wdrożeń programu SharePoint do platformy Azure.

[Dowiedz się więcej](site-recovery-sharepoint.md) o odzyskiwaniu po awarii dla programu SharePoint.

## <a name="protect-dynamics-ax"></a>Ochrona programu Dynamics AX

Usługa Azure Site Recovery pomaga chronić rozwiązanie Dynamics AX ERP w następujący sposób:

- Zarządzanie replikacją całego środowiska Dynamics AX (warstwy sieci Web i systemu AOS, warstwy baz danych, program SharePoint) do platformy Azure lub lokacji dodatkowej.
- Upraszczanie migracji wdrożeń Dynamics AX do chmury (Azure).
- Upraszczanie projektowania i testowania aplikacji programu Dynamics AX poprzez tworzenie na żądanie kopii środowiska przypominającej środowisko produkcyjne, co umożliwia testowanie i debugowanie.

[Dowiedz się więcej o](site-recovery-dynamicsax.md) odzyskiwaniu po awarii dla systemu Dynamic AX.

## <a name="protect-remote-desktop-services"></a>Ochrona Usługi pulpitu zdalnego

Usługi pulpitu zdalnego pulpitu zdalnego (RDS) umożliwia korzystanie z infrastruktury pulpitów wirtualnych (VDI), pulpitów opartych na sesji i aplikacji, które umożliwiają użytkownikom pracę w dowolnym miejscu.

Za Azure Site Recovery można replikować następujące usługi:

- Replikowanie zarządzanych lub nieza zarządzanych pulpitów wirtualnych w puli do lokacji dodatkowej.
- Replikowanie zdalnych aplikacji i sesji do lokacji dodatkowej lub platformy Azure.

W poniższej tabeli przedstawiono opcje replikacji:

| **Usługi pulpitu zdalnego** |**Replikacja maszyn wirtualnych platformy Azure na platformę Azure** | **Replikacja maszyn wirtualnych funkcji Hyper-V do lokacji dodatkowej** | **Replikacja maszyn wirtualnych funkcji Hyper-V do platformy Azure** | **Replikacja maszyn wirtualnych VMware do lokacji dodatkowej** | **Replikowanie maszyn wirtualnych VMware na platformę Azure** | **Replikacja serwerów fizycznych do lokacji dodatkowej** | **Replikacja serwerów fizycznych do platformy Azure** |
|---| --- | --- | --- | --- | --- | --- | --- |
| **Pulpit wirtualny w puli (niezarządzany)** |Nie|Tak |Nie |Tak |Nie |Tak |Nie |
| **Pulpit wirtualny w puli (zarządzany i bez dysku UPD)** |Nie|Tak |Nie |Tak |Nie |Tak |Nie |
| **Zdalne aplikacje i sesje pulpitu (bez dysku UPD)** |Tak|Tak |Tak |Tak |Tak |Tak |Tak |

[Dowiedz się więcej](/windows-server/remote/remote-desktop-services/rds-disaster-recovery-with-azure) o odzyskiwaniu po awarii dla usług pulpitu zdalnego.

## <a name="protect-exchange"></a>Ochrona programu Exchange

Usługa Site Recovery pomaga chronić program Exchange w następujący sposób:

- W przypadku małych wdrożeń programu Exchange, np. serwerów pojedynczych lub autonomicznych, usługa Site Recovery może wykonywać replikację i stosować tryb failover na platformie Azure lub w lokacji dodatkowej.
- W przypadku większych wdrożeń usługa Site Recovery integruje się z grupami DAG programu Exchange.
- Grupy DAG programu Exchange są zalecanym rozwiązaniem dla odzyskiwania po awarii programu Exchange w przedsiębiorstwie.  Plany odzyskiwania usługi Site Recovery mogą uwzględniać grupy DAG, aby organizować tryb failover grupy DAG między lokacjami.

Aby dowiedzieć się więcej na temat odzyskiwania po awarii dla programu Exchange, zobacz [Exchange DAG](/Exchange/high-availability/database-availability-groups/database-availability-groups) and Exchange disaster recovery (Odzyskiwanie po awarii programu Exchange i [odzyskiwanie po awarii programu Exchange).](/Exchange/high-availability/disaster-recovery/disaster-recovery)

## <a name="protect-sap"></a>Ochrona systemu SAP

Z usługi Site Recovery można korzystać, aby zabezpieczyć wdrożenie SAP w następujący sposób:

- Włącz ochronę aplikacji produkcyjnych środowiska SAP NetWeaver i nienależących do środowiska NetWeaver uruchomionych lokalnie, replikując składniki do platformy Azure.
- Włącz ochronę aplikacji produkcyjnych środowiska SAP NetWeaver i nienależących do środowiska NetWeaver uruchomionych na platformie Azure, replikując składniki do innego centrum danych platformy Azure.
- Skorzystaj z uproszczenia migracji do chmury, używając usługi Site Recovery do migrowania wdrożenia SAP do platformy Azure.
- Uprość uaktualnienia, testowanie i tworzenie prototypów projektu SAP, tworząc na żądanie klony środowiska produkcyjnego na potrzeby testowania aplikacji SAP.

[Dowiedz się więcej o](site-recovery-sap.md) odzyskiwaniu po awarii dla oprogramowania SAP.

## <a name="protect-internet-information-services"></a>Ochrona Internet Information Services

Użyj Site Recovery, aby chronić wdrożenie Internet Information Services (IIS), w następujący sposób:

Usługa Azure Site Recovery zapewnia odzyskiwanie po awarii przez replikowanie krytycznych składników środowiska do zimnej lokacji zdalnej lub chmury publicznej, takiej jak Microsoft Azure. Ponieważ maszyny wirtualne z serwerem sieci Web i bazą danych są replikowane do lokacji odzyskiwania, nie jest wymagane tworzenie oddzielnej kopii zapasowej plików konfiguracji ani certyfikatów. Mapowania i powiązania aplikacji zależne od zmiennych środowiskowych, które są zmieniane po przełączeniu do trybu failover, mogą być aktualizowane za pomocą skryptów zintegrowanych z planami odzyskiwania po awarii. Maszyny wirtualne są dostępne w lokacji odzyskiwania tylko podczas pracy w trybie failover. Azure Site Recovery pomaga również w organizowaniu end-to-end trybu failover, zapewniając następujące możliwości:

- Obsługa sekwencji wyłączania i włączania maszyn wirtualnych w różnych warstwach.
- Dodawanie skryptów w celu umożliwienia aktualizacji zależności i powiązań aplikacji na maszynach wirtualnych po ich zakończeniu. Skrypty mogą też służyć do aktualizacji serwera DNS w taki sposób, aby wskazywał lokację odzyskiwania.
- Przydziel adresy IP do maszyn wirtualnych przed trybem failover, mapując sieci podstawowe i sieci odzyskiwania oraz korzystając ze skryptów, które nie muszą być aktualizowane po pracy w trybie failover.
- Możliwość pracy w trybie failover jednym kliknięciem dla wielu aplikacji internetowych, która eliminuje zakres pomyłek podczas awarii.
- Możliwość testowania planów odzyskiwania w izolowanym środowisku na potrzeby próbnego odzyskiwania po awarii.

[Dowiedz się więcej](site-recovery-iis.md) o odzyskiwaniu po awarii dla usług IIS.

## <a name="protect-citrix-xenapp-and-xendesktop"></a>Ochrona programów Citrix XenApp i XenDesktop

Od marca 2020 r. w usłudze Citrix ogłoszono zakończenie obsługi obciążeń hostowanych w chmurze publicznej. W związku z tym nie zalecamy używania Site Recovery do ochrony obciążeń Citrix.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej](azure-to-azure-quickstart.md) na temat odzyskiwania po awarii dla maszyny wirtualnej platformy Azure.
