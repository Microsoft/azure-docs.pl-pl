---
title: Połącz serwer Thycotic Secret z platformą Azure — wskaźnik | Microsoft Docs
description: Dowiedz się, jak używać łącznika danych tajnego serwera Thycotic do ściągania Thycotic wpisów tajnych serwera do platformy Azure. Wyświetlaj dane serwera Thycotic Secret w skoroszytach, twórz Alerty i ulepszaj badanie.
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
ms.date: 12/13/2020
ms.author: yelevin
ms.openlocfilehash: a0056391dddec25511deb7033d37f59db3d835c8
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567935"
---
# <a name="connect-your-thycotic-secret-server-to-azure-sentinel"></a>Połącz serwer tajny Thycotic z platformą Azure — wskaźnikiem

> [!IMPORTANT]
> Łącznik serwera Thycotic Secret jest obecnie w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.

W tym artykule wyjaśniono, jak połączyć urządzenie Thycotic Secret Server z platformą Azure. Łącznik danych Thycotic Secret Server umożliwia łatwe łączenie dzienników tajnych serwera Thycotic z systemem Azure, dzięki czemu można wyświetlać dane ze skoroszytów, używać ich do tworzenia niestandardowych alertów i uwzględniania ich w celu usprawnienia badania. Integracja między programem Thycotic i platformą Azure wskaźnikiem umożliwia korzystanie z łącznika danych CEF w celu prawidłowej analizy i wyświetlania komunikatów dziennika systemowego serwera tajnego.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć uprawnienia do odczytu i zapisu w obszarze roboczym wskaźnikowego platformy Azure.

- Użytkownik musi mieć uprawnienia do odczytu kluczy udostępnionych dla obszaru roboczego.

- Serwer tajny Thycotic musi być skonfigurowany do eksportowania dzienników za pośrednictwem dziennika systemowego.

## <a name="send-thycotic-secret-server-logs-to-azure-sentinel"></a>Wyślij dzienniki serwera Thycotic Secret do usługi Azure wskaźnikowej

Aby pobrać swoje dzienniki do funkcji wskaźnikowej platformy Azure, skonfiguruj serwer tajny Thycotic do wysyłania komunikatów dziennika systemu w formacie CEF do serwera przekazywania dzienników opartego na systemie Linux (uruchamianie rsyslog lub dziennika systemu). Na tym serwerze będzie zainstalowany agent Log Analytics, a agent przekazuje dzienniki do obszaru roboczego wskaźnikowego platformy Azure.

1. W menu nawigacji wskaźnikowej platformy Azure wybierz pozycję **Łączniki danych**.

1. W galerii **Łączniki danych** wybierz pozycję **Thycotic Secret Server (wersja zapoznawcza)**, a następnie **Otwórz stronę łącznik**.

1. Postępuj zgodnie z instrukcjami wyświetlanymi na karcie **instrukcje** w obszarze **Konfiguracja**:

    1. W obszarze **1. Konfiguracja agenta dziennika systemu Linux** — wykonaj ten krok, jeśli nie masz jeszcze uruchomionego usługi przesyłania dalej dzienników lub jeśli potrzebujesz innego. Zobacz [krok 1. wdrażanie usługi przesyłania dalej dzienników](connect-cef-agent.md) w dokumentacji usługi Azure wskaźnikowej w celu uzyskania bardziej szczegółowych instrukcji i wyjaśnień.

    1. W obszarze **2. Dzienniki przesyłania dalej Common Event format (CEF) do agenta dziennika** systemu — postępuj zgodnie z instrukcjami Thycotic, aby [skonfigurować serwer tajny](https://thy.center/ss/link/syslog). Ta konfiguracja powinna obejmować następujące elementy:
        - Miejsce docelowe dziennika — nazwa hosta i/lub adres IP serwera przekazywania dzienników
        - Protokół i port — **TCP 514** (w razie potrzeby w przeciwnym razie należy wprowadzić zmianę równoległą w demona dziennika systemowego na serwerze przesyłania dalej dzienników)
        - Format dziennika — CEF
        - Typy dzienników — wszystkie dostępne

    1. W obszarze **3. Sprawdź poprawność** pozyskiwania danych przez skopiowanie polecenia na stronie łącznika i uruchomienie go w usłudze przesyłania dalej dzienników. Zobacz [krok 3. Weryfikowanie łączności](connect-cef-verify.md) w dokumentacji usługi Azure wskaźnikowej, aby uzyskać bardziej szczegółowe instrukcje i wyjaśnienie.

        Rozpoczęcie wyświetlania dzienników w Log Analytics może potrwać do 20 minut.

## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia dane pojawiają się w **dziennikach** w sekcji **wskaźnik platformy Azure** w tabeli *CommonSecurityLog* .

Aby wykonać zapytanie dotyczące danych Thycotic tajnego serwera w Log Analytics, Skopiuj następujące polecenie do okna zapytania, stosując inne filtry w wybranym przez siebie sposób:

```kusto
CommonSecurityLog 
| where DeviceVendor == "Thycotic Software"
```

Zobacz kartę **następne kroki** na stronie łącznik, aby poznać przydatne skoroszyty i przykłady zapytań.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób nawiązywania połączenia z serwerem Thycotic Secret z platformą Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.