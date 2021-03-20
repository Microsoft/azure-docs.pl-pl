---
title: Informacje o odzyskiwaniu po awarii dla aplikacji lokalnych przy użyciu Azure Site Recovery
description: W tym artykule opisano obciążenia, które można chronić za pomocą funkcji odzyskiwania po awarii w usłudze Azure Site Recovery.
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 2b901425a0020c0ccc7b834ee36d965910028018
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "80062835"
---
# <a name="about-disaster-recovery-for-on-premises-apps"></a>Informacje o odzyskiwaniu po awarii dla aplikacji lokalnych

W tym artykule opisano obciążenia lokalne i aplikacje, które można chronić przed odzyskiwaniem po awarii za pomocą usługi [Azure Site Recovery](site-recovery-overview.md) .

## <a name="overview"></a>Omówienie

Organizacje potrzebują strategii ciągłości działania i odzyskiwania po awarii (BCDR), aby zapewnić, że obciążenia i dane są bezpiecznie i dostępne podczas planowanych i nieplanowanych przestojów. I Odzyskaj do zwykłych warunków roboczych.

Usługa Site Recovery na platformie Azure ułatwia realizację strategii BCDR. Za pomocą usługi Site Recovery można wdrożyć replikację obsługującą aplikacje w chmurze lub lokacji dodatkowej. Site Recovery służy do zarządzania replikacją, wykonywania testów odzyskiwania po awarii oraz uruchamiania trybu failover i powrotu po awarii. Aplikacje mogą być uruchamiane na komputerach z systemem Windows lub Linux, serwerach fizycznych, oprogramowaniu VMware lub funkcji Hyper-V.

Site Recovery integruje się z aplikacjami firmy Microsoft, takimi jak SharePoint, Exchange, Dynamics, SQL Server i Active Directory. Firma Microsoft ściśle współpracuje z wiodącymi dostawcami, takimi jak Oracle, SAP i Red Hat. Rozwiązania replikacji można dostosować do poszczególnych aplikacji.

## <a name="why-use-site-recovery-for-application-replication"></a>Dlaczego warto używać usługi Site Recovery do replikacji aplikacji?

Usługa Site Recovery przyczynia się do ochrony i odzyskiwania na poziomie aplikacji w następujący sposób:

- App-niezależny od i zapewnia replikację dla wszystkich obciążeń uruchomionych na obsługiwanej maszynie.
- Replikacja niemal synchroniczna z celami punktu odzyskiwania (RPO), które są niskie niż 30 sekund, aby sprostać potrzebom najbardziej krytycznych aplikacji firmowych.
- Spójne migawki jednowarstwowych lub wielowarstwowych aplikacji.
- Integracja z usługą SQL Server AlwaysOn i partnerstwo z innymi technologiami replikacji na poziomie aplikacji. Na przykład Active Directory replikacja, funkcja SQL AlwaysOn i grupy dostępności bazy danych (grupy DAG składające) programu Exchange.
- Elastyczne plany odzyskiwania, które umożliwiają odzyskanie całego stosu aplikacji jednym kliknięciem i dołączenie zewnętrznych skryptów i akcji ręcznych w planie.
- Zaawansowane zarządzanie siecią w Site Recovery i na platformie Azure upraszcza wymagania dotyczące sieci aplikacji. Zarządzanie siecią, takie jak możliwość rezerwowania adresów IP, konfigurowanie równoważenia obciążenia i integracja z usługą Azure Traffic Manager w przypadku niewielkich celów czasu odzyskiwania (RTO).
- Bogata biblioteka automatyzacji, która zapewnia gotowe do zastosowania w środowisku produkcyjnym skrypty dopasowane do danych aplikacji, które można pobrać i zintegrować z planami odzyskiwania.

## <a name="workload-summary"></a>Podsumowanie obciążenia

Usługa Site Recovery może replikować dowolną aplikację uruchomioną na obsługiwanej maszynie. Współpracujemy z zespołami ds. produktów w celu przeprowadzenia dodatkowego testowania aplikacji określonych w poniższej tabeli.

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
| Citrix XenApp i XenDesktop |Tak|Nie dotyczy |Tak |Nie dotyczy |Tak |

## <a name="replicate-active-directory-and-dns"></a>Replikacja usługi Active Directory i DNS

Usługa Active Directory i infrastruktura DNS są istotne dla większości aplikacji firmowych. Podczas odzyskiwania po awarii należy chronić i odzyskiwać te składniki infrastruktury przed odzyskaniem obciążeń i aplikacji.

Usługa Site Recovery umożliwia utworzenie całkowicie zautomatyzowanego planu odzyskiwania po awarii dla usługi Active Directory i infrastruktury DNS. Na przykład w celu przełączenia w tryb failover programu SharePoint i SAP z lokacji podstawowej do dodatkowej można skonfigurować plan odzyskiwania, który najpierw przejdzie w tryb failover w przypadku Active Directory. Następnie użyj dodatkowego planu odzyskiwania specyficznego dla aplikacji, aby przełączyć inne aplikacje, które są zależne od Active Directory.

[Dowiedz się więcej](site-recovery-active-directory.md) na temat odzyskiwania po awarii dla Active Directory i systemu DNS.

## <a name="protect-sql-server"></a>Ochrona programu SQL Server

SQL Server oferuje podstawę usługi Data Services dla wielu aplikacji firmowych w lokalnym centrum danych. Site Recovery można używać z technologią SQL Server HA/DR, aby chronić wielowarstwowe aplikacje firmowe korzystające z SQL Server.

Usługa Site Recovery zapewnia:

- Proste i ekonomiczne rozwiązanie do odzyskiwania po awarii dla programu SQL Server. Replikacja wielu wersji i edycji autonomicznych serwerów i klastrów programu SQL Server do platformy Azure lub lokacji dodatkowej.
- Integracja z zawsze włączonymi grupami dostępności programu SQL w celu zarządzania pracą w trybie failover i powrotem po awarii przy użyciu planów odzyskiwania usługi Azure Site Recovery.
- Kompleksowe plany odzyskiwania dla wszystkich warstw w aplikacji, w tym dla baz danych programu SQL Server.
- Skalowanie SQL Server do obciążeń szczytowych przy użyciu Site Recovery _, poprzez ich_ przebudowanie do większych rozmiarów maszyn wirtualnych IaaS na platformie Azure.
- Łatwe testowanie odzyskiwania po awarii programu SQL Server. Możliwość uruchomienia testowego trybu failover w celu przeanalizowania danych i przeprowadzenia kontroli zgodności bez wpływu na środowisko produkcyjne.

[Dowiedz się więcej](site-recovery-sql.md) na temat odzyskiwania po awarii dla programu SQL Server.

## <a name="protect-sharepoint"></a>Ochrona programu SharePoint

Usługa Azure Site Recovery pomaga w ochronie wdrożeń programu SharePoint w następujący sposób:

- Eliminuje potrzebę stosowania farmy rezerwowej (oraz koszty związane z infrastrukturą) na potrzeby odzyskiwania po awarii. Użyj Site Recovery do replikowania całej farmy (warstwy sieci Web, aplikacji i bazy danych) na platformę Azure lub do lokacji dodatkowej.
- Upraszcza wdrażanie aplikacji i zarządzanie nimi. Aktualizacje wdrożone w lokacji głównej są automatycznie replikowane. Aktualizacje są dostępne po przejściu w tryb failover i odzyskiwanie farmy w lokacji dodatkowej. Obniża złożoność zarządzania i koszty związane z utrzymywaniem Aktualności w farmie.
- Upraszcza projektowanie i testowanie aplikacji programu SharePoint poprzez tworzenie na żądanie repliki środowiska z kopią przypominającą środowisko produkcyjne, co umożliwia testowanie i debugowanie.
- Upraszcza przejście do chmury przez użycie usługi Site Recovery do migrowania wdrożeń programu SharePoint do platformy Azure.

[Dowiedz się więcej](site-recovery-sharepoint.md) na temat odzyskiwania po awarii dla programu SharePoint.

## <a name="protect-dynamics-ax"></a>Ochrona programu Dynamics AX

Usługa Azure Site Recovery pomaga chronić rozwiązanie Dynamics AX ERP w następujący sposób:

- Zarządzanie replikacją całego środowiska Dynamics AX (warstw sieci Web i serwera AOS, warstw baz danych, programu SharePoint) do platformy Azure lub lokacji dodatkowej.
- Upraszczanie migracji wdrożeń Dynamics AX do chmury (Azure).
- Upraszczanie projektowania i testowania aplikacji programu Dynamics AX poprzez tworzenie na żądanie kopii środowiska przypominającej środowisko produkcyjne, co umożliwia testowanie i debugowanie.

[Dowiedz się więcej](site-recovery-dynamicsax.md) na temat odzyskiwania po awarii dla dynamicznego systemu AX.

## <a name="protect-remote-desktop-services"></a>Ochrona Usługi pulpitu zdalnego

Usługi pulpitu zdalnego (RDS) umożliwia korzystanie z infrastruktury pulpitów wirtualnych (VDI), pulpitów opartych na sesji i aplikacji, które umożliwiają użytkownikom korzystanie z dowolnego miejsca.

Za pomocą Azure Site Recovery można replikować następujące usługi:

- Replikowanie zarządzanych lub niezarządzanych pulpitów wirtualnych w puli do lokacji dodatkowej.
- Replikowanie aplikacji zdalnych i sesji do lokacji dodatkowej lub platformy Azure.

W poniższej tabeli przedstawiono opcje replikacji:

| **Usługi pulpitu zdalnego** |**Replikacja maszyn wirtualnych platformy Azure na platformę Azure** | **Replikacja maszyn wirtualnych funkcji Hyper-V do lokacji dodatkowej** | **Replikacja maszyn wirtualnych funkcji Hyper-V do platformy Azure** | **Replikacja maszyn wirtualnych VMware do lokacji dodatkowej** | **Replikowanie maszyn wirtualnych VMware na platformę Azure** | **Replikacja serwerów fizycznych do lokacji dodatkowej** | **Replikacja serwerów fizycznych do platformy Azure** |
|---| --- | --- | --- | --- | --- | --- | --- |
| **Pulpit wirtualny w puli (niezarządzany)** |Nie|Tak |Nie |Tak |Nie |Tak |Nie |
| **Pulpit wirtualny w puli (zarządzany i bez dysku UPD)** |Nie|Tak |Nie |Tak |Nie |Tak |Nie |
| **Zdalne aplikacje i sesje pulpitu (bez dysku UPD)** |Tak|Tak |Tak |Tak |Tak |Tak |Tak |

[Dowiedz się więcej](/windows-server/remote/remote-desktop-services/rds-disaster-recovery-with-azure) na temat odzyskiwania po awarii dla usług RDS.

## <a name="protect-exchange"></a>Ochrona programu Exchange

Usługa Site Recovery pomaga chronić program Exchange w następujący sposób:

- W przypadku małych wdrożeń programu Exchange, np. serwerów pojedynczych lub autonomicznych, usługa Site Recovery może wykonywać replikację i stosować tryb failover na platformie Azure lub w lokacji dodatkowej.
- W przypadku większych wdrożeń usługa Site Recovery integruje się z grupami DAG programu Exchange.
- Grupy DAG programu Exchange są zalecanym rozwiązaniem dla odzyskiwania po awarii programu Exchange w przedsiębiorstwie.  Plany odzyskiwania usługi Site Recovery mogą uwzględniać grupy DAG, aby organizować tryb failover grupy DAG między lokacjami.

Aby dowiedzieć się więcej na temat odzyskiwania po awarii dla programu Exchange, zobacz temat program [Exchange grupy DAG składające](/Exchange/high-availability/database-availability-groups/database-availability-groups) i [odzyskiwanie po awarii programu Exchange](/Exchange/high-availability/disaster-recovery/disaster-recovery).

## <a name="protect-sap"></a>Ochrona systemu SAP

Z usługi Site Recovery można korzystać, aby zabezpieczyć wdrożenie SAP w następujący sposób:

- Włącz ochronę aplikacji produkcyjnych środowiska SAP NetWeaver i nienależących do środowiska NetWeaver uruchomionych lokalnie, replikując składniki do platformy Azure.
- Włącz ochronę aplikacji produkcyjnych środowiska SAP NetWeaver i nienależących do środowiska NetWeaver uruchomionych na platformie Azure, replikując składniki do innego centrum danych platformy Azure.
- Skorzystaj z uproszczenia migracji do chmury, używając usługi Site Recovery do migrowania wdrożenia SAP do platformy Azure.
- Uprość uaktualnienia, testowanie i tworzenie prototypów projektu SAP, tworząc na żądanie klony środowiska produkcyjnego na potrzeby testowania aplikacji SAP.

[Dowiedz się więcej](site-recovery-sap.md) na temat odzyskiwania po awarii dla oprogramowania SAP.

## <a name="protect-internet-information-services"></a>Ochrona Internet Information Services

Użyj Site Recovery, aby chronić wdrożenie Internet Information Services (IIS) w następujący sposób:

Usługa Azure Site Recovery zapewnia odzyskiwanie po awarii przez replikowanie krytycznych składników środowiska do zimnej lokacji zdalnej lub chmury publicznej, takiej jak Microsoft Azure. Ponieważ maszyny wirtualne z serwerem sieci Web i bazą danych są replikowane do lokacji odzyskiwania, nie jest wymagana oddzielna kopia zapasowa plików konfiguracyjnych lub certyfikatów. Mapowania i powiązania aplikacji zależne od zmiennych środowiskowych, które są zmieniane po przełączeniu do trybu failover, mogą być aktualizowane za pomocą skryptów zintegrowanych z planami odzyskiwania po awarii. Maszyny wirtualne są przenoszone do lokacji odzyskiwania tylko podczas pracy w trybie failover. Azure Site Recovery ułatwia również organizowanie kompleksowej pracy w trybie failover, oferując następujące możliwości:

- Obsługa sekwencji wyłączania i włączania maszyn wirtualnych w różnych warstwach.
- Dodawanie skryptów w celu zezwolenia na aktualizacje zależności aplikacji i powiązań na maszynach wirtualnych po ich uruchomieniu. Skrypty mogą też służyć do aktualizacji serwera DNS w taki sposób, aby wskazywał lokację odzyskiwania.
- Przydziel adresy IP do maszyn wirtualnych przed przełączeniem w tryb failover, mapując sieci podstawowe i odzyskiwania oraz używając skryptów, które nie muszą być aktualizowane po przejściu do trybu failover.
- Możliwość pracy w trybie failover jednym kliknięciem dla wielu aplikacji sieci Web, która eliminuje zakres pomyłek podczas awarii.
- Możliwość testowania planów odzyskiwania w izolowanym środowisku na potrzeby próbnego odzyskiwania po awarii.

[Dowiedz się więcej](site-recovery-iis.md) na temat odzyskiwania po awarii dla usług IIS.

## <a name="protect-citrix-xenapp-and-xendesktop"></a>Ochrona programów Citrix XenApp i XenDesktop

Aby chronić wdrożenia programów Citrix XenApp i XenDesktop, korzystaj z usługi Site Recovery w następujący sposób:

- Włącz ochronę wdrożenia Citrix XenApp i XenDesktop. Replikowanie różnych warstw wdrożenia na platformę Azure: Active Directory, serwer DNS, serwer bazy danych SQL, program Citrix Delivery Server, serwer sklepu, XenApp Master (VDA), serwer licencji Citrix XenApp.
- Skorzystaj z uproszczenia migracji do chmury, używając usługi Site Recovery do migrowania wdrożenia programów Citrix XenApp i XenDesktop do platformy Azure.
- Ułatw projektowanie i testowanie wdrożenia Citrix XenApp/XenDesktop przez tworzenie na żądanie kopii środowiska przypominającej środowisko produkcyjne, co umożliwia testowanie i debugowanie.
- To rozwiązanie dotyczy tylko pulpitów wirtualnych systemu Windows Server, a nie pulpitów wirtualnych klienta. Pulpity wirtualne klienta nie są jeszcze obsługiwane na potrzeby licencjonowania na platformie Azure. [Dowiedz się więcej](https://azure.microsoft.com/pricing/licensing-faq/) na temat licencjonowania dla komputerów stacjonarnych klienta/serwera na platformie Azure.

[Dowiedz się więcej](site-recovery-citrix-xenapp-and-xendesktop.md) o odzyskiwaniu po awarii dla wdrożeń Citrix XenApp i XenDesktop. Możesz też zapoznać się z oficjalnym [dokumentem Citrix](https://aka.ms/citrix-xenapp-xendesktop-with-asr).

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej](azure-to-azure-quickstart.md) o odzyskiwaniu po awarii dla maszyny wirtualnej platformy Azure.
