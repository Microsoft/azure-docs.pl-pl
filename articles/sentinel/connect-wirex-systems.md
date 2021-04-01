---
title: Connect WireX Network dowodowych platform (NFP) do platformy Azure — wskaźnik | Microsoft Docs
description: Dowiedz się, w jaki sposób używać łącznika danych WireX Systems NFP do ściągania dzienników WireX NFP do usługi Azure wskaźnikowej. Wyświetlaj dane WireX NFP w skoroszytach, twórz Alerty i ulepszaj badanie.
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
ms.openlocfilehash: 9029b945eabd05b34306393b513e26ee9c1563f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98541574"
---
# <a name="connect-your-wirex-network-forensics-platform-nfp-appliance-to-azure-sentinel"></a>Połącz urządzenie WireX Network dowodowych platform (NFP) z usługą Azure wskaźnikiem

> [!IMPORTANT]
> Łącznik WireX Systems NFP jest obecnie w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.

W tym artykule wyjaśniono, jak podłączyć urządzenie WireX Systems Network dowodowych platform (NFP) do usługi Azure wskaźnikowej. Łącznik danych WireX NFP umożliwia łatwe łączenie dzienników NFP z platformą Azure, dzięki czemu można wyświetlać dane ze skoroszytów, używać ich do tworzenia niestandardowych alertów i uwzględniania ich w celu usprawnienia badania. 

> [!NOTE] 
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć uprawnienia do odczytu i zapisu w obszarze roboczym wskaźnikowego platformy Azure.

- Użytkownik musi mieć uprawnienia do odczytu kluczy udostępnionych dla obszaru roboczego.

## <a name="send-wirex-nfp-logs-to-azure-sentinel"></a>Wysyłanie dzienników WireX NFP do usługi Azure wskaźnikowej

Aby pobrać swoje dzienniki do funkcji wskaźnikowej platformy Azure, Skonfiguruj urządzenie WireX Systems NFP w taki sposób, aby wysyłał komunikaty dziennika systemu w formacie CEF do serwera z przekazywaniem danych dziennika z systemem Linux (z systemem rsyslog lub dziennikiem systemu). Na tym serwerze będzie zainstalowany agent Log Analytics, a agent przekazuje dzienniki do obszaru roboczego wskaźnikowego platformy Azure.

1. W menu nawigacji wskaźnikowej platformy Azure wybierz pozycję **Łączniki danych**.

1. W galerii **łączników danych** wybierz pozycję **WireX Network dowodowych platform (wersja zapoznawcza)**, a następnie **Otwórz stronę łącznik**.

1. Postępuj zgodnie z instrukcjami wyświetlanymi na karcie **instrukcje** w obszarze **Konfiguracja**:

    1. **1. Konfiguracja agenta dziennika systemu Linux** — wykonaj ten krok, jeśli nie masz jeszcze uruchomionego usługi przesyłania dalej dzienników lub potrzebujesz innej. Zobacz [krok 1. wdrażanie usługi przesyłania dalej dzienników](connect-cef-agent.md) w dokumentacji usługi Azure wskaźnikowej w celu uzyskania bardziej szczegółowych instrukcji i wyjaśnień.

    1. **2. Przekaż dzienniki usługi Common Event format (CEF) do agenta dziennika** systemu, aby skontaktować się z [pomocą techniczną WireX](https://wirexsystems.com/contact-us/) w celu uzyskania prawidłowej konfiguracji rozwiązania NFP WireX. Ta konfiguracja powinna obejmować następujące elementy:
        - Miejsce docelowe dziennika — nazwa hosta i/lub adres IP serwera przekazywania dzienników
        - Protokół i port — TCP 514 (w razie potrzeby w przeciwnym razie należy wprowadzić zmianę równoległą w demona dziennika systemowego na serwerze przesyłania dalej dzienników)
        - Format dziennika — CEF
        - Typy dzienników — wszystkie zalecane przez WireX

    1. **3. Sprawdź poprawność** pozyskiwania danych, kopiując polecenie na stronie łącznika i uruchamiając je w usłudze przesyłania dalej dzienników. Zobacz [krok 3. Weryfikowanie łączności](connect-cef-verify.md) w dokumentacji usługi Azure wskaźnikowej, aby uzyskać bardziej szczegółowe instrukcje i wyjaśnienie.

        Rozpoczęcie wyświetlania dzienników w Log Analytics może potrwać do 20 minut.

## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia dane pojawiają się w **dziennikach** w sekcji **wskaźnik platformy Azure** w tabeli *CommonSecurityLog* .

Aby wykonać zapytanie o dane WireX NFP w Log Analytics, Skopiuj następujące elementy do okna zapytania, stosując inne filtry w miarę wyboru:

```kusto
CommonSecurityLog 
| where DeviceVendor == "WireX"
```

Więcej przykładów zapytań można znaleźć na karcie **następne kroki** na stronie łącznika.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób łączenia WireX Systems NFP z platformą Azure wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.