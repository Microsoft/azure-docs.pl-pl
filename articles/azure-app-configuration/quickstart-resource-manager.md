---
title: Tworzenie magazynu konfiguracji aplikacji platformy Azure przy użyciu szablonu Azure Resource Manager (szablon ARM)
titleSuffix: Azure App Configuration
description: Dowiedz się, jak utworzyć magazyn konfiguracji aplikacji platformy Azure przy użyciu szablonu Azure Resource Manager (szablon ARM).
author: ZhijunZhao
ms.author: zhijzhao
ms.date: 09/21/2020
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: 840f907015e9673caba46998493b5cb705de5fb7
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91824175"
---
# <a name="quickstart-create-an-azure-app-configuration-store-by-using-an-arm-template"></a>Szybki Start: Tworzenie magazynu konfiguracji aplikacji platformy Azure przy użyciu szablonu ARM

W tym przewodniku szybki start opisano, jak:

- Wdróż magazyn konfiguracji aplikacji przy użyciu szablonu usługi ARM
- Tworzenie wartości kluczy w magazynie konfiguracji aplikacji przy użyciu szablonu ARM
- Odczytywanie wartości klucza w magazynie konfiguracji aplikacji z szablonu ARM

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store-kv%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/en-us/resources/templates/101-app-configuration-store-kv/). Tworzy nowy magazyn konfiguracji aplikacji z dwoma kluczowymi wartościami wewnątrz. Następnie używa `reference` funkcji do wyprowadzania wartości dwóch zasobów klucz-wartość. Odczytanie wartości klucza w ten sposób umożliwia użycie jej w innych miejscach w szablonie.

Przewodnik Szybki Start używa `copy` elementu, aby utworzyć wiele wystąpień zasobu klucz-wartość. Aby dowiedzieć się więcej na temat `copy` elementu, zobacz [iteracja zasobów w szablonach ARM](../azure-resource-manager/templates/copy-resources.md).

> [!IMPORTANT]
> Ten szablon wymaga wersji dostawcy zasobów konfiguracji aplikacji `2020-07-01-preview` lub nowszej. Ta wersja używa `reference` funkcji do odczytywania wartości klucza. `listKeyValue`Funkcja, która została użyta do odczytu wartości kluczy w poprzedniej wersji, jest niedostępna w wersji `2020-07-01-preview` .

:::code language="json" source="~/quickstart-templates/101-app-configuration-store-kv/azuredeploy.json" range="1-88" highlight="52-58,61-75,80,84":::

Dwa zasoby platformy Azure są zdefiniowane w szablonie:

- [Microsoft. AppConfiguration/configurationStores](/azure/templates/microsoft.appconfiguration/2020-06-01/configurationstores): Tworzenie magazynu konfiguracji aplikacji.
- Microsoft. AppConfiguration/configurationStores/wartości kluczowe: Utwórz klucz-wartość w magazynie konfiguracji aplikacji.

> [!NOTE]
> `keyValues`Nazwa zasobu jest kombinacją klucza i etykiety. Klucz i etykieta są przyłączone przez `$` ogranicznik. Etykieta jest opcjonalna. W powyższym przykładzie `keyValues` zasób o nazwie `myKey` tworzy klucz-wartość bez etykiety.
>
> Kodowanie procentowe, znane także jako kodowanie adresów URL, umożliwia używanie klawiszy lub etykiet do dołączania znaków, które nie są dozwolone w nazwach zasobów szablonu ARM. `%` nie jest dozwolonym znakiem, więc `~` jest używany w jego miejscu. Aby poprawnie zakodować nazwę, wykonaj następujące kroki:
>
> 1. Zastosuj kodowanie adresu URL
> 2. Zamień `~` na `~7E`
> 3. Zamień `%` na `~`
>
> Na przykład, aby utworzyć parę klucz-wartość z nazwą klucza `AppName:DbEndpoint` i nazwą etykiety `Test` , nazwa zasobu powinna być `AppName~3ADbEndpoint$Test` .

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Wybierz poniższy obraz, aby zalogować się na platformie Azure i otworzyć szablon. Szablon tworzy magazyn konfiguracji aplikacji z dwoma kluczowymi wartościami wewnątrz.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store-kv%2Fazuredeploy.json)

Szablon można również wdrożyć przy użyciu następującego polecenia cmdlet programu PowerShell. Wartości klucza będą znajdować się w danych wyjściowych konsoli programu PowerShell.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-configuration-store-kv/azuredeploy.json"

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
1. W polu wyszukiwania Azure Portal wpisz **Konfiguracja aplikacji**. Z listy wybierz pozycję **Konfiguracja aplikacji** .
1. Wybierz nowo utworzony zasób konfiguracji aplikacji.
1. W obszarze **operacje**kliknij pozycję **Eksplorator konfiguracji.**
1. Sprawdź, czy istnieją dwa wartości kluczy.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, magazyn konfiguracji aplikacji i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. Jeśli planujesz używać sklepu z konfiguracją aplikacji w przyszłości, możesz pominąć jego usuwanie. Jeśli nie chcesz nadal korzystać z tego sklepu, Usuń wszystkie zasoby utworzone w tym przewodniku Szybki Start, uruchamiając następujące polecenie cmdlet:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o tworzeniu innych aplikacji za pomocą usługi Azure App Configuration, przejdź do następującego artykułu:

> [!div class="nextstepaction"]
> [Szybki Start: Tworzenie aplikacji ASP.NET Core przy użyciu konfiguracji aplikacji platformy Azure](quickstart-aspnet-core-app.md)