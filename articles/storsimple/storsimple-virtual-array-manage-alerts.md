---
title: Wyświetl alerty dla macierzy wirtualnej StorSimple i zarządzaj nimi
description: Opisuje warunki i ważność alertów macierzy wirtualnej StorSimple oraz sposób używania usługi StorSimple Manager do zarządzania alertami.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 97ee25a1-0ec3-4883-9a0a-54b722598462
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/12/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 12fcc9996697f3bbba35826d79bec238bfb0f8b3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993003"
---
# <a name="use-storsimple-device-manager-to-manage-alerts-for-the-storsimple-virtual-array"></a>Używanie StorSimple Menedżer urządzeń do zarządzania alertami dla macierzy wirtualnej StorSimple

## <a name="overview"></a>Omówienie

Funkcja alertów w usłudze StorSimple Menedżer urządzeń umożliwia przeglądanie i czyszczenie alertów związanych z StorSimple wirtualnymi macierzy w czasie rzeczywistym. Możesz użyć alertów w bloku **Podsumowanie usługi** , aby centralnie monitorować problemy z kondycją macierzy wirtualnych StorSimple oraz ogólne rozwiązanie Microsoft Azure StorSimple.

W tym samouczku opisano sposób konfigurowania powiadomień o alertach, typowych warunków alertów, poziomów ważności alertów oraz sposobu wyświetlania i śledzenia alertów. Oprócz tego zawiera tabele szybkich odwołań alertów, które umożliwiają szybkie lokalizowanie określonego alertu i odpowiednie reagowanie.

![Strona alerty](./media/storsimple-virtual-array-manage-alerts/alerts1.png)

## <a name="configure-alert-settings"></a>Konfigurowanie ustawień alertów

Możesz wybrać, czy chcesz otrzymywać powiadomienia pocztą e-mail o warunkach alertów dla każdej z wirtualnych macierzy StorSimple. Ponadto możesz identyfikować innych adresatów powiadomień o alertach, wprowadzając ich adresy e-mail w polu **dodatkowi adresaci wiadomości e-mail** , rozdzielając je średnikami.

> [!NOTE]
> Możesz wprowadzić maksymalnie 20 adresów e-mail na tablicę wirtualną.

Po włączeniu powiadomienia e-mail dla macierzy wirtualnej członkowie listy powiadomień otrzymają wiadomość e-mail za każdym razem, gdy wystąpi alert krytyczny. Komunikaty będą wysyłane z *StorSimple-Alerts-noreply \@ mail.windowsazure.com* i opisują warunek alertu. Adresaci mogą kliknąć przycisk **Anuluj subskrypcję** , aby usunąć siebie z listy powiadomień e-mail.

#### <a name="to-enable-email-notification-for-alerts"></a>Aby włączyć powiadomienia e-mail dla alertów

1. Przejdź do usługi StorSimple Menedżer urządzeń i w sekcji **Zarządzanie** wybierz pozycję **urządzenia**. Z wyświetlonej listy urządzeń wybierz i kliknij urządzenie.
   
    ![Ustawienia alertu](./media/storsimple-virtual-array-manage-alerts/alerts2.png)
2. Spowoduje to otwarcie bloku **Ustawienia** . W sekcji **Ustawienia urządzenia** wybierz pozycję **Ogólne**. Spowoduje to otwarcie bloku **Ustawienia ogólne** .
   
    ![Zrzut ekranu przedstawia okienko ustawień urządzenia z obszarem ustawień alertów o nazwie out.](./media/storsimple-virtual-array-manage-alerts/alerts4.png)
3. W bloku **Ustawienia ogólne** przejdź do sekcji **Ustawienia alertu** i ustaw następujące opcje:
   
   1. W polu **Włącz powiadomienie e-mail** wybierz pozycję **tak**.
   2. W polu **Administratorzy usługi poczty e-mail** wybierz pozycję **tak** , jeśli chcesz, aby administrator usługi i wszyscy współadministratorzy otrzymywali powiadomienia o alertach.
   3. W polu **dodatkowi adresaci wiadomości e-mail** wprowadź adresy e-mail wszystkich adresatów, którzy powinni otrzymywać powiadomienia o alertach. Wprowadź nazwy w formacie *ktoś \@ Somewhere.com*. Użyj średników, aby rozdzielić adresy e-mail. Można skonfigurować maksymalnie 20 adresów e-mail na urządzenie wirtualne.
      
       ![Zrzut ekranu przedstawia szczegóły ustawień alertów z ustawieniami opisanymi w tym kroku.](./media/storsimple-virtual-array-manage-alerts/alerts6.png)
   4. Aby wysłać testową wiadomość e-mail z powiadomieniem, kliknij pozycję **Wyślij testową wiadomość e-mail**. Usługa StorSimple Menedżer urządzeń będzie wyświetlać komunikaty o stanie podczas przesyłania dalej powiadomienia testowego.
      
       ![Zrzut ekranu przedstawia okno dialogowe z informacją weryfikującą testową wiadomość e-mail.](./media/storsimple-virtual-array-manage-alerts/alerts7.png)
      
      > [!NOTE]
      > Jeśli nie można wysłać komunikatu z powiadomieniem o testach, w usłudze StorSimple Menedżer urządzeń zostanie wyświetlony odpowiedni komunikat. Kliknij przycisk **OK**, odczekaj kilka minut, a następnie spróbuj ponownie wysłać testową wiadomość e-mail.
      >
      >
   5. W dolnej części strony kliknij pozycję **Zapisz** , aby zapisać konfigurację. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak**.
      
      ![Zrzut ekranu przedstawia okienko ustawień z wybranym przyciskiem Zapisz.](./media/storsimple-virtual-array-manage-alerts/alerts10.png)

## <a name="common-alert-conditions"></a>Typowe warunki alertów

Wirtualna macierz StorSimple generuje alerty w odpowiedzi na różne warunki. Poniżej przedstawiono najczęściej spotykane typy warunków alertów:

* **Problemy z łącznością** — te alerty występują w przypadku trudności związanych z transferem danych. Problemy z komunikacją mogą wystąpić podczas transferu danych do i z konta usługi Azure Storage lub z powodu braku łączności między urządzeniami wirtualnymi a usługą StorSimple Menedżer urządzeń. Problemy z komunikacją są nieco trudne do rozwiązania, ponieważ istnieje wiele punktów awarii. Przed kontynuowaniem bardziej zaawansowanego rozwiązywania problemów należy zawsze sprawdzić, czy łączność sieciowa i dostęp do Internetu są dostępne. Aby uzyskać informacje o portach i ustawieniach zapory, przejdź do [StorSimple wirtualnej macierzy wirtualnych wymagania systemowe](storsimple-ova-system-requirements.md). Aby uzyskać pomoc dotyczącą rozwiązywania problemów, przejdź do [obszaru Rozwiązywanie problemów za pomocą polecenia cmdlet Test-Connection](./storsimple-8000-troubleshoot-deployment.md).
* **Problemy z wydajnością** — te alerty są generowane, gdy system nie działa optymalnie, na przykład gdy jest w dużym obciążeniu.

Ponadto mogą pojawić się alerty związane z zabezpieczeniami, aktualizacjami lub niepowodzeńmi zadań.

## <a name="alert-severity-levels"></a>Poziomy ważności alertu

Alerty mają różne poziomy ważności, w zależności od wpływu działania alertu i konieczności odpowiedzi na alert. Poziomy ważności to:

* **Krytyczny** — ten alert jest w odpowiedzi na warunek, który ma wpływ na pomyślną wydajność systemu. Wymagana jest akcja, aby upewnić się, że Usługa StorSimple nie została przerwana.
* **Ostrzeżenie** — ten stan może stać się krytyczny, jeśli nie został rozpoznany. Należy zbadać sytuację i podjąć wszelkie działania wymagane do wyczyszczenia problemu.
* **Informacje** — ten alert zawiera informacje, które mogą być przydatne do śledzenia systemu i zarządzania nim.

## <a name="view-and-track-alerts"></a>Wyświetlanie i śledzenie alertów

Blok podsumowania usługi StorSimple Menedżer urządzeń zapewnia szybki przegląd liczby alertów na urządzeniach wirtualnych uporządkowanych według poziomu ważności.

![Pulpit nawigacyjny alertów](./media/storsimple-virtual-array-manage-alerts/alerts14.png)

Kliknij poziom ważności, aby otworzyć blok **alerty** . Wyniki obejmują tylko alerty pasujące do tego poziomu ważności.

![Raport o alertach objęty zakresem alertu](./media/storsimple-virtual-array-manage-alerts/alerts15.png)

Kliknij alert na liście, aby uzyskać dodatkowe szczegóły dotyczące alertu, w tym czas ostatniego zgłoszenia alertu, liczbę wystąpień alertu na urządzeniu oraz zalecaną akcję w celu rozwiązania alertu.

![Lista alertów i szczegóły](./media/storsimple-virtual-array-manage-alerts/alerts16.png)

Możesz skopiować szczegóły alertu do pliku tekstowego, jeśli trzeba wysłać informacje do pomoc techniczna firmy Microsoft. Po wykonaniu zalecenia i rozwiązaniu lokalnego stanu alertu należy wyczyścić ten alert z listy. Wybierz z listy alert, a następnie kliknij przycisk **Wyczyść**. Aby wyczyścić wiele alertów, zaznacz poszczególne alerty, kliknij dowolną kolumnę poza kolumną **alertu** , a następnie kliknij przycisk **Wyczyść** po wybraniu wszystkich alertów do wyczyszczenia.

Po kliknięciu przycisku **Wyczyść** będziesz mieć możliwość udostępnienia komentarzy dotyczących alertu i kroków, które należy wykonać w celu rozwiązania problemu.

![Komentarze alertu](./media/storsimple-virtual-array-manage-alerts/alerts17.png)

Niektóre zdarzenia zostaną wyczyszczone przez system, jeśli inne zdarzenie zostanie wyzwolone przy użyciu nowych informacji.

## <a name="sort-and-review-alerts"></a>Sortowanie i przeglądanie alertów

Blok **alerty** może wyświetlać do 250 alertów. W przypadku przekroczenia tej liczby alertów nie wszystkie alerty będą wyświetlane w widoku domyślnym. Można połączyć następujące pola w celu dostosowania wyświetlanych alertów:

* **Stan** — można wyświetlić **aktywne** lub **wyczyszczone** alerty. Aktywne alerty są nadal wyzwalane w systemie, podczas gdy wyczyszczone alerty zostały ręcznie wyczyszczone przez administratora lub programowo wyczyszczone, ponieważ system zaktualizował warunek alertu o nowe informacje.
* **Ważność** — można wyświetlić alerty o wszystkich poziomach ważności (krytyczne, ostrzegawcze, informacje) lub tylko o określonej ważności, takie jak tylko alerty krytyczne.
* **Źródło** — można wyświetlić alerty ze wszystkich źródeł lub ograniczyć alerty do tych, które pochodzą z usługi lub jednego lub wszystkich urządzeń wirtualnych.
* **Zakres czasu** — przez określenie daty **od** i **do** oraz sygnatur czasowych można sprawdzić alerty w odpowiednim czasie.

## <a name="alerts-quick-reference"></a>Szybkie informacje o alertach

W poniższej tabeli wymieniono niektóre z alertów StorSimple, które mogą wystąpić, a także dodatkowe informacje i zalecenia, jeśli są dostępne. StorSimple alerty macierzy wirtualnych należą do jednej z następujących kategorii:

* [Alerty łączności w chmurze](#cloud-connectivity-alerts)
* [Alerty konfiguracji](#configuration-alerts)
* [Alerty błędów zadań](#job-failure-alerts)
* [Alerty wydajności](#performance-alerts)
* [Alerty zabezpieczeń](#security-alerts)

### <a name="cloud-connectivity-alerts"></a>Alerty łączności w chmurze

| Tekst alertu | Zdarzenie | Więcej informacji/zalecane akcje |
|:--- |:--- |:--- |
| > *nazwy urządzenia* <urządzenia nie jest połączona z chmurą. |Nazwane urządzenie nie może nawiązać połączenia z chmurą. |Nie można nawiązać połączenia z chmurą. Może to być spowodowane jedną z następujących przyczyn:<ul><li>Być może wystąpił problem z ustawieniami sieci na urządzeniu.</li><li>Może wystąpić problem z poświadczeniami konta magazynu.</li></ul>Aby uzyskać więcej informacji na temat rozwiązywania problemów z łącznością, przejdź do [lokalnego interfejsu użytkownika sieci Web](storsimple-ova-web-ui-admin.md) urządzenia. |

### <a name="configuration-alerts"></a>Alerty konfiguracji

| Tekst alertu | Zdarzenie | Więcej informacji/zalecane akcje |
|:--- |:--- |:--- |
| Konfiguracja lokalnej urządzenia wirtualnego jest nieobsługiwana. |Niska wydajność. |Bieżąca konfiguracja może spowodować spadek wydajności. Upewnij się, że serwer spełnia minimalne wymagania dotyczące konfiguracji. Aby uzyskać więcej informacji, przejdź do [wymagań dotyczących macierzy wirtualnej StorSimple](storsimple-ova-system-requirements.md). |
| Wykorzystano miejsce na dysku w <*nazwie urządzenia* \> . |Ostrzeżenie dotyczące miejsca na dysku. |Używasz wolnego miejsca na dysku. Aby zwolnić miejsce, rozważ przeniesienie obciążeń na inny wolumin lub udział lub usunięcie danych. |

### <a name="job-failure-alerts"></a>Alerty błędów zadań

| Tekst alertu | Zdarzenie | Więcej informacji/zalecane akcje |
|:--- |:--- |:--- |
| Nie można ukończyć tworzenia kopii zapasowej *nazwy urządzenia* <\> . |Niepowodzenie zadania tworzenia kopii zapasowej. |Nie można utworzyć kopii zapasowej. Weź pod uwagę jedną z następujących czynności:<ul><li>Problemy z łącznością mogą uniemożliwiać pomyślne ukończenie operacji tworzenia kopii zapasowej. Upewnij się, że nie występują problemy z łącznością. Aby uzyskać więcej informacji na temat rozwiązywania problemów z łącznością, przejdź do [lokalnego interfejsu użytkownika sieci Web](storsimple-ova-web-ui-admin.md) dla urządzenia wirtualnego.</li><li>Osiągnięto limit ilości dostępnego miejsca. Aby zwolnić miejsce, Rozważ usunięcie wszystkich kopii zapasowych, które nie są już potrzebne.</li></ul> Rozwiąż problemy, Usuń alert i spróbuj ponownie wykonać operację. |
| Nie można ukończyć klonowania *nazwy urządzenia* <\> . |Niepowodzenie zadania klonowania. |Nie można utworzyć klonu. Weź pod uwagę jedną z następujących czynności:<ul><li>Lista kopii zapasowych może być nieprawidłowa. Odśwież listę, aby upewnić się, że jest ona nadal ważna.</li><li>Problemy z łącznością mogą uniemożliwiać pomyślne zakończenie operacji klonowania. Upewnij się, że nie występują problemy z łącznością.</li><li>Osiągnięto limit ilości dostępnego miejsca. Aby zwolnić miejsce, Rozważ usunięcie wszystkich kopii zapasowych, które nie są już potrzebne.</li></ul>Rozwiąż problemy, Usuń alert i spróbuj ponownie wykonać operację. |

### <a name="networking-alerts"></a>Alerty sieciowe

| Tekst alertu | Zdarzenie | Więcej informacji/zalecane akcje |
|:--- |:--- |:--- |
| Nie można nawiązać połączenia z usługą uwierzytelniania. |Błąd ścieżki datapath |Adres URL używany do uwierzytelniania jest nieosiągalny. Upewnij się, że reguły zapory zawierają wzorce URL określone dla urządzenia StorSimple. Aby uzyskać więcej informacji na temat wzorców adresów URL w Azure Portal, przejdź do [wymagań sieci wirtualnej macierzy StorSimple](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).|

### <a name="performance-alerts"></a>Alerty wydajności

| Tekst alertu | Zdarzenie | Więcej informacji/zalecane akcje |
|:--- |:--- |:--- |
| Występują nieoczekiwane opóźnienia w transferze danych. |Wolny transfer danych. |Błędy ograniczania są wykonywane, gdy przekroczy się tarcze skalowalności usługi magazynu. Usługa Storage wykonuje tę funkcję, aby upewnić się, że żaden klient ani dzierżawca nie mogą korzystać z usługi w kosztach innych. Aby uzyskać więcej informacji na temat rozwiązywania problemów z kontem usługi Azure Storage, przejdź do pozycji [monitorowanie, diagnozowanie i rozwiązywanie problemów Microsoft Azure Storage](../storage/common/storage-monitoring-diagnosing-troubleshooting.md). |
| Używasz wolnego miejsca na dysku rezerwacji lokalnej na <*nazwie urządzenia* \> . |Czas wolnej odpowiedzi. |10% całkowitego rozmiaru alokowanego dla *nazwy urządzenia* <\> jest zarezerwowane na urządzeniu lokalnym, a teraz zaczyna brakować miejsca zarezerwowanego. Obciążenie na <*urządzeniu* \> generuje wyższą częstotliwość zmian lub niedawno przeprowadzono migrację dużej ilości danych. Może to spowodować zmniejszenie wydajności. Aby rozwiązać ten problem, należy rozważyć jedną z następujących akcji:<ul><li>Zwiększ przepustowość chmury na tym urządzeniu.</li><li>Zmniejszenie lub przeniesienie obciążeń do innego woluminu lub udziału.</li></ul> |

### <a name="security-alerts"></a>Alerty zabezpieczeń

| Tekst alertu | Zdarzenie | Więcej informacji/zalecane akcje |
|:--- |:--- |:--- |
| Hasło dla *nazwy urządzenia* <\> wygaśnie za <*liczbę* \> dni. |Ostrzeżenie o haśle. |Twoje hasło wygaśnie za <*liczbę* \> dni. Rozważ zmianę hasła. Aby uzyskać więcej informacji, przejdź do [obszaru zmiana hasła administratora urządzenia macierzy wirtualnej StorSimple](storsimple-virtual-array-change-device-admin-password.md). |

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o macierzy wirtualnej StorSimple](storsimple-ova-overview.md).