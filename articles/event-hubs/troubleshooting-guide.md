---
title: Rozwiązywanie problemów z łącznością — Event Hubs platformy Azure | Microsoft Docs
description: Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z łącznością z usługą Azure Event Hubs.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: b85c0895d1c8f165f494d29013adea014187dd23
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87039331"
---
# <a name="troubleshoot-connectivity-issues---azure-event-hubs"></a>Rozwiązywanie problemów z łącznością — Event Hubs platformy Azure
Istnieją różne przyczyny, dla których aplikacje klienckie nie mogą połączyć się z centrum zdarzeń. Problemy z łącznością mogą być stałe lub przejściowe. Jeśli problem występuje cały czas (trwały), możesz chcieć sprawdzić parametry połączenia, ustawienia zapory w organizacji, ustawienia zapory protokołu IP, ustawienia zabezpieczeń sieci (punkty końcowe usługi, prywatne punkty końcowe itp.) i inne. W przypadku problemów przejściowych należy uaktualnić do najnowszej wersji zestawu SDK, uruchamiać polecenia służące do sprawdzania porzuconych pakietów i uzyskiwania śladów sieci, które mogą pomóc w rozwiązywaniu problemów. 

Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów z łącznością z usługą Azure Event Hubs. 

## <a name="troubleshoot-permanent-connectivity-issues"></a>Rozwiązywanie stałych problemów z łącznością
Jeśli aplikacja nie może połączyć się z centrum zdarzeń w ogóle, wykonaj kroki opisane w tej sekcji, aby rozwiązać problem. 

### <a name="check-if-there-is-a-service-outage"></a>Sprawdź, czy występuje awaria usługi
Wyszukaj awarię usługi Azure Event Hubs w [witrynie stanu usługi platformy Azure](https://azure.microsoft.com/status/).

### <a name="verify-the-connection-string"></a>Weryfikowanie parametrów połączenia 
Sprawdź, czy używane parametry połączenia są poprawne. Zobacz [pobieranie parametrów połączenia](event-hubs-get-connection-string.md) , aby uzyskać parametry połączenia przy użyciu Azure Portal, interfejsu wiersza polecenia lub programu PowerShell. 

W przypadku klientów Kafka upewnij się, że pliki producer.config lub consumer.config są prawidłowo skonfigurowane. Aby uzyskać więcej informacji, zobacz [wysyłanie i odbieranie komunikatów za pomocą Kafka w Event Hubs](event-hubs-quickstart-kafka-enabled-event-hubs.md#send-and-receive-messages-with-kafka-in-event-hubs).

### <a name="check-if-the-ports-required-to-communicate-with-event-hubs-are-blocked-by-organizations-firewall"></a>Sprawdź, czy porty wymagane do komunikacji z Event Hubs są blokowane przez zaporę organizacji
Sprawdź, czy porty używane podczas komunikowania się z platformą Azure Event Hubs nie są blokowane w zaporze organizacji. Zapoznaj się z poniższą tabelą dla portów wychodzących, które należy otworzyć, aby komunikować się z usługą Azure Event Hubs. 

| Protokół | Porty | Szczegóły | 
| -------- | ----- | ------- | 
| AMQP | 5671 i 5672 | Zobacz [Przewodnik po protokole AMQP](../service-bus-messaging/service-bus-amqp-protocol-guide.md) | 
| HTTP, HTTPS | 80, 443 |  |
| Kafka | 9093 | Zobacz [używanie Event Hubs z aplikacji Kafka](event-hubs-for-kafka-ecosystem-overview.md)

Oto przykładowe polecenie, które sprawdza, czy port 5671 jest zablokowany.

```powershell
tnc <yournamespacename>.servicebus.windows.net -port 5671
```

W systemie Linux:

```shell
telnet <yournamespacename>.servicebus.windows.net 5671
```

### <a name="verify-that-ip-addresses-are-allowed-in-your-corporate-firewall"></a>Sprawdź, czy adresy IP są dozwolone w zaporze firmowej
Podczas pracy z platformą Azure czasami trzeba zezwolić na określone zakresy adresów IP lub adresy URL w firmowej zaporze lub serwerze proxy, aby uzyskać dostęp do wszystkich usług platformy Azure, których używasz lub których próbujesz użyć. Sprawdź, czy ruch jest dozwolony dla adresów IP używanych przez Event Hubs. Adresy IP używane przez usługę Azure Event Hubs: zobacz [zakres adresów IP platformy Azure i Tagi usług — chmura publiczna](https://www.microsoft.com/download/details.aspx?id=56519).

Sprawdź również, czy adres IP dla przestrzeni nazw jest dozwolony. Aby znaleźć odpowiednie adresy IP w celu zezwolenia na połączenia, wykonaj następujące kroki:

1. Uruchom następujące polecenie w wierszu polecenia: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Zanotuj adres IP zwrócony w `Non-authoritative answer` . Jedyną zmianą jest to, że w przypadku przywracania przestrzeni nazw w innym klastrze.

Jeśli używasz nadmiarowości strefy dla przestrzeni nazw, musisz wykonać kilka dodatkowych czynności: 

1. Najpierw uruchom polecenie nslookup w przestrzeni nazw.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Zanotuj nazwę w sekcji **Nieautorytatywna odpowiedź** , która znajduje się w jednym z następujących formatów: 

    ```
    <name>-s1.cloudapp.net
    <name>-s2.cloudapp.net
    <name>-s3.cloudapp.net
    ```
3. Uruchom polecenie nslookup dla każdego z sufiksów S1, S2 i S3, aby uzyskać adresy IP wszystkich trzech wystąpień uruchomionych w trzech strefach dostępności. 

### <a name="verify-that-azureeventgrid-service-tag-is-allowed-in-your-network-security-groups"></a>Sprawdź, czy w sieciowej grupie zabezpieczeń jest dozwolony tag usługi AzureEventGrid
Jeśli aplikacja działa w podsieci i istnieje skojarzona sieciowa Grupa zabezpieczeń, sprawdź, czy jest dozwolony ruch wychodzący z Internetu, czy też jest dostępny tag usługi AzureEventGrid. Zobacz [Tagi usługi sieci wirtualnej](../virtual-network/service-tags-overview.md) i Wyszukaj `EventHub` .

### <a name="check-if-the-application-needs-to-be-running-in-a-specific-subnet-of-a-vnet"></a>Sprawdź, czy aplikacja musi być uruchomiona w określonej podsieci sieci wirtualnej
Upewnij się, że aplikacja działa w podsieci sieci wirtualnej, która ma dostęp do przestrzeni nazw. Jeśli tak nie jest, uruchom aplikację w podsieci, która ma dostęp do przestrzeni nazw lub Dodaj adres IP komputera, na którym aplikacja jest uruchomiona, do [zapory IP](event-hubs-ip-filtering.md). 

Podczas tworzenia punktu końcowego usługi sieci wirtualnej dla przestrzeni nazw centrum zdarzeń przestrzeń nazw akceptuje tylko ruch z podsieci powiązanej z punktem końcowym usługi. Wystąpił wyjątek dotyczący tego zachowania. Można dodać określone adresy IP w zaporze IP, aby umożliwić dostęp do publicznego punktu końcowego centrum zdarzeń. Aby uzyskać więcej informacji, zobacz [punkty końcowe usługi sieciowej](event-hubs-service-endpoints.md).

### <a name="check-the-ip-firewall-settings-for-your-namespace"></a>Sprawdź ustawienia zapory adresu IP dla swojej przestrzeni nazw
Sprawdź, czy publiczny adres IP komputera, na którym uruchomiona jest aplikacja, nie jest blokowany przez zaporę protokołu IP.  

Domyślnie obszary nazw Event Hubs są dostępne z Internetu, o ile żądanie zawiera prawidłowe uwierzytelnianie i autoryzację. Za pomocą zapory IP można ograniczyć ją tylko do zestawu adresów IPv4 lub zakresów adresów IPv4 w notacji [CIDR (bez klas Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

Reguły zapory adresów IP są stosowane na poziomie przestrzeni nazw Event Hubs. W związku z tym reguły są stosowane do wszystkich połączeń z klientów przy użyciu dowolnego obsługiwanego protokołu. Wszystkie próby połączenia z adresu IP, które nie pasują do dozwolonej reguły adresów IP w przestrzeni nazw Event Hubs, są odrzucane jako nieautoryzowane. Odpowiedź nie zawiera wzmianki o regule adresów IP. Reguły filtrowania adresów IP są stosowane w podanej kolejności, a pierwsza reguła zgodna z adresem IP określa akcję Akceptuj lub Odrzuć.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie reguł zapory adresów IP dla przestrzeni nazw platformy Azure Event Hubs](event-hubs-ip-filtering.md). Aby sprawdzić, czy występują problemy z filtrowaniem adresów IP, siecią wirtualną lub łańcuchem certyfikatów, zobacz [Rozwiązywanie problemów związanych z siecią](#troubleshoot-network-related-issues).

#### <a name="find-the-ip-addresses-blocked-by-ip-firewall"></a>Znajdź adresy IP blokowane przez zaporę IP
Włącz dzienniki diagnostyczne dla [zdarzeń połączeń sieci wirtualnej Event Hubs](event-hubs-diagnostic-logs.md#event-hubs-virtual-network-connection-event-schema) , postępując zgodnie z instrukcjami w temacie [Włączanie dzienników diagnostycznych](event-hubs-diagnostic-logs.md#enable-diagnostic-logs). Zostanie wyświetlony adres IP dla niedozwolonego połączenia.

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

### <a name="check-if-the-namespace-can-be-accessed-using-only-a-private-endpoint"></a>Sprawdź, czy można uzyskać dostęp do przestrzeni nazw tylko przy użyciu prywatnego punktu końcowego
Jeśli przestrzeń nazw Event Hubs jest skonfigurowana tak, aby była dostępna tylko za pośrednictwem prywatnego punktu końcowego, upewnij się, że aplikacja kliencka uzyskuje dostęp do przestrzeni nazw za pośrednictwem prywatnego punktu końcowego. 

[Usługa link prywatny platformy Azure](../private-link/private-link-overview.md) umożliwia dostęp do usługi Azure Event Hubs za pośrednictwem **prywatnego punktu końcowego** w sieci wirtualnej. Prywatny punkt końcowy to interfejs sieciowy, który nawiązuje połączenie prywatnie i bezpiecznie z usługą obsługiwanej przez link prywatny platformy Azure. Prywatny punkt końcowy używa prywatnego adresu IP z sieci wirtualnej, efektywnie przenosząc usługę do sieci wirtualnej. Cały ruch do usługi może być kierowany przez prywatny punkt końcowy, dlatego nie są konieczne żadne bramy, urządzenia NAT, połączenia ExpressRoute lub sieci VPN ani publiczne adresy IP. Ruch między siecią wirtualną a usługą odbywa się za pośrednictwem sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu. Można nawiązać połączenie z wystąpieniem zasobu platformy Azure, zapewniając najwyższy poziom szczegółowości kontroli dostępu.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie prywatnych punktów końcowych](private-link-service.md). Aby potwierdzić, że jest używany prywatny punkt końcowy, zobacz sekcję **Weryfikowanie działania połączenia prywatnego punktu końcowego** . 

### <a name="troubleshoot-network-related-issues"></a>Rozwiązywanie problemów związanych z siecią
Aby rozwiązać problemy związane z siecią przy użyciu Event Hubs, wykonaj następujące kroki: 

Przejdź do lub [Wget](https://www.gnu.org/software/wget/) `https://<yournamespacename>.servicebus.windows.net/` . Pomaga sprawdzić, czy masz problemy z filtrowaniem adresów IP lub siecią wirtualną lub łańcuchem certyfikatów (najbardziej typowe w przypadku korzystania z zestawu Java SDK).

Przykład **pomyślnego komunikatu**:

```xml
<feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
```

Przykładowy **komunikat o błędzie**błędu:

```json
<Error>
    <Code>400</Code>
    <Detail>
        Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
    </Detail>
</Error>
```

## <a name="troubleshoot-transient-connectivity-issues"></a>Rozwiązywanie problemów z łącznością przejściową
Jeśli występują sporadyczne problemy z łącznością, w poniższych sekcjach znajdziesz wskazówki dotyczące rozwiązywania problemów. 

### <a name="use-the-latest-version-of-the-client-sdk"></a>Użyj najnowszej wersji zestawu SDK klienta
Niektóre z przejściowych problemów z łącznością mogą zostać rozwiązane w nowszych wersjach zestawu SDK niż to, czego używasz. Upewnij się, że używasz najnowszej wersji zestawów SDK klienta w aplikacjach. Zestawy SDK są ciągle ulepszane dzięki nowym/zaktualizowanym funkcjom i poprawkach błędów, więc zawsze Testuj je przy użyciu najnowszego pakietu. Sprawdź informacje o wersji dotyczące problemów, które zostały naprawione i dodane/zaktualizowane. 

Aby uzyskać informacje na temat zestawów SDK klienta, zobacz artykuł [Azure Event Hubs — zestawy SDK klienta](sdks.md) . 

### <a name="run-the-command-to-check-dropped-packets"></a>Uruchom polecenie, aby sprawdzić porzucone pakiety
Gdy występują sporadyczne problemy z łącznością, uruchom następujące polecenie, aby sprawdzić, czy pakiety pozostaną usunięte. To polecenie próbuje nawiązać 25 różnych połączeń TCP co 1 sekundę z usługą. Następnie można sprawdzić, ile z nich zakończyło się powodzeniem/niepowodzeniem, i zobaczyć także opóźnienia połączeń TCP. Możesz pobrać `psping` Narzędzie z tego [miejsca](/sysinternals/downloads/psping).

```shell
.\psping.exe -n 25 -i 1 -q <yournamespacename>.servicebus.windows.net:5671 -nobanner     
```
Możesz użyć równoważnych poleceń, jeśli używasz innych narzędzi, takich jak `tnc` , `ping` , i tak dalej. 

Uzyskaj ślad sieci, jeśli poprzednie kroki nie pomagają i nie analizują go przy użyciu narzędzi, takich jak [Wireshark](https://www.wireshark.org/). W razie konieczności skontaktuj się z [Pomoc techniczna firmy Microsoft](https://support.microsoft.com/) . 

### <a name="service-upgradesrestarts"></a>Uaktualnienia/ponowne uruchomienia usługi
Przejściowe problemy z łącznością mogą wystąpić z powodu uaktualnień i ponownych uruchomień usługi zaplecza. Gdy wystąpią, mogą pojawić się następujące objawy: 

- Może wystąpić spadek przychodzących komunikatów/żądań.
- Plik dziennika może zawierać komunikaty o błędach.
- Aplikacje mogą być odłączone od usługi przez kilka sekund.
- Żądania mogą mieć na chwilę ograniczenie przepustowości.

Jeśli kod aplikacji korzysta z zestawu SDK, zasady ponawiania są już wbudowane i aktywne. Aplikacja będzie się łączyć ponownie bez znaczącego wpływu na aplikację/przepływ pracy. Przechwycenie tych błędów przejściowych, tworzenie kopii zapasowych i ponawianie próby wywołania zapewni, że kod jest odporny na te problemy przejściowe.

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

* [Rozwiązywanie problemów z uwierzytelnianiem i autoryzacją](troubleshoot-authentication-authorization.md)
