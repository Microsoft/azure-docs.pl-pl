---
title: Ustaw zakres odnajdowania maszyn wirtualnych VMware z Azure Migrate
description: Opisuje sposób ustawiania zakresu odnajdywania na potrzeby oceny i migracji maszyn wirtualnych VMware przy użyciu Azure Migrate.
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: dfc9c12edd93fc720ef716fd43b04e0c193d5803
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88919730"
---
# <a name="set-discovery-scope-for-vmware-vms"></a>Ustaw zakres odnajdowania dla maszyn wirtualnych VMware

W tym artykule opisano sposób ograniczania zakresu odnajdywania maszyn wirtualnych VMware, które są wykrywane w następujący sposób:

- Odnajdywanie maszyn wirtualnych za pomocą [urządzenia Azure Migrate](migrate-appliance-architecture.md) podczas korzystania z Azure Migrate: narzędzia do oceny serwera.
- Odnajdywanie maszyn wirtualnych za pomocą [urządzenia Azure Migrate](migrate-appliance-architecture.md) w przypadku korzystania z Azure Migrate: Narzędzia migracji serwera w przypadku migracji bez agentów maszyn wirtualnych VMware na platformę Azure.

Skonfigurowanie urządzenia spowoduje nawiązanie połączenia z vCenter Server i uruchomienie odnajdywania. Przed podłączeniem urządzenia do vCenter Server można ograniczyć odnajdywanie do vCenter Server centrów danych, klastrów, folderu klastrów, hostów, folderu hostów lub poszczególnych maszyn wirtualnych. Aby ustawić zakres, należy przypisać uprawnienia do konta używanego przez urządzenie w celu uzyskania dostępu do vCenter Server.

## <a name="before-you-start"></a>Przed rozpoczęciem

Jeśli nie skonfigurowano konta użytkownika vCenter, którego Azure Migrate używa do odnajdywania, zrób to teraz na potrzeby [oceny](tutorial-prepare-vmware.md#set-up-permissions-for-assessment) lub [migracji bez agentów](tutorial-prepare-vmware.md#assign-permissions-to-an-account).


## <a name="assign-permissions-and-roles"></a>Przypisywanie uprawnień i ról

Można przypisać uprawnienia do obiektów spisu VMware przy użyciu jednej z dwóch metod:

- Na koncie używanym przez urządzenie Przypisz rolę z uprawnieniami wymaganymi dla obiektów, które mają być objęte zakresem.
- Alternatywnie można przypisać rolę do konta na poziomie centrum danych i propagować do obiektów podrzędnych. Następnie nadaj kontu rolę **Brak dostępu** dla każdego obiektu, którego nie chcesz w zakresie. Nie zalecamy tego podejścia, ponieważ jest to bardzo uciążliwe i może uwidaczniać kontrolę dostępu, ponieważ każdy nowy obiekt podrzędny ma automatycznie udzielony dostęp Dziedziczony z elementu nadrzędnego.

Nie można zakres odnajdywania spisu na poziomie folderu vCenter VM. Jeśli potrzebujesz zakresu odnajdywania do maszyn wirtualnych w folderze maszyny wirtualnej, Utwórz użytkownika i Udziel dostępu indywidualnie dla każdej wymaganej maszyny wirtualnej. Hosty i foldery klastrów są obsługiwane.


### <a name="assign-a-role-for-assessment"></a>Przypisywanie roli do oceny

1. Na koncie vCenter urządzenia, którego używasz do odnajdywania, Zastosuj rolę **tylko do odczytu** dla wszystkich obiektów nadrzędnych, które obsługują maszyny wirtualne, które mają być odnajdywane i oceniane (host, klaster, hosty, folder klastrów, do centrum danych).
2. Propaguj te uprawnienia do obiektów podrzędnych w hierarchii.

    ![Przypisywanie uprawnień](./media/tutorial-assess-vmware/assign-perms.png)

### <a name="assign-a-role-for-agentless-migration"></a>Przypisywanie roli do migracji bez agentów

1. Na koncie vCenter urządzenia, którego używasz do migracji, Zastosuj rolę zdefiniowaną przez użytkownika, która ma [wymagane uprawnienia](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless), do wszystkich obiektów nadrzędnych, które obsługują maszyny wirtualne, które mają być odnajdywane i migrowane.
2. Można nazwać rolę, aby ułatwić jej identyfikację. Na przykład <em>Azure_Migrate</em>.

## <a name="work-around-vm-folder-restriction"></a>Obejście ograniczenia folderów maszyny wirtualnej

Obecnie narzędzie do oceny serwera nie może odnaleźć maszyn wirtualnych w przypadku udzielenia dostępu na poziomie folderu programu vCenter VM. Jeśli chcesz przeznaczać zakres odnajdywania i oceny według folderów maszyny wirtualnej, użyj tego rozwiązania.

1. Przypisz uprawnienia tylko do odczytu do wszystkich maszyn wirtualnych znajdujących się w folderach maszyn wirtualnych, które chcesz określić do zakresu odnajdywania i oceny.
2. Przyznaj dostęp tylko do odczytu do wszystkich obiektów nadrzędnych, które obsługują hosta maszyn wirtualnych, klaster, folder hosts, folder klastrów, do centrum danych. Nie musisz propagować uprawnień do wszystkich obiektów podrzędnych.
3. Aby użyć poświadczeń do odnajdywania, wybierz centrum danych jako **zakres kolekcji**.


Konfiguracja kontroli dostępu opartej na rolach zapewnia, że odpowiednie konto użytkownika vCenter ma dostęp tylko do maszyn wirtualnych specyficznych dla dzierżawy.


## <a name="next-steps"></a>Następne kroki

[Konfigurowanie urządzenia](how-to-set-up-appliance-vmware.md)
