---
title: Filtry połączeń IP usługi Azure IoT DPS | Microsoft Docs
description: Jak używać filtrowania IP do blokowania połączeń z określonych adresów IP do wystąpienia usługi Azure IoT DPS. Można blokować połączenia z poszczególnych adresów IP lub z nich.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 580c378df5fc3912aa540b5d85adf99bc42605e0
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86511946"
---
# <a name="use-azure-iot-dps-ip-connection-filters"></a>Korzystanie z filtrów połączeń IP usługi Azure IoT DPS

Bezpieczeństwo jest ważnym aspektem każdego rozwiązania IoT. Czasami trzeba jawnie określić adresy IP, z których urządzenia mogą łączyć się w ramach konfiguracji zabezpieczeń. Funkcja *filtru IP* dla usługi Azure IoT Hub Device PROVISIONING Service (DPS) umożliwia konfigurowanie reguł odrzucania lub akceptowania ruchu z określonych adresów IPv4.

## <a name="when-to-use"></a>Kiedy stosować

Istnieją dwa konkretne przypadki użycia, w których warto zablokować połączenia z punktem końcowym usługi DPS z określonych adresów IP:

* Usługa DPS powinna odbierać ruch tylko z określonego zakresu adresów IP i odrzucać wszystkie inne. Na przykład do tworzenia prywatnych połączeń między działem DPS a urządzeniami jest używana usługa DPS z [usługą Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) .

* Należy odrzucić ruch z adresów IP, które zostały zidentyfikowane jako podejrzane przez administratora usługi DPS.

## <a name="how-filter-rules-are-applied"></a>Jak są stosowane reguły filtrowania

Reguły filtrów IP są stosowane na poziomie wystąpienia DPS. W związku z tym reguły filtru IP są stosowane do wszystkich połączeń z urządzeń i aplikacji zaplecza przy użyciu dowolnego obsługiwanego protokołu.

Wszystkie próby połączenia z adresu IP, które pasują do reguły odrzucenia adresu IP w wystąpieniu usługi DPS, otrzymują nieautoryzowany kod stanu 401 i opis. Komunikat odpowiedzi nie zawiera wzmianki o regule adresu IP.

## <a name="default-setting"></a>Ustawienie domyślne

Domyślnie siatka **filtrów IP** w portalu dla usługi DPS jest pusta. To ustawienie domyślne oznacza, że usługa DPS akceptuje połączenia z dowolnego adresu IP. To ustawienie domyślne jest równoważne z regułą akceptującą zakres adresów IP 0.0.0.0/0.

![Ustawienia domyślnego filtru IP IoT DPS](./media/iot-dps-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>Dodawanie lub Edytowanie reguły filtru IP

Aby dodać regułę filtrowania adresów IP, wybierz pozycję **+ Dodaj regułę filtrowania adresów IP**.

![Dodawanie reguły filtru IP do punktu dystrybucji IoT](./media/iot-dps-ip-filtering/ip-filter-add-rule.png)

Po wybraniu pozycji **Dodaj regułę filtrowania adresów IP**Wypełnij pola.

![Po wybraniu opcji Dodaj regułę filtrowania adresów IP](./media/iot-dps-ip-filtering/ip-filter-after-selecting-add.png)

* Podaj **nazwę** dla reguły filtru IP. Musi to być unikatowy ciąg alfanumeryczny bez uwzględniania wielkości liter, do 128 znaków. Akceptowane są tylko znaki alfanumeryczne ASCII 7-bitowe Plus `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` .

* Podaj pojedynczy adres IPv4 lub blok adresów IP w notacji CIDR. Na przykład w notacji CIDR 192.168.100.0/22 reprezentuje adresy IPv4 1024 z 192.168.100.0 do 192.168.103.255.

* Wybierz pozycję **Zezwalaj** lub **Blokuj** jako **akcję** dla reguły filtru IP.

Po wypełnieniu pól wybierz pozycję **Zapisz** , aby zapisać regułę. Zostanie wyświetlony alert informujący o tym, że aktualizacja jest w toku.

![Powiadomienie o zapisaniu reguły filtru IP](./media/iot-dps-ip-filtering/ip-filter-save-new-rule.png)

Opcja **Dodaj** jest wyłączona, gdy zostanie osiągnięta maksymalna liczba reguł filtru IP.

Aby edytować istniejącą regułę, wybierz dane, które chcesz zmienić, wprowadź zmianę, a następnie wybierz pozycję **Zapisz** , aby zapisać edycję.

> [!NOTE]
> Odrzucenie adresów IP może uniemożliwić innym usługom platformy Azure współdziałanie z wystąpieniem usługi DPS.

## <a name="delete-an-ip-filter-rule"></a>Usuwanie reguły filtrowania adresów IP

Aby usunąć regułę filtrowania adresów IP, wybierz ikonę kosza dla tego wiersza, a następnie wybierz pozycję **Zapisz**. Reguła zostanie usunięta, a zmiana została zapisana.

![Usuwanie reguły filtrowania adresów IP IoT DPS](./media/iot-dps-ip-filtering/ip-filter-delete-rule.png)


## <a name="update-ip-filter-rules-in-code"></a>Aktualizuj reguły filtrowania adresów IP w kodzie

Możesz pobrać i zmodyfikować filtr IP usługi DPS przy użyciu punktu końcowego REST dostawcy zasobów platformy Azure. Zobacz `properties.ipFilterRules` w [metodzie metodę createorupdate](https://docs.microsoft.com/rest/api/iot-dps/iotdpsresource/createorupdate).

Aktualizowanie reguł filtrów IP usługi DPS nie jest obecnie obsługiwane w interfejsie wiersza polecenia platformy Azure ani Azure PowerShell ale można je wykonać przy użyciu szablonów Azure Resource Manager. Aby uzyskać wskazówki dotyczące używania szablonów Menedżer zasobów, zobacz [Azure Resource Manager szablonów](../azure-resource-manager/templates/overview.md) . Poniższe przykłady szablonów pokazują, jak tworzyć, edytować i usuwać reguły filtru IP DPS.

### <a name="add-an-ip-filter-rule"></a>Dodaj regułę filtrowania adresów IP

Poniższy przykład szablonu tworzy nową regułę filtrowania adresów IP o nazwie "AllowAll", która akceptuje cały ruch.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#", 
    "contentVersion": "1.0.0.0", 
    "parameters": {
        "iotDpsName": {
            "type": "string",
            "defaultValue": "[resourceGroup().name]",
            "minLength": 3,
            "metadata": {
                "description": "Specifies the name of the IoT DPS service."
            }
        }, 
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for Iot DPS resource."
            }
        }        
    }, 
    "variables": {
        "iotDpsApiVersion": "2020-01-01"
    }, 
    "resources": [
        {
            "type": "Microsoft.Devices/provisioningServices",
            "apiVersion": "[variables('iotDpsApiVersion')]",
            "name": "[parameters('iotDpsName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "capacity": 1
            },
            "properties": {
                "IpFilterRules": [
                    {
                        "FilterName": "AllowAll",
                        "Action": "Accept",
                        "ipMask": "0.0.0.0/0"
                    }
                ]
            }            
        }
    ]
}
```

Zaktualizuj atrybuty reguły filtrowania adresów IP szablonu na podstawie wymagań.

| Atrybut                | Opis |
| ------------------------ | ----------- |
| **FilterName**           | Podaj nazwę dla reguły filtru IP. Musi to być unikatowy ciąg alfanumeryczny bez uwzględniania wielkości liter, do 128 znaków. Tylko znaki alfanumeryczne ASCII 7-bitowe plus {"-", ":", "/", " \' ,". "," + ","% "," _ "," # "," * ","? ","! "akceptowane są wartości" (",") ",", "," = "," @ ","; "i" "". |
| **Akcja**               | Akceptowane wartości są **akceptowane**   lub **odrzucane**   jako akcja dla reguły filtru IP. |
| **ipMask**               | Podaj pojedynczy adres IPv4 lub blok adresów IP w notacji CIDR. Na przykład w notacji CIDR 192.168.100.0/22 reprezentuje adresy IPv4 1024 z 192.168.100.0 do 192.168.103.255. |


### <a name="update-an-ip-filter-rule"></a>Aktualizowanie reguły filtrowania adresów IP

Poniższy przykład szablonu aktualizuje regułę filtru IP o nazwie "AllowAll", pokazana wcześniej, aby odrzucić cały ruch.

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
                "IpFilterRules": [ 
                    { 
                        "FilterName": "AllowAll", 
                        "Action": "Reject", 
                        "ipMask": "0.0.0.0/0" 
                    } 
                ] 
            }             
        } 
    ] 
}
```

### <a name="delete-an-ip-filter-rule"></a>Usuwanie reguły filtrowania adresów IP

Poniższy przykład szablonu usuwa wszystkie reguły filtrów IP dla wystąpienia usługi DPS.

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
            }             
        } 
    ] 
}
```



## <a name="ip-filter-rule-evaluation"></a>Obliczanie reguły filtru IP

Reguły filtrowania adresów IP są stosowane w kolejności, a pierwsza reguła zgodna z adresem IP określa akcję Akceptuj lub Odrzuć.

Na przykład, jeśli chcesz akceptować adresy w zakresie 192.168.100.0/22 i odrzucić wszystkie inne, pierwsza reguła w siatce powinna akceptować zakres adresów 192.168.100.0/22. Kolejna reguła powinna odrzucać wszystkie adresy przy użyciu zakresu 0.0.0.0/0.

Aby zmienić kolejność reguł filtrów IP w siatce, klikaj trzy pionowe kropki na początku wiersza i przy użyciu przeciągania i upuszczania.

Aby zapisać nową kolejność reguł filtrowania adresów IP, kliknij przycisk **Zapisz**.

![Zmiana kolejności reguł filtrowania adresów IP w usłudze DPS](./media/iot-dps-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat zarządzania usługą DPS, zobacz:

* [Informacje o adresach IP IoT DPS](iot-dps-understand-ip-address.md)
* [Konfigurowanie usługi DPS przy użyciu interfejsu wiersza polecenia platformy Azure](how-to-manage-dps-with-cli.md)
* [Kontrola dostępu do punktu dystrybucji](how-to-control-access.md)
