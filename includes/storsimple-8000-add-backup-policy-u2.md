---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 02274bacb66a33ef54e07bc8113d7db46d4d5296
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67183421"
---
#### <a name="to-add-a-storsimple-backup-policy"></a>Aby dodać zasady kopii zapasowych danych StorSimple

1. Przejdź do urządzenia StorSimple i kliknij pozycję **Zasady kopii zapasowych**.

2. W bloku **Zasady kopii zapasowych** kliknij pozycję **+ Dodaj zasady** na pasku poleceń.
   
    ![Dodawanie zasad kopii zapasowych](./media/storsimple-8000-add-backup-policy-u2/addbupol1.png)

3. W bloku **Tworzenie zasad kopii zapasowych** wykonaj następujące czynności:
   
   1. Pole **Wybierz urządzenie** jest automatycznie wypełniane na podstawie wybranego urządzenia.
   
   2. Określ **nazwę zasad** kopii zapasowych o długości od 3 do 150 znaków. Po utworzeniu zasad nie można zmienić ich nazwy.
       
   3. Aby przypisać woluminy do tych zasad kopii zapasowych, wybierz pozycję **Dodaj woluminy**, a następnie na tabelarycznej liście woluminów kliknij pola wyboru, aby przypisać woluminy do tych zasad kopii zapasowych.

       ![Dodawanie zasad kopii zapasowych](./media/storsimple-8000-add-backup-policy-u2/addbupol2.png)

   4. Aby zdefiniować harmonogram dla tych zasad kopii zapasowych, kliknij pozycję **Pierwszy harmonogram**, a następnie zmodyfikuj następujące parametry:

       ![Dodawanie zasad kopii zapasowych](./media/storsimple-8000-add-backup-policy-u2/addbupol3.png)

       1. Dla pozycji **Typ migawki** wybierz opcję **Chmura** lub **Lokalna**.

       2. Wskaż częstotliwość wykonywania kopii zapasowych (Określ liczbę, a następnie wybierz z listy rozwijanej pozycję **dni** lub **tygodnie** ).

       3. Wprowadź harmonogram przechowywania.

       4. Wprowadź datę i godzinę rozpoczęcia dla zasad kopii zapasowych.

       5. Kliknij przycisk **OK**, aby zdefiniować harmonogram.

   5. Kliknij przycisk **Utwórz**, aby utworzyć zasady kopii zapasowych.

       ![Dodawanie zasad kopii zapasowych](./media/storsimple-8000-add-backup-policy-u2/addbupol4.png)
   
   6. Otrzymasz powiadomienie o utworzeniu zasad kopii zapasowych. Nowo dodane zasady będą wyświetlane w widoku tabelarycznym w bloku **Zasady kopii zapasowych**.

       ![Dodawanie zasad kopii zapasowych](./media/storsimple-8000-add-backup-policy-u2/addbupol7.png)

