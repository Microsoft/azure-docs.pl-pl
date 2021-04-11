---
title: Zaplanuj wydajność odzyskiwania po awarii funkcji Hyper-V za pomocą Azure Site Recovery
description: Ten artykuł służy do oceny pojemności podczas konfigurowania odzyskiwania po awarii przy użyciu usługi Azure Site Recovery.
services: site-recovery
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 0bdc66b46e83a1592d1ea74c1080d6f8080f4635
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580655"
---
# <a name="plan-capacity-for-hyper-v-vm-disaster-recovery"></a>Planowanie wydajności na potrzeby odzyskiwania po awarii maszyny wirtualnej funkcji Hyper-V 

Program [Planista wdrażania usługi Azure Site Recovery] (site-recovery-hyper-v-deployment-planner.md) do wdrażania funkcji Hyper-V na platformie Azure zapewnia następujące informacje:

* Ocena uprawnień maszyny wirtualnej na podstawie liczby dysków, rozmiaru dysku, operacji we/wy na sekundę, zmian i kilku cech maszyn wirtualnych
* Zapotrzebowanie na przepustowość sieci w porównaniu z oceną celu punktu odzyskiwania
* Wymagania dotyczące infrastruktury platformy Azure
* Wymagania dotyczące infrastruktury lokalnej
* Wskazówki dotyczące dzielenia na partie replikacji początkowej
* Szacowany łączny koszt odzyskiwania po awarii na platformie Azure


Planista wydajności usługi Azure Site Recovery pomaga określić wymagania dotyczące pojemności podczas replikowania maszyn wirtualnych funkcji Hyper-V z Azure Site Recovery.

Użyj Planista wydajności Site Recovery, aby analizować środowisko źródłowe i obciążenia. Ułatwia to oszacowanie potrzeb dotyczących przepustowości, zasobów serwera potrzebnych do lokalizacji źródłowej oraz zasobów (takich jak maszyny wirtualne i magazyn), które są potrzebne w lokalizacji docelowej.

Narzędzie można uruchomić w dwóch trybach:

* **Szybkie planowanie**: zapewnia projekcję sieci i serwera na podstawie średniej liczby maszyn wirtualnych, dysków, magazynu i szybkości zmian.
* **Szczegółowe planowanie**: zawiera szczegółowe informacje o każdym obciążeniu na poziomie maszyny wirtualnej. Analizowanie zgodności maszyn wirtualnych i pobieranie projekcji sieci i serwera.

## <a name="before-you-start"></a>Przed rozpoczęciem

* Zbierz informacje o środowisku, w tym maszyny wirtualne, dyski na maszynę wirtualną, magazyn na dysk.
* Zidentyfikuj częstotliwość dziennej zmiany (zmian) dla replikowanych danych. Pobierz [Narzędzie do planowania pojemności funkcji Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) , aby uzyskać współczynnik zmian. [Dowiedz się więcej](./hyper-v-deployment-planner-overview.md) na temat tego narzędzia. Zalecamy uruchomienie tego narzędzia w tygodniu, aby przechwycić średnią.


## <a name="run-the-quick-planner"></a>Uruchamianie szybkiego planisty
1. Pobierz i Otwórz [Planista wydajności Site Recovery](/samples/browse/?redirectedfrom=TechNet-Gallery). Należy uruchomić makra. Po wyświetleniu monitu wybierz opcje, aby włączyć edytowanie i zawartość.

2. W polu listy **Wybierz typ planisty** wybierz pozycję **Quick Planner**.

   ![Zrzut ekranu przedstawiający opcję Wybierz typ planisty z wybranym szybkim terminarzem.](./media/site-recovery-capacity-planner/getting-started.png)

3. W arkuszu **planista wydajności** wprowadź wymagane informacje. Wypełnij wszystkie pola wyróżnione czerwonymi na poniższym zrzucie ekranu:

   a. W obszarze **Wybierz swój scenariusz** wybierz pozycję **Hyper-V do platformy Azure** lub **oprogramowanie VMware/Physical na platformie Azure**.

   b. W polu **Średni dzienny współczynnik zmian danych (%)** wprowadź informacje zbierane przy użyciu [narzędzia planowania pojemności funkcji Hyper-V](./hyper-v-deployment-planner-overview.md) lub [planista wdrażania Site Recovery](./site-recovery-deployment-planner.md).

   c. Ustawienie **kompresji** nie jest używane w przypadku replikowania maszyn wirtualnych funkcji Hyper-V do platformy Azure. W przypadku kompresji Użyj urządzenia innej firmy, takiego jak Riverbed.

   d. W polu **przechowywanie w dniach** Określ czas przechowywania replik w dniach.

   e. W **godzinach, w których replikacja początkowa partii maszyn wirtualnych powinna zakończyć** się i **Liczba maszyn wirtualnych na partię replikacji początkowej**, wprowadź ustawienia, które są używane do obliczania wymagań replikacji początkowej. Po wdrożeniu Site Recovery cały początkowy zestaw danych zostanie przekazany.

   ![Zrzut ekranu przedstawiający arkusz Planista wydajności, pokazujący wymagane informacje wejściowe.](./media/site-recovery-capacity-planner/inputs.png)

4. Po wprowadzeniu wartości dla środowiska źródłowego wyświetlane dane wyjściowe obejmują:

   * **Przepustowość wymagana na potrzeby replikacji różnicowej (w megabitach/s)**: przepustowość sieci na potrzeby replikacji różnicowej jest obliczana na podstawie średniego dziennego współczynnika zmian danych.
   * **Przepustowość wymagana dla replikacji początkowej (w megabitach/s)**: przepustowość sieci na potrzeby replikacji początkowej jest obliczana na wprowadzonych wartościach replikacji początkowej.
   * **Wymagane miejsce do magazynowania (w GB)**: Łączna ilość wymaganych magazynów platformy Azure.
   * **Łączna liczba operacji we/wy w magazynie w warstwie Standardowa**: liczba jest obliczana na podstawie rozmiaru jednostki IOPS 8K na łącznym koncie magazynu w warstwie Standardowa. W przypadku szybkiego planisty liczba jest obliczana na podstawie wszystkich źródłowych dysków maszyny wirtualnej i dziennego współczynnika zmian danych. W przypadku szczegółowego planisty liczba jest obliczana na podstawie łącznej liczby maszyn wirtualnych mapowanych na standardowe maszyny wirtualne platformy Azure, a szybkość zmian danych na tych maszynach wirtualnych.
   * **Wymagana liczba kont magazynu w warstwie Standardowa**: Łączna liczba kont magazynu w warstwie Standardowa potrzebna do ochrony maszyn wirtualnych. Konto magazynu w warstwie Standardowa może zawierać maksymalnie 20 000 operacji we/wy na wszystkie maszyny wirtualne w magazynie w warstwie Standardowa. Maksymalna liczba operacji we/wy 500 jest obsługiwana na dysku.
   * **Wymagana liczba dysków BLOB**: liczba dysków tworzonych w usłudze Azure Storage.
   * **Wymagana liczba kont Premium**: Łączna liczba kont magazynu w warstwie Premium potrzebnych do ochrony maszyn wirtualnych. Źródłowa maszyna wirtualna o dużej liczbie operacji we/wy (ponad 20 000) wymaga konta magazynu w warstwie Premium. Konto magazynu w warstwie Premium może zawierać maksymalnie 80 000 operacji we/wy na sekundę.
   * **Łączna liczba operacji we/wy na Premium Storage**: liczba jest obliczana na podstawie rozmiaru jednostki IOPS 256 k na łącznym koncie magazynu w warstwie Premium. W przypadku szybkiego planisty liczba jest obliczana na podstawie wszystkich źródłowych dysków maszyny wirtualnej i dziennego współczynnika zmian danych. W przypadku szczegółowego planisty liczba jest obliczana na podstawie łącznej liczby maszyn wirtualnych mapowanych na maszyny wirtualne platformy Azure w warstwie Premium (seria DS i GS) oraz szybkość zmian danych na tych maszynach wirtualnych.
   * **Liczba wymaganych serwerów konfiguracji**: pokazuje, ile serwerów konfiguracji jest wymaganych do wdrożenia.
   * **Liczba dodatkowych wymaganych serwerów przetwarzania**: wskazuje, czy oprócz serwera przetwarzania, który jest domyślnie uruchomiony na serwerze konfiguracji, jest wymagane dodatkowe serwery przetwarzania.
   * **100% dodatkowego magazynu w źródle**: wskazuje, czy w lokalizacji źródłowej jest wymagany dodatkowy magazyn.

      ![Zrzut ekranu przedstawiający wyświetlane dane wyjściowe na podstawie podanego wejścia.](./media/site-recovery-capacity-planner/output.png)

## <a name="run-the-detailed-planner"></a>Uruchamianie szczegółowego planisty

1. Pobierz i Otwórz [Planista wydajności Site Recovery](/samples/browse/?redirectedfrom=TechNet-Gallery). Należy uruchomić makra. Po wyświetleniu monitu wybierz opcje, aby włączyć edytowanie i zawartość.

2. W obszarze **Wybierz typ planisty** wybierz pozycję **szczegółowy planista** w polu listy.

   ![Zrzut ekranu przedstawiający opcję Wybierz typ planisty z wybraną szczegółowym terminarzem.](./media/site-recovery-capacity-planner/getting-started-2.png)

3. W arkuszu **kwalifikacje obciążenia** wprowadź wymagane informacje. Musisz wypełnić wszystkie pola oznaczone.

   a. W obszarze **rdzenie procesora** Określ łączną liczbę rdzeni na serwerze źródłowym.

   b. W obszarze **alokacja pamięci (w MB)** Określ rozmiar pamięci RAM serwera źródłowego.

   c. W obszarze **Liczba kart interfejsu** sieciowego Określ liczbę karty sieciowych na serwerze źródłowym.

   d. W obszarze **łączny rozmiar magazynu (w GB)** Określ łączną ilość miejsca w magazynie maszyn wirtualnych. Jeśli na przykład serwer źródłowy ma trzy dyski z 500 GB każdy, łączny rozmiar magazynu to 1 500 GB.

   e. W polu **liczba podłączonych dysków** Określ łączną liczbę dysków serwera źródłowego.

   f. W obszarze **wykorzystanie pojemności dysku (%)** Określ średnie użycie.

   przykład W **dziennym współczynniku zmian danych (%)** Określ dzienny współczynnik zmian danych serwera źródłowego.

   h. W obszarze **Mapowanie rozmiaru maszyny wirtualnej platformy Azure** wprowadź rozmiar maszyny wirtualnej platformy Azure, która ma zostać zmapowana. Jeśli nie chcesz tego robić ręcznie, wybierz pozycję **obliczenia IaaS maszyny wirtualne**. Jeśli wprowadzasz ustawienia ręczne, a następnie wybierzesz pozycję **COMPUTE IaaS maszyny wirtualne**, ustawienie ręczne może zostać nadpisane. Proces obliczeń automatycznie identyfikuje najlepsze dopasowanie rozmiaru maszyny wirtualnej platformy Azure.

   ![Zrzut ekranu arkusza kwalifikacji obciążeń przedstawiający wymagane informacje wejściowe.](./media/site-recovery-capacity-planner/workload-qualification.png)

4. W przypadku wybrania opcji **Oblicz IaaS maszyny wirtualne** można wykonać następujące czynności:

   * Weryfikuje obowiązkowe dane wejściowe.
   * Oblicza liczbę IOPS i sugeruje najlepszą zgodność rozmiaru maszyny wirtualnej platformy Azure dla każdej maszyny wirtualnej, która kwalifikuje się do replikacji na platformę Azure. Jeśli nie można wykryć odpowiedniego rozmiaru maszyny wirtualnej platformy Azure, zostanie wyświetlony komunikat o błędzie. Jeśli na przykład liczba podłączonych dysków to 65, zostanie wyświetlony błąd, ponieważ najwyższy rozmiar maszyny wirtualnej platformy Azure to 64.
   * Sugeruje konto magazynu, które może być używane dla maszyny wirtualnej platformy Azure.
   * Oblicza łączną liczbę kont magazynu w warstwie Standardowa i kont magazynu w warstwie Premium wymaganych do obciążania. Przewiń w dół, aby wyświetlić typ usługi Azure Storage i konto magazynu, które mogą być używane na potrzeby serwera źródłowego.
   * Wykonuje i sortuje resztę tabeli w oparciu o wymagany typ magazynu (standardowa lub Premium) przypisany do maszyny wirtualnej oraz liczbę podłączonych dysków. W przypadku wszystkich maszyn wirtualnych, które spełniają wymagania platformy Azure, kolumna **jest kwalifikowana dla maszyny wirtualnej?** pokazuje **wartość tak**. Jeśli nie można utworzyć kopii zapasowej maszyny wirtualnej na platformie Azure, zostanie wyświetlony komunikat o błędzie.

Kolumny AA do AE są danymi wyjściowymi i zawierają informacje dla każdej maszyny wirtualnej.

![Zrzut ekranu przedstawiający kolumny wyjściowe od AA do AE.](./media/site-recovery-capacity-planner/workload-qualification-2.png)

### <a name="example"></a>Przykład
Przykładowo w przypadku sześciu maszyn wirtualnych z wartościami podanymi w tabeli narzędzie oblicza i przypisuje najlepszą zgodność z maszyną wirtualną platformy Azure oraz wymagania dotyczące usługi Azure Storage.

![Zrzut ekranu przedstawiający przypisania kwalifikacji obciążeń.](./media/site-recovery-capacity-planner/workload-qualification-3.png)

* W przykładzie danych wyjściowych Zwróć uwagę na następujące kwestie:

  * Pierwsza kolumna jest kolumną walidacji dla maszyn wirtualnych, dysków i zmian.
  * W przypadku pięciu maszyn wirtualnych potrzeba dwóch kont magazynu w warstwie Standardowa i jednego konta magazynu w warstwie Premium.
  * VM3 nie kwalifikuje się do ochrony, ponieważ co najmniej jeden dysk ma więcej niż 1 TB.
  * VM1 i VM2 mogą korzystać z pierwszego konta magazynu w warstwie Standardowa
  * VM4 może używać drugiego konta magazynu w warstwie Standardowa.
  * VM5 i VM6 wymagają konta magazynu w warstwie Premium, a oba mogą korzystać z jednego konta.

    > [!NOTE]
    > Operacje we/wy w ramach magazynu w warstwach Standardowa i Premium są obliczane na poziomie maszyny wirtualnej, a nie na poziomie dysku. Standardowa maszyna wirtualna może obsłużyć do 500 operacji we/wy na dysku. Jeśli liczba IOPS dysku jest większa niż 500, potrzebna jest Usługa Premium Storage. Jeśli liczba operacji we/wy na sekundę dla dysku jest większa niż 500, ale liczby IOPS dla wszystkich dysków maszyny wirtualnej są w ramach standardowych limitów maszyn wirtualnych platformy Azure, planista wybiera standardową maszynę wirtualną, a nie z serii DS lub GS. (Limity maszyn wirtualnych platformy Azure to rozmiar maszyn wirtualnych, liczba dysków, liczba kart, procesor CPU i pamięć). Musisz ręcznie zaktualizować mapowanie komórki rozmiaru platformy Azure przy użyciu odpowiedniej maszyny wirtualnej z serii DS lub GS.


Po wprowadzeniu wszystkich informacji wybierz pozycję **Prześlij dane do narzędzia planisty** , aby otworzyć planista wydajności. Obciążenia są wyróżnione, aby pokazać, czy kwalifikują się do ochrony.

### <a name="submit-data-in-capacity-planner"></a>Przesyłanie danych w Planista wydajności
1. Po otwarciu arkusza **planista wydajności** zostanie on wypełniony na podstawie określonych ustawień. Słowo "obciążenie" pojawia się w komórce **Źródło danych wejściowych ze źródła** , aby pokazać, że dane wejściowe są arkusza **kwalifikacji obciążeń** .

2. Jeśli chcesz wprowadzić zmiany, musisz zmodyfikować arkusz **kwalifikacji obciążeń** . Następnie wybierz ponownie polecenie **Prześlij dane do narzędzia Planner** .

   ![Zrzut ekranu przedstawiający zmodyfikowane dane wejściowe i wynikowe wyniki w arkuszu Planista wydajności.](./media/site-recovery-capacity-planner/capacity-planner.png)

## <a name="next-steps"></a>Następne kroki
[Dowiedz się, jak uruchomić](./hyper-v-deployment-planner-overview.md) narzędzie planowania pojemności.