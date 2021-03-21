---
title: Łączenie danych Aruba ClearPass z platformą Azure Microsoft Docs
description: Dowiedz się, jak używać łącznika Aruba ClearPass do ściągania dzienników Aruba ClearPass do usługi Azure wskaźnikowej. Wyświetlaj dane Aruba ClearPass w skoroszytach, twórz Alerty i ulepszaj badanie.
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
ms.date: 02/28/2021
ms.author: yelevin
ms.openlocfilehash: 1b03e2d8715b3d80425ef181dd908978d5df4cf2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101746727"
---
# <a name="connect-your-aruba-clearpass-to-azure-sentinel"></a>Połącz swoje Aruba ClearPass z platformą Azure — wskaźnikiem

> [!IMPORTANT]
> Łącznik Aruba ClearPass jest obecnie w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.

W tym artykule wyjaśniono, jak połączyć urządzenie Aruba ClearPass z platformą Azure. Łącznik danych Aruba ClearPass umożliwia łatwe łączenie dzienników Aruba ClearPass z platformą Azure — wskaźnikiem, aby można było wyświetlać dane w skoroszytach, wysyłać zapytania do tworzenia niestandardowych alertów i uwzględniać je w celu usprawnienia badania. Integracja między programami Aruba ClearPass i Azure wskaźnikiem, korzysta z dziennika systemowego z systemem CEF, usługi przesyłania dalej dzienników opartego na systemie Linux i agenta Log Analytics. Używa także analizatora dzienników utworzonego przez użytkownika w oparciu o funkcję Kusto.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć uprawnienia do odczytu i zapisu w obszarze roboczym wskaźnikowego platformy Azure.

- Użytkownik musi mieć uprawnienia do odczytu kluczy udostępnionych dla obszaru roboczego. [Dowiedz się więcej o kluczach obszarów roboczych](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key).

## <a name="send-aruba-clearpass-logs-to-azure-sentinel"></a>Wysyłanie dzienników Aruba ClearPass do usługi Azure wskaźnikowej

Aby pobrać swoje dzienniki do funkcji wskaźnikowej platformy Azure, Skonfiguruj urządzenie Aruba ClearPass do wysyłania komunikatów dziennika systemowego w formacie CEF do serwera z przekazywaniem danych dzienników opartych na systemie Linux (z systemem rsyslog lub dziennikiem systemu). Na tym serwerze będzie zainstalowany agent Log Analytics, a agent przekazuje dzienniki do obszaru roboczego wskaźnikowego platformy Azure.

1. W menu nawigacji wskaźnikowej platformy Azure wybierz pozycję **Łączniki danych**.

1. W galerii **łączników danych** wybierz pozycję **Aruba ClearPass (wersja zapoznawcza)**, a następnie **Otwórz stronę łącznik**.

1. Postępuj zgodnie z instrukcjami wyświetlanymi na karcie **instrukcje** w obszarze **Konfiguracja**:

    1. W obszarze **1. Konfiguracja agenta dziennika systemu Linux** — wykonaj ten krok, jeśli nie masz jeszcze uruchomionego usługi przesyłania dalej dzienników lub jeśli potrzebujesz innego. Zobacz [krok 1. wdrażanie usługi przesyłania dalej dzienników](connect-cef-agent.md) w dokumentacji usługi Azure wskaźnikowej w celu uzyskania bardziej szczegółowych instrukcji i wyjaśnień.

    1. W obszarze **2. Przekazywanie dzienników Aruba ClearPass do agenta dziennika** systemu — wykonaj instrukcje Aruba w celu [skonfigurowania ClearPass](https://www.arubanetworks.com/techdocs/ClearPass/6.7/PolicyManager/Content/CPPM_UserGuide/Admin/syslogExportFilters_add_syslog_filter_general.htm). Ta konfiguracja powinna obejmować następujące elementy:
        - Miejsce docelowe dziennika — nazwa hosta i/lub adres IP serwera przekazywania dzienników
        - Protokół i port — TCP 514 (w razie potrzeby w przeciwnym razie należy wprowadzić zmianę równoległą w demona dziennika systemowego na serwerze przesyłania dalej dzienników)
        - Format dziennika — CEF
        - Typy dzienników — wszystkie dostępne lub wszystkie odpowiednie

    1. W obszarze **3. Sprawdź poprawność** pozyskiwania danych przez skopiowanie polecenia na stronie łącznika i uruchomienie go w usłudze przesyłania dalej dzienników. Zobacz [krok 3. Weryfikowanie łączności](connect-cef-verify.md) w dokumentacji usługi Azure wskaźnikowej, aby uzyskać bardziej szczegółowe instrukcje i wyjaśnienie.

        Rozpoczęcie wyświetlania dzienników w Log Analytics może potrwać do 20 minut.

## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia dane pojawiają się w **dziennikach** w sekcji **wskaźnik platformy Azure** w tabeli *CommonSecurityLog* .

Ten łącznik danych zależy od analizatora opartego na funkcji Kusto, aby działać zgodnie z oczekiwaniami. [Wykonaj następujące kroki](https://aka.ms/sentinel-arubaclearpass-parser) , aby utworzyć alias funkcji **ArubaClearPass** Kusto.

Następnie, aby wykonać zapytanie o dane Aruba ClearPass w Log Analytics, wprowadź `ArubaClearPass` w górnej części okna zapytania.

Zobacz kartę **następne kroki** na stronie łącznik, aby poznać przydatne przykłady zapytań.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób łączenia Aruba ClearPass z platformą Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.
