---
title: Monitoruj Azure File Sync | Microsoft Docs
description: Zapoznaj się z tematem monitorowanie wdrożenia Azure File Sync przy użyciu Azure Monitor, usługi synchronizacji magazynu i systemu Windows Server.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/28/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 1ef24522f688c5ae1176630a2f370cd7ee7c3cd7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448061"
---
# <a name="monitor-azure-file-sync"></a>Monitorowanie usługi Azure File Sync

Użyj Azure File Sync, aby scentralizować udziały plików w organizacji w Azure Files, utrzymując elastyczność, wydajność i zgodność lokalnego serwera plików. Funkcja Azure File Sync przekształca system Windows Server w szybką pamięć podręczną udziału plików platformy Azure. Możesz użyć dowolnego dostępnego protokołu w systemie Windows Server w celu uzyskania lokalnego dostępu do danych (w tym protokołu SMB, systemu plików NFS i protokołu FTPS). Na całym świecie możesz mieć dowolną liczbę pamięci podręcznych.

W tym artykule opisano sposób monitorowania wdrożenia Azure File Sync przy użyciu Azure Monitor, usługi synchronizacji magazynu i systemu Windows Server.

Następujące scenariusze zostały omówione w tym przewodniku: 
- Wyświetl metryki Azure File Sync w Azure Monitor.
- Twórz alerty w Azure Monitor, aby aktywnie powiadamiać o kluczowych warunkach.
- Wyświetl kondycję wdrożenia Azure File Sync przy użyciu Azure Portal.
- Jak używać dzienników zdarzeń i liczników wydajności na serwerach z systemem Windows do monitorowania kondycji wdrożenia Azure File Sync. 

## <a name="azure-monitor"></a>Azure Monitor

Użyj [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) , aby wyświetlić metryki i skonfigurować alerty synchronizacji, obsługi warstw w chmurze i łączności z serwerem.  

### <a name="metrics"></a>Metryki

Metryki dla Azure File Sync są domyślnie włączone i są wysyłane do Azure Monitor co 15 minut.

**Jak wyświetlić metryki Azure File Sync w Azure Monitor**
1. Przejdź do **usługi synchronizacji magazynu** w **Azure Portal** a następnie kliknij pozycję **metryki**.
2. Kliknij listę rozwijaną **Metryka** i wybierz metrykę, którą chcesz wyświetlić.

![Zrzut ekranu przedstawiający metryki Azure File Sync](media/storage-sync-files-troubleshoot/file-sync-metrics.png)

Następujące metryki dla Azure File Sync są dostępne w Azure Monitor:

| Nazwa metryki | Opis |
|-|-|
| Zsynchronizowane bajty | Rozmiar transferowanych danych (przekazywanie i pobieranie).<br><br>Jednostka: bajty<br>Typ agregacji: sum<br>Odpowiednie wymiary: nazwa punktu końcowego serwera, kierunek synchronizacji, nazwa grupy synchronizacji |
| Odwołanie do warstw w chmurze | Rozmiar danych, które zostały odwołane.<br><br>**Uwaga**: Ta Metryka zostanie usunięta w przyszłości. Metryka rozmiaru odwołania do warstw chmury służy do monitorowania rozmiaru danych, które zostały odwołane.<br><br>Jednostka: bajty<br>Typ agregacji: sum<br>Odpowiedni wymiar: Nazwa serwera |
| Rozmiar odwołania do warstw w chmurze | Rozmiar danych, które zostały odwołane.<br><br>Jednostka: bajty<br>Typ agregacji: sum<br>Odpowiedni wymiar: Nazwa serwera, nazwa grupy synchronizacji |
| Rozmiar odwołania do warstw w chmurze według aplikacji | Rozmiar danych, które zostały odwołane przez aplikację.<br><br>Jednostka: bajty<br>Typ agregacji: sum<br>Odpowiedni wymiar: Nazwa aplikacji, nazwa serwera, nazwa grupy synchronizacji |
| Przepływność odwołań do warstw w chmurze | Rozmiar przepływności operacji odwoływania danych.<br><br>Jednostka: bajty<br>Typ agregacji: sum<br>Odpowiedni wymiar: Nazwa serwera, nazwa grupy synchronizacji |
| Pliki, których nie należy synchronizować | Liczba plików, których synchronizacja nie powiodła się.<br><br>Jednostka: liczba<br>Typ agregacji: sum<br>Odpowiednie wymiary: nazwa punktu końcowego serwera, kierunek synchronizacji, nazwa grupy synchronizacji |
| Synchronizowane pliki | Liczba transferowanych plików (przekazywanie i pobieranie).<br><br>Jednostka: liczba<br>Typ agregacji: sum<br>Odpowiednie wymiary: nazwa punktu końcowego serwera, kierunek synchronizacji, nazwa grupy synchronizacji |
| Stan online serwera | Liczba pulsów odebranych z serwera.<br><br>Jednostka: liczba<br>Typ agregacji: maksimum<br>Odpowiedni wymiar: Nazwa serwera |
| Wynik synchronizacji sesji | Wynik sesji synchronizacji (1 = pomyślna sesja synchronizacji; 0 = nieudana sesja synchronizacji)<br><br>Jednostka: liczba<br>Typy agregacji: maksimum<br>Odpowiednie wymiary: nazwa punktu końcowego serwera, kierunek synchronizacji, nazwa grupy synchronizacji |

### <a name="alerts"></a>Alerty

Alerty z wyprzedzeniem powiadamiają Cię, gdy w danych monitorowania zostaną znalezione ważne warunki. Aby dowiedzieć się więcej o konfigurowaniu alertów w Azure Monitor, zobacz [Omówienie alertów w Microsoft Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

**Jak utworzyć alerty dla Azure File Sync**

1. Przejdź do **usługi synchronizacji magazynu** w **Azure Portal**. 
2. W sekcji monitorowanie kliknij pozycję **alerty** , a następnie kliknij pozycję **+ Nowa reguła alertów**.
3. Kliknij pozycję **Wybierz warunek** i podaj następujące informacje dotyczące alertu: 
    - **Metryka**
    - **Nazwa wymiaru**
    - **Logika alertu**
4. Kliknij pozycję **Wybierz grupę akcji** i Dodaj grupę akcji (wiadomości e-mail, wiadomości SMS itp.) do alertu, wybierając istniejącą grupę akcji lub tworząc nową grupę akcji.
5. Wypełnij **szczegóły alertu** , takie jak nazwa, **Opis** i **ważność** **reguły alertu**.
6. Kliknij przycisk **Utwórz regułę alertu** , aby utworzyć alert.  

W poniższej tabeli przedstawiono niektóre przykładowe scenariusze monitorowania i właściwej metryki do użycia w ramach alertu:

| Scenariusz | Metryka do użycia dla alertu |
|-|-|
| Kondycja punktu końcowego serwera przedstawia błąd w portalu | Wynik synchronizacji sesji |
| Synchronizowanie plików z serwerem lub punktem końcowym w chmurze nie powiodło się | Pliki, których nie należy synchronizować |
| Serwer zarejestrowany nie może komunikować się z usługą synchronizacji magazynu | Stan online serwera |
| Rozmiar odwołania do warstwy chmury przekroczył 500GiB w ciągu dnia  | Rozmiar odwołania do warstw w chmurze |

Aby uzyskać instrukcje dotyczące sposobu tworzenia alertów dla tych scenariuszy, zobacz sekcję [przykłady alertów](#alert-examples) .

## <a name="storage-sync-service"></a>Usługa synchronizacji magazynu

Aby wyświetlić kondycję wdrożenia Azure File Sync w **Azure Portal**, przejdź do **usługi synchronizacji magazynu** i dostępne są następujące informacje:

- Zarejestrowana kondycja serwera
- Kondycja punktu końcowego serwera
    - Pliki, których nie należy synchronizować
    - Działanie synchronizacji
    - Wydajność warstw chmury
    - Niewarstwowe pliki
    - Błędy odwoływania
- Metryki

### <a name="registered-server-health"></a>Zarejestrowana kondycja serwera

Aby wyświetlić **stan zarejestrowanego serwera** w portalu, przejdź do sekcji **zarejestrowane serwery** **usługi synchronizacji magazynu**.

![Zrzut ekranu przedstawiający kondycję zarejestrowanych serwerów](media/storage-sync-files-troubleshoot/file-sync-registered-servers.png)

- Jeśli **zarejestrowany stan serwera** jest w **trybie online**, serwer pomyślnie komunikuje się z usługą.
- Jeśli **zarejestrowany stan serwera** jest **wyświetlany w trybie offline**, proces monitora synchronizacji magazynu (AzureStorageSyncMonitor.exe) nie jest uruchomiony lub serwer nie może uzyskać dostępu do usługi Azure File Sync. Wskazówki można znaleźć w dokumentacji dotyczącej [rozwiązywania problemów](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#server-endpoint-noactivity) .

### <a name="server-endpoint-health"></a>Kondycja punktu końcowego serwera

Aby wyświetlić kondycję **punktu końcowego serwera** w portalu, przejdź do sekcji **synchronizowanie grup** **usługi synchronizacji magazynu** i wybierz **grupę synchronizacji**.

![Zrzut ekranu stanu punktu końcowego serwera](media/storage-sync-files-troubleshoot/file-sync-server-endpoint-health.png)

- **Działanie** **kondycji punktu końcowego serwera** w portalu jest oparte na zdarzeniach synchronizacji, które są rejestrowane w dzienniku zdarzeń telemetrii na serwerze (identyfikator 9102 i 9302). Jeśli sesja synchronizacji kończy się niepowodzeniem z powodu błędu przejściowego, takiego jak błąd anulowany, punkt końcowy serwera będzie nadal wyświetlany jako w **dobrej kondycji** w portalu, dopóki bieżąca sesja synchronizacji jest w toku (pliki są stosowane). Identyfikator zdarzenia 9302 jest zdarzeniem postępu synchronizacji, a identyfikator zdarzenia 9102 jest rejestrowany po zakończeniu sesji synchronizacji.  Aby uzyskać więcej informacji, zobacz [kondycja synchronizacji](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) i [postęp synchronizacji](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session). Jeśli kondycja punktu końcowego serwera pokazuje **błąd** lub **nie działa**, zobacz dokumentację dotyczącą [rozwiązywania problemów](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) , aby uzyskać wskazówki.
- Liczba **synchronizowanych plików** w portalu jest oparta na identyfikatorze zdarzenia 9121, który jest rejestrowany w dzienniku zdarzeń telemetrii na serwerze. To zdarzenie jest rejestrowane dla każdego błędu poszczególnych elementów po zakończeniu sesji synchronizacji. Aby rozwiązać błędy poszczególnych elementów, zobacz [Jak mogę sprawdzić, czy istnieją określone pliki lub foldery, które nie są synchronizowane?](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).
- Aby wyświetlić **wydajność obsługi warstw w chmurze** w portalu, przejdź do **właściwości punktu końcowego serwera** i przejdź do sekcji Obsługa **warstw chmury** . Dane dotyczące wydajności warstw chmury są oparte na zdarzeniu o IDENTYFIKATORze 9071, który jest rejestrowany w dzienniku zdarzeń telemetrii na serwerze. Aby dowiedzieć się więcej, zobacz [Omówienie obsługi warstw w chmurze](https://docs.microsoft.com/azure/storage/files/storage-sync-cloud-tiering).
- Aby wyświetlić **pliki niewarstwowe** i **Błędy odwołania** w portalu, przejdź do **właściwości punktu końcowego serwera** i przejdź do sekcji Obsługa **warstw w chmurze** . **Pliki bez warstw** opierają się na identyfikatorze zdarzenia 9003, który jest rejestrowany w dzienniku zdarzeń telemetrii na serwerze, a **Błędy odwoływania** są oparte na identyfikatorze zdarzenia 9006. Aby zbadać pliki, których nie można przystąpić do warstwy lub odwołania, zobacz [Jak rozwiązywać problemy z niepowodzeniem](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#how-to-troubleshoot-files-that-fail-to-tier) i [sposobami rozwiązywania problemów z plikami, które nie zostały wywołane](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#how-to-troubleshoot-files-that-fail-to-be-recalled).

### <a name="metric-charts"></a>Wykresy metryk

- Następujące wykresy pomiarowe są widoczne w portalu usługi synchronizacji magazynu:

  | Nazwa metryki | Opis | Nazwa bloku |
  |-|-|-|
  | Zsynchronizowane bajty | Rozmiar transferowanych danych (przekazywanie i pobieranie) | Grupa synchronizacji, punkt końcowy serwera |
  | Odwołanie do warstw w chmurze | Rozmiar danych, które zostały odwołane | Zarejestrowane serwery |
  | Pliki, których nie należy synchronizować | Liczba plików, których synchronizacja nie powiodła się | Punkt końcowy serwera |
  | Synchronizowane pliki | Liczba transferowanych plików (przekazywanie i pobieranie) | Grupa synchronizacji, punkt końcowy serwera |
  | Stan online serwera | Liczba pulsów odebranych z serwera | Zarejestrowane serwery |

- Aby dowiedzieć się więcej, zobacz [Azure monitor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor).

  > [!Note]  
  > Wykresy w portalu usługi synchronizacji magazynu mają przedział czasu wynoszący 24 godziny. Aby wyświetlić różne zakresy czasu lub wymiary, użyj Azure Monitor.

## <a name="windows-server"></a>Windows Server

Na **serwerze z systemem Windows** , na którym jest zainstalowany agent Azure File Sync, można wyświetlić kondycję punktów końcowych serwera na tym serwerze przy użyciu **dzienników zdarzeń** i **liczników wydajności**.

### <a name="event-logs"></a>Dzienniki zdarzeń

Użyj dziennika zdarzeń telemetrii na serwerze do monitorowania zarejestrowanego serwera, synchronizacji i kondycji warstw w chmurze. Dziennik zdarzeń telemetrii znajduje się w Podgląd zdarzeń w obszarze *aplikacje i Services\Microsoft\FileSync\Agent*.

Kondycja synchronizacji

- Zdarzenie o IDENTYFIKATORze 9102 jest rejestrowane po zakończeniu sesji synchronizacji. To zdarzenie służy do określenia, czy sesje synchronizacji powiodły się (**HRESULT = 0**) i czy występują błędy synchronizacji poszczególnych elementów (**PerItemErrorCount**). Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [kondycji synchronizacji](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) i  [błędów poszczególnych elementów](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) .

  > [!Note]  
  > Czasami sesje synchronizacji kończą się niepowodzeniem lub mają niezerową PerItemErrorCount. Jednak nadal trwają postęp, a niektóre pliki są synchronizowane pomyślnie. Można to sprawdzić w zastosowanych polach, takich jak AppliedFileCount, AppliedDirCount, AppliedTombstoneCount i AppliedSizeBytes. Te pola zawierają informacje o tym, ile sesji zakończyło się pomyślnie. Jeśli w wierszu wystąpi awaria wielu sesji synchronizacji i zostanie osiągnięty wzrost liczby zastosowanych, należy podać czas synchronizacji, aby ponowić próbę przed otwarciem biletu pomocy technicznej.

- Identyfikator zdarzenia 9121 jest rejestrowany dla każdego błędu dla każdego elementu po zakończeniu sesji synchronizacji. To zdarzenie służy do określenia liczby plików, które nie są synchronizowane z tym błędem (**PersistentCount** i **TransientCount**). Należy zbadać błędy trwałe dla elementów, zobacz [Jak mogę sprawdzić, czy istnieją określone pliki lub foldery, które nie są synchronizowane?](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).

- Zdarzenie o IDENTYFIKATORze 9302 jest rejestrowane co 5 – 10 minut, jeśli istnieje aktywna sesja synchronizacji. To zdarzenie służy do określania, ile elementów ma być synchronizowanych (**TotalItemCount**), liczby elementów, które zostały zsynchronizowane do tej pory (**AppliedItemCount**) i liczby elementów, które nie zostały zsynchronizowane z powodu błędu dla elementu (**PerItemErrorCount**). Jeśli synchronizacja nie powoduje postępu (**AppliedItemCount = 0**), sesja synchronizacji zakończy się niepowodzeniem, a identyfikator zdarzenia 9102 zostanie zarejestrowany z powodu błędu. Aby uzyskać więcej informacji, zobacz [dokumentację dotyczącą postępu synchronizacji](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).

Zarejestrowana kondycja serwera

- Zdarzenie o IDENTYFIKATORze 9301 jest rejestrowane co 30 sekund, gdy serwer wysyła zapytanie do usługi dla zadań. Jeśli GetNextJob zakończy się **stanem = 0**, serwer może komunikować się z usługą. Jeśli GetNextJob kończy się z błędem, zapoznaj się z [dokumentacją rozwiązywania problemów](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#server-endpoint-noactivity) , aby uzyskać wskazówki.

Kondycja warstw chmury

- Aby monitorować aktywność warstwową na serwerze, należy użyć zdarzenia o IDENTYFIKATORze 9003, 9016 i 9029 w dzienniku zdarzeń telemetrii, który znajduje się w Podgląd zdarzeń w obszarze *aplikacje i Services\Microsoft\FileSync\Agent*.

  - Identyfikator zdarzenia 9003 zapewnia dystrybucję błędów dla punktu końcowego serwera. Na przykład: całkowita liczba błędów i kod błędu. Jedno zdarzenie jest rejestrowane na kod błędu.
  - Identyfikator zdarzenia 9016 zawiera wyniki duplikowania dla woluminu. Na przykład: procent wolnego miejsca to, liczba plików zduplikowanych w sesji i liczba plików, których duplikat nie powiódł się.
  - Identyfikator zdarzenia 9029 zawiera zduplikowane informacje o sesji dla punktu końcowego serwera. Przykład: liczba plików, które próbowano wykonać w sesji, liczba plików warstwowych w sesji oraz liczba plików, które zostały już warstwowe.
  
- Aby monitorować działanie odwoływania na serwerze, należy użyć zdarzenia o IDENTYFIKATORze 9005, 9006, 9009, 9059 i 9071 w dzienniku zdarzeń telemetrii, który znajduje się w Podgląd zdarzeń w obszarze *aplikacje i Services\Microsoft\FileSync\Agent*.

  - Identyfikator zdarzenia 9005 zapewnia niezawodność odwoływania się do punktu końcowego serwera. Na przykład: całkowita liczba dostępnych unikatowych plików i całkowita liczba unikatowych plików z dostępem zakończonym niepowodzeniem.
  - Identyfikator zdarzenia 9006 zapewnia dystrybuowanie błędów odwołania dla punktu końcowego serwera. Na przykład: całkowita liczba nieudanych żądań i kod błędu. Jedno zdarzenie jest rejestrowane na kod błędu.
  - Identyfikator zdarzenia 9009 zawiera informacje o sesji odwoływania dla punktu końcowego serwera. Na przykład: DurationSeconds, CountFilesRecallSucceeded i CountFilesRecallFailed.
  - Identyfikator zdarzenia 9059 zapewnia dystrybucję odwoływania aplikacji dla punktu końcowego serwera. Na przykład: ShareId, nazwa aplikacji i TotalEgressNetworkBytes.
  - Identyfikator zdarzenia 9071 zapewnia wydajność warstwową w chmurze dla punktu końcowego serwera. Na przykład: TotalDistinctFileCountCacheHit, TotalDistinctFileCountCacheMiss, TotalCacheHitBytes i TotalCacheMissBytes.

### <a name="performance-counters"></a>Liczniki wydajności

Użyj Azure File Sync liczników wydajności na serwerze, aby monitorować aktywność synchronizacji.

Aby wyświetlić liczniki wydajności Azure File Sync na serwerze, otwórz Monitor wydajności (Perfmon.exe). Liczniki można znaleźć w obszarze **transfery bajtów AFS** i obiekty **operacji synchronizacji AFS** .

Następujące liczniki wydajności dla Azure File Sync są dostępne w Monitorze wydajności:

| Nazwa Object\Counter wydajności | Opis |
|-|-|
| Bajty AFS Transferred\Downloaded bajty/s | Liczba bajtów pobranych na sekundę. |
| Bajty AFS Transferred\Uploaded bajty/s | Liczba bajtów przekazana na sekundę. |
| Bajty AFS Transferred\Total bajty/s | Łączna liczba bajtów na sekundę (przekazywania i pobierania). |
| Pliki synchronizacji Operations\Downloaded synchronizacji AFS/s | Liczba pobranych plików na sekundę. |
| Pliki synchronizacji Operations\Uploaded synchronizacji AFS/s | Liczba przekazanych plików na sekundę. |
| Operacje pliku synchronizacji Operations\Total synchronizacji AFS/s | Łączna liczba synchronizowanych plików (przekazywania i pobierania). |

## <a name="alert-examples"></a>Przykłady alertów
Ta sekcja zawiera przykładowe alerty dotyczące Azure File Sync.

  > [!Note]  
  > Jeśli tworzysz alert i jest zbyt dużo szumu, Dostosuj wartość progową i logikę alertu.
  
### <a name="how-to-create-an-alert-if-the-server-endpoint-health-shows-an-error-in-the-portal"></a>Jak utworzyć alert, jeśli kondycja punktu końcowego serwera pokazuje błąd w portalu

1. W **Azure Portal**przejdź do odpowiedniej **usługi synchronizacji magazynu**. 
2. Przejdź do sekcji **monitorowanie** , a następnie kliknij pozycję **alerty**. 
3. Kliknij pozycję **+ Nowa reguła alertu** , aby utworzyć nową regułę alertu. 
4. Skonfiguruj warunek, klikając **pozycję Wybierz warunek**.
5. W bloku **Konfiguruj logikę sygnału** kliknij pozycję **Synchronizuj wynik sesji** w obszarze Nazwa sygnału.  
6. Wybierz następującą konfigurację wymiaru: 
    - Nazwa wymiaru: **Nazwa punktu końcowego serwera**  
    - Zakład **=** 
    - Wartości wymiaru: **wszystkie bieżące i przyszłe wartości**  
7. Przejdź do **logiki alertu** i wykonaj następujące czynności: 
    - Próg ustawiony na **statyczny** 
    - Operator: **mniejsze niż** 
    - Typ agregacji: **maksimum**  
    - Wartość progowa: **1** 
    - Oceniane na podstawie: stopień szczegółowości agregacji = **24 godziny** | Częstotliwość oceny = **co godzinę** 
    - Kliknij przycisk **gotowe.** 
8. Kliknij pozycję **Wybierz grupę akcji** , aby dodać do alertu grupę akcji (wiadomości e-mail, wiadomości SMS itp.), wybierając istniejącą grupę akcji lub tworząc nową grupę akcji.
9. Wypełnij **szczegóły alertu** , takie jak nazwa, **Opis** i **ważność** **reguły alertu**.
10. Kliknij pozycję **Utwórz regułę alertu**. 

### <a name="how-to-create-an-alert-if-files-are-failing-to-sync-to-a-server-or-cloud-endpoint"></a>Jak utworzyć alert, jeśli synchronizacja plików z serwerem lub punktem końcowym w chmurze kończy się niepowodzeniem

1. W **Azure Portal**przejdź do odpowiedniej **usługi synchronizacji magazynu**. 
2. Przejdź do sekcji **monitorowanie** , a następnie kliknij pozycję **alerty**. 
3. Kliknij pozycję **+ Nowa reguła alertu** , aby utworzyć nową regułę alertu. 
4. Skonfiguruj warunek, klikając **pozycję Wybierz warunek**.
5. W bloku **Konfigurowanie logiki sygnału** kliknij pozycję **pliki** , które nie są synchronizowane w obszarze Nazwa sygnału.  
6. Wybierz następującą konfigurację wymiaru: 
     - Nazwa wymiaru: **Nazwa punktu końcowego serwera**  
     - Zakład **=** 
     - Wartości wymiaru: **wszystkie bieżące i przyszłe wartości**  
7. Przejdź do **logiki alertu** i wykonaj następujące czynności: 
     - Próg ustawiony na **statyczny** 
     - Operator: **większe niż** 
     - Typ agregacji: **łącznie**  
     - Wartość progowa: **100** 
     - Oceniane na podstawie: stopień szczegółowości agregacji = **5 minut** | Częstotliwość oceny = **co 5 minut** 
     - Kliknij przycisk **gotowe.** 
8. Kliknij pozycję **Wybierz grupę akcji** , aby dodać do alertu grupę akcji (wiadomości e-mail, wiadomości SMS itp.), wybierając istniejącą grupę akcji lub tworząc nową grupę akcji.
9. Wypełnij **szczegóły alertu** , takie jak nazwa, **Opis** i **ważność** **reguły alertu**.
10. Kliknij pozycję **Utwórz regułę alertu**. 

### <a name="how-to-create-an-alert-if-a-registered-server-is-failing-to-communicate-with-the-storage-sync-service"></a>Jak utworzyć alert, jeśli zarejestrowany serwer nie może komunikować się z usługą synchronizacji magazynu

1. W **Azure Portal**przejdź do odpowiedniej **usługi synchronizacji magazynu**. 
2. Przejdź do sekcji **monitorowanie** , a następnie kliknij pozycję **alerty**. 
3. Kliknij pozycję **+ Nowa reguła alertu** , aby utworzyć nową regułę alertu. 
4. Skonfiguruj warunek, klikając **pozycję Wybierz warunek**.
5. W bloku **Konfigurowanie logiki sygnału** kliknij pozycję **serwer online stan** w obszarze Nazwa sygnału.  
6. Wybierz następującą konfigurację wymiaru: 
     - Nazwa wymiaru: **Nazwa serwera**  
     - Zakład **=** 
     - Wartości wymiaru: **wszystkie bieżące i przyszłe wartości**  
7. Przejdź do **logiki alertu** i wykonaj następujące czynności: 
     - Próg ustawiony na **statyczny** 
     - Operator: **mniejsze niż** 
     - Typ agregacji: **maksimum**  
     - Wartość progowa (w bajtach): **1** 
     - Oceniane na podstawie: stopień szczegółowości agregacji = **1 godzina** | Częstotliwość oceny = **co 30 minut** 
        - Należy zauważyć, że metryki są wysyłane do Azure Monitor co 15 – 20 minut. Nie ustawiaj **częstotliwości obliczania** na wartość mniejszą niż 30 minut (spowoduje to wygenerowanie fałszywych alertów).
     - Kliknij przycisk **gotowe.** 
8. Kliknij pozycję **Wybierz grupę akcji** , aby dodać do alertu grupę akcji (wiadomości e-mail, wiadomości SMS itp.), wybierając istniejącą grupę akcji lub tworząc nową grupę akcji.
9. Wypełnij **szczegóły alertu** , takie jak nazwa, **Opis** i **ważność** **reguły alertu**.
10. Kliknij pozycję **Utwórz regułę alertu**. 

### <a name="how-to-create-an-alert-if-the-cloud-tiering-recall-size-has-exceeded-500gib-in-a-day"></a>Jak utworzyć alert, jeśli rozmiar odwołania warstwowego w chmurze przekroczył 500GiB w ciągu dnia

1. W **Azure Portal**przejdź do odpowiedniej **usługi synchronizacji magazynu**. 
2. Przejdź do sekcji **monitorowanie** , a następnie kliknij pozycję **alerty**. 
3. Kliknij pozycję **+ Nowa reguła alertu** , aby utworzyć nową regułę alertu. 
4. Skonfiguruj warunek, klikając **pozycję Wybierz warunek**.
5. W bloku **Konfiguruj logikę sygnału** kliknij pozycję **warstwa chmury rozmiar odwołania** w obszarze Nazwa sygnału.  
6. Wybierz następującą konfigurację wymiaru: 
     - Nazwa wymiaru: **Nazwa serwera**  
     - Zakład **=** 
     - Wartości wymiaru: **wszystkie bieżące i przyszłe wartości**  
7. Przejdź do **logiki alertu** i wykonaj następujące czynności: 
     - Próg ustawiony na **statyczny** 
     - Operator: **większe niż** 
     - Typ agregacji: **łącznie**  
     - Wartość progowa (w bajtach): **67108864000** 
     - Oceniane na podstawie: stopień szczegółowości agregacji = **24 godziny** | Częstotliwość oceny = **co godzinę** 
    - Kliknij przycisk **gotowe.** 
8. Kliknij pozycję **Wybierz grupę akcji** , aby dodać do alertu grupę akcji (wiadomości e-mail, wiadomości SMS itp.), wybierając istniejącą grupę akcji lub tworząc nową grupę akcji.
9. Wypełnij **szczegóły alertu** , takie jak nazwa, **Opis** i **ważność** **reguły alertu**.
10. Kliknij pozycję **Utwórz regułę alertu**. 

## <a name="next-steps"></a>Następne kroki
- [Planowanie wdrażania usługi Azure File Sync](storage-sync-files-planning.md)
- [Rozważ użycie ustawień zapory i serwera proxy](storage-sync-files-firewall-and-proxy.md)
- [Wdrażanie funkcji Azure File Sync](storage-sync-files-deployment-guide.md)
- [Rozwiązywanie problemów z usługą Azure File Sync](storage-sync-files-troubleshoot.md)
- [Azure Files często zadawane pytania](storage-files-faq.md)
