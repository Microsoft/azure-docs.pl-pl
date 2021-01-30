---
title: Utwórz i Włącz plan Azure DDoS Protection przy użyciu szablonu Azure Resource Manager (szablon ARM).
description: Dowiedz się, jak utworzyć i włączyć plan Azure DDoS Protection przy użyciu szablonu Azure Resource Manager (szablon ARM).
services: ddos-protection
documentationcenter: na
author: mumian
ms.service: ddos-protection
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.custom: subject-armqs
ms.author: jgao
ms.date: 01/14/2021
ms.openlocfilehash: 75d6c484a0f1d6325aaa7894d8902ff78cadbd74
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99092504"
---
# <a name="quickstart-create-an-azure-ddos-protection-standard-using-arm-template"></a>Szybki Start: Tworzenie standardu Azure DDoS Protection przy użyciu szablonu usługi ARM

W tym przewodniku szybki start opisano, jak używać szablonu Azure Resource Manager (szablon ARM) do tworzenia planu ochrony DDoS (Distributed odmowa usługi) i sieci wirtualnej (VNet). Plan Azure DDoS Protection Standard definiuje zbiór sieci wirtualnych, dla których włączono ochronę DDoS w różnych subskrypcjach. Można skonfigurować jeden plan ochrony DDoS dla Twojej organizacji i połączyć sieci wirtualne z wielu subskrypcji w ramach tego samego planu.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-create-and-enable-ddos-protection-plans%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-create-and-enable-ddos-protection-plans).

:::code language="json" source="~/quickstart-templates/101-create-and-enable-ddos-protection-plans/azuredeploy.json":::

Szablon definiuje dwa zasoby:

- [Microsoft. Network/ddosProtectionPlans](/azure/templates/microsoft.network/ddosprotectionplans)
- [Microsoft. Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)

## <a name="deploy-the-template"></a>Wdrożenie szablonu

W tym przykładzie szablon tworzy nową grupę zasobów, plan ochrony DDoS i sieć wirtualną.

1. Aby zalogować się do platformy Azure i otworzyć szablon, wybierz przycisk **Wdróż na platformie Azure** .

    [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-create-and-enable-ddos-protection-plans%2Fazuredeploy.json)

1. Wprowadź wartości, aby utworzyć nową grupę zasobów, plan ochrony DDoS i nazwę sieci wirtualnej.

    :::image type="content" source="media/manage-ddos-protection-template/ddos-template.png" alt-text="Szablon szybkiego startu DDoS.":::

    - **Subskrypcja**: Nazwa subskrypcji platformy Azure, w której zostaną wdrożone zasoby.
    - **Grupa zasobów**: wybierz istniejącą grupę zasobów lub Utwórz nową grupę zasobów.
    - **Region**: region, w którym wdrożono grupę zasobów, na przykład Wschodnie stany USA.
    - **Nazwa planu ochrony DDoS**: Nazwa nowego planu ochrony DDoS.
    - **Nazwa Virtual Network**: tworzy nazwę nowej sieci wirtualnej.
    - **Lokalizacja**: funkcja, która używa tego samego regionu co grupa zasobów na potrzeby wdrażania zasobów.
    - **Prefiks adresu sieci wirtualnej**: Użyj wartości domyślnej lub wprowadź adres sieci wirtualnej.
    - **Prefiks podsieci**: Użyj wartości domyślnej lub wprowadź podsieć sieci wirtualnej.
    - **Włączono plan ochrony DDoS**: wartość domyślna to `true` włączenie planu ochrony DDoS.

1. Wybierz pozycję **Przejrzyj i utwórz**.
1. Sprawdź, czy Walidacja szablonu zakończono pomyślnie, a następnie wybierz pozycję **Utwórz** , aby rozpocząć wdrażanie.

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

Aby skopiować polecenie interfejsu wiersza polecenia platformy Azure lub Azure PowerShell, wybierz przycisk **Kopiuj** . Przycisk **wypróbuj go** otwiera Azure Cloud Shell, aby uruchomić polecenie.

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

```azurecli-interactive
az network ddos-protection show \
  --resource-group MyResourceGroup \
  --name MyDdosProtectionPlan
```

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Get-AzDdosProtectionPlan -ResourceGroupName 'MyResourceGroup' -Name 'MyDdosProtectionPlan'
```

---

Dane wyjściowe pokazują nowe zasoby.

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

```Output
{
  "etag": "W/\"abcdefgh-1111-2222-bbbb-987654321098\"",
  "id": "/subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/ddosProtectionPlans/MyDdosProtectionPlan",
  "location": "eastus",
  "name": "MyDdosProtectionPlan",
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
  "resourceGuid": null,
  "tags": null,
  "type": "Microsoft.Network/ddosProtectionPlans",
  "virtualNetworks": [
    {
      "id": "/subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet",
      "resourceGroup": "MyResourceGroup"
    }
  ]
}
```

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

```Output
Name              : MyDdosProtectionPlan
Id                : /subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/ddosProtectionPlans/MyDdosProtectionPlan
Etag              : W/"abcdefgh-1111-2222-bbbb-987654321098"
ProvisioningState : Succeeded
VirtualNetworks   : [
                      {
                        "Id": "/subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet"
                      }
                    ]
```

---

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu możesz usunąć zasoby. Polecenie usuwa grupę zasobów i wszystkie zawarte w niej zasoby.

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

```azurecli-interactive
az group delete --name MyResourceGroup
```

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'MyResourceGroup'
```

---

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak wyświetlać i konfigurować dane telemetryczne dla planu ochrony DDoS, przejdź do samouczków.

> [!div class="nextstepaction"]
> [Wyświetlanie i konfigurowanie telemetrii ochrony przed atakami DDoS](telemetry.md)
