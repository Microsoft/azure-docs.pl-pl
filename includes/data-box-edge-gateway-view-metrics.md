---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: e02c0b86cd542b3ea12914e35a6577cf4e9b43d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "67183682"
---
Można również wyświetlić metryki do monitorowania wydajności urządzenia i w niektórych przypadkach rozwiązywania problemów z urządzeniami.

Wykonaj następujące kroki w Azure Portal, aby utworzyć wykres dla wybranych metryk urządzeń.

1. Dla zasobu w Azure Portal przejdź do pozycji **monitorowanie > metryki** i wybierz pozycję **Dodaj metrykę**.

    ![Dodawanie metryki](media/data-box-edge-gateway-view-metrics/view-metrics-1.png)

2. Zasób jest wypełniany automatycznie.  

    ![Bieżący zasób](media/data-box-edge-gateway-view-metrics/view-metrics-2.png)

    Aby określić inny zasób, wybierz zasób. W bloku **Wybierz zasób** wybierz subskrypcję, grupę zasobów, typ zasobu i konkretny zasób, dla którego chcesz wyświetlić metryki, a następnie wybierz pozycję **Zastosuj**.

    ![Wybierz inny zasób](media/data-box-edge-gateway-view-metrics/view-metrics-3.png)

3. Z listy rozwijanej wybierz metrykę, aby monitorować urządzenie. Metryki mogą być **metrykami wydajności** lub **metrykami transakcji**. Metryki pojemności są powiązane z pojemnością urządzenia. Metryki transakcji są powiązane z operacjami odczytu i zapisu w usłudze Azure Storage.

    |Metryki pojemności                     |Opis  |
    |-------------------------------------|-------------|
    |**Dostępna pojemność**               | Odnosi się do rozmiaru danych, które mogą być zapisywane na urządzeniu. Innymi słowy, jest to wydajność, którą można udostępnić na urządzeniu. <br></br>Możesz zwolnić pojemność urządzenia, usuwając lokalną kopię plików, która ma kopię zarówno na urządzeniu, jak i w chmurze.        |
    |**Całkowita pojemność**                   | Odnosi się do łącznej liczby bajtów na urządzeniu, do której mają zostać zapisane dane. Jest to również określane jako łączny rozmiar lokalnej pamięci podręcznej. <br></br> Teraz można zwiększyć pojemność istniejącego urządzenia wirtualnego, dodając dysk danych. Dodaj dysk danych za pomocą funkcji zarządzania funkcją hypervisor dla maszyny wirtualnej, a następnie uruchom ponownie maszynę wirtualną. Lokalna Pula magazynów urządzenia bramy zostanie rozszerzona w celu uwzględnienia nowo dodanego dysku z danymi. <br></br>Aby uzyskać więcej informacji, przejdź do obszaru [Dodawanie dysku twardego dla maszyny wirtualnej funkcji Hyper-V](https://www.youtube.com/watch?v=EWdqUw9tTe4). |
    
    |Metryki transakcji              | Opis         |
    |-------------------------------------|---------|
    |**Przekazane bajty w chmurze (urządzenie)**    | Suma wszystkich bajtów przesłanych przez wszystkie udziały na urządzeniu        |
    |**Przekazane bajty w chmurze (udział)**     | Bajty przekazane na udział. Może to być: <br></br> Średnia, czyli (suma wszystkich bajtów przesłanych na udział/liczbę udziałów),  <br></br>Max, która jest maksymalną liczbą bajtów przekazana z udziału <br></br>Minimum, czyli minimalna liczba bajtów przekazana z udziału      |
    |**Przepływność pobierania w chmurze (udział)**| Bajty pobrane na udział. Może to być: <br></br> Średnia, czyli (suma wszystkich bajtów odczytywanych lub pobranych do udziału/liczby udziałów) <br></br> Max, czyli maksymalna liczba bajtów pobranych z udziału<br></br> i min, czyli minimalna liczba bajtów pobranych z udziału  |
    |**Przepływność odczytu w chmurze**            | Suma wszystkich bajtów odczytanych z chmury między wszystkimi udziałami na urządzeniu     |
    |**Przepływność przekazywania w chmurze**          | Suma wszystkich bajtów zapisanych w chmurze dla wszystkich udziałów na urządzeniu     |
    |**Przepływność przekazywania w chmurze (udział)**  | Suma wszystkich bajtów zapisywana w chmurze z udziału/liczby udziałów jest średnia, maksymalna i minimalna na udział      |
    |**Przepływność odczytu (Sieć)**           | Obejmuje przepływność sieci systemowej dla wszystkich bajtów odczytanych z chmury. Ten widok może zawierać dane, które nie są ograniczone do udziałów. <br></br>Dzielenie spowoduje wyświetlenie ruchu przez wszystkie karty sieciowe na urządzeniu. Obejmuje to karty, które nie są połączone lub są włączone.      |
    |**Przepływność zapisu (Sieć)**       | Obejmuje przepływność sieci systemowej dla wszystkich bajtów zapisanych w chmurze. Ten widok może zawierać dane, które nie są ograniczone do udziałów. <br></br>Dzielenie spowoduje wyświetlenie ruchu przez wszystkie karty sieciowe na urządzeniu. Obejmuje to karty, które nie są połączone lub są włączone.          |
    |**Obliczenia brzegowe — użycie pamięci**      | Ta Metryka nie ma zastosowania do Data Box Gateway i dlatego nie jest wypełniona.          |
    |**Obliczenia brzegowe — procentowy procesor CPU**    | Ta Metryka nie ma zastosowania do Data Box Gateway i dlatego nie jest wypełniona.         |

4. Po wybraniu metryki z listy rozwijanej można także zdefiniować agregację. Agregacja odnosi się do wartości rzeczywistej zagregowanej w określonym przedziale czasu. Zagregowane wartości mogą być średnie, minimum lub wartości maksymalnej. Wybierz agregację z średnich, maks. lub min.

    ![Wyświetl wykres](media/data-box-edge-gateway-view-metrics/view-metrics-4.png)

5. Jeśli wybrana Metryka ma wiele wystąpień, dostępna jest opcja dzielenia. Wybierz pozycję **Zastosuj podział** , a następnie wybierz wartość, według której ma zostać wyświetlony podział.

    ![Zastosuj podział](media/data-box-edge-gateway-view-metrics/view-metrics-5.png)

6. Jeśli chcesz teraz zobaczyć podział tylko dla kilku wystąpień, możesz odfiltrować dane. Na przykład w tym przypadku, jeśli chcesz zobaczyć przepływność sieci tylko dla dwóch podłączonych interfejsów sieciowych na urządzeniu, możesz odfiltrować te interfejsy. Wybierz pozycję **Dodaj filtr** i określ nazwę interfejsu sieciowego do filtrowania.

    ![Dodaj filtr](media/data-box-edge-gateway-view-metrics/view-metrics-6.png)

7. Możesz również przypiąć wykres do pulpitu nawigacyjnego w celu ułatwienia dostępu.

    ![Przypnij do pulpitu nawigacyjnego](media/data-box-edge-gateway-view-metrics/view-metrics-7.png)

8. Aby wyeksportować dane wykresu do arkusza kalkulacyjnego programu Excel lub uzyskać link do wykresu, który można udostępnić, wybierz opcję Udostępnij na pasku poleceń.

    ![Eksportowanie danych](media/data-box-edge-gateway-view-metrics/view-metrics-8.png)