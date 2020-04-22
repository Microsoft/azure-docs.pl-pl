---
title: Sprawdzanie poprawności łączności z usługą Azure Sentinel | Dokumenty firmy Microsoft
description: Sprawdź poprawność łączności rozwiązania zabezpieczeń, aby upewnić się, że wiadomości CEF są przekazywane do usługi Azure Sentinel.
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
ms.openlocfilehash: 6b91e36ee09aa855c119add2c0eb268cf8b97393
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731833"
---
# <a name="step-3-validate-connectivity"></a>KROK 3: Sprawdzanie poprawności łączności

Po wdrożeniu usługi przesyłania dalej dziennika (w kroku 1) i skonfigurowaniu rozwiązania zabezpieczeń do wysyłania wiadomości cef (w kroku 2), postępuj zgodnie z tymi instrukcjami, aby zweryfikować łączność między rozwiązaniem zabezpieczeń a usługą Azure Sentinel. 

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć podwyższone uprawnienia (sudo) na komputerze z usługi przesyłania dalej dziennika.

- Musisz mieć zainstalowany język Python na komputerze z przesyłaczam dalej dziennikiem.<br>
Za `python –version` pomocą polecenia można sprawdzić.

## <a name="how-to-validate-connectivity"></a>Jak sprawdzić poprawność łączności

1. Z menu nawigacji usługi Azure Sentinel otwórz **dzienniki**. Uruchom kwerendę przy użyciu schematu **CommonSecurityLog,** aby sprawdzić, czy otrzymujesz dzienniki z rozwiązania zabezpieczeń.<br>
Należy pamiętać, że może upłynąć około 20 minut, aż dzienniki zaczną pojawiać się w **usłudze Log Analytics.** 

1. Jeśli nie widzisz żadnych wyników z kwerendy, sprawdź, czy zdarzenia są generowane z rozwiązania zabezpieczeń lub spróbuj wygenerować niektóre z nich i sprawdź, czy są przekazywane do wyznaczonego komputera usługi przesyłania dalej Syslog. 

1. Uruchom następujący skrypt na usługi przesyłania dalej dziennika, aby sprawdzić łączność między rozwiązaniem zabezpieczeń, usługą przesyłania dalej dziennika i usługą Azure Sentinel. Ten skrypt sprawdza, czy demon nasłuchuje na odpowiednich portach, czy przekazywanie jest poprawnie skonfigurowane i że nic nie blokuje komunikacji między demonem a agentem usługi Log Analytics. Wysyła również makiety wiadomości "TestCommonEventFormat", aby sprawdzić łączność end-to-end. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`

## <a name="validation-script-explained"></a>Objaśnienie skryptu sprawdzania poprawności

Skrypt sprawdzania poprawności wykonuje następujące kontrole:

# <a name="rsyslog-daemon"></a>[demon rsyslog](#tab/rsyslog)

1. Sprawdza, czy plik<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    istnieje i jest prawidłowa.

1. Sprawdza, czy plik zawiera następujący tekst:

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

1. Sprawdza, czy na komputerze są jakieś ulepszenia zabezpieczeń, które mogą blokować ruch sieciowy (na przykład zapora hosta).

1. Sprawdza, czy demon syslogu (rsyslog) jest poprawnie skonfigurowany do wysyłania wiadomości, które identyfikuje jako CEF (przy użyciu wyrażenia regularnego) do agenta usługi Log Analytics na porcie TCP 25226:

    - Plik konfiguracyjny:`/etc/rsyslog.d/security-config-omsagent.conf`

            :rawmsg, regex, "CEF\|ASA" ~
            *.* @@127.0.0.1:25226

1. Sprawdza, czy demon sysloga odbiera dane na porcie 514

1. Sprawdza, czy są ustanawiane niezbędne połączenia: tcp 514 do odbierania danych, tcp 25226 dla komunikacji wewnętrznej między demonem syslog a agentem usługi Log Analytics

1. Wysyła dane MOCK do portu 514 na localhost. Te dane powinny być obserwowalne w obszarze roboczym usługi Azure Sentinel, uruchamiając następującą kwerendę:

        CommonSecurityLog
        | where DeviceProduct == "MOCK"

# <a name="syslog-ng-daemon"></a>[demon syslog-ng](#tab/syslogng)

1. Sprawdza, czy plik<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    istnieje i jest prawidłowa.

1. Sprawdza, czy plik zawiera następujący tekst:

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

1. Sprawdza, czy na komputerze są jakieś ulepszenia zabezpieczeń, które mogą blokować ruch sieciowy (na przykład zapora hosta).

1. Sprawdza, czy demon syslog (syslog-ng) jest poprawnie skonfigurowany do wysyłania wiadomości, które identyfikuje jako CEF (przy użyciu wyrażenia regularnego) do agenta usługi Log Analytics na porcie TCP 25226:

    - Plik konfiguracyjny:`/etc/syslog-ng/conf.d/security-config-omsagent.conf`

            filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
            destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
            log {source(s_src);filter(f_oms_filter);destination(oms_destination);};

1. Sprawdza, czy demon sysloga odbiera dane na porcie 514

1. Sprawdza, czy są ustanawiane niezbędne połączenia: tcp 514 do odbierania danych, tcp 25226 dla komunikacji wewnętrznej między demonem syslog a agentem usługi Log Analytics

1. Wysyła dane MOCK do portu 514 na localhost. Te dane powinny być obserwowalne w obszarze roboczym usługi Azure Sentinel, uruchamiając następującą kwerendę:

        CommonSecurityLog
        | where DeviceProduct == "MOCK"

---

## <a name="next-steps"></a>Następne kroki
W tym dokumencie dowiesz się, jak połączyć urządzenia CEF z usługą Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:
- Dowiedz się, jak [uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)
- Rozpocznij [wykrywanie zagrożeń za pomocą usługi Azure Sentinel](tutorial-detect-threats.md).
- Monitorowanie danych [za pomocą skoroszytów.](tutorial-monitor-your-data.md)

