---
title: Tworzenie magazynu konfiguracji aplikacji platformy Azure przy użyciu szablonu Azure Resource Manager (szablon ARM)
titleSuffix: Azure App Configuration
description: Dowiedz się, jak utworzyć magazyn konfiguracji aplikacji platformy Azure przy użyciu szablonu Azure Resource Manager (szablon ARM).
author: ZhijunZhao
ms.author: zhijzhao
ms.date: 10/16/2020
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: c35b6d27a5e6d7ffd2a9f76ea201af4a138a77df
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99219115"
---
# <a name="quickstart-create-an-azure-app-configuration-store-by-using-an-arm-template"></a>Szybki Start: Tworzenie magazynu konfiguracji aplikacji platformy Azure przy użyciu szablonu ARM

W tym przewodniku szybki start opisano, jak:

- Wdróż magazyn konfiguracji aplikacji przy użyciu szablonu Azure Resource Manager (szablon ARM).
- Utwórz wartości kluczy w magazynie konfiguracji aplikacji przy użyciu szablonu ARM.
- Odczytaj wartości klucza w magazynie konfiguracji aplikacji z szablonu ARM.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store-kv%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-app-configuration-store-kv/). Tworzy nowy magazyn konfiguracji aplikacji z dwoma kluczowymi wartościami wewnątrz. Następnie używa `reference` funkcji do wyprowadzania wartości dwóch zasobów klucz-wartość. Odczytanie wartości klucza w ten sposób umożliwia użycie jej w innych miejscach w szablonie.

Przewodnik Szybki Start używa `copy` elementu, aby utworzyć wiele wystąpień zasobu klucz-wartość. Aby dowiedzieć się więcej na temat `copy` elementu, zobacz [iteracja zasobów w szablonach ARM](../azure-resource-manager/templates/copy-resources.md).

> [!IMPORTANT]
> Ten szablon wymaga wersji dostawcy zasobów konfiguracji aplikacji `2020-07-01-preview` lub nowszej. Ta wersja używa `reference` funkcji do odczytywania wartości klucza. `listKeyValue`Funkcja, która została użyta do odczytu wartości kluczy w poprzedniej wersji, jest niedostępna w wersji `2020-07-01-preview` .

:::code language="json" source="~/quickstart-templates/101-app-configuration-store-kv/azuredeploy.json":::

Dwa zasoby platformy Azure są zdefiniowane w szablonie:

- [Microsoft. AppConfiguration/configurationStores](/azure/templates/microsoft.appconfiguration/2020-07-01-preview/configurationstores): Tworzenie magazynu konfiguracji aplikacji.
- [Microsoft. AppConfiguration/configurationStores/wartości](/azure/templates/microsoft.appconfiguration/2020-07-01-preview/configurationstores/keyvalues)kluczowe: Utwórz klucz-wartość w magazynie konfiguracji aplikacji.

> [!TIP]
> `keyValues`Nazwa zasobu jest kombinacją klucza i etykiety. Klucz i etykieta są przyłączone przez `$` ogranicznik. Etykieta jest opcjonalna. W powyższym przykładzie `keyValues` zasób o nazwie `myKey` tworzy klucz-wartość bez etykiety.
>
> Kodowanie procentowe, znane także jako kodowanie adresów URL, umożliwia używanie klawiszy lub etykiet do dołączania znaków, które nie są dozwolone w nazwach zasobów szablonu ARM. `%` nie jest dozwolonym znakiem, więc `~` jest używany w jego miejscu. Aby poprawnie zakodować nazwę, wykonaj następujące kroki:
>
> 1. Zastosuj kodowanie adresu URL
> 2. Zamień `~` na `~7E`
> 3. Zamień `%` na `~`
>
> Na przykład, aby utworzyć parę klucz-wartość z nazwą klucza `AppName:DbEndpoint` i nazwą etykiety `Test` , nazwa zasobu powinna być `AppName~3ADbEndpoint$Test` .

> [!NOTE]
> Konfiguracja aplikacji umożliwia dostęp do danych klucza za pośrednictwem [prywatnego linku](concept-private-endpoint.md) do sieci wirtualnej. Domyślnie po włączeniu tej funkcji wszystkie żądania dotyczące danych konfiguracyjnych aplikacji za pośrednictwem sieci publicznej są odrzucane. Ponieważ szablon ARM jest uruchamiany poza siecią wirtualną, dostęp do danych z szablonu ARM nie jest dozwolony. Aby zezwolić na dostęp do danych z szablonu usługi ARM w przypadku użycia linku prywatnego, można włączyć dostęp do sieci publicznej za pomocą następującego polecenia interfejsu CLI platformy Azure. Należy wziąć pod uwagę implikacje zabezpieczeń dotyczące włączania dostępu do sieci publicznej w tym scenariuszu.
>
> ```azurecli-interactive
> az appconfig update -g MyResourceGroup -n MyAppConfiguration --enable-public-network true
> ```

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

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W polu wyszukiwania Azure Portal wpisz **Konfiguracja aplikacji**. Z listy wybierz pozycję **Konfiguracja aplikacji** .
1. Wybierz nowo utworzony zasób konfiguracji aplikacji.
1. W obszarze **operacje** kliknij pozycję **Eksplorator konfiguracji**.
1. Sprawdź, czy istnieją dwa wartości kluczy.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, magazyn konfiguracji aplikacji i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. Jeśli planujesz używać sklepu z konfiguracją aplikacji w przyszłości, możesz pominąć jego usuwanie. Jeśli nie chcesz nadal korzystać z tego sklepu, Usuń wszystkie zasoby utworzone w tym przewodniku Szybki Start, uruchamiając następujące polecenie cmdlet:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o dodawaniu flagi funkcji i Key Vault odwołaniu do magazynu konfiguracji aplikacji, zaznacz poniżej przykłady szablonów ARM.

- [101 — aplikacja-konfiguracja — magazyn — ZZ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-app-configuration-store-ff)
- [101-App-Configuration-Store-keyvaultref](https://github.com/Azure/azure-quickstart-templates/tree/master/101-app-configuration-store-keyvaultref)