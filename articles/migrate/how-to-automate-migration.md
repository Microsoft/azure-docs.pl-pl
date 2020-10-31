---
title: Automatyzacja migracji VMware bez agentów w Azure Migrate
description: Opisuje sposób używania skryptów do migrowania dużej liczby maszyn wirtualnych VMware w Azure Migrate
author: rahulgup
ms.service: azure-migrate
ms.topic: article
ms.date: 10/30/2020
ms.author: rahugup
ms.openlocfilehash: 5541d5c3b9beea86f5c76747dd13ffeff1b12d97
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93133617"
---
# <a name="scale-migration-of-vmware-vms"></a>Skalowanie migracji maszyn wirtualnych VMware 

Ten artykuł pomaga zrozumieć, jak za pomocą skryptów migrować dużą liczbę maszyn wirtualnych VMware przy użyciu metody bez agenta. Aby skalować migracje, należy użyć [modułu Azure Migrate PowerShell](https://aka.ms/azuremigratepowershellvmware). 

Azure Migrate skrypty automatyzacji migracji VMware są dostępne do pobrania w repozytorium [Azure PowerShell Samples] https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-vmware-agentles) w witrynie GitHub. Skrypty te mogą służyć do migrowania maszyn wirtualnych VMware na platformę Azure przy użyciu metody migracji bez wykorzystania agentów. Polecenia Azure Migrate PowerShell używane w tych skryptach zostały udokumentowane w [tym miejscu](https://aka.ms/azuremigratepowershellvmware).

## <a name="current-limitations"></a>Bieżące ograniczenia
- Te skrypty obsługują migrację maszyn wirtualnych VMware ze wszystkimi dyskami. Możesz zaktualizować skrypty, jeśli chcesz selektywnie replikować dyski dołączone do maszyny wirtualnej VMware. 
- Skrypty obsługują korzystanie z rekomendacji dotyczących oceny. Jeśli nie są używane zalecenia dotyczące oceny, wszystkie dyski dołączone do maszyny wirtualnej VMware są migrowane do tego samego typu dysku zarządzanego (standardowa lub Premium). Możesz zaktualizować skrypty, jeśli chcesz używać wielu typów dysków zarządzanych z tą samą maszyną wirtualną

## <a name="prerequisites"></a>Wymagania wstępne

- [Ukończ samouczek odnajdywania](tutorial-discover-vmware.md) , aby przygotować platformę Azure i oprogramowanie VMware do migracji.
- Zalecamy wykonanie drugiego samouczka, aby [ocenić maszyny wirtualne VMware](tutorial-assess-vmware.md) przed przeprowadzeniem migracji na platformę Azure.
- Masz `Az` moduł Azure PowerShell. Jeśli musisz zainstalować lub uaktualnić Azure PowerShell, postępuj zgodnie [z tym przewodnikiem, aby zainstalować i skonfigurować Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="install-azure-migrate-powershell-module"></a>Zainstaluj moduł Azure Migrate PowerShell

Moduł Azure Migrate PowerShell jest dostępny w wersji zapoznawczej. Należy zainstalować moduł programu PowerShell przy użyciu następującego polecenia. 

```azurepowershell
Install-Module -Name Az.Migrate 
```

## <a name="csv-input-file"></a>Plik wejściowy CSV
Po zakończeniu wszystkich wymagań wstępnych należy utworzyć plik CSV zawierający dane dla każdej źródłowej maszyny wirtualnej, która ma zostać poddana migracji. Wszystkie skrypty zostały zaprojektowane tak, aby działały w tym samym pliku CSV. Przykładowy szablon CSV jest dostępny w folderze skryptów dla odwołania. Plik CSV jest konfigurowalny, dzięki czemu można użyć zaleceń dotyczących oceny, a nawet określić, czy nie należy wyzwalać niektórych operacji dla określonej maszyny wirtualnej. 

> [!NOTE]
> Ten sam plik CSV może służyć do migrowania maszyn wirtualnych w wielu projektach Azure Migrate.

### <a name="csv-file-schema"></a>Schemat pliku CSV

**Nagłówek kolumny** | **Opis**
--- | ---
AZMIGRATEPROJECT_SUBSCRIPTION_ID | Podaj identyfikator subskrypcji projektu Azure Migrate.
AZMIGRATEPROJECT_RESOURCE_GROUP_NAME | Podaj nazwę grupy zasobów Azure Migrate.
AZMIGRATEPROJECT_NAME | Podaj nazwę projektu Azure Migrate, w którym chcesz migrować serwery. 
SOURCE_MACHINE_NAME | Podaj przyjazną nazwę (nazwa wyświetlana) dla odnalezionej maszyny wirtualnej w projekcie Azure Migrate.
AZMIGRATEASSESSMENT_NAME | Podaj nazwę oceny, której należy użyć do migracji.
AZMIGRATEGROUP_NAME | Podaj nazwę grupy, która została użyta do oceny Azure Migrate.
TARGET_RESOURCE_GROUP_NAME | Podaj nazwę grupy zasobów platformy Azure, do której należy migrować maszynę wirtualną.
TARGET_VNET_NAME| Podaj nazwę Virtual Network platformy Azure, której powinna używać migrowana maszyna wirtualna.
TARGET_SUBNET_NAME | Podaj nazwę podsieci w docelowej sieci wirtualnej, której powinna używać migrowana maszyna wirtualna. Jeśli pole pozostanie puste, zostanie użyta domyślna podsieć.
TARGET_MACHINE_NAME | Podaj nazwę, która powinna być używana przez migrowana maszynę wirtualną na platformie Azure. Jeśli pole pozostanie puste, zostanie użyta nazwa maszyny źródłowej.  
TARGET_MACHINE_SIZE | Podaj jednostkę SKU, która powinna być używana przez maszynę wirtualną na platformie Azure. Aby przeprowadzić migrację maszyny wirtualnej do D2_v2 maszyny wirtualnej na platformie Azure, określ wartość w tym polu jako "Standard_D2_v2". Jeśli używasz oceny, ta wartość zostanie utworzona na podstawie zalecenia dotyczącego oceny.
LICENSE_TYPE | Określ, czy chcesz używać Korzyść użycia hybrydowego platformy Azure na potrzeby maszyn wirtualnych z systemem Windows Server. Użyj wartości "WindowsServer", aby skorzystać z Korzyść użycia hybrydowego platformy Azure. W przeciwnym razie pozostaw puste lub użyj "nolicensetype".
OS_DISK_ID | Podaj identyfikator dysku systemu operacyjnego dla maszyny wirtualnej, która ma zostać zmigrowana. Identyfikator dysku, który ma być używany, to właściwość unikatowego identyfikatora (UUID) dla dysku pobranego za pomocą polecenia cmdlet Get-AzMigrateServer. Skrypt użyje pierwszego dysku maszyny wirtualnej jako dysku systemu operacyjnego w przypadku braku podanej wartości.
TARGET_DISKTYPE | Podaj typ dysku, który ma być używany dla wszystkich dysków maszyny wirtualnej na platformie Azure. Użyj "Premium_LRS" dla dysków zarządzanych w warstwie Premium, "StandardSSD_LRS" dla standardowych dysków SSD i "Standard_LRS", aby używać standardowych dysków DYSKowych. Jeśli zdecydujesz się użyć oceny, skrypt będzie miał priorytet przy użyciu zalecanych typów dysków dla każdego dysku maszyny wirtualnej. Jeśli nie używasz oceny lub nie określisz żadnej wartości, skrypt domyślnie użyje standardowych dysków DYSKowych.    
AVAILABILITYZONE_NUMBER | Określ numer strefy dostępności, który ma być używany dla migrowanej maszyny wirtualnej. Możesz pozostawić to pole puste, jeśli nie chcesz używać stref dostępności. 
AVAILABILITYSET_NAME | Określ nazwę zestawu dostępności, który ma być używany dla migrowanej maszyny wirtualnej. Możesz pozostawić to pole puste, jeśli nie chcesz używać zestawu dostępności.
TURNOFF_SOURCESERVER | Określ wartość "Y", jeśli chcesz wyłączyć źródłową maszynę wirtualną w czasie migracji. W przeciwnym razie użyj "N". Jeśli pole pozostanie puste, skrypt przyjmuje wartość jako "N".
TESTMIGRATE_VNET_NAME | Określ nazwę sieci wirtualnej, która ma być używana na potrzeby migracji testowej.
UPDATED_TARGET_RESOURCE_GROUP_NAME | Jeśli chcesz zaktualizować grupę zasobów, która ma być używana przez zmigrowanyą maszynę wirtualną na platformie Azure, określ nazwę grupy zasobów platformy Azure, w przeciwnym razie pozostaw pustą wartość. 
UPDATED_TARGET_VNET_NAME | Jeśli chcesz zaktualizować Virtual Network, które mają być używane przez zmigrowanyą maszynę wirtualną na platformie Azure, określ nazwę Virtual Network platformy Azure, w przeciwnym razie pozostaw pustą wartość.
UPDATED_TARGET_MACHINE_NAME | Jeśli chcesz zaktualizować nazwę, która ma być używana przez migrowana maszynę wirtualną na platformie Azure, a następnie określ nową nazwę, która ma być używana, w przeciwnym razie pozostaw pustą wartość.
UPDATED_TARGET_MACHINE_SIZE | Jeśli chcesz zaktualizować jednostkę SKU, która ma być używana przez migrowana maszynę wirtualną na platformie Azure, określ nową jednostkę SKU, która ma być używana, w przeciwnym razie pozostaw pustą wartość.
UPDATED_AVAILABILITYZONE_NUMBER | Jeśli chcesz zaktualizować strefę dostępności, która ma być używana przez zmigrowanyą maszynę wirtualną na platformie Azure, określ nową strefę dostępności, która ma być używana, w przeciwnym razie pozostaw pustą wartość.
UPDATED_AVAILABILITYSET_NAME | Jeśli chcesz zaktualizować zestaw dostępności, który ma być używany przez migrowana maszynę wirtualną na platformie Azure, określ nowy zestaw dostępności, który ma być używany, w przeciwnym razie pozostaw puste.
UPDATE_NIC1_ID | Określ identyfikator karty sieciowej, która ma zostać zaktualizowana. Jeśli pole pozostanie puste, skrypt przyjmuje wartość jako pierwszą kartę sieciową wykrytej maszyny wirtualnej. Jeśli nie chcesz aktualizować karty sieciowej maszyny wirtualnej, pozostaw wszystkie pola zawierające pustą nazwę karty sieciowej. 
UPDATED_TARGET_NIC1_SELECTIONTYPE | Określ wartość, która ma być używana dla tej karty sieciowej. Użyj "Primary", "pomocniczy" lub "DoNotCreate", aby określić, czy ta karta sieciowa powinna być podstawowa, pomocnicza, czy nie powinna być utworzona na migrowanej maszynie wirtualnej. Jako podstawowej karty sieciowej maszyny wirtualnej można określić tylko jedną kartę sieciową. Jeśli nie chcesz aktualizować, pozostaw to pole puste.
UPDATED_TARGET_NIC1_SUBNET_NAME | Określ nazwę podsieci, która ma być używana dla karty sieciowej na migrowanej maszynie wirtualnej. Jeśli nie chcesz aktualizować, pozostaw to pole puste.
UPDATED_TARGET_NIC1_IP | Określ adres IPv4, który ma być używany przez kartę sieciową na migrowanej maszynie wirtualnej, jeśli chcesz użyć statycznego adresu IP. Użyj "automatycznie", jeśli chcesz automatycznie przypisać adres IP. Jeśli nie chcesz aktualizować, pozostaw to pole puste.
UPDATE_NIC2_ID | Określ identyfikator karty sieciowej, która ma zostać zaktualizowana. Jeśli pole pozostanie puste, skrypt przyjmuje wartość jako drugą kartę sieciową wykrytej maszyny wirtualnej. Jeśli nie chcesz aktualizować karty sieciowej maszyny wirtualnej, pozostaw wszystkie pola zawierające pustą nazwę karty sieciowej.
UPDATED_TARGET_NIC2_SELECTIONTYPE | Określ wartość, która ma być używana dla tej karty sieciowej. Użyj "Primary", "pomocniczy" lub "DoNotCreate", aby określić, czy ta karta sieciowa powinna być podstawowa, pomocnicza, czy nie powinna być utworzona na migrowanej maszynie wirtualnej. Jako podstawowej karty sieciowej maszyny wirtualnej można określić tylko jedną kartę sieciową. Jeśli nie chcesz aktualizować, pozostaw to pole puste.
UPDATED_TARGET_NIC2_SUBNET_NAME | Określ nazwę podsieci, która ma być używana dla karty sieciowej na migrowanej maszynie wirtualnej. Jeśli nie chcesz aktualizować, pozostaw to pole puste.
UPDATED_TARGET_NIC2_IP | Określ adres IPv4, który ma być używany przez kartę sieciową na migrowanej maszynie wirtualnej, jeśli chcesz użyć statycznego adresu IP. Użyj "automatycznie", jeśli chcesz automatycznie przypisać adres IP. Jeśli nie chcesz aktualizować, pozostaw to pole puste.
OK_TO_UPDATE | Użyj "Y", aby wskazać, czy właściwości maszyny wirtualnej mają być aktualizowane podczas uruchamiania skryptu AzMigrate_UpdateMachineProperties. Użyj "N" lub pozostaw puste w inny sposób.
OK_TO_MIGRATE | Użyj "Y", aby wskazać, czy maszyna wirtualna powinna być migrowana po uruchomieniu skryptu AzMigrate_StartMigration. Jeśli nie chcesz migrować maszyny wirtualnej, użyj "N" lub pozostaw to pole puste. 
OK_TO_USE_ASSESSMENT | Użyj "Y", aby wskazać, czy maszyna wirtualna ma rozpocząć replikację przy użyciu zaleceń dotyczących oceny podczas uruchamiania skryptu AzMigrate_StartReplication. Spowoduje to zastąpienie wartości TARGET_MACHINE_SIZE i TARGET_DISKTYPE w pliku CSV. Jeśli nie chcesz używać zaleceń dotyczących oceny, użyj "N" lub pozostaw to pole puste.
OK_TO_TESTMIGRATE | Użyj "Y", aby wskazać, czy maszyna wirtualna ma zostać przetestowana po uruchomieniu skryptu AzMigrate_StartTestMigration. Użyj "N" lub pozostaw to pole puste, jeśli nie chcesz testować migracji maszyny wirtualnej. 
OK_TO_RETRIEVE_REPLICATIONSTATUS | Użyj "Y", aby wskazać, czy stan replikacji maszyny wirtualnej ma zostać zaktualizowany po uruchomieniu skryptu AzMigrate_ReplicationStatus. Jeśli nie chcesz aktualizować stanu replikacji, użyj wartości "N" lub pozostaw to pole puste.
OK_TO_CLEANUP | Użyj "Y", aby wskazać, czy replikacja dla maszyny wirtualnej powinna zostać oczyszczona po uruchomieniu skryptu AzMigrate_StopReplication. Użyj "N" lub pozostaw puste w inny sposób.
OK_TO_TESTMIGRATE_CLEANUP | Użyj "Y", aby wskazać, czy migracja testowa dla maszyny wirtualnej powinna zostać oczyszczona po uruchomieniu skryptu AzMigrate_CleanUpTestMigration. Użyj "N" lub pozostaw puste w inny sposób.


## <a name="script-execution"></a>Wykonywanie skryptu

Gdy wolumin CSV jest gotowy, możesz wykonać następujące kroki, aby przeprowadzić migrację lokalnych maszyn wirtualnych programu VMware.

**Czynności #** | **Nazwa skryptu** | **Opis**
--- | --- | ---
1 | AzMigrate_StartReplication.ps1 | Włącz replikację dla wszystkich maszyn wirtualnych wymienionych w woluminie CSV, skrypt tworzy dane wyjściowe woluminu CSV i plik dziennika na potrzeby rozwiązywania problemów.
2 | AzMigrate_ReplicationStatus.ps1 | Sprawdź stan replikacji, skrypt tworzy dane wyjściowe woluminu CSV ze stanem każdej maszyny wirtualnej i plikiem dziennika w celu rozwiązywania problemów.
3 | AzMigrate_UpdateMachineProperties.ps1 | Gdy maszyny wirtualne zakończą replikację początkową, użyj tego skryptu, aby zaktualizować właściwości docelowe maszyny wirtualnej (właściwości obliczeniowe i sieciowe). Skrypt tworzy dane wyjściowe woluminu CSV z informacjami o zadaniu dla każdej maszyny wirtualnej.
4 | AzMigrate_StartTestMigration.ps1 |  Uruchom test pracy w trybie failover dla wszystkich maszyn wirtualnych znajdujących się w woluminie CSV skonfigurowanym do migracji testowej. Skrypt tworzy dane wyjściowe woluminu CSV z informacjami o zadaniu dla każdej maszyny wirtualnej.
5 | AzMigrate_CleanUpTestMigration.ps1 | Po ręcznym zweryfikowaniu maszyn wirtualnych, które zostały przełączone w tryb failover, należy użyć tego skryptu do oczyszczenia maszyn wirtualnych z testem pracy awaryjnej dla wszystkich maszyn wirtualnych wymienionych w woluminie CSV, które są skonfigurowane na potrzeby oczyszczania migracji testowej. Skrypt tworzy dane wyjściowe woluminu CSV z informacjami o zadaniu dla każdej maszyny wirtualnej.
6 | AzMigrate_StartMigration.ps1 | Rozpocznij migrację dla wszystkich maszyn wirtualnych wymienionych w woluminie CSV skonfigurowanym do migracji. Skrypt tworzy dane wyjściowe woluminu CSV z informacjami o zadaniu dla każdej maszyny wirtualnej.
7 | AzMigrate_StopReplication.ps1 | Powoduje zatrzymanie replikacji maszyny wirtualnej po jej pomyślnym migracji lub zamiar anulowania replikacji z innych przyczyn. Skrypt tworzy dane wyjściowe woluminu CSV z informacjami o zadaniu dla każdej maszyny wirtualnej.


Następujące skrypty są wywoływane przez inne skrypty dla wszystkich operacji Azure Migrate, takich jak włączanie replikacji, Rozpoczynanie migracji testów, aktualizowanie właściwości maszyny wirtualnej i tak dalej. Upewnij się, że wszystkie skrypty są obecne w tym samym folderze/ścieżce. 

**Czynności #** | **Nazwa skryptu** | **Opis**
--- | --- | ---
1 | AzMigrate_Shared.ps1 | Typowy skrypt zawierający funkcje do pobierania właściwości oceny (za pośrednictwem interfejsu API), odnalezionych maszyn wirtualnych i replikowania maszyn wirtualnych. 
2 | AzMigrate_CSV_Processor.ps1 | Typowy skrypt zawierający funkcje służące do wykonywania operacji na plikach CSV, w tym ładowanie, odczytywanie i drukowanie dzienników. 
3 | AzMigrate_Logger.ps1 | Wywołano typowy skrypt do generowania pliku dziennika dla operacji automatyzacji Azure Migrate. Plik dziennika będzie miał format log.Scriptname.Datetime.txt.

Oprócz powyższych, folder zawiera również AzMigrate_Template.ps1, które zawierają strukturę szkieletową do tworzenia skryptów niestandardowych dla różnych operacji Azure Migrate. 

### <a name="script-execution-syntax"></a>Składnia wykonywania skryptu

Po pobraniu skryptów skrypty można wykonać w następujący sposób.

Jeśli chcesz uruchomić skrypt w celu uruchomienia replikacji dla maszyn wirtualnych przy użyciu pliku Input.csv, użyj następującej składni. 

```azurepowershell
".\AzMigrate_StartReplication.ps1" .\Input.csv 
```

Aby dowiedzieć się więcej o używaniu Azure PowerShell do migrowania maszyn wirtualnych VMware z Azure Migrate, postępuj zgodnie z [samouczkiem](https://aka.ms/azuremigratepowershellvmware).