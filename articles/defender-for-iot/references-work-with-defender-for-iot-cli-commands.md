---
title: Korzystanie z poleceń interfejsu wiersza polecenia usługi Defender for IoT
description: W tym artykule opisano polecenia usługi Defender dla usługi IoT dla czujników i lokalnych konsol zarządzania.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/12/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 48c525004b095c1e0f498f86a5395d0002be26f1
ms.sourcegitcommit: 5ef018fdadd854c8a3c360743245c44d306e470d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/01/2021
ms.locfileid: "97845251"
---
# <a name="work-with-defender-for-iot-cli-commands"></a>Korzystanie z poleceń interfejsu wiersza polecenia usługi Defender for IoT

Ten artykuł zawiera opis poleceń interfejsu wiersza polecenia dla czujników i lokalnych konsol zarządzania. Polecenia są dostępne dla administratorów, użytkowników cyberx i użytkowników pomocy technicznej.

Definiowanie reguł wykluczeń podczas planowania działań konserwacyjnych lub działań, które nie wymagają alertu.

## <a name="create-local-alert-exclusion-rules"></a>Utwórz reguły wykluczania alertów lokalnych

Regułę wykluczania można utworzyć, wprowadzając następujące polecenie do interfejsu wiersza polecenia:

```azurecli-interactive
alerts exclusion-rule-create [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

Atrybuty, które można zdefiniować w ramach reguł wykluczenia alertu, są następujące:

| Atrybut | Opis |
|--|--|
| [-h] | Drukuje informacje pomocy dla polecenia. |
| -n nazwa | Nazwa tworzonej reguły. |
| [-TS TIMES] | Przedział czasu, dla którego reguła jest aktywna. Należy określić jako:<br />`xx:yy-xx:yy`<br />Można zdefiniować więcej niż jeden przedział czasu przy użyciu przecinka między nimi. Na przykład: `xx:yy-xx:yy, xx:yy-xx:yy`. |
| [-DIR — kierunek] | Kierunek zastosowania reguły. Należy określić jako:<br />`both | src | dst` |
| [-dev DEVICES] | Adres IP i typ adresu urządzeń, które mają być wykluczone przez regułę, określone jako:<br />`ip-x.x.x.x`<br />`mac-xx:xx:xx:xx:xx:xx`<br />`subnet: x.x.x.x/x` |
| [-a alerty] | Nazwa alertu, który zostanie wykluczony przez regułę:<br />`0x00000`<br />`0x000001` |

## <a name="append-local-alert-exclusion-rules"></a>Dołącz lokalne reguły wykluczenia alertu

Nowe reguły można dodać do bieżących reguł wykluczenia alertu, wprowadzając następujące polecenie w interfejsie wiersza polecenia:

```azurecli-interactive
alerts exclusion-rule-append [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

Używane tu atrybuty są podobne do atrybutów opisanych podczas tworzenia reguł wykluczania alertów lokalnych. W tym miejscu użycia atrybuty są stosowane do istniejących reguł.

## <a name="show-local-alert-exclusion-rules"></a>Pokaż reguły wykluczania alertów lokalnych

Wprowadź następujące polecenie, aby wyświetlić wszystkie istniejące reguły wykluczeń:

```azurecli-interactive
alerts exclusion-rule-list [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

## <a name="delete-local-alert-exclusion-rules"></a>Usuń reguły wykluczania alertów lokalnych

Istniejącą regułę wykluczania alertu można usunąć, wprowadzając następujące polecenie:

```azurecli-interactive
alerts exclusion-rule-remove [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

Można użyć następującego atrybutu z regułami wykluczenia alertu:

| Atrybut | Opis|
| --------- | ---------------------------------- |
| -n nazwa | Nazwa reguły, która ma zostać usunięta. |

## <a name="sync-time-from-the-ntp-server"></a>Czas synchronizacji z serwera NTP

Synchronizację czasu można włączyć i wyłączyć z poziomu serwera NTP.

### <a name="enable-ntp-sync"></a>Włącz synchronizację NTP

Wprowadzenie następującego polecenia umożliwi okresowe pobieranie bieżącego czasu z określonego serwera NTP:

```azurecli-interactive
ntp enable IP
```

Atrybut, który można zdefiniować w poleceniu jest adresem IP serwera NTP.

### <a name="disable-ntp-sync"></a>Wyłącz synchronizację NTP

Wprowadzenie następującego polecenia spowoduje wyłączenie synchronizacji czasu z określonym serwerem NTP:

```azurecli-interactive
ntp disable IP
```

Atrybut, który można zdefiniować w poleceniu jest adresem IP serwera NTP.

## <a name="configure-the-network"></a>Konfigurowanie sieci

W poniższej tabeli opisano polecenia dostępne w celu skonfigurowania opcji sieciowych dla usługi Azure Defender dla IoT:

|Nazwa|Polecenie|Opis|
|-----------|-------|-----------|
|Ping|`ping IP `| Wysyła polecenia ping do adresów spoza usługi Defender for IoT.|
|Blink|`network blink`|Włącza zmianę parametrów konfiguracji sieci.|
|Skonfiguruj ponownie sieć |`network edit-settings`| Włącza zmianę parametrów konfiguracji sieci. |
|Pokaż ustawienia sieci |`network list`|Wyświetla parametry karty sieciowej. |
|Weryfikowanie konfiguracji sieci |`network validate` |Przedstawia ustawienia wyjściowe sieci. <br /> <br />Na przykład: <br /> <br />Bieżące ustawienia sieci: <br /> Interfejs: eth0 <br /> adres IP: 10.100.100.1 <br />podsieć: 255.255.255.0 <br />Brama domyślna: 10.100.100.254 <br />DNS: 10.100.100.254 <br />Monitoruj interfejsy: eth1|
|Importowanie certyfikatu |`certificate import FILE` |Importuje certyfikat HTTPS. Należy określić pełną ścieżkę, która prowadzi do \* pliku. CRT. |
|Pokaż datę |`date` |Zwraca bieżącą datę na hoście w formacie GMT. |

## <a name="filter-network-configurations"></a>Filtruj konfiguracje sieci

`network capture-filter`Polecenie pozwala administratorom wyeliminować ruch sieciowy, który nie musi być analizowany. Filtrowanie ruchu przy użyciu listy dołączania lub listy wykluczeń.

```azurecli-interactive
network capture-filter
```

Po wprowadzeniu polecenia zostanie wyświetlony monit z następującym pytaniem:

>`Would you like to supply devices and subnet masks you wish to include in the capture filter? [Y/N]:`

Wybierz `Y` , aby otworzyć plik nano, w którym można dodać urządzenia, kanały, porty i podzestawy zgodnie z następującą składnią:

| Atrybut | Opis |
|--|--|
| 1.1.1.1 | Obejmuje cały ruch dla tego urządzenia. |
| 1.1.1.1, 2.2.2.2 | Obejmuje cały ruch dla tego kanału. |
| 1.1.1, 2.2.2 | Obejmuje cały ruch dla tej podsieci. |

Oddziel argumenty przez upuszczenie wiersza.

Po dołączeniu urządzenia, kanału lub podsieci czujnik przetwarza cały prawidłowy ruch dla tego argumentu, włącznie z portami i ruchem, który zwykle nie został przetworzony.

Następnie zostanie wyświetlony monit o podanie następujących danych:

>`Would you like to supply devices and subnet masks you wish to exclude from the capture filter? [Y/N]:`

Wybierz `Y` , aby otworzyć plik nano, w którym można dodać urządzenie, kanały, porty i podzestawy zgodnie z następującą składnią:

| Atrybut | Opis |
|--|--|
| 1.1.1.1 | Wyklucza cały ruch dla tego urządzenia. |
| 1.1.1.1, 2.2.2.2 | Wyklucza cały ruch dla tego kanału, co oznacza cały ruch między dwoma urządzeniami. |
| 1.1.1.1, 2.2.2.2, 443 | Wyklucza cały ruch dla tego kanału przez port. |
| 1.1.1 | Wyklucza cały ruch dla tej podsieci. |
| 1.1.1, 2.2.2 | Wyklucza cały ruch między podsieciami. |

Oddziel argumenty przez upuszczenie wiersza.

Po wykluczeniu urządzenia, kanału lub podsieci, czujnik wykluczy cały prawidłowy ruch dla tego argumentu.

### <a name="ports"></a>Porty

Uwzględnianie i wykluczanie portów UDP i TCP dla całego ruchu.

>`502`: pojedynczy port

>`502,443`: Oba porty

>`Enter tcp ports to include (delimited by comma or Enter to skip):`

>`Enter udp ports to include (delimited by comma or Enter to skip):`

>`Enter tcp ports to exclude (delimited by comma or Enter to skip):`

>`Enter udp ports to exclude (delimited by comma or Enter to skip):`

### <a name="components"></a>Składniki

Zostanie wyświetlony monit o podanie następujących danych:

>`In which component do you wish to apply this capture filter?`

Dostępne są następujące opcje:  `all` ,,,, `dissector` `collector` `statistics-collector` `rpc-parser` lub `smb-parser` .

W większości przypadków użycia wybierz opcję `all` .

### <a name="custom-base-capture-filter"></a>Niestandardowy filtr przechwytywania podstawowego

Podstawowy filtr przechwytywania jest punktem odniesienia dla składników. Na przykład filtr Określa, które porty są dostępne dla składnika.

Wybierz opcję `Y` dla wszystkich następujących opcji. Wszystkie filtry są dodawane do linii bazowej po ustawieniu zmian. Wprowadzenie zmiany spowoduje zastąpienie istniejącej linii bazowej.

>`Would you like to supply a custom base capture filter for the dissector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the collector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the statistics-collector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the rpc-parser component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the smb-parser component? [Y/N]:`

>`Type Y for "internal" otherwise N for "all-connected" (custom operation mode enabled) [Y/N]:`

Jeśli zdecydujesz się na wykluczenie podsieci, takiej jak 1.1.1:

- `internal` spowoduje wykluczenie tylko tej podsieci.

- `all-connected` wykluczają tę podsieć i cały ruch do i z tej podsieci.

Zalecamy wybranie opcji `internal` .

> [!NOTE]
> Wybrane opcje są używane dla wszystkich filtrów w narzędziu i nie są zależne od sesji. Innymi słowy, nie można kiedykolwiek wybierać `internal`   niektórych filtrów i  `all-connected`   dla innych.

### <a name="comments"></a>Komentarze

Filtry można wyświetlać w  ```/var/cyberx/properties/cybershark.properties``` :

- **Statystyka — moduł zbierający**:  `bpf_filter property` w ```/var/cyberx/properties/net.stats.collector.properties```

- **odsektorowanie**: `override.capture_filter`   Właściwość w ```/var/cyberx/properties/cybershark.properties```

- **RPC-parser**: `override.capture_filter` Właściwość w ```/var/cyberx/properties/rpc-parser.properties```

- **SMB-parser**:  `override.capture_filter`   Właściwość w ```/var/cyberx/properties/smb-parser.properties```

- **moduł zbierający**: `general.bpf_filter`   Właściwość w ```/var/cyberx/properties/collector.properties```

Konfigurację domyślną można przywrócić, wprowadzając następujący kod dla użytkownika cyberx:

```azurecli-interactive
sudo cyberx-xsense-capture-filter -p all -m all-connected
```

## <a name="define-client-and-server-hosts"></a>Definiowanie hostów klienta i serwera

Jeśli usługa Defender for IoT nie wykryje automatycznie hostów klienta i serwera, wprowadź następujące polecenie, aby ustawić hosta klienta i serwera:

```azurecli-interactive
directions [-h] [--identifier IDENTIFIER] [--port PORT] [--remove] [--add]  
[--tcp] [--udp]
```

Następujące atrybuty można użyć z `directions` poleceniem:

| Atrybut | Opis |
|--|--|
| [-h] | Drukuje informacje pomocy dla polecenia. |
| [--identyfikator identyfikatora] | Identyfikator serwera. |
| [--port portu] | Port serwera. |
| [--Usuń] | Usuwa klienta lub hosta serwera z listy. |
| [--Add] | Dodaje klienta lub hosta serwera do listy. |
| [--TCP] | Użyj protokołu TCP podczas komunikacji z tym hostem. |
| [--UDP] | Użyj protokołu UDP podczas komunikacji z tym hostem. |

## <a name="system-actions"></a>Akcje systemu
W poniższej tabeli opisano polecenia dostępne do wykonywania różnych działań systemowych w usłudze Defender dla IoT:

|Nazwa|Kod|Opis|
|----|----|-----------|
|Ponowne uruchamianie hosta|`system reboot`|Wykonuje ponowny rozruch urządzenia hosta.|
|Zamknij hosta|`system shutdown`|Zamyka hosta.|
|Tworzenie kopii zapasowej systemu|`system backup`|Inicjuje natychmiastową kopię zapasową (nieplanowaną kopię zapasową).|
|Przywracanie systemu z kopii zapasowej|`system restore`|Przywraca z najnowszej kopii zapasowej.|
|Utwórz listę plików kopii zapasowej|`system backup-list`|Wyświetla listę dostępnych plików kopii zapasowej.|
|Wyświetlanie stanu wszystkich usług Defender for IoT platform|`system sanity`|Sprawdza wydajność systemu, wyświetlając bieżący stan wszystkich usług platformy Defender dla usługi IoT.|
|Pokaż wersję oprogramowania|`system version`|Wyświetla wersję oprogramowania aktualnie uruchomionego w systemie.|

## <a name="deploy-ssl-and-tls-certificates-to-appliances"></a>Wdrażanie certyfikatów SSL i TLS na urządzeniach

Wprowadź następujące polecenie, aby zaimportować certyfikaty SSL i TLS przedsiębiorstwa do interfejsu wiersza polecenia:

```azurecli-interactive
cyberx-xsense-certificate-import
```
Aby użyć narzędzia, należy przekazać pliki certyfikatów na urządzenie. Można to zrobić za poorednictwem narzędzi, takich jak WinSCP lub Wget. 

Polecenie obsługuje następujące flagi wejściowe:

| Flaga | Opis |
|--|--|
| -h | Pokazuje składnię pomocy wiersza polecenia. |
| --CRT | Ścieżka do pliku certyfikatu (rozszerzenie CRT). |
| --klucz | \*Plik. Key. Długość klucza powinna wynosić co najmniej 2 048 bitów. |
| --łańcuch | Ścieżka do pliku łańcucha certyfikatów (opcjonalnie). |
| --Pass | Hasło używane do szyfrowania certyfikatu (opcjonalne). |
| --Ustawianie hasła | Wartość domyślna to **false**, **nieużywane**. <br />Ustaw **wartość true** , aby użyć poprzedniego hasła dostarczonego z poprzednim certyfikatem (opcjonalnie). |  |

Gdy używasz narzędzia:

- Sprawdź, czy pliki certyfikatów są odczytywane na urządzeniu. 

- Potwierdź, że jest ona domeną urządzenia (w postaci, w jakiej występuje w certyfikacie) z serwerem DNS i odpowiednim adresem IP. 
    
## <a name="see-also"></a>Zobacz także

[Interfejsy API usługi Defender dla czujnika i konsoli zarządzania usługi IoT](references-work-with-defender-for-iot-apis.md)
