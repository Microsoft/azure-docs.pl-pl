---
title: Wycofywanie z powodu błędu do pomyślnego wdrożenia
description: Określ, że niepowodzenie wdrożenia powinno zostać przywrócone do pomyślnego wdrożenia.
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 742a8f16a2dce3204b48085759091540586a4522
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99492216"
---
# <a name="rollback-on-error-to-successful-deployment"></a>Wycofywanie z powodu błędu do pomyślnego wdrożenia

W przypadku niepowodzenia wdrożenia można automatycznie ponownie wdrożyć wcześniejsze, pomyślne wdrożenie z historii wdrożenia. Ta funkcja jest przydatna, jeśli masz znany dobry stan wdrożenia infrastruktury i chcesz przywrócić ten stan. Można określić konkretne wcześniejsze wdrożenie lub ostatnie pomyślne wdrożenie.

> [!IMPORTANT]
> Ta funkcja cofa nieudane wdrożenie przez ponowne wdrożenie wcześniejszego wdrożenia. Ten wynik może się różnić od tego, co będzie oczekiwać od cofnięcia wdrożenia zakończonego niepowodzeniem. Upewnij się, że rozumiesz, jak poprzednie wdrożenie jest ponownie wdrażane.

## <a name="considerations-for-redeploying"></a>Zagadnienia dotyczące ponownego wdrażania

Przed użyciem tej funkcji należy wziąć pod uwagę następujące szczegóły dotyczące sposobu obsługi ponownego wdrażania:

- Poprzednie wdrożenie jest uruchamiane przy użyciu [trybu kompletnego](./deployment-modes.md#complete-mode), nawet jeśli podczas wcześniejszego wdrożenia użyto [trybu przyrostowego](./deployment-modes.md#incremental-mode) . Ponowne wdrożenie w trybie kompletnym może spowodować nieoczekiwane wyniki w przypadku użycia przyrostowego starszego wdrożenia. Tryb kompletny oznacza, że wszystkie zasoby, które nie są uwzględnione w poprzednim wdrożeniu, są usuwane. Określ wcześniejsze wdrożenie reprezentujące wszystkie zasoby i ich Stany, które mają być obecne w grupie zasobów. Aby uzyskać więcej informacji, zobacz [tryby wdrażania](./deployment-modes.md).
- Ponowne wdrożenie jest uruchamiane dokładnie tak, jak zostało wcześniej uruchomione z tymi samymi parametrami. Nie można zmienić parametrów.
- Ponowne wdrożenie ma wpływ tylko na zasoby, a zmiany danych nie są modyfikowane.
- Tej funkcji można użyć tylko w przypadku wdrożeń grup zasobów. Nie obsługuje subskrypcji, grupy zarządzania ani wdrożeń na poziomie dzierżawy. Aby uzyskać więcej informacji na temat wdrażania na poziomie subskrypcji, zobacz [Tworzenie grup zasobów i zasobów na poziomie subskrypcji](./deploy-to-subscription.md).
- Tej opcji można używać tylko z wdrożeniami na poziomie głównym. Wdrożenia z zagnieżdżonego szablonu nie są dostępne do ponownego wdrożenia.

Aby można było użyć tej opcji, wdrożenia muszą mieć unikatowe nazwy w historii wdrażania. Jest to tylko unikatowe nazwy, które mogą być zidentyfikowane dla określonego wdrożenia. Jeśli nie masz unikatowych nazw, Niepowodzenie wdrożenia może zastąpić pomyślne wdrożenie w historii.

Jeśli określisz wcześniejsze wdrożenie, które nie istnieje w historii wdrożenia, wycofywanie zwróci błąd.

## <a name="powershell"></a>PowerShell

Aby ponownie wdrożyć ostatnie pomyślne wdrożenie, Dodaj `-RollbackToLastDeployment` parametr jako flagę.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollbackToLastDeployment
```

Aby ponownie wdrożyć określone wdrożenie, użyj `-RollBackDeploymentName` parametru i podaj nazwę wdrożenia. Określone wdrożenie musi się powieść.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollBackDeploymentName ExampleDeployment01
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby ponownie wdrożyć ostatnie pomyślne wdrożenie, Dodaj `--rollback-on-error` parametr jako flagę.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

Aby ponownie wdrożyć określone wdrożenie, użyj `--rollback-on-error` parametru i podaj nazwę wdrożenia. Określone wdrożenie musi się powieść.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

## <a name="rest-api"></a>Interfejs API REST

Aby ponownie wdrożyć ostatnie pomyślne wdrożenie, jeśli bieżące wdrożenie zakończy się niepowodzeniem, użyj:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "LastSuccessful",
    }
  }
}
```

Aby ponownie wdrożyć określone wdrożenie, jeśli bieżące wdrożenie nie powiedzie się, użyj:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "SpecificDeployment",
      "deploymentName": "<deploymentname>"
    }
  }
}
```

Określone wdrożenie musi się powieść.

## <a name="next-steps"></a>Następne kroki

- Aby zrozumieć tryb pełny i przyrostowy, zobacz [Azure Resource Manager trybami wdrożenia](deployment-modes.md).
- Aby zrozumieć, jak definiować parametry w szablonie, zobacz [Opis struktury i składni szablonów Azure Resource Manager](template-syntax.md).
