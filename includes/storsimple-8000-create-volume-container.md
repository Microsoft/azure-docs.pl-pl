---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: fd9b3b501d6efbe6a74d350a678494e8254dbb32
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100545404"
---
#### <a name="to-create-a-volume-container"></a>Aby utworzyć kontener woluminów

1. Przejdź do usługi Menedżer urządzeń StorSimple i kliknij pozycję **Urządzenia**. Na tabelarycznej liście urządzeń wybierz i kliknij urządzenie. 

    ![Blok kontenera woluminów](./media/storsimple-8000-create-volume-container/create-volume-container-01.png)

2. Na pulpicie nawigacyjnym urządzenia kliknij pozycję **+ Dodaj kontener woluminów**

    ![Blok kontenera woluminów 2](./media/storsimple-8000-create-volume-container/create-volume-container-02.png)

3. W bloku **Dodawanie kontenera woluminów**:
   
   1. Urządzenie jest wybierane automatycznie.
   2. Wprowadź wartość **Nazwa** kontenera woluminów. Nazwa musi składać się z 3–32 znaków. Nie można zmienić nazwy kontenera woluminów po jego utworzeniu.
   3. Wybierz pozycję **Włącz szyfrowanie magazynu w chmurze**, aby włączyć szyfrowanie danych wysyłanych z urządzenia do chmury.
   4. Wprowadź i potwierdź **klucz szyfrowania magazynu w chmurze** o długości od 8 do 32 znaków. Ten klucz jest używany przez urządzenie do uzyskiwania dostępu do zaszyfrowanych danych.
   5. Wybierz wartość **Konto magazynu** do skojarzenia z tym kontenerem woluminów. Możesz wybrać istniejące konto magazynu lub domyślne konto generowane podczas tworzenia usługi. Możesz również użyć opcji **Dodaj nowe**, aby wybrać konto magazynu, które nie zostało połączone z tą subskrypcją usługi.
   6. Wybierz pozycję **Nieograniczona** z listy rozwijanej **Określ przepustowość**, jeśli chcesz wykorzystać całą dostępną przepustowość. Możesz również ustawić tę opcję na wartość **Niestandardowa**, aby stosować kontrolę przepustowości, i wybrać wartość z przedziału od 1 do 1000 Mb/s.
   
      Jeśli masz informacje o użyciu przepustowości, możesz przydzielić przepustowość zgodnie z harmonogramem, określając wartość **Wybierz szablon przepustowości**. Aby zapoznać się z procedurą krok po kroku, przejdź do tematu dotyczącego [dodawania szablonu przepustowości](../articles/storsimple/storsimple-8000-manage-bandwidth-templates.md#add-a-bandwidth-template).

      ![Blok kontenera woluminów 3](./media/storsimple-8000-create-volume-container/create-volume-container-06-b.png)<!--New graphic. Source: add-volume-container-bw-setting.-->

   7. Kliknij pozycję **Utwórz**.

        <!--![Volume container blade 4](./media/storsimple-8000-create-volume-container/create-volume-container-06.png)-->
   
       Otrzymasz powiadomienie o pomyślnym utworzeniu kontenera woluminów.

       ![Powiadomienie o utworzeniu kontenera woluminów](./media/storsimple-8000-create-volume-container/create-volume-container-08.png)

   Nowo utworzony kontener woluminów znajduje się na liście kontenerów woluminów dla danego urządzenia.

   ![Blok dodawania kontenera woluminów](./media/storsimple-8000-create-volume-container/create-volume-container-09.png)
