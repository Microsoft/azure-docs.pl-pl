---
title: Co nowego w Azure Migrate
description: Dowiedz się więcej na temat Nowości i ostatnich aktualizacji w usłudze Azure Migrate.
ms.topic: overview
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.date: 04/19/2020
ms.custom: mvc
ms.openlocfilehash: f50737bdefc9065c9b16633ba3360bda1d028cd6
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043052"
---
# <a name="whats-new-in-azure-migrate"></a>Co nowego w Azure Migrate

[Azure Migrate](migrate-services-overview.md) ułatwia odnajdywanie, ocenianie i migrowanie lokalnych serwerów, aplikacji i danych do chmury Microsoft Azure. W tym artykule zestawiono nowe wersje i funkcje programu Azure Migrate.

## <a name="update-march-2021"></a>Aktualizacja (marzec 2021)
- Obsługa dostarczania wielu poświadczeń serwera na Azure Migrate urządzeniu w celu odnajdywania zainstalowanych aplikacji (spisu oprogramowania), analizy zależności bez wykorzystania agentów oraz odnajdywania wystąpień SQL Server i baz danych w środowisku programu VMware. [Dowiedz się więcej](tutorial-discover-vmware.md#provide-server-credentials)
- Odnajdywanie i Ocena SQL Server wystąpień i baz danych działających w środowisku VMware jest teraz w wersji zapoznawczej. [Więcej informacji](concepts-azure-sql-assessment-calculation.md)<br/>Aby wypróbować tę funkcję, użyj [tego linku](https://aka.ms/AzureMigrate/SQL) , aby utworzyć projekt w regionie **Australia Wschodnia** . Aby rozpocząć, zapoznaj się z samouczkami [odnajdywania](tutorial-discover-vmware.md) i [oceny](tutorial-assess-sql.md) .

## <a name="update-january-2021"></a>Aktualizacja (styczeń 2021)
-  Azure Migrate: Narzędzie do migracji serwera umożliwia teraz Migrowanie maszyn wirtualnych VMware, serwerów fizycznych i maszyn wirtualnych z innych chmur do maszyn wirtualnych platformy Azure z dyskami szyfrowanymi przy użyciu szyfrowania po stronie serwera za pomocą kluczy zarządzanych przez klienta (CMK).

## <a name="update-december-2020"></a>Aktualizacja (grudzień 2020)
- Azure Migrate teraz automatycznie instaluje agenta maszyny wirtualnej platformy Azure na maszynach wirtualnych VMware podczas migrowania ich do platformy Azure przy użyciu metody bez agenta migracji VMware.
- Migracja maszyn wirtualnych programu VMware do usługi Azure Virtual Machines z dyskami szyfrowanymi przy użyciu funkcji szyfrowania po stronie serwera (SSE) z kluczami zarządzanymi przez klienta (CMK) przy użyciu migracji serwera Azure Migrate (replikacja bez agenta) jest teraz dostępna za pośrednictwem Azure Portal.

## <a name="update-september-2020"></a>Aktualizacja (2020 września)
- Migracja serwerów do Strefy dostępności jest teraz obsługiwana.
- Teraz jest obsługiwana migracja maszyn wirtualnych i serwerów fizycznych opartych na interfejsie UEFI do maszyn wirtualnych generacji 2 platformy Azure. W tej wersji Azure Migrate: Narzędzie do migracji serwera nie wykona konwersji z maszyny wirtualnej generacji 2 na maszynę wirtualną generacji 1 podczas migracji.
- Dostępny jest nowy pulpit nawigacyjny oceny Power BI Azure Migrate, który ułatwia porównanie kosztów różnych ustawień oceny. Pulpit nawigacyjny zawiera narzędzie programu PowerShell, które automatycznie tworzy oceny, które są podłączone do pulpitu nawigacyjnego Power BI. [Dowiedz się więcej.](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/assessment-utility)
- Analiza zależności (bez wykorzystania agentów) można teraz uruchamiać jednocześnie na maszynach wirtualnych 1000.
- Analizę zależności (bez wykorzystania agentów) można teraz włączyć lub wyłączyć na dużą skalę przy użyciu skryptów programu PowerShell. [Dowiedz się więcej.](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale)
- Wizualizowanie połączeń sieciowych w Power BI przy użyciu danych zbieranych przy użyciu analizy zależności (bez wykorzystania agentów) [Dowiedz się więcej.](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale)
- Migracja maszyn wirtualnych VMware z rozmiarem dysku danych wynoszącym maksymalnie 32 TB jest teraz obsługiwana przy użyciu Azure Migrate: Metoda migracji oprogramowania VMware bez agenta.

## <a name="update-august-2020"></a>Aktualizacja (sierpień 2020)

- Udoskonalone środowisko dołączania, w którym jest generowany Azure Migrate klucz projektu z portalu i służy do ukończenia rejestracji urządzenia.
- Opcja pobrania plików JAJOWE/VHD lub skryptów Instalatora z portalu w celu skonfigurowania odpowiednio urządzeń VMware i funkcji Hyper-V.
- Odświeżanie programu Configuration Manager przy użyciu ulepszonego środowiska użytkownika.
- Obsługa wielu poświadczeń dla odnajdywania maszyn wirtualnych funkcji Hyper-V.

## <a name="update-july-2020"></a>Aktualizacja (lipiec 2020)

- Migracja oprogramowania VMware bez agenta obsługuje teraz współbieżną replikację maszyn wirtualnych 300 na program vCenter

## <a name="update-june-2020"></a>Aktualizacja (Czerwiec 2020)

- Ocenianie migracji lokalnych maszyn wirtualnych programu VMware do [rozwiązania Azure VMware (Automatyczna synchronizacja)](./concepts-azure-vmware-solution-assessment-calculation.md) jest teraz obsługiwane. [Dowiedz się więcej](how-to-create-azure-vmware-solution-assessment.md)
- Obsługa wielu poświadczeń na urządzeniu na potrzeby odnajdywania serwera fizycznego.
- Obsługa zezwalania na logowanie do platformy Azure z urządzenia dla dzierżawy, w której skonfigurowano ograniczenie dzierżawy.


## <a name="update-april-2020"></a>Aktualizacja (kwiecień 2020)

Azure Migrate obsługuje wdrożenia w Azure Government. 

- Można odnajdywać i oceniać maszyny wirtualne VMware, maszyny wirtualne funkcji Hyper-V i serwery fizyczne.
- Maszyny wirtualne programu VMware, maszyny wirtualne funkcji Hyper-V i serwery fizyczne można migrować do platformy Azure.
- W przypadku migracji oprogramowania VMware można korzystać z migracji bez wykorzystania agentów lub z wykorzystaniem agentów. [Dowiedz się więcej](server-migrate-overview.md).
- [Przejrzyj](migrate-support-matrix.md#supported-geographies-azure-government) obsługiwane lokalizacje geograficzne i regiony Azure Government.
- [Analiza zależności oparta na agentach](concepts-dependency-visualization.md#agent-based-analysis) nie jest obsługiwana w Azure Government.
- Funkcje w wersji zapoznawczej są obsługiwane w Azure Government, w ramach [analizy zależności bez agenta](concepts-dependency-visualization.md#agentless-analysis)i [odnajdywania aplikacji](how-to-discover-applications.md).


## <a name="update-march-2020"></a>Aktualizacja (marzec 2020)

W celu skonfigurowania [urządzenia Azure Migrate](migrate-appliance.md)jest teraz dostępna Instalacja oparta na skrypcie:

- Instalacja oparta na skrypcie jest alternatywą dla. Instalacja urządzenia komórki jajowe (VMware)/VHD (Hyper-V).
- Zawiera skrypt Instalatora programu PowerShell, którego można użyć do skonfigurowania urządzenia dla oprogramowania VMware/Hyper-V na istniejącym komputerze z systemem Windows Server 2016.

## <a name="update-november-2019"></a>Aktualizacja (listopad 2019)

Dodano kilka nowych funkcji do Azure Migrate:

- **Ocena serwera fizycznego**. Teraz jest obsługiwana Ocena lokalnych serwerów fizycznych, oprócz migracji serwera fizycznego, która jest już obsługiwana.
- **Ocena na podstawie importu**. Teraz obsługiwana jest ocena maszyn przy użyciu metadanych i danych wydajności dostępnych w pliku CSV.
- **Odnajdywanie aplikacji**: Azure Migrate obsługuje teraz odnajdywanie aplikacji, ról i funkcji na poziomie aplikacji przy użyciu urządzenia Azure Migrate. Jest to obecnie obsługiwane tylko w przypadku maszyn wirtualnych VMware i jest ograniczone tylko do odnajdowania (ocena nie jest obecnie obsługiwana). [Dowiedz się więcej](how-to-discover-applications.md)
- **Wizualizacja zależności bez agenta**: nie trzeba już jawnie instalować agentów w celu wizualizacji zależności. Teraz obsługiwane są zarówno Agent, jak i oparte na agentach.
- **Pulpit wirtualny**: Użyj narzędzi niezależnych dostawców oprogramowania, aby ocenić i zmigrować lokalną infrastrukturę pulpitu wirtualnego (VDI) do pulpitu wirtualnego systemu Windows na platformie Azure.
- **Aplikacja internetowa**: Asystent migracji Azure App Service używany do oceniania i migracji aplikacji sieci Web, jest teraz zintegrowana z Azure Migrate.

Dodano nowe narzędzia do oceny i migracji do Azure Migrate:

- **Stojak**: zapewnianie migracji do chmury.
- Produkt **przenoszenia**: Ocena oferty.

[Dowiedz się więcej](migrate-services-overview.md) o korzystaniu z narzędzi i ofert niezależnych dostawców oprogramowania na potrzeby oceny i migracji w programie Azure Migrate.

## <a name="azure-migrate-current-version"></a>Azure Migrate bieżąca wersja

Bieżąca wersja Azure Migrate (wydana w lipcu 2019) zawiera wiele nowych funkcji:

- **Ujednolicona platforma migracji**: Azure Migrate teraz oferuje pojedynczy Portal, który pozwala na scentralizowanie i monitorowanie podróży migracji do platformy Azure oraz zarządzanie nią przy użyciu ulepszonego przepływu wdrożenia i środowiska portalu.
- **Narzędzia do oceny i migracji**: usługa Azure Migrate udostępnia narzędzia natywne i integruje się z innymi usługami platformy Azure, a także z narzędziami niezależnych dostawców oprogramowania (ISV). [Dowiedz się więcej](migrate-services-overview.md#isv-integration) o integracji niezależnego dostawcy oprogramowania.
- **Azure Migrate oceny**: za pomocą narzędzia do oceny serwera Azure Migrate można ocenić maszyny wirtualne VMware i maszyny wirtualne funkcji Hyper-V do migracji na platformę Azure. Możesz również ocenić migrację przy użyciu innych usług platformy Azure i narzędzi niezależnych dostawców oprogramowania.
- **Azure Migrate migracji**: za pomocą narzędzia migracji serwera Azure Migrate można migrować lokalne maszyny wirtualne VMware i maszyny wirtualne funkcji Hyper-V do platformy Azure, a także serwery fizyczne, inne serwery zwirtualizowane oraz maszyny wirtualne w chmurze prywatnej/publicznej. Ponadto można migrować do platformy Azure za pomocą narzędzi niezależnych dostawców oprogramowania.
- **Urządzenie Azure Migrate**: Azure Migrate wdraża lekkim urządzeniem do odnajdywania i oceniania lokalnych maszyn wirtualnych VMware i maszyn wirtualnych funkcji Hyper-V.
    - To urządzenie jest używane przez Azure Migrate oceny serwera i migracji serwera Azure Migrate na potrzeby migracji bez agentów.
    - Urządzenie stale odnajduje metadane serwera i dane wydajności na potrzeby oceny i migracji.  
- **Migracja maszyny wirtualnej VMware**: migracja serwera Azure Migrate zapewnia kilka metod migrowania lokalnych maszyn wirtualnych programu VMware na platformę Azure.  Migracja bez agentów przy użyciu urządzenia Azure Migrate oraz migracja oparta na agentach, która korzysta z urządzenia replikacji, i wdraża agenta na każdej maszynie wirtualnej, która ma zostać zmigrowana. [Dowiedz się więcej](server-migrate-overview.md)
 - **Ocena i migracja bazy danych**: w Azure Migrate można ocenić lokalne bazy danych na potrzeby migracji na platformę Azure przy użyciu usługi azure Database Asystent migracji. Bazę danych można migrować przy użyciu Azure Database Migration Service.
- **Migracja aplikacji sieci Web**: możesz ocenić aplikacje sieci Web przy użyciu publicznego adresu URL punktu końcowego z Azure App Service. W przypadku migracji wewnętrznych aplikacji .NET można pobrać i uruchomić App Service Asystent migracji.
- **Urządzenie Data Box**: zaimportuj duże ilości danych w trybie offline na platformę Azure przy użyciu Azure Data Box w Azure Migrate.

## <a name="azure-migrate-previous-version"></a>Azure Migrate poprzednią wersję

Jeśli używasz wcześniejszej wersji Azure Migrate (obsługiwana jest tylko Ocena lokalnych maszyn wirtualnych programu VMware), należy teraz używać bieżącej wersji. W poprzedniej wersji nie można już tworzyć nowych projektów Azure Migrate ani wykonywać nowych odkrycia. Nadal możesz uzyskiwać dostęp do istniejących projektów. Aby to zrobić, w Azure Portal > **wszystkie usługi**, Wyszukaj **Azure Migrate**. W Azure Migrate powiadomieniach jest dostępny link umożliwiający dostęp do starych projektów Azure Migrate.



## <a name="next-steps"></a>Następne kroki

- [Uzyskaj więcej informacji](https://azure.microsoft.com/pricing/details/azure-migrate/) o cenach usługi Azure Migrate.
- [Przejrzyj często zadawane pytania](resources-faq.md) dotyczące usługi Azure Migrate.
- Wypróbuj nasze samouczki, aby ocenić [maszyny wirtualne VMware](./tutorial-assess-vmware-azure-vm.md) i [maszyny wirtualne funkcji Hyper-V](tutorial-assess-hyper-v.md).