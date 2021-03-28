---
ms.openlocfilehash: cc628b1f1fcae5e837f7f61db584c8747100f353
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644748"
---
## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/dotnet/).
- Zainstaluj [moduł Azure AZ PowerShell](https://docs.microsoft.com/powershell/azure/)

## <a name="create-azure-communication-resource"></a>Tworzenie zasobu komunikacyjnego platformy Azure

Aby utworzyć zasób usługi Azure Communications Services, [Zaloguj się do interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli). Można to zrobić za pośrednictwem terminalu przy użyciu ```Connect-AzAccount``` polecenia i podać swoje poświadczenia. Uruchom następujące polecenie, aby utworzyć zasób:

```PowerShell
PS C:\> New-AzCommunicationService -ResourceGroupName ContosoResourceProvider1 -Name ContosoAcsResource1 -DataLocation UnitedStates -Location Global
```

Jeśli chcesz wybrać określoną subskrypcję, możesz także określić ```--subscription``` flagę i podać identyfikator subskrypcji.
```PowerShell
PS C:\> New-AzCommunicationService -ResourceGroupName ContosoResourceProvider1 -Name ContosoAcsResource1 -DataLocation UnitedStates -Location Global -SubscriptionId SubscriptionID
```

Zasób usług komunikacyjnych można skonfigurować przy użyciu następujących opcji:

* Grupa zasobów
* Nazwa zasobu usług komunikacyjnych
* Geografia, z którą zostanie skojarzony zasób

W następnym kroku można przypisać Tagi do zasobu. Tagi mogą służyć do organizowania zasobów platformy Azure. Aby uzyskać więcej informacji na temat tagów, zobacz [dokumentację tagowania zasobów](../../../azure-resource-manager/management/tag-resources.md) .

## <a name="manage-your-communication-services-resource"></a>Zarządzanie zasobem usług komunikacyjnych

Aby dodać tagi do zasobu usług komunikacyjnych, uruchom następujące polecenia. Możesz również wskazać konkretną subskrypcję.

```PowerShell
PS C:\> Update-AzCommunicationService -Name ContosoAcsResource1 -ResourceGroupName ContosoResourceProvider1 -Tag @{ExampleKey1="ExampleValue1"}

PS C:\> Update-AzCommunicationService -Name ContosoAcsResource1 -ResourceGroupName ContosoResourceProvider1 -Tag @{ExampleKey1="ExampleValue1"} -SubscriptionId SubscriptionID
```

Aby wyświetlić listę wszystkich zasobów usług Azure Communications Services w danej subskrypcji, użyj następującego polecenia:

```PowerShell
PS C:\> Get-AzCommunicationService -SubscriptionId SubscriptionID
```

Aby wyświetlić wszystkie informacje dotyczące danego zasobu, użyj następującego polecenia:

```PowerShell
PS C:\> Get-AzCommunicationService -Name ContosoAcsResource1 -ResourceGroupName ContosoResourceProvider1
```
