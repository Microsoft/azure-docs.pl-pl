---
title: Tworzenie kontenera usługi w chmurze za pomocą programu PowerShell | Microsoft Docs
description: W tym artykule wyjaśniono, jak utworzyć kontener usługi w chmurze przy użyciu programu PowerShell. Kontener obsługuje role sieci Web i procesu roboczego.
services: cloud-services
documentationcenter: .net
author: cawaMS
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: na
ms.date: 11/18/2016
ms.author: cawa
ms.openlocfilehash: ef9d3c7c479df9c71a855f0a243b5b9d0da947d7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "68359505"
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-container"></a>Użyj Azure PowerShell polecenia, aby utworzyć pusty kontener usługi w chmurze

W tym artykule wyjaśniono, jak szybko utworzyć kontener Cloud Services przy użyciu Azure PowerShell poleceń cmdlet. Wykonaj poniższe kroki:

1. Na stronie [pliki do pobrania Azure PowerShell](https://aka.ms/webpi-azps) Zainstaluj polecenie cmdlet Microsoft Azure PowerShell.
2. Otwórz wiersz polecenia programu PowerShell.
3. Aby się zalogować, użyj [dodatku Add-AzureAccount](/powershell/module/servicemanagement/azure/add-azureaccount?view=azuresmps-4.0.0) .

   > [!NOTE]
   > Dalsze instrukcje dotyczące instalowania Azure PowerShell polecenia cmdlet i nawiązywania połączenia z subskrypcją platformy Azure można znaleźć w [tematach Instalowanie i konfigurowanie Azure PowerShell](/powershell/azure/overview).
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

* Aby zarządzać wdrożeniem usługi w chmurze, zobacz polecenia [Get-AzureService](/powershell/module/servicemanagement/azure/Get-AzureService?view=azuresmps-4.0.0), [Remove-AzureService](/powershell/module/servicemanagement/azure/Remove-AzureService?view=azuresmps-4.0.0)i [Set-AzureService](/powershell/module/servicemanagement/azure/set-azureservice?view=azuresmps-4.0.0) . Możesz również zapoznać się z artykułem [jak skonfigurować usługi w chmurze](cloud-services-how-to-configure-portal.md) pod kątem dalszych informacji.
* Aby opublikować projekt usługi w chmurze na platformie Azure, zapoznaj się z przykładem kodu **PublishCloudService. ps1** z [zarchiwizowanego repozytorium usług Cloud Services](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Scripts/cloud-services-continuous-delivery).