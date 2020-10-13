---
title: Aktualizowanie zarządzanych zasobów
description: Opisuje sposób pracy z zasobami w zarządzanej grupie zasobów dla aplikacji zarządzanej platformy Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/26/2017
ms.author: tomfitz
ms.openlocfilehash: 3dbb88d53a961e277e102d7eec580309951c73b6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75651217"
---
# <a name="work-with-resources-in-the-managed-resource-group-for-azure-managed-application"></a>Pracuj z zasobami w zarządzanej grupie zasobów dla aplikacji zarządzanej platformy Azure

W tym artykule opisano sposób aktualizacji zasobów wdrożonych w ramach aplikacji zarządzanej. Jako Wydawca aplikacji zarządzanej masz dostęp do zasobów w zarządzanej grupie zasobów. Aby zaktualizować te zasoby, należy znaleźć zarządzaną grupę zasobów skojarzoną z zarządzaną aplikacją i uzyskać dostęp do zasobu w tej grupie zasobów.

W tym artykule przyjęto założenie, że aplikacja zarządzana została wdrożona w [zarządzanej aplikacji sieci Web (IaaS) za pomocą przykładowego projektu usług zarządzania systemu Azure](https://github.com/Azure/azure-managedapp-samples/tree/master/Managed%20Application%20Sample%20Packages/201-managed-web-app) . Ta aplikacja zarządzana zawiera **Standard_D1_v2** maszynę wirtualną. Jeśli aplikacja zarządzana nie została wdrożona, można nadal korzystać z tego artykułu, aby zapoznać się z procedurami aktualizowania zarządzanej grupy zasobów.

Na poniższej ilustracji przedstawiono wdrożoną aplikację zarządzaną.

![Wdrożona aplikacja zarządzana](./media/update-managed-resources/deployed.png)

W tym artykule opisano użycie interfejsu wiersza polecenia platformy Azure w celu:

* Identyfikowanie aplikacji zarządzanej
* Identyfikowanie zarządzanej grupy zasobów
* Identyfikowanie zasobów maszyny wirtualnej w zarządzanej grupie zasobów
* Zmiana rozmiaru maszyny wirtualnej (do mniejszej, jeśli nie jest używana lub większa do obsługi większej liczby obciążeń)
* Przypisywanie zasad do zarządzanej grupy zasobów, która określa dozwolone lokalizacje

## <a name="get-managed-application-and-managed-resource-group"></a>Pobierz zarządzaną aplikację i zarządzaną grupę zasobów

Aby uzyskać zarządzane aplikacje w grupie zasobów, użyj:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')]"
```

Aby uzyskać identyfikator zarządzanej grupy zasobów, użyj:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')].{ managedResourceGroup:managedResourceGroupId }"
```

## <a name="resize-vms-in-managed-resource-group"></a>Zmień rozmiar maszyn wirtualnych w zarządzanej grupie zasobów

Aby wyświetlić maszyny wirtualne w zarządzanej grupie zasobów, podaj nazwę zarządzanej grupy zasobów.

```azurecli-interactive
az vm list -g DemoApp6zkevchqk7sfq --query "[].{VMName:name,OSType:storageProfile.osDisk.osType,VMSize:hardwareProfile.vmSize}"
```

Aby zaktualizować rozmiar maszyn wirtualnych, użyj:

```azurecli-interactive
az vm resize --size Standard_D2_v2 --ids $(az vm list -g DemoApp6zkevchqk7sfq --query "[].id" -o tsv)
```

Po zakończeniu operacji Sprawdź, czy aplikacja jest uruchomiona na standardzie D2 v2.

![Zarządzana aplikacja przy użyciu standardowej D2 v2](./media/update-managed-resources/upgraded.png)

## <a name="apply-policy-to-managed-resource-group"></a>Zastosuj zasady do zarządzanej grupy zasobów

Uzyskaj zarządzaną grupę zasobów i przypisywanie zasad w tym zakresie. Zasady **e56962a6-4747-49cd-b67b-bf8b01975c4c** to wbudowane zasady służące do określania dozwolonych lokalizacji.

```azurecli-interactive
managedGroup=$(az managedapp show --name <app-name> --resource-group DemoApp --query managedResourceGroupId --output tsv)

az policy assignment create --name locationAssignment --policy e56962a6-4747-49cd-b67b-bf8b01975c4c --scope $managedGroup --params '{
                            "listofallowedLocations": {
                                "value": [
                                    "northeurope",
                                    "westeurope"
                                ]
                            }
                        }'
```

Aby wyświetlić dozwolone lokalizacje, użyj:

```azurecli-interactive
az policy assignment show --name locationAssignment --scope $managedGroup --query parameters.listofallowedLocations.value
```

Przypisanie zasad pojawia się w portalu.

![Wyświetl przypisanie zasad](./media/update-managed-resources/assignment.png)

## <a name="next-steps"></a>Następne kroki

* Zobacz artykuł [Omówienie aplikacji zarządzanych](overview.md) zawierający wprowadzenie do aplikacji zarządzanych.
* Aby uzyskać przykładowe projekty, zobacz [przykładowe projekty dla aplikacji zarządzanych przez platformę Azure](sample-projects.md).
