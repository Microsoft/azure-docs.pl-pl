---
title: Tworzenie kontenera usługi w chmurze (klasycznego) przy użyciu programu PowerShell | Microsoft Docs
description: W tym artykule wyjaśniono, jak utworzyć kontener usługi w chmurze przy użyciu programu PowerShell. Kontener obsługuje role sieci Web i procesu roboczego.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 39fe439e37b1af4e833396ef83205729af8c7ad3
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102610420"
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-classic-container"></a>Użyj Azure PowerShell polecenia, aby utworzyć pusty kontener usługi w chmurze (klasyczny)

> [!IMPORTANT]
> [Azure Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md) to nowy model wdrażania oparty na Azure Resource Manager dla produktu Cloud Services platformy Azure.Ta zmiana spowoduje, że usługa Azure Cloud Services uruchomiona w ramach modelu wdrażania opartego na usłudze Azure Service Manager została zmieniona jako Cloud Services (klasyczny), a wszystkie nowe wdrożenia powinny używać [Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md).

W tym artykule wyjaśniono, jak szybko utworzyć kontener Cloud Services przy użyciu Azure PowerShell poleceń cmdlet. Wykonaj poniższe kroki:

1. Na stronie [pliki do pobrania Azure PowerShell](https://aka.ms/webpi-azps) Zainstaluj polecenie cmdlet Microsoft Azure PowerShell.
2. Otwórz wiersz polecenia programu PowerShell.
3. Aby się zalogować, użyj [dodatku Add-AzureAccount](/powershell/module/servicemanagement/azure.service/add-azureaccount) .

   > [!NOTE]
   > Dalsze instrukcje dotyczące instalowania Azure PowerShell polecenia cmdlet i nawiązywania połączenia z subskrypcją platformy Azure można znaleźć w [tematach Instalowanie i konfigurowanie Azure PowerShell](/powershell/azure/).
   >
   >
4. Użyj polecenia cmdlet **New-AzureService** , aby utworzyć pusty kontener usługi w chmurze platformy Azure.

   ```
   New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   ```

5. Postępuj zgodnie z tym przykładem, aby wywołać polecenie cmdlet:

   ```powershell
   New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
   ```

Aby uzyskać więcej informacji na temat tworzenia usługi w chmurze platformy Azure, uruchom polecenie:

```powershell
Get-help New-AzureService
```

### <a name="next-steps"></a>Następne kroki

* Aby zarządzać wdrożeniem usługi w chmurze, zobacz polecenia [Get-AzureService](/powershell/module/servicemanagement/azure.service/Get-AzureService), [Remove-AzureService](/powershell/module/servicemanagement/azure.service/Remove-AzureService)i [Set-AzureService](/powershell/module/servicemanagement/azure.service/set-azureservice) . Możesz również zapoznać się z artykułem [jak skonfigurować usługi w chmurze](cloud-services-how-to-configure-portal.md) pod kątem dalszych informacji.
* Aby opublikować projekt usługi w chmurze na platformie Azure, zapoznaj się z przykładowym kodem  **PublishCloudService.ps1** z [zarchiwizowanego repozytorium usług Cloud Services](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Scripts/cloud-services-continuous-delivery).