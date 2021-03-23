---
title: Ustaw zakres odnajdywania serwerów w oprogramowaniu VMware z Azure Migrate
description: Opisuje sposób ustawiania zakresu odnajdowania dla serwerów hostowanych w ramach oceny i migracji oprogramowania VMware przy użyciu Azure Migrate.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/13/2021
ms.openlocfilehash: 29ac42da6560a717f12cd256fdd71282e0bd313f
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775361"
---
# <a name="set-discovery-scope-for-servers-in-vmware-environment"></a>Ustaw zakres odnajdowania dla serwerów w środowisku VMware

W tym artykule opisano sposób ograniczania zakresu odnajdywania serwerów w środowisku VMware, gdy są one następujące:

- Odnajdywanie serwerów z [urządzeniem Azure Migrate](migrate-appliance-architecture.md) podczas korzystania Azure Migrate z narzędzia do odnajdywania i oceny.
- Odnajdywanie serwerów z [urządzeniem Azure Migrate](migrate-appliance-architecture.md) w przypadku korzystania z Azure Migrate: narzędzia do migracji serwera w przypadku migracji bez agentów serwerów ze środowiska VMware na platformę Azure.

Skonfigurowanie urządzenia spowoduje nawiązanie połączenia z vCenter Server i uruchomienie odnajdywania. Przed podłączeniem urządzenia do vCenter Server można ograniczyć odnajdywanie do vCenter Server centrów danych, klastrów, folderu klastrów, hostów, folderu hostów lub poszczególnych serwerów. Aby ustawić zakres, należy przypisać uprawnienia do konta używanego przez urządzenie w celu uzyskania dostępu do vCenter Server.

## <a name="before-you-start"></a>Przed rozpoczęciem

Jeśli nie skonfigurowano konta użytkownika vCenter, którego Azure Migrate używa do odnajdywania, zrób to teraz na potrzeby [oceny](./tutorial-discover-vmware.md#prepare-vmware) lub [migracji bez agentów](./migrate-support-matrix-vmware-migration.md#agentless-migration).


## <a name="assign-permissions-and-roles"></a>Przypisywanie uprawnień i ról

Można przypisać uprawnienia do obiektów spisu VMware przy użyciu jednej z dwóch metod:

- Na koncie używanym przez urządzenie Przypisz rolę z uprawnieniami wymaganymi dla obiektów, które mają być objęte zakresem.
- Alternatywnie można przypisać rolę do konta na poziomie centrum danych i propagować do obiektów podrzędnych. Następnie nadaj kontu rolę **Brak dostępu** dla każdego obiektu, którego nie chcesz w zakresie. Nie zalecamy tego podejścia, ponieważ jest to bardzo uciążliwe i może uwidaczniać kontrolę dostępu, ponieważ każdy nowy obiekt podrzędny ma automatycznie udzielony dostęp Dziedziczony z elementu nadrzędnego.

Nie można zakres odnajdywania spisu na poziomie folderu vCenter Server. Jeśli potrzebujesz zakresu odnajdywania do serwerów w folderze, Utwórz użytkownika i Udziel dostępu indywidualnie dla każdego z wymaganych serwerów. Hosty i foldery klastrów są obsługiwane.


### <a name="assign-a-role-for-assessment"></a>Przypisywanie roli do oceny

1. Na koncie vCenter urządzenia, którego używasz do odnajdywania, Zastosuj rolę **tylko do odczytu** dla wszystkich obiektów nadrzędnych, które mają być odnajdywane i oceniane przez serwery hosta, klaster, hosty, folder klastrów, do centrum danych.
2. Propaguj te uprawnienia do obiektów podrzędnych w hierarchii.

    ![Przypisywanie uprawnień](./media/tutorial-assess-vmware/assign-perms.png)

### <a name="assign-a-role-for-agentless-migration"></a>Przypisywanie roli do migracji bez agentów

1. Na koncie vCenter urządzenia, którego używasz do migracji, Zastosuj rolę zdefiniowaną przez użytkownika, która ma [wymagane uprawnienia](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless), do wszystkich obiektów nadrzędnych, które mają być odnajdywane i migrowane przez serwery.
2. Można nazwać rolę, aby ułatwić jej identyfikację. Na przykład <em>Azure_Migrate</em>.

## <a name="work-around-for-server-folder-restriction"></a>Obejście dla ograniczenia folderu serwera

Obecnie Azure Migrate: narzędzie odnajdywania i oceny nie może odnaleźć serwerów, jeśli dostęp jest udzielany na poziomie folderu programu vCenter Server. Jeśli chcesz ograniczyć zakres odnajdywania i oceny do folderów na serwerze, użyj tego rozwiązania.

1. Przypisz uprawnienia tylko do odczytu na wszystkich serwerach znajdujących się w folderach, w których chcesz określić zakres odnajdywania i oceny.
2. Przyznaj dostęp tylko do odczytu do wszystkich obiektów nadrzędnych, które obsługują hosty hostów, klaster, hosty folderu, klastry, do centrum danych. Nie musisz propagować uprawnień do wszystkich obiektów podrzędnych.
3. Aby użyć poświadczeń do odnajdywania, wybierz centrum danych jako **zakres kolekcji**.


Konfiguracja kontroli dostępu opartej na rolach zapewnia, że odpowiednie konto użytkownika vCenter ma dostęp tylko do serwerów specyficznych dla dzierżawy.


## <a name="next-steps"></a>Następne kroki

[Konfigurowanie urządzenia](how-to-set-up-appliance-vmware.md)