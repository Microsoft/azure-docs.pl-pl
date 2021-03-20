---
title: Zarządzanie zasadami tworzenia kopii zapasowych serii StorSimple 8000 | Microsoft Docs
description: Wyjaśnia, w jaki sposób można użyć usługi StorSimple Device Manager do tworzenia ręcznych kopii zapasowych, harmonogramów kopii zapasowych i przechowywania kopii zapasowych na urządzeniu z serii StorSimple 8000.
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
ms.date: 07/05/2017
ms.author: alkohli
ms.openlocfilehash: 9ec2bbef88ed185c3927676ad2596269783d1850
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85515632"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-manage-backup-policies"></a>Zarządzanie zasadami tworzenia kopii zapasowych za pomocą usługi StorSimple Device Manager w Azure Portal


## <a name="overview"></a>Omówienie

W tym samouczku wyjaśniono, jak używać bloku **zasad tworzenia kopii zapasowych** usługi StorSimple Device Manager do kontrolowania procesów tworzenia kopii zapasowych i przechowywania kopii zapasowych dla woluminów StorSimple. Opisano w nim również, jak wykonać ręczną kopię zapasową.

Podczas tworzenia kopii zapasowej woluminu można utworzyć lokalną migawkę lub migawkę w chmurze. W przypadku tworzenia kopii zapasowej woluminu przypiętego lokalnie zalecamy określenie migawki w chmurze. Wykonanie dużej liczby lokalnych migawek woluminu przypiętego lokalnie połączonego z zestawem danych, który ma dużo zmian, spowoduje powstanie sytuacji, w której można szybko wymusić miejsce na dysku lokalnym. Jeśli zdecydujesz się na korzystanie z migawek lokalnych, zalecamy przeprowadzenie mniejszej liczby codziennych migawek, aby utworzyć kopię zapasową najnowszego stanu, zachować je na dzień, a następnie usunąć je.

Po przełączeniu migawki w chmurze woluminu przypiętego lokalnie można skopiować tylko zmienione dane do chmury, w której są deduplikowane i skompresowane.

## <a name="the-backup-policy-blade"></a>Blok zasady tworzenia kopii zapasowych

Blok **zasady tworzenia kopii zapasowych** urządzenia StorSimple umożliwia zarządzanie zasadami tworzenia kopii zapasowych i planowanie migawek lokalnych i chmurowych. Zasady tworzenia kopii zapasowych są używane do konfigurowania harmonogramów kopii zapasowych i przechowywania kopii zapasowych dla kolekcji woluminów. Zasady tworzenia kopii zapasowych umożliwiają wykonywanie migawek wielu woluminów jednocześnie. Oznacza to, że kopie zapasowe utworzone za pomocą zasad tworzenia kopii zapasowych będą kopiami spójnymi na poziomie awarii.

Lista tabelaryczna zasady tworzenia kopii zapasowych umożliwia także filtrowanie istniejących zasad tworzenia kopii zapasowych przez jedno lub więcej z następujących pól:

* **Nazwa zasad** — Nazwa skojarzona z zasadami. Różne typy zasad obejmują:

  * Zaplanowane zasady, które zostały jawnie utworzone przez użytkownika.
  * Zaimportowane zasady, które zostały pierwotnie utworzone w Snapshot Manager StorSimple. Mają tag, który opisuje StorSimple Snapshot Manager hosta, z którego zostały zaimportowane zasady.

  > [!NOTE]
  > Automatyczne lub domyślne zasady tworzenia kopii zapasowych nie są już włączone w momencie tworzenia woluminu.

* **Ostatnia pomyślna kopia zapasowa** — Data i godzina ostatniej pomyślnej kopii zapasowej, która została wykonana z tymi zasadami.

* **Kolejna kopia zapasowa** — Data i godzina kolejnej zaplanowanej kopii zapasowej, która zostanie zainicjowana przez te zasady.

* **Woluminy** — woluminy skojarzone z zasadami. Podczas tworzenia kopii zapasowych wszystkie woluminy skojarzone z zasadami tworzenia kopii zapasowych są grupowane razem.

* **Harmonogramy** — liczba harmonogramów skojarzonych z zasadami tworzenia kopii zapasowych.

Często używane operacje, które można wykonywać w ramach zasad tworzenia kopii zapasowych, to:

* Dodawanie zasad kopii zapasowych
* Dodawanie lub modyfikowanie harmonogramu
* Dodawanie lub usuwanie woluminu
* Usuwanie zasad kopii zapasowych
* Ręczne tworzenie kopii zapasowej

## <a name="add-a-backup-policy"></a>Dodawanie zasad kopii zapasowych

Dodaj zasady tworzenia kopii zapasowych, aby automatycznie planować kopie zapasowe. Podczas pierwszego tworzenia woluminu nie ma żadnych domyślnych zasad kopii zapasowych skojarzonych z tym woluminem. Należy dodać zasady tworzenia kopii zapasowych i przypisać je do ochrony danych woluminu.

Wykonaj następujące kroki w Azure Portal, aby dodać zasady tworzenia kopii zapasowych dla urządzenia z systemem StorSimple. Po dodaniu zasad można zdefiniować harmonogram (patrz [Dodawanie lub modyfikowanie harmonogramu](#add-or-modify-a-schedule)).

[!INCLUDE [storsimple-8000-add-backup-policy-u2](../../includes/storsimple-8000-add-backup-policy-u2.md)]

## <a name="add-or-modify-a-schedule"></a>Dodawanie lub modyfikowanie harmonogramu

Możesz dodać lub zmodyfikować harmonogram, który jest dołączony do istniejących zasad tworzenia kopii zapasowych na urządzeniu StorSimple. Aby dodać lub zmodyfikować harmonogram, wykonaj następujące czynności w Azure Portal.

[!INCLUDE [storsimple-8000-add-modify-backup-schedule](../../includes/storsimple-8000-add-modify-backup-schedule-u2.md)]


## <a name="add-or-remove-a-volume"></a>Dodawanie lub usuwanie woluminu

Można dodać lub usunąć wolumin przypisany do zasad tworzenia kopii zapasowych na urządzeniu StorSimple. Aby dodać lub usunąć wolumin, wykonaj następujące czynności w Azure Portal.

[!INCLUDE [storsimple-8000-add-volume-backup-policy-u2](../../includes/storsimple-8000-add-remove-volume-backup-policy-u2.md)]


## <a name="delete-a-backup-policy"></a>Usuwanie zasad kopii zapasowych

Wykonaj następujące kroki w Azure Portal, aby usunąć zasady tworzenia kopii zapasowych na urządzeniu StorSimple.

[!INCLUDE [storsimple-8000-delete-backup-policy](../../includes/storsimple-8000-delete-backup-policy.md)]

## <a name="take-a-manual-backup"></a>Ręczne tworzenie kopii zapasowej

Wykonaj następujące kroki w Azure Portal, aby utworzyć kopię zapasową na żądanie (ręcznie) dla pojedynczego woluminu.

[!INCLUDE [storsimple-8000-create-manual-backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [korzystaniu z usługi StorSimple Device Manager w celu administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

