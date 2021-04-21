---
title: Tworzenie aplikacji App Service przy użyciu szablonu Azure Resource Manager szablonu
description: Utwórz swoją pierwszą aplikację do Azure App Service w ciągu kilku sekund przy użyciu szablonu usługi Azure Resource Manager (arm), który jest jednym z wielu sposobów wdrażania w App Service.
author: msangapu-msft
ms.author: msangapu
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 10/16/2020
ms.custom: subject-armqs, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: bce6bfb61eb59d1fa66c550a133ac8b6f8d7f2c5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769009"
---
# <a name="quickstart-create-app-service-app-using-an-arm-template"></a>Szybki start: tworzenie App Service przy użyciu szablonu usługi ARM

Rozpoczynanie pracy [z Azure App Service](overview.md) wdrażaniem aplikacji w chmurze przy użyciu <abbr title="Plik JSON, który deklaratywnie definiuje jeden lub więcej zasobów platformy Azure i zależności między wdrożonymi zasobami. Szablon może służyć do spójnego i wielokrotnego wdrażania zasobów.">Szablon ARM</abbr> i [interfejs wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli) w Cloud Shell. Ponieważ korzystasz z bezpłatnej App Service, nie ponosisz żadnych kosztów, aby ukończyć ten przewodnik Szybki start. <abbr title="W składni deklaratywnej opisujesz rozwiązanie, które chcesz wdrożyć, bez konieczności pisania sekwencji poleceń programistycznych służących do tworzenia takiego wdrożenia.">W szablonie używana jest składnia deklaratywna.</abbr>

 Jeśli środowisko spełnia wymagania wstępne i znasz szablony [usługi ARM,](../azure-resource-manager/templates/overview.md)wybierz przycisk **Wdeń na platformie Azure.** Szablon zostanie otwarty w witrynie Azure Portal.

::: zone pivot="platform-windows"
[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-service-docs-windows%2Fazuredeploy.json)
::: zone-end

::: zone pivot="platform-linux"
[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-service-docs-linux%2Fazuredeploy.json)
::: zone-end

<hr/>

## <a name="1-prepare-your-environment"></a>1. Przygotowywanie środowiska

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<hr/>

## <a name="2-review-the-template"></a>2. Przeglądanie szablonu

::: zone pivot="platform-windows"
Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-app-service-docs-windows). Wdraża ona plan App Service i aplikację App Service w systemie Windows.

:::code language="json" source="~/quickstart-templates/101-app-service-docs-windows/azuredeploy.json":::

<details>
<summary>Jakie zasoby i parametry są zdefiniowane w szablonie?</summary>

W szablonie zdefiniowano dwa zasoby platformy Azure:

* [**Microsoft.Web/serverfarms:**](/azure/templates/microsoft.web/serverfarms)utwórz App Service planu.
* [**Microsoft.Web/sites:**](/azure/templates/microsoft.web/sites)tworzenie App Service aplikacji.

W poniższej tabeli przedstawiono domyślne parametry i ich opisy:

| Parametry | Typ    | Wartość domyślna                | Opis |
|------------|---------|------------------------------|-------------|
| WebAppName | ciąg  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | Nazwa aplikacji |
| location   | ciąg  | "[[resourceGroup().location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | Region aplikacji |
| sku        | ciąg  | "F1"                         | Rozmiar wystąpienia (F1 = warstwa Bezpłatna) |
| language   | ciąg  | ".net"                       | Stos języka programowania (.net, php, node, html) |
| Helloworld | boolean | Fałsz                        | True = Wdrażanie aplikacji "Hello world" |
| repoUrl    | ciąg  | " "                          | Zewnętrzne repozytorium Git (opcjonalnie) |

---

</details>
::: zone-end
::: zone pivot="platform-linux"
Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-app-service-docs-linux). Wdraża ona plan App Service i aplikację App Service w systemie Windows.

:::code language="json" source="~/quickstart-templates/101-app-service-docs-linux/azuredeploy.json":::

Ten szablon zawiera zasoby i parametry platformy Azure, które są zdefiniowane dla Twojej wygody.

<details>
<summary>Jakie zasoby i parametry są zdefiniowane w szablonie?</summary>

W szablonie zdefiniowano dwa zasoby platformy Azure:

* [**Microsoft.Web/serverfarms:**](/azure/templates/microsoft.web/serverfarms)utwórz App Service planu.
* [**Microsoft.Web/sites:**](/azure/templates/microsoft.web/sites)utwórz App Service aplikacji.

W poniższej tabeli przedstawiono domyślne parametry i ich opisy:

| Parametry | Typ    | Wartość domyślna                | Opis |
|------------|---------|------------------------------|-------------|
| webAppName | ciąg  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | Nazwa aplikacji |
| location   | ciąg  | "[[resourceGroup().location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | Region aplikacji |
| sku        | ciąg  | "F1"                         | Rozmiar wystąpienia (F1 = warstwa Bezpłatna) |
| linuxFxVersion   | ciąg  | "DOTNETCORE&#124;3.0        | "Stos języka programowania &#124; wersji" |
| repoUrl    | ciąg  | " "                          | Zewnętrzne repozytorium Git (opcjonalnie) |

---

</details>

::: zone-end

<hr/>

## <a name="3-deploy-the-template"></a>3. Wdrażanie szablonu

::: zone pivot="platform-windows"
Uruchom poniższy kod, aby wdrożyć aplikację .NET Framework w systemie Windows przy użyciu interfejsu wiersza polecenia platformy Azure. 

Zamień <abbr title="Prawidłowe znaki to `a-z` , `0-9` i `-` .">`<app-name>`</abbr> na globalnie unikatową nazwę aplikacji. Aby poznać inne <abbr title="Możesz również użyć interfejsu AZURE PORTAL, Azure PowerShell i interfejsu API REST.">metody wdrażania</abbr>, zobacz [Wdrażanie szablonów](../azure-resource-manager/templates/deploy-powershell.md). Więcej przykładów szablonów [Azure App Service znaleźć tutaj.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sites)

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup \
--parameters language=".net" helloWorld="true" webAppName="<app-name>" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-windows/azuredeploy.json"
```
::: zone-end
::: zone pivot="platform-linux"
Uruchom poniższy kod, aby utworzyć aplikację w języku Python w systemie Linux. 

Zamień <abbr title="Prawidłowe znaki to `a-z` , `0-9` i `-` .">`<app-name>`</abbr> na globalnie unikatową nazwę aplikacji.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup --parameters webAppName="<app-name>" linuxFxVersion="PYTHON|3.7" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-linux/azuredeploy.json"
```
::: zone-end

<details>
<summary>Co robi kod?</summary>
<p>Polecenia te wykonaj następujące czynności:</p>
<ul>
<li>Tworzenie wartości domyślnej <abbr title="Kontener logiczny dla powiązanych zasobów platformy Azure, które można zarządzać jako jednostką.">grupa zasobów</abbr>.</li>
<li>Tworzenie wartości domyślnej <abbr title="Plan określający lokalizację, rozmiar i funkcje farmy serwerów sieci Web, która hostuje aplikację.">Plan usługi App Service</abbr>.</li>
<li><a href="/cli/azure/webapp#az_webapp_create">Tworzenie <abbr title="Reprezentacja aplikacji internetowej, która zawiera kod aplikacji, nazwy hostów DNS, certyfikaty i powiązane zasoby. "> App Service aplikacji</abbr></a> o określonej nazwie.</li>
</ul>
</details>

::: zone pivot="platform-windows"
<details>
<summary>Jak mogę wdrożyć inny stos języka?</summary>
Aby wdrożyć inny stos języka, zaktualizuj <abbr title="Ten szablon jest zgodny z aplikacjami .NET Core, .NET Framework, PHP, Node.js i statycznych aplikacji HTML. "> parametr language</abbr> z odpowiednimi wartościami. W przypadku języka Java zobacz <a href="/azure/app-service/quickstart-java-uiex">Tworzenie aplikacji Java</a>.

| Parametry | Typ    | Wartość domyślna                | Opis |
|------------|---------|------------------------------|-------------|
| language   | ciąg  | ".net"                       | Stos języka programowania (.net, php, node, html) |

---

</details>
::: zone-end
::: zone pivot="platform-linux"
<details>
<summary>Jak mogę wdrożyć inny stos języka?</summary>
Aby wdrożyć inny stos języka, zaktualizuj `linuxFxVersion` go przy użyciu odpowiednich wartości. Poniżej przedstawiono przykłady. Aby wyświetlić bieżące wersje, uruchom następujące polecenie w Cloud Shell: `az webapp config show --resource-group myResourceGroup --name <app-name> --query linuxFxVersion`

| Język    | Przykład                                              |
|-------------|------------------------------------------------------|
| **.NET**    | linuxFxVersion="DOTNETCORE&#124;3.0"                 |
| **PHP**     | linuxFxVersion="PHP&#124;7.4"                        |
| **Node.js** | linuxFxVersion="NODE&#124;10.15"                     |
| **Java**    | linuxFxVersion="JAVA&#124;1.8 &#124;TOMCAT&#124;9.0" |
| **Python**  | linuxFxVersion="PYTHON&#124;3.7"                     |
| **Ruby**    | linuxFxVersion="RUBY&#124;2.6"                       |

---

</details>
::: zone-end

<hr/>

## <a name="4-validate-the-deployment"></a>4. Weryfikowanie wdrożenia

Przejdź do `http://<app_name>.azurewebsites.net/` strony i sprawdź, czy została ona utworzona.

<hr/>

## <a name="5-clean-up-resources"></a>5. Czyszczenie zasobów

Gdy grupa zasobów nie [będzie już potrzebna, usuń ją.](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group)

<hr/>

## <a name="next-steps"></a>Następne kroki

- [Wdrażanie z lokalnego wdrożenia narzędzia Git](deploy-local-git.md)
- [ASP.NET Core with SQL Database (Platforma ASP.NET Core z usługą SQL Database)](tutorial-dotnetcore-sqldb-app.md)
- [Język Python z bazą danych Postgres](tutorial-python-postgresql-app.md)
- [PHP z bazą danych MySQL](tutorial-php-mysql-app.md)
- [Nawiązywanie połączenia z Azure SQL danych przy użyciu języka Java](../azure-sql/database/connect-query-java.md?toc=%2fazure%2fjava%2ftoc.json)
- [Mapowanie domeny niestandardowej](app-service-web-tutorial-custom-domain-uiex.md)
