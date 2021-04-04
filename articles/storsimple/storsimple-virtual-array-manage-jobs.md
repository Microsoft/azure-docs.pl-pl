---
title: Wyświetlanie zadań macierzy wirtualnych StorSimple i zarządzanie nimi | Microsoft Docs
description: Opisuje stronę zadania usługi StorSimple Device Manager i sposób jej używania do śledzenia ostatnich i bieżących zadań macierzy wirtualnej StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 31879821-b599-4609-a7f4-d4b0f658a933
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/11/2016
ms.author: alkohli
ms.openlocfilehash: d806d8a04dc1bd8547808d20c77bfec310f7dd06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95992990"
---
# <a name="use-the-storsimple-device-manager-service-to-view-jobs-for-the-storsimple-virtual-array"></a>Użyj usługi StorSimple Device Manager, aby wyświetlić zadania dla macierzy wirtualnej StorSimple
## <a name="overview"></a>Omówienie
Blok **zadania** zawiera pojedynczy Portal centralny do wyświetlania zadań i zarządzania nimi, które są uruchamiane w tablicach wirtualnych, które są połączone z usługą StorSimple Device Manager. Można wyświetlać zadania uruchomione, ukończone i zakończone niepowodzeniem dla wielu urządzeń wirtualnych. Wyniki są prezentowane w formacie tabelarycznym.

![Blok zadań](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)

Możesz szybko znaleźć interesujące Cię zadania, filtrując pola, takie jak:

* **Zakres czasu** — zadania można filtrować na podstawie daty i zakresu czasu.
* **Urządzenia** — zadania są inicjowane na określonym urządzeniu podłączonym do usługi. Przefiltrowane zadania są następnie wykonywane na podstawie następujących atrybutów:
  
  * **Nazwa** — nazwa zadania może mieć wartość **wszystkie**, **kopia zapasowa**, **klonowanie**, **przełączanie** do trybu failover, **pobieranie aktualizacji** lub **Instalowanie aktualizacji**.
  * **Stan** — zadania mogą być **wszystkie**, **w toku**, **zakończone powodzeniem** lub **niepowodzeniem** lub **anulowane**.
  * **Entity** — zadania mogą być skojarzone z woluminem, udziałem lub urządzeniem.
  * **Urządzenie** — nazwa urządzenia, na którym uruchomiono zadanie.
  * **Uruchomiono** — czas rozpoczęcia zadania.
  * **Czas trwania** — czas, w którym zadanie zostało uruchomione.
* **Stan** — umożliwia wyszukanie wszystkich zadań, uruchomionych, ukończonych lub zakończonych niepowodzeniem.
* **Typ zadania** — typ zadania to: wszystkie, kopia zapasowa, przywracanie, tryb failover, pobieranie aktualizacji lub instalowanie aktualizacji.

Lista zadań jest odświeżana co 30 sekund.

## <a name="view-job-details"></a>Wyświetlanie szczegółów zadania
Wykonaj poniższe kroki, aby wyświetlić szczegóły każdego zadania.

#### <a name="to-view-job-details"></a>Aby wyświetlić szczegóły zadania
1. W bloku **zadania** Wyświetl zadania, które Cię interesują, uruchamiając zapytanie z odpowiednimi filtrami. Można wyszukiwać ukończone lub uruchomione zadania.
2. Wybierz zadanie z tabelarycznej listy zadań.
   
    ![Blok zadania](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)
3. W dolnej części strony kliknij pozycję **szczegóły**.
4. W oknie dialogowym **szczegóły** można wyświetlić informacje o stanie, szczegółach i statystyce czasowej. Na poniższej ilustracji przedstawiono przykład okna dialogowego **szczegóły zadania tworzenia kopii zapasowej** .
   
    ![Szczegóły zadania](./media/storsimple-virtual-array-manage-jobs/ova-jobs-details.png)

#### <a name="job-failures-when-the-virtual-machine-is-paused-in-the-hypervisor"></a>Błędy zadań w przypadku wstrzymania maszyny wirtualnej w funkcji hypervisor
Gdy zadanie jest w toku w macierzy wirtualnej StorSimple, a urządzenie (maszyna wirtualna zainicjowana w funkcji hypervisor) zostało wstrzymane przez ponad 15 minut, zadanie zakończy się niepowodzeniem. Jest to spowodowane tym, że czas StorSimple tablicy wirtualnej nie jest zsynchronizowany z czasem Microsoft Azure. 

Zostanie wyświetlony następujący komunikat o błędzie: "czas urządzenia jest niezsynchronizowany z czasem Microsoft Azure przez ponad 15 minut. Upewnij się, że funkcja hypervisor i czas urządzenia są zsynchronizowane z serwerem NTP. Sprawdź, czy nie występują problemy z łącznością. Aby rozwiązać problemy z łącznością, Uruchom testy diagnostyczne z poziomu lokalnego interfejsu użytkownika sieci Web urządzenia wirtualnego.

Te błędy dotyczą zadań tworzenia kopii zapasowych, przywracania, aktualizowania i przełączania do trybu failover. Jeśli maszyna wirtualna została zainicjowana w funkcji Hyper-V, komputer ostatecznie synchronizuje czas z funkcją hypervisor. W takim przypadku można uruchomić ponownie zadanie.

## <a name="next-steps"></a>Następne kroki
[Dowiedz się, jak zarządzać wirtualną tablicą StorSimple za pomocą lokalnego interfejsu użytkownika sieci Web](storsimple-ova-web-ui-admin.md).

