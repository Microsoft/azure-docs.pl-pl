---
title: Dezaktywowanie i usuwanie urządzenia z serii StorSimple 8000 | Microsoft Docs
description: Dowiedz się, jak dezaktywować i usuwać Urządzenie StorSimple połączone z usługą StorSimple Device Manager.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2018
ms.author: alkohli
ms.openlocfilehash: 07d108306fdca9bfe8f793b61660550e43151d71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017189"
---
# <a name="deactivate-and-delete-a-storsimple-device"></a>Dezaktywacja i usuwanie urządzenia StorSimple

## <a name="overview"></a>Omówienie

W tym artykule opisano sposób dezaktywowania i usuwania urządzenia StorSimple, które jest połączone z usługą StorSimple Device Manager. Wskazówki zawarte w tym artykule dotyczą tylko urządzeń z serii StorSimple 8000, włącznie z urządzeniami w chmurze StorSimple. Jeśli używasz macierzy wirtualnej StorSimple, przejdź do pozycji [Dezaktywuj i Usuń tablicę wirtualną StorSimple](storsimple-virtual-array-deactivate-and-delete-device.md).

Dezaktywacja łączy połączenie między urządzeniem a odpowiadającą Device Manager usługą StorSimple. Możesz zastanowić się, że urządzenie StorSimple jest poza usługą (na przykład w przypadku zastąpienia lub uaktualnienia urządzenia lub jeśli już nie używasz usługi StorSimple). Jeśli tak, należy dezaktywować urządzenie przed jego usunięciem.

W przypadku dezaktywowania urządzenia żadne dane przechowywane lokalnie na urządzeniu nie są już dostępne. Można odzyskać tylko dane skojarzone z urządzeniem, które było przechowywane w chmurze.

> [!WARNING]
> Dezaktywacja jest operacją TRWAŁą i nie można jej cofnąć. Dezaktywowanego urządzenia nie można zarejestrować w usłudze StorSimple Device Manager, chyba że zostanie zresetowana do domyślnych ustawień fabrycznych.
>
> Proces resetowania do ustawień fabrycznych usuwa wszystkie dane przechowywane lokalnie na urządzeniu. W związku z tym przed dezaktywacją urządzenia musisz wykonać migawkę w chmurze dla wszystkich danych. Ta migawka w chmurze umożliwia odzyskanie wszystkich danych na późniejszym etapie.

> [!NOTE]
>
> - Przed dezaktywacją urządzenia fizycznego StorSimple lub urządzeniem w chmurze upewnij się, że dane z usuniętego kontenera woluminów są w rzeczywistości usuwane z urządzenia. Możesz monitorować wykresy zużycia w chmurze i wyświetlić listę rozwijaną użycia w chmurze ze względu na usunięte kopie zapasowe, a następnie można rozpocząć dezaktywację urządzenia. Jeśli urządzenie zostanie dezaktywowane przed wystąpieniem tej porzucenia, dane są uwzględniane na koncie magazynu i naliczane są opłaty.
>
> - Przed dezaktywacją urządzenia fizycznego StorSimple lub urządzeniem w chmurze Zatrzymaj lub Usuń klientów i hosty, które są zależne od tego urządzenia.
>
> - Jeśli konta magazynu lub kontenery na koncie magazynu skojarzonym z kontenerami woluminów zostały już usunięte przed usunięciem danych z urządzenia, wystąpi błąd i może nie być możliwe usunięcie danych. Zalecamy usunięcie danych z urządzenia przed usunięciem z nich konta magazynu lub kontenerów. Jednak w takiej sytuacji trzeba będzie kontynuować dezaktywację i usunięcie urządzenia przy założeniu, że dane zostały już usunięte z konta magazynu.

Po przeczytaniu tego samouczka będziesz mieć możliwość:

- Dezaktywuj urządzenie i Usuń dane.
- Dezaktywuj urządzenie i Zachowaj dane.

## <a name="deactivate-and-delete-data"></a>Dezaktywuj i Usuń dane

Jeśli chcesz całkowicie usunąć urządzenie i nie chcesz zachować danych na urządzeniu, wykonaj następujące czynności.

### <a name="to-deactivate-the-device-and-delete-the-data"></a>Aby dezaktywować urządzenie i usunąć dane

1. Przed dezaktywacją urządzenia należy usunąć wszystkie kontenery woluminów (i woluminy) skojarzone z urządzeniem. Kontenery woluminów można usunąć dopiero po usunięciu skojarzonych kopii zapasowych. Zapoznaj się z uwagami opisanymi w powyższym omówieniu przed dezaktywacją urządzenia fizycznego StorSimple lub urządzeniem w chmurze.

2. Dezaktywuj urządzenie w następujący sposób:

   1. Przejdź do usługi Menedżer urządzeń StorSimple i kliknij pozycję **Urządzenia**. W bloku **urządzenia** wybierz urządzenie, które chcesz dezaktywować, kliknij prawym przyciskiem myszy, a następnie kliknij pozycję **Dezaktywuj**.

        ![Dezaktywuj Urządzenie StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. W bloku **Dezaktywuj** wpisz nazwę urządzenia, aby potwierdzić, a następnie kliknij przycisk **Dezaktywuj**. Proces dezaktywowania rozpocznie się i trwa kilka minut.

        ![Dezaktywuj Urządzenie StorSimple 2](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)

3. Po dezaktywacji urządzenia można całkowicie usunąć. Usunięcie urządzenia spowoduje usunięcie go z listy urządzeń podłączonych do usługi. Usługa nie może już zarządzać usuniętym urządzeniem. Aby usunąć urządzenie, wykonaj następujące czynności:
   
   1. Przejdź do usługi Menedżer urządzeń StorSimple i kliknij pozycję **Urządzenia**. W bloku **urządzenia** wybierz dezaktywowane urządzenie, które chcesz usunąć, kliknij prawym przyciskiem myszy, a następnie kliknij polecenie **Usuń**.

        ![Dezaktywuj Urządzenie StorSimple 3](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. W bloku **Usuń** wpisz nazwę urządzenia, aby potwierdzić, a następnie kliknij przycisk **Usuń**. Usunięcie może potrwać kilka minut.

        ![Dezaktywuj Urządzenie StorSimple 4](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. Po pomyślnym ukończeniu usuwania zostanie wyświetlone powiadomienie. Lista urządzeń jest również aktualizowana w celu odzwierciedlenia usunięcia.

## <a name="deactivate-and-retain-data"></a>Dezaktywowanie i zachowywanie danych

Jeśli interesuje Cię usunięcie urządzenia, ale chcesz zachować dane, wykonaj następujące czynności:

### <a name="to-deactivate-a-device-and-retain-the-data"></a>Aby dezaktywować urządzenie i zachować dane

1. Dezaktywuj urządzenie. Wszystkie kontenery woluminów i migawki urządzenia pozostają.
   
   1. Przejdź do usługi Menedżer urządzeń StorSimple i kliknij pozycję **Urządzenia**. W bloku **urządzenia** wybierz urządzenie, które chcesz dezaktywować, kliknij prawym przyciskiem myszy, a następnie kliknij pozycję **Dezaktywuj**.

         ![Dezaktywuj Urządzenie StorSimple 5](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. W bloku **Dezaktywuj** wpisz nazwę urządzenia, aby potwierdzić, a następnie kliknij przycisk **Dezaktywuj**. Proces dezaktywowania rozpocznie się i trwa kilka minut.

         ![Dezaktywuj Urządzenie StorSimple 6](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)
2. Teraz można przechodzić w tryb failover kontenerów woluminów i skojarzonych migawek. W przypadku procedur przejdź do [trybu failover i odzyskiwania po awarii dla urządzenia StorSimple](storsimple-8000-device-failover-disaster-recovery.md).
3. Po zdezaktywowaniu i przejściu w tryb failover można całkowicie usunąć urządzenie. Usunięcie urządzenia spowoduje usunięcie go z listy urządzeń podłączonych do usługi. Usługa nie może już zarządzać usuniętym urządzeniem. Aby usunąć urządzenie, wykonaj następujące czynności:
   
   1. Przejdź do usługi Menedżer urządzeń StorSimple i kliknij pozycję **Urządzenia**. W bloku **urządzenia** wybierz dezaktywowane urządzenie, które chcesz usunąć, kliknij prawym przyciskiem myszy, a następnie kliknij polecenie **Usuń**.

       ![Dezaktywuj Urządzenie StorSimple 7](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. W bloku **Usuń** wpisz nazwę urządzenia, aby potwierdzić, a następnie kliknij przycisk **Usuń**. Usunięcie może potrwać kilka minut.

       ![Dezaktywuj Urządzenie StorSimple 8](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. Po pomyślnym ukończeniu usuwania zostanie wyświetlone powiadomienie. Lista urządzeń jest również aktualizowana w celu odzwierciedlenia usunięcia.

## <a name="deactivate-and-delete-a-cloud-appliance"></a>Dezaktywowanie i usuwanie urządzenia w chmurze

W przypadku urządzenia w chmurze StorSimple dezaktywacja z poziomu portalu powoduje cofnięcie alokacji i usunięcie maszyny wirtualnej oraz zasobów utworzonych po zainicjowaniu obsługi administracyjnej. Po dezaktywacji urządzenia w chmurze nie można go przywrócić do poprzedniego stanu.

![Dezaktywuj urządzenie w chmurze StorSimple](./media/storsimple-8000-deactivate-and-delete-device/deactivate7.png)

Dezaktywacja powoduje wykonanie następujących czynności:

* Urządzenie w chmurze StorSimple zostanie usunięte z usługi.
* Maszyna wirtualna dla urządzenia w chmurze StorSimple jest usuwana.
* Dysk systemu operacyjnego i dyski danych utworzone dla urządzenia w chmurze StorSimple są zachowywane. Jeśli te jednostki nie są używane, należy usunąć je ręcznie.
* Usługa hostowana i Virtual Network, które zostały utworzone podczas aprowizacji, są zachowywane. Jeśli te jednostki nie są używane, należy usunąć je ręcznie.
* Migawki w chmurze utworzone przez urządzenie w chmurze StorSimple są zachowywane.

Gdy urządzenie w chmurze zostanie zdezaktywowane, można je usunąć z listy urządzeń. Wybierz dezaktywowane urządzenie, kliknij prawym przyciskiem myszy, a następnie kliknij polecenie **Usuń**. StorSimple powiadamia użytkownika po usunięciu urządzenia i wyświetleniu listy urządzeń.

## <a name="next-steps"></a>Następne kroki

* Aby przywrócić domyślne ustawienia fabryczne urządzenia, przejdź do pozycji [Zresetuj urządzenie do domyślnych ustawień fabrycznych](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).
* Aby uzyskać pomoc techniczną, [skontaktuj się z firmą pomoc techniczna firmy Microsoft](storsimple-8000-contact-microsoft-support.md).
* Aby dowiedzieć się więcej na temat korzystania z usługi StorSimple Device Manager, przejdź do [korzystania z usługi StorSimple Device Manager w celu administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

