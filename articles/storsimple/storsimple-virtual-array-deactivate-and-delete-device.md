---
title: Dezaktywowanie i usuwanie Microsoft Azure StorSimple macierzy wirtualnej | Microsoft Docs
description: Opisuje sposób usuwania urządzenia StorSimple z usługi przez jego dezaktywowanie, a następnie jego usunięcie.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: a929f5bc-03e2-4b01-b925-973db236f19f
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: alkohli
ms.openlocfilehash: c787df901fc33c2dcd2c8a901202c72ea6de45d4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85513604"
---
# <a name="deactivate-and-delete-a-storsimple-virtual-array"></a>Dezaktywacja i usuwanie macierzy wirtualnej usługi StorSimple

## <a name="overview"></a>Omówienie

W przypadku dezaktywowania macierzy wirtualnej StorSimple należy przerwać połączenie między urządzeniem a odpowiednią usługą StorSimple Menedżer urządzeń. W tym samouczku wyjaśniono:

* Dezaktywowanie urządzenia 
* Usuwanie dezaktywowanego urządzenia

Informacje zawarte w tym artykule dotyczą tylko tablic wirtualnych StorSimple. Aby uzyskać informacje na temat serii 8000, przejdź do pozycji jak [dezaktywować lub usunąć urządzenie](storsimple-deactivate-and-delete-device.md).

## <a name="when-to-deactivate"></a>Kiedy należy dezaktywować?

Dezaktywacja jest operacją TRWAŁą i nie można jej cofnąć. Nie można ponownie zarejestrować zdezaktywowanego urządzenia z usługą StorSimple Menedżer urządzeń. Może być konieczne dezaktywowanie i usuwanie macierzy wirtualnej StorSimple w następujących scenariuszach:

* **Planowana praca w trybie failover** : urządzenie jest w trybie online i planujesz przełączenia urządzenia w tryb pracy awaryjnej. Jeśli planujesz uaktualnienie do większego urządzenia, może zajść potrzeba przełączenia urządzenia w tryb failover. Po przeniesieniu własności danych i zakończeniu przejścia w tryb failover urządzenie źródłowe zostanie automatycznie usunięte.
* **Nieplanowana praca w trybie failover** : urządzenie jest w trybie offline i konieczne jest przełączenie na urządzenie. Ten scenariusz może wystąpić w przypadku awarii w centrum danych, a urządzenie podstawowe nie działa. Planowane jest przełączenie urządzenia w tryb failover na urządzenie pomocnicze. Po przeniesieniu własności danych i zakończeniu przejścia w tryb failover urządzenie źródłowe zostanie automatycznie usunięte.
* **Likwidowanie** : chcesz zlikwidować urządzenie. Wymaga to najpierw dezaktywacji urządzenia, a następnie jego usunięcia. Dezaktywacja urządzenia uniemożliwi dostęp do danych przechowywanych lokalnie. Można uzyskać dostęp tylko do danych przechowywanych w chmurze i je odzyskać. Jeśli planujesz zachować dane urządzenia po dezaktywacji, przed dezaktywowaniem urządzenia należy wykonać migawkę w chmurze dla wszystkich danych. Ta migawka w chmurze umożliwia odzyskanie wszystkich danych na późniejszym etapie.

## <a name="deactivate-a-device"></a>Dezaktywowanie urządzenia

Aby dezaktywować urządzenie, wykonaj następujące czynności.

#### <a name="to-deactivate-the-device"></a>Aby dezaktywować urządzenie

1. W usłudze przejdź do pozycji **zarządzanie > urządzenia**. W bloku **urządzenia** kliknij i wybierz urządzenie, które chcesz dezaktywować.
   
    ![Wybierz urządzenie do dezaktywowania](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete7.png)
2. W bloku **pulpitu nawigacyjnego urządzenia** kliknij pozycję **... Więcej** i z listy wybierz pozycję **Dezaktywuj**.
   
    ![Kliknij pozycję Dezaktywuj.](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete8.png)
3. W bloku **Dezaktywuj** wpisz nazwę urządzenia, a następnie kliknij pozycję **Dezaktywuj**. 
   
    ![Potwierdź dezaktywację](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete1.png)
   
    Proces dezaktywowania rozpocznie się i trwa kilka minut.
   
    ![Dezaktywowanie w toku](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete2.png)
4. Po dezaktywacji Lista odświeża urządzenia.
   
    ![Dezaktywacja ukończona](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete3.png)
   
    Możesz teraz usunąć to urządzenie.

## <a name="delete-the-device"></a>Usuwanie urządzenia

Aby usunąć urządzenie, należy je najpierw dezaktywować. Usunięcie urządzenia spowoduje usunięcie go z listy urządzeń podłączonych do usługi. Usługa nie może już zarządzać usuniętym urządzeniem. Dane skojarzone z urządzeniem pozostają jednak w chmurze. Następnie naliczane są opłaty za te dane.

Aby usunąć urządzenie, wykonaj następujące czynności.

#### <a name="to-delete-the-device"></a>Aby usunąć urządzenie

1. W Menedżer urządzeń StorSimple przejdź do pozycji **zarządzanie > urządzenia**. W bloku **urządzenia** wybierz dezaktywowane urządzenie, które chcesz usunąć.
2. W bloku **pulpitu nawigacyjnego urządzenia** kliknij pozycję **... Więcej** , a następnie kliknij przycisk **Usuń**.
   
   ![Wybierz urządzenie do usunięcia](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete4.png)
3. W bloku **Usuń** wpisz nazwę urządzenia, aby potwierdzić usunięcie, a następnie kliknij przycisk **Usuń**. Usunięcie urządzenia nie powoduje usunięcia danych w chmurze skojarzonych z urządzeniem. 
   
   ![Potwierdzenie usunięcia](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete5.png) 
4. Usuwanie rozpocznie się i trwa kilka minut.
   
   ![Usuwanie w toku](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete6.png)
   
    Po usunięciu urządzenia można wyświetlić zaktualizowaną listę urządzeń.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje na temat sposobu pracy awaryjnej, przejdź do [trybu failover i odzyskiwanie po awarii macierzy wirtualnej StorSimple](storsimple-virtual-array-failover-dr.md).

* Aby dowiedzieć się więcej na temat korzystania z usługi StorSimple Menedżer urządzeń, przejdź do [korzystania z usługi StorSimple Menedżer urządzeń w celu administrowania wirtualną tablicą StorSimple](storsimple-virtual-array-manager-service-administration.md). 

