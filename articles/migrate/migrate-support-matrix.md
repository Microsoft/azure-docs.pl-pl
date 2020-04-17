---
title: Macierz pomocy technicznej migracji platformy Azure
description: Zawiera podsumowanie ustawień pomocy technicznej i ograniczeń dla usługi Azure Migrate.
ms.topic: conceptual
ms.date: 03/22/2020
ms.author: raynew
ms.openlocfilehash: 0f766bf95bb7e26d942e7dde3f315bbef6d5dc5c
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535200"
---
# <a name="azure-migrate-support-matrix"></a>Macierz pomocy technicznej migracji platformy Azure

Za pomocą [usługi Azure Migrate można](migrate-overview.md) oceniać i migrować maszyny do chmury platformy Microsoft Azure. W tym artykule podsumowano ogólne ustawienia pomocy technicznej i ograniczenia dotyczące scenariuszy i wdrożeń migracji platformy Azure.

## <a name="supported-assessmentmigration-scenarios"></a>Scenariusze oceny/migracji obsługiwane

W tabeli podsumowano obsługiwane scenariusze odnajdywania, oceny i migracji.

**Wdrożenie** | **Szczegóły** 
--- | --- 
**Odnajdowanie specyficzne dla aplikacji** | Możesz odkrywać aplikacje, role i funkcje uruchomione na maszynach wirtualnych VMware. Obecnie ta funkcja jest ograniczona tylko do odnajdywania. Ocena jest obecnie na poziomie maszyny. Nie oferujemy jeszcze oceny aplikacji, roli ani funkcji. 
**Ocena lokalna** | Oceniaj obciążenia lokalne i dane uruchomione na maszynach wirtualnych VMware, maszynach wirtualnych funkcji Hyper V i serwerach fizycznych. Oceniaj przy użyciu usługi Azure Migrate Server Assessment i Microsoft Data Migration Assistant (DMA), a także innych narzędzi i ofert isv.
**Migracja lokalna na platformę Azure** | Migrowanie obciążeń i danych uruchomionych na serwerach fizycznych, maszynach wirtualnych VMware, maszynach wirtualnych funkcji Hyper V, serwerach fizycznych i chmurowych maszynach wirtualnych na platformie Azure. Migruj przy użyciu usługi Azure Migrate Server Assessment i Usługa migracji bazy danych azure (DMS), a także innych narzędzi i ofert usługodawców internetowych.


## <a name="supported-tools"></a>Obsługiwane narzędzia

Obsługa określonych narzędzi jest podsumowana w tabeli.

**Narzędzie** | **Ocenić** | **Migrate (Migracja)** 
--- | --- | ---
Ocena serwera migracji platformy Azure | Oceń [maszyny wirtualne VMware,](tutorial-prepare-vmware.md) [maszyny wirtualne hyper-V](tutorial-prepare-hyper-v.md)i [serwery fizyczne](tutorial-prepare-physical.md). |  Niedostępne (NA)
Migracja serwera usługi Azure Migrate | Nie dotyczy | Migrowanie [maszyn wirtualnych VMware,](tutorial-migrate-vmware.md) [maszyn wirtualnych funkcji Hyper V](tutorial-migrate-hyper-v.md)i [serwerów fizycznych](tutorial-migrate-physical-virtual-machines.md).
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) | Nie dotyczy | Migrowanie maszyn wirtualnych VMware, maszyn wirtualnych funkcji Hyper V, serwerów fizycznych, obciążeń w chmurze publicznej. 
[Cloudamize](https://www.cloudamize.com/platform#tab-0)| Oceń maszyny wirtualne VMware, maszyny wirtualne hyper-V, serwery fizyczne, obciążenia w chmurze publicznej. | Nie dotyczy
[Corent Technology](https://go.microsoft.com/fwlink/?linkid=2084928) | Oceniaj i migruj maszyny wirtualne VMware, maszyny wirtualne hyper-V, serwery fizyczne, obciążenia w chmurze publicznej. |  Migrowanie maszyn wirtualnych VMware, maszyn wirtualnych funkcji Hyper V, serwerów fizycznych, obciążeń w chmurze publicznej.
[Urządzenie 42](https://go.microsoft.com/fwlink/?linkid=2097158) | Oceń maszyny wirtualne VMware, maszyny wirtualne hyper-V, serwery fizyczne, obciążenia w chmurze publicznej.| Nie dotyczy
[Narzędzie DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | Ocena lokalnych baz danych programu SQL Server. | Nie dotyczy
[DMS](https://docs.microsoft.com/azure/dms/dms-overview) | Nie dotyczy | Migrowanie programu SQL Server, Oracle, MySQL, PostgreSQL, MongoDB. 
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Ocena infrastruktury pulpitu wirtualnego (VDI) | Nie dotyczy
[Movere](https://go.microsoft.com/fwlink/?linkid=2109528) | Ocena maszyn wirtualnych VMWare, maszyn wirtualnych funkcji Hyper V, maszyn wirtualnych Xen, maszyn fizycznych, stacji roboczych (w tym VDI), obciążeń chmury publicznej | Nie dotyczy
[RackWare (Wyroby s](https://go.microsoft.com/fwlink/?linkid=2102735) | Nie dotyczy | Migrowanie maszyn wirtualnych VMWare, maszyn wirtualnych funkcji Hyper V, maszyn wirtualnych Xen, maszyn wirtualnych KVM, komputerów fizycznych, obciążeń chmury publicznej 
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295)  | Oceń maszyny wirtualne VMware, maszyny wirtualne hyper-V, serwery fizyczne, obciążenia w chmurze publicznej. | Nie dotyczy
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) | Oceń maszyny wirtualne VMware, maszyny wirtualne funkcji Hyper V, serwery fizyczne, obciążenia w chmurze publicznej i bazy danych programu SQL Server. | Nie dotyczy
[Asystent migracji aplikacji WebApp](https://appmigration.microsoft.com/) | Ocena aplikacji internetowych | Migrowanie aplikacji internetowych.


## <a name="azure-migrate-projects"></a>Projekty migracji platformy Azure

**Pomoc techniczna** | **Szczegóły**
--- | ---
Subskrypcja | W ramach subskrypcji można mieć wiele projektów migracji platformy Azure.
Uprawnienia platformy Azure | Aby utworzyć projekt migracji platformy Azure, potrzebujesz uprawnień współautora lub właściciela w ramach subskrypcji.
Maszyny wirtualne VMware  | Oceń do 35 000 maszyn wirtualnych VMware w jednym projekcie.
Maszyny wirtualne funkcji Hyper-V    | Ocena do 35 000 maszyn wirtualnych z funkcji Hyper V w jednym projekcie.

Projekt może zawierać zarówno maszyny wirtualne VMware, jak i maszyny wirtualne funkcji Hyper-V, aż do limitów oceny.

## <a name="azure-permissions"></a>Uprawnienia platformy Azure

Aby usługa Azure Migrate działała z platformą Azure, potrzebujesz tych uprawnień przed rozpoczęciem oceny i migracji maszyn.

**Zadanie** | **Uprawnienia** | **Szczegóły**
--- | --- | ---
Tworzenie projektu migracji platformy Azure | Twoje konto platformy Azure potrzebuje uprawnień do tworzenia projektu. | Konfiguracja [serwerów](tutorial-prepare-physical.md#assign-permissions-to-create-project) [VMware,](tutorial-prepare-vmware.md#assign-permissions-to-create-project) [Hyper-V](tutorial-prepare-hyper-v.md#assign-permissions-to-create-project)lub fizycznych .
Zarejestruj urządzenie migracji platformy Azure| Usługa Azure Migrate używa lekkiego [urządzenia migracji platformy Azure](migrate-appliance.md) do oceny maszyn za pomocą oceny serwera migracji usługi Azure i do [uruchamiania bezagentowej migracji](server-migrate-overview.md) maszyn wirtualnych vmware za pomocą migracji serwera migracji usługi Azure Migrate Server. To urządzenie odnajduje maszyny i wysyła metadane i dane wydajności do usługi Azure Migrate.<br/><br/> Podczas rejestracji dostawcy rejestru (Microsoft.OffAzure, Microsoft.Migrate i Microsoft.KeyVault) są zarejestrowani w subskrypcji wybranej w urządzeniu, dzięki czemu subskrypcja współpracuje z dostawcą zasobów. Aby się zarejestrować, potrzebujesz dostępu współautora lub właściciela w ramach subskrypcji.<br/><br/> **VMware**— podczas dołączania usługa Azure Migrate tworzy dwie aplikacje usługi Azure Active Directory (Azure AD). Pierwsza aplikacja komunikuje się między agentami urządzenia a usługą Azure Migrate. Aplikacja nie ma uprawnień do wykonywania wywołań zarządzania zasobami platformy Azure lub ma dostęp RBAC dla zasobów. Druga aplikacja uzyskuje dostęp do usługi Azure Key Vault utworzonej w ramach subskrypcji użytkownika tylko dla bezagentowej migracji VMware. W przypadku migracji bez agenta usługa Azure Migrate tworzy magazyn kluczy do zarządzania kluczami dostępu do konta magazynu replikacji w ramach subskrypcji. Ma dostęp RBAC w usłudze Azure Key Vault (w dzierżawie klienta) po zainicjowaniu odnajdywania z urządzenia.<br/><br/> **Hyper-V**-Podczas dołączania. Usługa Azure Migrate tworzy jedną aplikację usługi Azure AD. Aplikacja komunikuje się między agentami urządzenia a usługą Azure Migrate. Aplikacja nie ma uprawnień do wykonywania wywołań zarządzania zasobami platformy Azure lub ma dostęp RBAC dla zasobów. | Konfiguracja [serwerów](tutorial-prepare-physical.md#assign-permissions-to-register-the-appliance) [VMware,](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance) [Hyper-V](tutorial-prepare-hyper-v.md#assign-permissions-to-register-the-appliance)lub fizycznych .
Tworzenie magazynu kluczy dla migracji bez agenta VMware | Aby przeprowadzić migrację maszyn wirtualnych vmware za pomocą bezagentowej migracji serwera migracji usługi Azure Migrate Server, usługa Azure Migrate tworzy magazyn kluczy do zarządzania kluczami dostępu do konta magazynu replikacji w ramach subskrypcji. Aby utworzyć magazyn, należy ustawić uprawnienia (właściciel lub współautor i administrator dostępu użytkownika) w grupie zasobów, w której znajduje się projekt migracji platformy Azure. | [Konfigurowanie](tutorial-prepare-vmware.md#assign-permissions-to-create-a-key-vault) uprawnień.

## <a name="supported-geographies-public-cloud"></a>Obsługiwane obszary geograficzne (chmura publiczna)

Można utworzyć projekt migracji platformy Azure w wielu regionach geograficznych w chmurze publicznej. Chociaż można tworzyć projekty tylko w tych regionach geograficznych, można oceniać lub migrować maszyny dla innych lokalizacji docelowych. Geografia projektu jest używana tylko do przechowywania odnalezionych metadanych.

**Geografia** | **Lokalizacja przechowywania metadanych**
--- | ---
Azja i Pacyfik | Azja Wschodnia lub Azja Południowo-Wschodnia
Australia | Australia Wschodnia lub Australia Południowo-Wschodnia
Brazylia | Brazylia Południowa
Kanada | Kanada Środkowa lub Kanada Wschodnia
Europa | Europa Północna lub Europa Zachodnia
Francja | Francja Środkowa
Indie | Indie Środkowe lub Południowe Indie
Japonia |  Japonia Wschodnia lub Japonia Zachodnia
Korea | Korea Środkowa lub Korea Południowa
Wielka Brytania | Wielka Brytania Południowa lub Wielka Brytania Zachód
Stany Zjednoczone | Środkowe stany USA lub zachodnie stany USA 2


## <a name="supported-geographies-azure-government"></a>Obsługiwane lokalizacje geograficzne (Azure Government)

**Zadanie** | **Geografia** | **Szczegóły**
--- | --- | ---
Tworzenie projektu | Stany Zjednoczone | Metadane są przechowywane w us Gov Arizona, US Gov Virginia
Ocena docelowa | Stany Zjednoczone | Regiony docelowe: US Gov Arizona, US Gov Virginia/US Gov Texas
Replikacja docelowa | Stany Zjednoczone | Regiony docelowe: US DoD Central, US DoD East, US Gov Arizona, US Gov Iowa, US Gov Texas, US Gov Virginia


## <a name="vmware-assessment-and-migration"></a>Ocena i migracja VMware

[Przejrzyj](migrate-support-matrix-vmware.md) macierz pomocy technicznej oceny serwera migracji i migracji serwera platformy Azure dla maszyn wirtualnych VMware.

## <a name="hyper-v-assessment-and-migration"></a>Ocena i migracja funkcji Hyper-V

[Przejrzyj](migrate-support-matrix-hyper-v.md) macierz pomocy technicznej oceny serwera migracji i migracji serwera platformy Azure dla maszyn wirtualnych z programem Hyper V.



## <a name="azure-migrate-versions"></a>Wersje migracji platformy Azure

Istnieją dwie wersje usługi Azure Migrate:

- **Bieżąca wersja:** Za pomocą tej wersji można tworzyć nowe projekty migracji platformy Azure, odnajdywania lokalnych ocen i organizowania ocen i migracji. [Dowiedz się więcej](whats-new.md).
- **Poprzednia wersja**: Dla klienta przy użyciu poprzedniej wersji usługi Azure Migrate (tylko ocena lokalnych maszyn wirtualnych VMware był obsługiwany), należy teraz użyć bieżącej wersji. W poprzedniej wersji nie można utworzyć nowych projektów migracji platformy Azure ani wykonywać nowych odnajdek.

## <a name="next-steps"></a>Następne kroki

- [Oceń maszyny wirtualne VMware pod](tutorial-assess-vmware.md) kątem migracji.
- [Oceń maszyny wirtualne funkcji Hyper V pod](tutorial-assess-hyper-v.md) kątem migracji.

