---
title: Tworzenie aplikacji App Service przy użyciu szablonu Azure Resource Manager
description: Utwórz pierwszą aplikację do Azure App Service w ciągu kilku sekund przy użyciu szablonu Azure Resource Manager (szablon ARM), który jest jednym z wielu sposobów wdrożenia w App Service.
author: msangapu-msft
ms.author: msangapu
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 10/16/2020
ms.custom: subject-armqs, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: c8542bfe3d1393917a63e4a1feae7d6dfc223031
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746184"
---
# <a name="quickstart-create-app-service-app-using-an-arm-template"></a>Szybki Start: Tworzenie aplikacji App Service przy użyciu szablonu ARM

Rozpocznij pracę z [Azure App Service](overview.md) , wdrażając aplikację w chmurze przy użyciu szablonu Azure Resource Manager (szablon ARM) i [interfejsu wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli) w Cloud Shell. Ponieważ korzystasz z bezpłatnej warstwy App Service, nie poniesiesz żadnych kosztów, aby ukończyć ten przewodnik Szybki Start.

 [!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure** . Szablon zostanie otwarty w witrynie Azure Portal.

Aby wdrożyć system w systemie **Linux** , użyj następującego przycisku:

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-service-docs-linux%2Fazuredeploy.json)

Użyj następującego przycisku, aby wdrożyć w **systemie Windows** :

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-service-docs-windows%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="review-the-template"></a>Przegląd szablonu

::: zone pivot="platform-windows"
Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-app-service-docs-windows). Wdraża Plan App Service i aplikację App Service w systemie Windows. Jest on zgodny z platformą .NET Core, .NET Framework, PHP, Node.js i statycznymi aplikacjami HTML. W przypadku języka Java zobacz [Tworzenie aplikacji Java](./quickstart-java.md).

:::code language="json" source="~/quickstart-templates/101-app-service-docs-windows/azuredeploy.json":::

Dwa zasoby platformy Azure są zdefiniowane w szablonie:

* [**Microsoft. Web/dopuszczalna**](/azure/templates/microsoft.web/serverfarms): tworzenie planu App Service.
* [**Microsoft. Web/Sites**](/azure/templates/microsoft.web/sites): tworzenie aplikacji App Service.

Ten szablon zawiera kilka parametrów, które są wstępnie zdefiniowane dla wygody użytkownika. Zapoznaj się z tabelą poniżej, aby poznać wartości domyślne parametrów i ich opisy:

| Parametry | Typ    | Wartość domyślna                | Opis |
|------------|---------|------------------------------|-------------|
| webAppName | string  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | Nazwa aplikacji |
| location   | string  | "[[resourceName (). Location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | Region aplikacji |
| sku        | string  | Nacionięcie                         | Rozmiar wystąpienia (F1 = warstwa Bezpłatna) |
| language   | string  | architektury                       | Stos języka programowania (.NET, php, Node, html) |
| helloWorld | boolean | Fałsz                        | True = Wdróż aplikację "Hello world" |
| repoUrl    | string  | " "                          | Zewnętrzne repozytorium git (opcjonalnie) |
::: zone-end
::: zone pivot="platform-linux"
Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-app-service-docs-linux). Wdraża Plan App Service i aplikację App Service w systemie Linux. Są one zgodne ze wszystkimi obsługiwanymi językami programowania w App Service.

:::code language="json" source="~/quickstart-templates/101-app-service-docs-linux/azuredeploy.json":::

Dwa zasoby platformy Azure są zdefiniowane w szablonie:

* [**Microsoft. Web/dopuszczalna**](/azure/templates/microsoft.web/serverfarms): tworzenie planu App Service.
* [**Microsoft. Web/Sites**](/azure/templates/microsoft.web/sites): tworzenie aplikacji App Service.

Ten szablon zawiera kilka parametrów, które są wstępnie zdefiniowane dla wygody użytkownika. Zapoznaj się z tabelą poniżej, aby poznać wartości domyślne parametrów i ich opisy:

| Parametry | Typ    | Wartość domyślna                | Opis |
|------------|---------|------------------------------|-------------|
| webAppName | string  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | Nazwa aplikacji |
| location   | string  | "[[resourceName (). Location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | Region aplikacji |
| sku        | string  | Nacionięcie                         | Rozmiar wystąpienia (F1 = warstwa Bezpłatna) |
| linuxFxVersion   | string  | "DOTNETCORE&#124;3,0        | "Stos języka programowania &#124; wersja" |
| repoUrl    | string  | " "                          | Zewnętrzne repozytorium git (opcjonalnie) |

---
::: zone-end

## <a name="deploy-the-template"></a>Wdrażanie szablonu

Interfejs wiersza polecenia platformy Azure jest używany tutaj do wdrożenia szablonu. Można również użyć interfejsu API Azure Portal, Azure PowerShell i REST. Aby poznać inne metody wdrażania, zobacz [wdrażanie szablonów](../azure-resource-manager/templates/deploy-powershell.md).

Poniższy kod tworzy grupę zasobów, plan App Service i aplikację sieci Web. Ustawiono domyślną grupę zasobów, plan App Service i lokalizację. Zamień na `<app-name>` globalnie unikatową nazwę aplikacji (prawidłowe znaki to `a-z` , `0-9` i `-` ).

::: zone pivot="platform-windows"
Uruchom Poniższy kod, aby wdrożyć aplikację .NET Framework w systemie Windows.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup \
--parameters language=".net" helloWorld="true" webAppName="<app-name>" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-windows/azuredeploy.json"
::: zone-end
::: zone pivot="platform-linux"
Run the code below to create a Python app on Linux.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup --parameters webAppName="<app-name>" linuxFxVersion="PYTHON|3.7" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-linux/azuredeploy.json"
```

Aby wdrożyć inny stos języka, zaktualizuj `linuxFxVersion` odpowiednie wartości. Poniżej przedstawiono przykłady. Aby wyświetlić bieżące wersje, uruchom następujące polecenie w Cloud Shell: `az webapp config show --resource-group myResourceGroup --name <app-name> --query linuxFxVersion`

| Język    | Przykład                                              |
|-------------|------------------------------------------------------|
| **.NET**    | linuxFxVersion = "DOTNETCORE&#124;3,0"                 |
| **PHP**     | linuxFxVersion = "PHP&#124;7,4"                        |
| **Node.js** | linuxFxVersion = "NODE&#124;10,15"                     |
| **Java**    | linuxFxVersion = "JAVA&#124;1,8 &#124;TOMCAT&#124;9,0" |
| **Python**  | linuxFxVersion = "PYTHON&#124;3,7"                     |
| **Ruby**    | linuxFxVersion = "RUBY&#124;2,6"                       |

---
::: zone-end

> [!NOTE]
> Więcej [przykładów szablonów Azure App Service](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sites)można znaleźć tutaj.

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

Przejdź do `http://<app_name>.azurewebsites.net/` i sprawdź, czy został on utworzony.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli grupa zasobów nie będzie już konieczna, [Usuń ją](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wdrażanie z lokalnego wdrożenia narzędzia Git](deploy-local-git.md)

> [!div class="nextstepaction"]
> [ASP.NET Core with SQL Database (Platforma ASP.NET Core z usługą SQL Database)](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Język Python z bazą danych Postgres](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [PHP z bazą danych MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z usługą Azure SQL Database przy użyciu języka Java](../azure-sql/database/connect-query-java.md?toc=%252fazure%252fjava%252ftoc.json)

> [!div class="nextstepaction"]
> [Mapowanie domeny niestandardowej](app-service-web-tutorial-custom-domain.md)
