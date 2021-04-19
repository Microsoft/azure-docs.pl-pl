---
title: Usuwanie projektu usługi Azure Migrate
description: W tym artykule dowiesz się, jak można usunąć Azure Migrate projektu przy użyciu Azure Portal.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: how-to
ms.date: 10/22/2019
ms.openlocfilehash: 8c94bb23f5d514fef5cdacb855657efdf5219631
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714744"
---
# <a name="delete-an-azure-migrate-project"></a>Usuwanie projektu usługi Azure Migrate

W tym artykule opisano sposób usuwania [Azure Migrate](./migrate-services-overview.md) projektu.


## <a name="before-you-start"></a>Przed rozpoczęciem

Przed usunięciem projektu:

- Po usunięciu projektu projekt i odnalezione metadane maszyny są usuwane.
- Jeśli obszar roboczy usługi Log Analytics został dołączony do narzędzia Do oceny serwera w celu analizy zależności, zdecyduj, czy chcesz usunąć obszar roboczy. 
    - Obszar roboczy nie jest automatycznie usuwany. Usuń go ręcznie.
    - Przed usunięciem sprawdź, do czego służy obszar roboczy. Ten sam obszar roboczy usługi Log Analytics może być używany w wielu scenariuszach.
    - Przed usunięciem projektu możesz znaleźć link do obszaru roboczego w obszarze **Azure Migrate**— Servers Azure Migrate — Server Assessment w obszarze Obszar  >   **roboczy OMS.**
    - Aby usunąć obszar roboczy po usunięciu projektu, znajdź obszar roboczy w odpowiedniej grupie zasobów i postępuj zgodnie z [tymi instrukcjami.](../azure-monitor/logs/delete-workspace.md)


## <a name="delete-a-project"></a>Usuwanie projektu


1. W Azure Portal otwórz grupę zasobów, w której został utworzony projekt.
2. Na stronie grupy zasobów wybierz pozycję **Pokaż ukryte typy**.
3. Wybierz projekt i skojarzone zasoby, które chcesz usunąć.
    - Typ zasobu dla Azure Migrate to **Microsoft.Migrate/migrateprojects.**
    - W następnej sekcji przejrzyj zasoby utworzone do odnajdywania, oceny i migracji w Azure Migrate projektu.
    - Jeśli grupa zasobów zawiera tylko Azure Migrate projektu, możesz usunąć całą grupę zasobów.
    - Jeśli chcesz usunąć projekt z poprzedniej wersji programu Azure Migrate, kroki są takie same. Typ zasobu dla tych projektów to **Projekt migracji**.


## <a name="created-resources"></a>Utworzone zasoby

Te tabele zawierają podsumowanie zasobów utworzonych na podstawie odnajdywania, oceny i migracji w Azure Migrate projektu.

> [!NOTE]
> Magazyn kluczy należy usuwać z rozwagą, ponieważ może zawierać klucze zabezpieczeń.

### <a name="projects-with-public-endpoint-connectivity"></a>Projekty z łącznością z publicznym punktem końcowym

#### <a name="vmwarephysical-server"></a>VMware/serwer fizyczny

**Zasób** | **Typ**
--- | ---
"Appliancename"kv | Magazyn kluczy
"Appliancename"site | Microsoft.OffAzure/VMwareSites
"ProjectName" | Microsoft.Migrate/migrateprojects
Projekt "ProjectName" | Microsoft.Migrate/assessmentProjects
"ProjectName"rsvault | Magazyn usługi Recovery Services
"ProjectName"-MigrateVault-* | Magazyn usługi Recovery Services
migrateappligwsa* | Konto magazynu
migrateapplilsa* | Konto magazynu
migrateapplicsa* | Konto magazynu
migrateapplikv* | Magazyn kluczy
migrateapplisbns* | Przestrzeń nazw usługi Service Bus

#### <a name="hyper-v-vm"></a>Maszyna wirtualna funkcji Hyper-V

**Zasób** | **Typ**
--- | ---
"ProjectName" | Microsoft.Migrate/migrateprojects
Projekt "ProjectName" | Microsoft.Migrate/assessmentProjects
HyperV*kv | Magazyn kluczy
HyperV*Site | Microsoft.OffAzure/HyperVSites
"ProjectName"-MigrateVault-* | Magazyn usługi Recovery Services

<br/>
W poniższych tabelach przedstawiono podsumowanie zasobów utworzonych przez Azure Migrate odnajdywania, oceniania i migrowania serwerów za pośrednictwem sieci prywatnej przy użyciu linku [prywatnego platformy Azure.](./how-to-use-azure-migrate-with-private-endpoints.md)

### <a name="projects-with-private-endpoint-connectivity"></a>Projekty z łącznością z prywatnym punktem końcowym

#### <a name="vmware-vms---agentless-migrations"></a>Maszyny wirtualne VMware — migracje bez agentów

**Typ** | **Zasób** | **Prywatny punkt końcowy <br/>** |
--- | --- | ---
Microsoft.Migrate/migrateprojects | "ProjectName" | "ProjectName" \* pe 
Lokacja odnajdywania (lokacja główna) | "ProjectName"*masterite | Wzorce "ProjectName"ite \* \* pe 
Microsoft.Migrate/assessmentProjects | "ApplianceName"*project | Projekt "ApplianceName" \* \* pe 
Magazyn kluczy | "ProjectName"*kv | "ProjectName" \* kv \* pe
Microsoft.OffAzure/VMwareSites | "ApplianceName"*site | NA
Magazyn usługi Recovery Services | "ApplianceName"*vault | NA
Konto magazynu | "ApplianceName"*usa | "ApplianceName" \* usa \* pe
Magazyn usługi Recovery Services | "ProjectName"-MigrateVault-* | NA
Konto magazynu | migrateappligwsa* | NA
Konto magazynu | migrateapplilsa* | NA
Magazyn kluczy | migrateapplikv* | NA
Przestrzeń nazw usługi Service Bus | migrateapplisbns* | NA

#### <a name="hyper-v-vms"></a>Maszyny wirtualne funkcji Hyper-V 

**Typ** | **Zasób** | **Prywatny punkt końcowy <br/>** |
--- | --- | ---
Microsoft.Migrate/migrateprojects | "ProjectName" | "ProjectName" \* pe 
Lokacja odnajdywania (lokacja główna) | "ProjectName"*masterite | Masterite \* pe "ProjectName" \* 
Microsoft.Migrate/assessmentProjects | "ApplianceName"*project | Projekt "ApplianceName" \* \* pe 
Magazyn kluczy | "ProjectName"*kv | "ProjectName" \* kv \* pe
Microsoft.OffAzure/HyperVSites | "ApplianceName"*site | NA
Magazyn usługi Recovery Services | "ProjectName"-MigrateVault-* | "ProjectName"-MigrateVault-*pe

#### <a name="physical-servers--aws-vms--gcp-vms"></a>Serwery fizyczne/ maszyny wirtualne AWS/maszyny wirtualne GCP 

**Typ** | **Zasób** | **Prywatny punkt końcowy <br/>** |
--- | --- | ---
Microsoft.Migrate/migrateprojects | "ProjectName" | "ProjectName" \* pe 
Lokacja odnajdywania (lokacja główna) | "ProjectName"*masterite | Wzorce "ProjectName"ite \* \* pe 
Microsoft.Migrate/assessmentProjects | "ApplianceName"*project | Projekt "ApplianceName" \* \* pe 
Magazyn kluczy | "ProjectName"*kv | "ProjectName" \* kv \* pe
Microsoft.OffAzure/serversites | "ApplianceName"*site | NA
Magazyn usługi Recovery Services | "ProjectName"-MigrateVault-* | "ProjectName"-MigrateVault-*pe


## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak dodać [dodatkowe narzędzia do](how-to-assess.md) oceny i [migracji.](how-to-migrate.md) 
