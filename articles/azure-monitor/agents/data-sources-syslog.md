---
title: Zbierz źródła danych dziennika systemowego za pomocą agenta Log Analytics w Azure Monitor
description: Dziennik systemowy to protokół rejestrowania zdarzeń, który jest wspólny dla systemu Linux. W tym artykule opisano sposób konfigurowania kolekcji komunikatów dziennika systemu w Log Analytics i szczegóły dotyczące tworzonych przez siebie rekordów.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/26/2021
ms.openlocfilehash: e82e74f4cd325444221bbd2e1c060b7cd2f5c6c7
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036739"
---
# <a name="collect-syslog-data-sources-with-log-analytics-agent"></a>Zbierz źródła danych dziennika systemowego za pomocą agenta Log Analytics
Dziennik systemowy to protokół rejestrowania zdarzeń, który jest wspólny dla systemu Linux. Aplikacje będą wysyłać komunikaty, które mogą być przechowywane na komputerze lokalnym lub dostarczane do modułu zbierającego dziennik systemowy. Po zainstalowaniu agenta Log Analytics dla systemu Linux program skonfiguruje lokalny demon dziennika systemowego, aby przekazywać komunikaty do agenta. Następnie Agent wysyła komunikat do Azure Monitor, w którym zostanie utworzony odpowiedni rekord.  

> [!IMPORTANT]
> W tym artykule opisano zbieranie zdarzeń dziennika systemu przy użyciu [agenta log Analytics](./log-analytics-agent.md) , który jest jednym z agentów używanych przez Azure monitor. Inni Agenci zbierają różne dane i są skonfigurowani inaczej. Zobacz [Omówienie agentów Azure monitor](../agents/agents-overview.md) , aby uzyskać listę dostępnych agentów oraz dane, które mogą zbierać.

> [!NOTE]
> Azure Monitor obsługuje zbieranie komunikatów wysyłanych przez rsyslog lub Dziennik systemowy, gdzie rsyslog jest demonem domyślnym. Domyślny demon dziennika systemowego w wersji 5 Red Hat Enterprise Linux, CentOS i Oracle Linux wersja (sysklog) nie jest obsługiwany w przypadku zbierania zdarzeń dziennika systemowego. Aby zebrać dane dziennika systemu z tej wersji dystrybucji, [demona rsyslog](http://rsyslog.com) powinna zostać zainstalowana i skonfigurowana do zastępowania sysklog.
>
>

![Kolekcja dziennika systemowego](media/data-sources-syslog/overview.png)

W module zbierającym dziennik systemu są obsługiwane następujące obiekty:

* Kerning
* użytkownik
* mail (poczta)
* Demon
* uwierzytelniania
* syslog
* usługę
* news
* uucp
* zaczyna
* authpriv
* ftp
* local0-local7

W przypadku każdej innej funkcji [Skonfiguruj niestandardowe źródło danych dzienników](data-sources-custom-logs.md) w Azure monitor.
 
## <a name="configuring-syslog"></a>Konfigurowanie dziennika systemowego
Agent Log Analytics dla systemu Linux będzie zbierać tylko zdarzenia z obiektami i serwerami, które są określone w jego konfiguracji. Dziennik systemowy można skonfigurować za pomocą Azure Portal lub przez zarządzanie plikami konfiguracji w agentach systemu Linux.

### <a name="configure-syslog-in-the-azure-portal"></a>Skonfiguruj dziennik systemowy w Azure Portal
Skonfiguruj dziennik systemowy z [menu konfiguracji agenta](../agents/agent-data-sources.md#configuring-data-sources) dla obszaru roboczego log Analytics. Ta konfiguracja jest dostarczana do pliku konfiguracji na każdym agencie systemu Linux.

Nową funkcję można dodać, klikając pozycję **Dodaj** funkcję. Dla każdej funkcji zbierane będą tylko komunikaty z wybranymi serwerami.  Sprawdź, czy w przypadku konkretnej funkcji mają być zbierane. Nie można podać żadnych dodatkowych kryteriów filtrowania komunikatów.

[![Konfigurowanie dziennika systemowego](media/data-sources-syslog/configure.png)](media/data-sources-syslog/configure.png#lightbox)

Domyślnie wszystkie zmiany konfiguracji są automatycznie wypychane do wszystkich agentów. Jeśli chcesz ręcznie skonfigurować dziennik systemu na każdym agencie systemu Linux, usuń zaznaczenie pola *Zastosuj poniżej konfiguracji do moich maszyn*.

### <a name="configure-syslog-on-linux-agent"></a>Konfigurowanie dziennika systemowego w agencie systemu Linux
Po [zainstalowaniu agenta log Analytics na kliencie z systemem Linux](../vm/quick-collect-linux-computer.md)instalowany jest domyślny plik konfiguracji dziennika systemowego, który określa zakres i ważność komunikatów, które są zbierane. Możesz zmodyfikować ten plik, aby zmienić konfigurację. Plik konfiguracji różni się w zależności od demona dziennika systemu zainstalowanego przez klienta programu.

> [!NOTE]
> W przypadku edytowania konfiguracji dziennika systemowego należy ponownie uruchomić demona dziennika systemu, aby zmiany zaczęły obowiązywać.
>
>

#### <a name="rsyslog"></a>rsyslog
Plik konfiguracji rsyslog znajduje się w lokalizacji **/etc/rsyslog.d/95-omsagent.conf**. Jego domyślna zawartość jest pokazana poniżej. W ten sposób zbierane są komunikaty dziennika systemowego wysyłane z agenta lokalnego dla wszystkich urządzeń z poziomem ostrzegawczym lub wyższym.

```config
kern.warning       @127.0.0.1:25224
user.warning       @127.0.0.1:25224
daemon.warning     @127.0.0.1:25224
auth.warning       @127.0.0.1:25224
syslog.warning     @127.0.0.1:25224
uucp.warning       @127.0.0.1:25224
authpriv.warning   @127.0.0.1:25224
ftp.warning        @127.0.0.1:25224
cron.warning       @127.0.0.1:25224
local0.warning     @127.0.0.1:25224
local1.warning     @127.0.0.1:25224
local2.warning     @127.0.0.1:25224
local3.warning     @127.0.0.1:25224
local4.warning     @127.0.0.1:25224
local5.warning     @127.0.0.1:25224
local6.warning     @127.0.0.1:25224
local7.warning     @127.0.0.1:25224
```

Można usunąć obiekt, usuwając jego sekcję w pliku konfiguracji. Można ograniczyć liczbę zbieranych danych dla konkretnej funkcji, modyfikując wpis tej funkcji. Na przykład, aby ograniczyć możliwości użytkownika do komunikatów o ważności błędu lub nowszej, należy zmodyfikować ten wiersz pliku konfiguracji na następujący:

```config
user.error    @127.0.0.1:25224
```

#### <a name="syslog-ng"></a>Dziennik systemowy — ng
Plik konfiguracji dla dziennika systemowego jest lokalizacją o godzinie **/etc/syslog-ng/syslog-ng.conf**.  Jego domyślna zawartość jest pokazana poniżej. W ten sposób zbierane są komunikaty dziennika systemowego wysyłane z agenta lokalnego dla wszystkich obiektów i wszystkich serwerów.   

```config
#
# Warnings (except iptables) in one file:
#
destination warn { file("/var/log/warn" fsync(yes)); };
log { source(src); filter(f_warn); destination(warn); };

#OMS_Destination
destination d_oms { udp("127.0.0.1" port(25224)); };

#OMS_facility = auth
filter f_auth_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(auth); };
log { source(src); filter(f_auth_oms); destination(d_oms); };

#OMS_facility = authpriv
filter f_authpriv_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(authpriv); };
log { source(src); filter(f_authpriv_oms); destination(d_oms); };

#OMS_facility = cron
filter f_cron_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(cron); };
log { source(src); filter(f_cron_oms); destination(d_oms); };

#OMS_facility = daemon
filter f_daemon_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(daemon); };
log { source(src); filter(f_daemon_oms); destination(d_oms); };

#OMS_facility = kern
filter f_kern_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(kern); };
log { source(src); filter(f_kern_oms); destination(d_oms); };

#OMS_facility = local0
filter f_local0_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local0); };
log { source(src); filter(f_local0_oms); destination(d_oms); };

#OMS_facility = local1
filter f_local1_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local1); };
log { source(src); filter(f_local1_oms); destination(d_oms); };

#OMS_facility = mail
filter f_mail_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(mail); };
log { source(src); filter(f_mail_oms); destination(d_oms); };

#OMS_facility = syslog
filter f_syslog_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(syslog); };
log { source(src); filter(f_syslog_oms); destination(d_oms); };

#OMS_facility = user
filter f_user_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(user); };
log { source(src); filter(f_user_oms); destination(d_oms); };
```

Można usunąć obiekt, usuwając jego sekcję w pliku konfiguracji. Można ograniczyć liczbę zbieranych danych dla konkretnej funkcji, usuwając je z listy.  Aby na przykład ograniczyć możliwości użytkownika tylko do alertów i komunikatów o krytycznym znaczeniu, należy zmodyfikować tę sekcję w pliku konfiguracji na następujące:

```config
#OMS_facility = user
filter f_user_oms { level(alert,crit) and facility(user); };
log { source(src); filter(f_user_oms); destination(d_oms); };
```

### <a name="collecting-data-from-additional-syslog-ports"></a>Zbieranie danych z dodatkowych portów dziennika systemu
Agent Log Analytics nasłuchuje komunikatów dziennika systemowego na kliencie lokalnym na porcie 25224.  Po zainstalowaniu agenta zostanie zastosowana domyślna konfiguracja dziennika systemowego i znaleziona w następującej lokalizacji:

* Rsyslog `/etc/rsyslog.d/95-omsagent.conf`
* Dziennik systemowy — NG: `/etc/syslog-ng/syslog-ng.conf`

Aby zmienić numer portu, można utworzyć dwa pliki konfiguracji: plik konfiguracji z systemem i plik rsyslog-lub-dziennika systemu w zależności od zainstalowanego demona dziennika systemowego.  

* Plik konfiguracji, który został pozostały, musi być nowym plikiem znajdującym się w lokalizacji: `/etc/opt/microsoft/omsagent/conf/omsagent.d` i zastąpić wartość w wpisie **portu** numerem portu niestandardowego.

    ```xml
    <source>
        type syslog
        port %SYSLOG_PORT%
        bind 127.0.0.1
        protocol_type udp
        tag oms.syslog
    </source>
    <filter oms.syslog.**>
        type filter_syslog
    ```

* W przypadku rsyslog należy utworzyć nowy plik konfiguracji znajdujący się w: `/etc/rsyslog.d/` i zastąpić wartość% SYSLOG_PORT% numerem portu niestandardowego.  

    > [!NOTE]
    > Jeśli zmodyfikujesz tę wartość w pliku konfiguracji `95-omsagent.conf` , zostanie ona nadpisywana, gdy Agent zastosuje konfigurację domyślną.
    >

    ```config
    # OMS Syslog collection for workspace %WORKSPACE_ID%
    kern.warning              @127.0.0.1:%SYSLOG_PORT%
    user.warning              @127.0.0.1:%SYSLOG_PORT%
    daemon.warning            @127.0.0.1:%SYSLOG_PORT%
    auth.warning              @127.0.0.1:%SYSLOG_PORT%
    ```

* Konfigurację dziennika systemowego-NG należy zmodyfikować przez skopiowanie przykładowej konfiguracji pokazanej poniżej i dodanie niestandardowych ustawień zmodyfikowanych na końcu pliku konfiguracji dziennika systemowego — ng. conf znajdującego się w temacie `/etc/syslog-ng/` . **Nie** Używaj domyślnej etykiety **% WORKSPACE_ID% _oms** lub **% WORKSPACE_ID_OMS**, zdefiniuj etykietę niestandardową, aby ułatwić odróżnienie zmian.  

    > [!NOTE]
    > Jeśli zmodyfikujesz wartości domyślne w pliku konfiguracji, zostaną one nadpisywane, gdy Agent zastosuje konfigurację domyślną.
    >

    ```config
    filter f_custom_filter { level(warning) and facility(auth; };
    destination d_custom_dest { udp("127.0.0.1" port(%SYSLOG_PORT%)); };
    log { source(s_src); filter(f_custom_filter); destination(d_custom_dest); };
    ```

Po wprowadzeniu zmian należy ponownie uruchomić dziennik systemowy i usługę agenta Log Analytics, aby upewnić się, że zmiany konfiguracji zostały wprowadzone.   

## <a name="syslog-record-properties"></a>Właściwości rekordu dziennika systemowego
Rekordy dziennika systemu mają typ **dziennika** systemowego i mają właściwości w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| Computer (Komputer) |Komputer, z którego zostało zebrane zdarzenie. |
| Urządzenia |Definiuje część systemu, która wygenerowała komunikat. |
| HostIP |Adres IP systemu, w którym jest wysyłany komunikat. |
| HostName |Nazwa systemu wysyłającego komunikat. |
| SeverityLevel |Poziom ważności zdarzenia. |
| SyslogMessage |Tekst komunikatu. |
| ProcessId |Identyfikator procesu, który wygenerował komunikat. |
| EventTime |Data i godzina wygenerowania zdarzenia. |

## <a name="log-queries-with-syslog-records"></a>Rejestruj zapytania przy użyciu rekordów dziennika systemowego
W poniższej tabeli przedstawiono różne przykłady zapytań dzienników, które pobierają rekordy dziennika systemowego.

| Zapytanie | Opis |
|:--- |:--- |
| Dziennik systemu |Wszystkie dziennik systemowy. |
| Dziennik systemowy &#124;, gdzie SeverityLevel = = "Error" |Wszystkie rekordy dziennika systemu o ważności błędu. |
| Dziennik systemowy &#124; podsumowujący AggregatedValue = Count () według komputera |Liczba rekordów dziennika systemowego według komputera. |
| Dziennik systemowy &#124; Sumuj AggregatedValue = Count () według funkcji |Liczba rekordów dziennika systemowego według funkcji. |

## <a name="next-steps"></a>Następne kroki
* Informacje na temat [zapytań dzienników](../logs/log-query-overview.md) w celu analizowania danych zebranych ze źródeł danych i rozwiązań.
* [Pola niestandardowe](../logs/custom-fields.md) służą do analizowania danych z rekordów dziennika systemowego do poszczególnych pól.
* [Skonfiguruj agentów systemu Linux](../vm/quick-collect-linux-computer.md) do zbierania innych typów danych.