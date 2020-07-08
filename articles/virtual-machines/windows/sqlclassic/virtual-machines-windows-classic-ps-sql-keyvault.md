---
title: Integrowanie Key Vault z klasyczną maszyną wirtualną platformy Azure SQL Server
description: Dowiedz się, jak zautomatyzować konfigurację szyfrowania SQL Server do użycia z Azure Key Vault. W tym temacie wyjaśniono, jak używać integracji Azure Key Vault z maszynami wirtualnymi SQL Server tworzyć w klasycznym modelu wdrażania.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: ab8d41a7-1971-4032-ab71-eb435c455dc1
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/17/2017
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 7439aa360395490f31a638ac690ed7e5cad1054b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84195834"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-classic"></a>Konfigurowanie integracji Azure Key Vault SQL Server na platformie Azure Virtual Machines (klasyczny)
> [!div class="op_single_selector"]
> * [Resource Manager](../../../azure-sql/virtual-machines/windows/azure-key-vault-integration-configure.md)
> * [Klasyczny](../classic/ps-sql-keyvault.md)
> 
> 

## <a name="overview"></a>Omówienie
Istnieje wiele funkcji szyfrowania SQL Server, takich jak [przezroczyste szyfrowanie danych (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [szyfrowanie na poziomie kolumny (cle)](https://msdn.microsoft.com/library/ms173744.aspx)i [szyfrowanie kopii zapasowych](https://msdn.microsoft.com/library/dn449489.aspx). Te formy szyfrowania wymagają zarządzania kluczami kryptograficznymi używanymi do szyfrowania i ich przechowywania. Usługa Azure Key Vault (AKV) została zaprojektowana w celu poprawy bezpieczeństwa i zarządzania tymi kluczami w bezpiecznej i wysoce dostępnej lokalizacji. [SQL Server Connector](https://www.microsoft.com/download/details.aspx?id=45344) umożliwia SQL Server Korzystanie z tych kluczy z Azure Key Vault.

> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania służące do tworzenia zasobów i pracy z nimi: [Menedżer zasobów i klasyczne](../../../azure-resource-manager/management/deployment-models.md). W tym artykule opisano korzystanie z klasycznego modelu wdrażania. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.

W przypadku uruchamiania SQL Server z maszynami lokalnymi istnieją [kroki, które można wykonać, aby uzyskać dostęp do Azure Key Vault z lokalnego komputera SQL Server](https://msdn.microsoft.com/library/dn198405.aspx). Jednak w przypadku SQL Server na maszynach wirtualnych platformy Azure można zaoszczędzić czas, korzystając z funkcji *integracji Azure Key Vault* . Aby włączyć tę funkcję za pomocą kilku poleceń cmdlet Azure PowerShell, można zautomatyzować konfigurację niezbędną do uzyskania dostępu do magazynu kluczy w maszynie wirtualnej SQL.

Po włączeniu tej funkcji program automatycznie zainstaluje SQL Server Connector, skonfiguruje dostawcę EKM w celu uzyskania dostępu Azure Key Vault i utworzy poświadczenie, aby umożliwić dostęp do magazynu. Jeśli zostały przedstawione kroki opisane wcześniej w dokumentacji lokalnej, można zobaczyć, że ta funkcja automatyzuje kroki 2 i 3. Jedyną czynnością, którą należy wykonać ręcznie, jest utworzenie magazynu kluczy i kluczy. Z tego miejsca cała konfiguracja maszyny wirtualnej SQL jest zautomatyzowana. Po ukończeniu tej funkcji można wykonać instrukcje języka T-SQL, aby rozpocząć szyfrowanie baz danych lub kopii zapasowych w zwykły sposób.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="configure-akv-integration"></a>Konfigurowanie integracji AKV
Skonfiguruj integrację Azure Key Vault przy użyciu programu PowerShell. Poniższe sekcje zawierają omówienie wymaganych parametrów, a następnie przykładowy skrypt programu PowerShell.

### <a name="install-the-sql-server-iaas-extension"></a>Zainstaluj rozszerzenie IaaS SQL Server
Najpierw [Zainstaluj rozszerzenie IaaS SQL Server](../classic/sql-server-agent-extension.md).

### <a name="understand-the-input-parameters"></a>Zrozumienie parametrów wejściowych
Poniższa tabela zawiera listę parametrów wymaganych do uruchomienia skryptu programu PowerShell w następnej sekcji.

| Parametr | Opis | Przykład |
| --- | --- | --- |
| **$akvURL** |**Adres URL magazynu kluczy** |"https: \/ /contosokeyvault.Vault.Azure.NET/" |
| **$spName** |**Nazwa główna usługi** |"fde2b411-33d5-4e11-af04eb07b669ccf2" |
| **$spSecret** |**Klucz tajny jednostki usługi** |"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM =" |
| **$credName** |**Nazwa poświadczenia**: integracja usługi Azure Key Vault powoduje utworzenie poświadczenia w programie SQL Server, co umożliwia maszynie wirtualnej dostęp do magazynu kluczy. Wybierz nazwę tego poświadczenia. |"mycred1" |
| **$vmName** |**Nazwa maszyny wirtualnej**: nazwa wcześniej utworzonej maszyny wirtualnej SQL. |"myvmname" |
| **$serviceName** |**Nazwa usługi**: nazwa usługi w chmurze skojarzona z maszyną wirtualną SQL. |"mycloudservicename" |

### <a name="enable-akv-integration-with-powershell"></a>Włączanie integracji AKV z programem PowerShell
Polecenie cmdlet **New-AzureVMSqlServerKeyVaultCredentialConfig** tworzy obiekt konfiguracji dla funkcji integracji Azure Key Vault. **Set-AzureVMSqlServerExtension** konfiguruje tę integrację z parametrem **KeyVaultCredentialSettings** . Poniższe kroki pokazują, jak używać tych poleceń.

1. W Azure PowerShell należy najpierw skonfigurować parametry wejściowe przy użyciu określonych wartości zgodnie z opisem w poprzednich sekcjach tego tematu. Poniższy skrypt jest przykładem.
   
        $akvURL = "https:\//contosokeyvault.vault.azure.net/"
        $spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
        $spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
        $credName = "mycred1"
        $vmName = "myvmname"
        $serviceName = "mycloudservicename"
2. Następnie użyj poniższego skryptu, aby skonfigurować i włączyć integrację AKV.
   
        $secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
        $akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
        Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

Rozszerzenie SQL IaaS Agent zaktualizuje maszynę wirtualną SQL z tą nową konfiguracją.

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

