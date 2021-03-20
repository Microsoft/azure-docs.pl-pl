---
title: Zastępowanie modułu PCM na urządzeniu z serii StorSimple 8000 | Microsoft Docs
description: Wyjaśnia, jak usunąć i zamienić moduł zarządzania i chłodzenia (PCM) na urządzeniu StorSimple
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: 045cec85174a88d1d608a4adc679461008852768
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85514583"
---
# <a name="replace-a-power-and-cooling-module-on-your-storsimple-device"></a>Wymiana modułu zasilania i chłodzenia w urządzeniu StorSimple
## <a name="overview"></a>Omówienie
Moduł zarządzania zasilaniem i chłodzeniem (PCM) na urządzeniu Microsoft Azure StorSimple składa się z wentylatorów i gniazd chłodzących, które są kontrolowane przez podstawowe i EBOD obudowy. Istnieje tylko jeden model modułu PCM certyfikowany dla każdej obudowy. Obudowa podstawowa jest certyfikowana do 764 W module PCM, a obudowa EBOD jest certyfikowana dla 580 W module PCM. Mimo że PCMs dla obudowy podstawowej i obudowy EBOD są różne, procedura zastępująca jest taka sama.

W tym samouczku wyjaśniono:

* Usuwanie modułu PCM
* Instalowanie zamiennika modułu PCM

> [!IMPORTANT]
> Przed usunięciem i zastępowaniem modułu PCM zapoznaj się z informacjami o zabezpieczeniach [zastępują składniki sprzętowe StorSimple](storsimple-8000-hardware-component-replacement.md).


## <a name="before-you-replace-a-pcm"></a>Przed zastępowaniem modułu PCM
Przed zastępowaniem modułu PCM należy pamiętać o następujących ważnych problemach:

* Jeśli zasilanie modułu PCM zakończy się niepowodzeniem, pozostaw zainstalowany wadliwy moduł, ale Usuń przewód zasilający. Wentylator nadal będzie odbierać energię z obudowy i kontynuuje zapewnienie prawidłowego chłodzenia. Jeśli wentylator ulegnie awarii, moduł PCM musi zostać zastąpiony natychmiast.
* Przed usunięciem modułu PCM Odłącz zasilanie od modułu PCM, wyłączając przełącznik główny (tam gdzie jest obecny) lub fizycznie usuwając przewód zasilający. Zapewnia to ostrzeżenie dotyczące systemu, które jest bliskie zamknięcia.
* Upewnij się, że inny moduł PCM jest funkcjonalny do kontynuowania operacji systemu przed zastąpieniem uszkodzonego modułu PCM. Uszkodzony moduł PCM musi zostać zastąpiony w pełni działającym modułem PCM tak szybko, jak to możliwe.
* Zastępowanie modułu PCM trwa zaledwie kilka minut, ale należy je wykonać w ciągu 10 minut od usunięcia niepowodzenia modułu PCM, aby zapobiec przegrzaniu.
* Należy zauważyć, że zastępowanie modułów 764 W module PCM dostarczonych od fabryki nie zawiera modułu baterii tworzenia kopii zapasowych. Należy usunąć baterię z uszkodzonego modułu PCM, a następnie wstawić ją do zastępujący przed zastępowaniem. Aby uzyskać więcej informacji, zobacz jak [usunąć i wstawić moduł baterii tworzenia kopii zapasowej](storsimple-8000-battery-replacement.md).

## <a name="remove-a-pcm"></a>Usuwanie modułu PCM
Postępuj zgodnie z tymi instrukcjami, gdy wszystko jest gotowe do usunięcia modułu do obsługi zasilacza i chłodzenia (PCM) z urządzenia Microsoft Azure StorSimple.

> [!NOTE]
> Przed usunięciem modułu PCM Sprawdź, czy masz poprawną zamianę (764 W przypadku podstawowej obudowy lub 580 W przypadku obudowy EBOD).

#### <a name="to-remove-a-pcm"></a>Aby usunąć moduł PCM
1. W klasycznym portalu Azure kliknij pozycję **ustawienia > monitoruj > kondycja sprzętu**. Sprawdź stan składników modułu PCM w obszarze **składniki udostępnione** , aby określić, który moduł PCM nie powiódł się:
   
   * Jeśli zasilacz w module PCM 0 zakończył się niepowodzeniem, stan **zasilacza w module PCM 0** będzie czerwony.
   * Jeśli zasilacz w module PCM 1 zakończył się niepowodzeniem, stan **zasilacza w module PCM 1** będzie czerwony.
   * Jeśli wentylator w module PCM 1 zakończył się niepowodzeniem, stanem **chłodzenia 0 dla PCM 0** lub **chłodzenia 1 dla PCM 0** będzie czerwony.
2. Znajdź moduł PCM zakończony niepowodzeniem z tyłu obudowy podstawowej. Jeśli korzystasz z modelu 8600, zidentyfikuj podstawową obudowę, przeglądając numer identyfikacyjny jednostki systemowej widoczny w wyświetlonym ekranie diody LED. Domyślnym IDENTYFIKATORem jednostki wyświetlanym w obudowie podstawowej jest **00**, podczas gdy domyślny identyfikator jednostki wyświetlany w obudowie EBOD jest **01**. Poniższy diagram i tabela zawierają opis przedniego panelu ekranu diody LED.
   
    ![Identyfikator systemu w panelu OPS](./media/storsimple-power-cooling-module-replacement/IC740991.png)
   
     **Rysunek 1** Panel przedni urządzenia  
   
   | Etykieta | Opis |
   |:--- |:--- |
   | 1 |Przycisk Wycisz |
   | 2 |Moc systemu |
   | 3 |Błąd modułu |
   | 4 |Błąd logiczny |
   | 5 |Wyświetlanie identyfikatora jednostki |
3. Diody LED wskaźnika monitorowania z tyłu obudowy podstawowej mogą być również używane do identyfikowania uszkodzonego modułu PCM. Zapoznaj się z poniższym diagramem i tabelą, aby dowiedzieć się, jak znaleźć uszkodzony moduł PCM przy użyciu diod LED. Jeśli na przykład dioda LED nie **ulegnie awarii** , wentylator zakończył się niepowodzeniem. Podobnie, Jeśli dioda LED nie **powiedzie** się, zasilacz nie powiódł się. 
   
    ![Przeplanowanie diod LED wskaźnika monitorowania urządzenia PCM](./media/storsimple-power-cooling-module-replacement/IC740992.png)
   
     **Rysunek 2** Z tyłu PCM za pomocą diod LED wskaźnika
   
   | Etykieta | Opis |
   |:--- |:--- |
   | 1 |Awaria zasilania AC |
   | 2 |Awaria wentylatora |
   | 3 |Awaria baterii |
   | 4 |MODUŁ PCM JEST PRAWIDŁOWY |
   | 5 |Awaria zasilania kontrolera domeny |
   | 6 |Kondycja baterii |
4. Zapoznaj się z poniższym diagramem tylnej części urządzenia StorSimple, aby zlokalizować moduł PCM, który się nie powiódł. Moduł PCM 0 jest po lewej stronie, a moduł PCM 1 znajduje się po prawej stronie. W poniższej tabeli objaśniono moduły.
   
     ![Planowanie planu podstawowego modułów obudowy urządzenia](./media/storsimple-power-cooling-module-replacement/IC740994.png)
   
     **Rysunek 3** Z tyłu urządzenia za pomocą modułów wtyczek 
   
   | Etykieta | Opis |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Kontroler 0 |
   | 4 |Kontroler 1 |
5. Wyłącz uszkodzony moduł PCM i Odłącz przewód zasilacza. Teraz możesz usunąć moduł PCM.
6. Opanujesz zamka i bok uchwytu PCM między kciukiem i forefinger i ściskaj je, aby otworzyć uchwyt.
   
    ![Otwieranie uchwytu PCM](./media/storsimple-power-cooling-module-replacement/IC740995.png)
   
    **Rysunek 4** Otwieranie uchwytu PCM
7. Uchwyt uchwytu i usunięcie modułu PCM.
   
    ![Usuwanie urządzenia PCM](./media/storsimple-power-cooling-module-replacement/IC740996.png)
   
    **Rysunek 5** Usuwanie modułu PCM

## <a name="install-a-replacement-pcm"></a>Instalowanie zamiennika modułu PCM
Postępuj zgodnie z tymi instrukcjami, aby zainstalować moduł PCM na urządzeniu StorSimple. Przed zainstalowaniem wymiennego modułu PCM upewnij się, że został wstawiony moduł baterii kopii zapasowej (dotyczy tylko 764 W PCMs). Aby uzyskać więcej informacji, zobacz jak [usunąć i wstawić moduł baterii tworzenia kopii zapasowej](storsimple-8000-battery-replacement.md).

#### <a name="to-install-a-pcm"></a>Aby zainstalować moduł PCM
1. Sprawdź, czy masz prawidłowy zamiennik PCM dla tej obudowy. Obudowa podstawowa wymaga 764 W module PCM, a obudowa EBOD wymaga 580 W module PCM. Nie należy próbować używać 580 w module PCM w obudowie podstawowej ani 764 w module PCM w obudowie EBOD. Na poniższej ilustracji przedstawiono lokalizację, w której można zidentyfikować te informacje na etykiecie, która jest umieszczana w module PCM.
   
    ![Etykieta PCM urządzenia](./media/storsimple-power-cooling-module-replacement/IC740973.png)
   
    **Rysunek 6** . Etykieta PCM
2. Zapoznaj się z uszkodzeniem obudowy, zwracając szczególną uwagę na łączniki. 
   
   > [!NOTE]
   > **Nie należy instalować modułu, jeśli jakiekolwiek numery PIN łącznika są wygięte.**
   > 
   > 
3. Za pomocą uchwytu PCM w pozycji Otwórz przesuń moduł do obudowy.
   
    ![Instalowanie modułu PCM urządzenia](./media/storsimple-power-cooling-module-replacement/IC740975.png)
   
    **Rysunek 7** Instalowanie modułu PCM
4. Ręcznie Zamknij uchwyt PCM. Należy usłyszeć kliknięcie, gdy zachodzi zamknięcie uchwytu.
   
   > [!NOTE]
   > Aby upewnić się, że numery PIN łączników miały zajęty dostęp, można delikatnie tug na dojście bez zwalniania zamka. Jeśli wystąpiło slajdy PCM, oznacza to, że zamknięcie zostało zamknięte przed przeprowadzeniem łączników.
   
5. Podłącz kable zasilające do źródła prądu i modułu PCM.
6. Zabezpiecz ulgi Bales.
7. Włącz moduł PCM.
8. Sprawdź, czy zastąpienie zakończyło się pomyślnie: w Azure Portal usługi StorSimple Device Manager przejdź do urządzenia, a następnie wybierz pozycję **ustawienia > monitoruj > kondycji sprzętu**. W obszarze **składniki udostępnione** stan PCM powinien być zielony.
   
   > [!NOTE]
   > Zastępowanie modułu PCM może potrwać kilka minut.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [wymianie składników sprzętowych StorSimple](storsimple-8000-hardware-component-replacement.md).

