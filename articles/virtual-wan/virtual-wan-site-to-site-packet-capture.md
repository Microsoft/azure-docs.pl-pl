---
title: 'Samouczek: przechwytywanie pakietów Azure Virtual WAN połączeń typu lokacja-lokacja'
description: Z tego samouczka dowiesz się, jak przechwycić pakiety w Virtual WAN lokacja-VPN Gateway lokacja.
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 04/13/2021
ms.author: wellee
Customer intent: As someone with a networking background using Virtual WAN, I want to perform a packet capture on my Site-to-site VPN Gateway.
ms.openlocfilehash: dbe7e06484797063ed4122ee3fdde625dc66c41f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107879157"
---
# <a name="perform-packet-capture-on-the-azure-virtual-wan-site-to-site-vpn-gateway"></a>Przechwyć pakiety w Azure Virtual WAN lokacja-lokacj VPN Gateway a 

Problemy związane z łącznością i wydajnością są często złożone. Zawężenie przyczyny problemu może zająć znaczną część czasu i nakładu pracy. Przechwytywanie pakietów może ułatwić zawężenie zakresu problemu do niektórych części sieci. Może to pomóc w ustaleniu, czy problem występuje po stronie klienta sieci, po stronie platformy Azure sieci, czy w jakimś miejscu między nimi. Po zawężeniu problemu bardziej efektywne jest debugowanie i działanie naprawcze.

W poniższym artykule opisano, jak uruchomić i zatrzymać przechwytywanie pakietów w Azure Virtual WAN lokacja-lokacja w VPN Gateway. Obecnie ta funkcja jest dostępna tylko za pośrednictwem programu PowerShell.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, musisz mieć już następującą konfigurację w swoim środowisku:

* Centrum Virtual WAN wirtualnego i lokacja-lokacja VPN Gateway wdrożone w koncentratorze wirtualnym. Możesz również mieć połączenia łączące lokacje sieci VPN z lokacjami typu lokacja-lokacja VPN Gateway.


### <a name="working-with-azure-powershell"></a>Praca z Azure PowerShell

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="set-up-the-environment"></a>Konfigurowanie środowiska

Upewnij się, że jesteś w odpowiednim kontekście subskrypcji, uruchamiając następujący skrypt w programie PowerShell. Dzięki temu użytkownik jest zalogowany i ma uprawnienia do wykonywania przechwytywania pakietów w witrynie typu lokacja-lokacja VPN Gateway.

   ```azurepowershell-interactive
    $subid = “<insert Virtual WAN subscription ID here>”
    Set-AzContext -SubscriptionId $subid
   ```

## <a name="create-a-storage-account"></a><a name="createstorage"></a> Tworzenie konta magazynu

Musisz utworzyć konto magazynu w ramach subskrypcji platformy Azure, aby przechowywać wyniki przechwytywania pakietów. Zapoznaj się z [tym dokumentem,](.././storage/common/storage-account-create.md) aby uzyskać instrukcje dotyczące sposobu tworzenia konta magazynu.

Po utworzeniu konta uruchom następujące polecenia, aby wygenerować adres URL sygnatury dostępu współdzielonego (SAS). Wyniki przechwytywania pakietów będą przechowywane za pośrednictwem tego adresu URL.
   ```azurepowershell-interactive
  $rgname = “<resource group name containing storage account>” 
$storeName = “<name of storage account> “
$containerName = “<name of container you want to store packet capture in>
$key = Get-AzStorageAccountKey -ResourceGroupName $rgname -Name $storeNAme
$context = New-AzStorageContext -StorageAccountName  $storeName -StorageAccountKey $key[0].value
New-AzStorageContainer -Name $containerName -Context $context
$container = Get-AzStorageContainer -Name $containerName  -Context $context
$now = get-date
$sasurl = New-AzureStorageContainerSASToken -Name $containerName -Context $context -Permission "rwd" -StartTime $now.AddHours(-1) -ExpiryTime $now.AddDays(1) -FullUri
   ```

## <a name="start-the-packet-capture"></a>Uruchamianie przechwytywania pakietów

Aby rozpocząć przechwytywanie pakietów, dostępne są dwie opcje przechwytywania pakietów w ramach jednego połączenia sieci VPN lub całego połączenia typu lokacja-lokacja VPN Gateway. Należy pamiętać, że w przypadku wybrania przechwytywania połączeń sieci VPN można wykonywać przechwytywania tylko dla 6 połączeń jednocześnie.

### <a name="packet-capture-on-a-site-to-site-vpn-gateway-all-connections"></a>Przechwytywanie pakietów w sieci typu lokacja-lokacja VPN Gateway (wszystkie połączenia)

Uruchom następujące polecenia. Nazwę połączenia lokacja-lokacja można VPN Gateway, przechodząc do centrum wirtualnego i klikając pozycję VPN (lokacja-lokacja) w obszarze Łączność.

:::image type="content" source="./media/virtual-wan-pcap-screenshots/vpn-gateway-name.png" alt-text="Obraz Virtual WAN bramy." lightbox="./media/virtual-wan-pcap-screenshots/vpn-gateway-name.png":::

   ```azurepowershell-interactive
Start-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name "<name of the Gateway>" -Sasurl $sasurl
   ```

### <a name="packet-capture-on-specific-site-to-site-vpn-connections"></a>Przechwytywanie pakietów dla określonych połączeń sieci VPN typu lokacja-lokacja

>[!NOTE]
> Należy pamiętać, że przechwytywanie pakietów można uruchomić tylko dla 5 połączeń sieci VPN jednocześnie.


Uruchom następujące polecenia. Nazwę połączeń sieci VPN typu lokacja-lokacja można znaleźć, przechodząc do koncentratora wirtualnego, klikając pozycję VPN (lokacja-lokacja) w obszarze Łączność. Następnie przejdź do witryny sieci VPN, na której chcesz przechwycić pakiet, i kliknij trzy kropki po prawej stronie. Kliknij **pozycję Edytuj połączenie sieci VPN** w menu, które zostanie wyskakujące.

:::image type="content" source="./media/virtual-wan-pcap-screenshots/sample-connection.png" alt-text="Obraz sposobu znalezienia nazw połączeń sieci VPN." lightbox="./media/virtual-wan-pcap-screenshots/sample-connection.png":::

Nazwę linków połączonych z określoną witryną sieci VPN można znaleźć, klikając witrynę sieci VPN z poprzedniej sekcji i spoglądając na **tabelę Linki.** 

:::image type="content" source="./media/virtual-wan-pcap-screenshots/link-name-sample.png" alt-text="Obraz sposobu znalezienia nazwy linku sieci VPN." lightbox="./media/virtual-wan-pcap-screenshots/link-name-sample.png":::

   ```azurepowershell-interactive
Start-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name "<name of the VPN connection>" -ParentResourceName “<name of the Gateway>” -LinkConnection “<comma separated list of links eg. "link1,link2">” -Sasurl $sasurl 
   ```

## <a name="optional-specifying-filters"></a>Opcjonalnie: Określanie filtrów

Istnieją pewne powszechnie dostępne narzędzia do przechwytywania pakietów. Pobieranie odpowiednich przechwytywania pakietów za pomocą tych narzędzi może być kłopotliwe, szczególnie w scenariuszach z dużym ruchem. Aby uprościć przechwytywanie pakietów, można określić filtry przechwytywania pakietów, aby skoncentrować się na określonych zachowaniach. Poniżej przedstawiono dostępne parametry:

>[!NOTE]
> W przypadku protokołów TracingFlags i TCPFlags można określić wiele protokołów, dodając wartości liczbowe dla protokołów, które chcesz przechwycić (takie same jak logiczne OR). Jeśli na przykład chcesz przechwytywać tylko pakiety ESP i OPVN, określ wartość tracingFlag 8+1 = 9.  

| Parametr | Opis | Wartości domyślne | Dostępne wartości|
|--- |--- | --- | ---|
| TracingFlags | Liczba całkowita, która określa, jakie typy pakietów są przechwytywane | 11 (ESP, IKE, OVPN) | ESP = 1 IKE = 2 OPVN = 8 |
| TcpFlags | Liczba całkowita, która określa, które typy pakietów TCP są przechwytywane | 0 (brak) | FIN = 1, SYN = 2, RST = 4, PSH = 8, ACK = 16,URG = 32, ECE = 64, CWR = 128| 
| MaxPacketBufferSize|Maksymalny rozmiar przechwyconego pakietu w bajtach. Pakiety są obcinane, jeśli są większe niż podana wartość. |120|Dowolne|
| Maxfilesize |Maksymalny rozmiar pliku przechwytywania w Mb. Przechwycenia są przechowywane w cyklicznym buforze, więc przepełnienie jest obsługiwane w trybie FIFO (starsze pakiety są najpierw usuwane)|100|Dowolne|
|Podsieć źródłową|Przechwytywane są pakiety z określonych zakresów CIDR. Określony jako tablica.|[] (wszystkie adresy IPv4)|Tablica rozdzielonych przecinkami podsieci IPV4|
| Podsieci docelowe |Przechwytywane są pakiety przeznaczone dla określonych zakresów CIDR. Określony jako tablica. |[] (wszystkie adresy IPv4)|Tablica rozdzielonych przecinkami podsieci IPV4|
|Port źródłowy |Przechwytywane są pakiety ze źródłem w określonych zakresach. Określony jako tablica.|[] (wszystkie porty)|Tablica portów rozdzielonych przecinkami|
|DestinationPort |Przechwytywane są pakiety z miejscem docelowym w określonych zakresach. Określony jako tablica.|[] (wszystkie porty)|Tablica portów rozdzielonych przecinkami|
| CaptureSingleDirectionTrafficOnly |W przypadku wartości true w przechwytywaniu pakietów będzie pokazywany tylko jeden kierunek przepływu dwukierunkowego. Spowoduje to przechwycenie wszystkich możliwych kombinacji adresów IP i portów.|Prawda|True, False|
|Protokół|Tablica liczb całkowitych, które odpowiadają protokołom IANA. |[] (wszystkie protokoły)| Wszystkie protokoły znalezione [tutaj](https://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml) |

Poniżej znajduje się przykład przechwytywania pakietów przy użyciu ciągu filtru. Możesz zmienić parametry zgodnie z potrzebami na podstawie powyższej tabeli.  

   ```azurepowershell-interactive
$filter="{`"TracingFlags`":11,`"MaxPacketBufferSize`":120,`"MaxFileSize`":500,`"Filters`":[{`"SourceSubnets`":[`"10.19.0.4/32`",`"10.20.0.4/32`"],`"DestinationSubnets`":[`"10.20.0.4/32`",`"10.19.0.4/32`"],`"TcpFlags`":9,`"CaptureSingleDirectionTrafficOnly`":true}]}"
Start-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name "<name of the VPN connection>" -ParentResourceName “<name of the Gateway>” -LinkConnection “<comma separated list of links>” -Sasurl $sasurl -FilterData $filter

Start-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name "<name of the Gateway>" -Sasurl $sasurl -FilterData $filter
   ```

## <a name="stopping-the-packet-capture"></a>Zatrzymywanie przechwytywania pakietów
Zaleca się, aby przechwytywanie pakietów było uruchamiane przez co najmniej 600 sekund. Ze względu na problemy z synchronizacją między wieloma składnikami ścieżki krótsze przechwytywanie pakietów może nie dostarczać pełnych danych. Gdy wszystko będzie gotowe do zatrzymania przechwytywania pakietów, uruchom następujące polecenie.

Parametry są podobne do tych z sekcji Uruchamianie przechwytywania pakietów. Adres URL sygnatury dostępu współdzielonego został wygenerowany w [sekcji Tworzenie konta](#createstorage) magazynu.

### <a name="gateway-level-packet-capture"></a>Przechwytywanie pakietów na poziomie bramy

   ```azurepowershell-interactive
Stop-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name <GatewayName> -SasUrl $sas
   ```

### <a name="connection-level-packet-captures"></a>Przechwytywanie pakietów na poziomie połączenia

   ```azurepowershell-interactive
Stop-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name <name of the VPN connection> -ParentResourceName "<name of VPN Gateway>" -LinkConnectionName <comma separated list of links e.g. "link1,link2">-SasUrl $sas
   ```

## <a name="viewing-your-packet-capture"></a>Wyświetlanie przechwytywania pakietów

W Azure Portal przejdź do konta magazynu utworzonego w chmurze "Tworzenie konta magazynu" Kliknij kontener i pobierz plik. Pliki danych przechwytywania pakietów są generowane w formacie PCAP. Do otwierania plików PCAP można użyć programu Wireshark lub innej często dostępnej aplikacji.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Virtual WAN, zobacz:

> [!div class="nextstepaction"]
> * [Często zadawane pytania dotyczące usługi Virtual WAN](virtual-wan-faq.md)