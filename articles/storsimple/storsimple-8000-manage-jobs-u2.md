---
title: Wyświetlanie zadań i zarządzanie nimi dla serii StorSimple 8000 | Microsoft Docs
description: Opisuje blok zadania usługi StorSimple Menedżer urządzeń i sposobu jego użycia do śledzenia ostatnich, bieżących i zaplanowanych zadań tworzenia kopii zapasowej.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: dc630021cbf93074119d4feedcb3a09fae1453e3
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2020
ms.locfileid: "91949432"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-jobs-update-3-and-later"></a>Korzystanie z usługi StorSimple Menedżer urządzeń do wyświetlania zadań i zarządzania nimi (Aktualizacja Update 3 i nowsze)

## <a name="overview"></a>Omówienie
Blok **zadania** zawiera pojedynczy centralny portal umożliwiający wyświetlanie zadań uruchomionych na urządzeniach połączonych z usługą StorSimple Menedżer urządzeń i zarządzanie nimi. Można wyświetlać zadania zaplanowane, uruchomione, ukończone, anulowane i zakończone niepowodzeniem dla wielu urządzeń. Wyniki są prezentowane w formacie tabelarycznym.

![Blok zadań](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

Możesz szybko znaleźć interesujące Cię zadania, filtrując pola, takie jak:

* **Stan** — zadania mogą być w toku, zakończone powodzeniem, anulowane, zakończone niepowodzeniem, anulowanie lub zakończone powodzeniem z błędami.
* **Zakres czasu** — zadania można filtrować na podstawie daty i zakresu czasu. Zakresem są ostatnie 1 godziny, ostatnie 24 godziny, ostatnie 7 dni, ostatnie 30 dni, ubiegły rok lub Data niestandardowa.
* **Typ** — typ zadania może być zaplanowana kopia zapasowa, ręczna kopia zapasowa, przywracanie kopii zapasowej, klonowanie woluminu, przełączanie do trybu failover, Tworzenie woluminu przypiętego lokalnie, modyfikowanie woluminu, instalowanie aktualizacji, zbieranie dzienników pomocy technicznej i tworzenie urządzenia w chmurze.
* **Urządzenia** — zadania są inicjowane na określonym urządzeniu podłączonym do usługi.
  
Odfiltrowane zadania są następnie wyświetlane na podstawie następujących atrybutów:
  
* **Nazwa** — zaplanowana kopia zapasowa, ręczna kopia zapasowa, przywracanie kopii zapasowej, klonowanie woluminu, przełączanie w tryb failover kontenery woluminów, Tworzenie woluminu przypiętego lokalnie, modyfikowanie woluminu, instalowanie aktualizacji, zbieranie dzienników pomocy technicznej lub tworzenie urządzenia w chmurze.
* **Stan** — uruchomione, ukończone, anulowane, zakończone niepowodzeniem, anulowanie lub ukończono z błędami.
* **Entity** — zadania można kojarzyć z woluminem, zasadami tworzenia kopii zapasowych lub urządzeniem. Na przykład zadanie klonowania jest skojarzone z woluminem, podczas gdy zaplanowane zadanie tworzenia kopii zapasowej jest skojarzone z zasadami tworzenia kopii zapasowych. Zadanie urządzenia jest tworzone w wyniku odzyskiwania po awarii (DR) lub operacji przywracania.
* **Urządzenie** — nazwa urządzenia, na którym uruchomiono zadanie.
* **Uruchomiono** — czas rozpoczęcia zadania.
* **Czas trwania** — czas wymagany do ukończenia zadania.

Lista zadań jest odświeżana co 30 sekund.

Na tej stronie można wykonać następujące akcje związane z zadaniami:

* Wyświetlanie szczegółów zadania
* Anulowanie zadania

## <a name="view-job-details"></a>Wyświetlanie szczegółów zadania
Wykonaj poniższe kroki, aby wyświetlić szczegóły każdego zadania.

#### <a name="to-view-job-details"></a>Aby wyświetlić szczegóły zadania
1. Przejdź do usługi StorSimple Menedżer urządzeń, a następnie kliknij pozycję **Jobs (zadania**).

2. W bloku **zadania** Wyświetl zadania, które Cię interesują, uruchamiając zapytanie z odpowiednimi filtrami. Można wyszukiwać ukończone, uruchomione lub anulowane zadania.

    ![Blok zadania 2](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

2. Wybierz i kliknij zadanie.

    ![Blok zadania 3](./media/storsimple-8000-manage-jobs-u2/jobs3.png)

3. W bloku szczegóły zadania można wyświetlić stan, szczegóły, statystyki czasu i statystyki danych.
   
    ![Szczegóły zadania](./media/storsimple-8000-manage-jobs-u2/jobs4.png)

## <a name="cancel-a-job"></a>Anulowanie zadania
Wykonaj następujące kroki, aby anulować uruchomione zadanie.

> [!NOTE]
> Niektóre zadania, takie jak modyfikowanie woluminu w celu zmiany typu woluminu lub Rozszerzanie woluminu, nie mogą być anulowane.


### <a name="to-cancel-a-job"></a>Aby anulować zadanie
1. Na stronie **zadania** Wyświetl uruchomione zadania, które chcesz anulować, uruchamiając zapytanie z odpowiednimi filtrami. Wybierz zadanie.

2. Kliknij prawym przyciskiem myszy wybrane zadanie, aby wywołać menu kontekstowe, a następnie kliknij przycisk **Anuluj**.

    ![Szczegóły zadania 2](./media/storsimple-8000-manage-jobs-u2/jobs2.png)

3. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak**. To zadanie zostało anulowane.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [zarządzać zasadami kopii zapasowych StorSimple](storsimple-8000-manage-backup-policies-u2.md).
* Dowiedz się [, jak zarządzać urządzeniem StorSimple przy użyciu usługi StorSimple Menedżer urządzeń](storsimple-8000-manager-service-administration.md).

