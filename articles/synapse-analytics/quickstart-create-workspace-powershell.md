---
title: 'Szybki start: tworzenie obszaru roboczego usługi Synapse przy użyciu Azure PowerShell'
description: Utwórz obszar Azure Synapse przy użyciu Azure PowerShell, zgodnie z instrukcjami w tym przewodniku.
services: synapse-analytics
author: alehall
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 10/19/2020
ms.author: alehall
ms.reviewer: jrasnick
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1f02833ef7497c34b72db6b858a51c6046bbf3df
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567574"
---
# <a name="quickstart-create-an-azure-synapse-workspace-with-azure-powershell"></a>Szybki start: tworzenie obszaru roboczego usługi Azure Synapse przy użyciu Azure PowerShell

Azure PowerShell to zestaw poleceń cmdlet do zarządzania zasobami platformy Azure bezpośrednio z programu PowerShell. Można używać go w przeglądarce za pośrednictwem usługi Azure Cloud Shell. Można go również zainstalować w systemie macOS, Linux lub Windows.

Z tego przewodnika Szybki start dowiesz się, jak utworzyć obszar roboczy usługi Synapse przy użyciu Azure PowerShell.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="prerequisites"></a>Wymagania wstępne

- [Azure Data Lake Storage Gen2 konta magazynu](../storage/common/storage-account-create.md)

    > [!IMPORTANT]
    > Obszar Azure Synapse musi mieć możliwość odczytu i zapisu na wybranym koncie ADLS Gen2 danych. Dla dowolnego konta magazynu, które zostanie połączyć jako konto magazynu podstawowego, musisz włączyć **hierarchiczną** przestrzeń nazw podczas tworzenia konta magazynu zgodnie z opisem w tece [Tworzenie konta magazynu.](../storage/common/storage-account-create.md?tabs=azure-powershell#create-a-storage-account)

Jeśli zdecydujesz się używać Cloud Shell, zobacz [Omówienie Azure Cloud Shell,](../cloud-shell/overview.md) aby uzyskać więcej informacji.

### <a name="install-the-azure-powershell-module-locally"></a>Instalowanie modułu Azure PowerShell lokalnie

Jeśli zdecydujesz się używać programu PowerShell lokalnie, ten artykuł wymaga zainstalowania modułu Az programu PowerShell i nawiązania połączenia z kontem platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Aby uzyskać więcej informacji na temat instalowania modułu Az programu PowerShell, zobacz [Instalowanie Azure PowerShell](/powershell/azure/install-az-ps).

Aby uzyskać więcej informacji na temat uwierzytelniania za Azure PowerShell, zobacz [Logowanie za pomocą Azure PowerShell](/powershell/azure/authenticate-azureps).

### <a name="install-the-azure-synapse-powershell-module"></a>Instalowanie modułu Azure Synapse PowerShell

> [!IMPORTANT]
> Gdy moduł **Az.Synapse** PowerShell jest w wersji zapoznawczej, należy zainstalować go oddzielnie przy użyciu `Install-Module` polecenia cmdlet . Gdy ten moduł programu PowerShell stanie się ogólnie dostępny, będzie częścią przyszłych wydań modułu Az programu PowerShell i będzie domyślnie dostępny z poziomu usługi Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.Synapse
```

## <a name="create-an-azure-synapse-workspace-using-azure-powershell"></a>Tworzenie Azure Synapse roboczego przy użyciu Azure PowerShell

1. Zdefiniuj zmienne środowiskowe niezbędne do tworzenia zasobów dla Azure Synapse roboczego.

   |        Nazwa zmiennej        |                                                 Opis                                                 |
   | --------------------------- | ----------------------------------------------------------------------------------------------------------- |
   | StorageAccountName          | Nazwa istniejącego konta ADLS Gen2 magazynu.                                                           |
   | StorageAccountResourceGroup | Nazwa istniejącej grupy zasobów ADLS Gen2 magazynu.                                             |
   | FileShareName               | Nazwa istniejącego systemu plików magazynu.                                                                  |
   | SynapseResourceGroup        | Wybierz nową nazwę grupy Azure Synapse zasobów.                                                    |
   | Region (Region)                      | Wybierz jeden z regionów [świadczenia usługi Azure.](https://azure.microsoft.com/global-infrastructure/geographies/#overview) |
   | SynapseWorkspaceName        | Wybierz unikatową nazwę dla nowego obszaru roboczego Azure Synapse roboczego.                                                  |
   | SqlUser                     | Wybierz wartość dla nowej nazwy użytkownika.                                                                          |
   | SqlPassword                 | Wybierz bezpieczne hasło.                                                                                   |
   | ClientIP                    | Publiczny adres IP systemu, z których jest uruchomiony program PowerShell.                                             |
   |                             |                                                                                                             |

1. Utwórz grupę zasobów jako kontener dla swojego Azure Synapse roboczego:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $SynapseResourceGroup -Location $Region
   ```

1. Tworzenie Azure Synapse roboczego:

   ```azurepowershell-interactive
   $Cred = New-Object -TypeName System.Management.Automation.PSCredential ($SqlUser, (ConvertTo-SecureString $SqlPassword -AsPlainText -Force))

   $WorkspaceParams = @{
     Name = $SynapseWorkspaceName
     ResourceGroupName = $SynapseResourceGroup
     DefaultDataLakeStorageAccountName = $StorageAccountName
     DefaultDataLakeStorageFilesystem = $FileShareName
     SqlAdministratorLoginCredential = $Cred
     Location = $Region
   }
   New-AzSynapseWorkspace @WorkspaceParams
   ```

1. Pobierz adres URL sieci Web i dewelopera dla Azure Synapse workspace:

   ```azurepowershell-interactive
   $WorkspaceWeb = (Get-AzSynapseWorkspace -Name $SynapseWorkspaceName -ResourceGroupName $StorageAccountResourceGroup).ConnectivityEndpoints.Web
   $WorkspaceDev = (Get-AzSynapseWorkspace -Name $SynapseWorkspaceName -ResourceGroupName $StorageAccountResourceGroup).ConnectivityEndpoints.Dev
   ```

1. Utwórz regułę zapory, aby zezwolić na dostęp do Azure Synapse z maszyny:

   ```azurepowershell-interactive
   $FirewallParams = @{
     WorkspaceName = $SynapseWorkspaceName
     Name = 'Allow Client IP'
     ResourceGroupName = $StorageAccountResourceGroup
     StartIpAddress = $ClientIP
     EndIpAddress = $ClientIP
   }
   New-AzSynapseFirewallRule @FirewallParams
   ```

1. Otwórz adres AZURE SYNAPSE URL obszaru roboczego przechowywany w zmiennej środowiskowej, `WorkspaceWeb` aby uzyskać dostęp do obszaru roboczego:

   ```azurepowershell-interactive
   Start-Process $WorkspaceWeb
   ```

   ![Azure Synapse obszaru roboczego w sieci Web](media/quickstart-create-synapse-workspace-powershell/create-workspace-powershell-1.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Wykonaj poniższe kroki, aby usunąć obszar Azure Synapse roboczego.

> [!WARNING]
> Usunięcie obszaru Azure Synapse spowoduje usunięcie aparatów analitycznych i danych przechowywanych w bazie danych zawartych pul SQL i metadanych obszaru roboczego. Nie będzie już można nawiązać połączenia z bazą danych SQL lub Apache Spark końcowych. Wszystkie artefakty kodu zostaną usunięte (zapytania, notesy, definicje zadań i potoki). Usunięcie obszaru **roboczego nie będzie miało** wpływu na dane w Data Lake Store Gen2 połączonej z obszarem roboczym.

Jeśli obszar Azure Synapse utworzony w tym artykule nie jest potrzebny, możesz go usunąć, uruchamiając poniższy przykład.

```azurepowershell-interactive
Remove-AzSynapseWorkspace -Name $SynapseWorkspaceNam -ResourceGroupName $SynapseResourceGroup
```

## <a name="next-steps"></a>Następne kroki

Następnie możesz utworzyć [pule SQL lub](quickstart-create-sql-pool-studio.md) utworzyć [Apache Spark,](quickstart-create-apache-spark-pool-studio.md) aby rozpocząć analizowanie i eksplorowanie danych.