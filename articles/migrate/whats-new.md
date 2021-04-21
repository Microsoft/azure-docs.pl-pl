---
title: Co nowego w programie Azure Migrate
description: Dowiedz się więcej o nowościach i najnowszych aktualizacjach w usłudze Azure Migrate Service.
ms.topic: overview
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.date: 04/19/2020
ms.custom: mvc
ms.openlocfilehash: 3f49c0acc99f82cc2986ca896d40b3998cf47c5c
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835006"
---
# <a name="whats-new-in-azure-migrate"></a>Co nowego w programie Azure Migrate

[Azure Migrate](migrate-services-overview.md) ułatwia odnajdywanie, ocenianie i migrowanie lokalnych serwerów, aplikacji i danych do Microsoft Azure chmury. Ten artykuł zawiera podsumowanie nowych wersji i funkcji Azure Migrate.

## <a name="update-march-2021"></a>Aktualizacja (marzec 2021 r.)
- Obsługa zapewnienia wielu poświadczeń serwera na urządzeniu Azure Migrate w celu odnajdywania zainstalowanych aplikacji (spisu oprogramowania), analizy zależności bez agenta oraz odnajdywania wystąpień SQL Server i baz danych w środowisku VMware. [Dowiedz się więcej](tutorial-discover-vmware.md#provide-server-credentials)
- Odnajdywanie i ocena SQL Server i baz danych uruchomionych w środowisku VMware jest teraz w wersji zapoznawczej. [Dowiedz się więcej](concepts-azure-sql-assessment-calculation.md) Zapoznaj się z [samouczkami odnajdywania](tutorial-discover-vmware.md) [i](tutorial-assess-sql.md) oceny, aby rozpocząć pracę.
- Migracja oprogramowania VMware bez agenta obsługuje teraz współbieżną replikację 500 maszyn wirtualnych na program vCenter.

## <a name="update-january-2021"></a>Aktualizacja (styczeń 2021 r.)
-  Azure Migrate: Narzędzie do migracji serwera umożliwia teraz migrację maszyn wirtualnych VMware, serwerów fizycznych i maszyn wirtualnych z innych chmur do maszyn wirtualnych platformy Azure z dyskami zaszyfrowanymi za pomocą szyfrowania po stronie serwera przy użyciu kluczy zarządzanych przez klienta .

## <a name="update-december-2020"></a>Aktualizacja (grudzień 2020 r.)
- Azure Migrate teraz automatycznie instaluje agenta maszyny wirtualnej platformy Azure na maszynach wirtualnych VMware podczas migrowania ich na platformę Azure przy użyciu metody migracji VMware bez agenta. (Windows Server 2008 R2 i nowsze)
- Migracja maszyn wirtualnych VMware na maszyny wirtualne platformy Azure z dyskami zaszyfrowanymi przy użyciu szyfrowania po stronie serwera (SSE) przy użyciu kluczy zarządzanych przez klienta (CMK) przy użyciu migracji serwera Azure Migrate (replikacja bez agenta) jest teraz dostępna za pośrednictwem Azure Portal.

## <a name="update-september-2020"></a>Aktualizacja (wrzesień 2020 r.)
- Migracja serwerów do Strefy dostępności jest teraz obsługiwana.
- Migracja maszyn wirtualnych i serwerów fizycznych opartych na interfejsie UEFI na maszyny wirtualne 2. generacji platformy Azure jest teraz obsługiwana. W tej wersji narzędzie Azure Migrate: Server Migration nie wykona konwersji z maszyny wirtualnej 2. generacji na maszynę wirtualną 1. generacji podczas migracji.
- Dostępny jest Azure Migrate Power BI nawigacyjny oceny aplikacji, który pomaga porównywać koszty w różnych ustawieniach oceny. Pulpit nawigacyjny jest dostarczany z narzędziem PowerShell, które automatycznie tworzy oceny, które są podłączane do Power BI pulpitu nawigacyjnego. [Dowiedz się więcej.](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/assessment-utility)
- Analizę zależności (bez agenta) można teraz uruchamiać jednocześnie na 1000 maszyn wirtualnych.
- Analizę zależności (bez agenta) można teraz włączać lub wyłączać na dużą skalę za pomocą skryptów programu PowerShell. [Dowiedz się więcej.](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale)
- Wizualizowanie połączeń Power BI przy użyciu danych zebranych przy użyciu analizy zależności (bez agenta) [Dowiedz się więcej.](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale)
- Migracja maszyn wirtualnych VMware o rozmiarze dysku danych do 32 TB jest teraz obsługiwana przy użyciu metody Azure Migrate: Migracja serwera bez agenta VMware.

## <a name="update-august-2020"></a>Aktualizacja (sierpień 2020 r.)

- Ulepszone środowisko dołączania, w Azure Migrate klucz projektu jest generowany z portalu i jest używany do ukończenia rejestracji urządzenia.
- Opcja pobierania plików OVA/VHD lub skryptów instalatora z portalu w celu skonfigurowania odpowiednio urządzeń VMware i Hyper-V.
- Odświeżony menedżer konfiguracji urządzenia z ulepszonym interfejsem użytkownika.
- Obsługa wielu poświadczeń dla odnajdywania maszyn wirtualnych funkcji Hyper-V.

## <a name="update-july-2020"></a>Aktualizacja (lipiec 2020 r.)

- Migracja oprogramowania VMware bez agenta obsługuje teraz współbieżną replikację 300 maszyn wirtualnych na program vCenter

## <a name="update-june-2020"></a>Aktualizacja (czerwiec 2020 r.)

- Oceny migracji lokalnych maszyn wirtualnych VMware do usługi [Azure VMware Solution (AVS)](./concepts-azure-vmware-solution-assessment-calculation.md) są teraz obsługiwane. [Dowiedz się więcej](how-to-create-azure-vmware-solution-assessment.md)
- Obsługa wielu poświadczeń na urządzeniu w celu odnajdywania serwerów fizycznych.
- Obsługa zezwalania na logowanie platformy Azure z urządzenia dla dzierżawy, w której skonfigurowano ograniczenie dzierżawy.


## <a name="update-april-2020"></a>Aktualizacja (kwiecień 2020 r.)

Azure Migrate obsługuje wdrożenia w Azure Government. 

- Można odnajdywać i oceniać maszyny wirtualne VMware, maszyny wirtualne funkcji Hyper-V i serwery fizyczne.
- Maszyny wirtualne VMware, maszyny wirtualne funkcji Hyper-V i serwery fizyczne można migrować na platformę Azure.
- W przypadku migracji VMware można użyć migracji bez agenta lub migracji opartej na agentach. [Dowiedz się więcej](server-migrate-overview.md).
- [Przejrzyj](migrate-support-matrix.md#supported-geographies-azure-government) obsługiwane lokalizacje geograficzne i regiony dla Azure Government.
- [Analiza zależności oparta na agentach](concepts-dependency-visualization.md#agent-based-analysis) nie jest obsługiwana w Azure Government.
- Funkcje w wersji zapoznawczej są obsługiwane Azure Government, w szczególności analiza [zależności bez agenta](concepts-dependency-visualization.md#agentless-analysis)i [odnajdywanie aplikacji.](how-to-discover-applications.md)


## <a name="update-march-2020"></a>Aktualizacja (marzec 2020 r.)

Dostępna jest instalacja oparta na skrypcie do skonfigurowania [Azure Migrate wirtualnego:](migrate-appliance.md)

- Instalacja oparta na skryptach jest alternatywą dla programu . Instalacja OVA (VMware)/dysku VHD (Hyper-V) urządzenia.
- Udostępnia skrypt instalatora programu PowerShell, który może służyć do skonfigurowania urządzenia dla oprogramowania VMware/Hyper-V na istniejącej maszynie z systemem Windows Server 2016.

## <a name="update-november-2019"></a>Aktualizacja (listopad 2019 r.)

Dodano kilka nowych funkcji do Azure Migrate:

- **Ocena serwera fizycznego.** Ocena lokalnych serwerów fizycznych jest teraz obsługiwana, oprócz migracji serwerów fizycznych, która jest już obsługiwana.
- **Ocena oparta na imporcie.** Ocena maszyn przy użyciu metadanych i danych wydajności podanych w pliku CSV jest teraz obsługiwana.
- **Odnajdywanie** aplikacji: Azure Migrate teraz obsługuje odnajdywanie aplikacji, ról i funkcji na poziomie aplikacji przy użyciu Azure Migrate wirtualnej. Ta funkcja jest obecnie obsługiwana tylko dla maszyn wirtualnych VMware i jest ograniczona tylko do odnajdywania (ocena nie jest obecnie obsługiwana). [Dowiedz się więcej](how-to-discover-applications.md)
- **Wizualizacja zależności bez agenta:** nie trzeba już jawnie instalować agentów na potrzeby wizualizacji zależności. Obsługiwane są teraz zarówno bez agenta, jak i oparte na agentach.
- **Pulpit wirtualny:** użyj narzędzi isv tools to assess and migrate on-premises virtual desktop infrastructure (VDI) to Windows Virtual Desktop na platformie Azure.
- **Aplikacja internetowa:** aplikacja Azure App Service Migration Assistant używana do oceniania i migracji aplikacji internetowych jest teraz zintegrowana z Azure Migrate.

Dodano nowe narzędzia do oceny i migracji do Azure Migrate:

- **RackWare:** oferuje migrację do chmury.
- **Movere:** Ocena oferty.

[Dowiedz się więcej](migrate-services-overview.md) o używaniu narzędzi i ofert isV do oceny i migracji w Azure Migrate.

## <a name="azure-migrate-current-version"></a>Azure Migrate bieżąca wersja

Bieżąca wersja Azure Migrate (wydana w lipcu 2019 r.) udostępnia szereg nowych funkcji:

- **Ujednolicona platforma** migracji: usługa Azure Migrate udostępnia teraz jeden portal, który umożliwia scentralizowanie i śledzenie migracji na platformę Azure oraz zarządzanie nimi, dzięki ulepszonemu przepływowi wdrażania i udoskonalonemu interfejsowi portalu.
- **Narzędzia do oceny i migracji:** Azure Migrate narzędzia natywne i integruje się z innymi usługami platformy Azure, a także z narzędziami niezależnego dostawcy oprogramowania. [Dowiedz się więcej](migrate-services-overview.md#isv-integration) o integracji z isV.
- **Azure Migrate oceny:** za pomocą narzędzia Azure Migrate Server Assessment można ocenić maszyny wirtualne VMware i maszyny wirtualne funkcji Hyper-V do migracji na platformę Azure. Możesz również ocenić migrację przy użyciu innych usług platformy Azure i narzędzi isv.
- **Azure Migrate** migracji: za pomocą narzędzia migracji serwera Azure Migrate można migrować lokalne maszyny wirtualne VMware i maszyny wirtualne funkcji Hyper-V na platformę Azure, a także serwery fizyczne, inne serwery zwirtualizowane oraz maszyny wirtualne w chmurze prywatnej/publicznej. Ponadto możesz przeprowadzić migrację na platformę Azure przy użyciu narzędzi isV.
- **Azure Migrate wirtualne:** Azure Migrate uproszczone urządzenie do odnajdywania i oceny lokalnych maszyn wirtualnych VMware i maszyn wirtualnych funkcji Hyper-V.
    - To urządzenie jest używane przez Azure Migrate Server Assessment i Azure Migrate Server Migration w celu migracji bez agenta.
    - Urządzenie stale odnajduje metadane serwera i dane wydajności na potrzeby oceny i migracji.  
- **Migracja maszyn wirtualnych VMware:** Azure Migrate Server Migration udostępnia kilka metod migracji lokalnych maszyn wirtualnych VMware na platformę Azure.  Migracja bez agenta przy użyciu Azure Migrate i migracja oparta na agentach, która korzysta z urządzenia replikacji i wdraża agenta na każdej maszynie wirtualnej, którą chcesz migrować. [Dowiedz się więcej](server-migrate-overview.md)
 - **Ocena i migracja** bazy danych: z Azure Migrate można ocenić lokalne bazy danych do migracji na platformę Azure przy użyciu usługi Azure Database Migration Assistant. Bazy danych można migrować przy użyciu Azure Database Migration Service.
- **Migracja aplikacji internetowej:** aplikacje internetowe można ocenić przy użyciu publicznego adresu URL punktu końcowego z Azure App Service. W przypadku migracji wewnętrznych aplikacji .NET można pobrać i uruchomić App Service Migration Assistant.
- **urządzenie Data Box:** importowanie dużych ilości danych w trybie offline na platformę Azure Azure Data Box użyciu Azure Migrate.

## <a name="azure-migrate-previous-version"></a>Azure Migrate poprzedniej wersji

Jeśli używasz poprzedniej wersji programu Azure Migrate (obsługiwana była tylko ocena lokalnych maszyn wirtualnych VMware), użyj bieżącej wersji. W poprzedniej wersji nie można już tworzyć nowych Azure Migrate ani przeprowadzać nowych odnajdywania. Nadal można uzyskać dostęp do istniejących projektów. Aby to zrobić, w Azure Portal > **wszystkie usługi** wyszukaj **Azure Migrate**. W Azure Migrate aplikacji znajduje się link do uzyskiwania dostępu do starych Azure Migrate projektów.



## <a name="next-steps"></a>Następne kroki

- [Uzyskaj więcej informacji](https://azure.microsoft.com/pricing/details/azure-migrate/) o cenach usługi Azure Migrate.
- [Przejrzyj często zadawane pytania](resources-faq.md) dotyczące usługi Azure Migrate.
- Wypróbuj nasze samouczki, aby ocenić [maszyny wirtualne VMware](./tutorial-assess-vmware-azure-vm.md) i maszyny [wirtualne funkcji Hyper-V.](tutorial-assess-hyper-v.md)
