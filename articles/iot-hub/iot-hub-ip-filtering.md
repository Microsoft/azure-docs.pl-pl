---
title: Filtry protokołu IP IoT Hub platformy Azure | Microsoft Docs
description: Jak używać filtrowania adresów IP, aby zezwalać na połączenia z określonych adresów IP dla usługi Azure IoT Hub.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/19/2020
ms.author: jlian
ms.openlocfilehash: c6544e8ac00744602476207a89567aea5afe5b1d
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632369"
---
# <a name="use-ip-filters"></a>Korzystanie z filtrów adresów IP

Bezpieczeństwo jest ważnym aspektem każdego rozwiązania IoT w oparciu o IoT Hub platformy Azure. Czasami trzeba jawnie określić adresy IP, z których urządzenia mogą łączyć się w ramach konfiguracji zabezpieczeń. Funkcja *filtr IP* umożliwia konfigurowanie reguł odrzucania lub akceptowania ruchu z określonych adresów IPv4.

## <a name="when-to-use"></a>Kiedy stosować

Użyj filtru IP, aby odbierać ruch tylko z określonego zakresu adresów IP i odrzucać wszystkie pozostałe. Na przykład do tworzenia prywatnych połączeń między usługą IoT Hub a infrastrukturą lokalną można używać usługi IoT Hub w usłudze [Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) .

## <a name="default-setting"></a>Ustawienie domyślne

Aby przejść do strony Ustawienia filtru IP, wybierz pozycję **Sieć**, **dostęp publiczny**, a następnie wybierz pozycję **wybrane zakresy adresów IP**:

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-default.png" alt-text="IoT Hub domyślnych ustawień filtru IP":::

Domyślnie siatka **filtrów IP** w portalu Centrum IoT jest pusta. To ustawienie domyślne oznacza, że centrum blokuje połączenia ze wszystkich adresów IP. To ustawienie domyślne jest równoważne z regułą, która blokuje `0.0.0.0/0` zakres adresów IP.

## <a name="add-or-edit-an-ip-filter-rule"></a>Dodawanie lub edytowanie reguły filtru adresów IP

Aby dodać regułę filtru adresów IP, wybierz pozycję **+ Dodaj regułę filtrowania adresów IP**.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-add-rule.png" alt-text="Dodawanie reguły filtru IP do centrum IoT Hub":::

Po wybraniu pozycji **Dodaj regułę filtrowania adresów IP** wypełnij pola.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-after-selecting-add.png" alt-text="Po wybraniu pozycji Dodaj regułę filtrowania adresów IP":::

* Podaj **nazwę** reguły filtrowania adresów IP. Ta nazwa musi być unikatowym, niezależnym od wielkości liter, ciągiem alfanumerycznym o długości do 128 znaków. Akceptowane są tylko 7-bitowe znaki alfanumeryczne ASCII i `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`.

* Podaj pojedynczy adres IPv4 lub blok adresów IP w notacji CIDR. Na przykład w notacji CIDR zapis 192.168.100.0/22 reprezentuje 1024 adresy IPv4 z zakresu od 192.168.100.0 do 192.168.103.255.

Po wypełnieniu pól wybierz pozycję **Zapisz**, aby zapisać regułę. Zostanie wyświetlony alert informujący o tym, że aktualizacja jest w toku.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png" alt-text="Powiadomienie o zapisaniu reguły filtrowania adresów IP":::

Opcja **Dodaj** jest wyłączona, gdy osiągniesz maksymalną liczbę 10 reguł filtrowania adresów IP.

Aby edytować istniejącą regułę, wybierz dane, które chcesz zmienić, wprowadź zmianę, a następnie wybierz pozycję **Zapisz**, aby zapisać zmiany.

## <a name="delete-an-ip-filter-rule"></a>Usuwanie reguły filtrowania adresów IP

Aby usunąć regułę filtrowania adresów IP, wybierz ikonę kosza dla tego wiersza, a następnie wybierz pozycję **Zapisz**. Reguła zostanie usunięta, a zmiana zostanie zapisana.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-delete-rule.png" alt-text="Usuwanie reguły filtru IoT Hub IP":::

## <a name="apply-ip-filter-rules-to-the-built-in-event-hub-compatible-endpoint"></a>Zastosuj reguły filtru IP do wbudowanego punktu końcowego zgodnego z centrum zdarzeń

Aby zastosować reguły filtru IP do wbudowanego punktu końcowego zgodnego z centrum zdarzeń, zaznacz pole wyboru obok **zastosowania filtrów IP do wbudowanego punktu końcowego?**, a następnie wybierz pozycję **Zapisz**.

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-built-in-endpoint.png" alt-text="Obraz przedstawiający przełącznik wbudowanego punktu końcowego i zapisywania":::

> [!NOTE]
> Ta opcja nie jest dostępna do zwolnienia (F1) centrów IoT. Aby zastosować reguły filtru IP do wbudowanego punktu końcowego, użyj płatnego Centrum IoT.

Włączenie tej opcji powoduje, że reguły filtrów adresów IP są replikowane do wbudowanego punktu końcowego, dlatego dostęp do niego mają tylko zaufane zakresy adresów IP.

Jeśli wyłączysz tę opcję, wbudowany punkt końcowy będzie dostępny dla wszystkich adresów IP. Takie zachowanie może być przydatne, jeśli chcesz czytać z punktu końcowego przy użyciu usług, które zmieniają adresy IP, takie jak Azure Stream Analytics. 

## <a name="how-filter-rules-are-applied"></a>Jak są stosowane reguły filtrowania

Reguły filtrów IP są stosowane na poziomie usługi IoT Hub. W związku z tym reguły filtru IP są stosowane do wszystkich połączeń z urządzeń i aplikacji zaplecza przy użyciu dowolnego obsługiwanego protokołu. Ponadto możesz wybrać, czy [wbudowanego punktu końcowego zgodnego z centrum zdarzeń](iot-hub-devguide-messages-read-builtin.md) (nie za pośrednictwem parametrów połączenia IoT Hub) są powiązane z tymi regułami. 

Dowolna próba połączenia z adresu IP, który nie jest jawnie dozwolony, odbiera nieautoryzowany kod i opis stanu 401. Komunikat odpowiedzi nie zawiera reguły adresu IP. Odrzucenie adresów IP może uniemożliwić korzystanie z innych usług platformy Azure, takich jak Azure Stream Analytics, Azure Virtual Machines lub Device Explorer w Azure Portal ze współdziałania z usługą IoT Hub.

> [!NOTE]
> Jeśli musisz użyć Azure Stream Analytics (ASA) do odczytywania komunikatów z Centrum IoT z włączonym filtrem IP, **Wyłącz** opcję **Zastosuj filtry IP do wbudowanego punktu końcowego** , a następnie użyj nazwy zgodnej z centrum zdarzeń i punktu końcowego usługi IoT Hub, aby ręcznie dodać [dane wejściowe strumienia Event Hubs](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs) w ASA.

### <a name="ordering"></a>Zamawianie

Reguły filtru IP *umożliwiają* stosowanie reguł i stosowanie ich bez określania kolejności. Tylko dodawane adresy IP mogą łączyć się z IoT Hub. 

Na przykład, jeśli chcesz akceptować adresy z zakresu `192.168.100.0/22` i odrzucić wszystko inne, musisz dodać tylko jedną regułę w siatce z zakresem adresów `192.168.100.0/22` .

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>Pobieranie i aktualizowanie filtrów IP przy użyciu interfejsu wiersza polecenia platformy Azure

Filtry IP IoT Hub można pobrać i zaktualizować za pomocą [interfejsu wiersza polecenia platformy Azure](/cli/azure/).

Aby pobrać bieżące filtry IP IoT Hub, uruchom polecenie:

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

Spowoduje to zwrócenie obiektu JSON, w którym istniejące filtry IP są wymienione w `properties.networkRuleSets` kluczu:

```json
{
...
    "properties": {
        "networkRuleSets": {
            "defaultAction": "Deny",
            "applyToBuiltInEventHubEndpoint": true,
            "ipRules": [{
                    "filterName": "TrustedFactories",
                    "action": "Allow",
                    "ipMask": "1.2.3.4/5"
                },
                {
                    "filterName": "TrustedDevices",
                    "action": "Allow",
                    "ipMask": "1.1.1.1/1"
                }
            ]
        }
    }
}
```

Aby dodać nowy filtr IP dla IoT Hub, uruchom polecenie:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.networkRuleSets.ipRules "{\"action\":\"Allow\",\"filterName\":\"TrustedIP\",\"ipMask\":\"192.168.0.1\"}"
```

Aby usunąć istniejący filtr IP w IoT Hub, uruchom polecenie:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.networkRuleSets.ipRules <ipFilterIndexToRemove>
```

W tym miejscu `<ipFilterIndexToRemove>` musi odpowiadać kolejności filtrów IP w IoT Hub `properties.networkRuleSets.ipRules` .

## <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>Pobieranie i aktualizowanie filtrów IP przy użyciu Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Filtry IP IoT Hub mogą być pobierane i ustawiane przez [Azure PowerShell](/powershell/azure/).

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.networkRuleSets.ipRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='TrustedIP'; 'action'='Allow'; 'ipMask'='192.168.0.1'}

# Add your new IP filter rule
$iothubResource.Properties.networkRuleSets.ipRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.networkRuleSets.ipRules = @($iothubResource.Properties.networkRuleSets.ipRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzResource -Force
```

## <a name="update-ip-filter-rules-using-rest"></a>Aktualizowanie reguł filtrów IP przy użyciu REST


Możesz również pobrać i zmodyfikować filtr IP IoT Hub przy użyciu punktu końcowego REST dostawcy zasobów platformy Azure. Zobacz `properties.networkRuleSets` w opisie [metody createorupdate](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate).

## <a name="ip-filter-classic-retirement"></a>Wycofanie filtru IP (klasycznego)

Klasyczny filtr IP został wycofany. Aby dowiedzieć się więcej, zobacz [IoT Hub klasyczny filtr IP oraz jak przeprowadzić uaktualnienie](iot-hub-ip-filter-classic.md).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o możliwościach IoT Hub, zobacz:

* [Metryki IoT Hub](iot-hub-metrics.md)
* [IoT Hub obsługa sieci wirtualnych z linkiem prywatnym i tożsamością zarządzaną](virtual-network-support.md)
* [Zarządzanie dostępem do sieci publicznej dla Centrum IoT Hub](iot-hub-public-network-access.md)
* [Monitorowanie usługi IoT Hub](monitor-iot-hub.md)
