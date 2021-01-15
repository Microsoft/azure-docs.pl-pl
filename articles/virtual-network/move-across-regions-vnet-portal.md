---
title: Przenoszenie sieci wirtualnej platformy Azure do innego regionu platformy Azure przy użyciu Azure Portal.
description: Przenoszenie sieci wirtualnej platformy Azure z jednego regionu platformy Azure do innego przy użyciu szablonu Menedżer zasobów i Azure Portal.
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: 0a80c731e4245b1a295364e5b8c87f90290f7f74
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98223312"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-the-azure-portal"></a>Przenoszenie sieci wirtualnej platformy Azure do innego regionu przy użyciu Azure Portal

Istnieją różne scenariusze dotyczące przechodzenia istniejącej sieci wirtualnej platformy Azure z jednego regionu do innego. Na przykład możesz chcieć utworzyć sieć wirtualną o tej samej konfiguracji do testowania i dostępności jako istniejącą sieć wirtualną. Możesz też chcieć przenieść produkcyjną sieć wirtualną do innego regionu w ramach planowania odzyskiwania po awarii.

Możesz użyć szablonu Azure Resource Manager, aby zakończyć przenoszenie sieci wirtualnej do innego regionu. W tym celu należy wyeksportować sieć wirtualną do szablonu, modyfikując parametry w celu dopasowania do regionu docelowego, a następnie wdrożyć szablon w nowym regionie. Aby uzyskać więcej informacji na temat szablonów Menedżer zasobów, zobacz [Szybki Start: Tworzenie i wdrażanie szablonów Azure Resource Manager przy użyciu Azure Portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).


## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że sieć wirtualna znajduje się w regionie świadczenia usługi Azure, z którego chcesz przejść.

- Aby wyeksportować sieć wirtualną i wdrożyć szablon w celu utworzenia sieci wirtualnej w innym regionie, musisz mieć rolę współautor sieci lub wyższą.

- Komunikacja równorzędna sieci wirtualnych nie zostanie utworzona i nie powiedzie się, jeśli nadal znajdują się one w szablonie. Przed wyeksportowaniem szablonu należy usunąć wszystkie równorzędne sieci wirtualne. Następnie można je ponownie ustanowić po przeniesieniu sieci wirtualnej.

- Zidentyfikuj układ sieci źródłowej i wszystkie aktualnie używane zasoby. Ten układ obejmuje, ale nie jest ograniczony do modułów równoważenia obciążenia, sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) i publicznych adresów IP.

- Sprawdź, czy subskrypcja platformy Azure umożliwia tworzenie sieci wirtualnych w regionie docelowym. Aby włączyć wymagany limit przydziału, skontaktuj się z pomocą techniczną.

- Upewnij się, że Twoja subskrypcja ma wystarczającą ilość zasobów, aby obsłużyć Dodawanie sieci wirtualnych dla tego procesu. Aby uzyskać więcej informacji, zobacz [Azure subscription and service limits, quotas, and constraints (Limity, przydziały i ograniczenia usług i subskrypcji platformy Azure)](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).


## <a name="prepare-for-the-move"></a>Przygotuj do przeniesienia
Ta sekcja umożliwia przygotowanie sieci wirtualnej do przenoszenia przy użyciu szablonu Menedżer zasobów. Następnie można przenieść sieć wirtualną do regionu docelowego przy użyciu Azure Portal.

Aby wyeksportować sieć wirtualną i wdrożyć docelową sieć wirtualną przy użyciu Azure Portal, wykonaj następujące czynności:

1. Zaloguj się do [Azure Portal](https://portal.azure.com), a następnie wybierz pozycję **grupy zasobów**.
1. Znajdź grupę zasobów zawierającą źródłową sieć wirtualną, a następnie wybierz ją.
1. Wybierz kolejno pozycje **Ustawienia**  >  **Eksportuj szablon**.
1. W okienku **Eksportuj szablon** wybierz pozycję **Wdróż**.
1. Aby otworzyć *parameters.js* w pliku w edytorze online, wybierz pozycję **szablon**  >  **Edytuj parametry**.
1. Aby edytować parametr nazwy sieci wirtualnej, Zmień właściwość **Value** w obszarze **Parametry**:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "virtualNetworks_myVNET1_name": {
                "value": "<target-virtual-network-name>"
            }
        }
    }
    ```

1. W edytorze Zmień wartość Nazwa źródłowej sieci wirtualnej w edytorze na nazwę, która ma być docelowa dla docelowej sieci wirtualnej. Upewnij się, że nazwa jest ujęta w cudzysłów.

1. Wybierz pozycję **Zapisz** w edytorze.

1. Aby otworzyć *template.js* w pliku w edytorze online, wybierz pozycję **szablon**  >  **Edytuj szablon**.

1. Aby edytować region docelowy, w którym zostanie przeniesiona Sieć wirtualna, w edytorze online Zmień właściwość **Location** w obszarze **zasoby**:

    ```json
    "resources": [
                {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region>",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                        "addressSpace": {
                            "addressPrefixes": [
                                "10.0.0.0/16"
                            ]
                        },

    ```

1. Aby uzyskać kody lokalizacji regionu, zobacz [lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/locations/). Kod regionu to nazwa regionu, bez spacji (na przykład **środkowe stany USA**  =  ).

1. Obowiązkowe Możesz również zmienić inne parametry w szablonie, w zależności od wymagań:

    * **Przestrzeń adresowa**: przed zapisaniem pliku można zmienić przestrzeń adresową sieci wirtualnej, modyfikując   >  sekcję **addressSpace** zasobów i zmieniając właściwość **addressPrefixes** :

        ```json
                "resources": [
                    {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                    "addressSpace": {
                        "addressPrefixes": [
                        "10.0.0.0/16"
                        ]
                    },

        ```

    * **Podsieć**: można zmienić lub dodać nazwę podsieci i przestrzeń adresową podsieci, zmieniając sekcję **podsieci** szablonu. Nazwę podsieci można zmienić, zmieniając właściwość **name** . I można zmienić przestrzeń adresową podsieci, zmieniając właściwość **addressPrefix** :

        ```json
                "subnets": [
                    {
                    "name": "subnet-1",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.0.0/24",
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    },
                    {
                    "name": "GatewaySubnet",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.1.0/29",
                    "serviceEndpoints": [],
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    }

                ]
        ```

        Aby zmienić prefiks adresu w *template.js* w pliku, należy edytować go w dwóch miejscach: w kodzie w poprzedniej sekcji i w sekcji **Type** poniższego kodu. Zmień właściwość **addressPrefix** w poniższym kodzie, aby dopasować Właściwość **addressPrefix** w kodzie w poprzedniej sekcji.

        ```json
         "type": "Microsoft.Network/virtualNetworks/subnets",
           "apiVersion": "2019-06-01",
           "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/GatewaySubnet')]",
              "dependsOn": [
                 "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                   ],
              "properties": {
                 "provisioningState": "Succeeded",
                 "addressPrefix": "10.0.1.0/29",
                 "serviceEndpoints": [],
                 "delegations": [],
                 "privateEndpointNetworkPolicies": "Enabled",
                 "privateLinkServiceNetworkPolicies": "Enabled"
                  }
                 },
                  {
                  "type": "Microsoft.Network/virtualNetworks/subnets",
                  "apiVersion": "2019-06-01",
                  "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/subnet-1')]",
                     "dependsOn": [
                        "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                          ],
                     "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "10.0.0.0/24",
                        "delegations": [],
                        "privateEndpointNetworkPolicies": "Enabled",
                        "privateLinkServiceNetworkPolicies": "Enabled"
                         }
                  }
         ]
        ```

1. W edytorze online wybierz pozycję **Zapisz**.

1. Aby wybrać subskrypcję, w której zostanie wdrożona docelowa sieć wirtualna, wybierz pozycję  >  **subskrypcja** podstawy.

1. Aby wybrać grupę zasobów, w której zostanie wdrożona docelowa sieć wirtualna, wybierz pozycję **podstawowe**  >  **grupy zasobów**. 

    Jeśli musisz utworzyć nową grupę zasobów dla docelowej sieci wirtualnej, wybierz pozycję **Utwórz nową**. Upewnij się, że nazwa nie jest taka sama jak nazwa źródłowej grupy zasobów w istniejącej sieci wirtualnej.

1. Sprawdź, czy  >  **Lokalizacja** podstawy jest ustawiona na lokalizację docelową, w której ma zostać wdrożona Sieć wirtualna.

1. W obszarze **Ustawienia** Sprawdź, czy nazwa jest zgodna z nazwą wprowadzoną wcześniej w edytorze parametrów.

1. Zaznacz pole wyboru **warunki i postanowienia** .

1. Aby wdrożyć docelową sieć wirtualną, wybierz pozycję **Kup**.

## <a name="delete-the-target-virtual-network"></a>Usuń docelową sieć wirtualną

Aby odrzucić docelową sieć wirtualną, należy usunąć grupę zasobów zawierającą docelową sieć wirtualną. W tym celu:
1. Na pulpicie nawigacyjnym Azure Portal wybierz grupę zasobów.
1. W górnej części okienka **Przegląd** wybierz pozycję **Usuń**.

## <a name="clean-up"></a>Czyszczenie

Aby zatwierdzić zmiany i zakończyć przenoszenie sieci wirtualnej, należy usunąć źródłową sieć wirtualną lub grupę zasobów. W tym celu:
1. Na pulpicie nawigacyjnym Azure Portal wybierz sieć wirtualną lub grupę zasobów.
1. W górnej części każdego okienka wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przeniesiono sieć wirtualną platformy Azure z jednego regionu do innego przy użyciu Azure Portal a następnie oczyszczono niepotrzebne zasoby źródłowe. Aby dowiedzieć się więcej o przenoszeniu zasobów między regionami i odzyskiwaniem po awarii na platformie Azure, zobacz:


- [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Przenoszenie maszyn wirtualnych platformy Azure do innego regionu](../site-recovery/azure-to-azure-tutorial-migrate.md)