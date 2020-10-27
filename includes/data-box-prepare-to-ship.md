---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 07/01/2020
ms.author: alkohli
ms.openlocfilehash: d099e33e7b35381f5404c9f8964d3ea90d4f3908
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2020
ms.locfileid: "85959480"
---
Ostatnim krokiem jest przygotowanie urządzenia do wysłania. W tym kroku wszystkie udziały urządzenia zostają przeniesione do trybu offline. Po rozpoczęciu tego procesu dostęp do udziałów nie jest już możliwy.

> [!IMPORTANT]
> Przygotowywanie do wysłania jest wymagane, ponieważ umożliwia oflagowanie danych, które nie są zgodne z konwencjami nazewnictwa platformy Azure. Ten krok zapobiega potencjalnym niepowodzeniom przekazywania danych z powodu ich niezgodności.

1. Przejdź do pozycji **Przygotowanie do wysłania** i kliknij przycisk **Rozpocznij przygotowywanie** . Domyślnie sumy kontrolne są obliczane podczas kopiowania danych. Podczas przygotowywania do wysłania kończone jest obliczanie sum kontrolnych i tworzona jest lista plików ( *pliki BOM* ). Obliczanie sum kontrolnych może potrwać klika godzin a nawet dni — w zależności od rozmiaru danych. 
   
    ![Przygotowanie do wysłania 1](media/data-box-prepare-to-ship/prepare-to-ship1.png)

    Jeśli z jakiegoś powodu chcesz zatrzymać przygotowywanie urządzenia, kliknij przycisk **Zatrzymaj przygotowywanie** . Przygotowywanie do wysłania można wznowić później.
        
    ![Przygotowanie do wysłania 2](media/data-box-prepare-to-ship/prepare-to-ship2.png)
    
2. Przygotowywanie do wysłania rozpoczyna się i udziały urządzenia przechodzą w tryb offline. <!--You see a reminder to download the shipping label once the device is ready.--> Po zakończeniu przygotowywania urządzenia jego stan jest aktualizowany do wartości *Gotowe do wysyłki* , a urządzenie jest blokowane.
        
    ![Przygotowanie do wysłania 3](media/data-box-prepare-to-ship/prepare-to-ship3.png)

    Jeśli chcesz skopiować więcej danych na urządzenie, możesz je odblokować, skopiować dane i ponownie uruchomić proces przygotowywania do wysłania.

    Jeśli w tym kroku wystąpią błędy, stan zostanie zaktualizowany do *Skanowanie ukończone z błędami* . Odblokuj urządzenie, przejdź do strony **Połącz i skopiuj** , pobierz listę problemów i usuń błędy.

    ![Przygotowanie do wysłania 1](media/data-box-prepare-to-ship/prepare-to-ship4.png)

    Po usunięciu błędów uruchom **przygotowywanie do wysłania** .

4. Po pomyślnym ukończeniu przygotowania do wysłania (bez błędów) wykonaj następujące czynności:

    1. Zanotuj numer referencyjny ukończenia. W zależności od kraju, w którym jesteś, ten numer może być wymagany do wykonania różnych operacji.
    2. Pobierz listę plików (nazywaną też manifestem), która została skopiowana w ramach tego procesu. Później możesz użyć tej listy, aby zweryfikować pliki przekazane na platformę Azure. Aby uzyskać więcej informacji, zobacz [Inspekcja plików BOM podczas przygotowywania do wysłania](../articles/databox/data-box-logs.md#inspect-bom-during-prepare-to-ship).
        
        ![Przygotowanie do wysłania 1](media/data-box-prepare-to-ship/prepare-to-ship5.png)
    3. Wybierz i pobierz instrukcje dotyczące wysyłki dla urządzenia. Instrukcje dotyczące wysyłki są różne w zależności od kraju, w którym się znajdujesz.
    4. Jeśli na wyświetlaczu E-ink nie jest wyświetlana etykieta wysyłkowa, w tym miejscu możesz pobrać etykietę przesyłki zwrotnej. 

5. Zamknij urządzenie. Przejdź do strony **Zamknij lub uruchom ponownie** i kliknij przycisk **Zamknij** . Po wyświetleniu monitu o potwierdzenie kliknij przycisk **OK** , aby kontynuować.

6. Odłącz kable. Następnym krokiem jest wysłanie urządzenia do firmy Microsoft.
