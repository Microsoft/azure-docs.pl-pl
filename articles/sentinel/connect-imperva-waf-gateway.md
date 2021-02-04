---
title: Podłącz urządzenie bramy WAF Imperva do usługi Azure wskaźnikowej | Microsoft Docs
description: Dowiedz się, jak używać łącznika bramy WAF Imperva do ściągania dzienników Imperva WAF do platformy Azure. Wyświetlaj dane Imperva WAF w skoroszytach, twórz Alerty i ulepszaj badanie.
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
ms.openlocfilehash: 3094d20a921f9aa13e111e7af60955ce934b91db
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99566803"
---
# <a name="connect-your-imperva-waf-gateway-appliance-to-azure-sentinel"></a>Połącz urządzenie bramy WAF Imperva z platformą Azure — wskaźnikiem

> [!IMPORTANT]
> Łącznik bramy Imperva WAF jest obecnie w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.

W tym artykule wyjaśniono, jak podłączyć urządzenie bramy usługi Imperva WAF do centrum danych platformy Azure. Łącznik danych bramy Imperva WAF umożliwia łatwe łączenie dzienników bramy Imperva WAF z platformą Azure, dzięki czemu można wyświetlać dane w skoroszytach, używać ich do tworzenia alertów niestandardowych i włączania ich w celu usprawnienia badania. Integracja między bramą Imperva WAF i wskaźnikiem na platformie Azure oznacza korzystanie z dziennika systemowego z systemem CEF, usługi przesyłania dalej dzienników opartej na systemie Linux i agenta Log Analytics.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć uprawnienia do odczytu i zapisu w obszarze roboczym wskaźnikowego platformy Azure.

- Użytkownik musi mieć uprawnienia do odczytu kluczy udostępnionych dla obszaru roboczego. [Dowiedz się więcej o kluczach obszarów roboczych](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key).

## <a name="send-imperva-waf-gateway-logs-to-azure-sentinel"></a>Wysyłanie dzienników bramy Imperva WAF do usługi Azure wskaźnikowej

Aby pobrać swoje dzienniki do centrum danych platformy Azure, Skonfiguruj urządzenie bramy Imperva WAF do wysyłania komunikatów dziennika systemowego w formacie CEF do serwera z przekazywaniem danych dzienników opartych na systemie Linux (z systemem rsyslog lub dziennikiem systemu). Na tym serwerze będzie zainstalowany agent Log Analytics, a agent przekazuje dzienniki do obszaru roboczego wskaźnikowego platformy Azure.

1. W menu nawigacji wskaźnikowej platformy Azure wybierz pozycję **Łączniki danych**.

1. W galerii **łączników danych** wybierz pozycję **Imperva WAF Gateway (wersja zapoznawcza)**, a następnie **Otwórz stronę łącznik**.

1. Postępuj zgodnie z instrukcjami wyświetlanymi na karcie **instrukcje** w obszarze **Konfiguracja**:

    1. W obszarze **1. Konfiguracja agenta dziennika systemu Linux** — wykonaj ten krok, jeśli nie masz jeszcze uruchomionego usługi przesyłania dalej dzienników lub jeśli potrzebujesz innego. Zobacz [krok 1. wdrażanie usługi przesyłania dalej dzienników](connect-cef-agent.md) w dokumentacji usługi Azure wskaźnikowej w celu uzyskania bardziej szczegółowych instrukcji i wyjaśnień.

    1. W obszarze **2. Przekazywanie dzienników usługi Common Event format (CEF) do agenta dziennika** systemu — ten łącznik wymaga, aby **interfejs akcji** i **zestaw akcji** zostały utworzone w konsoli zarządzania **Imperva SecureSphere MX** . Postępuj zgodnie z instrukcjami Imperva, aby [włączyć rejestrowanie alertów bramy IMPERVA WAF na platformie Azure](https://community.imperva.com/blogs/craig-burlingame1/2020/11/13/steps-for-enabling-imperva-waf-gateway-alert). Ta konfiguracja powinna obejmować następujące elementy:
        - Miejsce docelowe dziennika — nazwa hosta i/lub adres IP serwera przekazywania dzienników
        - Protokół i port — TCP 514
        - Format dziennika — CEF
        - Typy dzienników — wszystkie dostępne

    1. W obszarze **3. Sprawdź poprawność** pozyskiwania danych przez skopiowanie polecenia na stronie łącznika i uruchomienie go w usłudze przesyłania dalej dzienników. Zobacz [krok 3. Weryfikowanie łączności](connect-cef-verify.md) w dokumentacji usługi Azure wskaźnikowej, aby uzyskać bardziej szczegółowe instrukcje i wyjaśnienie.

        Rozpoczęcie wyświetlania dzienników w Log Analytics może potrwać do 20 minut.

## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia dane pojawiają się w **dziennikach** w sekcji **wskaźnik platformy Azure** w tabeli *CommonSecurityLog* .

Aby wykonać zapytanie o dane bramy Imperva WAF w Log Analytics, Skopiuj następujące elementy do okna zapytania, stosując inne filtry w miarę wyboru:

```kusto
CommonSecurityLog 
| where DeviceVendor == "Imperva Inc." 
| where DeviceProduct == "WAF Gateway" 
| where TimeGenerated == ago(5m)
```

Zobacz kartę **następne kroki** na stronie łącznik, aby poznać przydatne przykłady zapytań.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób nawiązywania połączenia z bramą Imperva WAF Gateway z platformą Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.
