---
title: Wdrażanie Notification Hubs i zarządzanie nimi przy użyciu programu PowerShell
description: Jak utworzyć Notification Hubs i zarządzać nimi za pomocą programu PowerShell dla usługi Automation
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 7c58f2c8-0399-42bc-9e1e-a7f073426451
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 4534584144f54618d7f3dd39cf5e40bc0464fb21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102454989"
---
# <a name="deploy-and-manage-notification-hubs-using-powershell"></a>Wdrażanie centrów powiadomień i zarządzanie nimi przy użyciu programu PowerShell

## <a name="overview"></a>Omówienie

W tym artykule pokazano, jak używać usługi Azure Notification Hubs tworzenia i zarządzania nią przy użyciu programu PowerShell. W tym artykule przedstawiono następujące typowe zadania automatyzacji.

- Tworzenie centrum powiadomień
- Ustaw poświadczenia

Jeśli trzeba również utworzyć nową przestrzeń nazw usługi Service Bus dla centrów powiadomień, zobacz [zarządzanie Service Bus przy użyciu programu PowerShell](../service-bus-messaging/service-bus-manage-with-ps.md).

Zarządzanie centrami powiadomień nie jest obsługiwane bezpośrednio przez polecenia cmdlet dołączone do Azure PowerShell. Najlepszym podejściem z programu PowerShell jest odwołanie do zestawu Microsoft.Azure.NotificationHubs.dll. Zestaw jest dystrybuowany z [pakietem NuGet Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Azure to platforma oparta na subskrypcjach. Aby uzyskać więcej informacji na temat uzyskiwania subskrypcji, zobacz [Opcje zakupu], [oferty członków]lub [bezpłatna wersja próbna].
- Komputer z Azure PowerShell. Aby uzyskać instrukcje, zobacz [Instalowanie i konfigurowanie Azure PowerShell].
- Ogólna znajomość skryptów programu PowerShell, pakietów NuGet i .NET Framework.

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Dołączenie odwołania do zestawu .NET dla Service Bus

Zarządzanie usługą Azure Notification Hubs nie jest jeszcze dołączone do poleceń cmdlet programu PowerShell w programie Azure PowerShell. Aby udostępnić Centra powiadomień, można użyć klienta platformy .NET dostarczonego w [pakiecie NuGet Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Najpierw upewnij się, że skrypt może zlokalizować zestaw **Microsoft.Azure.NotificationHubs.dll** , który jest instalowany jako pakiet NuGet w projekcie programu Visual Studio. Aby zapewnić elastyczność, skrypt wykonuje następujące czynności:

1. Określa ścieżkę, w której został wywołany.
2. Przechodzi ścieżką do momentu znalezienia folderu o nazwie `packages` . Ten folder jest tworzony podczas instalowania pakietów NuGet dla projektów programu Visual Studio.
3. Rekursywnie wyszukuje `packages` folder dla zestawu o nazwie `Microsoft.Azure.NotificationHubs.dll` .
4. Odwołuje się do zestawu, tak aby typy były dostępne do późniejszego użycia.

Oto, jak te kroki są zaimplementowane w skrypcie programu PowerShell:

``` powershell
try
{
    # WARNING: Make sure to reference the latest version of Microsoft.Azure.NotificationHubs.dll
    Write-Output "Adding the [Microsoft.Azure.NotificationHubs.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.Azure.NotificationHubs.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.Azure.NotificationHubs.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.Azure.NotificationHubs.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}
```

## <a name="create-the-namespacemanager-class"></a>Utwórz `NamespaceManager` klasę

Aby zainicjować obsługę administracyjną Notification Hubs, Utwórz wystąpienie klasy [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) z zestawu SDK.

Za pomocą polecenia cmdlet [Get-AzureSBAuthorizationRule] dołączonego do Azure PowerShell można pobrać regułę autoryzacji używaną w celu podania parametrów połączenia. Odwołanie do `NamespaceManager` wystąpienia jest przechowywane w `$NamespaceManager` zmiennej. `$NamespaceManager` służy do aprowizacji centrum powiadomień.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## <a name="provisioning-a-new-notification-hub"></a>Inicjowanie obsługi nowego centrum powiadomień

Aby udostępnić nowe centrum powiadomień, użyj [interfejsu API platformy .NET dla Notification Hubs].

W tej części skryptu można skonfigurować cztery zmienne lokalne.

1. `$Namespace`: Ustaw tę wartość na nazwę przestrzeni nazw, w której chcesz utworzyć centrum powiadomień.
2. `$Path`: Ustaw tę ścieżkę na nazwę nowego centrum powiadomień.  Na przykład "MyHub".
3. `$WnsPackageSid`: Ustaw tę wartość na identyfikator SID pakietu dla aplikacji systemu Windows z [Centrum deweloperów systemu Windows](https://developer.microsoft.com/en-us/windows).
4. `$WnsSecretkey`: Ustaw tę wartość na klucz tajny aplikacji systemu Windows z [Centrum deweloperów systemu Windows](https://developer.microsoft.com/en-us/windows).

Te zmienne są używane do nawiązywania połączenia z przestrzenią nazw i tworzenia nowego centrum powiadomień skonfigurowanego do obsługi powiadomień usługi Windows Notification Services (WNS) z poświadczeniami WNS dla aplikacji systemu Windows. Aby uzyskać informacje na temat uzyskiwania identyfikatora SID pakietu i klucza tajnego, zobacz [wprowadzenie z Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) samouczkiem.

- Fragment skryptu używa `NamespaceManager` obiektu do sprawdzenia, czy centrum powiadomień identyfikowane przez `$Path` istnieje.
- Jeśli nie istnieje, skrypt tworzy `NotificationHubDescription` przy użyciu poświadczeń WNS i przekazuje go do `NamespaceManager` `CreateNotificationHub` metody klasy.

``` powershell
$Namespace = "<Enter your namespace>"
$Path  = "<Enter a name for your notification hub>"
$WnsPackageSid = "<your package sid>"
$WnsSecretkey = "<enter your secret key>"

$WnsCredential = New-Object -TypeName Microsoft.Azure.NotificationHubs.WnsCredential -ArgumentList $WnsPackageSid,$WnsSecretkey

# Query the namespace
$CurrentNamespace = Get-AzureSBNamespace -Name $Namespace

# Check if the namespace already exists
if ($CurrentNamespace)
{
    Write-Output "The namespace [$Namespace] in the [$($CurrentNamespace.Region)] region was found."

    # Create the NamespaceManager object used to create a new notification hub
    $sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
    Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
    $NamespaceManager = [Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
    Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."

    # Check to see if the Notification Hub already exists
    if ($NamespaceManager.NotificationHubExists($Path))
    {
        Write-Output "The [$Path] notification hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] notification hub in the [$Namespace] namespace."
        $NHDescription = New-Object -TypeName Microsoft.Azure.NotificationHubs.NotificationHubDescription -ArgumentList $Path;
        $NHDescription.WnsCredential = $WnsCredential;
        $NamespaceManager.CreateNotificationHub($NHDescription);
        Write-Output "The [$Path] notification hub was created in the [$Namespace] namespace."
    }
}
else
{
    Write-Host "The [$Namespace] namespace does not exist."
}
```

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Zarządzanie magistralą usług za pomocą programu PowerShell](../service-bus-messaging/service-bus-manage-with-ps.md)
- [Jak utworzyć Service Bus kolejki, tematy i subskrypcje przy użyciu skryptu programu PowerShell](/archive/blogs/paolos/how-to-create-service-bus-queues-topics-and-subscriptions-using-a-powershell-script)
- [Jak utworzyć Service Bus przestrzeni nazw i centrum zdarzeń przy użyciu skryptu programu PowerShell](/archive/blogs/paolos/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script)

Niektóre gotowe skrypty są również dostępne do pobrania:

- [Skrypty Service Bus PowerShell](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)

[Opcje zakupu]: https://azure.microsoft.com/pricing/purchase-options/
[Oferty elementu członkowskiego]: https://azure.microsoft.com/pricing/member-offers/
[Bezpłatna wersja próbna]: https://azure.microsoft.com/pricing/free-trial/
[Zainstaluj i skonfiguruj Azure PowerShell]: /powershell/azure/
[Interfejs API platformy .NET dla Notification Hubs]: /dotnet/api/overview/azure/notification-hubs
[Get-AzureSBNamespace]: /powershell/module/servicemanagement/azure.service/get-azuresbnamespace
[New-AzureSBNamespace]: /powershell/module/servicemanagement/azure.service/new-azuresbnamespace
[Get-AzureSBAuthorizationRule]: /powershell/module/servicemanagement/azure.service/get-azuresbauthorizationrule
