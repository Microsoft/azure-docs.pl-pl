---
title: Łączenie Trend Micro TippingPoint z platformą Azure — wskaźnikiem Microsoft Docs
description: Dowiedz się, w jaki sposób używać łącznika danych usługi Trend Micro TippingPoint do ściągania dzienników programu SMS na platformie Azure. Wyświetlaj dane TippingPoint w skoroszytach, twórz Alerty i ulepszaj badanie.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/12/2021
ms.author: yelevin
ms.openlocfilehash: 5c7491a0e0ba2a3bf604988c613e1fd8937f277d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98752179"
---
# <a name="connect-your-trend-micro-tippingpoint-solution-to-azure-sentinel"></a>Połącz swoje rozwiązanie Trend Micro TippingPoint z platformą Azure — wskaźnikiem

> [!IMPORTANT]
> Łącznik Trend Micro TippingPoint jest obecnie w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.

W tym artykule wyjaśniono, jak połączyć rozwiązanie systemu ochrony przed zagrożeniami w usłudze Trend Micro TippingPoint z platformą Azure. Łącznik danych Trend Micro TippingPoint pozwala łatwo połączyć Dzienniki systemu zarządzania bezpieczeństwem (SMS) z systemem Azure, dzięki czemu można wyświetlać dane w skoroszytach, używać ich do tworzenia niestandardowych alertów i uwzględniania ich w celu usprawnienia badania.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć uprawnienia do odczytu i zapisu w obszarze roboczym wskaźnikowego platformy Azure.

- Użytkownik musi mieć uprawnienia do odczytu kluczy udostępnionych dla obszaru roboczego.

## <a name="send-trend-micro-tippingpoint-logs-to-azure-sentinel"></a>Wyślij dzienniki Trend Micro TippingPoint do usługi Azure wskaźnikowej

Aby pobrać swoje dzienniki do funkcji wskaźnikowej platformy Azure, skonfiguruj rozwiązanie TPS TippingPoint do wysyłania komunikatów dziennika systemowego w formacie CEF do serwera z przekazywaniem danych dzienników opartych na systemie Linux (z systemem rsyslog lub dziennikiem systemu). Na tym serwerze będzie zainstalowany agent Log Analytics, a agent przekazuje dzienniki do obszaru roboczego wskaźnikowego platformy Azure. Łącznik korzysta z funkcji analizatora składni, aby przekonwertować dane odbierane w znormalizowanym schemacie. 

1. W menu nawigacji wskaźnikowej platformy Azure wybierz pozycję **Łączniki danych**.

1. W galerii **Łączniki danych** wybierz pozycję **Trend Micro TippingPoint (wersja zapoznawcza)**, a następnie **Otwórz stronę łącznik**.

1. Postępuj zgodnie z instrukcjami wyświetlanymi na karcie **instrukcje** w obszarze **Konfiguracja**:

    1. W obszarze **1. Konfiguracja agenta dziennika systemu Linux** — wykonaj ten krok, jeśli nie masz jeszcze uruchomionego usługi przesyłania dalej dzienników lub jeśli potrzebujesz innego. Zobacz [krok 1. wdrażanie usługi przesyłania dalej dzienników](connect-cef-agent.md) w dokumentacji usługi Azure wskaźnikowej w celu uzyskania bardziej szczegółowych instrukcji i wyjaśnień.

    1. W obszarze **2. Prześlij dalej dzienniki SMS usługi Trend Micro TippingPoint do agenta dziennika** systemu — ta konfiguracja powinna obejmować następujące elementy:
        - Miejsce docelowe dziennika — nazwa hosta i/lub adres IP serwera przekazywania dzienników
        - Protokół i port — **TCP 514** (w razie potrzeby w przeciwnym razie należy wprowadzić zmianę równoległą w demona dziennika systemowego na serwerze przesyłania dalej dzienników)
        - Format dziennika — **ARCSIGHT CEF format v 4.2**
        - Typy dzienników — wszystkie dostępne

    1. W obszarze **3. Sprawdź poprawność** pozyskiwania danych przez skopiowanie polecenia na stronie łącznika i uruchomienie go w usłudze przesyłania dalej dzienników. Zobacz [krok 3. Weryfikowanie łączności](connect-cef-verify.md) w dokumentacji usługi Azure wskaźnikowej, aby uzyskać bardziej szczegółowe instrukcje i wyjaśnienie.

        Rozpoczęcie wyświetlania dzienników w Log Analytics może potrwać do 20 minut.

## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia dane pojawiają się w **dziennikach** w sekcji **wskaźnik platformy Azure** w tabeli *CommonSecurityLog* .

Aby uzyskać dane Trend Micro TippingPoint w Log Analytics, zbadasz funkcję parsera zamiast tabeli. Skopiuj następujące elementy do okna zapytania, stosując inne filtry w wybranym przez siebie sposób:

```kusto
TrendMicroTippingPoint
| sort by TimeGenerated
```

Więcej przykładów zapytań można znaleźć na karcie **następne kroki** na stronie łącznika.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób nawiązywania połączenia z Trend Micro TippingPoint z platformą Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.
