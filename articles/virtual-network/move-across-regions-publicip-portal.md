---
title: Przenoszenie konfiguracji publicznego adresu IP platformy Azure do innego regionu platformy Azure Azure Portal
description: Użyj szablonu, aby przenieść konfigurację publicznego adresu IP platformy Azure z jednego regionu platformy Azure do innego przy użyciu Azure Portal.
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: b00fca8cf39bc44e0e53a112a332e6f6c5f0194e
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98218607"
---
# <a name="move-azure-public-ip-configuration-to-another-region-using-the-azure-portal"></a>Przenoszenie konfiguracji publicznego adresu IP platformy Azure do innego regionu przy użyciu Azure Portal

Istnieją różne scenariusze, w których należy przenieść istniejące konfiguracje publicznego adresu IP platformy Azure z jednego regionu do innego. Na przykład możesz chcieć utworzyć publiczny adres IP z taką samą konfiguracją i jednostką SKU na potrzeby testowania. Możesz również przenieść publiczną konfigurację adresu IP do innego regionu w ramach planowania odzyskiwania po awarii.

**Publiczne adresy IP platformy Azure są specyficzne dla regionu i nie można ich przenosić z jednego regionu do innego.** Można jednak użyć szablonu Azure Resource Manager do eksportowania istniejącej konfiguracji publicznego adresu IP.  Następnie można przemieścić zasób w innym regionie, eksportując publiczny adres IP do szablonu, modyfikując parametry w celu dopasowania do regionu docelowego, a następnie wdrożyć szablon w nowym regionie.  Aby uzyskać więcej informacji na temat Menedżer zasobów i szablonów, zobacz [Szybki Start: Tworzenie i wdrażanie szablonów Azure Resource Manager przy użyciu Azure Portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).


## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że publiczny adres IP platformy Azure znajduje się w regionie świadczenia usługi Azure, z którego chcesz przenieść.

- Nie można przenosić publicznych adresów IP platformy Azure między regionami.  Musisz skojarzyć nowy publiczny adres IP z zasobami w regionie docelowym.

- Aby wyeksportować konfigurację publicznego adresu IP i wdrożyć szablon w celu utworzenia publicznego adresu IP w innym regionie, musisz mieć rolę współautor sieci lub wyższą.

- Zidentyfikuj układ sieci źródłowej i wszystkie aktualnie używane zasoby. Ten układ obejmuje, ale nie jest ograniczony do modułów równoważenia obciążenia, sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) i sieci wirtualnych.

- Sprawdź, czy subskrypcja platformy Azure umożliwia tworzenie publicznych adresów IP w używanym regionie docelowym. Skontaktuj się z pomocą techniczną, aby włączyć wymagany limit przydziału.

- Upewnij się, że Twoja subskrypcja ma wystarczającą ilość zasobów, aby obsłużyć Dodawanie publicznych adresów IP dla tego procesu.  Zobacz [Azure subscription and service limits, quotas, and constraints](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) (Limity, przydziały i ograniczenia usługi i subskrypcji platformy Azure).


## <a name="prepare-and-move"></a>Przygotowywanie i przenoszenie
Poniższe kroki pokazują, jak przygotować publiczny adres IP do przenoszenia konfiguracji przy użyciu szablonu Menedżer zasobów i przenieść konfigurację publicznego adresu IP do regionu docelowego przy użyciu Azure Portal.

### <a name="export-the-template-and-deploy-from-a-script"></a>Eksportowanie szablonu i wdrażanie go ze skryptu

1. Zaloguj się do [](https://portal.azure.com)  >  **grup zasobów** Azure Portal.
2. Znajdź grupę zasobów zawierającą źródłowy publiczny adres IP, a następnie kliknij ją.
3. Wybierz pozycję > **Ustawienia**  >  **Eksportuj szablon**.
4. Wybierz pozycję **Wdróż** w bloku **Eksportuj szablon** .
5. Kliknij pozycję **szablon**  >  **Edytuj parametry** , aby otworzyć **parameters.jsw** pliku w edytorze online.
8. Aby edytować parametr publicznej nazwy IP, Zmień właściwość w polu   >  **wartość** parametrów ze źródłowej publicznej nazwy IP na nazwę docelowego publicznego adresu IP, upewnij się, że nazwa jest cudzysłowem:

    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "publicIPAddresses_myVM1pubIP_name": {
            "value": "<target-publicip-name>"
              }
             }
            }

    ```
8.  Kliknij przycisk **Zapisz** w edytorze.

9.  Kliknij pozycję **szablon**  >  **Edytuj szablon** , aby otworzyć **template.jsw** pliku w edytorze online.

10. Aby edytować region docelowy, w którym zostanie przeniesiony publiczny adres IP, Zmień właściwość **lokalizacji** w obszarze **zasoby**:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]
    ```

11. Aby uzyskać kody lokalizacji regionu, zobacz [lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Kod regionu to nazwa regionu bez spacji, **środkowe stany USA**  =  .

12. W przypadku wybrania opcji i opcjonalnych w zależności od wymagań można także zmienić inne parametry szablonu:

    * **Jednostka SKU** — można zmienić jednostkę SKU publicznego adresu IP w konfiguracji z warstwy Standardowa na podstawowa lub podstawowa na Standard, zmieniając właściwość Nazwa **jednostki SKU**  >   w **template.jsw** pliku:

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
        ```

        Aby uzyskać więcej informacji na temat różnic między podstawowymi a standardowymi adresami IP jednostki SKU, zobacz [Tworzenie, zmienianie lub usuwanie publicznego adresu IP](./virtual-network-public-ip-address.md):

    * **Metoda alokacji publicznego adresu IP** i **limit czasu bezczynności** — można zmienić obie te opcje w szablonie, zmieniając właściwość **publicIPAllocationMethod** z **dynamicznego** na **statyczny** lub **statyczny** na **dynamiczny**. Limit czasu bezczynności można zmienić, zmieniając właściwość **idleTimeoutInMinutes** na żądaną wartość.  Wartość domyślna to **4**:

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []

        ```

        Aby uzyskać więcej informacji na temat metod alokacji i wartości limitu czasu bezczynności, zobacz [Tworzenie, zmienianie lub usuwanie publicznego adresu IP](./virtual-network-public-ip-address.md).


13. Kliknij przycisk **Zapisz** w edytorze online.

14. Kliknij pozycję  >  **subskrypcja** podstawy, aby wybrać subskrypcję, w której zostanie wdrożony docelowy publiczny adres IP.

15. Kliknij pozycję **podstawowe**  >  **grupy zasobów** , aby wybrać grupę zasobów, w której zostanie wdrożony docelowy publiczny adres IP.  Możesz kliknąć pozycję **Utwórz nowy** , aby utworzyć nową grupę zasobów dla docelowego publicznego adresu IP.  Upewnij się, że nazwa nie jest taka sama jak źródłowa Grupa zasobów istniejącego publicznego adresu IP.

16. Sprawdź   >  , czy **Lokalizacja** podstawy jest ustawiona na lokalizację docelową, w której ma zostać wdrożony publiczny adres IP.

17. Sprawdź, czy w obszarze **Ustawienia** nazwa jest zgodna z nazwą wprowadzoną w edytorze parametrów powyżej.

18. Zaznacz pole wyboru w obszarze **warunki i postanowienia**.

19. Kliknij przycisk **Kup** , aby wdrożyć docelowy publiczny adres IP.

## <a name="discard"></a>Odrzuć

Jeśli chcesz odrzucić docelowy publiczny adres IP, Usuń grupę zasobów zawierającą docelowy publiczny adres IP.  Aby to zrobić, wybierz grupę zasobów z pulpitu nawigacyjnego w portalu i wybierz pozycję **Usuń** w górnej części strony przegląd.

## <a name="clean-up"></a>Czyszczenie

Aby zatwierdzić zmiany i zakończyć przenoszenie publicznego adresu IP, Usuń źródłowy publiczny adres IP lub grupę zasobów. Aby to zrobić, wybierz publiczny adres IP lub grupę zasobów z pulpitu nawigacyjnego w portalu i wybierz pozycję **Usuń** w górnej części każdej strony.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przyniesiono publiczny adres IP platformy Azure z jednego regionu do innego i wyczyszczono zasoby źródłowe.  Aby dowiedzieć się więcej o przenoszeniu zasobów między regionami i odzyskiwaniem po awarii na platformie Azure, zobacz:


- [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Przenoszenie maszyn wirtualnych platformy Azure do innego regionu](../site-recovery/azure-to-azure-tutorial-migrate.md)