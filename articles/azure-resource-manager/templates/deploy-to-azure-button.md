---
title: Przycisk Wdróż na platformie Azure
description: Użyj przycisku, aby wdrożyć szablony Azure Resource Manager z repozytorium GitHub.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: e403d095f7419ed13742dcfa8e8407a92ad10be0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82744094"
---
# <a name="use-a-deployment-button-to-deploy-templates-from-github-repository"></a>Użyj przycisku wdrożenia, aby wdrożyć szablony z repozytorium GitHub

W tym artykule opisano sposób wdrażania szablonów z repozytorium GitHub za pomocą przycisku **Wdróż na platformie Azure** . Możesz dodać przycisk bezpośrednio do pliku README.md w repozytorium GitHub lub do strony sieci Web, która odwołuje się do repozytorium. Ta metoda obsługuje tylko wdrożenie na poziomie grupy zasobów.

## <a name="use-common-image"></a>Użyj wspólnego obrazu

Aby dodać przycisk do strony sieci Web lub repozytorium, użyj następującego obrazu:

```html
<img src="https://aka.ms/deploytoazurebutton"/>
```

Obraz jest wyświetlany jako:

![Przycisk Wdróż na platformie Azure](https://aka.ms/deploytoazurebutton)

## <a name="create-url-for-deploying-template"></a>Utwórz adres URL do wdrożenia szablonu

Aby utworzyć adres URL dla szablonu, Zacznij od pierwotnego adresu URL do szablonu w repozytorium:

```html
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Następnie należy zakodować ten adres URL. Możesz użyć kodera online lub uruchomić polecenie. Poniższy przykład programu PowerShell przedstawia sposób kodowania wartości przy użyciu adresu URL.

```powershell
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

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Aby przetestować pełne rozwiązanie, wybierz następujący przycisk:

[![Wdrażanie na platformie Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)

W portalu zostanie wyświetlone okienko pozwalające łatwo podawać wartości parametrów. Parametry są wstępnie wypełnione wartościami domyślnymi z szablonu.

![Używanie portalu do wdrażania](./media/deploy-to-azure-button/portal.png)

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat szablonów, zobacz [Opis struktury i składni szablonów Azure Resource Manager](template-syntax.md).
