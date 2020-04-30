---
title: Szybki Start — Tworzenie profilu i punktu końcowego przy użyciu szablonów Menedżer zasobów
titleSuffix: Azure Content Delivery Network
description: Dowiedz się, jak utworzyć profil sieci i punkt końcowy dostarczania zawartości platformy Azure przy użyciu szablonów Menedżer zasobów
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81683472"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint-using-resource-manager-template"></a>Szybki Start: Tworzenie profilu Azure CDN i punktu końcowego przy użyciu szablonu Menedżer zasobów

W tym przewodniku szybki start wdrażasz szablon Azure Resource Manager przy użyciu interfejsu wiersza polecenia. Tworzony szablon wdraża profil usługi CDN i punkt końcowy usługi CDN na potrzeby aplikacji sieci Web.
Wykonanie tych kroków powinno potrwać około 10 minut.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prequisites"></a>Wymagania wstępne

Na potrzeby tego przewodnika Szybki Start aplikacja sieci Web musi być używana jako źródło. Przykładowa aplikacja sieci Web używana w tym przewodniku szybki start została wdrożona w programiehttps://cdndemo.azurewebsites.net

Aby uzyskać więcej informacji, zobacz [Tworzenie statycznej aplikacji sieci Web w języku HTML na platformie Azure](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-html).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Wszystkie zasoby muszą zostać wdrożone w tej samej grupie zasobów.

Utwórz grupę zasobów w wybranej lokalizacji. Ten przykład pokazuje Tworzenie grupy zasobów o nazwie CDN w lokalizacji Wschodnie stany USA.

```azurecli-interactive
az group create --name cdn --location eastus
```

![Nowa grupa zasobów](./media/create-profile-resource-manager-template/cdn-create-resource-group.png)

## <a name="create-the-resource-manager-template"></a>Tworzenie szablonu usługi Resource Manager

W tym kroku utworzysz plik szablonu, który wdraża zasoby.

Ten przykład przeprowadzi Cię przez ogólny scenariusz przyspieszania witryn sieci Web, ale istnieje wiele innych ustawień, które można skonfigurować. Te ustawienia są dostępne w odniesieniu do szablonu Azure Resource Manager. Zobacz odwołania do [profilu usługi CDN](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles) i [punktu końcowego profilu sieci CDN](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles/endpoints).

Należy pamiętać, że usługa Microsoft CDN nie obsługuje modyfikowania listy typów zawartości.

Zapisz szablon jako **Resource-Manager-CDN. JSON**.

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

Wdróż szablon przy użyciu interfejsu wiersza polecenia platformy Azure. Zostanie wyświetlony monit o 2 dane wejściowe:

**cdnProfileSku** — Dostawca usługi CDN, którego chcesz użyć. Dostępne są następujące opcje:

* Standard_Microsoft
* Standard_Akamai
* Standard_Verizon
* Premium_Verizon.

**endpointOriginHostName** — punkt końcowy, który będzie obsługiwany w sieci CDN, na przykład cdndemo.azurewebsites.NET.

```azurecli-interactive
az group deployment create --resource-group cdn --template-file arm-cdn.json
```

![Wdróż szablon Menedżer zasobów](./media/create-profile-resource-manager-template/cdn-deploy-resource-manager.png)

## <a name="view-the-cdn-profile"></a>Wyświetlanie profilu usługi CDN

```azurecli-interactive
az cdn profile list --resource-group cdn -o table
```

![Wyświetl profil usługi CDN](./media/create-profile-resource-manager-template/cdn-view-profile.png)

## <a name="view-the-cdn-endpoint-for-the-profile-standard-microsoft"></a>Wyświetlanie punktu końcowego usługi CDN dla profilu Standard — Microsoft

```azurecli-interactive
az cdn endpoint list --profile-name standard-microsoft --resource-group cdn -o table
```

![Wyświetl punkt końcowy usługi CDN](./media/create-profile-resource-manager-template/cdn-view-endpoint.png)

Użyj nazwy hosta, aby wyświetlić zawartość. Na przykład dostęp do usługi https\/:/cdndemo-azurewebsites-net.azureedge.NET za pomocą przeglądarki.

## <a name="clean-up"></a>Czyszczenie

Usunięcie grupy zasobów spowoduje automatyczne usunięcie wszystkich zasobów, które zostały w niej wdrożone.

```azurecli-interactive
az group delete --name cdn
```

![Usuń grupę zasobów](./media/create-profile-resource-manager-template/cdn-delete-resource-group.png)

## <a name="references"></a>Dokumentacja

* Profil usługi CDN — [Dokumentacja szablonu Azure Resource Manager](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles)
* Punkt końcowy usługi CDN — [Dokumentacja szablonu Azure Resource Manager](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles/endpoints)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat dodawania domeny niestandardowej do punktu końcowego usługi CDN, zapoznaj się z następującym samouczkiem:

> [!div class="nextstepaction"]
> [Samouczek: dodawanie domeny niestandardowej do punktu końcowego usługi Azure CDN](cdn-map-content-to-custom-domain.md)
