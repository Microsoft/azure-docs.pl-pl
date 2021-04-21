---
title: Monitorowanie Azure File Sync | Microsoft Docs
description: Sprawdź, jak monitorować wdrożenie Azure File Sync przy użyciu Azure Monitor, usługi synchronizacji magazynu i systemu Windows Server.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 8e9fbd5fd8fc90681432ee8403b6dd139d02a5a5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797312"
---
# <a name="monitor-azure-file-sync"></a>Monitorowanie usługi Azure File Sync

Użyj Azure File Sync, aby scentralizować udziały plików organizacji w programie Azure Files przy zachowaniu elastyczności, wydajności i zgodności lokalnego serwera plików. Funkcja Azure File Sync przekształca system Windows Server w szybką pamięć podręczną udziału plików platformy Azure. Możesz użyć dowolnego dostępnego protokołu w systemie Windows Server w celu uzyskania lokalnego dostępu do danych (w tym protokołu SMB, systemu plików NFS i protokołu FTPS). Możesz mieć tyle pamięci podręcznych, ile potrzebujesz na całym świecie.

W tym artykule opisano sposób monitorowania wdrożenia Azure File Sync przy użyciu Azure Monitor, usługi synchronizacji magazynu i systemu Windows Server.

W tym przewodniku o przedstawiono następujące scenariusze: 
- Wyświetlanie Azure File Sync metryk w Azure Monitor.
- Twórz alerty w Azure Monitor, aby proaktywnie powiadamiać o warunkach krytycznych.
- Wyświetl kondycję wdrożenia Azure File Sync przy użyciu Azure Portal.
- Jak używać dzienników zdarzeń i liczników wydajności na serwerach z systemem Windows do monitorowania kondycji Azure File Sync wdrożenia. 

## <a name="azure-monitor"></a>Azure Monitor

Użyj [Azure Monitor,](../../azure-monitor/overview.md) aby wyświetlać metryki i konfigurować alerty dotyczące synchronizacji, warstw w chmurze i łączności z serwerem.  

### <a name="metrics"></a>Metryki

Metryki dla Azure File Sync są domyślnie włączone i są wysyłane do usługi Azure Monitor co 15 minut.

**Jak wyświetlać Azure File Sync metryki w Azure Monitor**
1. Przejdź do usługi **synchronizacji magazynu w** chmurze **Azure Portal** i kliknij **pozycję Metryki.**
2. Kliknij menu **rozwijane** Metryka i wybierz metrykę, którą chcesz wyświetlić.

![Zrzut ekranu Azure File Sync metryk](media/storage-sync-files-troubleshoot/file-sync-metrics.png)

Następujące metryki dla Azure File Sync są dostępne w Azure Monitor:

| Nazwa metryki | Opis |
|-|-|
| Zsynchronizowane bajty | Rozmiar przesyłanych danych (przekazywanie i pobieranie).<br><br>Jednostka: Bajty<br>Typ agregacji: Suma<br>Odpowiednie wymiary: nazwa punktu końcowego serwera, kierunek synchronizacji, nazwa grupy synchronizacji |
| Odwoływanie się do warstw w chmurze | Rozmiar odwołanych danych.<br><br>**Uwaga:** ta metryka zostanie usunięta w przyszłości. Użyj metryki Rozmiar odwołań warstw w chmurze, aby monitorować rozmiar odwołanych danych.<br><br>Jednostka: Bajty<br>Typ agregacji: Suma<br>Wymiar, który ma zastosowanie: Nazwa serwera |
| Rozmiar odwołań dla warstw w chmurze | Rozmiar odwołanych danych.<br><br>Jednostka: Bajty<br>Typ agregacji: Suma<br>Odpowiednie wymiary: Nazwa serwera, Nazwa grupy synchronizacji |
| Rozmiar odwołań dla warstw w chmurze według aplikacji | Rozmiar danych przywołynych przez aplikację.<br><br>Jednostka: Bajty<br>Typ agregacji: Suma<br>Odpowiednie wymiary: Nazwa aplikacji, Nazwa serwera, Nazwa grupy synchronizacji |
| Przepływność odwoływania do warstwy w chmurze | Rozmiar przepływności odwoływania danych.<br><br>Jednostka: Bajty<br>Typ agregacji: Suma<br>Odpowiednie wymiary: Nazwa serwera, Nazwa grupy synchronizacji |
| Pliki nie są synchronizowane | Liczba plików, których nie można zsynchronizować.<br><br>Jednostka: Liczba<br>Typy agregacji: średnia, suma<br>Odpowiednie wymiary: Nazwa punktu końcowego serwera, Kierunek synchronizacji, Nazwa grupy synchronizacji |
| Zsynchronizowane pliki | Liczba transferowanych plików (przekazywanie i pobieranie).<br><br>Jednostka: Liczba<br>Typ agregacji: Suma<br>Odpowiednie wymiary: Nazwa punktu końcowego serwera, Kierunek synchronizacji, Nazwa grupy synchronizacji |
| Stan online serwera | Liczba pulsów odebranych z serwera.<br><br>Jednostka: Liczba<br>Typ agregacji: Maksimum<br>Wymiar, który ma zastosowanie: Nazwa serwera |
| Wynik sesji synchronizacji | Wynik sesji synchronizacji (1 = pomyślna sesja synchronizacji; 0 = nieudana sesja synchronizacji)<br><br>Jednostka: Liczba<br>Typy agregacji: maksimum<br>Odpowiednie wymiary: nazwa punktu końcowego serwera, kierunek synchronizacji, nazwa grupy synchronizacji |

### <a name="alerts"></a>Alerty

Alerty proaktywnie powiadamiają o ważnych warunkach znalezionych w danych monitorowania. Aby dowiedzieć się więcej na temat konfigurowania alertów w programie Azure Monitor, zobacz [Omówienie alertów w programie Microsoft Azure](../../azure-monitor/alerts/alerts-overview.md).

**Jak tworzyć alerty dla Azure File Sync**

1. Przejdź do usługi **synchronizacji magazynu w** Azure Portal .  
2. Kliknij **pozycję Alerty** w sekcji Monitorowanie, a następnie **kliknij pozycję + Nowa reguła alertu.**
3. Kliknij **pozycję Wybierz warunek** i podaj następujące informacje dotyczące alertu: 
    - **Metryka**
    - **Nazwa wymiaru**
    - **Logika alertów**
4. Kliknij **pozycję Wybierz grupę** akcji i dodaj grupę akcji (e-mail, SMS itp.) do alertu, wybierając istniejącą grupę akcji lub tworząc nową grupę akcji.
5. Wypełnij pola **Szczegóły alertu,** takie jak **Nazwa reguły alertu,** **Opis** i **Ważność.**
6. Kliknij **pozycję Utwórz regułę alertu,** aby utworzyć alert.  

W poniższej tabeli wymieniono kilka przykładowych scenariuszy monitorowania oraz odpowiednią metrykę do użycia dla alertu:

| Scenariusz | Metryka do użycia dla alertu |
|-|-|
| Kondycja punktu końcowego serwera wyświetla błąd w portalu | Wynik sesji synchronizacji |
| Nie można zsynchronizować plików z serwerem lub punktem końcowym w chmurze | Pliki nie są synchronizowane |
| Zarejestrowany serwer nie może komunikować się z usługą synchronizacji magazynu | Stan online serwera |
| Rozmiar odwołań dla warstw w chmurze przekroczył 500GiB w ciągu dnia  | Rozmiar odwołań dla warstw w chmurze |

Aby uzyskać instrukcje dotyczące tworzenia alertów dla tych scenariuszy, zobacz [sekcję Przykłady alertów.](#alert-examples)

## <a name="storage-sync-service"></a>Usługa synchronizacji magazynu

Aby wyświetlić kondycję wdrożenia Azure File Sync w usłudze **Azure Portal,** przejdź  do usługi synchronizacji magazynu i dostępne są następujące informacje:

- Kondycja zarejestrowanego serwera
- Kondycja punktu końcowego serwera
    - Pliki nie są synchronizowane
    - Działanie synchronizacji
    - Wydajność warstw w chmurze
    - Pliki nie są warstwowe
    - Błędy odwoływania
- Metryki

### <a name="registered-server-health"></a>Kondycja zarejestrowanego serwera

Aby wyświetlić **kondycję zarejestrowanego serwera** w portalu, przejdź do sekcji **Zarejestrowane** serwery w **usłudze synchronizacji magazynu.**

![Zrzut ekranu przedstawiający kondycję zarejestrowanych serwerów](media/storage-sync-files-troubleshoot/file-sync-registered-servers.png)

- Jeśli stan **zarejestrowanego serwera to** **Online,** serwer pomyślnie komunikuje się z usługą.
- Jeśli stan **zarejestrowanego serwera** to Pojawia się w trybie **offline,** proces monitora synchronizacji magazynu (AzureStorageSyncMonitor.exe) nie jest uruchomiony lub serwer nie może uzyskać dostępu do Azure File Sync usługi. Wskazówki można [znaleźć w dokumentacji dotyczącej](file-sync-troubleshoot.md?tabs=portal1%252cazure-portal#server-endpoint-noactivity) rozwiązywania problemów.

### <a name="server-endpoint-health"></a>Kondycja punktu końcowego serwera

Aby wyświetlić kondycję punktu końcowego serwera w  portalu, przejdź  do sekcji Grupy synchronizacji usługi synchronizacji magazynu i wybierz **grupę synchronizacji.** 

![Zrzut ekranu przedstawiający kondycję punktu końcowego serwera](media/storage-sync-files-troubleshoot/file-sync-server-endpoint-health.png)

- Działanie **kondycji**  i synchronizacji punktu końcowego serwera w portalu jest oparte na zdarzeniach synchronizacji zarejestrowanych w dzienniku zdarzeń telemetrii na serwerze (identyfikatory 9102 i 9302). Jeśli sesja synchronizacji nie powiedzie się z powodu błędu przejściowego, takiego  jak anulowanie błędu, punkt końcowy serwera będzie nadal wyświetlany jako w dobrej kondycji w portalu, o ile trwa bieżąca sesja synchronizacji (są stosowane pliki). Identyfikator zdarzenia 9302 to zdarzenie postępu synchronizacji, a zdarzenie o identyfikatorze 9102 jest rejestrowane po zakończeniu sesji synchronizacji.  Aby uzyskać więcej informacji, zobacz [Synchronizacja kondycji](file-sync-troubleshoot.md?tabs=server%252cazure-portal#broken-sync) [i postęp synchronizacji](file-sync-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session). Jeśli kondycja punktu końcowego serwera ma wartość **Błąd** lub **Brak aktywności,** zapoznaj się ze wskazówkami w [dokumentacji rozwiązywania](file-sync-troubleshoot.md?tabs=portal1%252cazure-portal#common-sync-errors) problemów.
- Liczba **plików, które nie są synchronizowane** w portalu, jest oparta na identyfikatorze zdarzenia 9121, które jest rejestrowane w dzienniku zdarzeń telemetrii na serwerze. To zdarzenie jest rejestrowane dla każdego błędu poszczególnych elementów po zakończeniu sesji synchronizacji. Aby rozwiązać problemy z błędami poszczególnych elementów, zobacz Jak mogę czy istnieją określone pliki lub foldery, które [nie są synchronizowane?](file-sync-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).
- Aby wyświetlić wydajność **warstw w chmurze** w portalu, przejdź do sekcji **Właściwości** punktu końcowego serwera i przejdź do sekcji **Cloud Tiering (Warstwy w** chmurze). Dane dotyczące wydajności obsługi warstw w chmurze są oparte na identyfikatorze zdarzenia 9071, które jest rejestrowane w dzienniku zdarzeń telemetrii na serwerze. Aby dowiedzieć się więcej, zobacz [Monitorowanie warstw w chmurze.](file-sync-monitor-cloud-tiering.md)
- Aby wyświetlić **pliki, które nie mają warstw** i  błędy odwoływania się do nich w portalu, przejdź do sekcji Właściwości punktu końcowego serwera i przejdź do sekcji Cloud **Tiering (Warstwy w** chmurze).  **Warstwy plików nie** są oparte na identyfikatorze zdarzenia 9003, które jest  rejestrowane w dzienniku zdarzeń telemetrii na serwerze, a błędy odwoływania są oparte na identyfikatorze zdarzenia 9006. Aby zbadać pliki, których nie można przesłonić lub odwołać, zobacz How to troubleshoot files [that fail to tier](file-sync-troubleshoot.md?tabs=portal1%252cazure-portal#how-to-troubleshoot-files-that-fail-to-tier) (Jak rozwiązywać problemy z plikami, których nie można odwołać) i How to troubleshoot files that fail to be recalled (Jak rozwiązywać problemy z plikami, których nie można [odwołać).](file-sync-troubleshoot.md?tabs=portal1%252cazure-portal#how-to-troubleshoot-files-that-fail-to-be-recalled)

### <a name="metric-charts"></a>Wykresy metryk

- Następujące wykresy metryk są dostępne w portalu usługi synchronizacji magazynu:

  | Nazwa metryki | Opis | Nazwa bloku |
  |-|-|-|
  | Zsynchronizowane bajty | Rozmiar przesyłanych danych (przekazywanie i pobieranie) | Grupa synchronizacji, punkt końcowy serwera |
  | Odwoływanie się do warstw w chmurze | Rozmiar odwołanych danych | Zarejestrowane serwery |
  | Pliki nie są synchronizowane | Liczba plików, których nie można zsynchronizować | Punkt końcowy serwera |
  | Zsynchronizowane pliki | Liczba transferowanych plików (przekazywanie i pobieranie) | Grupa synchronizacji, punkt końcowy serwera |
  | Stan online serwera | Liczba pulsów odebranych z serwera | Zarejestrowane serwery |

- Aby dowiedzieć się więcej, [zobacz Azure Monitor](#azure-monitor).

  > [!Note]  
  > Wykresy w portalu usługi synchronizacji magazynu mają zakres czasu 24 godziny. Aby wyświetlić różne zakresy czasu lub wymiary, użyj Azure Monitor.

## <a name="windows-server"></a>Windows Server

W systemie **Windows Server** z zainstalowanym agentem Azure File Sync można wyświetlić kondycję punktów końcowych  serwera na tym serwerze przy użyciu dzienników zdarzeń i **liczników wydajności.**

### <a name="event-logs"></a>Dzienniki zdarzeń

Użyj dziennika zdarzeń telemetrii na serwerze, aby monitorować kondycję zarejestrowanego serwera, synchronizacji i obsługi warstw w chmurze. Dziennik zdarzeń telemetrii znajduje się w Podgląd zdarzeń w obszarze *Aplikacje i usługi\Microsoft\FileSync\Agent.*

Kondycja synchronizacji

- Identyfikator zdarzenia 9102 jest rejestrowany po zakończeniu sesji synchronizacji. Użyj tego zdarzenia, aby określić, czy sesje synchronizacji są pomyślne **(HResult = 0**) i czy występują błędy synchronizacji 1 elementu (**PerItemErrorCount**). Aby uzyskać więcej informacji, zobacz [dokumentację dotyczącą kondycji synchronizacji](file-sync-troubleshoot.md?tabs=server%252cazure-portal#broken-sync) i  [błędów 1](file-sync-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) elementu.

  > [!Note]  
  > Czasami sesje synchronizacji ogólnie się nie powiodą lub mają niezerową wartość PerItemErrorCount. Nadal jednak postępują, a niektóre pliki są pomyślnie synchronizowane. Można to zobaczyć w polach Zastosowane, takich jak AppliedFileCount, AppliedDirCount, AppliedTombstoneCount i AppliedSizeBytes. Te pola informują o tym, jaka część sesji zakończyła się pomyślnie. Jeśli w wierszu widzisz, że wiele sesji synchronizacji nie powiedzie się i ich liczba rośnie, przed otwarciem biletu pomocy technicznej należy dać czas synchronizacji, aby spróbować ponownie.

- Po zakończeniu sesji synchronizacji dla każdego błędu elementu rejestrowane jest zdarzenie o identyfikatorze 9121. Użyj tego zdarzenia, aby określić liczbę plików, których nie można zsynchronizować z tym błędem **(PersistentCount** i **TransientCount).** Należy zbadać trwałe błędy poszczególnych elementów. Zobacz Jak mogę, czy istnieją określone pliki lub foldery, które [nie są synchronizowane?](file-sync-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).

- Zdarzenie o identyfikatorze 9302 jest rejestrowane co 5–10 minut, jeśli istnieje aktywna sesja synchronizacji. To zdarzenie pozwala określić liczbę elementów do zsynchronizowania **(TotalItemCount),** liczbę elementów, które zostały zsynchronizowane do tej pory **(AppliedItemCount)** i liczbę elementów, których synchronizacja nie powiodła się z powodu błędu 1 elementu **(PerItemErrorCount).** Jeśli synchronizacja nie postępuje **(AppliedItemCount=0),** sesja synchronizacji ostatecznie nie powiedzie się, a zdarzenie o identyfikatorze 9102 zostanie zarejestrowane z błędem. Aby uzyskać więcej informacji, zobacz [dokumentację postępu synchronizacji](file-sync-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).

Kondycja zarejestrowanego serwera

- Identyfikator zdarzenia 9301 jest rejestrowany co 30 sekund, gdy serwer wysyła zapytanie do usługi o zadania. Jeśli getNextJob zakończy się ze **stanem = 0,** serwer może komunikować się z usługą. Jeśli getNextJob zakończy się z błędem, zapoznaj się z [dokumentacją rozwiązywania problemów,](file-sync-troubleshoot.md?tabs=portal1%252cazure-portal#server-endpoint-noactivity) aby uzyskać wskazówki.

Kondycja warstw w chmurze

- Aby monitorować działanie obsługi warstw na serwerze, użyj identyfikatorów zdarzeń 9003, 9016 i 9029 w dzienniku zdarzeń telemetrii, który znajduje się w programie Podgląd zdarzeń w obszarze Aplikacje i *usługi\Microsoft\FileSync\Agent.*

  - Identyfikator zdarzenia 9003 zapewnia dystrybucję błędów dla punktu końcowego serwera. Na przykład: Łączna liczba błędów i Kod błędu. Jedno zdarzenie jest rejestrowane na kod błędu.
  - Identyfikator zdarzenia 9016 zawiera wyniki dla woluminu. Na przykład: Procent wolnego miejsca to, Liczba plików w sesji wydychanych i Liczba plików nie powiodło się.
  - Zdarzenie o identyfikatorze 9029 zawiera informacje o sesji w przypadku punktu końcowego serwera. Na przykład: liczba plików, które podjęto próbę w sesji, liczba plików w sesji warstwowych i liczba plików już warstwowych.

- Aby monitorować aktywność odwoływania na serwerze, użyj zdarzeń o identyfikatorach 9005, 9006, 9009, 9059 i 9071 w dzienniku zdarzeń telemetrii, który znajduje się w folderze Podgląd zdarzeń w obszarze Aplikacje i *usługi\Microsoft\FileSync\Agent.*

  - Identyfikator zdarzenia 9005 zapewnia niezawodność odwoływania dla punktu końcowego serwera. Na przykład: Łączna liczba unikatowych plików, do których uzyskano dostęp, i Łączna liczba unikatowych plików z nieudanym dostępem.
  - Identyfikator zdarzenia 9006 zapewnia dystrybucję błędów odwołania dla punktu końcowego serwera. Na przykład: Łączna liczba żądań nieudanych i Kod błędu. Jedno zdarzenie jest rejestrowane na kod błędu.
  - Zdarzenie o identyfikatorze 9009 zawiera informacje o sesji odwołania dla punktu końcowego serwera. Na przykład: DurationSeconds, CountFilesRecallSucceeded i CountFilesRecallFailed.
  - Zdarzenie o identyfikatorze 9059 zapewnia dystrybucję odwołania aplikacji dla punktu końcowego serwera. Na przykład: ShareId, Application Name i TotalEgressNetworkBytes.
  - Identyfikator zdarzenia 9071 zapewnia wydajność obsługi warstw w chmurze dla punktu końcowego serwera. Na przykład: TotalDistinctFileCountCacheHit, TotalDistinctFileCountCacheMiss, TotalCacheHitBytes i TotalCacheMissBytes.

### <a name="performance-counters"></a>Liczniki wydajności

Użyj Azure File Sync liczników wydajności na serwerze, aby monitorować aktywność synchronizacji.

Aby wyświetlić Azure File Sync wydajności na serwerze, otwórz monitor wydajności (Perfmon.exe). Liczniki znajdują się w obszarze **obiektów Przesłane bajty AFS** i **Operacje synchronizacji afs.**

Następujące liczniki wydajności dla Azure File Sync są dostępne w monitor wydajności:

| Performance Object\Counter Name | Opis |
|-|-|
| Bajty afs przeniesione\pobrane bajty/s | Liczba pobranych bajtów na sekundę. |
| Przesłane bajty AFS\Przekazane bajty/s | Liczba przekazanych bajtów na sekundę. |
| Przesłane bajty AFS\Całkowita liczba bajtów/s | Całkowita liczba bajtów na sekundę (przekazywanie i pobieranie). |
| Operacje synchronizacji usługi AFS\Pobrane pliki synchronizacji/s | Liczba pobranych plików na sekundę. |
| Operacje synchronizacji usługi AFS\Przekazane pliki synchronizacji/s | Liczba przekazanych plików na sekundę. |
| Operacje synchronizacji usługi AFS\Łączna liczba operacji na plikach synchronizacji/s | Łączna liczba zsynchronizowanych plików (przekazywanie i pobieranie). |

## <a name="alert-examples"></a>Przykłady alertów
Ta sekcja zawiera przykładowe alerty dla Azure File Sync.

  > [!Note]  
  > Jeśli tworzysz alert i jest on zbyt zaszumiiony, dostosuj wartość progową i logikę alertu.

### <a name="how-to-create-an-alert-if-the-server-endpoint-health-shows-an-error-in-the-portal"></a>Jak utworzyć alert, jeśli kondycja punktu końcowego serwera wyświetla błąd w portalu

1. W **Azure Portal** przejdź do odpowiedniej **usługi synchronizacji magazynu.** 
2. Przejdź do sekcji **Monitorowanie i** kliknij pozycję **Alerty.** 
3. Kliknij pozycję **+ Nowa reguła alertu,** aby utworzyć nową regułę alertu. 
4. Skonfiguruj warunek, klikając **pozycję Wybierz warunek**.
5. W **bloku Konfigurowanie logiki sygnału** kliknij pozycję **Sync session result (Synchronizuj wynik sesji)** pod nazwą sygnału.  
6. Wybierz następującą konfigurację wymiaru: 
     - Nazwa wymiaru: **Nazwa punktu końcowego serwera**  
     - Operator: **=** 
     - Wartości wymiarów: **wszystkie bieżące i przyszłe wartości**  
7. Przejdź do **logiki alertów** i wykonaj następujące czynności: 
     - Próg ustawiony na **statyczny** 
     - Operator: **Mniejsze niż** 
     - Typ agregacji: **Maksimum**  
     - Wartość progu: **1** 
     - Oceniane na podstawie: Poziom szczegółowości agregacji = **24 godziny** | Częstotliwość oceny = **co godzinę** 
     - Kliknij **przycisk Gotowe.** 
8. Kliknij **pozycję Wybierz grupę akcji,** aby dodać grupę akcji (wiadomość e-mail, wiadomość SMS itp.) do alertu, wybierając istniejącą grupę akcji lub tworząc nową grupę akcji.
9. Wypełnij pola **Szczegóły alertu,** takie jak **Nazwa reguły alertu,** **Opis** i **Ważność.**
10. Kliknij pozycję **Utwórz regułę alertu**. 

### <a name="how-to-create-an-alert-if-files-are-failing-to-sync-to-a-server-or-cloud-endpoint"></a>Jak utworzyć alert, jeśli nie można zsynchronizować plików z serwerem lub punktem końcowym w chmurze

1. W **Azure Portal** przejdź do odpowiedniej **usługi synchronizacji magazynu.** 
2. Przejdź do sekcji **Monitorowanie i** kliknij pozycję **Alerty.** 
3. Kliknij pozycję **+ Nowa reguła alertu,** aby utworzyć nową regułę alertu. 
4. Skonfiguruj warunek, klikając **pozycję Wybierz warunek**.
5. W **bloku Konfigurowanie logiki sygnału** kliknij pozycję **Pliki nie są synchronizowane pod** nazwą sygnału.  
6. Wybierz następującą konfigurację wymiaru: 
     - Nazwa wymiaru: **Nazwa punktu końcowego serwera**  
     - Operator: **=** 
     - Wartości wymiarów: **wszystkie bieżące i przyszłe wartości**  
7. Przejdź do **logiki alertów** i wykonaj następujące czynności: 
     - Próg ustawiony na **statyczny** 
     - Operator: **Większe niż** 
     - Typ agregacji: **Średnia**  
     - Wartość progowa: **100** 
     - Oceniane na podstawie: Poziom szczegółowości agregacji = **5** minut | Częstotliwość oceny = **co 5 minut** 
     - Kliknij **przycisk Gotowe.** 
8. Kliknij **pozycję Wybierz grupę akcji,** aby dodać grupę akcji (wiadomość e-mail, wiadomość SMS itp.) do alertu, wybierając istniejącą grupę akcji lub tworząc nową grupę akcji.
9. Wypełnij pola **Szczegóły alertu,** takie jak **Nazwa reguły alertu,** **Opis** i **Ważność.**
10. Kliknij pozycję **Utwórz regułę alertu**. 

### <a name="how-to-create-an-alert-if-a-registered-server-is-failing-to-communicate-with-the-storage-sync-service"></a>Jak utworzyć alert, jeśli zarejestrowany serwer nie może komunikować się z usługą synchronizacji magazynu

1. W **Azure Portal** przejdź do odpowiedniej **usługi synchronizacji magazynu.** 
2. Przejdź do sekcji **Monitorowanie i** kliknij pozycję **Alerty.** 
3. Kliknij pozycję **+ Nowa reguła alertu,** aby utworzyć nową regułę alertu. 
4. Skonfiguruj warunek, klikając **pozycję Wybierz warunek**.
5. W **bloku Konfigurowanie logiki sygnału** kliknij pozycję **Stan online serwera w** obszarze nazwa sygnału.  
6. Wybierz następującą konfigurację wymiaru: 
     - Nazwa wymiaru: **Nazwa serwera**  
     - Operator: **=** 
     - Wartości wymiarów: **wszystkie bieżące i przyszłe wartości**  
7. Przejdź do **logiki alertów** i wykonaj następujące czynności: 
     - Próg ustawiony na **statyczny** 
     - Operator: **Mniejsze niż** 
     - Typ agregacji: **Maksimum**  
     - Wartość progowa (w bajtach): **1** 
     - Oceniane na podstawie: Poziom szczegółowości agregacji = **1 godzina** | Częstotliwość oceny = **co 30 minut** 
         - Należy pamiętać, że metryki są wysyłane do Azure Monitor co 15–20 minut. Nie należy ustawiać **częstotliwości oceny na** mniej niż 30 minut (spowoduje to wygenerowanie fałszywych alertów).
     - Kliknij **przycisk Gotowe.** 
8. Kliknij **pozycję Wybierz grupę akcji,** aby dodać grupę akcji (wiadomość e-mail, wiadomość SMS itp.) do alertu, wybierając istniejącą grupę akcji lub tworząc nową grupę akcji.
9. Wypełnij pola **Szczegóły alertu,** takie jak **Nazwa reguły alertu,** **Opis** i **Ważność.**
10. Kliknij pozycję **Utwórz regułę alertu**. 

### <a name="how-to-create-an-alert-if-the-cloud-tiering-recall-size-has-exceeded-500gib-in-a-day"></a>Jak utworzyć alert, jeśli rozmiar przywołania dla warstw w chmurze przekroczył 500GiB w ciągu dnia

1. W **Azure Portal** przejdź do odpowiedniej **usługi synchronizacji magazynu.** 
2. Przejdź do sekcji **Monitorowanie i** kliknij pozycję **Alerty.** 
3. Kliknij pozycję **+ Nowa reguła alertu,** aby utworzyć nową regułę alertu. 
4. Skonfiguruj warunek, klikając **pozycję Wybierz warunek**.
5. W **bloku Konfigurowanie logiki sygnału** kliknij pozycję **Rozmiar odwoływu dla warstw w chmurze** w obszarze nazwa sygnału.  
6. Wybierz następującą konfigurację wymiaru: 
     - Nazwa wymiaru: **Nazwa serwera**  
     - Operator: **=** 
     - Wartości wymiarów: **wszystkie bieżące i przyszłe wartości**  
7. Przejdź do **logiki alertów** i wykonaj następujące czynności: 
     - Próg ustawiony na **statyczny** 
     - Operator: **Większe niż** 
     - Typ agregacji: **Suma**  
     - Wartość progowa (w bajtach): **67108864000** 
     - Oceniane na podstawie: Poziom szczegółowości agregacji = **24 godziny** | Częstotliwość oceny = **co godzinę** 
     - Kliknij **przycisk Gotowe.** 
8. Kliknij **pozycję Wybierz grupę akcji,** aby dodać grupę akcji (wiadomość e-mail, wiadomość SMS itp.) do alertu, wybierając istniejącą grupę akcji lub tworząc nową grupę akcji.
9. Wypełnij pola **Szczegóły alertu,** takie jak **Nazwa reguły alertu,** **Opis** i **Ważność.**
10. Kliknij pozycję **Utwórz regułę alertu**. 

## <a name="next-steps"></a>Następne kroki
- [Planowanie wdrażania usługi Azure File Sync](file-sync-planning.md)
- [Rozważ ustawienia zapory i serwera proxy](file-sync-firewall-and-proxy.md)
- [Wdrażanie funkcji Azure File Sync](file-sync-deployment-guide.md)
- [Rozwiązywanie problemów z usługą Azure File Sync](file-sync-troubleshoot.md)