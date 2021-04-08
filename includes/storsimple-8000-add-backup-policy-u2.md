---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: 23ce17844a0113f63931c6ece7d36bfefedc2de5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100552902"
---
#### <a name="to-add-a-storsimple-backup-policy"></a>Aby dodać zasady kopii zapasowych danych StorSimple

1. Przejdź do urządzenia StorSimple i kliknij pozycję **Zasady kopii zapasowych**.

2. W bloku **Zasady kopii zapasowych** kliknij pozycję **+ Dodaj zasady** na pasku poleceń.
   
    ![Dodawanie zasad kopii zapasowych](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-01.png)

3. W bloku **Tworzenie zasad kopii zapasowych** wykonaj następujące czynności:
   
   1. Pole **Wybierz urządzenie** jest automatycznie wypełniane na podstawie wybranego urządzenia.
   
   2. Określ **nazwę zasad** kopii zapasowych o długości od 3 do 150 znaków. Po utworzeniu zasad nie można zmienić ich nazwy.
       
   3. Aby przypisać woluminy do tych zasad kopii zapasowych, wybierz pozycję **Dodaj woluminy**, a następnie na tabelarycznej liście woluminów kliknij pola wyboru, aby przypisać woluminy do tych zasad kopii zapasowych.

       ![Dodawanie zasad kopii zapasowych 2](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-02.png)<!--Replacement screen source: create-backup-policy-addvolumes.png-->

   4. Aby zdefiniować harmonogram dla tych zasad kopii zapasowych, kliknij pozycję **Pierwszy harmonogram**, a następnie zmodyfikuj następujące parametry:<!--Do the substeps remain the same? Can they follow without a screenshot?-->

       ![Dodawanie zasad tworzenia kopii zapasowych 3](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-03.png)<!--Replacement screen source: create-backup-policy-first-schedule.png-->

       1. Dla pozycji **Typ migawki** wybierz opcję **Chmura** lub **Lokalna**.

       2. Wskaż częstotliwość wykonywania kopii zapasowych (Określ liczbę, a następnie wybierz z listy rozwijanej pozycję **dni** lub **tygodnie** ).

       3. Wprowadź harmonogram przechowywania.

       4. Wprowadź datę i godzinę rozpoczęcia dla zasad kopii zapasowych.

       5. Kliknij przycisk **OK**, aby zdefiniować harmonogram.

   5. Kliknij przycisk **Utwórz**, aby utworzyć zasady kopii zapasowych.
   
   6. Otrzymasz powiadomienie o utworzeniu zasad kopii zapasowych. Nowo dodane zasady będą wyświetlane w widoku tabelarycznym w bloku **Zasady kopii zapasowych**.

       ![Dodawanie zasad tworzenia kopii zapasowych 5](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-07.png)
