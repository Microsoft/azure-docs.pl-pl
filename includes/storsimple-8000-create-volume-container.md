---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 467af776af95cf035121250fdcadd2fee65d9805
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67183276"
---
#### <a name="to-create-a-volume-container"></a>Aby utworzyć kontener woluminów
1. Przejdź do usługi Menedżer urządzeń StorSimple i kliknij pozycję **Urządzenia**. Na tabelarycznej liście urządzeń wybierz i kliknij urządzenie. 

    ![Blok kontenera woluminów](./media/storsimple-8000-create-volume-container/createvolumecontainer1.png)

2. Na pulpicie nawigacyjnym urządzenia kliknij pozycję **+ Dodaj kontener woluminów**

    ![Blok kontenera woluminów](./media/storsimple-8000-create-volume-container/createvolumecontainer2.png)

3. W bloku **Dodawanie kontenera woluminów**:
   
   1. Urządzenie jest wybierane automatycznie.
   2. Wprowadź wartość **Nazwa** kontenera woluminów. Nazwa musi składać się z 3–32 znaków. Nie można zmienić nazwy kontenera woluminów po jego utworzeniu.
   3. Wybierz pozycję **Włącz szyfrowanie magazynu w chmurze**, aby włączyć szyfrowanie danych wysyłanych z urządzenia do chmury.
   4. Wprowadź i potwierdź **klucz szyfrowania magazynu w chmurze** o długości od 8 do 32 znaków. Ten klucz jest używany przez urządzenie do uzyskiwania dostępu do zaszyfrowanych danych.
   5. Wybierz wartość **Konto magazynu** do skojarzenia z tym kontenerem woluminów. Możesz wybrać istniejące konto magazynu lub domyślne konto generowane podczas tworzenia usługi. Możesz również użyć opcji **Dodaj nowe**, aby wybrać konto magazynu, które nie zostało połączone z tą subskrypcją usługi.
   6. Wybierz pozycję **Nieograniczona** z listy rozwijanej **Określ przepustowość**, jeśli chcesz wykorzystać całą dostępną przepustowość. Możesz również ustawić tę opcję na wartość **Niestandardowa**, aby stosować kontrolę przepustowości, i wybrać wartość z przedziału od 1 do 1000 Mb/s.
      Jeśli masz informacje o użyciu przepustowości, możesz przydzielić przepustowość zgodnie z harmonogramem, określając wartość **Wybierz szablon przepustowości**. Aby zapoznać się z procedurą krok po kroku, przejdź do tematu dotyczącego [dodawania szablonu przepustowości](../articles/storsimple/storsimple-8000-manage-bandwidth-templates.md#add-a-bandwidth-template).

      ![Blok kontenera woluminów](./media/storsimple-8000-create-volume-container/createvolumecontainer6b.png)
   7. Kliknij przycisk **Utwórz**.

        ![Blok kontenera woluminów](./media/storsimple-8000-create-volume-container/createvolumecontainer6.png)
   
       Otrzymasz powiadomienie o pomyślnym utworzeniu kontenera woluminów.

       ![Powiadomienie o utworzeniu kontenera woluminów](./media/storsimple-8000-create-volume-container/createvolumecontainer8.png)

   Nowo utworzony kontener woluminów znajduje się na liście kontenerów woluminów dla danego urządzenia.

   ![Blok dodawania kontenera woluminów](./media/storsimple-8000-create-volume-container/createvolumecontainer9.png)


