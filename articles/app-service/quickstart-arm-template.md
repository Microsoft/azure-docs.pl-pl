---
title: Tworzenie aplikacji App Service przy użyciu szablonu Azure Resource Manager
description: Utwórz pierwszą aplikację do Azure App Service w ciągu kilku sekund przy użyciu szablonu Azure Resource Manager, który jest jednym z wielu sposobów wdrożenia w App Service.
author: msangapu-msft
ms.author: msangapu
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 05/25/2020
ms.custom: subject-armqs
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: e577616e0976ca050a55c8524e68129545ed1912
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "89653582"
---
# <a name="create-app-service-app-using-an-azure-resource-manager-template"></a>Tworzenie aplikacji App Service przy użyciu szablonu Azure Resource Manager

Rozpocznij pracę z [Azure App Service](overview.md) , wdrażając aplikację w chmurze przy użyciu szablonu Azure Resource Manager i [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) w Cloud Shell. Ponieważ korzystasz z bezpłatnej warstwy App Service, nie poniesiesz żadnych kosztów, aby ukończyć ten przewodnik Szybki Start.

 [!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-azure-app-service-app"></a>Tworzenie aplikacji Azure App Service

### <a name="review-the-template"></a>Przegląd szablonu

::: zone pivot="platform-windows"
Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://github.com/Azure/azure-quickstart-templates/). Wdraża Plan App Service i aplikację App Service w systemie Windows. Jest on zgodny z platformą .NET Core, .NET Framework, PHP, Node.js i statycznymi aplikacjami HTML. W przypadku języka Java zobacz [Tworzenie aplikacji Java](app-service-web-get-started-java.md). 

[!code-json[<Azure Resource Manager template App Service Windows app>](~/quickstart-templates/101-app-service-docs-windows/azuredeploy.json)]

Dwa zasoby platformy Azure są zdefiniowane w szablonie:

* [**Microsoft. Web/dopuszczalna**](/azure/templates/microsoft.web/serverfarms): tworzenie planu App Service.
* [**Microsoft. Web/Sites**](/azure/templates/microsoft.web/sites): tworzenie aplikacji App Service.

Ten szablon zawiera kilka parametrów, które są wstępnie zdefiniowane dla wygody użytkownika. Zapoznaj się z tabelą poniżej, aby poznać wartości domyślne parametrów i ich opisy:

| Parametry | Typ    | Wartość domyślna                | Opis |
|------------|---------|------------------------------|-------------|
| webAppName | ciąg  | "webApp- **[`<uniqueString>`](/azure/azure-resource-manager/templates/template-functions-string#uniquestring)** " | Nazwa aplikacji |
| location   | ciąg  | "[[resourceName (). Location](/azure/azure-resource-manager/templates/template-functions-resource#resourcegroup)]" | Region aplikacji |
| sku        | ciąg  | Nacionięcie                         | Rozmiar wystąpienia (F1 = warstwa Bezpłatna) |
| language   | ciąg  | architektury                       | Stos języka programowania (.NET, php, Node, html) |
| helloWorld | boolean | Fałsz                        | True = Wdróż aplikację "Hello world" |
| repoUrl    | ciąg  | " "                          | Zewnętrzne repozytorium git (opcjonalnie) |
::: zone-end
::: zone pivot="platform-linux"
Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://github.com/Azure/azure-quickstart-templates/). Wdraża Plan App Service i aplikację App Service w systemie Linux. Są one zgodne ze wszystkimi obsługiwanymi językami programowania w App Service.

[!code-json[<Azure Resource Manager template App Service Linux app>](~/quickstart-templates/101-app-service-docs-linux/azuredeploy.json)]

Dwa zasoby platformy Azure są zdefiniowane w szablonie:

* [**Microsoft. Web/dopuszczalna**](/azure/templates/microsoft.web/serverfarms): tworzenie planu App Service.
* [**Microsoft. Web/Sites**](/azure/templates/microsoft.web/sites): tworzenie aplikacji App Service.

Ten szablon zawiera kilka parametrów, które są wstępnie zdefiniowane dla wygody użytkownika. Zapoznaj się z tabelą poniżej, aby poznać wartości domyślne parametrów i ich opisy:

| Parametry | Typ    | Wartość domyślna                | Opis |
|------------|---------|------------------------------|-------------|
| webAppName | ciąg  | "webApp- **[`<uniqueString>`](/azure/azure-resource-manager/templates/template-functions-string#uniquestring)** " | Nazwa aplikacji |
| location   | ciąg  | "[[resourceName (). Location](/azure/azure-resource-manager/templates/template-functions-resource#resourcegroup)]" | Region aplikacji |
| sku        | ciąg  | Nacionięcie                         | Rozmiar wystąpienia (F1 = warstwa Bezpłatna) |
| linuxFxVersion   | ciąg  | "DOTNETCORE&#124;3,0        | "Stos języka programowania &#124; wersja" |
| repoUrl    | ciąg  | " "                          | Zewnętrzne repozytorium git (opcjonalnie) |

---
::: zone-end


### <a name="deploy-the-template"></a>Wdrożenie szablonu

Interfejs wiersza polecenia platformy Azure jest używany tutaj do wdrożenia szablonu. Można również użyć interfejsu API Azure Portal, Azure PowerShell i REST. Aby poznać inne metody wdrażania, zobacz [wdrażanie szablonów](../azure-resource-manager/templates/deploy-powershell.md). 

Poniższy kod tworzy grupę zasobów, plan App Service i aplikację sieci Web. Ustawiono domyślną grupę zasobów, plan App Service i lokalizację. Zamień na `<app-name>` globalnie unikatową nazwę aplikacji (prawidłowe znaki to `a-z` , `0-9` i `-` ).

::: zone pivot="platform-windows"
Uruchom Poniższy kod, aby wdrożyć aplikację .NET Framework w systemie Windows.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup \
--parameters language=".net" sample="true" webAppName="<app-name>" \
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
> [Wdrażanie z lokalnego wdrożenia narzędzia Git](deploy-local-git.md)

> [!div class="nextstepaction"]
> [ASP.NET Core with SQL Database (Platforma ASP.NET Core z usługą SQL Database)](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Język Python z bazą danych Postgres](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [PHP z bazą danych MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z usługą Azure SQL Database przy użyciu języka Java](/azure/sql-database/sql-database-connect-query-java?toc=%2Fazure%2Fjava%2Ftoc.json)

> [!div class="nextstepaction"]
> [Mapowanie domeny niestandardowej](app-service-web-tutorial-custom-domain.md)