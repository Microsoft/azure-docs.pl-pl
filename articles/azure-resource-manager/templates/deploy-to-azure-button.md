---
title: Przycisk Wdróż na platformie Azure
description: Użyj przycisku, aby wdrożyć szablony Azure Resource Manager z repozytorium GitHub.
ms.topic: conceptual
ms.date: 11/10/2020
ms.openlocfilehash: 65891cace1cb17614abbfe091e1592d6f13feff4
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185729"
---
# <a name="use-a-deployment-button-to-deploy-templates-from-github-repository"></a>Użyj przycisku wdrożenia, aby wdrożyć szablony z repozytorium GitHub

W tym artykule opisano sposób wdrażania szablonów z repozytorium GitHub za pomocą przycisku **Wdróż na platformie Azure** . Możesz dodać przycisk bezpośrednio do pliku README.md w repozytorium GitHub. Można też dodać przycisk do strony sieci Web, która odwołuje się do repozytorium.

Zakres wdrożenia jest określany przez schemat szablonu. Aby uzyskać więcej informacji, zobacz:

* [grupy zasobów](deploy-to-resource-group.md)
* [opłaty](deploy-to-subscription.md)
* [grupy zarządzania](deploy-to-management-group.md)
* [dzierżaw](deploy-to-tenant.md)

## <a name="use-common-image"></a>Użyj wspólnego obrazu

Aby dodać przycisk do strony sieci Web lub repozytorium, użyj następującego obrazu:

```markdown
![Deploy to Azure](https://aka.ms/deploytoazurebutton)
```

```html
<img src="https://aka.ms/deploytoazurebutton"/>
```

Obraz jest wyświetlany jako:

![Przycisk Wdróż na platformie Azure](https://aka.ms/deploytoazurebutton)

## <a name="create-url-for-deploying-template"></a>Utwórz adres URL do wdrożenia szablonu

Aby utworzyć adres URL dla szablonu, Zacznij od pierwotnego adresu URL do szablonu w repozytorium. Aby wyświetlić nieprzetworzony adres URL, wybierz opcję **RAW**.

:::image type="content" source="./media/deploy-to-azure-button/select-raw.png" alt-text="Wybierz surowce":::

Format adresu URL:

```html
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Następnie przekonwertuj adres URL na wartość zakodowaną w adresie URL. Możesz użyć kodera online lub uruchomić polecenie. Poniższy przykład programu PowerShell przedstawia sposób kodowania wartości przy użyciu adresu URL.

```powershell
$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
[uri]::EscapeDataString($url)
```

Przykładowy adres URL ma następującą wartość w przypadku kodowania adresu URL.

```html
https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

Każde łącze rozpoczyna się od tego samego podstawowego adresu URL:

```html
https://portal.azure.com/#create/Microsoft.Template/uri/
```

Dodaj link szablonu szyfrowanego przez adres URL do końca podstawowego adresu URL.

```html
https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

Masz pełny adres URL linku.

Zwykle hostuje szablon w publicznym repozytorium. Jeśli używasz repozytorium prywatnego, musisz dołączyć token, aby uzyskać dostęp do nieprzetworzonej zawartości szablonu. Token wygenerowany przez witrynę GitHub jest ważny tylko przez krótki czas. Należy często aktualizować link.

Jeśli korzystasz [z narzędzia Git z Azure Repos](/azure/devops/repos/git/) zamiast repozytorium GitHub, możesz nadal korzystać z przycisku Wdróż na platformie Azure. Upewnij się, że Twoje repozytorium jest publiczne. Użyj [operacji Items](/rest/api/azure/devops/git/items/get) , aby pobrać szablon. Twoje żądanie powinno mieć następujący format:

```http
https://dev.azure.com/{organization-name}/{project-name}/_apis/git/repositories/{repository-name}/items?scopePath={url-encoded-path}&api-version=6.0
```

Koduj ten adres URL żądania.

## <a name="create-deploy-to-azure-button"></a>Przycisk Utwórz wdrożenie na platformie Azure

Na koniec Umieść link i obraz razem.

Aby dodać przycisk z opcją inpromocji w pliku README.md w repozytorium GitHub lub stronie sieci Web, użyj:

```markdown
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)
```

W przypadku języka HTML należy użyć:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json" target="_blank">
  <img src="https://aka.ms/deploytoazurebutton"/>
</a>
```

W przypadku usługi git z repozytorium Azure przycisk ma format:

```markdown
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fdev.azure.com%2Forgname%2Fprojectname%2F_apis%2Fgit%2Frepositories%2Freponame%2Fitems%3FscopePath%3D%2freponame%2fazuredeploy.json%26api-version%3D6.0)
```

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Aby przetestować pełne rozwiązanie, wybierz następujący przycisk:

[![Wdrażanie na platformie Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)

W portalu zostanie wyświetlone okienko pozwalające łatwo podawać wartości parametrów. Parametry są wstępnie wypełnione wartościami domyślnymi z szablonu.

![Używanie portalu do wdrażania](./media/deploy-to-azure-button/portal.png)

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat szablonów, zobacz [Opis struktury i składni szablonów Azure Resource Manager](template-syntax.md).
