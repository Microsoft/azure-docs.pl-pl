---
title: Filtry połączeń IP usługi Azure IoT DPS | Microsoft Docs
description: Jak używać filtrowania adresów IP do blokowania połączeń z określonych adresów IP do wystąpienia usługi Azure IoT DPS. Możesz blokować połączenia z poszczególnych adresów IP lub ich zakresów.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: 6ff5731e7d346c954253ec4186357595461b7678
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400260"
---
# <a name="use-azure-iot-dps-ip-connection-filters"></a>Korzystanie z filtrów połączeń IP usługi Azure IoT DPS

Bezpieczeństwo jest ważnym aspektem każdego rozwiązania IoT. Czasami trzeba jawnie określić adresy IP, z których urządzenia mogą łączyć się w ramach konfiguracji zabezpieczeń. Funkcja *filtru adresów IP* dla usługi Azure IoT Hub Device Provisioning Service (DPS) umożliwia konfigurowanie reguł dotyczących odrzucania lub akceptowania ruchu z określonych adresów IPv4.

## <a name="when-to-use"></a>Kiedy stosować

Istnieją dwa konkretne przypadki użycia, w których warto zablokować połączenia z punktem końcowym usługi DPS z określonych adresów IP:

* Usługa DPS powinna odbierać ruch tylko z określonego zakresu adresów IP i odrzucać każdy inny ruch. Na przykład używasz usługi DPS z usługą [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services), aby tworzyć prywatne połączenia między usługą DPS a urządzeniami.

* Należy odrzucić ruch z adresów IP, które zostały zidentyfikowane jako podejrzane przez administratora usługi DPS.

## <a name="how-filter-rules-are-applied"></a>Jak są stosowane reguły filtrowania

Reguły filtrów adresów IP są stosowane na poziomie wystąpienia usługi DPS. W związku z tym reguły filtrów adresów IP są stosowane do wszystkich połączeń z urządzeń i aplikacji zaplecza przy użyciu dowolnego obsługiwanego protokołu.

Wszystkie próby połączenia z adresu IP, który pasuje do reguły odrzucającej adresy IP w wystąpieniu usługi DPS, otrzymują nieautoryzowany kod stanu 401 i opis. Komunikat odpowiedzi nie zawiera reguły adresu IP.

## <a name="default-setting"></a>Ustawienie domyślne

Domyślnie siatka **filtrów adresów IP** w portalu dla usługi DPS jest pusta. To ustawienie domyślne oznacza, że usługa DPS akceptuje połączenia z dowolnego adresu IP. To ustawienie domyślne jest równoważne z regułą akceptującą zakres adresów IP 0.0.0.0/0.

![Ustawienia domyślne filtru adresów IP w usłudze IoT DPS](./media/iot-dps-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>Dodawanie lub edytowanie reguły filtru adresów IP

Aby dodać regułę filtru adresów IP, wybierz pozycję **+ Dodaj regułę filtrowania adresów IP**.

![Dodawanie reguły filtrowania adresów IP do usługi IoT DPS](./media/iot-dps-ip-filtering/ip-filter-add-rule.png)

Po wybraniu pozycji **Dodaj regułę filtrowania adresów IP** wypełnij pola.

![Po wybraniu pozycji Dodaj regułę filtrowania adresów IP](./media/iot-dps-ip-filtering/ip-filter-after-selecting-add.png)

* Podaj **nazwę** reguły filtrowania adresów IP. Nazwa musi być unikatowym, alfanumerycznym ciągiem z rozróżnianą wielkością liter o długości do 128 znaków. Akceptowane są tylko 7-bitowe znaki alfanumeryczne ASCII i `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`.

* Podaj pojedynczy adres IPv4 lub blok adresów IP w notacji CIDR. Na przykład w notacji CIDR zapis 192.168.100.0/22 reprezentuje 1024 adresy IPv4 z zakresu od 192.168.100.0 do 192.168.103.255.

* Wybierz pozycję **Zezwalaj** lub **Blokuj** jako **akcję** reguły filtrowania adresów IP.

Po wypełnieniu pól wybierz pozycję **Zapisz**, aby zapisać regułę. Zostanie wyświetlony alert informujący o tym, że aktualizacja jest w toku.

![Powiadomienie o zapisaniu reguły filtrowania adresów IP](./media/iot-dps-ip-filtering/ip-filter-save-new-rule.png)

Opcja **Dodaj** jest wyłączona w przypadku osiągnięcia maksymalnej liczby reguł filtru IP 100.

Aby edytować istniejącą regułę, wybierz dane, które chcesz zmienić, wprowadź zmianę, a następnie wybierz pozycję **Zapisz**, aby zapisać zmiany.

> [!NOTE]
> Odrzucenie adresów IP może uniemożliwić innym usługom platformy Azure współdziałanie z wystąpieniem usługi DPS.

## <a name="delete-an-ip-filter-rule"></a>Usuwanie reguły filtrowania adresów IP

Aby usunąć regułę filtrowania adresów IP, wybierz ikonę kosza dla tego wiersza, a następnie wybierz pozycję **Zapisz**. Reguła zostanie usunięta, a zmiana zostanie zapisana.

![Usuwanie reguły filtrowania adresów IP usługi IoT DPS](./media/iot-dps-ip-filtering/ip-filter-delete-rule.png)


## <a name="update-ip-filter-rules-in-code"></a>Aktualizowanie reguł filtrowania adresów IP w kodzie

Możesz pobrać i zmodyfikować filtr adresów IP usługi DPS przy użyciu punktu końcowego REST dostawcy zasobów platformy Azure. Zobacz `properties.ipFilterRules` w opisie [metody createorupdate](/rest/api/iot-dps/iotdpsresource/createorupdate).

Aktualizowanie reguł filtrowania adresów IP usługi DPS nie jest obecnie obsługiwane w interfejsie wiersza polecenia platformy Azure ani w programie Azure PowerShell, ale można je wykonać przy użyciu szablonów usługi Azure Resource Manager. Zobacz [Szablony usługi Azure Resource Manager](../azure-resource-manager/templates/overview.md), aby uzyskać wskazówki dotyczące używania szablonów usługi Resource Manager. Poniższe przykłady szablonów pokazują, jak tworzyć, edytować i usuwać reguły filtrowania adresów IP w usłudze DPS.

### <a name="add-an-ip-filter-rule"></a>Dodawanie reguły filtrowania adresów IP

Poniższy przykładowy szablon umożliwia utworzenie nowej reguły filtrowania adresów IP o nazwie „AllowAll”, która akceptuje cały ruch.

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

Zaktualizuj atrybuty reguły filtrowania adresów IP szablonu zgodnie z Twoimi wymaganiami.

| Atrybut                | Opis |
| ------------------------ | ----------- |
| **FilterName**           | Podaj nazwę reguły filtrowania adresów IP. Nazwa musi być unikatowym, alfanumerycznym ciągiem z rozróżnianą wielkością liter o długości do 128 znaków. Akceptowane są tylko znaki alfanumeryczne ASCII 7-bitowe Plus  `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`   . |
| **Akcja**               | Akceptowane wartości akcji reguły filtrowania adresów IP to **Akceptuj** lub  **Odrzuć** . |
| **ipMask**               | Podaj pojedynczy adres IPv4 lub blok adresów IP w notacji CIDR. Na przykład w notacji CIDR zapis 192.168.100.0/22 reprezentuje 1024 adresy IPv4 z zakresu od 192.168.100.0 do 192.168.103.255. |


### <a name="update-an-ip-filter-rule"></a>Aktualizowanie reguły filtrowania adresów IP

Poniższy przykładowy szablon aktualizuje pokazaną wcześniej regułę filtrowania adresów IP o nazwie „AllowAll”, aby odrzucić cały ruch.

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

Poniższy przykładowy szablon usuwa wszystkie reguły filtrowania adresów IP dla wystąpienia usługi DPS.

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



## <a name="ip-filter-rule-evaluation"></a>Ocena reguły filtrowania adresów IP

Reguły filtrowania adresów IP są stosowane w odpowiedniej kolejności, a pierwsza reguła zgodna z adresem IP określa akcję Akceptuj lub Odrzuć.

Jeśli na przykład chcesz akceptować adresy w zakresie 192.168.100.0/22 i odrzucać wszystkie inne, pierwsza reguła w siatce powinna akceptować zakres adresów 192.168.100.0/22. Kolejna reguła powinna odrzucać wszystkie adresy przy użyciu zakresu 0.0.0.0/0.

Aby zmienić kolejność reguł filtrowania adresów IP w siatce, kliknij trzy pionowe kropki na początku wiersza oraz użyj metody przeciągania i upuszczania.

Aby zapisać nową kolejność reguł filtrowania adresów IP, kliknij przycisk **Zapisz**.

![Zmiana kolejności reguł filtrowania adresów IP w usłudze DPS](./media/iot-dps-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat zarządzania usługą DPS, zobacz:

* [Informacje o adresach IP usługi IoT DPS](iot-dps-understand-ip-address.md)
* [Konfigurowanie usługi DPS przy użyciu interfejsu wiersza polecenia platformy Azure](how-to-manage-dps-with-cli.md)
* [Kontrola dostępu do usługi DPS](how-to-control-access.md)
