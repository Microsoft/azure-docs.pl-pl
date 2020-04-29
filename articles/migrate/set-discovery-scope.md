---
title: Ustaw zakres odnajdowania maszyn wirtualnych VMware z Azure Migrate
description: Opisuje sposób ustawiania zakresu odnajdywania na potrzeby oceny i migracji maszyn wirtualnych VMware przy użyciu Azure Migrate.
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 29b3077ead168cef2790468d6ac62d1c59c24c11
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80337638"
---
# <a name="set-discovery-scope-for-vmware-vms"></a>Ustaw zakres odnajdowania dla maszyn wirtualnych VMware

W tym artykule opisano sposób ograniczania zakresu odnajdywania maszyn wirtualnych VMware, które są wykrywane przez [urządzenie Azure Migrate](migrate-appliance-architecture.md).

Urządzenie Azure Migrate odnajduje lokalne maszyny wirtualne VMware, gdy: 

- Użyj narzędzia do [oceny serwera Azure Migrate](migrate-services-overview.md#azure-migrate-server-assessment-tool) , aby ocenić maszyny wirtualne VMware na potrzeby migracji na platformę Azure.
- Aby przeprowadzić [migrację](server-migrate-overview.md) maszyn wirtualnych VMware na platformę Azure, użyj narzędzia do [migracji serwera Azure Migrate](migrate-services-overview.md#azure-migrate-server-migration-tool) .

## <a name="set-discovery-scope"></a>Ustaw zakres odnajdowania


Po skonfigurowaniu urządzenia Azure Migrate na potrzeby oceny lub migracji maszyn wirtualnych VMware urządzenie rozpocznie odnajdywanie maszyn wirtualnych i wysyłanie metadanych maszyny wirtualnej na platformę Azure. Przed podłączeniem urządzenia do vCenter Server odnajdywania można ustawić zakres odnajdywania, aby ograniczyć odnajdywanie do vCenter Server centrów danych, klastrów, folderu klastrów, hostów, folderu hostów lub poszczególnych maszyn wirtualnych.

Aby ustawić zakres, należy przypisać uprawnienia do konta, którego Azure Migrate używa do uzyskiwania dostępu do vCenter Server.

## <a name="create-a-vcenter-user-account"></a>Tworzenie konta użytkownika vCenter

Jeśli nie zostało jeszcze skonfigurowane konto użytkownika vCenter używane przez urządzenie Azure Migrate do odnajdywania, oceniania i migrowania maszyn wirtualnych VMware, należy to zrobić w pierwszej kolejności.

1.    Zaloguj się do klienta sieci Web vSphere jako administrator vCenter Server.
2.    Wybierz kolejno pozycje **Administracja** > **Użytkownicy i grupy logowania jednokrotnego**, a następnie kliknij kartę **Użytkownicy** .
3.    Wybierz ikonę **nowego użytkownika** .
4.    Wprowadź nowe informacje o użytkowniku > **OK**.

Uprawnienia konta zależą od tego, co jest wdrażane.

**Funkcja** | **Uprawnienia konta**
--- | ---
[Szacowaniu](tutorial-assess-vmware.md)| Konto wymaga dostępu tylko do odczytu.
[Odnajdywanie aplikacji/ról/funkcji](how-to-discover-applications.md) | Konto wymaga dostępu tylko do odczytu z włączonymi uprawnieniami dla maszyn wirtualnych > operacji gościa.
[Analizuj zależności (bez wykorzystania agentów)](how-to-create-group-machine-dependencies-agentless.md) | Konto wymaga dostępu tylko do odczytu z włączonymi uprawnieniami dla maszyn wirtualnych > operacji gościa.
[Migrowanie (bez agentów)](tutorial-migrate-vmware.md) | Potrzebna jest rola, do której przypisano odpowiednie uprawnienia.<br/><br/> Aby utworzyć rolę, zaloguj się do klienta sieci Web vSphere jako administrator vCenter Server. Wybierz wystąpienie vCenter Server > **Utwórz rolę**. Określ nazwę roli, na przykład <em>Azure_Migrate</em>, i przypisz [wymagane uprawnienia](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) do roli.


## <a name="assign-permissions-on-vcenter-objects"></a>Przypisywanie uprawnień do obiektów vCenter

Uprawnienia do obiektów spisu można przypisywać przy użyciu jednej z dwóch metod:

- Przypisz rolę z wymaganymi uprawnieniami do konta, którego używasz, dla wszystkich obiektów nadrzędnych, które obsługują maszyny wirtualne, które mają być odnajdywane/migrowane.
- Alternatywnie można przypisać rolę i konto użytkownika na poziomie centrum danych i propagować je do obiektów podrzędnych. Następnie nadaj kontu rolę **Brak dostępu** dla każdego obiektu, który nie ma być odnajdywany/migrowany. Nie zalecamy tego podejścia, ponieważ jest to bardzo uciążliwe i może uwidaczniać kontrolę dostępu, ponieważ każdy nowy obiekt podrzędny ma automatycznie udzielony dostęp Dziedziczony z elementu nadrzędnego.

Aby przypisać rolę do konta, którego używasz dla wszystkich odpowiednich obiektów, wykonaj następujące czynności:

- **W celu oceny**: w przypadku oceny maszyny wirtualnej należy zastosować rolę **tylko do odczytu** dla konta użytkownika vCenter dla wszystkich obiektów nadrzędnych, które będą hostować maszyny wirtualne, które mają zostać odnajdywane. Uwzględnione obiekty nadrzędne: host, folder hostów, klaster i folder klastrów w hierarchii, do centrum danych. Propaguj te uprawnienia do obiektów podrzędnych w hierarchii.

    ![Przypisywanie uprawnień](./media/tutorial-assess-vmware/assign-perms.png)

- **W przypadku migracji bez agentów**: w przypadku migracji bez wykorzystania agentów należy zastosować rolę zdefiniowaną przez użytkownika z [uprawnieniami wymaganymi](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) dla konta użytkownika dla wszystkich obiektów nadrzędnych, które mają zostać odnajdywane. Można nazwać rolę <em>Azure_Migrate</em>.

### <a name="scope-support"></a>Obsługa zakresu

Obecnie narzędzie do oceny serwera nie może odnaleźć maszyn wirtualnych, jeśli konto vCenter ma dostęp udzielony na poziomie folderu programu vCenter VM. Obsługiwane są foldery hostów i klastrów.

Jeśli chcesz ograniczyć zakres odnajdywania według folderów maszyny wirtualnej, wykonaj następną procedurę, aby upewnić się, że konto vCenter ma dostęp tylko do odczytu na poziomie maszyny wirtualnej.

1. Przypisz uprawnienia tylko do odczytu wszystkim maszynom wirtualnym w folderach maszyny wirtualnej, które mają być objęte odnajdywaniem.
2. Przyznaj dostęp tylko do odczytu do wszystkich obiektów nadrzędnych, które obsługują maszyny wirtualne.
    - Uwzględniono wszystkie obiekty nadrzędne (host, folder hostów, klaster, folder klastrów) w hierarchii do centrum danych.
    - Nie musisz propagować uprawnień do wszystkich obiektów podrzędnych.
3. Użyj poświadczeń do odnajdowania, wybierając pozycję Centrum **danych jako zakres kolekcji**. Konfiguracja kontroli dostępu opartej na rolach zapewnia, że odpowiednie konto użytkownika vCenter ma dostęp tylko do maszyn wirtualnych specyficznych dla dzierżawy.


## <a name="next-steps"></a>Następne kroki

[Skonfiguruj urządzenie](how-to-set-up-appliance-vmware.md) i [Rozpocznij odnajdywanie ciągłe](how-to-set-up-appliance-vmware.md#start-continuous-discovery-by-providing-vcenter-server-and-vm-credential).
