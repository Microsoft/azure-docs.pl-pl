---
title: Odnajdywanie aplikacji, ról i funkcji na serwerach lokalnych przy użyciu Azure Migrate
description: Informacje na temat odnajdywania aplikacji, ról i funkcji na serwerach lokalnych przy użyciu narzędzia do oceny Azure Migrate Server.
ms.topic: article
ms.date: 06/10/2020
ms.openlocfilehash: 960877ac4eab4d8f23b5616dc04628142964b46a
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96483457"
---
# <a name="discover-machine-apps-roles-and-features"></a>Odkryj aplikacje maszynowe, role i funkcje

W tym artykule opisano sposób odnajdywania aplikacji, ról i funkcji na serwerach lokalnych przy użyciu Azure Migrate: Ocena serwera.

Odnajdywanie spisu aplikacji, ról i funkcji działających na maszynach lokalnych pomaga identyfikować i dostosowywać ścieżkę migracji do platformy Azure dla obciążeń. Funkcja odnajdywania aplikacji korzysta z urządzenia Azure Migrate, aby przeprowadzić odnajdywanie przy użyciu poświadczeń gościa maszyny wirtualnej. Funkcja odnajdywania aplikacji nie ma żadnego agenta. Nic nie jest zainstalowane na maszynach wirtualnych.

> [!NOTE]
> Funkcja odnajdywania aplikacji jest obecnie dostępna tylko w wersji zapoznawczej dla maszyn wirtualnych VMware i jest ograniczona tylko do odnajdowania. Nie oferujemy jeszcze oceny opartej na aplikacji. 


## <a name="before-you-start"></a>Przed rozpoczęciem

- Upewnij się, że wykonano następujące czynności:
    - [Utworzono](./create-manage-projects.md) projekt Azure Migrate.
    - [Dodano](how-to-assess.md) Azure Migrate: Narzędzie do oceny serwera do projektu.
- Przejrzyj temat [Obsługa i wymagania dotyczące odnajdywania aplikacji](migrate-support-matrix-vmware.md#vmware-requirements).
- Upewnij się, że maszyny wirtualne, na których jest uruchomiona funkcja odnajdywania aplikacji, mają zainstalowany program PowerShell w wersji 2,0 lub nowszej, a zainstalowano narzędzia VMware (nowsze niż 10.2.0).
- Sprawdź [wymagania](migrate-appliance.md) dotyczące wdrażania urządzenia Azure Migrate.


## <a name="deploy-the-azure-migrate-appliance"></a>Wdrażanie urządzenia usługi Azure Migrate

1. [Zapoznaj](migrate-appliance.md#appliance---vmware) się z wymaganiami dotyczącymi wdrażania urządzenia Azure Migrate.
2. Przejrzyj adresy URL platformy Azure, do których urządzenie będzie musiało uzyskać [public](migrate-appliance.md#public-cloud-urls) dostęp w [chmurach publicznych i rządowych](migrate-appliance.md#government-cloud-urls).
3. [Przejrzyj dane](migrate-appliance.md#collected-data---vmware) zbierane przez urządzenie podczas odnajdywania i oceny.
4. [Zwróć uwagę](migrate-support-matrix-vmware.md#port-access-requirements) na wymagania dotyczące dostępu do portów dla urządzenia.
5. [Wdróż urządzenie Azure Migrate,](how-to-set-up-appliance-vmware.md) aby rozpocząć odnajdywanie. Aby wdrożyć urządzenie, należy pobrać i zaimportować szablon komórki jajowe do programu VMware w celu utworzenia urządzenia jako maszyny wirtualnej VMware. Należy skonfigurować urządzenie, a następnie zarejestrować je w Azure Migrate.
6. Podczas wdrażania urządzenia w celu rozpoczęcia wykrywania ciągłego należy określić następujące elementy:
    - Nazwa vCenter Server, z którą chcesz nawiązać połączenie.
    - Poświadczenia utworzone dla urządzenia w celu nawiązania połączenia z vCenter Server.
    - Poświadczenia konta utworzone dla urządzenia w celu nawiązania połączenia z maszynami wirtualnymi z systemem Windows/Linux.

Po wdrożeniu urządzenia i uzyskaniu poświadczeń urządzenie uruchamia ciągłe wykrywanie metadanych maszyn wirtualnych i danych wydajności oraz odnajdywanie aplikacji, funkcji i ról.  Czas odnajdowania aplikacji zależy od liczby posiadanych maszyn wirtualnych. Funkcja odnajdywania aplikacji na maszynach wirtualnych 500 zazwyczaj trwa godzinę.

## <a name="verify-permissions"></a>Weryfikowanie uprawnień

[Utworzono konto vCenter Server tylko do odczytu](./tutorial-discover-vmware.md#prepare-vmware) na potrzeby odnajdywania i oceny. Konto tylko do odczytu wymaga uprawnień włączonych do **Virtual Machines**  >  **operacji gościa**, aby można było korzystać z maszyny wirtualnej w celu odnajdywania aplikacji.

### <a name="add-the-user-account-to-the-appliance"></a>Dodaj konto użytkownika do urządzenia

Dodaj konto użytkownika w następujący sposób:

1. Otwórz aplikację zarządzanie urządzeniami. 
2. Przejdź do panelu **Podaj szczegóły programu vCenter** .
3. W obszarze **Znajdź aplikację i zależności na maszynach wirtualnych** kliknij pozycję **Dodaj poświadczenia** .
3. Wybierz **system operacyjny**, podaj przyjazną nazwę konta i hasło w polu **Nazwa użytkownika** / **Password**
6. Kliknij pozycję **Zapisz**.
7. Kliknij przycisk **Zapisz i Rozpocznij odnajdywanie**.

    ![Dodaj konto użytkownika maszyny wirtualnej](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)


## <a name="review-and-export-the-inventory"></a>Przeglądanie i eksportowanie spisu

Po zakończeniu odnajdywania, jeśli podano poświadczenia do odnajdowania aplikacji, możesz przejrzeć i wyeksportować spis aplikacji w Azure Portal.

1. W **Azure Migrate serwery**  >  **Azure Migrate: Ocena serwera**, kliknij liczbę wyświetlaną, aby otworzyć stronę **odnalezione serwery** .

    > [!NOTE]
    > Na tym etapie można także opcjonalnie skonfigurować analizę zależności dla odnalezionych maszyn, aby można było wizualizować zależności między maszynami, które chcesz ocenić. [Dowiedz się więcej](concepts-dependency-visualization.md) o analizie zależności.

2. W **odnalezionych aplikacjach** kliknij liczbę wyświetlaną.
3. W **spisie aplikacji** można przejrzeć odnalezione aplikacje, role i funkcje.
4. Aby wyeksportować spis, w **odnalezionych serwerach** kliknij pozycję **Eksportuj spis aplikacji**.

Spis aplikacji jest eksportowany i pobierany w formacie programu Excel. Arkusz **spisu aplikacji** zawiera wszystkie aplikacje odnalezione na wszystkich komputerach.

## <a name="next-steps"></a>Następne kroki

- [Utwórz ocenę](how-to-create-assessment.md) dla odnalezionych serwerów.
- Ocenianie SQL Server baz danych przy użyciu [Azure Migrate: Ocena bazy danych](/sql/dma/dma-assess-sql-data-estate-to-sqldb?view=sql-server-2017).