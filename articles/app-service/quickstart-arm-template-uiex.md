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
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 7437a5208f94b435576b8a38dc65a6e798303a72
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179089"
---
# <a name="quickstart-create-app-service-app-using-an-arm-template"></a>Szybki Start: Tworzenie aplikacji App Service przy użyciu szablonu ARM

Rozpocznij pracę z usługą [Azure App Service](overview.md) , wdrażając aplikację w chmurze przy użyciu <abbr title="Plik JSON, który deklaratywnie definiuje co najmniej jeden zasób platformy Azure i zależności między wdrożonymi zasobami. Szablon może służyć do spójnego i wielokrotnego wdrażania zasobów.">Szablon ARM</abbr> i [interfejs wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli) w Cloud Shell. Ponieważ korzystasz z bezpłatnej warstwy App Service, nie poniesiesz żadnych kosztów, aby ukończyć ten przewodnik Szybki Start. <abbr title="W składni deklaratywnej opisujesz rozwiązanie, które chcesz wdrożyć, bez konieczności pisania sekwencji poleceń programistycznych służących do tworzenia takiego wdrożenia.">W szablonie używana jest składnia deklaratywna.</abbr>

 Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z [szablonów usługi ARM](../azure-resource-manager/templates/overview.md), wybierz przycisk **Wdróż na platformie Azure** . Szablon zostanie otwarty w witrynie Azure Portal.

::: zone pivot="platform-windows"
[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-service-docs-windows%2Fazuredeploy.json)
::: zone-end

::: zone pivot="platform-linux"
[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-service-docs-linux%2Fazuredeploy.json)
::: zone-end

<hr/>

## <a name="1-prepare-your-environment"></a>1. Przygotuj środowisko

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<hr/>

## <a name="2-review-the-template"></a>2. Przejrzyj szablon

::: zone pivot="platform-windows"
Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-app-service-docs-windows). Wdraża Plan App Service i aplikację App Service w systemie Windows.

:::code language="json" source="~/quickstart-templates/101-app-service-docs-windows/azuredeploy.json":::

<details>
<summary>Jakie zasoby i parametry są zdefiniowane w szablonie?</summary>

Dwa zasoby platformy Azure są zdefiniowane w szablonie:

* [**Microsoft. Web/dopuszczalna**](/azure/templates/microsoft.web/serverfarms): tworzenie planu App Service.
* [**Microsoft. Web/Sites**](/azure/templates/microsoft.web/sites): tworzenie aplikacji App Service.

Następujące szczegóły tabeli zawierają parametry domyślne i ich opisy:

| Parametry | Typ    | Wartość domyślna                | Opis |
|------------|---------|------------------------------|-------------|
| webAppName | ciąg  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | Nazwa aplikacji |
| location   | ciąg  | "[[resourceName (). Location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | Region aplikacji |
| sku        | ciąg  | Nacionięcie                         | Rozmiar wystąpienia (F1 = warstwa Bezpłatna) |
| language   | ciąg  | architektury                       | Stos języka programowania (.NET, php, Node, html) |
| helloWorld | boolean | Fałsz                        | True = Wdróż aplikację "Hello world" |
| repoUrl    | ciąg  | " "                          | Zewnętrzne repozytorium git (opcjonalnie) |

---

</details>
::: zone-end
::: zone pivot="platform-linux"
Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-app-service-docs-linux). Wdraża Plan App Service i aplikację App Service w systemie Windows.

:::code language="json" source="~/quickstart-templates/101-app-service-docs-linux/azuredeploy.json":::

Ten szablon zawiera zasoby i parametry platformy Azure, które są zdefiniowane dla wygody użytkownika.

<details>
<summary>Jakie zasoby i parametry są zdefiniowane w szablonie?</summary>

Dwa zasoby platformy Azure są zdefiniowane w szablonie:

* [**Microsoft. Web/dopuszczalna**](/azure/templates/microsoft.web/serverfarms): tworzenie planu App Service.
* [**Microsoft. Web/Sites**](/azure/templates/microsoft.web/sites): tworzenie aplikacji App Service.

Następujące szczegóły tabeli zawierają parametry domyślne i ich opisy:

| Parametry | Typ    | Wartość domyślna                | Opis |
|------------|---------|------------------------------|-------------|
| webAppName | ciąg  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | Nazwa aplikacji |
| location   | ciąg  | "[[resourceName (). Location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | Region aplikacji |
| sku        | ciąg  | Nacionięcie                         | Rozmiar wystąpienia (F1 = warstwa Bezpłatna) |
| linuxFxVersion   | ciąg  | "DOTNETCORE&#124;3,0        | "Stos języka programowania &#124; wersja" |
| repoUrl    | ciąg  | " "                          | Zewnętrzne repozytorium git (opcjonalnie) |

---

</details>

::: zone-end

<hr/>

## <a name="3-deploy-the-template"></a>3. Wdróż szablon

::: zone pivot="platform-windows"
Uruchom Poniższy kod, aby wdrożyć aplikację .NET Framework w systemie Windows przy użyciu interfejsu wiersza polecenia platformy Azure. 

Zamień <abbr title="Prawidłowe znaki to `a-z` , `0-9` i `-` .">`<app-name>`</abbr> ze globalnie unikatową nazwą aplikacji. Aby poznać inne <abbr title="Można również użyć interfejsu API Azure Portal, Azure PowerShell i REST.">metody wdrażania</abbr>, zobacz [wdrażanie szablonów](../azure-resource-manager/templates/deploy-powershell.md). Więcej [przykładów szablonów Azure App Service](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sites)można znaleźć tutaj.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup \
--parameters language=".net" helloWorld="true" webAppName="<app-name>" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-windows/azuredeploy.json"
```
::: zone-end
::: zone pivot="platform-linux"
Uruchom Poniższy kod, aby utworzyć aplikację w języku Python w systemie Linux. 

Zamień <abbr title="Prawidłowe znaki to `a-z` , `0-9` i `-` .">`<app-name>`</abbr> ze globalnie unikatową nazwą aplikacji.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup --parameters webAppName="<app-name>" linuxFxVersion="PYTHON|3.7" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-linux/azuredeploy.json"
```
::: zone-end

<details>
<summary>Co to jest kod?</summary>
<p>Polecenia wykonują następujące czynności:</p>
<ul>
<li>Tworzenie domyślnego <abbr title="Logiczny kontener dla powiązanych zasobów platformy Azure, którymi można zarządzać jako jednostką.">grupa zasobów</abbr>.</li>
<li>Tworzenie domyślnego <abbr title="Plan określający lokalizację, rozmiar i funkcje farmy serwerów sieci Web, która hostuje aplikację.">Plan usługi App Service</abbr>.</li>
<li><a href="/cli/azure/webapp#az-webapp-create">Utwórz <abbr title="Reprezentacja aplikacji sieci Web, która zawiera kod aplikacji, nazwy hostów DNS, certyfikaty i powiązane zasoby. "> Aplikacja App Service</abbr></a> o określonej nazwie.</li>
</ul>
</details>

::: zone pivot="platform-windows"
<details>
<summary>Jak mogę wdrożyć inny stos języka?</summary>
Aby wdrożyć inny stos języka, zaktualizuj <abbr title="Ten szablon jest zgodny z aplikacjami .NET Core, .NET Framework, PHP, Node.js i static HTML. "> parametr języka</abbr> z odpowiednimi wartościami. W przypadku języka Java zobacz <a href="/azure/app-service/quickstart-java-uiex">Tworzenie aplikacji Java</a>.

| Parametry | Typ    | Wartość domyślna                | Opis |
|------------|---------|------------------------------|-------------|
| language   | ciąg  | architektury                       | Stos języka programowania (.NET, php, Node, html) |

---

</details>
::: zone-end
::: zone pivot="platform-linux"
<details>
<summary>Jak mogę wdrożyć inny stos języka?</summary>
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

</details>
::: zone-end

<hr/>

## <a name="4-validate-the-deployment"></a>4. Sprawdź poprawność wdrożenia

Przejdź do `http://<app_name>.azurewebsites.net/` i sprawdź, czy został on utworzony.

<hr/>

## <a name="5-clean-up-resources"></a>5. Wyczyść zasoby

Jeśli grupa zasobów nie będzie już konieczna, [Usuń ją](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group).

<hr/>

## <a name="next-steps"></a>Następne kroki

- [Wdrażanie z lokalnego wdrożenia narzędzia Git](deploy-local-git.md)
- [ASP.NET Core with SQL Database (Platforma ASP.NET Core z usługą SQL Database)](tutorial-dotnetcore-sqldb-app.md)
- [Język Python z bazą danych Postgres](tutorial-python-postgresql-app.md)
- [PHP z bazą danych MySQL](tutorial-php-mysql-app.md)
- [Nawiązywanie połączenia z usługą Azure SQL Database przy użyciu języka Java](../azure-sql/database/connect-query-java.md?toc=%2fazure%2fjava%2ftoc.json)
- [Mapowanie domeny niestandardowej](app-service-web-tutorial-custom-domain-uiex.md)

