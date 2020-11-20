---
title: Administracja macierzy wirtualnej programu Microsoft Azure StorSimple Manager | Microsoft Docs
description: Dowiedz się, jak zarządzać lokalną tablicą wirtualną StorSimple przy użyciu usługi StorSimple Menedżer urządzeń w Azure Portal.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 958244a5-f9f5-455e-b7ef-71a65558872e
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/1/2016
ms.author: alkohli
ms.openlocfilehash: 2395e3c6f2026bc1024de38122bc20959fdcf9b7
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967095"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-virtual-array"></a>Korzystanie z usługi StorSimple Menedżer urządzeń w celu administrowania wirtualną macierzą StorSimple
![przepływ procesu instalacji](./media/storsimple-virtual-array-manager-service-administration/manage4.png)

## <a name="overview"></a>Omówienie
W tym artykule opisano interfejs usługi StorSimple Menedżer urządzeń, w tym sposób nawiązywania z nim połączenia i różne dostępne opcje oraz podano linki do określonych przepływów pracy, które można wykonać za pośrednictwem tego interfejsu użytkownika.

Po przeczytaniu tego artykułu dowiesz się, jak:

* Nawiązywanie połączenia z usługą StorSimple Menedżer urządzeń
* Nawigowanie po INTERFEJSie StorSimple Menedżer urządzeń
* Administruj wirtualną macierzą StorSimple za pośrednictwem usługi StorSimple Menedżer urządzeń

> [!NOTE]
> Aby wyświetlić opcje zarządzania dostępne dla urządzenia z serii StorSimple 8000, przejdź do [korzystania z usługi StorSimple Manager w celu administrowania urządzeniem StorSimple](./storsimple-8000-manager-service-administration.md).
> 
> 

## <a name="connect-to-the-storsimple-device-manager-service"></a>Nawiązywanie połączenia z usługą StorSimple Menedżer urządzeń
Usługa StorSimple Menedżer urządzeń działa w Microsoft Azure i łączy się z wieloma wirtualnymi macierzami StorSimple. Aby zarządzać tymi urządzeniami, należy użyć centralnej Microsoft Azure Portal działającej w przeglądarce. Aby nawiązać połączenie z usługą StorSimple Menedżer urządzeń, wykonaj następujące czynności.

#### <a name="to-connect-to-the-service"></a>Aby nawiązać połączenie z usługą
1. Przejdź do witryny [https://ms.portal.azure.com](https://ms.portal.azure.com).
2. Korzystając z poświadczeń konto Microsoft, zaloguj się do Microsoft Azure Portal (znajdującego się w prawym górnym rogu okienka).
3. Przejdź do przycisku Przeglądaj--> "filter" w menedżerach urządzeń StorSimple, aby wyświetlić wszystkich menedżerów urządzeń w danej subskrypcji.

## <a name="use-the-storsimple-device-manager-service-to-perform-management-tasks"></a>Korzystanie z usługi StorSimple Menedżer urządzeń do wykonywania zadań zarządzania
W poniższej tabeli przedstawiono podsumowanie wszystkich typowych zadań zarządzania i złożonych przepływów pracy, które można wykonać w bloku podsumowania usługi StorSimple Menedżer urządzeń. Te zadania są zorganizowane na podstawie bloków, na których są inicjowane.

Aby uzyskać więcej informacji na temat każdego przepływu pracy, kliknij odpowiednią procedurę w tabeli.

#### <a name="storsimple-device-manager-workflows"></a>StorSimple przepływy pracy Menedżer urządzeń
| Jeśli chcesz to zrobić... | Użyj tej procedury |
| --- | --- |
| Tworzenie usługi</br>Usuwanie usługi</br>Pobieranie klucza rejestracji usługi</br>Wygeneruj ponownie klucz rejestracji usługi |[Wdrażanie usługi StorSimple Menedżer urządzeń](storsimple-virtual-array-manage-service.md) |
| Wyświetlanie dzienników aktywności |[Korzystanie z podsumowania usługi StorSimple](storsimple-virtual-array-service-summary.md) |
| Dezaktywuj tablicę wirtualną</br>Usuwanie macierzy wirtualnej |[Dezaktywowanie lub usuwanie macierzy wirtualnej](storsimple-virtual-array-deactivate-and-delete-device.md) |
| Odzyskiwanie po awarii i tryb failover urządzenia</br>Wymagania wstępne trybu failover</br>Ciągłość działania — odzyskiwanie po awarii (BCDR)</br>Błędy podczas odzyskiwania po awarii |[Odzyskiwanie po awarii i tryb failover urządzenia dla macierzy wirtualnej StorSimple](storsimple-virtual-array-failover-dr.md) |
| Tworzenie kopii zapasowych udziałów i woluminów</br>Ręczne tworzenie kopii zapasowej</br>Zmiana harmonogramu tworzenia kopii zapasowych</br>Wyświetl istniejące kopie zapasowe |[Tworzenie kopii zapasowej macierzy wirtualnej StorSimple](storsimple-virtual-array-backup.md) |
| Klonowanie udziałów z zestawu kopii zapasowych</br>Klonowanie woluminów z zestawu kopii zapasowych</br>Odzyskiwanie na poziomie elementu (tylko serwer plików) |[Klonowanie z kopii zapasowej macierzy wirtualnej StorSimple](storsimple-virtual-array-clone.md) |
| Informacje o kontach magazynu</br>Dodawanie konta magazynu</br>Edytowanie konta magazynu</br>Usuwanie konta magazynu |[Zarządzanie kontami magazynu dla macierzy wirtualnej StorSimple](storsimple-virtual-array-manage-storage-accounts.md) |
| Informacje o rekordach kontroli dostępu</br>Dodawanie lub modyfikowanie rekordu kontroli dostępu </br>Usuwanie rekordu kontroli dostępu |[Zarządzanie rekordami kontroli dostępu dla macierzy wirtualnej StorSimple](storsimple-virtual-array-manage-acrs.md) |
| Wyświetlanie szczegółów zadania |[Zarządzanie zadaniami macierzy wirtualnej StorSimple](storsimple-virtual-array-manage-jobs.md) |
| Konfigurowanie ustawień alertów</br>Otrzymywanie powiadomień o alertach</br>Zarządzanie alertami</br>Przegląd alertów |[Wyświetl alerty dla macierzy wirtualnej StorSimple i zarządzaj nimi](storsimple-virtual-array-manage-alerts.md) |
| Modyfikowanie hasła administratora urządzenia |[Zmień hasło administratora urządzenia macierzy wirtualnej StorSimple](storsimple-virtual-array-change-device-admin-password.md) |
| Zainstaluj aktualizacje oprogramowania |[Aktualizowanie macierzy wirtualnej](storsimple-virtual-array-install-update.md) |

> [!NOTE]
> Należy użyć [lokalnego interfejsu użytkownika sieci Web](storsimple-ova-web-ui-admin.md) dla następujących zadań:
> 
> * [Pobierz klucz szyfrowania danych usługi](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
> * [Tworzenie pakietu dla pomocy technicznej](storsimple-ova-web-ui-admin.md#generate-a-log-package)
> * [Zatrzymywanie i ponowne uruchamianie macierzy wirtualnej](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)
> 
> 

## <a name="next-steps"></a>Następne kroki
Aby uzyskać informacje o interfejsie użytkownika sieci Web i sposobach ich użycia, przejdź do strony przy [użyciu interfejsu użytkownika sieci Web StorSimple do administrowania wirtualną tablicą programu StorSimple](storsimple-ova-web-ui-admin.md).