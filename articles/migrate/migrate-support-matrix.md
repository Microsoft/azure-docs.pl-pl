---
title: Macierz obsługi Azure Migrate
description: Zawiera podsumowanie ustawień i ograniczeń pomocy technicznej dla usługi Azure Migrate.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: b9ea447b0204ad91065f27d265584c8787167fc2
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870725"
---
# <a name="azure-migrate-support-matrix"></a>Macierz obsługi Azure Migrate

Za pomocą [usługi Azure Migrate](./migrate-services-overview.md) można oceniać i migrować serwery do chmury Microsoft Azure. W tym artykule zestawiono ogólne ustawienia i ograniczenia dotyczące Azure Migrate scenariuszy i wdrożeń.

## <a name="supported-assessmentmigration-scenarios"></a>Obsługiwane scenariusze oceny/migracji

W tabeli zestawiono obsługiwane scenariusze odnajdywania, oceny i migracji.

**Wdrożenie** | **Szczegóły** 
--- | --- 
**Odnajdywanie** | Można odnajdywać metadane serwera i dynamiczne dane wydajności.
**Odnajdywanie aplikacji** | Możesz wykrywać aplikacje, role i funkcje działające na maszynach wirtualnych VMware. Obecnie ta funkcja jest ograniczona tylko do odnajdowania. Ocena jest obecnie na poziomie serwera. Nie oferujemy jeszcze ocen dotyczących aplikacji, roli lub funkcji. 
**Ocena** | Oceniaj obciążenia lokalne i dane uruchomione na maszynach wirtualnych VMware, maszynach wirtualnych funkcji Hyper-V i serwerach fizycznych. Oceń przy użyciu Azure Migrate: Ocena serwera, Microsoft Data Migration Assistant (DMA), a także inne narzędzia i oferty niezależnych dostawców oprogramowania.
**Migracja** | Migrowanie obciążeń i danych działających na serwerach fizycznych, maszynach wirtualnych VMware, maszynach wirtualnych funkcji Hyper-V, serwerach fizycznych i maszynach wirtualnych opartych na chmurze na platformie Azure. Migruj przy użyciu Azure Migrate: Ocena serwera i Azure Database Migration Service (DMS), a także inne narzędzia i oferty niezależnych dostawców oprogramowania.

> [!NOTE]
> Obecnie narzędzia niezależnego dostawcy oprogramowania nie mogą wysyłać danych do Azure Migrate w Azure Government. Można używać zintegrowanych narzędzi firmy Microsoft lub niezależnie używać narzędzi partnerskich.

## <a name="supported-tools"></a>Obsługiwane narzędzia


W tabeli przedstawiono obsługę określonego narzędzia.

**Narzędzie** | **Ocena** | **Migrate** 
--- | --- | ---
Azure Migrate: Ocena serwera | Oceniaj [maszyny wirtualne programu VMware](./tutorial-discover-vmware.md), [maszyny wirtualne funkcji Hyper-V](./tutorial-discover-hyper-v.md)i [serwery fizyczne](./tutorial-discover-physical.md). |  Niedostępne (nie dotyczy)
Azure Migrate: Migracja serwera | Nie dotyczy | Migrowanie [maszyn wirtualnych VMware](tutorial-migrate-vmware.md), [maszyn wirtualnych funkcji Hyper-V](tutorial-migrate-hyper-v.md)i [serwerów fizycznych](tutorial-migrate-physical-virtual-machines.md).
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) | Nie dotyczy | Migrowanie maszyn wirtualnych VMware, maszyn wirtualnych funkcji Hyper-V, serwerów fizycznych i innych obciążeń w chmurze. 
[Cloudamize](https://www.cloudamize.com/platform#tab-0)| Oceniaj maszyny wirtualne VMware, maszyny wirtualne funkcji Hyper-V, serwery fizyczne i inne obciążenia w chmurze. | Nie dotyczy
[Corent Technology](https://go.microsoft.com/fwlink/?linkid=2084928) | Oceniaj maszyny wirtualne VMware, maszyny wirtualne funkcji Hyper-V, serwer fizyczny — inne obciążenia w chmurze. |  Migrowanie maszyn wirtualnych VMware, maszyn wirtualnych funkcji Hyper-V, serwerów fizycznych, obciążeń chmury publicznej.
[Urządzenie 42](https://go.microsoft.com/fwlink/?linkid=2097158) | Oceniaj maszyny wirtualne VMware, maszyny wirtualne funkcji Hyper-V, serwery fizyczne i inne obciążenia w chmurze.| Nie dotyczy
[Narzędzie DMA](/sql/dma/dma-overview?view=sql-server-2017) | Oceń SQL Server baz danych. | Nie dotyczy
[DMS](../dms/dms-overview.md) | Nie dotyczy | Migrowanie SQL Server, Oracle, MySQL, PostgreSQL, MongoDB. 
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Ocenianie infrastruktury pulpitu wirtualnego (VDI) | Nie dotyczy
[Movere](https://www.movere.io/) | Oceniaj maszyny wirtualne VMware, maszyny wirtualne funkcji Hyper-V, maszyny wirtualne Xen, serwery fizyczne, stacje robocze (w tym infrastruktury VDI) i inne obciążenia w chmurze. | Nie dotyczy
[Stojaki](https://go.microsoft.com/fwlink/?linkid=2102735) | Nie dotyczy | Migrowanie maszyn wirtualnych VMWare, maszyn wirtualnych funkcji Hyper-V, maszyn wirtualnych Xen, maszyn wirtualnych KVM, serwerów fizycznych i innych obciążeń w chmurze 
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295)  | Oceniaj maszyny wirtualne VMware, maszyny wirtualne funkcji Hyper-V, serwery fizyczne i inne obciążenia w chmurze. | Nie dotyczy
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) | Oceniaj maszyny wirtualne programu VMware, maszyny wirtualne funkcji Hyper-V, serwery fizyczne i inne obciążenia chmury oraz SQL Server bazy danych. | Nie dotyczy
[Webapp Asystent migracji](https://appmigration.microsoft.com/) | Ocenianie aplikacji sieci Web | Migrowanie aplikacji sieci Web.
[Zerto](https://go.microsoft.com/fwlink/?linkid=2157322) | Nie dotyczy |  Migrowanie maszyn wirtualnych VMware, maszyn wirtualnych funkcji Hyper-V, serwerów fizycznych i innych obciążeń w chmurze.


## <a name="project"></a>Project

**Pomoc techniczna** | **Szczegóły**
--- | ---
Subskrypcja | Może mieć wiele projektów w ramach subskrypcji.
Uprawnienia platformy Azure | Użytkownik musi mieć uprawnienia współautora lub właściciela w subskrypcji, aby utworzyć projekt.
Maszyny wirtualne VMware  | Oceń do 35 000 maszyn wirtualnych VMware w jednym projekcie.
Maszyny wirtualne funkcji Hyper-V    | Oceń do 35 000 maszyn wirtualnych funkcji Hyper-V w jednym projekcie.

Projekt może zawierać zarówno maszyny wirtualne VMware, jak i maszyny wirtualne funkcji Hyper-V, a także limity oceny.

## <a name="azure-permissions"></a>Uprawnienia platformy Azure

Aby Azure Migrate do pracy z platformą Azure, musisz mieć te uprawnienia przed rozpoczęciem oceniania i migrowania serwerów.

**Zadanie** | **Uprawnienia** | **Szczegóły**
--- | --- | ---
Tworzenie projektu | Twoje konto platformy Azure wymaga uprawnień do utworzenia projektu. | Konfiguracja programu [VMware](./tutorial-discover-vmware.md#prepare-an-azure-user-account), [funkcji Hyper-V](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account)lub [serwerów fizycznych](./tutorial-discover-physical.md#prepare-an-azure-user-account).
Rejestrowanie urządzenia Azure Migrate| Azure Migrate korzysta z uproszczonego [urządzenia Azure Migrate](migrate-appliance.md) do oceny serwerów z Azure Migrate: Ocena serwera oraz do uruchamiania [migracji bez agentów](server-migrate-overview.md) maszyn wirtualnych VMware z Azure Migrate: Migracja serwera. To urządzenie umożliwia odnajdywanie serwerów oraz wysyłanie metadanych i danych wydajności do Azure Migrate.<br/><br/> W trakcie rejestracji dostawcy rejestru (Microsoft. OffAzure, Microsoft. zmigrować i Microsoft. kluczy) są zarejestrowani z subskrypcją wybraną w urządzeniu, dzięki czemu subskrypcja współpracuje z dostawcą zasobów. Aby się zarejestrować, musisz mieć uprawnienia współautora lub właściciela subskrypcji.<br/><br/> **VMware**— podczas dołączania Azure Migrate tworzy dwie aplikacje Azure Active Directory (Azure AD). Pierwsza aplikacja komunikuje się między agentami urządzeń a usługą Azure Migrate. Aplikacja nie ma uprawnień do wykonywania wywołań zarządzania zasobami platformy Azure ani uzyskiwania dostępu do usługi Azure RBAC dla zasobów. Druga aplikacja uzyskuje dostęp do Azure Key Vault utworzonego w subskrypcji użytkownika dla migracji VMware bez agentów. W przypadku migracji bez wykorzystania agentów Azure Migrate tworzy Key Vault do zarządzania kluczami dostępu do konta magazynu replikacji w ramach subskrypcji. Ma dostęp do usługi Azure RBAC na Azure Key Vault (w dzierżawie klienta) po zainicjowaniu odnajdowania z urządzenia.<br/><br/> **Funkcja Hyper-V**— podczas dołączania. Azure Migrate tworzy jedną aplikację usługi Azure AD. Aplikacja komunikuje się między agentami urządzeń a usługą Azure Migrate. Aplikacja nie ma uprawnień do wykonywania wywołań zarządzania zasobami platformy Azure ani uzyskiwania dostępu do usługi Azure RBAC dla zasobów. | Konfiguracja programu [VMware](./tutorial-discover-vmware.md#prepare-an-azure-user-account), [funkcji Hyper-V](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account)lub [serwerów fizycznych](./tutorial-discover-physical.md#prepare-an-azure-user-account).
Tworzenie magazynu kluczy dla migracji bez agenta VMware | Aby przeprowadzić migrację maszyn wirtualnych VMware z Azure Migrate bez agentów: Migracja serwera, Azure Migrate tworzy Key Vault do zarządzania kluczami dostępu do konta magazynu replikacji w ramach subskrypcji. Aby utworzyć magazyn, należy ustawić uprawnienia (właściciel lub współautor i administrator dostępu użytkowników) w grupie zasobów, w której znajduje się projekt. | [Skonfiguruj](./tutorial-discover-vmware.md#prepare-an-azure-user-account) uprawnienia.

## <a name="supported-geographies-public-cloud"></a>Obsługiwane lokalizacje geograficzne (chmura publiczna)

Projekt można utworzyć w wielu lokalizacje geograficzne w chmurze publicznej.

- Chociaż można tworzyć tylko projekty w tych lokalizacje geograficzne, można ocenić lub migrować serwery dla innych lokalizacji docelowych.
- Lokalizacja geograficzna projektu służy tylko do przechowywania odnalezionych metadanych.
- Podczas tworzenia projektu należy wybrać lokalizację geograficzną. Projekt i powiązane zasoby są tworzone w jednym z regionów w lokalizacji geograficznej. Region jest przypisywany przez usługę Azure Migrate.

**Lokalizacja geograficzna** | **Lokalizacja magazynu metadanych**
--- | ---
Azja i Pacyfik | Azja Wschodnia lub Azja Południowo-Wschodnia
Australia | Australia Wschodnia lub Australia Południowo-Wschodnia
Brazylia | Brazylia Południowa
Kanada | Kanada środkowa lub Kanada Wschodnia
Europa | Europa Północna lub Europa Zachodnia
Francja | Francja Środkowa
Indie | Indie Środkowe lub Indie Południowe
Japonia |  Japonia Wschodnia lub Japonia Zachodnia
Korea | Korea środkowa lub Korea Południowa
Szwajcaria | Szwajcaria Północna
Zjednoczone Królestwo | Południowe Zjednoczone Królestwo lub Zachodnie Zjednoczone Królestwo
Stany Zjednoczone | Środkowe stany USA lub zachodnie stany USA 2

> [!NOTE]
> W przypadku lokalizacji geograficznej Szwajcarii Szwajcaria Zachodnia są dostępne tylko dla użytkowników interfejsu API REST i wymagają zatwierdzonej subskrypcji.

## <a name="supported-geographies-azure-government"></a>Obsługiwane lokalizacje geograficzne (Azure Government)

**Zadanie** | **Lokalizacja geograficzna** | **Szczegóły**
--- | --- | ---
Tworzenie projektu | Stany Zjednoczone | Metadane są przechowywane w US Gov Arizona, US Gov Wirginia
Ocena celu | Stany Zjednoczone | Regiony docelowe: US Gov Arizona, US Gov Wirginia, US Gov Teksas
Replikacja docelowa | Stany Zjednoczone | Regiony docelowe: US DoD (region środkowy), US DoD (region wschodni), US Gov Arizona, US Gov Iowa, US Gov Teksas, US Gov Wirginia


## <a name="vmware-assessment-and-migration"></a>Ocena i migracja oprogramowania VMware

[Przejrzyj](migrate-support-matrix-vmware.md) Azure Migrate: Ocena serwera i Azure Migrate: Macierz obsługi migracji serwera dla maszyn wirtualnych VMware.

## <a name="hyper-v-assessment-and-migration"></a>Ocena i migracja funkcji Hyper-V

[Przejrzyj](migrate-support-matrix-hyper-v.md) Azure Migrate: Ocena serwera i Azure Migrate: Macierz obsługi migracji serwera dla maszyn wirtualnych funkcji Hyper-V.



## <a name="azure-migrate-versions"></a>Wersje Azure Migrate

Istnieją dwie wersje usługi Azure Migrate:

- **Bieżąca wersja**: korzystając z tej wersji, można tworzyć nowe projekty, odkrywać je w ramach badań lokalnych oraz organizować oceny i migracje. [Dowiedz się więcej](whats-new.md).
- **Poprzednia wersja**: dla klienta korzystającego z poprzedniej wersji Azure Migrate (obsługiwana jest tylko Ocena lokalnych maszyn wirtualnych programu VMware). teraz należy używać bieżącej wersji. W poprzedniej wersji nie można tworzyć nowych projektów ani wykonywać nowych odnajdywania.

## <a name="next-steps"></a>Następne kroki

- [Ocenianie maszyn wirtualnych VMware](./tutorial-assess-vmware-azure-vm.md) do migracji.
- [Oceń maszyny wirtualne funkcji Hyper-V](tutorial-assess-hyper-v.md) do migracji.
