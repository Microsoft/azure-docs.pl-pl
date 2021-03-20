---
title: Łączenie danych Symantec ICDx z platformą Azure — wskaźnikiem Microsoft Docs
description: Dowiedz się, jak za pomocą łącznika Symantec ICDx łatwo połączyć wszystkie dzienniki rozwiązań zabezpieczeń firmy Symantec z platformą Azure — wskaźnikiem.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 7b32baa23a31342095e542d60745a503b81e89a9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100092710"
---
# <a name="connect-your-symantec-icdx-appliance"></a>Łączenie urządzenia z firmą Symantec ICDx 



Łącznik Symantec ICDx umożliwia łatwe łączenie dzienników rozwiązań zabezpieczeń firmy Symantec z badaniem wskaźnikowym platformy Azure, przeglądanie pulpitów nawigacyjnych, tworzenie niestandardowych alertów i ulepszanie badania. Zapewnia to dokładniejszy wgląd w sieć organizacji i zwiększa możliwości operacji zabezpieczeń. Integracja między firmą Symantec ICDx i platformą Azure wskaźnikiem wykorzystuje interfejs API REST.


> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="configure-and-connect-symantec-icdx"></a>Konfigurowanie i łączenie z firmą Symantec ICDx 

Firma Symantec ICDx może zintegrować i eksportować dzienniki bezpośrednio do platformy Azure.

1. Otwórz konsolę zarządzania ICDx, aby dodać usługi przesyłania dalej Microsoft Azure wskaźnikiem próbnym (Log Analytics).
2. Na pasku nawigacyjnym ICDx kliknij pozycję **Konfiguracja**. 
3. W górnej części ekranu **konfiguracji** kliknij pozycję **usługi przesyłania dalej**.
4. W obszarze **usługi przesyłania** dalej obok pozycji Microsoft Azure wskaźnikiem (log Analytics) kliknij pozycję **Dodaj**. 
4. W oknie **Microsoft Azure wskaźnikiem kontrolnym (log Analytics)** kliknij pozycję **Pokaż zaawansowane**. 
5. W górnej części okna do Microsoft Azure wskaźnikiem kontrolnym (Log Analytics) wykonaj następujące czynności:
    -   **Nazwa**: wpisz nazwę usługi przesyłania dalej, która ma nie więcej niż 30 znaków. Wybierz unikatową, zrozumiałą nazwę. Ta nazwa jest wyświetlana na liście usług przesyłania dalej na ekranie **Konfiguracja** i na pulpitach nawigacyjnych na ekranie **pulpitu nawigacyjnego** . Na przykład: Microsoft Azure Log Analytics wschód. To pole jest wymagane.
    -   **Opis**: wpisz opis dla usługi przesyłania dalej. Ten opis jest również wyświetlany na liście usług przesyłania dalej na ekranie **Konfiguracja** . Dołącz szczegóły, takie jak typ zdarzenia, który jest przekazywany i Grupa, która wymaga inspekcji danych.
    -   **Typ uruchamiania**: Wybierz metodę uruchamiania konfiguracji usługi przesyłania dalej. Dostępne są opcje ręczne i automatyczne.<br>Wartość domyślna to automatyczne. 
6. W obszarze **zdarzenia** wykonaj następujące czynności: 
    - **Źródło**: Wybierz co najmniej jedno archiwum, z którego mają być przekazywane zdarzenia. Można wybrać aktywne archiwa modułu zbierającego (w tym typowe archiwum), oddzielone archiwa modułu zbierającego (czyli archiwa dla usuniętych modułów zbierających), archiwa odbiorników ICDx lub archiwum systemowe. <br>Wartość domyślna to częste archiwum.
      > [!NOTE]
      > Archiwa odbiorników ICDx są wymieniane osobno według nazwy. 
 
    - **Filtr**: Dodaj filtr określający podzestaw zdarzeń do przekierowania. Wykonaj jedną z następujących czynności:
        - Aby wybrać warunek filtru, kliknij typ, atrybut, operator i wartość. 
        - Sprawdź warunek filtru w polu Filtr. Można edytować go bezpośrednio w polu lub usunąć, w razie potrzeby.
        - Kliknij i lub lub, aby dodać do warunku filtru.
        - Możesz również kliknąć pozycję zapisane zapytania, aby zastosować zapisane zapytanie.
    - **Uwzględnione atrybuty**: Wpisz rozdzielaną przecinkami listę atrybutów do uwzględnienia w przekazywanych danych. Uwzględnione atrybuty mają pierwszeństwo przed atrybutami wykluczonymi.
    - **Wykluczone atrybuty**: Wpisz listę atrybutów rozdzielanych przecinkami, które mają zostać wykluczone z danych przekazywanych dalej.
    - **Rozmiar wsadu**: Wybierz liczbę zdarzeń do wysłania na partię. Dostępne opcje to 10, 50, 100, 500 i 1000.<br>Wartość domyślna to 100. 
    - **Limit szybkości**: Wybierz częstotliwość przekazywania zdarzeń, wyrażony jako zdarzenia na sekundę. Dostępne opcje to nieograniczone, 500, 1000, 5000, 10000. <br> Wartość domyślna to 5000. 
7. W obszarze **Lokalizacja docelowa platformy Azure** wykonaj następujące czynności: 
    - **Identyfikator obszaru roboczego**: wklej identyfikator obszaru roboczego poniżej. To pole jest wymagane.
    - **Klucz podstawowy**: Wklej klucz podstawowy poniżej. To pole jest wymagane.
    - **Nazwa dziennika niestandardowego**: wpisz nazwę dziennika niestandardowego w obszarze roboczym Microsoft Azure Portal log Analytics, do którego chcesz przekazywać zdarzenia. Wartość domyślna to SymantecICDx. To pole jest wymagane.
8. Kliknij przycisk *Zapisz* , aby zakończyć konfigurację usługi przesyłania dalej. 
9. Aby uruchomić usługę przesyłania dalej, w obszarze **Opcje** kliknij pozycję **więcej** , a następnie **Zacznij**.
10. Aby użyć odpowiedniego schematu w Log Analytics dla zdarzeń firmy Symantec ICDx, wyszukaj ciąg **SymantecICDx_CL**.


## <a name="validate-connectivity"></a>Sprawdź poprawność łączności

Rozpoczęcie wyświetlania dzienników w Log Analytics może potrwać do 20 minut. 



## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób nawiązywania połączenia z firmą Symantec ICDx z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.


