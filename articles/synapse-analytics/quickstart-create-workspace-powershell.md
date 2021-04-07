---
title: 'Szybki Start: Tworzenie obszaru roboczego usługi Synapse przy użyciu Azure PowerShell'
description: Utwórz obszar roboczy usługi Azure Synapse za pomocą Azure PowerShell, wykonując czynności opisane w tym przewodniku.
services: synapse-analytics
author: alehall
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: overview
ms.date: 10/19/2020
ms.author: alehall
ms.reviewer: jrasnick
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a19d1f26616697e15ae4c39a63c44bdaf83f78f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101675760"
---
# <a name="quickstart-create-an-azure-synapse-workspace-with-azure-powershell"></a>Szybki Start: Tworzenie obszaru roboczego usługi Azure Synapse za pomocą Azure PowerShell

Azure PowerShell to zestaw poleceń cmdlet służących do zarządzania zasobami platformy Azure bezpośrednio z programu PowerShell. Można używać go w przeglądarce za pośrednictwem usługi Azure Cloud Shell. Można go również zainstalować w systemach macOS, Linux lub Windows.

W tym przewodniku szybki start dowiesz się, jak utworzyć obszar roboczy Synapse przy użyciu Azure PowerShell.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="prerequisites"></a>Wymagania wstępne

- [Konto magazynu Azure Data Lake Storage Gen2](../storage/common/storage-account-create.md)

    > [!IMPORTANT]
    > Obszar roboczy usługi Azure Synapse musi mieć możliwość odczytu i zapisu na wybranym koncie ADLS Gen2. W przypadku dowolnego konta magazynu, które można połączyć jako konto magazynu podstawowego, należy włączyć **hierarchiczną przestrzeń nazw** podczas tworzenia konta magazynu, zgodnie z opisem w temacie [Tworzenie konta magazynu](../storage/common/storage-account-create.md?tabs=azure-powershell#create-a-storage-account).

Jeśli zdecydujesz się używać Cloud Shell, zobacz [omówienie Azure Cloud Shell](../cloud-shell/overview.md) , aby uzyskać więcej informacji.

### <a name="install-the-azure-powershell-module-locally"></a>Instalowanie modułu Azure PowerShell lokalnie

Jeśli zdecydujesz się używać programu PowerShell lokalnie, ten artykuł będzie wymagał instalacji modułu AZ PowerShell i nawiązania połączenia z kontem platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Aby uzyskać więcej informacji na temat instalowania modułu AZ PowerShell module, zobacz [Install Azure PowerShell](/powershell/azure/install-az-ps).

Aby uzyskać więcej informacji na temat uwierzytelniania przy użyciu Azure PowerShell, zobacz [Logowanie przy użyciu Azure PowerShell](/powershell/azure/authenticate-azureps).

### <a name="install-the-azure-synapse-powershell-module"></a>Instalowanie modułu Azure Synapse PowerShell

> [!IMPORTANT]
> Mimo że moduł **AZ. Synapse** PowerShell jest w wersji zapoznawczej, należy go zainstalować oddzielnie przy użyciu `Install-Module` polecenia cmdlet. Gdy ten moduł programu PowerShell stanie się ogólnie dostępny, będzie częścią przyszłych wydań modułu Az programu PowerShell i będzie domyślnie dostępny z poziomu usługi Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.Synapse
```

## <a name="create-an-azure-synapse-workspace-using-azure-powershell"></a>Tworzenie obszaru roboczego usługi Azure Synapse za pomocą Azure PowerShell

1. Zdefiniuj niezbędne zmienne środowiskowe, aby utworzyć zasoby dla obszaru roboczego usługi Azure Synapse.

   |        Nazwa zmiennej        |                                                 Opis                                                 |
   | --------------------------- | ----------------------------------------------------------------------------------------------------------- |
   | StorageAccountName          | Nazwa istniejącego konta magazynu ADLS Gen2.                                                           |
   | StorageAccountResourceGroup | Nazwa istniejącej grupy zasobów konta magazynu ADLS Gen2.                                             |
   | FileShareName               | Nazwa istniejącego systemu plików magazynu.                                                                  |
   | SynapseResourceGroup        | Wybierz nową nazwę grupy zasobów usługi Azure Synapse.                                                    |
   | Region (Region)                      | Wybierz jeden z [regionów świadczenia usługi Azure](https://azure.microsoft.com/global-infrastructure/geographies/#overview). |
   | SynapseWorkspaceName        | Wybierz unikatową nazwę dla nowego obszaru roboczego usługi Azure Synapse.                                                  |
   | Sqluser                     | Wybierz wartość nowej nazwy użytkownika.                                                                          |
   | SqlPassword                 | Wybierz bezpieczne hasło.                                                                                   |
   | ClientIP                    | Publiczny adres IP systemu, z którego korzystasz z programu PowerShell.                                             |
   |                             |                                                                                                             |

1. Utwórz grupę zasobów jako kontener dla obszaru roboczego usługi Azure Synapse:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $SynapseResourceGroup -Location $Region
   ```

1. Utwórz obszar roboczy usługi Azure Synapse:

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

1. Pobierz adres URL sieci Web i deweloperski dla obszaru roboczego usługi Azure Synapse:

   ```azurepowershell-interactive
   $WorkspaceWeb = (Get-AzSynapseWorkspace -Name $SynapseWorkspaceName -ResourceGroupName $StorageAccountResourceGroup).ConnectivityEndpoints.Web
   $WorkspaceDev = (Get-AzSynapseWorkspace -Name $SynapseWorkspaceName -ResourceGroupName $StorageAccountResourceGroup).ConnectivityEndpoints.Dev
   ```

1. Utwórz regułę zapory, aby zezwolić na dostęp do obszaru roboczego usługi Azure Synapse z maszyny:

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

1. Otwórz adres URL sieci Web obszaru roboczego usługi Azure Synapse przechowywany w zmiennej środowiskowej `WorkspaceWeb` , aby uzyskać dostęp do obszaru roboczego:

   ```azurepowershell-interactive
   Start-Process $WorkspaceWeb
   ```

   ![Sieć Web obszaru roboczego usługi Azure Synapse](media/quickstart-create-synapse-workspace-powershell/create-workspace-powershell-1.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Wykonaj poniższe kroki, aby usunąć obszar roboczy usługi Azure Synapse.

> [!WARNING]
> Usunięcie obszaru roboczego usługi Azure Synapse spowoduje usunięcie aparatów analizy i danych przechowywanych w bazie danych zawartych pul SQL i metadanych obszaru roboczego. Nie będzie już możliwe łączenie się z punktami końcowymi SQL lub Apache Spark. Wszystkie artefakty kodu zostaną usunięte (zapytania, notesy, definicje zadań i potoki). Usunięcie obszaru roboczego **nie** wpłynie na dane w Data Lake Store Gen2 połączonym z obszarem roboczym.

Jeśli obszar roboczy usługi Azure Synapse utworzony w tym artykule nie jest wymagany, można go usunąć, uruchamiając Poniższy przykład.

```azurepowershell-interactive
Remove-AzSynapseWorkspace -Name $SynapseWorkspaceNam -ResourceGroupName $SynapseResourceGroup
```

## <a name="next-steps"></a>Następne kroki

Następnie możesz [utworzyć pule SQL](quickstart-create-sql-pool-studio.md) lub [utworzyć pule Apache Spark](quickstart-create-apache-spark-pool-studio.md) , aby rozpocząć analizowanie i Eksplorowanie danych.