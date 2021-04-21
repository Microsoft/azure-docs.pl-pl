---
title: Tworzenie Azure App Configuration magazynu przy użyciu Azure Resource Manager szablonu (szablonu usługi ARM)
titleSuffix: Azure App Configuration
description: Dowiedz się, jak utworzyć Azure App Configuration magazynu przy użyciu Azure Resource Manager szablonu (szablonu usługi ARM).
author: GrantMeStrength
ms.author: jken
ms.date: 10/16/2020
ms.service: azure-app-configuration
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurepowershell
ms.openlocfilehash: 92ca80a6c807394c45be8f0187c7add736ba83ce
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831784"
---
# <a name="quickstart-create-an-azure-app-configuration-store-by-using-an-arm-template"></a>Szybki start: tworzenie magazynu Azure App Configuration przy użyciu szablonu usługi ARM

W tym przewodniku Szybki start opisano, jak:

- Wdrażanie App Configuration magazynu przy użyciu szablonu Azure Resource Manager (szablonu usługi ARM).
- Tworzenie kluczy-wartości w magazynie App Configuration przy użyciu szablonu usługi ARM.
- Odczytywanie kluczy i wartości w magazynie App Configuration z szablonu usługi ARM.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store-kv%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-app-configuration-store-kv/). Tworzy nowy magazyn App Configuration z dwoma kluczami-wartościami. Następnie używa funkcji `reference` , aby wyprowadzać wartości dwóch zasobów klucz-wartość. Odczytywanie wartości klucza w ten sposób umożliwia jej korzystanie z innych miejsc w szablonie.

W tym przewodniku Szybki `copy` start element jest używany do tworzenia wielu wystąpień zasobu klucz-wartość. Aby dowiedzieć się więcej na temat `copy` elementu , zobacz [Iteracja zasobów w szablonach arm.](../azure-resource-manager/templates/copy-resources.md)

> [!IMPORTANT]
> Ten szablon wymaga App Configuration dostawcy zasobów `2020-07-01-preview` lub nowszej. Ta wersja używa `reference` funkcji do odczytywania wartości klucz-wartość. Funkcja, `listKeyValue` która była używana do odczytywania wartości klucz-wartość w poprzedniej wersji, nie jest dostępna, począwszy od wersji `2020-07-01-preview` .

:::code language="json" source="~/quickstart-templates/101-app-configuration-store-kv/azuredeploy.json":::

W szablonie zdefiniowano dwa zasoby platformy Azure:

- [Microsoft.AppConfiguration/configurationStores:](/azure/templates/microsoft.appconfiguration/2020-07-01-preview/configurationstores)utwórz App Configuration magazynu.
- [Microsoft.AppConfiguration/configurationStores/keyValues:](/azure/templates/microsoft.appconfiguration/2020-07-01-preview/configurationstores/keyvalues)utwórz klucz-wartość wewnątrz App Configuration magazynu.

> [!TIP]
> Nazwa `keyValues` zasobu jest kombinacją klucza i etykiety. Klucz i etykieta są połączone przez `$` ogranicznik. Etykieta jest opcjonalna. W powyższym przykładzie `keyValues` zasób o nazwie `myKey` tworzy klucz-wartość bez etykiety.
>
> Kodowanie procentowe, znane również jako kodowanie adresów URL, umożliwia kluczom lub etykietom dołączanie znaków, które nie są dozwolone w nazwach zasobów szablonu usługi ARM. `%` nie jest też dozwolonym znakiem, więc `~` jest używany w jego miejscu. Aby poprawnie zakodować nazwę, wykonaj następujące kroki:
>
> 1. Stosowanie kodowania adresu URL
> 2. `~`Zamień na`~7E`
> 3. `%`Zamień na`~`
>
> Aby na przykład utworzyć parę klucz-wartość z nazwą klucza i nazwą `AppName:DbEndpoint` `Test` etykiety, nazwa zasobu powinna mieć wartość `AppName~3ADbEndpoint$Test` .

> [!NOTE]
> App Configuration umożliwia dostęp do danych typu klucz-wartość za pośrednictwem [łącza prywatnego](concept-private-endpoint.md) z sieci wirtualnej. Domyślnie, gdy funkcja jest włączona, wszystkie żądania dotyczące danych App Configuration przez sieć publiczną są odmowa. Ponieważ szablon usługi ARM działa poza siecią wirtualną, dostęp do danych z szablonu usługi ARM jest niedozwolone. Aby zezwolić na dostęp do danych z szablonu usługi ARM, gdy jest używane łącze prywatne, możesz włączyć dostęp do sieci publicznej za pomocą następującego polecenia interfejsu wiersza polecenia platformy Azure. W tym scenariuszu należy wziąć pod uwagę implikacje dotyczące bezpieczeństwa dotyczące włączania dostępu do sieci publicznej.
>
> ```azurecli-interactive
> az appconfig update -g MyResourceGroup -n MyAppConfiguration --enable-public-network true
> ```

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Wybierz poniższy obraz, aby zalogować się na platformie Azure i otworzyć szablon. Szablon tworzy magazyn App Configuration z dwoma kluczami-wartościami.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store-kv%2Fazuredeploy.json)

Szablon można również wdrożyć przy użyciu następującego polecenia cmdlet programu PowerShell. Klucz-wartość będzie w danych wyjściowych konsoli programu PowerShell.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-configuration-store-kv/azuredeploy.json"

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="review-deployed-resources"></a>Przeglądanie wdrożonych zasobów

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W polu Azure Portal wyszukiwania wpisz **App Configuration**. Wybierz **App Configuration** z listy.
1. Wybierz nowo utworzony zasób App Configuration zasobów.
1. W **obszarze Operacje** kliknij pozycję Eksplorator **konfiguracji**.
1. Sprawdź, czy istnieją dwie wartości klucza.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, magazyn zasobów i wszystkie pokrewne zasoby nie App Configuration już potrzebne, usuń je. Jeśli planujesz używać magazynu App Configuration w przyszłości, możesz pominąć jego usuwanie. Jeśli nie zamierzasz nadal korzystać z tego magazynu, usuń wszystkie zasoby utworzone w tym przewodniku Szybki start, uruchamiając następujące polecenie cmdlet:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat dodawania flagi funkcji i Key Vault do magazynu App Configuration, zapoznaj się z poniższymi przykładami szablonów usługi ARM.

- [101-app-configuration-store-ff](https://github.com/Azure/azure-quickstart-templates/tree/master/101-app-configuration-store-ff)
- [101-app-configuration-store-keyvaultref](https://github.com/Azure/azure-quickstart-templates/tree/master/101-app-configuration-store-keyvaultref)
