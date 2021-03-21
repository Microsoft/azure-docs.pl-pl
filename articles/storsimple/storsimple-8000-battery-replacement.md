---
title: Zastąp baterię na urządzeniu z serii Microsoft Azure StorSimple 8000
description: Opisuje sposób usuwania, zastępowania i konserwowania modułu baterii tworzenia kopii zapasowych na urządzeniu StorSimple.
author: alkohli
ms.service: storsimple
ms.topic: troubleshooting
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 17a6a07d117557cdee23634bd211a14cf90d49e3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96023775"
---
# <a name="replace-the-backup-battery-module-on-your-storsimple-device"></a>Wymiana modułu baterii zapasowej w urządzeniu StorSimple

## <a name="overview"></a>Omówienie
Podstawowy i moduł chłodzenia obudowy (PCM) na urządzeniu Microsoft Azure StorSimple ma dodatkowy pakiet baterii. Ten pakiet zapewnia moc, dzięki czemu urządzenie StorSimple może zapisywać dane w przypadku utraty zasilania podstawowego obudowy. Ten pakiet baterii jest określany jako *moduł baterii tworzenia kopii zapasowych*. Moduł baterii tworzenia kopii zapasowej istnieje tylko dla obudowy podstawowej na urządzeniu StorSimple (obudowa EBOD nie zawiera modułu baterii tworzenia kopii zapasowej).

W tym samouczku wyjaśniono:

* Usuń moduł baterii tworzenia kopii zapasowej
* Zainstaluj nowy moduł baterii tworzenia kopii zapasowej
* Obsługa modułu baterii tworzenia kopii zapasowych

> [!IMPORTANT]
> Przed usunięciem i zastępowaniem modułu baterii tworzenia kopii zapasowych zapoznaj się z informacjami o zabezpieczeniach w temacie [wprowadzenie do StorSimpleego składnika sprzętowego](storsimple-8000-hardware-component-replacement.md).


## <a name="remove-the-backup-battery-module"></a>Usuń moduł baterii tworzenia kopii zapasowej
Moduł baterii kopii zapasowej dla urządzenia StorSimple jest jednostką, którą należy umieścić w polu. Przed zainstalowaniem w module PCM moduł baterii powinien być przechowywany w oryginalnym pakiecie. Wykonaj następujące kroki, aby usunąć baterię tworzenia kopii zapasowych.

#### <a name="to-remove-the-backup-battery-module"></a>Aby usunąć moduł baterii tworzenia kopii zapasowej
1. W Azure Portal przejdź do bloku usługi StorSimple Device Manager. Przejdź do pozycji **urządzenia** , a następnie wybierz urządzenie z listy urządzeń. Przejdź do **monitorowania**  >  **kondycji sprzętu**. W obszarze **udostępnione składniki** Sprawdź stan baterii.
2. Zidentyfikuj moduł PCM, w którym bateria nie powiodła się. Rysunek 1 przedstawia tył urządzenia StorSimple.
   
    ![Planowanie planu podstawowego modułów obudowy urządzenia](./media/storsimple-battery-replacement/IC740994.png)
   
    **Rysunek 1** Tył urządzenia podstawowego, w którym są wyświetlane moduły PCM i modułów kontrolera
   
   | Etykieta | Opis |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Kontroler 0 |
   | 4 |Kontroler 1 |
   
    Jak pokazano na rysunku 2, wskaźnik monitorowania LED w module PCM 0, który odnosi się do **błędu baterii** , powinien być widoczny.
   
    ![Przeplanowanie diod LED wskaźnika monitorowania urządzenia PCM](./media/storsimple-battery-replacement/IC740992.png)
   
    **Rysunek 2** Z tyłu PCM pokazująca diody LED wskaźnika monitorowania
   
   | Etykieta | Opis |
   |:--- |:--- |
   | 1 |Awaria zasilania AC |
   | 2 |Awaria wentylatora |
   | 3 |Awaria baterii |
   | 4 |MODUŁ PCM JEST PRAWIDŁOWY |
   | 5 |Awaria zasilania kontrolera domeny |
   | 6 |Kondycja baterii |
3. Aby usunąć moduł PCM z niepowodzeniem baterią, wykonaj kroki opisane w sekcji [usuwanie modułu PCM](storsimple-8000-power-cooling-module-replacement.md#remove-a-pcm).
4. Po usunięciu modułu PCM, Unieś i obróć moduł baterii dojście do góry, jak wskazano na poniższej ilustracji, i wyjmij go, aby usunąć baterię.
   
    ![Usuwanie baterii z PCM](./media/storsimple-battery-replacement/IC741019.png)
   
    **Rysunek 3** Usuwanie baterii z modułu PCM
5. Umieść moduł w opakowaniu jednostkowym do przemieszczenia pól.
6. Zwróć wadliwą jednostkę do firmy Microsoft w celu zapewnienia prawidłowej obsługi i obsługi.

## <a name="install-a-new-backup-battery-module"></a>Zainstaluj nowy moduł baterii tworzenia kopii zapasowej
Wykonaj następujące kroki, aby zainstalować wymienny moduł baterii w module PCM w podstawowej obudowie urządzenia StorSimple.

#### <a name="to-install-the-battery-module"></a>Aby zainstalować moduł baterii
1. Umieść moduł baterii kopii zapasowej w odpowiedniej orientacji w module PCM.
2. Naciśnij klawisz w dół w celu dołączenia modułu baterii do łącznika.
3. Zastąp moduł PCM w obudowie podstawowej, postępując zgodnie ze wskazówkami w temacie [zastępowanie modułu zasilacza i chłodzenia na urządzeniu StorSimple](storsimple-8000-power-cooling-module-replacement.md).
4. Po zakończeniu wymiany przejdź do urządzenia, a następnie przejdź do pozycji **Monitoruj**  >  **kondycję sprzętu** w Azure Portal. Sprawdź stan baterii, aby upewnić się, że instalacja zakończyła się pomyślnie. Zielony stan oznacza, że bateria jest w dobrej kondycji.

## <a name="maintain-the-backup-battery-module"></a>Obsługa modułu baterii tworzenia kopii zapasowych
Na urządzeniu z systemem StorSimple moduł baterii tworzenia kopii zapasowych zapewnia zasilanie kontrolera podczas zdarzenia utraty zasilania. Dzięki temu urządzenie StorSimple może zapisywać krytyczne dane przed zamykaniem w kontrolowany sposób. W przypadku dwóch w pełni obciążonych baterii w PCMs system może obsłużyć dwa kolejne zdarzenia utraty.

W Azure Portal **kondycja sprzętu** w bloku **monitor** wskazuje, czy bateria jest uszkodzona, czy zbliża się koniec cyklu życia. Stan baterii jest wskazywany przez **baterię w module PCM 0** lub **baterii w module PCM 1** w obszarze **składniki udostępnione**. W tym bloku zostanie wyświetlony stan **obniżonej wydajności** i **nie powiodło się** osiągnięcie końca okresu użytkowania.

> [!NOTE]
> **Nie** można zgłosić baterii w przypadku, gdy po prostu musi zostać naliczona opłata.


Jeśli zostanie wyświetlony stan **obniżonej sprawności** , zalecamy wykonanie następujących czynności:

* System mógł napotkać ostatnią utratę zasilania lub baterie mogą być okresowo konserwacyjne. Przed kontynuowaniem Obserwuj system przez 12 godzin.
  
  * Jeśli stan jest nadal **obniżony** po 12 godzinach ciągłego połączenia z zasilaniem AC przy użyciu kontrolerów i PCMs, należy wymienić baterię. [Skontaktuj się pomoc techniczna firmy Microsoft](storsimple-8000-contact-microsoft-support.md) , aby uzyskać zastępujący moduł baterii tworzenia kopii zapasowych.
  * Jeśli stan zmieni się na OK po upływie 12 godzin, bateria działa i wymaga opłaty konserwacyjnej.
* Jeśli nie masz skojarzonej utraty zasilania, a moduł PCM jest włączony i podłączony do zasilania AC, należy wymienić baterię. [Skontaktuj się pomoc techniczna firmy Microsoft](storsimple-8000-contact-microsoft-support.md) , aby zamówić moduł baterii wymiany kopii zapasowych.

> [!IMPORTANT]
> Usuwanie baterii zakończonej niepowodzeniem zgodnie z przepisami krajowymi i regionalnymi.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [wymianie składników sprzętowych StorSimple](storsimple-8000-hardware-component-replacement.md).

