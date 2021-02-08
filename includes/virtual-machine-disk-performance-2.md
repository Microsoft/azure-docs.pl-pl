---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 10/12/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 3c4ab8362b2a717a348a59c0baf829b61e1a8006
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2021
ms.locfileid: "99808528"
---
![Wykres przedstawiający specyfikacje D s v 3.](media/vm-disk-performance/dsv3-documentation.jpg)

- Maksymalna przepływność dysku *niebuforowanego* jest domyślnym maksymalnym limitem magazynu, który może obsłużyć maszyna wirtualna.
- Maksymalny limit przepływności magazynu w *pamięci podręcznej* jest oddzielnym limitem po włączeniu buforowania hosta.

Buforowanie hostów działa przez umieszczenie magazynu bliżej maszyny wirtualnej, która może być szybko zapisywana lub odczytana. Ilość miejsca dostępnego dla maszyny wirtualnej na potrzeby buforowania hosta znajduje się w dokumentacji. Na przykład można zobaczyć, że Standard_D8s_v3 zawiera 200 GiB magazynu pamięci podręcznej.

Pamięć podręczną hosta można włączyć podczas tworzenia maszyny wirtualnej i dołączania dysków. Możesz również włączyć i wyłączyć buforowanie hosta na dyskach na istniejącej maszynie wirtualnej.

![Zrzut ekranu przedstawiający pamięć podręczną hosta.](media/vm-disk-performance/host-caching.jpg)

Pamięć podręczną hosta można dostosować w taki sposób, aby odpowiadała wymaganiom obciążeń dla każdego dysku. Pamięć podręczną hosta można ustawić na:

- **Tylko do odczytu**: dla obciążeń, które wykonują tylko operacje odczytu
- **Odczyt/zapis**: dla obciążeń, które wykonują bilans operacji odczytu i zapisu

Jeśli obciążenie nie jest zgodne z żadnym z tych wzorców, nie zaleca się używania buforowania hosta.

Uruchommy kilka przykładów różnych ustawień pamięci podręcznej hosta, aby zobaczyć, jak wpłynie to na przepływ danych i wydajność. W pierwszym przykładzie zawarto informacje na temat tego, co się dzieje z żądaniami we/wy, gdy ustawienie buforowania hosta ma wartość **tylko do odczytu**.

**Instalator**

- Standardowa_D8s_v3
  - Buforowane operacje we/wy: 16 000
  - Liczba IOPS niebuforowanych w pamięci podręcznej: 12 800
- Dysk danych P30
  - OPERACJE WE/WY: 5 000
  - Buforowanie hosta: **tylko do odczytu**

Po przeczytaniu i wybraniu żądanych danych w pamięci podręcznej pamięć podręczna zwraca żądane dane. Nie ma potrzeby odczytywania z dysku. Ten odczyt jest liczony do limitów pamięci podręcznej maszyny wirtualnej.

![Diagram przedstawiający trafienie odczytu odczytywania pamięci podręcznej hosta.](media/vm-disk-performance/host-caching-read-hit.jpg)

Gdy odczyt jest wykonywany i wymagane dane *nie* są dostępne w pamięci podręcznej, żądanie odczytu jest przekazywane do dysku. Następnie dysk umieszcza go w pamięci podręcznej i na maszynie wirtualnej. Ten odczyt jest liczony jako limit braku pamięci podręcznej maszyny wirtualnej i limit pamięci podręcznej maszyny wirtualnej.

![Diagram przedstawiający chybień odczytu pamięci podręcznej odczytu.](media/vm-disk-performance/host-caching-read-miss.jpg)

Po wykonaniu zapisu zapis musi być zapisany w pamięci podręcznej i na dysku, zanim zostanie uznany za zakończony. Ten zapis jest liczony do limitu braku pamięci podręcznej maszyny wirtualnej i buforowanego limitu maszyny wirtualnej.

![Diagram przedstawiający zapis pamięci podręcznej odczytu hosta.](media/vm-disk-performance/host-caching-write.jpg)

Kolejne Przyjrzyjmy się, co się dzieje z żądaniami we/wy, gdy ustawienie pamięci podręcznej hosta jest ustawione na **Odczyt/zapis**.

**Instalator**

- Standardowa_D8s_v3
  - Buforowane operacje we/wy: 16 000
  - Liczba IOPS niebuforowanych w pamięci podręcznej: 12 800
- Dysk danych P30
  - OPERACJE WE/WY: 5 000
  - Buforowanie hosta: **Odczyt/zapis**

Odczyt jest obsługiwany tak samo jak w przypadku tylko do odczytu. Operacje zapisu są takie same, jak w przypadku buforowania odczytu i zapisu. Podczas zapisu w pamięci podręcznej hosta jest ustawiony na wartość **Odczyt/zapis**, tylko zapis musi być zapisany w buforze hosta, aby można go było uznać za zakończony. Zapis jest następnie opóźnieniem zapisywana na dysku jako proces w tle. Oznacza to, że zapis jest liczony w kierunku buforowanej operacji we/wy, gdy zostanie zapisany w pamięci podręcznej. Gdy opóźnieniem jest zapisywana na dysku, liczy się do niebuforowanej operacji we/wy.

![Diagram przedstawiający zapisywanie w pamięci podręcznej hosta odczytu/zapisu.](media/vm-disk-performance/host-caching-read-write.jpg)

Kontynuujmy pracę z naszą Standard_D8s_v3ą maszyną wirtualną. Z tego momentu zostanie włączone buforowanie hosta na dyskach. Ponadto limit liczby operacji we/wy maszyny wirtualnej to 16 000 IOPS. Dołączone do maszyny wirtualnej to trzy bazowe dyski P30, które mogą obsłużyć 5 000 operacji we/wy na sekundę.

**Instalator**

- Standardowa_D8s_v3
  - Buforowane operacje we/wy: 16 000
  - Liczba IOPS niebuforowanych w pamięci podręcznej: 12 800
- Dysk systemu operacyjnego P30
  - OPERACJE WE/WY: 5 000
  - Buforowanie hosta: **Odczyt/zapis**
- Dwa dyski z danymi P30 × 2
  - OPERACJE WE/WY: 5 000
  - Buforowanie hosta: **Odczyt/zapis**

![Diagram przedstawiający przykład buforowania hosta.](media/vm-disk-performance/host-caching-example-without-remote.jpg)

Aplikacja używa Standard_D8s_v3 maszyny wirtualnej z włączonym buforowaniem. Wysyła żądanie do 15 000 operacji we/wy na sekundę. Żądania są podzielone na 5 000 IOPS do każdego dołączonego dysku podstawowego. Nie ma żadnego pułapu wydajności.

## <a name="combined-uncached-and-cached-limits"></a>Połączone limity niebuforowane i pamięci podręcznej

Limity pamięci podręcznej maszyny wirtualnej są oddzielone od limitów w pamięci podręcznej. Oznacza to, że można włączyć buforowanie hosta na dyskach dołączonych do maszyny wirtualnej bez włączania buforowania hosta na innych dyskach. Ta konfiguracja umożliwia maszynom wirtualnym pobieranie łącznej wartości we/wy magazynu w pamięci podręcznej i limitu braku pamięci podręcznej.

Uruchommy przykład, aby pomóc zrozumieć, jak te limity współpracują ze sobą. Będziemy kontynuować pracę z załączoną konfiguracją Standard_D8s_v3 maszyny wirtualnej i dysków w warstwie Premium.

**Instalator**

- Standardowa_D8s_v3
  - Buforowane operacje we/wy: 16 000
  - Liczba IOPS niebuforowanych w pamięci podręcznej: 12 800
- Dysk systemu operacyjnego P30
  - OPERACJE WE/WY: 5 000
  - Buforowanie hosta: **Odczyt/zapis**
- Dwa dyski z danymi P30 × 2
  - OPERACJE WE/WY: 5 000
  - Buforowanie hosta: **Odczyt/zapis**
- Dwa dyski z danymi P30 × 2
  - OPERACJE WE/WY: 5 000
  - Buforowanie hosta: **wyłączone**

![Diagram przedstawiający przykład buforowania hosta z magazynem zdalnym.](media/vm-disk-performance/host-caching-example-with-remote.jpg)

W takim przypadku aplikacja uruchomiona na Standard_D8s_v3 maszynie wirtualnej wykonuje żądanie do 25 000 operacji we/wy na sekundę. Żądanie jest podzielone na 5 000 IOPS dla każdego z dołączonych dysków. Trzy dyski używają buforowania hosta, a dwa dyski nie używają buforowania hosta.

- Ponieważ trzy dyski używające buforowania hosta znajdują się w pamięci podręcznej o limicie 16 000, te żądania zostały pomyślnie zakończone. Nie występuje żaden pułap wydajności magazynu.
- Ponieważ dwa dyski, które nie używają buforowania hosta, znajdują się w granicach niebuforowanych 12 800, te żądania również zostały pomyślnie ukończone. Nie ma żadnego pułapek.

## <a name="disk-performance-metrics"></a>Metryki wydajności dysku

Mamy metryki na platformie Azure, które zapewniają wgląd w sposób działania maszyn wirtualnych i dysków. Te metryki można przeglądać za pomocą Azure Portal. Mogą być również pobierane za poorednictwem wywołania interfejsu API. Metryki są obliczane w odstępach jednej minuty. Następujące metryki są dostępne w celu uzyskania szczegółowych informacji na temat maszyn wirtualnych i operacji we/wy na dysku, a także wydajności przepływności:

- **Głębokość kolejki dysku systemu operacyjnego**: liczba bieżących oczekujących żądań we/wy, które oczekują na odczytanie lub zapis na dysku systemu operacyjnego.
- **Bajty odczytu z dysku systemu operacyjnego/s**: liczba bajtów odczytanych w drugim z dysku systemu operacyjnego.
- **Operacje odczytu z dysku systemu operacyjnego/s**: liczba operacji wejściowych, które są odczytywane w drugim z dysku systemu operacyjnego.
- **Bajty zapisu na dysku systemu operacyjnego/s**: liczba bajtów zapisanych w drugim z dysku systemu operacyjnego.
- **Operacje zapisu na dysku systemu operacyjnego/s**: liczba operacji wyjściowych, które są zapisywane w drugim z dysku systemu operacyjnego.
- **Głębokość kolejki dysku danych**: liczba bieżących oczekujących żądań we/wy, które oczekują na odczyt lub zapis na dyskach danych.
- **Bajty odczytu z dysku danych/s**: liczba bajtów odczytanych w s z dysku danych.
- **Operacje odczytu z dysku danych/s**: liczba operacji wejściowych, które są odczytywane w s z dysku danych.
- **Bajty zapisu na dysku danych/s**: liczba bajtów zapisanych sekundowo z dysków danych.
- **Operacje zapisu na dysku danych/s**: liczba operacji wyjściowych, które są zapisywane w s z dysku danych.
- **Bajty odczytu dysku/s**: całkowita liczba bajtów odczytywanych w drugim ze wszystkich dysków dołączonych do maszyny wirtualnej.
- **Operacje odczytu z dysku/s**: liczba operacji wejścia, które są odczytywane w sekundę ze wszystkich dysków dołączonych do maszyny wirtualnej.
- **Bajty zapisu dysku/s**: liczba bajtów napisanych w drugim ze wszystkich dysków dołączonych do maszyny wirtualnej.
- **Operacje zapisu na dysku/s**: liczba operacji wyjściowych, które są zapisywane w drugim ze wszystkich dysków dołączonych do maszyny wirtualnej.

## <a name="storage-io-utilization-metrics"></a>Metryki wykorzystania operacji we/wy magazynu
Następujące metryki pomagają zdiagnozować wąskie gardło w maszynie wirtualnej i w kombinacji dysków. Te metryki są dostępne tylko w przypadku korzystania z maszyny wirtualnej z włączoną obsługą Premium. Te metryki są dostępne dla wszystkich typów dysków z wyjątkiem Ultra. 

Metryki pomagające zdiagnozować limitów operacji we/wy dysku:

- **Procent zużywanych operacji we/wy dysku danych**: procent obliczony przez operacje we/wy dysku danych wykonanych przez operacje we/wy na dysku danych zainicjowanych. Jeśli ta kwota wynosi 100%, aplikacja jest uruchomiona w ramach limitu liczby operacji we/wy na dysku danych.
- **Procent zużywanej przepustowości dysku danych**: procent obliczony przez przepływność dysku danych w ramach przepływności dysku danych z zainicjowaną obsługą. Jeśli ta kwota wynosi 100%, aplikacja jest uruchomiona we/wy z ograniczenia przepustowości dysku danych.
- **Procent zużytych operacji we/wy dysku systemu operacyjnego**: procent obliczony przez operacje we/wy dysku systemu operacyjnego wykonany przez operacje we/wy na dysku systemu operacyjnego. Jeśli ta kwota ma wartość 100%, uruchomiona aplikacja ma limit operacji we/wy na dysku systemu operacyjnego.
- **Procent zużytej przepustowości dysku systemu operacyjnego**: wartość procentowa obliczona przez przepływność dysku systemu operacyjnego została zakończona przez zainicjowaną przepływność dysku systemu operacyjnego. Jeśli ta kwota wynosi 100%, aplikacja jest uruchomiona w ramach limitu przepustowości dysku systemu operacyjnego.

Metryki pomagające zdiagnozować limitów operacji we/wy maszyny wirtualnej:

- **Procent zużytych operacji we/wy w pamięci podręcznej maszyny** wirtualnej: procent obliczony przez łączną liczbę IOPS zakończonych przez maksymalną liczbę IOPS w pamięci podręcznej Jeśli ta kwota wynosi od 100%, aplikacja jest uruchomiona we/wy ograniczonego limitu operacji wejścia/wyjścia pamięci maszyny wirtualnej.
- **Procent zajętej pamięci podręcznej maszyny wirtualnej**: procent obliczony przez łączną przepływność dysku ukończoną przez maksymalną przepływność maszyny wirtualnej w pamięci podręcznej. Jeśli ta kwota wynosi 100%, aplikacja jest uruchomiona we/wy ograniczonego limitu przepustowości w pamięci podręcznej maszyny wirtualnej.
- **Procent zużytych operacji we/wy pamięci podręcznej maszyny** wirtualnej: procent obliczony przez łączną liczbę operacji we/wy na maszynach wirtualnych zakończonych maksymalną liczbą IOPS niebuforowanej maszyny wirtualnej. Jeśli ta kwota wynosi od 100%, aplikacja jest uruchomiona w ramach operacji we/wy z limitu liczby IOPS niebuforowanej maszyny wirtualnej.
- **Procent wykorzystania przepustowości niebuforowanej przez maszynę** wirtualną: procent obliczony przez łączną przepływność dysku maszyny wirtualnej, która została zakończona przez maksymalną zainicjowaną przepływność maszyny wirtualnej. Jeśli ta kwota wynosi 100%, aplikacja jest uruchomiona w ramach operacji we/wy ograniczonej do limitu przepustowości niebuforowanej maszyny wirtualnej.

## <a name="storage-io-utilization-metrics-example"></a>Przykład metryk użycia operacji we/wy magazynu

Uruchommy Przykładowo, jak korzystać z nowych metryk użycia operacji we/wy magazynu, aby pomóc nam w debugowaniu, gdzie wąskie gardło w naszym systemie. Konfiguracja systemu jest taka sama jak w poprzednim przykładzie, z wyjątkiem tego, że dołączony dysk systemu operacyjnego *nie* jest buforowany.

**Instalator**

- Standardowa_D8s_v3
  - Buforowane operacje we/wy: 16 000
  - Liczba IOPS niebuforowanych w pamięci podręcznej: 12 800
- Dysk systemu operacyjnego P30
  - OPERACJE WE/WY: 5 000
  - Buforowanie hosta: **wyłączone**
- Dwa dyski z danymi P30 × 2
  - OPERACJE WE/WY: 5 000
  - Buforowanie hosta: **Odczyt/zapis**
- Dwa dyski z danymi P30 × 2
  - OPERACJE WE/WY: 5 000
  - Buforowanie hosta: **wyłączone**
