---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: 71f18cf8448060385ea38be9b2719b1ed545c5d2
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100545422"
---
> [!NOTE] 
> Nie można zmodyfikować ustawień szyfrowania i poświadczeń konta magazynu skojarzonych z kontenerem woluminów po jego utworzeniu.

#### <a name="to-modify-a-volume-container"></a>Aby zmodyfikować kontener woluminów

1. Przejdź do usługi StorSimple Device Manager, a następnie przejdź do pozycji **zarządzanie > kontenery woluminów**.

2. Z listy tabelarycznej kontenerów woluminów wybierz kontener woluminów, który chcesz zmodyfikować. Na stronie **urządzenia** wybierz urządzenie, kliknij je dwukrotnie, a następnie kliknij kartę **kontenery woluminów** .

3. Na tabelarycznej liście kontenerów woluminów wybierz kontener woluminów, który chcesz zmodyfikować. W bloku, który zostanie otwarty, kliknij przycisk **Modyfikuj** na pasku poleceń.

    ![Modyfikowanie kontenera woluminów](./media/storsimple-8000-modify-volume-container/modify-volume-container-01.png)

4. W bloku **Modyfikowanie kontenera woluminów** wykonaj następujące czynności:
   
   1. Po określeniu nazwy, klucza szyfrowania i konta magazynu skojarzonego z kontenerem woluminów nie można go zmienić. Zmień ustawienie skojarzonej przepustowości.<!--STEPS NEED WORK. Updated screen doesn't show alternative to Unlimited or subsequent steps if they customize bandwidth. Can we talk them through this (briefly)?-->
      
       ![Zmień ustawienie przepustowości](./media/storsimple-8000-modify-volume-container/modify-volume-container-02.png)<!--New graphic based on: modify-volume-container-bw-setting.png-->

   1.  Kliknij przycisk **OK**.<!--If they choose Custom, do they still click OK, or are there more steps?-->

5. Na następnej stronie okna dialogowego **Modyfikowanie kontenera woluminów** :<!--This step happens only if they choose Custom bandwidth? Are the steps similar to those in "Add volume container," step 3f, above?"-->
   
   1. Z listy rozwijanej wybierz istniejący szablon przepustowości.
   1. Sprawdź ustawienia harmonogramu dla określonego szablonu przepustowości.
   1. Kliknij przycisk **Zapisz** i potwierdź zmiany.
      
       ![Potwierdź zmiany](./media/storsimple-8000-modify-volume-container/modify-volume-container-03.png)

      Blok **kontenerów woluminów** został zaktualizowany w celu odzwierciedlenia zmian.
