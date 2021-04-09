---
title: Połącz moduł zbierający zdarzenia zabezpieczeń Akamai z platformą Azure — wskaźnikiem | Microsoft Docs
description: Dowiedz się, jak używać łącznika zdarzeń zabezpieczeń Akamai do ściągania dzienników zabezpieczeń rozwiązań Akamai do platformy Azure. Wyświetlaj dane Akamai w skoroszytach, twórz Alerty i ulepszaj badanie.
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
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 8aa5a52a06713b4f00b43205a57148049a8ef8da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101711964"
---
# <a name="connect-your-akamai-security-events-collector-to-azure-sentinel"></a>Połącz moduł zbierający zdarzenia zabezpieczeń Akamai z platformą Azure — wskaźnikiem

> [!IMPORTANT]
> Łącznik zdarzeń zabezpieczeń Akamai jest obecnie w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.

W tym artykule wyjaśniono, jak połączyć moduł zbierający zdarzenia zabezpieczeń Akamai z platformą Azure. Łącznik danych zdarzeń zabezpieczeń Akamai umożliwia łatwe łączenie dzienników Akamai z platformą Azure, dzięki czemu można wyświetlać dane w skoroszytach, wysyłać zapytania do tworzenia niestandardowych alertów i uwzględniać je w celu usprawnienia badania. Integracja między modułem zbierającym zdarzenia zabezpieczeń Akamai i wskaźnikiem na platformie Azure oznacza korzystanie z dziennika systemowego z systemem CEF, usługi przesyłania dalej dzienników opartej na systemie Linux i agenta Log Analytics. Używa także analizatora dzienników utworzonego przez użytkownika w oparciu o funkcję Kusto.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć uprawnienia do odczytu i zapisu w obszarze roboczym wskaźnikowego platformy Azure.

- Użytkownik musi mieć uprawnienia do odczytu kluczy udostępnionych dla obszaru roboczego. [Dowiedz się więcej o kluczach obszarów roboczych](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

## <a name="send-akamai-security-events-logs-to-azure-sentinel"></a>Wysyłanie dzienników zdarzeń zabezpieczeń Akamai do usługi Azure wskaźnikowej

Aby pobrać swoje dzienniki do funkcji wskaźnikowej platformy Azure, skonfiguruj moduł zbierający zdarzenia zabezpieczeń Akamai, aby wysyłać komunikaty dziennika systemu w formacie CEF do serwera z przekazywaniem danych dzienników opartych na systemie Linux (z systemem rsyslog lub dziennikiem systemu). Na tym serwerze będzie zainstalowany agent Log Analytics, a agent przekazuje dzienniki do obszaru roboczego wskaźnikowego platformy Azure.

1. W menu nawigacji wskaźnikowej platformy Azure wybierz pozycję **Łączniki danych**.

1. W galerii **Łączniki danych** wybierz pozycję **Akamai Security Events (wersja zapoznawcza)**, a następnie **Otwórz stronę łącznik**.

1. Postępuj zgodnie z instrukcjami wyświetlanymi na karcie **instrukcje** w obszarze **Konfiguracja**:

    1. W obszarze **1. Konfiguracja agenta dziennika systemu Linux** — wykonaj ten krok, jeśli nie masz jeszcze uruchomionego usługi przesyłania dalej dzienników lub jeśli potrzebujesz innego. Zobacz [krok 1. wdrażanie usługi przesyłania dalej dzienników](connect-cef-agent.md) w dokumentacji usługi Azure wskaźnikowej w celu uzyskania informacji o rozmiarach, bardziej szczegółowych instrukcji i dokładniejszych wyjaśnień.

    1. W obszarze **2. Dzienniki przesyłania dalej Common Event format (CEF) do agenta dziennika** systemu — postępuj zgodnie z instrukcjami Akamai w celu [skonfigurowania integracji Siem](https://developer.akamai.com/tools/integrations/siem) i [skonfigurowania łącznika CEF](https://developer.akamai.com/tools/integrations/siem/siem-cef-connector). Ten łącznik odbiera zdarzenia zabezpieczeń z rozwiązań Akamai niemal w czasie rzeczywistym za pomocą OTWARTEgo interfejsu API SIEM i konwertuje je z formatu JSON na format CEF.
    
        Ta konfiguracja powinna obejmować następujące elementy:
    
        - Miejsce docelowe dziennika — nazwa hosta i/lub adres IP serwera przekazywania dzienników
        - Protokół i port — TCP 514 (w razie potrzeby w przeciwnym razie należy wprowadzić zmianę równoległą w demona dziennika systemowego na serwerze przesyłania dalej dzienników)
        - Format dziennika — CEF
        - Typy dzienników — wszystkie dostępne

    1. W obszarze **3. Sprawdź poprawność** pozyskiwania danych przez skopiowanie polecenia na stronie łącznika i uruchomienie go w usłudze przesyłania dalej dzienników. Zobacz [krok 3. Weryfikowanie łączności](connect-cef-verify.md) w dokumentacji usługi Azure wskaźnikowej, aby uzyskać bardziej szczegółowe instrukcje i wyjaśnienie.

        Rozpoczęcie wyświetlania dzienników w Log Analytics może potrwać do 20 minut.

## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia dane pojawiają się w **dziennikach** w sekcji **wskaźnik platformy Azure** w tabeli *CommonSecurityLog* .

Ten łącznik danych zależy od analizatora opartego na funkcji Kusto, aby działać zgodnie z oczekiwaniami. Wykonaj następujące kroki, aby skonfigurować funkcję **AkamaiSIEMEvent** Kusto do użycia w zapytaniach i skoroszytach.

1. Z menu nawigacyjnego wskaźnikowego platformy Azure wybierz pozycję **dzienniki**.

1. Skopiuj poniższe zapytanie i wklej je w oknie zapytania.
    ```kusto
    CommonSecurityLog 
    | where DeviceVendor == 'Akamai'
    | where DeviceProduct == 'akamai_siem'
    | extend EventVendor = 'Akamai'
    | extend EventProduct = 'akamai_siem'
    | extend EventProductVersion = '1.0'
    | extend EventId = DeviceEventClassID
    | extend EventCategory = Activity
    | extend EventSeverity = LogSeverity
    | extend DvcAction = DeviceAction
    | extend NetworkApplicationProtocol = ApplicationProtocol
    | extend Ipv6Src = DeviceCustomIPv6Address2
    | extend RuleName = DeviceCustomString1
    | extend RuleMessages = DeviceCustomString2
    | extend RuleData = DeviceCustomString3
    | extend RuleSelectors = DeviceCustomString4
    | extend ClientReputation = DeviceCustomString5
    | extend ApiId = DeviceCustomString6
    | extend RequestId = DevicePayloadId
    | extend DstDvcHostname = DestinationHostName
    | extend DstPortNumber = DestinationPort
    | extend ConfigId = FlexString1
    | extend PolicyId = FlexString2
    | extend NetworkBytes = SentBytes
    | extend UrlOriginal = RequestURL
    | extend HttpRequestMethod = RequestMethod
    | extend SrcIpAddr = SourceIP
    | extend EventStartTime = datetime(1970-01-01) + tolong(extract(@'.*start=(.*?);', 1, AdditionalExtensions)) * 1s 
    | extend SlowPostAction = extract(@'.*AkamaiSiemSlowPostAction=(.*?);', 1, AdditionalExtensions)
    | extend SlowPostRate = extract(@'.*AkamaiSiemSlowPostRate=(.*?);', 1, AdditionalExtensions)
    | extend RuleVersions = extract(@'.*AkamaiSiemRuleVersions=,?(.*?);', 1, AdditionalExtensions)
    | extend RuleTags = extract(@'.*AkamaiSiemRuleTags=(.*?);', 1, AdditionalExtensions)
    | extend ApiKey = extract(@'.*AkamaiSiemApiKey=(.*?);', 1, AdditionalExtensions)
    | extend Tls = extract(@'.*AkamaiSiemTLSVersion=(.*?);', 1, AdditionalExtensions)
    | extend RequestHeaders = extract(@'.*AkamaiSiemRequestHeaders=;?(.*?);', 1, AdditionalExtensions)
    | extend ResponseHeaders = extract(@'.*AkamaiSiemResponseHeaders=(.*?);', 1, AdditionalExtensions)
    | extend HttpStatusCode = extract(@'.*AkamaiSiemResponseStatus=(.*?);', 1, AdditionalExtensions)
    | extend GeoContinent = extract(@'.*AkamaiSiemContinent=(.*?);', 1, AdditionalExtensions)
    | extend SrcGeoCountry = extract(@'.*AkamaiSiemCountry=(.*?);', 1, AdditionalExtensions)
    | extend SrcGeoCity = extract(@'.*AkamaiSiemCity=(.*?);', 1, AdditionalExtensions)
    | extend SrcGeoRegion = extract(@'.*AkamaiSiemRegion=(.*?);', 1, AdditionalExtensions)
    | extend GeoAsn = extract(@'.*AkamaiSiemASN=(\d+)', 1, AdditionalExtensions)
    | extend Custom = extract(@'.*AkamaiSiemCusomData=(.*?)', 1, AdditionalExtensions)
    | project TimeGenerated
            , EventVendor
            , EventProduct
            , EventProductVersion
            , EventStartTime
            , EventId
            , EventCategory
            , EventSeverity
            , DvcAction
            , NetworkApplicationProtocol
            , Ipv6Src
            , RuleName
            , RuleMessages
            , RuleData
            , RuleSelectors
            , ClientReputation
            , ApiId
            , RequestId
            , DstDvcHostname
            , DstPortNumber
            , ConfigId
            , PolicyId
            , NetworkBytes
            , UrlOriginal
            , HttpRequestMethod
            , SrcIpAddr
            , SlowPostAction
            , SlowPostRate
            , RuleVersions
            , RuleTags
            , ApiKey
            , Tls
            , RequestHeaders
            , ResponseHeaders
            , HttpStatusCode
            , GeoContinent
            , SrcGeoCountry
            , SrcGeoCity
            , SrcGeoRegion
            , GeoAsn
            , Custom
    ```

1. Kliknij listę rozwijaną **Zapisz** , a następnie kliknij przycisk **Zapisz**. W panelu **zapisywania** ,

    1. W polu **Nazwa** wprowadź **AkamaiSIEMEvent**.

    1. W obszarze **Zapisz jako** wybierz pozycję **Funkcja**.

    1. W obszarze **alias funkcji** wprowadź **AkamaiSIEMEvent**.

    1. W obszarze **Kategoria** wpisz **funkcje**.

    1. Kliknij pozycję **Zapisz**.

    Aktywowanie aplikacji funkcji zwykle trwa od 10 do 15 minut.

Teraz możesz przystąpić do wykonywania zapytań dotyczących danych Akamai, wprowadzając `AkamaiSIEMEvent` w górnej linii okna zapytania.

Więcej przykładów zapytań można znaleźć na karcie **następne kroki** na stronie łącznika.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób łączenia zdarzeń zabezpieczeń Akamai z platformą Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.