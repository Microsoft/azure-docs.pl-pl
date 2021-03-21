---
title: Wdróż usługę przesyłania dalej dzienników, aby połączyć dane CEF z platformą Azure — wskaźnikiem rozwoju | Microsoft Docs
description: Dowiedz się, jak wdrożyć agenta, aby połączyć dane CEF z platformą Azure.
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
ms.openlocfilehash: a4303f43dffa98f842bd3daf9e3a0cd5214932b1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100585368"
---
# <a name="step-1-deploy-the-log-forwarder"></a>Krok 1. wdrażanie usługi przesyłania dalej dzienników


W tym kroku wyznaczysz i skonfigurujesz maszynę z systemem Linux, która będzie przekazywać dzienniki z rozwiązania zabezpieczeń do obszaru roboczego wskaźnikowego platformy Azure. Ta maszyna może być maszyną fizyczną lub wirtualną w środowisku lokalnym, maszyną wirtualną platformy Azure lub MASZYNą wirtualną w innej chmurze. Przy użyciu dostarczonego linku uruchomisz skrypt na wyznaczeniym komputerze, który wykonuje następujące zadania:

- Instaluje agenta Log Analytics dla systemu Linux (znanego również jako agent pakietu OMS) i konfiguruje go do następujących celów:
    - nasłuchiwanie komunikatów CEF z wbudowanego demona dziennika systemu Linux na porcie TCP 25226
    - bezpieczne wysyłanie komunikatów za pośrednictwem protokołu TLS do obszaru roboczego wskaźnikowego platformy Azure, w którym są analizowane i wzbogacane

- Konfiguruje wbudowane demona dziennika systemu Linux (rsyslog. d/dziennik systemowy) dla następujących celów:
    - nasłuchiwanie komunikatów dziennika systemowego z rozwiązań zabezpieczeń na porcie TCP 514
    - przekazywanie tylko komunikatów identyfikowanych jako CEF do agenta Log Analytics na hoście lokalnym przy użyciu portu TCP 25226
 
## <a name="prerequisites"></a>Wymagania wstępne

- Użytkownik musi mieć podwyższony poziom uprawnień (sudo) na wyznaczeniu maszyny z systemem Linux.

- Na komputerze z systemem Linux musi być zainstalowany język **python 2,7** lub **3** .<br>Użyj `python -version` polecenia, aby sprawdzić.

- Komputer z systemem Linux nie może być połączony z żadnym obszarem roboczym platformy Azure przed zainstalowaniem agenta Log Analytics.

- Maszyna z systemem Linux musi mieć co najmniej **4 rdzenie procesora i 8 GB pamięci RAM**.

    > [!NOTE]
    > - Pojedyncza maszyna usługi przesyłania dalej dzienników przy użyciu demona **rsyslog** ma obsługiwaną pojemność **do 8500 zdarzeń na sekundę (EPS)** .

- W pewnym momencie tego procesu może być potrzebny identyfikator obszaru roboczego i klucz podstawowy obszaru roboczego. Można je znaleźć w obszarze roboczym Zasoby, w obszarze **Zarządzanie agentami**.

## <a name="run-the-deployment-script"></a>Uruchamianie skryptu wdrażania
 
1. W menu nawigacji wskaźnikowej platformy Azure kliknij pozycję **Łączniki danych**. Z listy łączników kliknij kafelek **Common Event format (CEF)** , a następnie przycisk **Otwórz stronę łącznika** w prawym dolnym rogu. 

1. W obszarze **1,2 Zainstaluj moduł CEF na komputerze z systemem Linux**, skopiuj link podany w obszarze **Uruchom następujący skrypt, aby zainstalować i zastosować moduł zbierający CEF**, lub poniższy tekst (stosując identyfikator obszaru roboczego i klucz podstawowy zamiast symboli zastępczych):

    ```bash
    sudo wget -O cef_installer.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]
    ```

1. Gdy skrypt jest uruchomiony, upewnij się, że nie są wyświetlane żadne komunikaty o błędach lub ostrzeżeniach.
    - Może zostać wyświetlony komunikat informujący o konieczności uruchomienia polecenia w celu rozwiązania problemu z mapowaniem pola *komputer* . Aby uzyskać szczegółowe informacje, zobacz [wyjaśnienie w skrypcie wdrażania](#mapping-command) .

1. Przejdź do [kroku 2: Skonfiguruj rozwiązanie zabezpieczeń do przesyłania dalej komunikatów CEF](connect-cef-solution-config.md).


> [!NOTE]
> **Używanie tego samego komputera do przesyłania zarówno zwykłego dziennika systemowego *, jak i* komunikatów CEF**
>
> Jeśli planujesz używać tego komputera usługi przesyłania dalej dzienników do przesyłania dalej [komunikatów](connect-syslog.md) dziennika systemowego i CEF, a następnie w celu uniknięcia duplikowania zdarzeń do tabel dziennika systemowego i CommonSecurityLog:
>
> 1. Na każdym komputerze źródłowym, który wysyła dzienniki do usługi przesyłania dalej w formacie CEF, należy edytować plik konfiguracji dziennika systemowego w celu usunięcia obiektów używanych do wysyłania komunikatów CEF. W ten sposób obiekty, które są wysyłane w CEF, nie będą również wysyłane w dzienniku systemu. Szczegółowe instrukcje można znaleźć w temacie [Konfigurowanie dziennika systemowego w agencie systemu Linux](../azure-monitor/agents/data-sources-syslog.md#configure-syslog-on-linux-agent) .
>
> 1. Na tych maszynach należy uruchomić następujące polecenie, aby wyłączyć synchronizację agenta z konfiguracją dziennika systemowego na platformie Azure. Dzięki temu zmiana konfiguracji wprowadzona w poprzednim kroku nie zostanie zastępować.<br>
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

> [!NOTE]
> **Zmiana źródła pola TimeGenerated**
>
> - Domyślnie agent Log Analytics wypełnia pole *TimeGenerated* w schemacie, gdy agent otrzymał zdarzenie z demona dziennika systemowego. W rezultacie czas, w którym zdarzenie zostało wygenerowane w systemie źródłowym, nie jest rejestrowany w usłudze Azure Sentinel.
>
> - Można jednak uruchomić następujące polecenie, które spowoduje pobranie i uruchomienie `TimeGenerated.py` skryptu. Ten skrypt konfiguruje agenta Log Analytics, aby wypełnić pole *TimeGenerated* z oryginalnym czasem zdarzenia w jego systemie źródłowym, zamiast czasu odebranego przez agenta.
>
>    ```bash
>    wget -O TimeGenerated.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/TimeGenerated.py && python TimeGenerated.py {ws_id}
>    ```

## <a name="deployment-script-explained"></a>Opisany skrypt wdrożenia

Poniżej znajduje się opis poleceń wykonywanych przez polecenie skryptu wdrażania.

Wybierz demona dziennika systemu, aby wyświetlić odpowiedni opis.

# <a name="rsyslog-daemon"></a>[Demon rsyslog](#tab/rsyslog)

1. **Pobieranie i Instalowanie agenta Log Analytics:**

    - Pobiera skrypt instalacyjny dla agenta systemu Linux Log Analytics (OMS).

        ```bash
        wget -O onboard_agent.sh https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/
            master/installer/scripts/onboard_agent.sh
        ```

    - Instaluje agenta Log Analytics.
    
        ```bash
        sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com
        ```

1. **Ustawianie konfiguracji agenta Log Analytics do nasłuchiwania na porcie 25226 i przesyłania dalej komunikatów CEF do platformy Azure — wskaźnik:**

    - Pobiera konfigurację z repozytorium usługi GitHub Agent Log Analytics.

        ```bash
        wget -O /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf
            https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/
            omsagent.d/security_events.conf
        ```

1. **Konfigurowanie demona dziennika systemowego:**

    - Otwiera port 514 dla komunikacji TCP przy użyciu pliku konfiguracji dziennika systemowego `/etc/rsyslog.conf` .

    - Konfiguruje demona do przesyłania dalej komunikatów CEF do agenta Log Analytics na porcie TCP 25226, wstawiając specjalny plik konfiguracji `security-config-omsagent.conf` do katalogu demona dziennika systemu `/etc/rsyslog.d/` .

        Zawartość `security-config-omsagent.conf` pliku:

        ```bash
        if $rawmsg contains "CEF:" or $rawmsg contains "ASA-" then @@127.0.0.1:25226 
        ```

1. **Ponowne uruchamianie demona dziennika systemu i agenta Log Analytics:**

    - Uruchamia ponownie demona rsyslog.
    
        ```bash
        service rsyslog restart
        ```

    - Uruchamia ponownie agenta Log Analytics.

        ```bash
        /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. **Weryfikowanie mapowania pola *komputera* zgodnie z oczekiwaniami:**

    - Zapewnia, że pole komputera w źródle dziennika *systemu* jest prawidłowo mapowane w agencie log Analytics przy użyciu następującego polecenia: 

        ```bash
        grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
        ```
    - <a name="mapping-command"></a>Jeśli wystąpi problem z mapowaniem, skrypt wygeneruje komunikat o błędzie informujący o konieczności **ręcznego uruchomienia następującego polecenia** (zastosowanie identyfikatora obszaru roboczego zamiast symbolu zastępczego). Polecenie zapewni poprawne mapowanie i ponownie uruchamia agenta.
    
        ```bash
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

# <a name="syslog-ng-daemon"></a>[Demon dziennika systemu](#tab/syslogng)

1. **Pobieranie i Instalowanie agenta Log Analytics:**

    - Pobiera skrypt instalacyjny dla agenta systemu Linux Log Analytics (OMS).

        ```bash
        wget -O onboard_agent.sh https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/
            master/installer/scripts/onboard_agent.sh
        ```

    - Instaluje agenta Log Analytics.
    
        ```bash
        sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com
        ```

1. **Ustawianie konfiguracji agenta Log Analytics do nasłuchiwania na porcie 25226 i przesyłania dalej komunikatów CEF do platformy Azure — wskaźnik:**

    - Pobiera konfigurację z repozytorium usługi GitHub Agent Log Analytics.

        ```bash
        wget -O /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf
            https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/
            omsagent.d/security_events.conf
        ```

1. **Konfigurowanie demona dziennika systemowego:**

    - Otwiera port 514 dla komunikacji TCP przy użyciu pliku konfiguracji dziennika systemowego `/etc/syslog-ng/syslog-ng.conf` .

    - Konfiguruje demona do przesyłania dalej komunikatów CEF do agenta Log Analytics na porcie TCP 25226, wstawiając specjalny plik konfiguracji `security-config-omsagent.conf` do katalogu demona dziennika systemu `/etc/syslog-ng/conf.d/` .

        Zawartość `security-config-omsagent.conf` pliku:

        ```bash
        filter f_oms_filter {match(\"CEF\|ASA\" ) ;};destination oms_destination {tcp(\"127.0.0.1\" port(25226));};
        log {source(s_src);filter(f_oms_filter);destination(oms_destination);};
        ```

1. **Ponowne uruchamianie demona dziennika systemu i agenta Log Analytics:**

    - Uruchamia ponownie demona dziennika systemu.
    
        ```bash
        service syslog-ng restart
        ```

    - Uruchamia ponownie agenta Log Analytics.

        ```bash
        /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. **Weryfikowanie mapowania pola *komputera* zgodnie z oczekiwaniami:**

    - Zapewnia, że pole komputera w źródle dziennika *systemu* jest prawidłowo mapowane w agencie log Analytics przy użyciu następującego polecenia: 

        ```bash
        grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
        ```
    - <a name="mapping-command"></a>Jeśli wystąpi problem z mapowaniem, skrypt wygeneruje komunikat o błędzie informujący o konieczności **ręcznego uruchomienia następującego polecenia** (zastosowanie identyfikatora obszaru roboczego zamiast symbolu zastępczego). Polecenie zapewni poprawne mapowanie i ponownie uruchamia agenta.
    
        ```bash
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```
---

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób wdrażania agenta Log Analytics w celu połączenia urządzeń CEF z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](./tutorial-detect-threats-built-in.md).
