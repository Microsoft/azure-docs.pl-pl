---
title: Tryby wdrażania
description: Opisuje, w jaki sposób należy określić, czy ma być używany pełny czy przyrostowy tryb wdrażania z Azure Resource Manager.
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: 45eee255cec06925095ed0696c669b5c205f8b56
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97724412"
---
# <a name="azure-resource-manager-deployment-modes"></a>Tryby wdrażania usługi Azure Resource Manager

Podczas wdrażania zasobów należy określić, że wdrożenie jest aktualizacją przyrostową, albo pełną aktualizacją. Różnica między tymi dwoma trybami polega na tym, że Menedżer zasobów obsługuje istniejące zasoby w grupie zasobów, która nie znajduje się w szablonie.

W przypadku obu trybów Menedżer zasobów próbuje utworzyć wszystkie zasoby określone w szablonie. Jeśli zasób już istnieje w grupie zasobów, a jego ustawienia nie są zmieniane, nie jest wykonywana żadna operacja dla tego zasobu. W przypadku zmiany wartości właściwości zasobu zasób zostanie zaktualizowany o te nowe wartości. Jeśli spróbujesz zaktualizować lokalizację lub typ istniejącego zasobu, wdrożenie nie powiedzie się z powodu błędu. Zamiast tego należy wdrożyć nowy zasób z wymaganą lokalizacją lub typem.

Domyślnym trybem jest przyrostowy.

## <a name="complete-mode"></a>Tryb kompletny

W trybie kompletnym Menedżer zasobów **usuwa** zasoby, które istnieją w grupie zasobów, ale nie są określone w szablonie.

> [!NOTE]
> Zawsze używaj [operacji działania warunkowego](template-deploy-what-if.md) przed wdrożeniem szablonu w trybie kompletnym. Co oznacza, które zasoby zostaną utworzone, usunięte lub zmodyfikowane. Użyj co do tego, aby uniknąć przypadkowego usunięcia zasobów.

Jeśli szablon zawiera zasób, który nie został wdrożony, ponieważ [warunek](conditional-resource-deployment.md) ma wartość false, wynik zależy od używanej wersji interfejsu API REST do wdrożenia szablonu. W przypadku używania wersji wcześniejszej niż 2019-05-10 zasób nie zostanie **usunięty**. W przypadku 2019-05-10 lub nowszych zasób **jest usuwany**. Najnowsze wersje Azure PowerShell i interfejsu wiersza polecenia platformy Azure usuwają zasób.

Należy zachować ostrożność przy użyciu trybu kompletnego z [pętlami kopiowania](copy-resources.md). Wszystkie zasoby, które nie są określone w szablonie po rozwiązaniu pętli kopiowania, są usuwane.

W przypadku wdrożenia w [więcej niż jednej grupie zasobów w szablonie](./deploy-to-resource-group.md)zasoby w grupie zasobów określonej w operacji wdrażania mogą zostać usunięte. Zasoby w dodatkowych grupach zasobów nie są usuwane.

Istnieją pewne różnice w sposobie, w jaki typy zasobów obsługują operacje usuwania w trybie pełnym. Zasoby nadrzędne są automatycznie usuwane, gdy nie znajdują się w szablonie, który został wdrożony w trybie kompletnym. Niektóre zasoby podrzędne nie są automatycznie usuwane, gdy nie znajdują się w szablonie. Jednak te zasoby podrzędne zostaną usunięte, jeśli zasób nadrzędny zostanie usunięty.

Na przykład, jeśli grupa zasobów zawiera strefę DNS ( `Microsoft.Network/dnsZones` Typ zasobu) i rekord CNAME ( `Microsoft.Network/dnsZones/CNAME` Typ zasobu), strefa DNS jest zasobem nadrzędnym rekordu CNAME. W przypadku wdrożenia z trybem kompletnym i nie dołączania strefy DNS do szablonu strefa DNS i rekord CNAME są usuwane. Jeśli w szablonie zostanie uwzględniona strefa DNS, ale nie zostanie uwzględniony rekord CNAME, rekord CNAME nie zostanie usunięty.

Aby uzyskać listę sposobu, w jaki typy zasobów obsługują usuwanie, zobacz [usuwanie zasobów platformy Azure na potrzeby wdrożeń w trybie pełnym](complete-mode-deletion.md).

Jeśli grupa zasobów jest [zablokowana](../management/lock-resources.md), tryb kompletny nie usuwa zasobów.

> [!NOTE]
> Tylko szablony na poziomie głównym obsługują pełny tryb wdrażania. W przypadku [szablonów połączonych lub zagnieżdżonych](linked-templates.md)należy użyć trybu przyrostowego.
>
> [Wdrożenia na poziomie subskrypcji](deploy-to-subscription.md) nie obsługują trybu pełnego.
>
> Obecnie portal nie obsługuje trybu pełnego.
>

## <a name="incremental-mode"></a>Tryb przyrostowy

W trybie przyrostowym Menedżer zasobów **opuszcza niezmienione** zasoby, które istnieją w grupie zasobów, ale nie są określone w szablonie. Zasoby w szablonie **są dodawane** do grupy zasobów.

> [!NOTE]
> Po ponownym wdrożeniu istniejącego zasobu w trybie przyrostowym zostaną ponownie zastosowane wszystkie właściwości. **Właściwości nie są dodawane przyrostowo**. Typowy nieporozumienia polega na tym, że właściwości, które nie są określone w szablonie, pozostaną bez zmian. Jeśli nie określisz pewnych właściwości, Menedżer zasobów interpretuje wdrożenie, zastępując te wartości. Właściwości, które nie znajdują się w szablonie, są resetowane do wartości domyślnych. Określ wszystkie wartości inne niż domyślne dla zasobu, a nie tylko te, które są aktualizowane. Definicja zasobu w szablonie zawsze zawiera końcowy stan zasobu. Nie może reprezentować częściowej aktualizacji istniejącego zasobu.
>
> W rzadkich przypadkach właściwości określone dla zasobu są faktycznie implementowane jako zasób podrzędny. Na przykład w przypadku podania wartości konfiguracyjnych lokacji dla aplikacji sieci Web te wartości są implementowane w podrzędnym typie zasobu `Microsoft.Web/sites/config` . Jeśli ponownie wdrożono aplikację sieci Web i określisz pusty obiekt dla wartości konfiguracji lokacji, zasób podrzędny nie zostanie zaktualizowany. Jeśli jednak podano nowe wartości konfiguracji lokacji, zostanie zaktualizowany podrzędny typ zasobu.

## <a name="example-result"></a>Przykładowy wynik

Aby zilustrować różnicę między trybami przyrostowym i kompletnym, należy wziąć pod uwagę Poniższy scenariusz.

**Grupa zasobów** zawiera:

* Zasób A
* Zasób B
* Zasób C

**Szablon** zawiera:

* Zasób A
* Zasób B
* Zasób D

Po wdrożeniu w trybie **przyrostowym** Grupa zasobów zawiera:

* Zasób A
* Zasób B
* Zasób C
* Zasób D

Po wdrożeniu w trybie **kompletnym** zasób C jest usuwany. Grupa zasobów zawiera:

* Zasób A
* Zasób B
* Zasób D

## <a name="set-deployment-mode"></a>Ustawianie trybu wdrożenia

Aby ustawić tryb wdrażania podczas wdrażania przy użyciu programu PowerShell, należy użyć `Mode` parametru.

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Mode Complete `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json
```

Aby ustawić tryb wdrażania podczas wdrażania przy użyciu interfejsu wiersza polecenia platformy Azure, użyj `mode` parametru.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --mode Complete \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

Poniższy przykład pokazuje połączony szablon z trybem wdrożenia przyrostowego:

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2017-05-10",
    "name": "linkedTemplate",
    "properties": {
      "mode": "Incremental",
          <nested-template-or-external-template>
    }
  }
]
```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat tworzenia szablonów Menedżer zasobów, zobacz [Opis struktury i składni szablonów ARM](template-syntax.md).
* Aby dowiedzieć się więcej o wdrażaniu zasobów, zobacz [wdrażanie zasobów przy użyciu szablonów ARM i Azure PowerShell](deploy-powershell.md).
* Aby wyświetlić operacje dla dostawcy zasobów, zobacz [interfejs API REST platformy Azure](/rest/api/).
