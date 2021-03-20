---
title: StorSimple Snapshot Manager administrowanie | Microsoft Docs
description: Zawiera omówienie i linki do dodatkowych informacji na temat zadań administracyjnych i przepływów pracy StorSimple Snapshot Manager.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 1cdbb61d-bd16-4be4-ade2-ceab11508acb
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2016
ms.author: alkohli
ms.openlocfilehash: 16d782c6a25d8d2d8e1323b7cb683f41d8c99649
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90054980"
---
# <a name="use-storsimple-snapshot-manager-to-administer-your-storsimple-solution"></a>Korzystanie z StorSimple Snapshot Manager do administrowania rozwiązaniem StorSimple

## <a name="overview"></a>Omówienie
StorSimple Snapshot Manager jest przystawką programu Microsoft Management Console (MMC), która upraszcza ochronę danych i zarządzanie kopiami zapasowymi w środowisku Microsoft Azure StorSimple. Dzięki StorSimple Snapshot Manager możesz zarządzać danymi Microsoft Azure StorSimple w centrum danych i w chmurze jako jednym zintegrowanym rozwiązaniu magazynu, co upraszcza proces tworzenia kopii zapasowych i obniżanie kosztów.

Centralna konsola zarządzania StorSimple Snapshot Manager umożliwia tworzenie spójnych kopii zapasowych w danym momencie w danym momencie. Można na przykład użyć konsoli programu, aby:

* Konfigurowanie, tworzenie kopii zapasowych i usuwanie woluminów.
* Skonfiguruj grupy woluminów, aby upewnić się, że kopia zapasowa danych jest spójna z aplikacją.
* Zarządzanie zasadami tworzenia kopii zapasowych, aby utworzyć kopię zapasową danych zgodnie z ustalonym harmonogramem.
* Twórz niezależne kopie danych, które mogą być przechowywane w chmurze i używane do odzyskiwania po awarii.

Ten artykuł zawiera linki do samouczków opisujących StorSimple Snapshot Manager i sposobu ich używania do kończenia zadań administracyjnych i przepływów pracy związanych z administracją systemu.

* Aby uzyskać więcej informacji o składnikach i architekturze StorSimple Snapshot Manager, zobacz [co to jest StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md) 
* Aby pobrać StorSimple Snapshot Manager, przejdź do [strony pobierania Snapshot Manager StorSimple](https://www.microsoft.com/download/details.aspx?id=44220).
* Aby zapoznać się z procedurami wdrażania StorSimple Snapshot Manager, przejdź do pozycji [Wdróż StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).

> [!NOTE]
> Nie można użyć Snapshot Manager StorSimple do zarządzania Microsoft Azure StorSimplemi macierzami wirtualnymi (znanymi również jako StorSimple lokalne urządzenia wirtualne).


## <a name="storsimple-snapshot-manager-tasks-and-workflows"></a>StorSimple Snapshot Manager zadania i przepływy pracy
Snapshot Manager StorSimple służy do monitorowania bieżących, zaplanowanych i zakończonych zadań tworzenia kopii zapasowych oraz zarządzania nimi. Ponadto StorSimple Snapshot Manager udostępnia wykaz do 64 ukończonych kopii zapasowych. Wykazu można używać do znajdowania i przywracania woluminów lub pojedynczych plików. 

| JEŚLI CHCESZ TO ZROBIĆ... | SKORZYSTAJ Z TEGO SAMOUCZKA... |
|:--- |:--- |
| Dowiedz się więcej o StorSimple Snapshot Manager |[Co to jest StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md) |
| Zainstaluj StorSimple Snapshot Manager<br>Zainstaluj ponownie program StorSimple Snapshot Manager<br>Usuń StorSimple Snapshot Manager |[Wdróż StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md) |
| Użyj StorSimple Snapshot Manager menu i funkcje:<ul><li>Pasek menu</li><li>Pasek narzędzi</li><li>Okienko zakresu</li><li>Okienko wyników</li><li>Okienko akcje</li><li>Nawigacja i skróty klawiaturowe</li></ul> |[Interfejs użytkownika Snapshot Manager StorSimple](storsimple-use-snapshot-manager.md) |
| Użyj wspólnych funkcji programu MMC zawartych w StorSimple Snapshot Manager:<ul><li>Widok</li><li>Nowe okno w tym miejscu</li><li>Odśwież</li><li>Eksportuj listę</li><li>Pomoc</li></ul> |[Użyj akcji menu programu MMC w StorSimple Snapshot Manager](storsimple-snapshot-manager-mmc-menu.md) |
| Dodawanie lub zastępowanie urządzenia<br>Łączenie urządzenia<br>Sprawdź zaimportowane grupy woluminów<br>Odśwież podłączone urządzenia<br>Uwierzytelnianie urządzenia<br>Wyświetlanie szczegółów urządzenia<br>Usuń konfigurację urządzenia<br>Zmiana hasła urządzenia<br>Zastępowanie urządzenia zakończonego niepowodzeniem<br> |[Używanie Snapshot Manager StorSimple do nawiązywania połączeń z urządzeniami StorSimple i zarządzania nimi](storsimple-snapshot-manager-manage-devices.md) |
| Zainstaluj woluminy<br>Wyświetl informacje o woluminach<br>Usuwanie woluminu<br>Skanuj ponownie woluminy<br>Konfigurowanie i tworzenie kopii zapasowej woluminu podstawowego<br>Konfigurowanie i tworzenie kopii zapasowej dynamicznego woluminu dublowanego |[Używanie Snapshot Manager StorSimple do wyświetlania woluminów i zarządzania nimi](storsimple-snapshot-manager-manage-volumes.md) |
| Wyświetl grupy woluminów<br>Utwórz grupę woluminów<br>Tworzenie kopii zapasowej grupy woluminów<br>Edytowanie grupy woluminów<br>Usuń grupę woluminów |[Tworzenie grup woluminów i zarządzanie nimi za pomocą StorSimple Snapshot Manager](storsimple-snapshot-manager-manage-volume-groups.md) |
| Tworzenie zasad kopii zapasowych <br>Edytowanie zasad kopii zapasowych<br>Usuwanie zasad kopii zapasowych |[Tworzenie zasad tworzenia kopii zapasowych i zarządzanie nimi za pomocą StorSimple Snapshot Manager](storsimple-snapshot-manager-manage-backup-policies.md) |
| Wyświetlanie zaplanowanych zadań tworzenia kopii zapasowych i zarządzanie nimi<br>Wyświetl ostatnie zadania tworzenia kopii zapasowych i zarządzaj nimi<br>Wyświetlaj aktualnie uruchomione zadania tworzenia kopii zapasowych i zarządzaj nimi |[Używanie Snapshot Manager StorSimple do wyświetlania zadań tworzenia kopii zapasowych i zarządzania nimi](storsimple-snapshot-manager-manage-backup-jobs.md) |
| Przywróć wolumin<br>Klonowanie woluminu lub grupy woluminów<br>Usuń kopię zapasową<br>Odzyskiwanie pliku<br>Przywracanie bazy danych StorSimple Snapshot Manager |[Zarządzanie wykazem kopii zapasowych przy użyciu Snapshot Manager StorSimple](storsimple-snapshot-manager-manage-backup-catalog.md) |

## <a name="next-steps"></a>Następne kroki
[Pobierz Snapshot Manager StorSimple](https://www.microsoft.com/download/details.aspx?id=44220).

