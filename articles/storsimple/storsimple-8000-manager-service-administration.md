---
title: Administracja usługą StorSimple Device Manager | Microsoft Docs
description: Dowiedz się, jak zarządzać urządzeniem StorSimple za pomocą usługi StorSimple Device Manager w Azure Portal.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/17/2021
ms.author: alkohli
ms.openlocfilehash: 0e25d41fbf6a9683f178cbfd1b07fde1b6796509
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104656825"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-device"></a>Korzystanie z usługi StorSimple Device Manager w celu administrowania urządzeniem StorSimple

## <a name="overview"></a>Omówienie

W tym artykule opisano interfejs usługi StorSimple Device Manager, w tym sposób nawiązywania z nim połączenia, różne dostępne opcje i linki do określonych przepływów pracy, które można wykonać za pomocą tego interfejsu użytkownika. Te wskazówki dotyczą obu tych programów; urządzenie fizyczne StorSimple i urządzenie w chmurze.

Po przeczytaniu tego artykułu dowiesz się, jak:

* Nawiązywanie połączenia z usługą StorSimple Device Manager
* Administruj urządzeniem StorSimple za pośrednictwem usługi StorSimple Device Manager

## <a name="connect-to-storsimple-device-manager-service"></a>Nawiązywanie połączenia z usługą StorSimple Device Manager

Usługa StorSimple Device Manager działa w Microsoft Azure i łączy się z wieloma urządzeniami StorSimple. Aby zarządzać tymi urządzeniami, należy użyć centralnej Microsoft Azure Portal działającej w przeglądarce. Aby nawiązać połączenie z usługą StorSimple Device Manager, wykonaj następujące czynności.

#### <a name="to-connect-to-the-service"></a>Aby nawiązać połączenie z usługą
1. Przejdź do [https://portal.azure.com/](https://portal.azure.com/) .
2. Korzystając z poświadczeń konto Microsoft, zaloguj się do Microsoft Azure Portal (znajdującego się w prawym górnym rogu okienka).
3. Przewiń w dół do lewego okienka nawigacji, aby uzyskać dostęp do usługi StorSimple Device Manager.


## <a name="administer-storsimple-device-using-storsimple-device-manager-service"></a>Administrowanie urządzeniem StorSimple za pomocą usługi StorSimple Device Manager

W poniższej tabeli przedstawiono podsumowanie wszystkich typowych zadań zarządzania i złożonych przepływów pracy, które można wykonać w interfejsie użytkownika usługi StorSimple Device Manager. Te zadania są zorganizowane na podstawie bloków interfejsu użytkownika, na których są inicjowane.

Aby uzyskać więcej informacji na temat każdego przepływu pracy, kliknij odpowiednią procedurę w tabeli.

[!INCLUDE [StorSimple software warning](../../includes/storsimple-update-software-warning.md)]

#### <a name="storsimple-device-manager-workflows"></a>StorSimple przepływy pracy Device Manager

| Jeśli chcesz to zrobić... | Użyj tej procedury. |
| --- | --- |
| Tworzenie usługi</br>Usuwanie usługi</br>Pobierz klucz rejestracji usługi</br>Wygeneruj ponownie klucz rejestracji usługi |[Wdrażanie usługi StorSimple Device Manager](storsimple-8000-manage-service.md) |
| Wyświetlanie dzienników aktywności |[Użyj podsumowania usługi StorSimple Device Manager](storsimple-8000-service-dashboard.md) |
| Zmień klucz szyfrowania danych usługi</br>Wyświetlanie dzienników operacji |[Korzystanie z pulpitu nawigacyjnego usługi StorSimple Device Manager](storsimple-8000-service-dashboard.md) |
| Dezaktywowanie urządzenia</br>Usuwanie urządzenia |[Dezaktywowanie lub usuwanie urządzenia](storsimple-8000-deactivate-and-delete-device.md) |
| Informacje o odzyskiwaniu po awarii i przejściu do trybu failover</br>Przełączanie do trybu failover na urządzeniu fizycznym</br>Przełączenie w tryb failover do urządzenia wirtualnego</br>Ciągłość działania — odzyskiwanie po awarii (BCDR) |[Tryb failover i odzyskiwanie po awarii dla urządzenia StorSimple](storsimple-8000-device-failover-disaster-recovery.md) |
| Utwórz listę kopii zapasowych dla woluminu</br>Wybierz zestaw kopii zapasowych</br>Usuwanie zestawu kopii zapasowych |[Zarządzanie kopiami zapasowymi](storsimple-8000-manage-backup-catalog.md) |
| Klonowanie woluminu |[Klonowanie woluminu](storsimple-8000-clone-volume-u2.md) |
| Przywracanie zestawu kopii zapasowych |[Przywracanie zestawu kopii zapasowych](storsimple-8000-restore-from-backup-set-u2.md) |
| Informacje o kontach magazynu</br>Dodawanie konta magazynu</br>Edytowanie konta magazynu</br>Usuwanie konta magazynu</br>Kluczowe rotacja kont magazynu |[Zarządzanie kontami magazynu](storsimple-8000-manage-storage-accounts.md) |
| Szablony przepustowości — informacje</br>Dodawanie szablonu przepustowości</br>Edytowanie szablonu przepustowości</br>Usuwanie szablonu przepustowości</br>Użyj domyślnego szablonu przepustowości</br>Utwórz szablon przepustowości całodziennej, który jest uruchamiany w określonym czasie |[Zarządzanie szablonami przepustowości](storsimple-8000-manage-bandwidth-templates.md) |
| Informacje o rekordach kontroli dostępu</br>Tworzenie rekordu kontroli dostępu</br>Edytowanie rekordu kontroli dostępu</br>Usuwanie rekordu kontroli dostępu |[Zarządzanie rekordami kontroli dostępu](storsimple-8000-manage-acrs.md) |
| Wyświetlanie szczegółów zadania</br>Anulowanie zadania |[Zarządzanie zadaniami](storsimple-8000-manage-jobs-u2.md) |
| Otrzymywanie powiadomień o alertach</br>Zarządzanie alertami</br>Przegląd alertów |[Wyświetlanie alertów StorSimple i zarządzanie nimi](storsimple-8000-manage-alerts.md) |
| Tworzenie wykresów monitorowania |[Monitorowanie urządzenia StorSimple](./storsimple-8000-monitor-device.md) |
| Dodawanie kontenera woluminów</br>Modyfikowanie kontenera woluminów</br>Usuwanie kontenera woluminów |[Zarządzanie kontenerami woluminów](storsimple-8000-manage-volume-containers.md) |
| Dodaj wolumin</br>Modyfikowanie woluminu</br>Przełącz wolumin w tryb offline</br>Usuwanie woluminu</br>Monitorowanie woluminu |[Zarządzanie woluminami](storsimple-8000-manage-volumes-u2.md) |
| Modyfikowanie ustawień urządzenia</br>Modyfikowanie ustawień czasu</br>Modyfikowanie ustawień DNS.md</br>Konfigurowanie interfejsów sieciowych |[Modyfikowanie konfiguracji urządzenia dla urządzenia StorSimple](storsimple-8000-modify-device-config.md) |
| Wyświetl ustawienia serwera proxy sieci Web |[Konfigurowanie serwera proxy sieci Web dla urządzenia](storsimple-8000-configure-web-proxy.md) |
| Modyfikowanie hasła administratora urządzenia</br>Modyfikowanie hasła Snapshot Manager StorSimple |[Zmień StorSimple hasła](storsimple-8000-change-passwords.md) |
| Konfigurowanie zdalnego zarządzania |[Zdalne nawiązywanie połączenia z urządzeniem StorSimple](storsimple-8000-remote-connect.md) |
| Konfigurowanie ustawień alertów |[Wyświetlanie alertów StorSimple i zarządzanie nimi](storsimple-8000-manage-alerts.md) |
| Konfigurowanie protokołu CHAP dla urządzenia StorSimple |[Konfigurowanie protokołu CHAP dla urządzenia StorSimple](./storsimple-8000-configure-chap.md) |
| Dodawanie zasad kopii zapasowych</br>Dodawanie lub modyfikowanie harmonogramu</br>Usuwanie zasad kopii zapasowych</br>Ręczne tworzenie kopii zapasowej</br>Tworzenie niestandardowych zasad tworzenia kopii zapasowych z wieloma woluminami i harmonogramami |[Zarządzanie zasadami tworzenia kopii zapasowych](storsimple-8000-manage-backup-policies-u2.md) |
| Zatrzymaj kontrolery urządzeń</br>Ponowne uruchamianie kontrolerów urządzeń</br>Zamykanie kontrolerów urządzeń</br>Resetowanie urządzenia do domyślnych ustawień fabrycznych</br>(Powyżej są przeznaczone tylko dla urządzeń lokalnych) |[Zarządzanie kontrolerem urządzeń StorSimple](storsimple-8000-manage-device-controller.md) |
| Informacje o składnikach sprzętowych StorSimple</br>Monitorowanie stanu sprzętu</br>(Powyżej są przeznaczone tylko dla urządzeń lokalnych) |[Monitoruj składniki sprzętowe](storsimple-8000-monitor-hardware-status.md) |
| Tworzenie pakietu dla pomocy technicznej |[Tworzenie pakietu dla pomocy technicznej i zarządzanie nim](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple) |
| Zainstaluj aktualizacje oprogramowania |[Aktualizowanie urządzenia](storsimple-update-device.md) |

## <a name="next-steps"></a>Następne kroki

Jeśli występują jakiekolwiek problemy z codziennym działaniem urządzenia StorSimple lub z dowolnym jego składnikiem sprzętowym, zapoznaj się z tematem:

* [Rozwiązywanie problemów przy użyciu narzędzia diagnostycznego](storsimple-8000-diagnostics.md)
* [Korzystanie z diod LED wskaźnika monitorowania StorSimple](storsimple-monitoring-indicators.md)

Jeśli nie możesz rozwiązać problemów i musisz utworzyć żądanie obsługi, zapoznaj się z tematem [kontakt pomoc techniczna firmy Microsoft](storsimple-8000-contact-microsoft-support.md).