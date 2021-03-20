---
title: Błędy przydziału
description: Opisuje sposób rozwiązywania problemów z przydziałami zasobów podczas wdrażania zasobów przy użyciu Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.openlocfilehash: 75e8abf31d035a1e3a106bc0c6561624762db5d5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90530424"
---
# <a name="resolve-errors-for-resource-quotas"></a>Resolve errors for resource quotas (Rozwiązywanie błędów z limitami przydziałów zasobów)

W tym artykule opisano błędy przydziałów, które mogą wystąpić podczas wdrażania zasobów.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>Objaw

W przypadku wdrożenia szablonu, który tworzy zasoby przekraczające limity przydziału platformy Azure, zostanie wyświetlony błąd wdrażania, który wygląda następująco:

```
Code=OperationNotAllowed
Message=Operation results in exceeding quota limits of Core.
Maximum allowed: 4, Current in use: 4, Additional requested: 2.
```

Możesz też zobaczyć:

```
Code=ResourceQuotaExceeded
Message=Creating the resource of type <resource-type> would exceed the quota of <number>
resources of type <resource-type> per resource group. The current resource count is <number>,
please delete some resources of this type before creating a new one.
```

## <a name="cause"></a>Przyczyna

Limity przydziału są stosowane dla każdej grupy zasobów, subskrypcji, konta i innych zakresów. Na przykład subskrypcja może być skonfigurowana w taki sposób, aby ograniczyć liczbę rdzeni dla regionu. Jeśli spróbujesz wdrożyć maszynę wirtualną o liczbie rdzeni większej niż dozwolona, otrzymasz błąd informujący o przekroczeniu limitu przydziału.
Aby uzyskać pełne informacje o limicie przydziału, zobacz [limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](../../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby `az vm list-usage` znaleźć przydziały maszyny wirtualnej, dla interfejsu wiersza polecenia platformy Azure wykonaj polecenie.

```azurecli
az vm list-usage --location "South Central US"
```

Które zwraca:

```output
[
  {
    "currentValue": 0,
    "limit": 2000,
    "name": {
      "localizedValue": "Availability Sets",
      "value": "availabilitySets"
    }
  },
  ...
]
```

### <a name="powershell"></a>PowerShell

W przypadku programu PowerShell Użyj polecenia **Get-AzVMUsage** , aby znaleźć przydziały maszyny wirtualnej.

```powershell
Get-AzVMUsage -Location "South Central US"
```

Które zwraca:

```output
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional Cores                         0   100 Count
Virtual Machines                             0 10000 Count
```

## <a name="solution"></a>Rozwiązanie

Aby zażądać zwiększenia limitu przydziału, przejdź do portalu i zapoznaj się z tematem problem z pomocą techniczną. W obszarze problem z pomocą techniczną Poproś o zwiększenie limitu przydziału dla regionu, w którym ma zostać wdrożone.

> [!NOTE]
> Należy pamiętać, że w przypadku grup zasobów limit przydziału dotyczy każdego regionu, a nie całej subskrypcji. Jeśli musisz wdrożyć 30 rdzeni w regionie zachodnie stany USA, musisz zażądać 30 Menedżer zasobów rdzeni w regionie zachodnie stany USA. Jeśli musisz wdrożyć 30 rdzeni w dowolnym z regionów, do których masz dostęp, należy zażądać 30 Menedżer zasobów rdzeni we wszystkich regionach.
>
>

1. Wybierz pozycję **Subskrypcje**.

   ![Przechwytywanie ekranu pokazuje menu witryny Azure Portal z wybranymi subskrypcjami.](./media/error-resource-quota/subscriptions.png)

2. Wybierz subskrypcję, która wymaga zwiększenia limitu przydziału.

   ![Wybieranie subskrypcji](./media/error-resource-quota/select-subscription.png)

3. Wybierz **użycie i przydziały**

   ![Wybierz użycie i przydziały](./media/error-resource-quota/select-usage-quotas.png)

4. W prawym górnym rogu wybierz pozycję **Zwiększ liczbę żądań**.

   ![Zwiększ liczbę żądań](./media/error-resource-quota/request-increase.png)

5. Wypełnij formularze dotyczące typu przydziału, który chcesz zwiększyć.

   ![Wypełnij formularz](./media/error-resource-quota/forms.png)