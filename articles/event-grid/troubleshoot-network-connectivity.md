---
title: Rozwiązywanie problemów z łącznością sieciową — Azure Event Grid | Microsoft Docs
description: Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z łącznością sieciową z Azure Event Grid.
author: batrived
ms.topic: article
ms.date: 06/21/2020
ms.author: batrived
ms.openlocfilehash: 7b93d7a110889192bb5be6fffa56a73758d6faa2
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92892319"
---
# <a name="troubleshoot-connectivity-issues---azure-event-grid"></a>Rozwiązywanie problemów z łącznością — Azure Event Grid

Istnieją różne przyczyny, dla których aplikacje klienckie nie mogą nawiązać połączenia z Event Grid tematem/domeną. Problemy z łącznością mogą być stałe lub przejściowe. Jeśli problem występuje przez cały czas (trwały), możesz sprawdzić ustawienia zapory w organizacji, ustawienia zapory protokołu IP, Tagi usług, prywatne punkty końcowe i inne. W przypadku problemów przejściowych uruchamianie poleceń w celu sprawdzenia pakietów porzuconych i uzyskanie śladów sieci może pomóc w rozwiązywaniu problemów.

Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów z łącznością z usługą Azure Event Grid.

## <a name="troubleshoot-permanent-connectivity-issues"></a>Rozwiązywanie stałych problemów z łącznością

Jeśli aplikacja nie może nawiązać połączenia z siatką zdarzeń w ogóle, wykonaj kroki opisane w tej sekcji, aby rozwiązać problem.

### <a name="check-if-theres-a-service-outage"></a>Sprawdź, czy wystąpiła awaria usługi

Sprawdź, czy usługa Azure Event Grid przestoje w [witrynie stanu usługi platformy Azure](https://azure.microsoft.com/status/).

### <a name="check-if-the-ports-required-to-communicate-with-event-grid-arent-blocked-by-organizations-firewall"></a>Sprawdź, czy porty wymagane do komunikacji z Event Grid nie są blokowane przez zaporę organizacji

Sprawdź, czy porty używane w komunikacji z Azure Event Grid nie są blokowane w zaporze organizacji. Zapoznaj się z poniższą tabelą dla portów wychodzących, które należy otworzyć, aby komunikować się z Azure Event Grid.

| Protokół | Porty |
| -------- | ----- |
| HTTPS    | 443   |

Oto przykładowe polecenie, które sprawdza, czy port 443 jest zablokowany.

```powershell
.\psping.exe -n 25 -i 1 -q {sampletopicname}.{region}-{suffix}.eventgrid.azure.net:443 -nobanner
```

W systemie Linux:

```shell
telnet {sampletopicname}.{region}-{suffix}.eventgrid.azure.net 443
```

### <a name="verify-that-ip-addresses-are-allowed-in-your-corporate-firewall"></a>Sprawdź, czy adresy IP są dozwolone w zaporze firmowej

Podczas pracy z platformą Azure czasami trzeba zezwolić na określone zakresy adresów IP lub adresy URL w firmowej zaporze lub serwerze proxy, aby uzyskać dostęp do wszystkich usług platformy Azure, których używasz lub których próbujesz użyć. Sprawdź, czy ruch jest dozwolony dla adresów IP używanych przez Event Grid. Adresy IP używane przez Azure Event Grid: zobacz [zakresy adresów IP platformy Azure i Tagi usług — chmura publiczna](https://www.microsoft.com/download/details.aspx?id=56519) i [tag usługi — AzureEventGrid](network-security.md#service-tags).

[Zakresy adresów IP platformy Azure i Tagi usług — dokument w chmurze publicznej](https://www.microsoft.com/download/details.aspx?id=56519) zawiera również adresy IP **według regionów** . Można zezwolić na zakresy adresów dla **regionu tematu** i **sparowanego regionu** w firmowej zaporze lub serwerze proxy. W przypadku sparowanego regionu dla regionu Zobacz temat [ciągłość działania i odzyskiwanie po awarii (BCDR): wieloelementowe regiony platformy Azure](/azure/best-practices-availability-paired-regions). 

> [!NOTE]
> Nowe adresy IP można dodać do tagu usługi AzureEventGrid, chociaż nie jest to normalne. Dlatego warto wykonać cotygodniowe sprawdzanie tagów usługi.

### <a name="verify-that-azureeventgrid-service-tag-is-allowed-in-your-network-security-groups"></a>Sprawdź, czy w sieciowej grupie zabezpieczeń jest dozwolony tag usługi AzureEventGrid

Jeśli aplikacja działa w podsieci i jeśli istnieje skojarzona sieciowa Grupa zabezpieczeń, upewnij się, czy jest dozwolony dostęp do Internetu, czy też jest dozwolony tag usługi AzureEventGrid. Zobacz [Tagi usług](../virtual-network/service-tags-overview.md)

### <a name="check-the-ip-firewall-settings-for-your-topicdomain"></a>Sprawdź ustawienia zapory IP dla tematu/domeny

Sprawdź, czy publiczny adres IP komputera, na którym uruchomiona jest aplikacja, nie jest blokowany przez EventGrid tematu/zapory adresu IP domeny.

Domyślnie tematy Event Grid/domeny są dostępne z Internetu, o ile żądanie zawiera prawidłowe uwierzytelnianie i autoryzację. Za pomocą zapory IP można ograniczyć ją tylko do zestawu adresów IPv4 lub zakresów adresów IPv4 w notacji [CIDR (bez klas Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

Reguły zapory adresów IP są stosowane na poziomie tematu/domeny Event Grid. W związku z tym reguły są stosowane do wszystkich połączeń z klientów przy użyciu dowolnego obsługiwanego protokołu. Dowolna próba połączenia z adresu IP, który nie jest zgodny z dozwoloną regułą adresów IP w temacie/domenie Event Grid, jest odrzucana jako zabronione. Odpowiedź nie zawiera wzmianki o regule adresów IP.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie reguł zapory adresów IP dla tematu Azure Event Grid/domeny](configure-firewall.md).

#### <a name="find-the-ip-addresses-blocked-by-ip-firewall"></a>Znajdź adresy IP blokowane przez zaporę IP

Włączanie dzienników diagnostycznych dla Event Grid tematu/domeny [Włączanie dzienników diagnostycznych](enable-diagnostic-logs-topic.md#enable-diagnostic-logs-for-a-custom-topic). Zobaczysz adres IP dla niedozwolonego połączenia.

```json
{
  "time": "2019-11-01T00:17:13.4389048Z",
  "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME",
  "category": "PublishFailures",
  "operationName": "Post",
  "message": "inputEventsCount=null, requestUri=https://SAMPLE-TOPIC-NAME.region-suffix.eventgrid.azure.net/api/events, publisherInfo=PublisherInfo(category=User, inputSchema=EventGridEvent, armResourceId=/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME), httpStatusCode=Forbidden, errorType=ClientIPRejected, errorMessage=Publishing to SAMPLE-TOPIC-NAME.{region}-{suffix}.EVENTGRID.AZURE.NET by client {clientIp} is rejected due to IpAddress filtering rules."
}
```

### <a name="check-if-the-eventgrid-topicdomain-can-be-accessed-using-only-a-private-endpoint"></a>Sprawdź, czy w temacie EventGrid/domenie można uzyskać dostęp tylko przy użyciu prywatnego punktu końcowego

Jeśli konfiguracja tematu/domeny Event Grid jest dostępna tylko za pośrednictwem prywatnego punktu końcowego, upewnij się, że aplikacja kliencka uzyskuje dostęp do tematu/domeny za pośrednictwem prywatnego punktu końcowego. Aby to potwierdzić, sprawdź, czy aplikacja kliencka działa w podsieci, a istnieje prywatny punkt końcowy dla Event Grid tematu/domeny w tej podsieci.

[Usługa link prywatny platformy Azure](../private-link/private-link-overview.md) umożliwia dostęp do Azure Event Grid za pośrednictwem **prywatnego punktu końcowego** w sieci wirtualnej. Prywatny punkt końcowy to interfejs sieciowy, który nawiązuje połączenie prywatnie i bezpiecznie z usługą obsługiwanej przez link prywatny platformy Azure. Prywatny punkt końcowy używa prywatnego adresu IP z sieci wirtualnej, co skutecznie doprowadza usługę do sieci wirtualnej. Cały ruch do usługi może być kierowany przez prywatny punkt końcowy, dlatego nie są konieczne żadne bramy, urządzenia NAT, połączenia ExpressRoute lub sieci VPN ani publiczne adresy IP. Ruch między siecią wirtualną a usługą odbywa się za pośrednictwem sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu. Można nawiązać połączenie z wystąpieniem zasobu platformy Azure, zapewniając najwyższy poziom szczegółowości kontroli dostępu.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie prywatnych punktów końcowych](configure-private-endpoints.md).

## <a name="troubleshoot-transient-connectivity-issues"></a>Rozwiązywanie problemów z łącznością przejściową

Jeśli występują sporadyczne problemy z łącznością, w poniższych sekcjach znajdziesz wskazówki dotyczące rozwiązywania problemów.

### <a name="run-the-command-to-check-dropped-packets"></a>Uruchom polecenie, aby sprawdzić porzucone pakiety

Gdy występują sporadyczne problemy z łącznością, uruchom następujące polecenie, aby sprawdzić, czy pakiety pozostaną usunięte. To polecenie próbuje nawiązać 25 różnych połączeń TCP co 1 sekundę z usługą. Następnie można sprawdzić, ile z nich zakończyło się powodzeniem/niepowodzeniem, i zobaczyć także opóźnienia połączeń TCP. Możesz pobrać `psping` Narzędzie z tego [miejsca](/sysinternals/downloads/psping).

```shell
.\psping.exe -n 25 -i 1 -q {sampletopicname}.{region}-{suffix}.eventgrid.azure.net:443 -nobanner
```

Możesz użyć równoważnych poleceń, jeśli używasz innych narzędzi, takich jak `tcpping` [tcpping.exe](https://www.elifulkerson.com/projects/tcping.php).

Uzyskaj ślad sieci, jeśli poprzednie kroki nie pomagają i nie analizują go przy użyciu narzędzi, takich jak [Wireshark](https://www.wireshark.org/). W razie konieczności skontaktuj się z [Pomoc techniczna firmy Microsoft](https://support.microsoft.com/) .

### <a name="service-upgradesrestarts"></a>Uaktualnienia/ponowne uruchomienia usługi

Przejściowe problemy z łącznością mogą wystąpić z powodu uaktualnień i ponownych uruchomień usługi zaplecza. Gdy wystąpią, mogą pojawić się następujące objawy:

- Może wystąpić spadek przychodzących komunikatów/żądań.
- Plik dziennika może zawierać komunikaty o błędach.
- Aplikacje mogą być odłączone od usługi przez kilka sekund.
- Żądania mogą mieć na chwilę ograniczenie przepustowości.

Przechwycenie tych błędów przejściowych, tworzenie kopii zapasowych i ponawianie próby wywołania zapewni, że kod jest odporny na te problemy przejściowe.

## <a name="next-steps"></a>Następne kroki

Jeśli potrzebujesz więcej pomocy, Opublikuj swój problem na [forum Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) lub Otwórz [bilet pomocy technicznej](https://azure.microsoft.com/support/options/).
