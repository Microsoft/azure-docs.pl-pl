---
title: Przenoszenie sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) platformy Azure do innego regionu platformy Azure przy użyciu Azure Portal
description: Użyj szablonu Azure Resource Manager, aby przenieść grupę zabezpieczeń sieci platformy Azure z jednego regionu platformy Azure do innego przy użyciu Azure Portal.
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: a22dc6dc0c4fc199d3f262b18aeeae5090a06dce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84689320"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-the-azure-portal"></a>Przenoszenie sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) platformy Azure do innego regionu przy użyciu Azure Portal

Istnieją różne scenariusze, w których należy przenieść istniejące sieciowych grup zabezpieczeń z jednego regionu do innego. Na przykład możesz chcieć utworzyć sieciowej grupy zabezpieczeń z tą samą konfiguracją i regułami zabezpieczeń na potrzeby testowania. Możesz również przenieść sieciowej grupy zabezpieczeń do innego regionu w ramach planowania odzyskiwania po awarii.

Grup zabezpieczeń platformy Azure nie można przenosić z jednego regionu do innego. Można jednak użyć szablonu Azure Resource Manager, aby wyeksportować istniejące reguły konfiguracji i zabezpieczeń sieciowej grupy zabezpieczeń.  Następnie można przemieścić zasób w innym regionie, eksportując sieciowej grupy zabezpieczeń do szablonu, modyfikując parametry w celu dopasowania do regionu docelowego, a następnie wdrożyć szablon w nowym regionie.  Aby uzyskać więcej informacji na temat Menedżer zasobów i szablonów, zobacz [Szybki Start: Tworzenie i wdrażanie szablonów Azure Resource Manager przy użyciu Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).


## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że grupa zabezpieczeń sieci platformy Azure znajduje się w regionie świadczenia usługi Azure, z którego chcesz przenieść.

- Nie można przenosić grup zabezpieczeń sieci platformy Azure między regionami.  Należy skojarzyć nowe sieciowej grupy zabezpieczeń z zasobami w regionie docelowym.

- Aby wyeksportować konfigurację sieciowej grupy zabezpieczeń i wdrożyć szablon w celu utworzenia sieciowej grupy zabezpieczeń w innym regionie, musisz mieć rolę współautor sieci lub wyższą.

- Zidentyfikuj układ sieci źródłowej i wszystkie aktualnie używane zasoby. Ten układ obejmuje, ale nie jest ograniczony do modułów równoważenia obciążenia, publicznych adresów IP i sieci wirtualnych.

- Sprawdź, czy subskrypcja platformy Azure umożliwia tworzenie sieciowych grup zabezpieczeń w regionie docelowym, który jest używany. Skontaktuj się z pomocą techniczną, aby włączyć wymagany limit przydziału.

- Upewnij się, że Twoja subskrypcja ma wystarczającą ilość zasobów, aby obsłużyć Dodawanie sieciowych grup zabezpieczeń dla tego procesu.  Zobacz [Azure subscription and service limits, quotas, and constraints](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits) (Limity, przydziały i ograniczenia usługi i subskrypcji platformy Azure).


## <a name="prepare-and-move"></a>Przygotowywanie i przenoszenie
Poniższe kroki pokazują, jak przygotować grupę zabezpieczeń sieci dla reguły konfiguracji i zabezpieczeń przenieść przy użyciu szablonu Menedżer zasobów i przenieść reguły konfiguracji i zabezpieczeń sieciowej grupy zabezpieczeń do regionu docelowego przy użyciu portalu.


### <a name="export-the-template-and-deploy-from-the-portal"></a>Eksportowanie szablonu i wdrażanie go z portalu

1. Zaloguj się do [Azure portal](https://portal.azure.com)  >  **grup zasobów**Azure Portal.
2. Znajdź grupę zasobów zawierającą sieciowej grupy zabezpieczeń źródłową i kliknij ją.
3. Wybierz pozycję > **Ustawienia**  >  **Eksportuj szablon**.
4. Wybierz pozycję **Wdróż** w bloku **Eksportuj szablon** .
5. Kliknij pozycję **szablon**  >  **Edytuj parametry** , aby otworzyć **parameters.jsw** pliku w edytorze online.
6. Aby edytować parametr nazwy sieciowej grupy zabezpieczeń, Zmień właściwość **Value** w obszarze **Parametry**:

    ```json
            {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
            "networkSecurityGroups_myVM1_nsg_name": {
               "value": "<target-nsg-name>"
                }
               }
            }
    ```

7. Zmień wartość sieciowej grupy zabezpieczeń źródła w edytorze na nazwę wybraną dla elementu docelowego sieciowej grupy zabezpieczeń. Upewnij się, że nazwa została ujęta w cudzysłów.

8.  Kliknij przycisk **Zapisz** w edytorze.

9.  Kliknij pozycję **szablon**  >  **Edytuj szablon** , aby otworzyć **template.jsw** pliku w edytorze online.

10. Aby edytować region docelowy, w którym zostaną przeniesione reguły konfiguracji i zabezpieczeń sieciowej grupy zabezpieczeń, Zmień właściwość **Location** w obszarze **zasoby** w edytorze online:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
             }
            }
           ]

    ```

11. Aby uzyskać kody lokalizacji regionu, zobacz [lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Kod regionu to nazwa regionu bez spacji, **środkowe stany USA**  =  **centralus**.

12. W przypadku wybrania opcji i opcjonalnych w zależności od wymagań można także zmienić inne parametry szablonu:

    * **Reguły zabezpieczeń** — można edytować, które reguły są wdrażane w docelowym sieciowej grupy zabezpieczeń, dodając lub usuwając reguły do sekcji **securityRules** w **template.js** pliku:

        ```json
           "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
                "securityRules": [
                    {
                        "name": "RDP",
                        "etag": "W/\"c630c458-6b52-4202-8fd7-172b7ab49cf5\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "TCP",
                            "sourcePortRange": "*",
                            "destinationPortRange": "3389",
                            "sourceAddressPrefix": "*",
                            "destinationAddressPrefix": "*",
                            "access": "Allow",
                            "priority": 300,
                            "direction": "Inbound",
                            "sourcePortRanges": [],
                            "destinationPortRanges": [],
                            "sourceAddressPrefixes": [],
                            "destinationAddressPrefixes": []
                             }
                    },
                ]
            }
        ```

      Aby ukończyć Dodawanie lub usuwanie reguł w docelowym sieciowej grupy zabezpieczeń, należy również edytować niestandardowe typy reguł na końcu **template.js** w pliku w formacie poniższego przykładu:

      ```json
           {
            "type": "Microsoft.Network/networkSecurityGroups/securityRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('networkSecurityGroups_myVM1_nsg_name'), '/Port_80')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('networkSecurityGroups_myVM1_nsg_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "protocol": "*",
                "sourcePortRange": "*",
                "destinationPortRange": "80",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 310,
                "direction": "Inbound",
                "sourcePortRanges": [],
                "destinationPortRanges": [],
                "sourceAddressPrefixes": [],
                "destinationAddressPrefixes": []
            }
      ```

13. Kliknij przycisk **Zapisz** w edytorze online.

14. Kliknij **BASICS**pozycję  >  **subskrypcja** podstawy, aby wybrać subskrypcję, w której zostanie wdrożony docelowy element sieciowej grupy zabezpieczeń.

15. Kliknij pozycję **podstawowe**  >  **grupy zasobów** , aby wybrać grupę zasobów, w której zostanie wdrożony docelowy element sieciowej grupy zabezpieczeń.  Możesz kliknąć przycisk **Utwórz nowy** , aby utworzyć nową grupę zasobów dla elementu docelowego sieciowej grupy zabezpieczeń.  Upewnij się, że nazwa nie jest taka sama jak źródłowa Grupa zasobów istniejącej sieciowej grupy zabezpieczeń.

16. Sprawdź **BASICS**  >  , czy**Lokalizacja** podstawy jest ustawiona na lokalizację docelową, w której ma zostać wdrożone sieciowej grupy zabezpieczeń.

17. Sprawdź, czy w obszarze **Ustawienia** nazwa jest zgodna z nazwą wprowadzoną w edytorze parametrów powyżej.

18. Zaznacz pole wyboru w obszarze **warunki i postanowienia**.

19. Kliknij przycisk **Kup** , aby wdrożyć docelową grupę zabezpieczeń sieci.

## <a name="discard"></a>Odrzuć

Jeśli chcesz odrzucić element docelowy sieciowej grupy zabezpieczeń, Usuń grupę zasobów zawierającą docelowy element sieciowej grupy zabezpieczeń.  Aby to zrobić, wybierz grupę zasobów z pulpitu nawigacyjnego w portalu i wybierz pozycję **Usuń** w górnej części strony przegląd.

## <a name="clean-up"></a>Czyszczenie

Aby zatwierdzić zmiany i zakończyć przenoszenie sieciowej grupy zabezpieczeń, Usuń źródło sieciowej grupy zabezpieczeń lub grupę zasobów. W tym celu wybierz grupę zabezpieczeń sieci lub grupę zasobów z pulpitu nawigacyjnego w portalu i wybierz pozycję **Usuń** w górnej części każdej strony.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przeniesiono grupę zabezpieczeń sieci platformy Azure z jednego regionu do innego i wyczyszczono zasoby źródłowe.  Aby dowiedzieć się więcej o przenoszeniu zasobów między regionami i odzyskiwaniem po awarii na platformie Azure, zobacz:


- [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Przenoszenie maszyn wirtualnych platformy Azure do innego regionu](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
