---
title: Szybki Start — Tworzenie wystąpienia usługi Azure API Management przy użyciu programu PowerShell | Microsoft Docs
description: Utwórz nowe wystąpienie usługi Azure API Management przy użyciu Azure PowerShell.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/14/2020
ms.author: apimpm
ms.openlocfilehash: eb2c42d26a85a07518a018ba5b8817f13d3cd17f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90707062"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-by-using-powershell"></a>Szybki Start: Tworzenie nowego wystąpienia usługi Azure API Management przy użyciu programu PowerShell

Usługa Azure API Management (APIM) pomaga organizacjom publikować interfejsy API dla deweloperów zewnętrznych, partnerskich i wewnętrznych, aby w pełni wykorzystać potencjał danych i usług. Usługa API Management zapewnia podstawowe możliwości, które pozwalają na pomyślne programowanie interfejsów API przez zaangażowanych deweloperów, a także udostępnia wgląd w kwestie biznesowe, analizy, zabezpieczenia i ochronę. Usługa APIM pozwala tworzyć nowoczesne bramy interfejsów API dla istniejących usług zaplecza hostowanych w dowolnym miejscu oraz zarządzać tymi bramami. Aby uzyskać więcej informacji, zapoznaj się z [omówieniem](api-management-key-concepts.md).

W tym przewodniku szybki start opisano procedurę tworzenia nowego wystąpienia API Management przy użyciu Azure PowerShell poleceń cmdlet.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten samouczek będzie wymagał modułu programu Azure PowerShell w wersji 1.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów platformy Azure za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 

Następujące polecenie tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji zachodnie stany USA:

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location WestUS
```

## <a name="create-an-api-management-service"></a>Tworzenie usługi API Management

Teraz, gdy masz grupę zasobów, możesz utworzyć wystąpienie usługi API Management. Utwórz je za pomocą polecenia [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) i podaj nazwę usługi oraz szczegóły wydawcy. Nazwa usługi musi być unikatowa w ramach platformy Azure.

W poniższym przykładzie nazwa usługi jest *myapim* . Zaktualizuj nazwę na unikatową wartość. Należy również zaktualizować nazwę organizacji wydawcy interfejsu API i adres e-mail administratora, aby otrzymywać powiadomienia.

Domyślnie polecenie tworzy wystąpienie w warstwie Deweloper, która jest ekonomiczną opcją oszacowania API Management platformy Azure. Ta warstwa nie jest używana do użycia w środowisku produkcyjnym. Aby uzyskać więcej informacji na temat skalowania warstw usługi API Management, zobacz [Upgrade and scale](upgrade-and-scale.md) (Uaktualnianie i skalowanie).

> [!NOTE]
> Jest to długotrwała operacja. Utworzenie i aktywowanie usługi API Management w tej warstwie może potrwać od 30 do 40 minut.

```azurepowershell-interactive
New-AzApiManagement -Name "myapim" -ResourceGroupName "myResourceGroup" `
  -Location "West US" -Organization "Contoso" -AdminEmail "admin@contoso.com" 
```

Po powrocie polecenia Uruchom polecenie [Get-AzApiManagement](/powershell/module/az.apimanagement/get-azapimanagement) , aby wyświetlić właściwości usługi Azure API Management. Po aktywacji stan aprowizacji zostanie zakończony pomyślnie, a wystąpienie usługi zawiera kilka skojarzonych adresów URL. Na przykład:

```azurepowershell-interactive
Get-AzApiManagement -Name "myapim" -ResourceGroupName "myResourceGroup" 
```

Przykładowe dane wyjściowe:

```console
PublicIPAddresses                     : {203.0.113.1}
PrivateIPAddresses                    :
Id                                    : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.ApiManagement/service/myapim
Name                                  : myapim
Location                              : West US
Sku                                   : Developer
Capacity                              : 1
CreatedTimeUtc                        : 9/9/2020 9:07:43 PM
ProvisioningState                     : Succeeded
RuntimeUrl                            : https://myapim.azure-api.net
RuntimeRegionalUrl                    : https://myapi-westus-01.regional.azure-api.net
PortalUrl                             : https://myapim.portal.azure-api.net
DeveloperPortalUrl                    : https://myapim.developer.azure-api.net
ManagementApiUrl                      : https://myapim.management.azure-api.net
ScmUrl                                : https://myapim.scm.azure-api.net
PublisherEmail                        : admin@contoso.com
OrganizationName                      : Contoso
NotificationSenderEmail               : apimgmt-noreply@mail.windowsazure.com
VirtualNetwork                        :
VpnType                               : None
PortalCustomHostnameConfiguration     :
ProxyCustomHostnameConfiguration      : {myapim.azure-api.net}
ManagementCustomHostnameConfiguration :
ScmCustomHostnameConfiguration        :
DeveloperPortalHostnameConfiguration  :
SystemCertificates                    :
Tags                                  : {}
AdditionalRegions                     : {}
SslSetting                            : Microsoft.Azure.Commands.ApiManagement.Models.PsApiManagementSslSetting
Identity                              :
EnableClientCertificate               :
ResourceGroupName                     : myResourceGroup

```

Po wdrożeniu wystąpienia usługi API Management jest gotowe do użycia. Zacznij od samouczka, aby [zaimportować i opublikować pierwszy interfejs API](import-and-publish.md).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Importowanie i publikowanie pierwszego interfejsu API](import-and-publish.md)
