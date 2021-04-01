---
title: 'Szybki Start: nowe przypisanie zasad przy użyciu interfejsu API REST'
description: W tym przewodniku szybki start użyjesz interfejsu API REST do utworzenia przypisania Azure Policy, aby zidentyfikować niezgodne zasoby.
ms.date: 01/29/2021
ms.topic: quickstart
ms.openlocfilehash: 438d8004cd50e6e2ef7586c51adc63257f37978b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99219981"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-rest-api"></a>Szybki Start: Tworzenie przypisania zasad w celu zidentyfikowania niezgodnych zasobów przy użyciu interfejsu API REST

Pierwszym krokiem do zrozumienia pojęcia zgodności na platformie Azure jest określenie obecnej sytuacji dotyczącej Twoich zasobów.
Ten przewodnik Szybki start przeprowadzi Cię przez proces tworzenia przypisania zasad w celu zidentyfikowania maszyn wirtualnych, które nie korzystają z dysków zarządzanych.

Po zakończeniu tego procesu pomyślnie zidentyfikujesz maszyny wirtualne, które nie korzystają z dysków zarządzanych. Są one _niezgodne_ z przypisaniem zasad.

Interfejs API REST służy do tworzenia zasobów platformy Azure i zarządzania nimi. Ten przewodnik używa interfejsu API REST w celu utworzenia przypisania zasad i zidentyfikowania niezgodnych zasobów w środowisku platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

- Jeśli jeszcze tego nie zrobiono, zainstaluj [ARMClient](https://github.com/projectkudu/ARMClient). Jest to narzędzie, które wysyła żądania HTTP do interfejsów API REST opartych na Azure Resource Manager. Możesz również użyć funkcji "Wypróbuj ją" w dokumentacji REST lub narzędzi, takich jak [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) lub [post](https://www.postman.com)programu PowerShell.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-policy-assignment"></a>Tworzenie przypisania zasad

W tym przewodniku szybki start utworzysz przypisanie zasad i przypiszesz **maszyny wirtualne inspekcji, które nie używają definicji Managed disks** ( `06a78e20-9358-41c9-923c-fb736d382a4d` ). Ta definicja zasad identyfikuje zasoby, które nie spełniają warunków określonych w definicji zasad.

Uruchom następujące polecenie, aby utworzyć przypisanie zasad:

   - Identyfikator URI interfejsu API REST

     ```http
     PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/audit-vm-manageddisks?api-version=2019-09-01
     ```

   - Treść żądania

     ```json
     {
       "properties": {
         "displayName": "Audit VMs without managed disks Assignment",
         "description": "Shows all virtual machines not using managed disks",
         "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d",
         "nonComplianceMessages": [
             {
                 "message": "Virtual machines should use a managed disk"
             }
         ]
       }
     }
     ```

Poprzedni punkt końcowy i treść żądania korzystają z następujących informacji:

IDENTYFIKATOR URI INTERFEJSU API REST:
- **Zakres** — zakres określa, jakie zasoby lub grupy zasobów są wymuszane w ramach przypisania zasad. Może ona przedziały od grupy zarządzania do pojedynczego zasobu. Pamiętaj, aby zastąpić `{scope}` jednym z następujących wzorców:
  - Grupa zarządzania: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - Subskrypcje: `/subscriptions/{subscriptionId}`
  - Grupa zasobów: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - Zasoby `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]{resourceType}/{resourceName}`
- **Nazwa** —rzeczywista nazwa przypisania. W tym przykładzie użyto nazwy _audit-vm-manageddisks_.

Treść żądania:
- **DisplayName** — nazwa wyświetlana przypisania zasad. W takim przypadku używasz _maszyn wirtualnych inspekcji bez przypisania dysków zarządzanych_.
- **Opis** — dokładniejsze wyjaśnienie tego, czym są zasady lub dlaczego są przypisane do tego zakresu.
- **policyDefinitionId** — identyfikator definicji zasad, na podstawie której tworzysz przypisanie. W takim przypadku jest IDENTYFIKATORem _maszyn wirtualnych inspekcji definicji zasad, które nie korzystają z dysków zarządzanych_.
- **nonComplianceMessages** — ustawia komunikat wyświetlany, gdy odmówiono zasobu z powodu niezgodności lub oceniono, że nie jest on zgodny. Aby uzyskać więcej informacji, zobacz temat [przypisywanie komunikatów niezgodnych ze zgodnością](./concepts/assignment-structure.md#non-compliance-messages).

## <a name="identify-non-compliant-resources"></a>Identyfikowanie niezgodnych zasobów

Aby wyświetlić zasoby, które nie są zgodne z tym nowym przypisaniem, uruchom następujące polecenie, aby pobrać identyfikatory zasobów niezgodnych zasobów, które są wyprowadzane do pliku JSON:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$filter=IsCompliant eq false and PolicyAssignmentId eq 'audit-vm-manageddisks'&$apply=groupby((ResourceId))"
```

Wyniki powinny wyglądać podobnie do następujących:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 3,
    "value": [{
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachineId>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine2Id>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3Id>"
        }

    ]
}
```

Wyniki są porównywalne z zawartością zwykle wyświetlaną w obszarze **Niezgodne zasoby** w widoku witryny Azure Portal.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć utworzone przypisanie, wykonaj następujące polecenie:

```http
DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/audit-vm-manageddisks?api-version=2019-09-01
```

Zamień na `{scope}` zakres użyty podczas pierwszego utworzenia przypisania zasad.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start została przypisana definicja zasad mających na celu zidentyfikowanie niezgodnych zasobów w środowisku platformy Azure.

Aby dowiedzieć się więcej na temat przypisywania zasad w celu sprawdzenia zgodności nowych zasobów, przejdź do samouczka:

> [!div class="nextstepaction"]
> [Tworzenie zasad i zarządzanie nimi](./tutorials/create-and-manage.md)
