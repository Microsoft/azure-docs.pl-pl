---
title: Odnajdywanie aplikacji, ról i funkcji na serwerach lokalnych przy użyciu Azure Migrate
description: Informacje na temat odnajdywania aplikacji, ról i funkcji na serwerach lokalnych przy użyciu narzędzia do oceny Azure Migrate Server.
ms.topic: article
ms.date: 03/12/2020
ms.openlocfilehash: e8ce279afc845ebf37ad4ab8b2ce7236cb18137a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79453586"
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

## <a name="prepare-for-app-discovery"></a>Przygotowanie do odnajdywania aplikacji

1. [Przygotowanie do wdrożenia urządzenia](tutorial-prepare-vmware.md). Przygotowanie obejmuje sprawdzenie ustawień urządzenia oraz skonfigurowanie konta, które będzie używane przez urządzenie w celu uzyskania dostępu do vCenter Server.
2. Upewnij się, że masz konto użytkownika (jeden z nich dla serwerów z systemem Windows i Linux) z uprawnieniami administratora dla maszyn, na których mają być odnajdywane aplikacje, role i funkcje.
3. [Wdróż urządzenie Azure Migrate,](how-to-set-up-appliance-vmware.md) aby rozpocząć odnajdywanie. Aby wdrożyć urządzenie, należy pobrać i zaimportować szablon komórki jajowe do programu VMware w celu utworzenia urządzenia jako maszyny wirtualnej VMware. Należy skonfigurować urządzenie, a następnie zarejestrować je w Azure Migrate.
2. Podczas wdrażania urządzenia w celu rozpoczęcia wykrywania ciągłego należy określić następujące elementy:
    - Nazwa vCenter Server, z którą chcesz nawiązać połączenie.
    - Poświadczenia utworzone dla urządzenia w celu nawiązania połączenia z vCenter Server.
    - Poświadczenia konta utworzone dla urządzenia w celu nawiązania połączenia z maszynami wirtualnymi z systemem Windows/Linux.

Po wdrożeniu urządzenia i uzyskaniu poświadczeń urządzenie uruchamia ciągłe wykrywanie metadanych maszyn wirtualnych i danych wydajności oraz odnajdywanie aplikacji, funkcji i ról.  Czas odnajdowania aplikacji zależy od liczby posiadanych maszyn wirtualnych. Funkcja odnajdywania aplikacji na maszynach wirtualnych 500 zazwyczaj trwa godzinę.

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
