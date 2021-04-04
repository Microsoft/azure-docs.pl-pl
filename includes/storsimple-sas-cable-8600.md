---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: f08a6b3f7abfc79bff6baff2a339053905612535
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96027182"
---
#### <a name="to-attach-the-sas-cables"></a>Aby dołączyć kable SAS
1. Zidentyfikuj podstawowe i EBOD obudowy. Dwie obudowy można zidentyfikować, patrząc na odpowiednie płaszczyzny wsteczne. Zapoznaj się z poniższym obrazem, aby uzyskać wskazówki. 
   
    ![Płaszczyzna tylna podstawowych i EBODych obudów](./media/storsimple-sas-cable-8600/HCSBackplaneofprimaryandEBODenclosure.png)
   
    **Widok z tyłu podstawowych i EBOD obudów**
   
   | Etykieta | Opis |
   |:--- |:--- |
   | 1 |Obudowa podstawowa |
   | 2 |Obudowa EBOD |
2. Znajdź numery seryjne w obudowach podstawowych i EBOD. Naklejka z numerami seryjnymi jest umieszczana na odwrocie każdej obudowy. Numery seryjne muszą być takie same dla obu obudów. [Skontaktuj się pomoc techniczna firmy Microsoft](../articles/storsimple/storsimple-8000-contact-microsoft-support.md) natychmiast, jeśli numery seryjne nie są zgodne. Zapoznaj się z poniższą ilustracją, aby znaleźć numery seryjne.
   
    ![Widok z tyłu obudowy pokazujący numer seryjny](./media/storsimple-sas-cable-8600/HCSRearviewofenclosureindicatinglocationofserialnumbersticker.png)
   
    **Lokalizacja nalepki numeru seryjnego**
   
   | Etykieta | Opis |
   |:--- |:--- |
   | 1 |Przełączenie obudowy |
3. Użyj dostarczonych kabli SAS do połączenia obudowy EBOD z obudową podstawową w następujący sposób:
   
   1. Zidentyfikuj cztery porty SAS dla obudowy podstawowej i obudowy EBOD. Porty sygnatury dostępu współdzielonego są oznaczone jako EBOD w obudowie podstawowej i odpowiadają portowi A w obudowie EBOD, jak pokazano na ilustracji okablowanie SAS poniżej.
   2. Użyj dostarczonych kabli SAS, aby podłączyć port EBOD do portu A.
   3. Port EBOD na kontrolerze 0 powinien być połączony z portem A na kontrolerze EBOD (0). Port EBOD na kontrolerze 1 powinien być połączony z portem A na kontrolerze EBOD 1. Aby uzyskać wskazówki, zobacz poniższą ilustrację. 
      
      ![Okablowanie SAS dla urządzenia](./media/storsimple-sas-cable-8600/HCSSAScablingforyourdevice.png)
      
      **Okablowanie SAS**
      
      | Etykieta | Opis |
      |:--- |:--- |
      | A |Obudowa podstawowa |
      | B |Obudowa EBOD |
      | 1 |Kontroler 0 |
      | 2 |Kontroler 1 |
      | 3 |Kontroler EBOD 0 |
      | 4 |Kontroler EBOD 1 |
      | 5, 6 |Porty SAS w obudowie podstawowej (z etykietą EBOD) |
      | 7, 8 |Porty SAS w obudowie EBOD (Port A) |