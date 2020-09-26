---
title: Tworzenie elementu członkowskiego usługi Azure łańcucha bloków — Azure PowerShell
description: Utwórz członka usługi Azure łańcucha bloków dla konsorcjum łańcucha bloków przy użyciu Azure PowerShell.
ms.date: 9/22/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: 899778d60c32de7b0079e3858407c3e9fbed6f54
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91349090"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-powershell"></a>Szybki Start: Tworzenie elementu członkowskiego usługi Azure łańcucha bloków Service łańcucha bloków przy użyciu Azure PowerShell

W tym przewodniku szybki start wdrożono nowego członka łańcucha bloków i konsorcjum w usłudze Azure łańcucha bloków przy użyciu Azure PowerShell.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

Jeśli zdecydujesz się używać programu PowerShell lokalnie, ten artykuł będzie wymagał instalacji modułu AZ PowerShell i nawiązania połączenia z kontem platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) . Aby uzyskać więcej informacji na temat instalowania modułu AZ PowerShell module, zobacz [Install Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Mimo że moduł **AZ. łańcucha bloków** PowerShell jest w wersji zapoznawczej, należy go zainstalować oddzielnie z poziomu polecenia AZ PowerShell module przy użyciu narzędzia `Install-Module` . Po ogólnym udostępnieniu tego modułu programu PowerShell jest on częścią przyszłej wersji modułu AZ PowerShell releases i będzie dostępny natywnie z poziomu Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.Blockchain
```

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

## <a name="register-resource-provider"></a>Rejestrowanie dostawcy zasobów

Jeśli korzystasz z usługi Azure łańcucha bloków po raz pierwszy, musisz zarejestrować dostawcę zasobów **Microsoft. łańcucha bloków** .

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Blockchain
```

## <a name="choose-a-specific-azure-subscription"></a>Wybierz określoną subskrypcję platformy Azure

Jeśli masz wiele subskrypcji platformy Azure, wybierz odpowiednią subskrypcję, w której będą naliczane opłaty za zasoby. Wybierz określoną subskrypcję za pomocą polecenia cmdlet [Set-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext) .

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="define-variables"></a>Definiowanie zmiennych

Będziesz wielokrotnie używać kilku informacji. Utwórz zmienne do przechowywania informacji.

```azurepowershell-interactive
# Name of resource group used throughout this article
$resourceGroupName = 'myResourceGroup'

# Azure region
$location = 'eastus'
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz [grupę zasobów platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) za pomocą polecenia cmdlet [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) . Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi w formie grupy.

Poniższy przykład tworzy grupę zasobów na podstawie nazwy w `$resourceGroupName` zmiennej w regionie określonym w `$location` zmiennej.

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
```

## <a name="create-a-blockchain-member"></a>Tworzenie elementu członkowskiego łańcucha bloków

Członek usługi Azure łańcucha bloków to węzeł łańcucha bloków w sieci prywatnej łańcucha bloków.
Podczas aprowizacji elementu członkowskiego można utworzyć sieć konsorcjum lub dołączyć do niej. Potrzebujesz co najmniej jednego elementu członkowskiego dla sieci konsorcjum. Liczba członków łańcucha bloków wymaganych przez uczestników zależy od danego scenariusza. Uczestnicy konsorcjum mogą mieć co najmniej jednego członka łańcucha bloków lub mogą współdzielić członków z innymi uczestnikami. Aby uzyskać więcej informacji na temat konsorcjów, zobacz [Azure łańcucha bloków Service Consortium](consortium.md).

Istnieje kilka parametrów i właściwości, które należy przekazać. Zastąp przykładowe parametry wartościami.

```azurepowershell-interactive
$passwd = Read-Host -Prompt 'Enter the members default transaction node password' -AsSecureString
$csPasswd = Read-Host -Prompt 'Enter  the consortium account password' -AsSecureString

$memberParams = @{
  Name = 'myblockchainmember'
  ResourceGroupName = $resourceGroupName
  Consortium = 'myconsortium'
  ConsortiumManagementAccountPassword = $csPasswd
  Location = $location
  Password = $passwd
  Protocol = 'Quorum'
  Sku = 'S0'
}
New-AzBlockchainMember @memberParams
```

| Parametr | Opis |
|---------|-------------|
| **ResourceGroupName** | Nazwa grupy zasobów, w której są tworzone zasoby usługi Azure łańcucha bloków. Użyj grupy zasobów utworzonej w poprzedniej sekcji.
| **Nazwa** | Unikatowa nazwa identyfikująca członka usługi Azure łańcucha bloków Service łańcucha bloków. Nazwa jest używana dla publicznego adresu punktu końcowego. Na przykład `myblockchainmember.blockchain.azure.com`.
| **Lokalizacja** | Region świadczenia usługi Azure, w którym jest tworzony element członkowski łańcucha bloków. Na przykład `westus2`. Wybierz lokalizację najbliżej użytkowników lub innych aplikacji Azure. Funkcje mogą być niedostępne w niektórych regionach. Usługa Azure łańcucha bloków Data Manager jest dostępna w następujących regionach świadczenia usługi Azure: Wschodnie stany USA i Europa Zachodnia.
| **Hasło** | Hasło dla domyślnego węzła transakcji elementu członkowskiego. Użyj hasła uwierzytelniania podstawowego podczas nawiązywania połączenia z domyślnym punktem końcowym węzła transakcji elementu członkowskiego łańcucha bloków.
| **Protokół** | Protokół łańcucha bloków. Obecnie jest obsługiwany protokół _kworum_ .
| **Konsorcjum** | Nazwa konsorcjum do przyłączenia lub utworzenia. Aby uzyskać więcej informacji na temat konsorcjów, zobacz [Azure łańcucha bloków Service Consortium](consortium.md).
| **ConsortiumManagementAccountPassword** | Hasło konta konsorcjum jest również znane jako hasło do konta elementu członkowskiego. Hasło konta elementu członkowskiego służy do szyfrowania klucza prywatnego dla konta Ethereum utworzonego dla elementu członkowskiego. Do zarządzania konsorcjum używasz konta elementu członkowskiego i hasła konta elementu członkowskiego.
| **Magazyn** | Typ warstwy. **S0** dla warstwy Standardowa lub **B0** dla warstwy Podstawowa. Skorzystaj z warstwy _podstawowa_ na potrzeby tworzenia, testowania i sprawdzania poprawności koncepcji. Użyj warstwy _standardowa_ dla wdrożeń klasy produkcyjnej. W przypadku korzystania z łańcucha bloków Data Manager lub wysyłania dużej liczby transakcji prywatnych należy również użyć warstwy _standardowa_ . Zmiana warstwy cenowej między podstawowa i Standardowa po utworzeniu elementu członkowskiego nie jest obsługiwana.

Utworzenie elementu członkowskiego łańcucha bloków i obsłudze zasobów trwa około 10 minut.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Możesz użyć elementu członkowskiego łańcucha bloków utworzonego dla następnego przewodnika Szybki start lub samouczka. Gdy zasoby nie będą już potrzebne, można je usunąć przez usunięcie `myResourceGroup` grupy zasobów utworzonej dla przewodnika Szybki Start.

> [!CAUTION]
> Poniższy przykład usuwa określoną grupę zasobów i wszystkie znajdujące się w niej zasoby.
> Jeśli zasoby spoza zakresu tego artykułu istnieją w określonej grupie zasobów, zostaną również usunięte.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start wdrożono członka usługi Azure łańcucha bloków i nowego konsorcjum. Skorzystaj z następnego przewodnika Szybki Start, aby użyć usługi Azure łańcucha bloków Development Kit dla Ethereum w celu dołączenia do członka usług Azure łańcucha bloków.

> [!div class="nextstepaction"]
> [Użyj Visual Studio Code, aby nawiązać połączenie z usługą Azure łańcucha bloków](connect-vscode.md)
