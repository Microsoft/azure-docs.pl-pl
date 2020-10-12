---
title: Zarządzanie zasobami Azure Service Bus za pomocą programu PowerShell | Microsoft Docs
description: W tym artykule wyjaśniono, jak za pomocą modułu Azure PowerShell utworzyć jednostki Service Bus (przestrzenie nazw, kolejki, tematy, subskrypcje) i zarządzać nimi.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: b6439deb2b86c2ea5b50fe3bdbad89a0875b2acc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88065747"
---
# <a name="use-powershell-to-manage-service-bus-resources"></a>Zarządzanie zasobami Service Bus przy użyciu programu PowerShell

Microsoft Azure PowerShell to środowisko do obsługi skryptów, które służy do kontrolowania i automatyzowania wdrażania usług platformy Azure oraz zarządzania nimi. W tym artykule opisano, jak używać [modułu programu PowerShell programu Service Bus Menedżer zasobów](/powershell/module/az.servicebus) do aprowizacji i zarządzania jednostkami Service Bus (obszarami nazw, kolejkami, tematami i subskrypcjami) przy użyciu lokalnej konsoli programu Azure PowerShell lub skryptu.

Można również zarządzać jednostkami Service Bus przy użyciu szablonów Azure Resource Manager. Aby uzyskać więcej informacji, zobacz artykuł [tworzenie Service Bus zasobów przy użyciu szablonów Azure Resource Manager](service-bus-resource-manager-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy spełnić następujące wymagania wstępne:

* Subskrypcja platformy Azure. Aby uzyskać więcej informacji na temat uzyskiwania subskrypcji, zobacz [Opcje zakupu][purchase options], [oferty członków][member offers]lub [bezpłatne konto][free account].
* Komputer z Azure PowerShell. Aby uzyskać instrukcje, zobacz [wprowadzenie do Azure PowerShell poleceń cmdlet](/powershell/azure/get-started-azureps).
* Ogólna znajomość skryptów programu PowerShell, pakietów NuGet i .NET Framework.

## <a name="get-started"></a>Rozpoczęcie pracy

Pierwszym krokiem jest użycie programu PowerShell w celu zalogowania się do konta platformy Azure i subskrypcji platformy Azure. Postępuj zgodnie z instrukcjami w temacie [wprowadzenie do Azure PowerShell poleceń cmdlet](/powershell/azure/get-started-azureps) , aby zalogować się do konta platformy Azure i pobrać i uzyskać dostęp do zasobów w ramach subskrypcji platformy Azure.

## <a name="provision-a-service-bus-namespace"></a>Inicjowanie obsługi administracyjnej Service Bus przestrzeni nazw

Podczas pracy z Service Bus przestrzeniami nazw można użyć poleceń cmdlet [Get-AzServiceBusNamespace](/powershell/module/az.servicebus/get-azservicebusnamespace), [New-AzServiceBusNamespace](/powershell/module/az.servicebus/new-azservicebusnamespace), [Remove-AzServiceBusNamespace](/powershell/module/az.servicebus/remove-azservicebusnamespace)i [Set-AzServiceBusNamespace](/powershell/module/az.servicebus/set-azservicebusnamespace) .

Ten przykład tworzy kilka zmiennych lokalnych w skrypcie; `$Namespace` i `$Location` .

* `$Namespace` jest nazwą przestrzeni nazw Service Bus, z którą chcemy.
* `$Location` Identyfikuje centrum danych, w którym ma zostać zainicjowana przestrzeń nazw.
* `$CurrentNamespace` przechowuje przestrzeń nazw referencyjnych pobieranych (lub tworzonych).

W rzeczywistym skrypcie `$Namespace` i `$Location` mogą być przesyłane jako parametry.

Ta część skryptu wykonuje następujące czynności:

1. Próbuje pobrać przestrzeń nazw Service Bus o podanej nazwie.
2. W przypadku znalezienia przestrzeni nazw raport zawiera informacje o tym, co zostało znalezione.
3. Jeśli przestrzeń nazw nie zostanie znaleziona, tworzy przestrzeń nazw, a następnie pobiera nowo utworzoną przestrzeń nazw.
   
    ``` powershell
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
   
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Host "The namespace $Namespace already exists in the $Location region:"
        # Report what was found
        Get-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
    }
    else
    {
        Write-Host "The $Namespace namespace does not exist."
        Write-Host "Creating the $Namespace namespace in the $Location region..."
        New-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace -Location $Location
        $CurrentNamespace = Get-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
        Write-Host "The $Namespace namespace in Resource Group $ResGrpName in the $Location region has been successfully created."
                
    }
    ```

### <a name="create-a-namespace-authorization-rule"></a>Tworzenie reguły autoryzacji przestrzeni nazw

Poniższy przykład pokazuje, jak zarządzać regułami autoryzacji przestrzeni nazw za pomocą poleceń cmdlet [New-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/new-azservicebusauthorizationrule), [Get-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/get-azservicebusauthorizationrule), [Set-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/set-azservicebusauthorizationrule)i [Remove-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/remove-azservicebusauthorizationrule) .

```powershell
# Query to see if rule exists
$CurrentRule = Get-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

# Check if the rule already exists or needs to be created
if ($CurrentRule)
{
    Write-Host "The $AuthRule rule already exists for the namespace $Namespace."
}
else
{
    Write-Host "The $AuthRule rule does not exist."
    Write-Host "Creating the $AuthRule rule for the $Namespace namespace..."
    New-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule -Rights @("Listen","Send")
    $CurrentRule = Get-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule
    Write-Host "The $AuthRule rule for the $Namespace namespace has been successfully created."

    Write-Host "Setting rights on the namespace"
    $authRuleObj = Get-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

    Write-Host "Remove Send rights"
    $authRuleObj.Rights.Remove("Send")
    Set-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Add Send and Manage rights to the namespace"
    $authRuleObj.Rights.Add("Send")
    Set-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj
    $authRuleObj.Rights.Add("Manage")
    Set-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Show value of primary key"
    $CurrentKey = Get-AzServiceBusKey -ResourceGroup $ResGrpName -NamespaceName $Namespace -Name $AuthRule
        
    Write-Host "Remove this authorization rule"
    Remove-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -Name $AuthRule
}
```

## <a name="create-a-queue"></a>Tworzenie kolejki

Aby utworzyć kolejkę lub temat, należy sprawdzić obszar nazw przy użyciu skryptu w poprzedniej sekcji. Następnie utwórz kolejkę:

```powershell
# Check if queue already exists
$CurrentQ = Get-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName

if($CurrentQ)
{
    Write-Host "The queue $QueueName already exists in the $Location region:"
}
else
{
    Write-Host "The $QueueName queue does not exist."
    Write-Host "Creating the $QueueName queue in the $Location region..."
    New-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -EnablePartitioning $True
    $CurrentQ = Get-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName
    Write-Host "The $QueueName queue in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

### <a name="modify-queue-properties"></a>Modyfikuj właściwości kolejki

Po wykonaniu skryptu w poprzedniej sekcji można użyć polecenia cmdlet [Set-AzServiceBusQueue](/powershell/module/az.servicebus/set-azservicebusqueue) , aby zaktualizować właściwości kolejki, jak w poniższym przykładzie:

```powershell
$CurrentQ.DeadLetteringOnMessageExpiration = $True
$CurrentQ.MaxDeliveryCount = 7
$CurrentQ.MaxSizeInMegabytes = 2048
$CurrentQ.EnableExpress = $True

Set-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -QueueObj $CurrentQ
```

## <a name="provisioning-other-service-bus-entities"></a>Inicjowanie obsługi innych jednostek Service Bus

Za pomocą [modułu Service Bus PowerShell](/powershell/module/az.servicebus) możesz inicjować inne jednostki, takie jak tematy i subskrypcje. Te polecenia cmdlet są składniowo podobne do poleceń cmdlet tworzenia kolejki, które przedstawiono w poprzedniej sekcji.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z [pełną dokumentacją modułu](/powershell/module/az.servicebus)Service Bus Menedżer zasobów programu PowerShell. Ta strona zawiera listę wszystkich dostępnych poleceń cmdlet.
- Aby uzyskać informacje o korzystaniu z szablonów Azure Resource Manager, zobacz artykuł [tworzenie Service Bus zasobów przy użyciu szablonów Azure Resource Manager](service-bus-resource-manager-overview.md).
- Informacje dotyczące [bibliotek zarządzania programu .net Service Bus](service-bus-management-libraries.md).

Istnieją różne sposoby zarządzania jednostkami Service Bus, zgodnie z opisem w następujących wpisach w blogu:

* [Jak utworzyć Service Bus kolejki, tematy i subskrypcje przy użyciu skryptu programu PowerShell](/archive/blogs/paolos/how-to-create-service-bus-queues-topics-and-subscriptions-using-a-powershell-script)
* [Jak utworzyć Service Bus przestrzeni nazw i centrum zdarzeń przy użyciu skryptu programu PowerShell](/archive/blogs/paolos/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script)
* [Skrypty Service Bus PowerShell](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Anchors-->

[purchase options]: https://azure.microsoft.com/pricing/purchase-options/
[member offers]: https://azure.microsoft.com/pricing/member-offers/
[free account]: https://azure.microsoft.com/pricing/free-trial/