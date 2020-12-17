---
title: Wprowadzenie do rozwiązywania problemów z połączeniem z usługą Azure Network Watcher | Microsoft Docs
description: Ta strona zawiera omówienie możliwości rozwiązywania problemów z Network Watcher łączności
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: damendo
ms.openlocfilehash: 9c855fff9e5791b9c0cf870acfc6de53e7a700b2
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97654000"
---
# <a name="introduction-to-connection-troubleshoot-in-azure-network-watcher"></a>Wprowadzenie do rozwiązywania problemów dotyczących połączeń w usłudze Azure Network Watcher

Funkcja rozwiązywania problemów z połączeniami Network Watcher umożliwia sprawdzenie bezpośredniego połączenia TCP między maszyną wirtualną a maszyną wirtualną (VM), w pełni kwalifikowaną nazwą domeny (FQDN), identyfikatorem URI lub adresem IPv4. Scenariusze sieci są skomplikowane, są implementowane przy użyciu sieciowych grup zabezpieczeń, zapór, tras zdefiniowanych przez użytkownika i zasobów udostępnianych przez platformę Azure. Skomplikowane konfiguracje sprawiają, że problemy z łącznością są trudne. Network Watcher pomaga skrócić czas znajdowania i wykrywania problemów z łącznością. Zwrócone wyniki mogą zapewnić wgląd w to, czy problem z łącznością jest spowodowany problemem z platformą lub konfiguracją użytkownika. Łączność można sprawdzić za pomocą [programu PowerShell](network-watcher-connectivity-powershell.md), interfejsu [wiersza polecenia platformy Azure i usługi](network-watcher-connectivity-cli.md) [API REST](network-watcher-connectivity-rest.md).

> [!IMPORTANT]
> Rozwiązywanie problemów z połączeniem wymaga, aby maszyna wirtualna z maszyną wirtualną była `AzureNetworkWatcherExtension` zainstalowana jako rozszerzenie maszyny wirtualnej. Aby zainstalować rozszerzenie na maszynie wirtualnej z systemem Windows, odwiedź [rozszerzenie maszyny wirtualnej usługi azure Network Watcher Agent dla systemu Windows](../virtual-machines/extensions/network-watcher-windows.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) i dla maszyny wirtualnej z systemem Linux odwiedź [rozszerzenie maszyny wirtualnej agenta usługi Azure Network Watcher](../virtual-machines/extensions/network-watcher-linux.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). Rozszerzenie nie jest wymagane w docelowym punkcie końcowym.

## <a name="response"></a>Reakcja

W poniższej tabeli przedstawiono właściwości zwracane po zakończeniu rozwiązywania problemów z połączeniami.

|Właściwość  |Opis  |
|---------|---------|
|ConnectionStatus     | Stan sprawdzania łączności. Możliwe wyniki są **dostępne** i **nieosiągalne**.        |
|AvgLatencyInMs     | Średnie opóźnienie podczas sprawdzania łączności w milisekundach. (Wyświetlane tylko w przypadku, gdy stan sprawdzania jest osiągalny)        |
|MinLatencyInMs     | Minimalne opóźnienie podczas sprawdzania łączności w milisekundach. (Wyświetlane tylko w przypadku, gdy stan sprawdzania jest osiągalny)        |
|MaxLatencyInMs     | Maksymalne opóźnienie podczas sprawdzania łączności w milisekundach. (Wyświetlane tylko w przypadku, gdy stan sprawdzania jest osiągalny)        |
|ProbesSent     | Liczba sondowań wysłanych podczas sprawdzania. Wartość maksymalna to 100.        |
|ProbesFailed     | Liczba sond, które zakończyły się niepowodzeniem podczas sprawdzania. Wartość maksymalna to 100.        |
|Przeskoki     | Przeskoki według ścieżki przeskoku ze źródła do miejsca docelowego.        |
|Przeskoki []. Wprowadź     | Typ zasobu. Możliwe wartości to **Source**, **VirtualAppliance**, **VnetLocal** i **Internet**.        |
|Przeskoki []. #C1 | Unikatowy identyfikator przeskoku.|
|Przeskoki []. Ulica | Adres IP przeskoku.|
|Przeskoki []. Identyfikator | Identyfikator ResourceID przeskoku, jeśli przeskok jest zasobem platformy Azure. Jeśli jest to zasób internetowy, ResourceID jest **Internet**. |
|Przeskoki []. NextHopIds | Unikatowy identyfikator pobranego następnego przeskoku.|
|Przeskoki []. Luk | Kolekcja problemów, które zostały napotkane podczas sprawdzania tego przeskoku. Jeśli nie wystąpiły żadne problemy, wartość jest pusta.|
|Przeskoki []. Problemy []. Źródł | W bieżącym przeskoku, w którym wystąpił problem. Możliwe wartości:<br/> **Ruch przychodzący** — problem dotyczy linku z poprzedniego przeskoku do bieżącego przeskoku<br/>Ruch **wychodzący** jest związany z linkiem z bieżącego przeskoku do następnego przeskoku<br/>**Lokalne** — problem dotyczy bieżącego przeskoku.|
|Przeskoki []. Problemy []. Obrażeń | Ważność wykrytego problemu. Możliwe wartości to **Error** i **Warning**. |
|Przeskoki []. Problemy []. Wprowadź |Typ znalezionego problemu. Możliwe wartości: <br/>**Procesor CPU**<br/>**Pamięć**<br/>**GuestFirewall**<br/>**DnsResolution**<br/>**NetworkSecurityRule**<br/>**UserDefinedRoute** |
|Przeskoki []. Problemy []. Context |Szczegóły dotyczące znalezionego problemu.|
|Przeskoki []. Problemy []. Context []. Key |Zwrócono klucz pary par klucz-wartość.|
|Przeskoki []. Problemy []. Context []. wartość |Wartość zwracanej pary kluczy.|

Poniżej znajduje się przykład problemu znalezionego w przeskoku.

```json
"Issues": [
    {
        "Origin": "Outbound",
        "Severity": "Error",
        "Type": "NetworkSecurityRule",
        "Context": [
            {
                "key": "RuleName",
                "value": "UserRule_Port80"
            }
        ]
    }
]
```
## <a name="fault-types"></a>Typy błędów

Rozwiązywanie problemów z połączeniami zwraca typy błędów dotyczące połączenia. Poniższa tabela zawiera listę bieżących zwracanych typów błędów.

|Typ  |Opis  |
|---------|---------|
|CPU     | Wysokie wykorzystanie procesora CPU.       |
|Pamięć     | Duże użycie pamięci.       |
|GuestFirewall     | Ruch jest zablokowany z powodu konfiguracji zapory maszyny wirtualnej.        |
|DNSResolution     | Rozpoznawanie nazw DNS dla adresu docelowego nie powiodło się.        |
|NetworkSecurityRule    | Ruch jest blokowany przez regułę sieciowej grupy zabezpieczeń (zwracana jest reguła)        |
|UserDefinedRoute|Ruch został porzucony z powodu zdefiniowanej przez użytkownika lub trasy systemowej. |

### <a name="next-steps"></a>Następne kroki

Dowiedz się, jak rozwiązywać problemy z połączeniami przy użyciu programu [Azure Portal](network-watcher-connectivity-portal.md), [programu PowerShell](network-watcher-connectivity-powershell.md), interfejsu [wiersza polecenia platformy Azure](network-watcher-connectivity-cli.md)lub [API REST](network-watcher-connectivity-rest.md).