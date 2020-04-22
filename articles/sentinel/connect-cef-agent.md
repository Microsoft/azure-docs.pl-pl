---
title: Wdrażanie usługi przesyłania dalej dziennika w celu połączenia danych cef z usługą Azure Sentinel | Dokumenty firmy Microsoft
description: Dowiedz się, jak wdrożyć agenta, aby połączyć dane CEF z usługą Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2020
ms.author: yelevin
ms.openlocfilehash: 5a8b97e5bef57b29f388c86628f0af5d05e1724a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731658"
---
# <a name="step-1-deploy-the-log-forwarder"></a>Krok 1: Wdrażanie usługi przesyłania dalej dziennika


W tym kroku wyznaczysz i skonfigurujesz komputer z systemem Linux, który przekaże dzienniki z rozwiązania zabezpieczeń do obszaru roboczego usługi Azure Sentinel. Ta maszyna może być maszyną fizyczną lub wirtualną w środowisku lokalnym, maszynie wirtualnej platformy Azure lub maszynie wirtualnej w innej chmurze. Korzystając z podanego łącza, na wyznaczonym komputerze zostanie uruchomiony skrypt, który wykonuje następujące zadania:
- Instaluje agenta usługi Log Analytics dla systemu Linux (znanego również jako agent OMS) i konfiguruje go do następujących celów:
    - nasłuchiwanie wiadomości CEF z wbudowanego demona Syslog Linux na porcie TCP 25226
    - bezpieczne wysyłanie wiadomości za pomocą protokołu TLS do obszaru roboczego usługi Azure Sentinel, gdzie są one analizowane i wzbogacane

- Konfiguruje wbudowany demon Syslog systemu Linux (rsyslog.d/syslog-ng) do następujących celów:
    - nasłuchiwanie komunikatów Syslog z rozwiązań zabezpieczających na porcie TCP 514
    - przekazywanie tylko wiadomości, które identyfikuje jako CEF do agenta usługi Log Analytics na stronie localhost przy użyciu portu TCP 25226
 
## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć podwyższone uprawnienia (sudo) na wyznaczonym komputerze z systemem Linux.
- Musisz mieć pythona zainstalowanego na komputerze z systemem Linux.<br>Za `python -version` pomocą polecenia można sprawdzić.
- Komputer z systemem Linux nie może być połączony z żadnymi obszarami roboczymi platformy Azure przed zainstalowaniem agenta usługi Log Analytics.

## <a name="run-the-deployment-script"></a>Uruchamianie skryptu wdrażania
 
1. Z menu nawigacji usługi Azure Sentinel kliknij polecenie **Łączniki danych**. Na liście łączników kliknij kafelek **Wspólny format zdarzeń (CEF),** a następnie przycisk **Otwórz stronę łącznika** w prawym dolnym dolnym czasie. 

1. W obszarze **1.2 Zainstaluj kolektor CEF na komputerze z systemem Linux,** skopiuj link podany w sekcji **Uruchom następujący skrypt, aby zainstalować i zastosować kolektor CEF**lub z poniższego tekstu:

     `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`

1. Gdy skrypt jest uruchomiony, sprawdź, czy nie otrzymasz żadnych komunikatów o błędzie ani ostrzeżeniach.

Przejdź do [kroku 2: Skonfiguruj rozwiązanie zabezpieczające do przesyłania dalej komunikatów CEF](connect-cef-solution-config.md) .

## <a name="deployment-script-explained"></a>Wyjaśniono skrypt wdrażania

Poniżej znajduje się opis poleceń akcji skryptu wdrażania.

Wybierz demona syslog, aby zobaczyć odpowiedni opis.

# <a name="rsyslog-daemon"></a>[demon rsyslog](#tab/rsyslog)

1. **Pobieranie i instalowanie agenta usługi Log Analytics:**

    - Pobiera skrypt instalacyjny dla agenta systemu Linux usługi Log Analytics (OMS)<br>
        `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh`

    - Instaluje agenta usługi Log Analytics<br>
        `sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com`

1. **Konfigurowanie demona Syslog:**

    1. Otwiera port 514 dla komunikacji TCP przy `/etc/rsyslog.conf`użyciu pliku konfiguracyjnego syslog .

    1. Konfiguruje demona do przesyłania dalej wiadomości CEF do agenta usługi Log Analytics na porcie `security-config-omsagent.conf` TCP 25226, `/etc/rsyslog.d/`wstawiając specjalny plik konfiguracyjny do katalogu demona syslog .

        Zawartość `security-config-omsagent.conf` pliku:

            :rawmsg, regex, "CEF\|ASA" ~
            *.* @@127.0.0.1:25226

1. **Ponowne uruchomienie demona Syslog**

    `service rsyslog restart`

1. **Ustawianie konfiguracji agenta usługi Log Analytics do nasłuchiwać na porcie 25226 i przesyłać dalej wiadomości CEF do usługi Azure Sentinel**

    1. Pobiera konfigurację z repozytorium GitHub agenta usługi Log Analytics<br>
        `wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/omsagent.d/security_events.conf`


    1. Uruchamia ponownie agenta usługi Log Analytics<br>
        `/opt/microsoft/omsagent/bin/service_control restart [workspaceID]`

# <a name="syslog-ng-daemon"></a>[demon syslog-ng](#tab/syslogng)

1. **Pobieranie i instalowanie agenta usługi Log Analytics:**

    - Pobiera skrypt instalacyjny dla agenta systemu Linux usługi Log Analytics (OMS)<br>`wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh`

    - Instaluje agenta usługi Log Analytics<br>`sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com`

1. **Konfigurowanie demona Syslog:**

    1. Otwiera port 514 dla komunikacji TCP przy `/etc/syslog-ng/syslog-ng.conf`użyciu pliku konfiguracyjnego syslog .

    1. Konfiguruje demona do przesyłania dalej wiadomości CEF do agenta usługi Log Analytics na porcie `security-config-omsagent.conf` TCP 25226, `/etc/syslog-ng/conf.d/`wstawiając specjalny plik konfiguracyjny do katalogu demona syslog .

        Zawartość `security-config-omsagent.conf` pliku:

            filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
            destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
            log {source(s_src);filter(f_oms_filter);destination(oms_destination);};

1. **Ponowne uruchomienie demona Syslog**

    `service syslog-ng restart`

1. **Ustawianie konfiguracji agenta usługi Log Analytics do nasłuchiwać na porcie 25226 i przesyłać dalej wiadomości CEF do usługi Azure Sentinel**

    1. Pobiera konfigurację z repozytorium GitHub agenta usługi Log Analytics<br>
        `wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/omsagent.d/security_events.conf`


    1. Uruchamia ponownie agenta usługi Log Analytics<br>
        `/opt/microsoft/omsagent/bin/service_control restart [workspaceID]`

---

## <a name="next-steps"></a>Następne kroki
W tym dokumencie dowiesz się, jak wdrożyć agenta usługi Log Analytics, aby połączyć urządzenia CEF z usługą Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:
- Dowiedz się, jak [uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)
- Rozpocznij [wykrywanie zagrożeń za pomocą usługi Azure Sentinel](tutorial-detect-threats.md).

