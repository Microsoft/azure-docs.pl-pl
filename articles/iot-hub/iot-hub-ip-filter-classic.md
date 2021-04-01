---
title: Azure IoT Hub klasyczny filtr IP (przestarzałe) | Microsoft Docs
description: Jak uaktualnić klasyczny filtr IP i jakie są korzyści
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: jlian
ms.openlocfilehash: 6f326bafb311acedc48c5a349c78f1cd6bcebc87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101661158"
---
# <a name="iot-hub-classic-ip-filter-and-how-to-upgrade"></a>IoT Hub klasyczny filtr IP i sposób uaktualniania 

Uaktualniony filtr IP dla IoT Hub chroni wbudowany punkt końcowy i jest zabezpieczony domyślnie. W związku z tym, że nigdy nie wprowadzisz zmian, ulepszony model zabezpieczeń uaktualnionego filtru IP jest niezgodny z klasycznym filtrem IP, więc ogłaszamy jego wycofanie. Aby dowiedzieć się więcej o nowym uaktualnionym filtrze IP, zobacz [co nowego](#whats-new) i [filtry IP Centrum IoT Hub](iot-hub-ip-filtering.md).

Aby uniknąć przerw w działaniu usługi, należy przeprowadzić uaktualnienie z przewodnikiem przed upływem ostatecznego terminu migracji. w tym momencie uaktualnienie zostanie wykonane automatycznie. Aby dowiedzieć się więcej o osi czasu migracji, zobacz [Azure Update](https://aka.ms/ipfilterv2azupdate).

## <a name="how-to-upgrade"></a>Jak przeprowadzić uaktualnianie

1.  Odwiedź witrynę Azure Portal
2.  Przejdź do centrum IoT Hub.
3.  Wybierz pozycję **Sieć** z menu po lewej stronie.
4.  Powinien pojawić się transparent z monitem o uaktualnienie filtru IP do nowego modelu. Wybierz przycisk **Tak**, aby kontynuować.
    :::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-upgrade-banner.png" alt-text="Obraz przedstawiający monit o uaktualnienie z klasycznego filtru IP":::
5.  Ponieważ nowy filtr IP domyślnie blokuje wszystkie adresy IP, to uaktualnienie usuwa poszczególne reguły odmowy, ale daje szansę przejrzenia ich przed zapisaniem. Uważnie Przejrzyj reguły, aby upewnić się, że działają.
6.  Postępuj zgodnie z monitami, aby zakończyć uaktualnianie.

## <a name="whats-new"></a>Co nowego

### <a name="secure-by-default"></a>Zabezpieczenie domyślne

Klasyczny filtr IP niejawnie zezwala na wszystkie adresy IP, które domyślnie łączą się z IoT Hub, co nie jest dobrze dostosowane do najbardziej typowych scenariuszy zabezpieczeń sieci. Zwykle do połączenia z usługą IoT Hub mają być dostępne tylko zaufane adresy IP i odrzucane są wszystkie inne. Aby osiągnąć ten cel przy użyciu klasycznego filtru IP, jest to proces wieloetapowy. Na przykład, jeśli chcesz akceptować tylko ruch z `192.168.100.0/22` , musisz

1. Skonfiguruj jedną regułę *zezwalania* dla programu `192.168.100.0/22` .
1. Skonfiguruj inną regułę *blokowania* dla `0.0.0.0/0` (reguła "Blokuj wszystko")
1. Upewnij się, że reguły są uporządkowane prawidłowo, z regułą zezwalania zamówioną powyżej reguły blokowania.

W tym przypadku proces wieloetapowy powoduje pomyłkę. Użytkownicy nie skonfigurowali reguły "Blokuj wszystko" lub nieprawidłowo porządkują reguły, co spowodowało niezamierzone narażenie. 

Nowy filtr IP domyślnie blokuje wszystkie adresy IP. Tylko zakresy adresów IP, które można jawnie dodać, mogą łączyć się z IoT Hub. W powyższym przykładzie kroki 2 i 3 nie są już potrzebne. To nowe zachowanie upraszcza konfigurację i przestrzega zasad przez [regułę zabezpieczeń zgodnie z zasadami domyślnymi](https://wikipedia.org/wiki/Secure_by_default).

### <a name="protect-the-built-in-event-hub-compatible-endpoint"></a>Ochrona wbudowanego punktu końcowego zgodnego z centrum zdarzeń

Klasyczny filtr IP nie może zostać zastosowany do wbudowanego punktu końcowego. To ograniczenie oznacza, że w przypadku, gdy skonfigurowano blok wszystkich reguł (bloków `0.0.0.0/0` ), wbudowany punkt końcowy jest nadal dostępny z dowolnego adresu IP.

Nowy filtr IP zapewnia opcję zastosowania reguł do wbudowanego punktu końcowego, co zmniejsza ryzyko dla zagrożeń bezpieczeństwa sieci.

:::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-built-in-endpoint.png" alt-text="Obraz przedstawiający przełącznik, który ma zostać zastosowany do wbudowanego punktu końcowego":::

> [!NOTE]
> Ta opcja nie jest dostępna do zwolnienia (F1) centrów IoT. Aby zastosować reguły filtru IP do wbudowanego punktu końcowego, użyj płatnego Centrum IoT.

### <a name="api-impact"></a>Wpływ interfejsu API

Uaktualniony filtr IP jest dostępny w interfejsie API zasobów IoT Hub z `2020-08-31` (a także `2020-08-31-preview` ) i do wewnątrz. Klasyczny filtr IP jest nadal dostępny we wszystkich wersjach interfejsu API, ale zostanie usunięty w przyszłej wersji interfejsu API w najbliższym terminie migracji. Aby dowiedzieć się więcej o osi czasu migracji, zobacz [Azure Update](https://aka.ms/ipfilterv2azupdate).

## <a name="tip-try-the-changes-before-they-apply"></a>Porada: Wypróbuj zmiany przed ich zastosowaniem

Ponieważ nowy filtr IP domyślnie blokuje wszystkie adresy IP, poszczególne reguły blokowania nie są już zgodne. W związku z tym proces uaktualniania z przewodnikiem usuwa te poszczególne reguły blokowania. 

Aby spróbować zmienić przy użyciu klasycznego filtru IP:

1. Odwiedź kartę **Sieć** w centrum IoT Hub
1. Zanotuj istniejącą konfigurację filtru IP (klasyczną), w przypadku której chcesz wycofać
1. Obok pozycji reguły z **blokiem** wybierz ikonę kosza, aby je usunąć
1. Dodaj kolejną regułę u dołu z `0.0.0.0/0` i wybierz pozycję **Blokuj**
1. Wybierz pozycję **Zapisz**

Ta konfiguracja śladuje, jak nowy filtr IP zachowuje się po uaktualnieniu z klasycznej. Jedynym wyjątkiem jest wbudowana funkcja ochrony punktu końcowego, która nie jest możliwa przy użyciu klasycznego filtru IP. Jednak ta funkcja jest opcjonalna, dlatego nie trzeba jej używać, jeśli uważasz, że może to spowodować uszkodzenie elementu.

## <a name="tip-check-diagnostic-logs-for-all-ip-connections-to-your-iot-hub"></a>Porada: sprawdzanie dzienników diagnostycznych dla wszystkich połączeń IP z Centrum IoT Hub

Aby zapewnić bezproblemowe przejście, Sprawdź dzienniki diagnostyczne w kategorii połączenia. Wyszukaj właściwość, `maskedIpAddress` Aby sprawdzić, czy zakresy są oczekiwane. Pamiętaj: nowy filtr IP spowoduje zablokowanie wszystkich adresów IP, które nie zostały jawnie dodane.

## <a name="iot-hub-classic-ip-filter-documentation-retired"></a>Dokumentacja filtru klasycznych adresów IP IoT Hub (wycofana)

> [!IMPORTANT]
> Poniżej znajduje się oryginalna dokumentacja klasycznego filtru IP, która jest wycofywana.

Bezpieczeństwo jest ważnym aspektem każdego rozwiązania IoT w oparciu o IoT Hub platformy Azure. Czasami trzeba jawnie określić adresy IP, z których urządzenia mogą łączyć się w ramach konfiguracji zabezpieczeń. Funkcja *filtr IP* umożliwia konfigurowanie reguł odrzucania lub akceptowania ruchu z określonych adresów IPv4.

### <a name="when-to-use"></a>Kiedy stosować

Istnieją dwa konkretne przypadki użycia, gdy warto zablokować IoT Hub punkty końcowe dla określonych adresów IP:

* Centrum IoT Hub powinna odbierać ruch tylko z określonego zakresu adresów IP i odrzucać wszystkie inne. Na przykład do tworzenia prywatnych połączeń między usługą IoT Hub a infrastrukturą lokalną można używać usługi IoT Hub w usłudze [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services) .

* Należy odrzucić ruch z adresów IP, które zostały zidentyfikowane jako podejrzane przez administratora usługi IoT Hub.

### <a name="how-filter-rules-are-applied"></a>Jak są stosowane reguły filtrowania

Reguły filtrów IP są stosowane na poziomie usługi IoT Hub. W związku z tym reguły filtru IP są stosowane do wszystkich połączeń z urządzeń i aplikacji zaplecza przy użyciu dowolnego obsługiwanego protokołu. Jednak klienci odczytujący się bezpośrednio z [wbudowanego punktu końcowego zgodnego z centrum zdarzeń](iot-hub-devguide-messages-read-builtin.md) (nie za pośrednictwem parametrów połączenia IoT Hub) nie są powiązane z regułami filtrowania adresów IP. 

Wszystkie próby połączenia z adresu IP, które pasują do reguły odrzucenia adresu IP w centrum IoT, odbierają nieautoryzowany kod stanu 401 i opis. Komunikat odpowiedzi nie zawiera reguły adresu IP. Odrzucenie adresów IP może uniemożliwić korzystanie z innych usług platformy Azure, takich jak Azure Stream Analytics, Azure Virtual Machines lub Device Explorer w Azure Portal ze współdziałania z usługą IoT Hub.

> [!NOTE]
> Jeśli musisz użyć Azure Stream Analytics (ASA) do odczytywania komunikatów z Centrum IoT z włączonym filtrem IP, użyj nazwy zgodnej z centrum zdarzeń i punktu końcowego Centrum IoT, aby ręcznie dodać [dane wejściowe strumienia Event Hubs](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs) do asa.

### <a name="default-setting"></a>Ustawienie domyślne

Domyślnie siatka **filtrów IP** w portalu Centrum IoT jest pusta. To ustawienie domyślne oznacza, że centrum akceptuje połączenia z dowolnego adresu IP. To ustawienie domyślne jest równoważne z regułą akceptującą zakres adresów IP 0.0.0.0/0.

Aby przejść do strony Ustawienia filtru IP, wybierz pozycję **Sieć**, **dostęp publiczny**, a następnie wybierz pozycję **wybrane zakresy adresów IP**:

:::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-default.png" alt-text="IoT Hub domyślnych ustawień filtru IP":::

### <a name="add-or-edit-an-ip-filter-rule"></a>Dodawanie lub edytowanie reguły filtru adresów IP

Aby dodać regułę filtru adresów IP, wybierz pozycję **+ Dodaj regułę filtrowania adresów IP**.

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-add-rule.png" alt-text="Dodawanie reguły filtru IP do centrum IoT Hub":::

Po wybraniu pozycji **Dodaj regułę filtrowania adresów IP** wypełnij pola.

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-after-selecting-add.png" alt-text="Po wybraniu pozycji Dodaj regułę filtrowania adresów IP":::

* Podaj **nazwę** reguły filtrowania adresów IP. Nazwa musi być unikatowym, alfanumerycznym ciągiem z rozróżnianą wielkością liter o długości do 128 znaków. Akceptowane są tylko 7-bitowe znaki alfanumeryczne ASCII i `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`.

* Podaj pojedynczy adres IPv4 lub blok adresów IP w notacji CIDR. Na przykład w notacji CIDR zapis 192.168.100.0/22 reprezentuje 1024 adresy IPv4 z zakresu od 192.168.100.0 do 192.168.103.255.

* Wybierz pozycję **Zezwalaj** lub **Blokuj** jako **akcję** reguły filtrowania adresów IP.

Po wypełnieniu pól wybierz pozycję **Zapisz**, aby zapisać regułę. Zostanie wyświetlony alert informujący o tym, że aktualizacja jest w toku.

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-save-new-rule.png" alt-text="Powiadomienie o zapisaniu reguły filtrowania adresów IP":::

Opcja **Dodaj** jest wyłączona, gdy osiągniesz maksymalną liczbę 10 reguł filtrowania adresów IP.

Aby edytować istniejącą regułę, wybierz dane, które chcesz zmienić, wprowadź zmianę, a następnie wybierz pozycję **Zapisz**, aby zapisać zmiany.

### <a name="delete-an-ip-filter-rule"></a>Usuwanie reguły filtrowania adresów IP

Aby usunąć regułę filtrowania adresów IP, wybierz ikonę kosza dla tego wiersza, a następnie wybierz pozycję **Zapisz**. Reguła zostanie usunięta, a zmiana zostanie zapisana.

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-delete-rule.png" alt-text="Usuwanie reguły filtru IoT Hub IP":::

### <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>Pobieranie i aktualizowanie filtrów IP przy użyciu interfejsu wiersza polecenia platformy Azure

Filtry IP IoT Hub można pobrać i zaktualizować za pomocą [interfejsu wiersza polecenia platformy Azure](/cli/azure/).

Aby pobrać bieżące filtry IP IoT Hub, uruchom polecenie:

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

Spowoduje to zwrócenie obiektu JSON, w którym istniejące filtry IP są wymienione w `properties.ipFilterRules` kluczu:

```json
{
...
    "properties": {
        "ipFilterRules": [
        {
            "action": "Reject",
            "filterName": "MaliciousIP",
            "ipMask": "6.6.6.6/6"
        },
        {
            "action": "Allow",
            "filterName": "GoodIP",
            "ipMask": "131.107.160.200"
        },
        ...
        ],
    },
...
}
```

Aby dodać nowy filtr IP dla IoT Hub, uruchom polecenie:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules "{\"action\":\"Reject\",\"filterName\":\"MaliciousIP\",\"ipMask\":\"6.6.6.6/6\"}"
```

Aby usunąć istniejący filtr IP w IoT Hub, uruchom polecenie:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules <ipFilterIndexToRemove>
```

Należy pamiętać, że `<ipFilterIndexToRemove>` musi odpowiadać kolejności filtrów IP w IoT Hub `properties.ipFilterRules` .

### <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>Pobieranie i aktualizowanie filtrów IP przy użyciu Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Filtry IP IoT Hub mogą być pobierane i ustawiane przez [Azure PowerShell](/powershell/azure/).

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.ipFilterRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='MaliciousIP'; 'action'='Reject'; 'ipMask'='6.6.6.6/6'}

# Add your new IP filter rule
$iothubResource.Properties.ipFilterRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.ipFilterRules = @($iothubResource.Properties.ipFilterRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzResource -Force
```

### <a name="update-ip-filter-rules-using-rest"></a>Aktualizowanie reguł filtrów IP przy użyciu REST

Możesz również pobrać i zmodyfikować filtr IP IoT Hub przy użyciu punktu końcowego REST dostawcy zasobów platformy Azure. Zobacz `properties.ipFilterRules` w opisie [metody createorupdate](/rest/api/iothub/iothubresource/createorupdate).

### <a name="ip-filter-rule-evaluation"></a>Ocena reguły filtrowania adresów IP

Reguły filtrowania adresów IP są stosowane w odpowiedniej kolejności, a pierwsza reguła zgodna z adresem IP określa akcję Akceptuj lub Odrzuć.

Jeśli na przykład chcesz akceptować adresy w zakresie 192.168.100.0/22 i odrzucać wszystkie inne, pierwsza reguła w siatce powinna akceptować zakres adresów 192.168.100.0/22. Kolejna reguła powinna odrzucać wszystkie adresy przy użyciu zakresu 0.0.0.0/0.

Aby zmienić kolejność reguł filtrowania adresów IP w siatce, kliknij trzy pionowe kropki na początku wiersza oraz użyj metody przeciągania i upuszczania.

Aby zapisać nową kolejność reguł filtrowania adresów IP, kliknij przycisk **Zapisz**.

:::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-rule-order.png" alt-text="Zmiana kolejności reguł filtrowania IoT Hub IP":::

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o możliwościach IoT Hub, zobacz:

* [Korzystanie z filtrów adresów IP](iot-hub-ip-filtering.md)