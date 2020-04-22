---
title: Szybki start — tworzenie profilu i punktu końcowego przy użyciu szablonów Menedżera zasobów
titleSuffix: Azure Content Delivery Network
description: Dowiedz się, jak utworzyć profil i punkt końcowy dostarczania zawartości platformy Azure przy użyciu szablonów Menedżera zasobów
services: cdn
documentationcenter: ''
author: senthuransivananthan
manager: danielgi
editor: ''
ms.assetid: cbc2ff98-916d-4339-8959-622823c5b772
ms.service: azure-cdn
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/05/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: dfd7b933502b96c0952a24dbee563e9b537dcdd8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683472"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint-using-resource-manager-template"></a>Szybki start: tworzenie profilu i punktu końcowego usługi Azure CDN przy użyciu szablonu Menedżera zasobów

W tym przewodniku Szybki start można wdrożyć szablon usługi Azure Resource Manager przy użyciu interfejsu wiersza polecenia. Utworzony szablon wdraża profil sieci CDN i punkt końcowy usługi CDN z przodu aplikacji sieci web.
Wykonanie tych kroków powinno potrwać około dziesięciu minut.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prequisites"></a>Wymagania wstępne

Na potrzeby tego przewodnika Szybki start musisz mieć aplikację sieci Web, która będzie używana jako źródło pochodzenia. Przykładowa aplikacja sieci Web użyta w tym przewodniku Szybki start została wdrożonahttps://cdndemo.azurewebsites.net

Aby uzyskać więcej informacji, zobacz [Tworzenie statycznej aplikacji sieci Web HTML na platformie Azure](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-html).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Wszystkie zasoby muszą być wdrożone w tej samej grupie zasobów.

Utwórz grupę zasobów w wybranej lokalizacji. W tym przykładzie pokazano utworzenie grupy zasobów o nazwie cdn w lokalizacji wschodnich stanów USA.

```azurecli-interactive
az group create --name cdn --location eastus
```

![Nowa grupa zasobów](./media/create-profile-resource-manager-template/cdn-create-resource-group.png)

## <a name="create-the-resource-manager-template"></a>Tworzenie szablonu usługi Resource Manager

W tym kroku należy utworzyć plik szablonu, który wdraża zasoby.

W tym przykładzie można przejść przez scenariusz ogólnego przyspieszenia witryny sieci Web, istnieje wiele innych ustawień, które można skonfigurować. Te ustawienia są dostępne w odwołaniu do szablonu usługi Azure Resource Manager. Zapoznaj się z odwołaniami do [punktu końcowego profilu sieci CDN](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles) i [punktu końcowego profilu SIECI CDN](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles/endpoints).

Należy zauważyć, że usługa Microsoft CDN nie obsługuje modyfikowania listy typów zawartości.

Zapisz szablon jako **resource-manager-cdn.json**.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "cdnProfileSku": {
            "type": "string",
            "allowedValues": [
                "Standard_Microsoft",
                "Standard_Akamai",
                "Standard_Verizon",
                "Premium_Verizon"
            ]
        },
        "endpointOriginHostName": {
            "type": "string"
        }
    },
    "variables": {
        "profile": {
            "name": "[replace(toLower(parameters('cdnProfileSku')), '_', '-')]"
        },
        "endpoint": {
            "name": "[replace(toLower(parameters('endpointOriginHostName')), '.', '-')]",
            "originHostName": "[parameters('endpointOriginHostName')]"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Cdn/profiles",
            "apiVersion": "2017-10-12",
            "location": "[resourceGroup().location]",
            "name": "[variables('profile').name]",
            "sku": {
                "name": "[parameters('cdnProfileSku')]"
            }
        },
        {
            "dependsOn": [
                "[resourceId('Microsoft.Cdn/profiles', variables('profile').name)]"
            ],
            "type": "Microsoft.Cdn/profiles/endpoints",
            "apiVersion": "2017-10-12",
            "location": "[resourceGroup().location]",
            "name": "[concat(variables('profile').name, '/', variables('endpoint').name)]",
            "properties": {
                "hostName": "[concat(variables('endpoint').name, '.azureedge.net')]",
                "originHostHeader": "[variables('endpoint').originHostName]",
                "isHttpAllowed": true,
                "isHttpsAllowed": true,
                "queryStringCachingBehavior": "IgnoreQueryString",
                "origins": [
                    {
                        "name": "[replace(variables('endpoint').originHostName, '.', '-')]",
                        "properties": {
                            "hostName": "[variables('endpoint').originHostName]",
                            "httpPort": 80,
                            "httpsPort": 443
                        }
                    }
                ],
                "contentTypesToCompress": [
                    "application/eot",
                    "application/font",
                    "application/font-sfnt",
                    "application/javascript",
                    "application/json",
                    "application/opentype",
                    "application/otf",
                    "application/pkcs7-mime",
                    "application/truetype",
                    "application/ttf",
                    "application/vnd.ms-fontobject",
                    "application/xhtml+xml",
                    "application/xml",
                    "application/xml+rss",
                    "application/x-font-opentype",
                    "application/x-font-truetype",
                    "application/x-font-ttf",
                    "application/x-httpd-cgi",
                    "application/x-javascript",
                    "application/x-mpegurl",
                    "application/x-opentype",
                    "application/x-otf",
                    "application/x-perl",
                    "application/x-ttf",
                    "font/eot",
                    "font/ttf",
                    "font/otf",
                    "font/opentype",
                    "image/svg+xml",
                    "text/css",
                    "text/csv",
                    "text/html",
                    "text/javascript",
                    "text/js",
                    "text/plain",
                    "text/richtext",
                    "text/tab-separated-values",
                    "text/xml",
                    "text/x-script",
                    "text/x-component",
                    "text/x-java-source"
                ],
                "isCompressionEnabled": true,
                "optimizationType": "GeneralWebDelivery"
            }
        }
    ],
    "outputs": {
        "cdnUrl": {
            "type": "string",
            "value": "[concat('https://', variables('endpoint').name, '.azureedge.net')]"
        }
    }
}
```

## <a name="create-the-resources"></a>Tworzenie zasobów

Wdrażanie szablonu przy użyciu interfejsu wiersza polecenia platformy Azure. Zostanie wyświetlony monit o 2 wejścia:

**cdnProfileSku** - dostawca CDN, którego chcesz użyć. Dostępne opcje to:

* Standard_Microsoft
* Standard_Akamai
* Standard_Verizon
* Premium_Verizon.

**endpointOriginHostName** — punkt końcowy, który będzie obsługiwany za pośrednictwem sieci CDN, na przykład cdndemo.azurewebsites.net.

```azurecli-interactive
az group deployment create --resource-group cdn --template-file arm-cdn.json
```

![Wdrażanie szablonu Menedżera zasobów](./media/create-profile-resource-manager-template/cdn-deploy-resource-manager.png)

## <a name="view-the-cdn-profile"></a>Wyświetlanie profilu sieci CDN

```azurecli-interactive
az cdn profile list --resource-group cdn -o table
```

![Wyświetl profil sieci CDN](./media/create-profile-resource-manager-template/cdn-view-profile.png)

## <a name="view-the-cdn-endpoint-for-the-profile-standard-microsoft"></a>Wyświetlanie punktu końcowego sieci CDN dla profilu standard-microsoft

```azurecli-interactive
az cdn endpoint list --profile-name standard-microsoft --resource-group cdn -o table
```

![Wyświetlanie punktu końcowego sieci CDN](./media/create-profile-resource-manager-template/cdn-view-endpoint.png)

Użyj nazwy hosta, aby wyświetlić zawartość. Na przykład dostęp https:\//cdndemo-azurewebsites-net.azureedge.net za pomocą przeglądarki.

## <a name="clean-up"></a>Czyszczenie

Usunięcie grupy zasobów spowoduje automatyczne usunięcie wszystkich zasobów, które zostały w niej wdrożone.

```azurecli-interactive
az group delete --name cdn
```

![Usuń grupę zasobów](./media/create-profile-resource-manager-template/cdn-delete-resource-group.png)

## <a name="references"></a>Dokumentacja

* Profil usługi CDN — [odwołanie do szablonu usługi Azure Resource Manager](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles)
* Punkt końcowy usługi CDN — [dokumentacja odwołania do szablonu usługi Azure Resource Manager](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles/endpoints)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat dodawania domeny niestandardowej do punktu końcowego usługi CDN, zapoznaj się z następującym samouczkiem:

> [!div class="nextstepaction"]
> [Samouczek: dodawanie domeny niestandardowej do punktu końcowego usługi Azure CDN](cdn-map-content-to-custom-domain.md)
