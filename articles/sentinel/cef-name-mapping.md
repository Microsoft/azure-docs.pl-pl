---
title: Klucz CEF (Common Event format) i Mapowanie pól CommonSecurityLog
description: Ten artykuł mapuje klucze CEF do odpowiednich nazw pól w CommonSecurityLog na platformie Azure.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: reference
ms.date: 03/16/2021
ms.openlocfilehash: 6c23fe86af030d371e12914062bb9558e8db3484
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104776302"
---
# <a name="cef-and-commonsecuritylog-field-mapping"></a>CEF i CommonSecurityLog, Mapowanie pól

Poniższe tabele mapują nazwy pól Common Event format (CEF) na nazwy, które są używane na platformie Azure — CommonSecurityLog i mogą być przydatne podczas pracy ze źródłem danych CEF na platformie Azure.

Aby uzyskać więcej informacji, zobacz [łączenie rozwiązania zewnętrznego przy użyciu typowego formatu zdarzeń](connect-common-event-format.md).

## <a name="a---c"></a>A–C

|Nazwa klucza CEF  |Nazwa pola CommonSecurityLog  |Opis  |
|---------|---------|---------|
| wykonywać    |    <a name="deviceaction"></a> DeviceAction     |  Akcja wymieniona w zdarzeniu.       |
|   aplikacja  |    ApplicationProtocol     |  Protokół używany w aplikacji, taki jak HTTP, HTTPS, SSHv2, Telnet, POP, IMPA, IMAP itd.   |
| CNT    |    EventCount     |  Liczba skojarzona ze zdarzeniem, pokazująca, ile razy zostało zaobserwowane to samo zdarzenie.       |
| | | |

## <a name="d"></a>D

|Nazwa klucza CEF  |Nazwa CommonSecurityLog  |Opis  |
|---------|---------|---------|
|Dostawca urządzenia     |  DeviceVendor       | Ciąg, który wraz z produktami i definicjami wersji urządzenia jednoznacznie identyfikuje typ urządzenia wysyłającego.       |
|Produkt urządzenia     |   DeviceProduct      |   Ciąg, który wraz z definicją dostawcy i wersji urządzenia jednoznacznie identyfikuje typ urządzenia wysyłającego.        |
|Wersja urządzenia     |   DeviceVersion      |      Ciąg, który wraz z definicją produktu i dostawcy urządzenia jednoznacznie identyfikuje typ urządzenia wysyłającego.     |
|DeviceEventClassID     |   DeviceEventClassID     |   Ciąg lub liczba całkowita, która służy jako unikatowy identyfikator dla każdego typu zdarzenia.      |
| destinationDnsDomain    | DestinationDnsDomain        |   Część DNS w pełni kwalifikowanej nazwy domeny (FQDN).      |
| destinationServiceName | DestinationServiceName | Usługa, która jest przeznaczona dla zdarzenia. Na przykład `sshd`.|
| destinationTranslatedAddress | DestinationTranslatedAddress | Identyfikuje przetłumaczone miejsce docelowe, do którego odwołuje się zdarzenie w sieci IP, jako adres IP IPv4. |
| destinationTranslatedPort | DestinationTranslatedPort | Port po translacji, taki jak Zapora. <br>Prawidłowe numery portów: `0` - `65535` |
| deviceDirection | <a name="communicationdirection"></a> CommunicationDirection | Wszelkie informacje o kierunku trwania komunikacji obserwowanej. Prawidłowe wartości: <br>- `0` = Przychodzące <br>- `1` = Wychodzące |
| deviceDnsDomain | DeviceDnsDomain | Część domeny DNS w pełni kwalifikowana nazwa domeny (FQDN) |
| deviceExternalID | DeviceExternalID | Nazwa, która jednoznacznie identyfikuje urządzenie generujące zdarzenie. |
| deviceFacility | DeviceFacility | Obiekt generujący zdarzenie.|
| deviceInboundInterface | DeviceInboundInterface |Interfejs, w którym pakiet lub dane przeszedł do urządzenia.  |
| deviceNtDomain | DeviceNtDomain | Domena systemu Windows adresu urządzenia |
| deviceOutboundInterface | DeviceOutboundInterface |Interfejs, w którym pakiet lub dane zostały pozostawione na urządzeniu. |
| devicePayloadId |DevicePayloadId |Unikatowy identyfikator ładunku skojarzonego ze zdarzeniem. |
| deviceProcessName | ProcessName | Nazwa procesu skojarzona ze zdarzeniem. <br><br>Na przykład w systemie UNIX proces generuje wpis dziennika systemowego. |
| deviceTranslatedAddress | DeviceTranslatedAddress | Identyfikuje przetłumaczony adres urządzenia, do którego odwołuje się zdarzenie, w sieci IP. <br><br>Format jest adresem IPv4. |
| dhost |DestinationHostName | Miejsce docelowe, do którego odwołuje się zdarzenie w sieci IP.  <br>Format powinien być nazwą FQDN skojarzoną z węzłem docelowym, gdy węzeł jest dostępny. Na przykład: `host.domain.com` lub `host`. |
| dmac | DestinationMacAddress | Docelowy adres MAC (FQDN) |
| dntdom | DestinationNTDomain | Nazwa domeny systemu Windows adresu docelowego.|
| dpid | DestinationProcessId |Identyfikator procesu docelowego skojarzony ze zdarzeniem.|
| dpriv | DestinationUserPrivileges | Definiuje uprawnienia do użycia miejsca docelowego. <br>Prawidłowe wartości: `Admninistrator` , `User` , `Guest` |
| dproc | DestinationProcessName | Nazwa procesu docelowego zdarzenia, taka jak `telnetd` lub `sshd.` |
| dpt | DestinationPort | Port docelowy. <br>Prawidłowe wartości: `*0` - `65535` |
| czas | DestinationIP | Docelowy adres IpV4, do którego odwołuje się zdarzenie w sieci IP. |
| dtz | DeviceTimeZon | Strefa czasowa generująca zdarzenie |
| identyfikatora |DestinationUserId | Identyfikuje użytkownika docelowego według identyfikatora. |
| duser | DestinationUserName |Identyfikuje użytkownika docelowego według nazwy.|
| współbieżne | DeviceAddress | Adres IPv4 urządzenia generującego zdarzenie. |
| dvchost | DeviceName | Nazwa FQDN skojarzona z węzłem urządzenia, gdy węzeł jest dostępny. Na przykład: `host.domain.com` lub `host`.| 
| dvcmac | DeviceMacAddress | Adres MAC urządzenia generującego zdarzenie. |
| dvcpid | Identyfikator procesu | Określa identyfikator procesu na urządzeniu generującym zdarzenie. |

## <a name="e---i"></a>E-I

|Nazwa klucza CEF  |Nazwa CommonSecurityLog  |Opis  |
|---------|---------|---------|
|end     |  EndTime       | Czas zakończenia działania związanego ze zdarzeniem.        |
|externalId    |   ExternalID      | Identyfikator używany przez urządzenie źródłowe. Zazwyczaj te wartości mają rosnące wartości, które są skojarzone ze zdarzeniem.        |
|fileCreateTime     |  FileCreateTime      | Czas utworzenia pliku.        |
|fileHash     |   Skrót pliku      |   Skrót pliku.      |
|fileId     |   FileID      |Identyfikator skojarzony z plikiem, taki jak inode.         |
| fileModificationTime | FileModificationTime |Godzina ostatniej modyfikacji pliku. |
| Parametr | Parametr | Pełna ścieżka do pliku, łącznie z nazwą pliku. Na przykład: `C:\ProgramFiles\WindowsNT\Accessories\wordpad.exe` lub `/usr/bin/zip` .|
| filePermission |FilePermission |Uprawnienia pliku. |
| Typ | Typ | Typ pliku, taki jak potok, gniazdo i tak dalej.|
|fname | Nazwa pliku| Nazwa pliku bez ścieżki. |
| dyrektywy | Rozmiar pliku | Rozmiar pliku. |
|Host    |  Computer (Komputer)       | Host, z dziennika systemowego        |
|in     |  ReceivedBytes      |Liczba bajtów przetransferowanych do ruchu przychodzącego.         |
| | | |

## <a name="m---p"></a>M-P

|Nazwa klucza CEF  |Nazwa CommonSecurityLog  |Opis  |
|---------|---------|---------|
|msg   |  Komunikat       | Komunikat, który daje więcej szczegółowych informacji o zdarzeniu.        |
|Nazwa     |  Działanie       |   Ciąg, który reprezentuje czytelny dla człowieka opis zdarzenia.     |
|oldFileCreateTime     |  OldFileCreateTime       | Godzina utworzenia starego pliku.        |
|oldFileHash     |   OldFileHash      |   Skrót starego pliku.      |
|oldFileId     |   OldFileId     |   I identyfikator skojarzony ze starym plikiem, taki jak inode.      |
| oldFileModificationTime | OldFileModificationTime |Godzina ostatniej modyfikacji starego pliku. |
| oldFileName |  OldFileName |Nazwa starego pliku. |
| oldFilePath | OldFilePath | Pełna ścieżka do starego pliku, łącznie z nazwą pliku. <br>Na przykład: `C:\ProgramFiles\WindowsNT\Accessories\wordpad.exe` lub `/usr/bin/zip`.|
| oldFilePermission | OldFilePermission |Uprawnienia starego pliku. |
|oldFileSize | OldFileSize | Rozmiar starego pliku.|
| oldFileType | OldFileType | Typ pliku starego pliku, na przykład potok, gniazdo i tak dalej.|
| out | SentBytes | Liczba bajtów przetransferowanych przez wychodzące. |
| Wynik | Wynik | Wynik zdarzenia, taki jak `success` lub `failure` .|
|proto    |  Protokół       | Protokół transportowy, który identyfikuje używany protokół warstwy 4. <br><br>Możliwe wartości to nazwy protokołów, takie jak `TCP` lub `UDP` .        |
| | | |

## <a name="r---t"></a>R-T

|Nazwa klucza CEF  |Nazwa CommonSecurityLog  |Opis  |
|---------|---------|---------|
|Przyczyna     |  Przyczyna      |Przyczyna wygenerowania zdarzenia inspekcji. <br><br>Na przykład: `Bad password` lub `Unknown user`.         |
|Żądanie     |   RequestURL      | Adres URL, do którego jest uzyskiwany dostęp do żądania HTTP, łącznie z protokołem. Na przykład `http://www/secure.com`        |
|requestClientApplication     |   RequestClientApplication      |   Agent użytkownika skojarzony z żądaniem.      |
| requestContext | RequestContext | Opisuje zawartość, z której pochodzi żądanie, na przykład odwołujący HTTP. |
| requestCookies | RequestCookies |Pliki cookie skojarzone z żądaniem. |
| requestMethod | RequestMethod | Metoda używana do uzyskiwania dostępu do adresu URL. <br><br>Prawidłowe wartości to metody `POST` , `GET` , i tak dalej. |
| RT | ReceiptTime | Godzina, o której otrzymano zdarzenie związane z działaniem. |
|Ważność     |  <a name="logseverity"></a> LogSeverity       |  Ciąg lub liczba całkowita, która opisuje ważność zdarzenia.<br><br> Prawidłowe wartości ciągu: `Unknown` , `Low` , `Medium` , `High` , `Very-High` <br><br>Prawidłowe wartości całkowite to: `0` - `3` = Low, `4` - `6` = medium, `7` - `8` = High, `9` - `10` = Very-High |
| shost    | SourceHostName        |Określa źródło, do którego odwołuje się zdarzenie w sieci IP. Format powinien być w pełni kwalifikowaną nazwą domeny (DQDN) skojarzoną z węzłem źródłowym, gdy węzeł jest dostępny. Na przykład: `host` lub `host.domain.com`. |
| smac | SourceMacAddress | Źródłowy adres MAC. |
| sntdom | SourceNTDomain | Nazwa domeny systemu Windows dla adresu źródłowego. |
| sourceDnsDomain | SourceDnsDomain | Część domeny DNS pełnej nazwy FQDN. |
| sourceServiceName | SourceServiceName | Usługa odpowiedzialna za generowanie zdarzenia. |
| sourceTranslatedAddress | SourceTranslatedAddress | Identyfikuje przetłumaczone źródło, do którego odwołuje się zdarzenie w sieci IP. |
| sourceTranslatedPort | SourceTranslatedPort | Port źródłowy po przetłumaczeniu, taki jak Zapora. <br>Prawidłowe numery portów to `0`  -  `65535` . |
| SPID | SourceProcessId | Identyfikator procesu źródłowego skojarzony ze zdarzeniem.|
| spriv | SourceUserPrivileges | Uprawnienia użytkownika źródłowego. <br><br>Prawidłowe wartości to: `Administrator` , `User` , `Guest` |
| sproc | SourceProcessName | Nazwa procesu źródłowego zdarzenia.|
| spt | SourcePort | Numer portu źródłowego. <br>Prawidłowe numery portów to `0`  -  `65535` . |
| src | SourceIP |Źródło, do którego odwołuje się zdarzenie w sieci IP jako adres IPv4. |
| start | StartTime | Czas rozpoczęcia działania, do którego odwołuje się zdarzenie. |
| suid | SourceUserID | Identyfikuje użytkownika źródłowego według identyfikatora. |
| typ | Typ zdarzenia | Typ zdarzenia. Wartości wartości to: <br>- `0`: zdarzenie podstawowe <br>- `1`: zagregowane <br>- `2`: zdarzenie korelacji <br>- `3`: zdarzenie akcji <br><br>**Uwaga**: to zdarzenie można pominąć dla zdarzeń podstawowych. |
| | | |

## <a name="unmapped-fields"></a>Niezamapowane pola

Następujące nazwy pól **CommonSecurityLog** nie mają mapowań w kluczach CEF:


|Nazwa pola CommonSecurityLog  |Opis  |
|---------|---------|
|**OriginalLogSeverity**     |  Zawsze puste, obsługiwane dla integracji z usługą CiscoASA. <br>Aby uzyskać szczegółowe informacje o ważności dziennika, zobacz pole [LogSeverity](#logseverity) .       |
|**RemoteIP**     |     Zdalny adres IP. <br>Ta wartość jest oparta na polu [CommunicationDirection](#communicationdirection) (jeśli to możliwe).     |
|**RemotePort**     |   Port zdalny. <br>Ta wartość jest oparta na polu [CommunicationDirection](#communicationdirection) (jeśli to możliwe).      |
|**SimplifiedDeviceAction**     |   Upraszcza wartość [DeviceAction](#deviceaction) do statycznego zestawu wartości, pozostawiając pierwotną wartość w polu [DeviceAction](#deviceaction) . <br>Na przykład: `Denied`  >  `Deny` .      |
|     |         |


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [łączenie rozwiązania zewnętrznego przy użyciu typowego formatu zdarzeń](connect-common-event-format.md).
