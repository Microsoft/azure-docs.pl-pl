---
title: Łączenie danych usługi Broadcom firmy Symantec do ochrony przed utratą danych (DLP) na platformie Azure Microsoft Docs
description: Dowiedz się, jak za pomocą łącznika danych DLP firmy Symantec rozciągnąć dzienniki DLP firmy Symantec do usługi Azure wskaźnikowej. Wyświetlaj dane DLP firmy Symantec w skoroszytach, twórz Alerty i ulepszaj badanie.
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
ms.date: 03/02/2021
ms.author: yelevin
ms.openlocfilehash: 4be182b3eee59f7f9d2ef704a3d0f57c6718b45d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105044978"
---
# <a name="connect-your-broadcom-symantec-data-loss-prevention-dlp-to-azure-sentinel"></a>Łączenie ochrony przed utratą danych (DLP) w systemie Broadcom firmy Symantec z platformą Azure — wskaźnikiem

> [!IMPORTANT]
> Łącznik funkcji Broadcom Symantec DLP jest obecnie w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.

W tym artykule wyjaśniono, jak podłączyć urządzenie DLP firmy Symantec do funkcji wskaźnikowej platformy Azure. Łącznik danych funkcji Broadcom Symantec DLP pozwala łatwo połączyć dzienniki DLP firmy Symantec z platformą Azure, aby wyświetlić pulpity nawigacyjne, utworzyć niestandardowe alerty i poprawić badanie. Ta funkcja zapewnia dokładniejszy wgląd w informacje dotyczące organizacji, miejsce ich podróży i zwiększa możliwości operacji zabezpieczeń. Integracja między firmami Broadcom Symantec i Azure wskaźnikiem rozwoju korzysta z CEF dziennika systemowego, usługi przesyłania dalej dzienników opartej na systemie Linux i agenta Log Analytics. Używa także analizatora dzienników utworzonego przez użytkownika w oparciu o funkcję Kusto.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć uprawnienia do odczytu i zapisu w obszarze roboczym wskaźnikowego platformy Azure.

- Użytkownik musi mieć uprawnienia do odczytu kluczy udostępnionych dla obszaru roboczego. [Dowiedz się więcej o kluczach obszarów roboczych](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

## <a name="send-broadcom-symantec-dlp-logs-to-azure-sentinel"></a>Wyślij dzienniki funkcji Broadcom firmy Symantec do funkcji wskaźnikowej platformy Azure

Aby pobrać swoje dzienniki do funkcji wskaźnikowej platformy Azure, Skonfiguruj urządzenie DLP firmy Symantec w celu wysyłania komunikatów dziennika systemowego w formacie CEF do serwera przekazywania dzienników opartego na systemie Linux (z systemem rsyslog lub dziennikiem systemu). Na tym serwerze będzie zainstalowany agent Log Analytics, a agent przekazuje dzienniki do obszaru roboczego wskaźnikowego platformy Azure.

1. W menu nawigacji wskaźnikowej platformy Azure wybierz pozycję **Łączniki danych**.

1. Z galerii **łączników danych** wybierz łącznik **Broadcom Symantec DLP (wersja zapoznawcza)** , a następnie **Otwórz stronę łącznik**.

1. Postępuj zgodnie z instrukcjami wyświetlanymi na karcie **instrukcje** w obszarze **Konfiguracja**:

    1. W obszarze **1. Konfiguracja agenta dziennika systemu Linux** — wykonaj ten krok, jeśli nie masz jeszcze uruchomionego usługi przesyłania dalej dzienników lub jeśli potrzebujesz innego. Zobacz [krok 1. wdrażanie usługi przesyłania dalej dzienników](connect-cef-agent.md) w dokumentacji usługi Azure wskaźnikowej w celu uzyskania bardziej szczegółowych instrukcji i wyjaśnień.

    1. W obszarze **2. Przekazuj dzienniki DLP firmy Symantec do agenta dziennika** systemu — postępuj zgodnie z instrukcjami w celu [skonfigurowania funkcji DLP firmy Symantec](https://help.symantec.com/cs/DLP15.7/DLP/v27591174_v133697641/Configuring-the-Log-to-a-Syslog-Server-action?locale=EN_US). Ta konfiguracja powinna obejmować następujące elementy:
        - Miejsce docelowe dziennika — nazwa hosta i/lub adres IP serwera przekazywania dzienników
        - Protokół i port — TCP 514 (w razie potrzeby w przeciwnym razie należy wprowadzić zmianę równoległą w demona dziennika systemowego na serwerze przesyłania dalej dzienników)
        - Format dziennika — CEF
        - Typy dzienników — wszystkie dostępne lub wszystkie odpowiednie

    1. W obszarze **3. Sprawdź poprawność** pozyskiwania danych przez skopiowanie polecenia na stronie łącznika i uruchomienie go w usłudze przesyłania dalej dzienników. Zobacz [krok 3. Weryfikowanie łączności](connect-cef-verify.md) w dokumentacji usługi Azure wskaźnikowej, aby uzyskać bardziej szczegółowe instrukcje i wyjaśnienie.

        Rozpoczęcie wyświetlania dzienników w Log Analytics może potrwać do 20 minut.

## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia dane pojawiają się w **dziennikach** w sekcji **wskaźnik platformy Azure** w tabeli *CommonSecurityLog* .

Ten łącznik danych zależy od analizatora opartego na funkcji Kusto, aby działać zgodnie z oczekiwaniami. [Wykonaj następujące kroki](https://aka.ms/sentinel-symantecdlp-parser) , aby utworzyć alias funkcji **SymantecDLP** Kusto.

Następnie w celu wykonywania zapytań dotyczących danych DLP firmy Symantec w Log Analytics wpisz `SymantecDLP` w górnej części okna zapytania.

Zobacz kartę **następne kroki** na stronie łącznik, aby poznać przydatne przykłady zapytań.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób łączenia funkcji DLP firmy Symantec z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.