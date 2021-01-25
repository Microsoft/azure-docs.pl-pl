---
title: Sprawdź poprawność łączności z platformą Azure — wskaźnikiem | Microsoft Docs
description: Sprawdź poprawność łączności rozwiązania zabezpieczeń, aby upewnić się, że komunikaty CEF są przekazywane do usługi Azure wskaźnikowej.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2021
ms.author: yelevin
ms.openlocfilehash: f9fb1c917a0719cb9d250b997329d3415b5872eb
ms.sourcegitcommit: 3c8964a946e3b2343eaf8aba54dee41b89acc123
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98747478"
---
# <a name="step-3-validate-connectivity"></a>Krok 3. Weryfikowanie łączności

Po wdrożeniu usługi przesyłania dalej dzienników (w kroku 1) i skonfigurowaniu rozwiązania zabezpieczającego do wysyłania komunikatów CEF (w kroku 2) postępuj zgodnie z tymi instrukcjami, aby sprawdzić łączność między rozwiązaniem zabezpieczeń a wskaźnikiem kontroli platformy Azure. 

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć podwyższony poziom uprawnień (sudo) na komputerze usługi przesyłania dalej dzienników.

- Na komputerze usługi przesyłania dalej dzienników musi być zainstalowany język **python 2,7** lub **3** .<br>
Użyj `python –version` polecenia, aby sprawdzić.

- W pewnym momencie tego procesu może być potrzebny identyfikator obszaru roboczego i klucz podstawowy obszaru roboczego. Można je znaleźć w obszarze roboczym Zasoby, w obszarze **Zarządzanie agentami**.

## <a name="how-to-validate-connectivity"></a>Sprawdzanie poprawności łączności

1. W menu nawigacyjnym usługi Azure wskaźnikowym Otwórz pozycję **dzienniki**. Uruchom zapytanie przy użyciu schematu **CommonSecurityLog** , aby zobaczyć, czy otrzymujesz dzienniki z rozwiązania zabezpieczeń.<br>
Należy pamiętać, że może upłynąć około 20 minut, dopóki dzienniki nie pojawią się w **log Analytics**. 

1. Jeśli nie widzisz żadnych wyników zapytania, sprawdź, czy zdarzenia są generowane z rozwiązania zabezpieczeń, lub spróbuj wygenerować niektóre i sprawdź, czy są przekazywane do wyszukanego komputera usługi przesyłania dalej dziennika systemowego. 

1. Uruchom następujący skrypt w usłudze przesyłania dalej dzienników (stosując identyfikator obszaru roboczego zamiast symbolu zastępczego), aby sprawdzić łączność między rozwiązaniem zabezpieczeń, usługą przesyłania dalej dzienników i wskaźnikiem kontroli platformy Azure. Ten skrypt sprawdza, czy demon nasłuchuje na prawidłowych portach, że przekazywanie jest prawidłowo skonfigurowane i że nic nie blokuje komunikacji między demonem a agentem Log Analytics. Wysyła również komunikat "TestCommonEventFormat", który umożliwia sprawdzenie kompleksowej łączności. <br>

    ```bash
    sudo wget -O cef_troubleshoot.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]
    ```

   - Może zostać wyświetlony komunikat informujący o konieczności uruchomienia polecenia w celu rozwiązania problemu z **mapowaniem pola *komputer***. Aby uzyskać szczegółowe informacje, zobacz [wyjaśnienie w skrypcie walidacji](#mapping-command) .

    - Może zostać wyświetlony komunikat informujący o konieczności uruchomienia polecenia w celu rozwiązania problemu z **analizą dzienników zapory Cisco ASA**. Aby uzyskać szczegółowe informacje, zobacz [wyjaśnienie w skrypcie walidacji](#parsing-command) .

## <a name="validation-script-explained"></a>Wyjaśniono skrypt walidacji

Skrypt walidacji wykonuje następujące sprawdzenia:

# <a name="rsyslog-daemon"></a>[Demon rsyslog](#tab/rsyslog)

1. Sprawdza, czy plik<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    istnieje i jest prawidłowy.

1. Sprawdza, czy plik zawiera następujący tekst:

    ```bash
    <source>
        type syslog
        port 25226
        bind 127.0.0.1
        protocol_type tcp
        tag oms.security
        format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
        <parse>
            message_format auto
        </parse>
    </source>

    <filter oms.security.**>
        type filter_syslog_security
    </filter>
    ```

1. Sprawdza, czy analiza zdarzeń zapory Cisco ASA jest skonfigurowana zgodnie z oczekiwaniami przy użyciu następującego polecenia: 

    ```bash
    grep -i "return ident if ident.include?('%ASA')" /opt/microsoft/omsagent/plugin/security_lib.rb
    ```

    - <a name="parsing-command"></a>Jeśli wystąpi problem z analizą, w skrypcie zostanie wyświetlony komunikat o błędzie informujący o konieczności **ręcznego uruchomienia następującego polecenia** (zastosowanie identyfikatora obszaru roboczego zamiast symbolu zastępczego). Polecenie zapewni poprawne analizowanie i ponowne uruchomienie agenta.
    
        ```bash
        # Cisco ASA parsing fix
        sed -i "s|return '%ASA' if ident.include?('%ASA')|return ident if ident.include?('%ASA')|g" /opt/microsoft/omsagent/plugin/security_lib.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. Sprawdza, czy pole komputer w źródle dziennika *systemu* jest prawidłowo mapowane w agencie log Analytics przy użyciu następującego polecenia: 

    ```bash
    grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
    ```

    - <a name="mapping-command"></a>Jeśli wystąpi problem z mapowaniem, skrypt wygeneruje komunikat o błędzie informujący o konieczności **ręcznego uruchomienia następującego polecenia** (zastosowanie identyfikatora obszaru roboczego zamiast symbolu zastępczego). Polecenie zapewni poprawne mapowanie i ponownie uruchamia agenta.

        ```bash
        # Computer field mapping fix
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. Sprawdza, czy na komputerze znajdują się jakieś ulepszenia zabezpieczeń, które mogą blokować ruch sieciowy (na przykład zaporę hosta).

1. Sprawdza, czy demon dziennika systemu (rsyslog) jest prawidłowo skonfigurowany do wysyłania komunikatów (identyfikowanych jako CEF) do agenta Log Analytics na porcie TCP 25226:

    - Plik konfiguracji: `/etc/rsyslog.d/security-config-omsagent.conf`

        ```bash
        if $rawmsg contains "CEF:" or $rawmsg contains "ASA-" then @@127.0.0.1:25226 
        ```

1. Uruchamia ponownie demona dziennika systemu i agenta Log Analytics:

    ```bash
    service rsyslog restart

    /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. Sprawdza, czy zostały ustanowione niezbędne połączenia: TCP 514 do odbioru danych, TCP 25226 w celu komunikacji wewnętrznej między demonem dziennika systemowego a agentem Log Analytics:

    ```bash
    netstat -an | grep 514

    netstat -an | grep 25226
    ```

1. Sprawdza, czy demon dziennika systemowego otrzymuje dane na porcie 514 i czy Agent otrzymuje dane na porcie 25226:

    ```bash
    sudo tcpdump -A -ni any port 514 -vv

    sudo tcpdump -A -ni any port 25226 -vv
    ```

1. Wysyła dane MAKIETy do portu 514 na hoście lokalnym. Te dane powinny być zauważalne w obszarze roboczym wskaźnik platformy Azure, uruchamiając następujące zapytanie:

    ```kusto
    CommonSecurityLog
    | where DeviceProduct == "MOCK"
    ```

# <a name="syslog-ng-daemon"></a>[Demon dziennika systemu](#tab/syslogng)

1. Sprawdza, czy plik<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    istnieje i jest prawidłowy.

1. Sprawdza, czy plik zawiera następujący tekst:

    ```bash
    <source>
        type syslog
        port 25226
        bind 127.0.0.1
        protocol_type tcp
        tag oms.security
        format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
        <parse>
            message_format auto
        </parse>
    </source>

    <filter oms.security.**>
        type filter_syslog_security
    </filter>
    ```

1. Sprawdza, czy analiza zdarzeń zapory Cisco ASA jest skonfigurowana zgodnie z oczekiwaniami przy użyciu następującego polecenia: 

    ```bash
    grep -i "return ident if ident.include?('%ASA')" /opt/microsoft/omsagent/plugin/security_lib.rb
    ```

    - <a name="parsing-command"></a>Jeśli wystąpi problem z analizą, w skrypcie zostanie wyświetlony komunikat o błędzie informujący o konieczności **ręcznego uruchomienia następującego polecenia** (zastosowanie identyfikatora obszaru roboczego zamiast symbolu zastępczego). Polecenie zapewni poprawne analizowanie i ponowne uruchomienie agenta.
    
        ```bash
        # Cisco ASA parsing fix
        sed -i "s|return '%ASA' if ident.include?('%ASA')|return ident if ident.include?('%ASA')|g" /opt/microsoft/omsagent/plugin/security_lib.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. Sprawdza, czy pole komputer w źródle dziennika *systemu* jest prawidłowo mapowane w agencie log Analytics przy użyciu następującego polecenia: 

    ```bash
    grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
    ```

    - <a name="mapping-command"></a>Jeśli wystąpi problem z mapowaniem, skrypt wygeneruje komunikat o błędzie informujący o konieczności **ręcznego uruchomienia następującego polecenia** (zastosowanie identyfikatora obszaru roboczego zamiast symbolu zastępczego). Polecenie zapewni poprawne mapowanie i ponownie uruchamia agenta.

        ```bash
        # Computer field mapping fix
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. Sprawdza, czy na komputerze znajdują się jakieś ulepszenia zabezpieczeń, które mogą blokować ruch sieciowy (na przykład zaporę hosta).

1. Sprawdza, czy demon dziennika systemowego (Dziennik systemowy) jest prawidłowo skonfigurowany do wysyłania komunikatów identyfikowanych jako CEF (przy użyciu wyrażenia regularnego) do agenta Log Analytics na porcie TCP 25226:

    - Plik konfiguracji: `/etc/syslog-ng/conf.d/security-config-omsagent.conf`

        ```bash
        filter f_oms_filter {match(\"CEF\|ASA\" ) ;};destination oms_destination {tcp(\"127.0.0.1\" port(25226));};
        log {source(s_src);filter(f_oms_filter);destination(oms_destination);};
        ```

1. Uruchamia ponownie demona dziennika systemu i agenta Log Analytics:

    ```bash
    service syslog-ng restart

    /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. Sprawdza, czy zostały ustanowione niezbędne połączenia: TCP 514 do odbioru danych, TCP 25226 w celu komunikacji wewnętrznej między demonem dziennika systemowego a agentem Log Analytics:

    ```bash
    netstat -an | grep 514

    netstat -an | grep 25226
    ```

1. Sprawdza, czy demon dziennika systemowego otrzymuje dane na porcie 514 i czy Agent otrzymuje dane na porcie 25226:

    ```bash
    sudo tcpdump -A -ni any port 514 -vv

    sudo tcpdump -A -ni any port 25226 -vv
    ```

1. Wysyła dane MAKIETy do portu 514 na hoście lokalnym. Te dane powinny być zauważalne w obszarze roboczym wskaźnik platformy Azure, uruchamiając następujące zapytanie:

    ```kusto
    CommonSecurityLog
    | where DeviceProduct == "MOCK"
    ```
---

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób łączenia urządzeń CEF z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](./tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.
