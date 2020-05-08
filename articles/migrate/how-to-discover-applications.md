---
title: Odnajdywanie aplikacji, ról i funkcji na serwerach lokalnych przy użyciu Azure Migrate
description: Informacje na temat odnajdywania aplikacji, ról i funkcji na serwerach lokalnych przy użyciu narzędzia do oceny Azure Migrate Server.
ms.topic: article
ms.date: 03/12/2020
ms.openlocfilehash: ff9f5489b513cd1405e6b093d7537e4cbcead041
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744614"
---
# <a name="discover-machine-apps-roles-and-features"></a>Odkryj aplikacje maszynowe, role i funkcje

W tym artykule opisano sposób odnajdywania aplikacji, ról i funkcji na serwerach lokalnych przy użyciu Azure Migrate: Ocena serwera.

Odnajdywanie spisu aplikacji, a role/funkcje działające na maszynach lokalnych, ułatwia identyfikowanie i planowanie ścieżki migracji do platformy Azure, która jest dostosowana do obciążeń.

> [!NOTE]
> Funkcja odnajdywania aplikacji jest obecnie dostępna tylko w wersji zapoznawczej dla maszyn wirtualnych VMware i jest ograniczona tylko do odnajdowania. Nie oferujemy jeszcze oceny opartej na aplikacji. Ocena oparta na maszynach dla lokalnych maszyn wirtualnych programu VMware, maszyn wirtualnych funkcji Hyper-V i serwerów fizycznych.

Odnajdywanie aplikacji przy użyciu Azure Migrate: Ocena serwera jest bez wykorzystania agentów. Nic nie jest zainstalowane na maszynach i maszynach wirtualnych. Funkcja oceny serwera używa urządzenia Azure Migrate do przeprowadzenia odnajdywania wraz z poświadczeniami gościa maszyny. Urządzenie zdalnie uzyskuje dostęp do maszyn VMware przy użyciu interfejsów API VMware.


## <a name="before-you-start"></a>Przed rozpoczęciem

1. Upewnij się, że [utworzono](how-to-add-tool-first-time.md) projekt Azure Migrate.
2. Upewnij się, że [dodano](how-to-assess.md) Azure Migrate: Narzędzie do oceny serwera do projektu.
4. Sprawdź [wymagania programu VMware](migrate-support-matrix-vmware.md#vmware-requirements) dotyczące odnajdywania i oceniania maszyn wirtualnych VMware przy użyciu urządzenia Azure Migrate.
5. Sprawdź [wymagania](migrate-appliance.md) dotyczące wdrażania urządzenia Azure Migrate.
6. [Sprawdź pomoc techniczną i wymagania](migrate-support-matrix-vmware.md#application-discovery) dotyczące odnajdywania aplikacji.



## <a name="deploy-the-azure-migrate-appliance"></a>Wdróż urządzenie Azure Migrate

1. [Zapoznaj](migrate-appliance.md#appliance---vmware) się z wymaganiami dotyczącymi wdrażania urządzenia Azure Migrate.
2. Przejrzyj adresy URL platformy Azure, do których urządzenie będzie musiało uzyskać [public](migrate-appliance.md#public-cloud-urls) dostęp w [chmurach publicznych i rządowych](migrate-appliance.md#government-cloud-urls).
3. [Przejrzyj dane](migrate-appliance.md#collected-data---vmware) zbierane przez urządzenie podczas odnajdywania i oceny.
4. [Zwróć uwagę](migrate-support-matrix-vmware.md#port-access) na wymagania dotyczące dostępu do portów dla urządzenia.
5. [Wdróż urządzenie Azure Migrate,](how-to-set-up-appliance-vmware.md) aby rozpocząć odnajdywanie. Aby wdrożyć urządzenie, należy pobrać i zaimportować szablon komórki jajowe do programu VMware w celu utworzenia urządzenia jako maszyny wirtualnej VMware. Należy skonfigurować urządzenie, a następnie zarejestrować je w Azure Migrate.
6. Podczas wdrażania urządzenia w celu rozpoczęcia wykrywania ciągłego należy określić następujące elementy:
    - Nazwa vCenter Server, z którą chcesz nawiązać połączenie.
    - Poświadczenia utworzone dla urządzenia w celu nawiązania połączenia z vCenter Server.
    - Poświadczenia konta utworzone dla urządzenia w celu nawiązania połączenia z maszynami wirtualnymi z systemem Windows/Linux.

Po wdrożeniu urządzenia i uzyskaniu poświadczeń urządzenie uruchamia ciągłe wykrywanie metadanych maszyn wirtualnych i danych wydajności oraz odnajdywanie aplikacji, funkcji i ról.  Czas odnajdowania aplikacji zależy od liczby posiadanych maszyn wirtualnych. Funkcja odnajdywania aplikacji na maszynach wirtualnych 500 zazwyczaj trwa godzinę.

## <a name="prepare-a-user-account"></a>Przygotowywanie konta użytkownika

Utwórz konto do użycia na potrzeby odnajdywania i Dodaj je do urządzenia.

### <a name="create-a-user-account-for-discovery"></a>Tworzenie konta użytkownika na potrzeby odnajdywania

Skonfiguruj konto użytkownika, aby Ocena serwera mogła uzyskać dostęp do maszyny wirtualnej w celu odnajdywania. [Dowiedz się więcej](migrate-support-matrix-vmware.md#application-discovery) o wymaganiach dotyczących konta.


### <a name="add-the-user-account-to-the-appliance"></a>Dodaj konto użytkownika do urządzenia

Dodaj konto użytkownika do urządzenia.

1. Otwórz aplikację zarządzanie urządzeniami. 
2. Przejdź do panelu **Podaj szczegóły programu vCenter** .
3. W obszarze **Znajdź aplikację i zależności na maszynach wirtualnych**kliknij pozycję **Dodaj poświadczenia** .
3. Wybierz **system operacyjny**, podaj przyjazną nazwę konta i**hasło** w polu **Nazwa użytkownika**/
6. Kliknij przycisk **Zapisz**.
7. Kliknij przycisk **Zapisz i Rozpocznij odnajdywanie**.

    ![Dodaj konto użytkownika maszyny wirtualnej](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)





## <a name="review-and-export-the-inventory"></a>Przeglądanie i eksportowanie spisu

Po zakończeniu odnajdywania, jeśli podano poświadczenia do odnajdowania aplikacji, możesz przejrzeć i wyeksportować spis aplikacji w Azure Portal.

1. W **Azure Migrate serwery** > **Azure Migrate: Ocena serwera**, kliknij liczbę wyświetlaną, aby otworzyć stronę **odnalezione serwery** .

    > [!NOTE]
    > Na tym etapie można także opcjonalnie skonfigurować mapowanie zależności dla odnalezionych maszyn, aby można było wizualizować zależności między maszynami, które chcesz ocenić. [Dowiedz się więcej](how-to-create-group-machine-dependencies.md).

2. W **odnalezionych aplikacjach**kliknij liczbę wyświetlaną.
3. W **spisie aplikacji**można przejrzeć odnalezione aplikacje, role i funkcje.
4. Aby wyeksportować spis, w **odnalezionych serwerach**kliknij pozycję **Eksportuj spis aplikacji**.

Spis aplikacji jest eksportowany i pobierany w formacie programu Excel. Arkusz **spisu aplikacji** zawiera wszystkie aplikacje odnalezione na wszystkich komputerach.

## <a name="next-steps"></a>Następne kroki

- [Utwórz ocenę](how-to-create-assessment.md) dla podnoszenia i przesunięcia migracji odnalezionych serwerów.
- Ocenianie SQL Server baz danych przy użyciu [Azure Migrate: Ocena bazy danych](https://docs.microsoft.com/sql/dma/dma-assess-sql-data-estate-to-sqldb?view=sql-server-2017).
