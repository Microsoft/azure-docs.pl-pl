---
title: Tworzenie Azure Blockchain Service — Azure PowerShell
description: Utwórz członka Azure Blockchain Service dla konsorcjum łańcucha bloków przy użyciu Azure PowerShell.
ms.reviewer: ravastra
ms.date: 9/22/2020
ms.topic: quickstart
ms.custom:
- references_regions
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 9087bda9c1979a98f4bbf9f8343d012c4cf3098c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529633"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-powershell"></a>Szybki start: tworzenie Azure Blockchain Service łańcucha bloków przy użyciu Azure PowerShell

W tym przewodniku Szybki start wdrożysz nowego członka łańcucha bloków i konsorcjum w Azure Blockchain Service użyciu Azure PowerShell.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

Jeśli zdecydujesz się używać programu PowerShell lokalnie, ten artykuł wymaga zainstalowania modułu Az programu PowerShell i nawiązania połączenia z kontem platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Aby uzyskać więcej informacji na temat instalowania modułu Az programu PowerShell, zobacz [Instalowanie Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Gdy moduł **Az.Blockchain** programu PowerShell jest w wersji zapoznawczej, należy go zainstalować niezależnie od modułu Az programu PowerShell przy użyciu `Install-Module` polecenia cmdlet . Gdy ten moduł programu PowerShell stanie się ogólnie dostępny, stanie się częścią przyszłych wersji modułu Az programu PowerShell i będzie dostępny natywnie z poziomu Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.Blockchain
```

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

## <a name="register-resource-provider"></a>Rejestrowanie dostawcy zasobów

Jeśli korzystasz z usługi Azure Blockchain po raz pierwszy, musisz zarejestrować dostawcę **zasobów Microsoft.Blockchain.**

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Blockchain
```

## <a name="choose-a-specific-azure-subscription"></a>Wybieranie określonej subskrypcji platformy Azure

Jeśli masz wiele subskrypcji platformy Azure, wybierz odpowiednią subskrypcję, w ramach której mają być naliczane opłaty za zasoby. Wybierz określoną subskrypcję za pomocą polecenia cmdlet [Set-AzContext.](/powershell/module/az.accounts/set-azcontext)

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="define-variables"></a>Definiowanie zmiennych

Będziesz wielokrotnie używać kilku rodzajów informacji. Utwórz zmienne do przechowywania informacji.

```azurepowershell-interactive
# Name of resource group used throughout this article
$resourceGroupName = 'myResourceGroup'

# Azure region
$location = 'eastus'
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz [grupę zasobów platformy Azure](../../azure-resource-manager/management/overview.md) przy użyciu polecenia cmdlet [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi w formie grupy.

Poniższy przykład tworzy grupę zasobów na podstawie nazwy zmiennej w `$resourceGroupName` regionie określonym w zmiennej `$location` .

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
```

## <a name="create-a-blockchain-member"></a>Tworzenie członka łańcucha bloków

Członek Azure Blockchain Service jest węzłem łańcucha bloków w sieci łańcucha bloków prywatnej konsorcjum.
Podczas aprowizowania członka możesz utworzyć lub dołączyć do sieci konsorcjum. Potrzebujesz co najmniej jednego członka sieci konsorcjum. Liczba elementów członkowskich łańcucha bloków wymaganych przez uczestników zależy od scenariusza. Uczestnicy konsorcjum mogą mieć co najmniej jednego członka łańcucha bloków lub mogą udostępniać członków innym uczestnikom. Aby uzyskać więcej informacji na temat consortia, zobacz [Azure Blockchain Service consortium](consortium.md).

Istnieje kilka parametrów i właściwości, które należy przekazać. Zastąp przykładowe parametry swoimi wartościami.

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
| **ResourceGroupName** | Nazwa grupy zasobów, w Azure Blockchain Service zasoby są tworzone. Użyj grupy zasobów utworzonej w poprzedniej sekcji.
| **Nazwa** | Unikatowa nazwa identyfikująca twój Azure Blockchain Service łańcucha bloków. Nazwa jest używana dla publicznego adresu punktu końcowego. Na przykład `myblockchainmember.blockchain.azure.com`.
| **Lokalizacja** | Region świadczenia usługi Azure, w którym jest tworzony członek łańcucha bloków. Na przykład `westus2`. Wybierz lokalizację najbliżej użytkowników lub innych aplikacji Azure. Funkcje mogą nie być dostępne w niektórych regionach. Azure Blockchain Data Manager jest dostępna w następujących regionach świadczenia usługi Azure: Wschodnie stany USA i Europa Zachodnia.
| **Password** (Hasło) | Hasło domyślnego węzła transakcji członka. Użyj hasła do uwierzytelniania podstawowego podczas nawiązywania połączenia z domyślnym publicznym punktem końcowym węzła transakcji członka łańcucha bloków.
| **Protokół** | Protokół łańcucha bloków. Obecnie obsługiwany _jest_ protokół kworum.
| **Konsorcjum** | Nazwa konsorcjum, które ma dołączyć lub utworzyć. Aby uzyskać więcej informacji na temat consortia, zobacz [Azure Blockchain Service consortium](consortium.md).
| **ConsortiumManagementAccountPassword** | Hasło do konta konsorcjum jest również nazywane hasłem konta członkowskiego. Hasło konta członkowskiego jest używane do szyfrowania klucza prywatnego dla konta Ethereum utworzonego dla członka. Do zarządzania konsorcjum należy użyć konta członkowskiego i hasła do konta członka.
| **Numer jednostki magazynowej** | Typ warstwy. **S0** w standardowych lub **B0** w standardowych. Użyj warstwy _Podstawowa_ do tworzenia, testowania i weryfikacji koncepcji. Użyj warstwy _Standardowa_ dla wdrożeń klasy produkcyjnej. Należy również użyć warstwy _Standardowa,_ jeśli używasz Blockchain Data Manager lub wysyłasz dużą ilość transakcji prywatnych. Zmiana warstwy cenowej między podstawową i standardową po utworzeniu członków nie jest obsługiwana.

Utworzenie członka łańcucha bloków i zasobów obsługi trwa około 10 minut.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Możesz użyć członka łańcucha bloków utworzonego w następnym przewodniku Szybki start lub samouczku. Gdy zasoby nie będą już potrzebne, możesz je usunąć, usuwając `myResourceGroup` grupę zasobów utworzoną na potrzeby przewodnika Szybki start.

> [!CAUTION]
> Poniższy przykład usuwa określoną grupę zasobów i wszystkie zawarte w niej zasoby.
> Jeśli zasoby poza zakresem tego artykułu istnieją w określonej grupie zasobów, również zostaną usunięte.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wdrożono Azure Blockchain Service i nowe konsorcjum. Wypróbuj następny przewodnik Szybki start, aby użyć Azure Blockchain Development Kit for Ethereum w celu dołączenia do Azure Blockchain Service członkowskiego.

> [!div class="nextstepaction"]
> [Użyj Visual Studio Code, aby nawiązać połączenie z Azure Blockchain Service](connect-vscode.md)
