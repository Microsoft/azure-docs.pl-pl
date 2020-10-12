---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 9ac7966538102273b91d6b7f15b90e18ceedd421
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83779935"
---
Można również wyświetlić metryki do monitorowania wydajności urządzenia i w niektórych przypadkach rozwiązywania problemów z urządzeniami.

Wykonaj następujące kroki w Azure Portal, aby utworzyć wykres dla wybranych metryk urządzeń.

1. Dla zasobu w Azure Portal przejdź do pozycji **monitorowanie > metryki** i wybierz pozycję **Dodaj metrykę**.

    ![Dodawanie metryki](media/data-box-edge-gateway-view-metrics/view-metrics-1.png)

2. Zasób jest wypełniany automatycznie.  

    ![Bieżący zasób](media/data-box-edge-gateway-view-metrics/view-metrics-2.png)

    Aby określić inny zasób, wybierz zasób. W bloku **Wybierz zasób** wybierz subskrypcję, grupę zasobów, typ zasobu i konkretny zasób, dla którego chcesz wyświetlić metryki, a następnie wybierz pozycję **Zastosuj**.

    ![Wybierz inny zasób](media/data-box-edge-gateway-view-metrics/view-metrics-3.png)

3. Z listy rozwijanej wybierz metrykę, aby monitorować urządzenie. Aby zapoznać się z pełną listą tych metryk, zobacz [metryki na urządzeniu](#metrics-on-your-device).

4. Po wybraniu metryki z listy rozwijanej można także zdefiniować agregację. Agregacja odnosi się do wartości rzeczywistej zagregowanej w określonym przedziale czasu. Zagregowane wartości mogą być średnie, minimum lub wartości maksymalnej. Wybierz agregację z średnich, maks. lub min.

    ![Wyświetl wykres](media/data-box-edge-gateway-view-metrics/view-metrics-4.png)

5. Jeśli wybrana Metryka ma wiele wystąpień, dostępna jest opcja dzielenia. Wybierz pozycję **Zastosuj podział** , a następnie wybierz wartość, według której ma zostać wyświetlony podział.

    ![Zastosuj podział](media/data-box-edge-gateway-view-metrics/view-metrics-5.png)

6. Jeśli chcesz teraz zobaczyć podział tylko dla kilku wystąpień, możesz odfiltrować dane. Na przykład w tym przypadku, jeśli chcesz zobaczyć przepływność sieci tylko dla dwóch podłączonych interfejsów sieciowych na urządzeniu, możesz odfiltrować te interfejsy. Wybierz pozycję **Dodaj filtr** i określ nazwę interfejsu sieciowego do filtrowania.

    ![Dodaj filtr](media/data-box-edge-gateway-view-metrics/view-metrics-6.png)

7. Możesz również przypiąć wykres do pulpitu nawigacyjnego w celu ułatwienia dostępu.

    ![Przypnij do pulpitu nawigacyjnego](media/data-box-edge-gateway-view-metrics/view-metrics-7.png)

8. Aby wyeksportować dane wykresu do arkusza kalkulacyjnego programu Excel lub uzyskać link do wykresu, który można udostępnić, wybierz opcję Udostępnij na pasku poleceń.

    ![Eksportuj dane](media/data-box-edge-gateway-view-metrics/view-metrics-8.png)
