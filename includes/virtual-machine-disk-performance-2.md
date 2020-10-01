---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 09/25/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: e5a6dae98e786bf55dc17d8fabe42f84e9927442
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91606054"
---
![Dokumentacja Dsv3](media/vm-disk-performance/dsv3-documentation.jpg)

Maksymalna przepływność dysku **niebuforowanego** jest domyślnym maksymalnym limitem magazynu, który może obsłużyć maszyna wirtualna. Maksymalny limit przepływności magazynu w **pamięci podręcznej** jest oddzielnym limitem po włączeniu buforowania hosta. Buforowanie hostów działa przez umieszczenie magazynu bliżej maszyny wirtualnej, która może być szybko zapisywana lub odczytana. Ilość miejsca dostępnego dla maszyny wirtualnej na potrzeby buforowania hosta znajduje się w dokumentacji. Na przykład można zobaczyć, że Standard_D8s_v3 zawiera 200 GiB magazynu pamięci podręcznej.

Włączenie buforowania hosta można wykonać podczas tworzenia maszyny wirtualnej i dołączania dysków. Możesz również dostosować, aby włączyć lub wyłączyć buforowanie dysków na istniejącej maszynie wirtualnej.

![Buforowanie hosta](media/vm-disk-performance/host-caching.jpg)

Pamięć podręczną hosta można dostosować w taki sposób, aby odpowiadała wymaganiom obciążeń dla każdego dysku. Pamięć podręczną hosta można ustawić jako tylko do odczytu dla obciążeń, które wykonują operacje odczytu i zapisu w przypadku obciążeń, które wykonują bilans operacji odczytu i zapisu. Jeśli obciążenie nie jest zgodne z żadnym z tych wzorców, nie zalecamy używania buforowania hosta. 

Uruchommy kilka przykładów różnych ustawień pamięci podręcznej hosta i zobacz, jak ma to wpływ na przepływ danych i wydajność. W pierwszym przykładzie Przyjrzyjmy się temu, co się dzieje z żądaniami we/wy, gdy ustawienie buforowania hosta ma wartość **tylko do odczytu**.

Konfiguracja:
- Standardowa_D8s_v3 
    - Buforowane operacje we/wy: 16 000
    - Liczba IOPS niebuforowanych w pamięci podręcznej: 12 800
- Dysk danych P30 
    - OPERACJE WE/WY: 5 000
    - **Buforowanie hosta: tylko do odczytu** 

Po przeczytaniu i wybraniu żądanych danych w pamięci podręcznej pamięć podręczna zwraca żądane dane i nie ma potrzeby odczytywania ich z dysku. Ten odczyt jest liczony do buforowanych limitów maszyn wirtualnych.

![Trafienie odczytu odczytu pamięci podręcznej hosta](media/vm-disk-performance/host-caching-read-hit.jpg)

Po przeczytaniu i pożądanych danych **nie** jest dostępna w pamięci podręcznej, żądanie odczytu jest następnie przekazywane do dysku, który następnie umieszcza go w pamięci podręcznej i na maszynie wirtualnej. Odczyt ten jest uwzględniany w odniesieniu do limitu braku pamięci podręcznej maszyny wirtualnej i buforowanego limitu maszyny wirtualnej.

![Odczytaj Chybienia odczytu pamięci podręcznej hosta](media/vm-disk-performance/host-caching-read-miss.jpg)

Po wykonaniu zapisu zapis musi być zapisany w pamięci podręcznej i na dysku, zanim zostanie uznany za zakończony. Ten zapis jest liczony do limitu braku pamięci podręcznej maszyny wirtualnej i buforowanego limitu maszyny wirtualnej.

![Odczytaj zapis pamięci podręcznej hosta](media/vm-disk-performance/host-caching-write.jpg)

W tym następnym przykładzie Przyjrzyjmy się co się dzieje z żądaniami we/wy, gdy ustawienie pamięci podręcznej hosta jest ustawione na **Odczyt/zapis**.

Konfiguracja:
- Standardowa_D8s_v3 
    - Buforowane operacje we/wy: 16 000
    - Liczba IOPS niebuforowanych w pamięci podręcznej: 12 800
- Dysk danych P30 
    - OPERACJE WE/WY: 5 000
    - **Buforowanie hosta: odczyt/zapis** 

Odczyty są obsługiwane dokładnie tak samo jak w trybie tylko do odczytu, a zapisy są inne, które różnią się od buforowania odczytu i zapisu. Podczas pisania z buforowaniem hosta ustawionym na odczyt/zapis, tylko zapis musi być zapisany w pamięci podręcznej hosta, aby można go było uznać za zakończony. Zapis jest następnie opóźnieniem zapisywana na dysku jako proces w tle. Oznacza to, że zapisy będą zliczane do buforowania operacji we/wy, gdy zostanie on zapisany w pamięci podręcznej i gdy zostanie on opóźnieniem zapisany na dysku, który będzie uwzględniał Niebuforowane operacje we/wy.

![Zapis pamięci podręcznej odczytu/zapisu](media/vm-disk-performance/host-caching-read-write.jpg)

Przejdźmy na przykład z naszą Standard_D8s_v3ą maszyną wirtualną. Z tego momentu zostanie włączone buforowanie hosta na dyskach, a teraz limit operacji we/wy maszyny wirtualnej to 16 000 IOPS. Dołączone do maszyny wirtualnej to trzy bazowe dyski P30, które mogą obsłużyć 5 000 operacji we/wy na sekundę.

Konfiguracja:
- Standardowa_D8s_v3 
    - Buforowane operacje we/wy: 16 000
    - Liczba IOPS niebuforowanych w pamięci podręcznej: 12 800
- Dysk systemu operacyjnego P30 
    - OPERACJE WE/WY: 5 000
    - Buforowanie hosta: odczyt/zapis 
- 2 P30 dyski danych
    - OPERACJE WE/WY: 5 000
    - Buforowanie hosta: odczyt/zapis

![Przykład buforowania hosta](media/vm-disk-performance/host-caching-example-without-remote.jpg)

Teraz aplikacja używająca tej Standard_D8s_v3 maszynę wirtualną z włączoną buforowaniem wykonuje żądanie do 15 000 operacji we/wy na sekundę. Te żądania są podzielone na 5 000 operacji wejścia/wyjścia na sekundę do każdego dołączonego dysku podstawowego i nie ma żadnego ograniczenia wydajności.

## <a name="combined-uncached-and-cached-limits"></a>Połączone limity niebuforowane i pamięci podręcznej

Limity buforowania maszyny wirtualnej są oddzielone od limitów pamięci podręcznych. Oznacza to, że można włączyć buforowanie hosta na dyskach dołączonych do maszyny wirtualnej, a także nie włączać buforowania hosta na innych dyskach, aby umożliwić maszynom wirtualnym pobieranie łącznej wartości we/wy magazynu w pamięci podręcznej oraz limitu niebuforowanego. Uruchommy na przykład, aby pomóc zwiększyć, jak te limity współpracują ze sobą, i będziemy kontynuować pracę z załączoną konfiguracją Standard_D8s_v3 maszyny wirtualnej i dysków w warstwie Premium.

Konfiguracja:
- Standardowa_D8s_v3 
    - Buforowane operacje we/wy: 16 000
    - Liczba IOPS niebuforowanych w pamięci podręcznej: 12 800
- Dysk systemu operacyjnego P30 
    - OPERACJE WE/WY: 5 000
    - Buforowanie hosta: odczyt/zapis
- 2 P30 dyski danych X 2
    - OPERACJE WE/WY: 5 000
    - Buforowanie hosta: odczyt/zapis
- 2 P30 dyski danych X 2
    - OPERACJE WE/WY: 5 000
    - Buforowanie hosta: wyłączone

![Przykład buforowania hosta z magazynem zdalnym](media/vm-disk-performance/host-caching-example-with-remote.jpg)

Teraz aplikacja uruchomiona na Standard_D8s_v3 maszynie wirtualnej z programem wysyła żądanie do 25 000 operacji we/wy na sekundę. To żądanie jest podzielone na 5 000 IOPS dla każdego dysku bazowego, w którym 3 z tych dysków używa buforowania hosta i 2 dysków nie są. Ponieważ 3 użycie buforowania hosta należy do buforowanych limitów 16 000, te żądania zostały pomyślnie zakończone i nie ma żadnego limitu wydajności magazynu. Ponadto, ponieważ 2 dyski, które nie używają buforowania hosta, znajdują się w granicach niebuforowanych 12 800, te żądania również zostały wykonane pomyślnie i nie występują żadne pułapy.

## <a name="metrics-for-disk-performance"></a>Metryki dotyczące wydajności dysków
Mamy metryki na platformie Azure, które zapewniają wgląd w sposób działania maszyn wirtualnych i dysków. Te metryki mogą być wyświetlane wizualnie za pomocą Azure Portal lub mogą być pobierane za pomocą wywołania interfejsu API. Metryki są obliczane w odstępach jednej minuty. Następujące metryki są dostępne w celu uzyskania wglądu w dane dotyczące maszyn wirtualnych i operacji we/wy na dysku oraz wydajności przepływności:
- **Głębokość kolejki dysku systemu operacyjnego** — liczba bieżących oczekujących żądań we/wy, które oczekują na odczytanie lub zapis na dysku systemu operacyjnego.
- **Bajty odczytu z dysku systemu operacyjnego/s** — liczba bajtów odczytywanych w drugim z dysku systemu operacyjnego.
- **Operacje odczytu z dysku systemu operacyjnego/s** — liczba operacji wejścia, które są odczytywane w drugim z dysku systemu operacyjnego.
- **Bajty zapisu dysku systemu operacyjnego/s** — liczba bajtów napisanych w drugim z dysku systemu operacyjnego.
- **Operacje zapisu na dysku systemu operacyjnego/s** — liczba operacji wyjściowych, które są zapisywane w drugim z dysku systemu operacyjnego.
- **Głębokość kolejki dysku danych** — liczba bieżących oczekujących żądań we/wy, które oczekują na odczyt lub zapis na dyskach danych.
- **Bajty odczytu dysku danych/s** — liczba bajtów odczytanych w s z dysku danych.
- **Operacje odczytu z dysku danych/s** — liczba operacji wejścia, które są odczytywane w s z dysku danych.
- **Bajty zapisu na dysku danych/s** — liczba bajtów napisanych w drugim z dysków danych.
- **Operacje zapisu na dysku danych/s** — liczba operacji wyjściowych, które zostały zapisane w drugim z dysków danych.
- **Bajty odczytu dysku/s** — łączna liczba bajtów, które są odczytywane w sekundę ze wszystkich dysków dołączonych do maszyny wirtualnej.
- **Operacje odczytu z dysku/s** — liczba operacji wejścia, które są odczytywane w sekundę ze wszystkich dysków dołączonych do maszyny wirtualnej.
- **Bajty zapisu dysku/s** — liczba bajtów napisanych w drugim ze wszystkich dysków dołączonych do maszyny wirtualnej.
- **Operacje zapisu na dysku/s** — liczba operacji wyjściowych napisanych w drugim ze wszystkich dysków dołączonych do maszyny wirtualnej.

## <a name="storage-io-utilization-metrics"></a>Metryki wykorzystania operacji we/wy magazynu
Metryki pomagające zdiagnozować limitów operacji we/wy dysku:
- **Procent zużywanych operacji we/wy dysku danych** — procent obliczony przez operacje we/wy na dysku danych wykonanych przez operacje we/wy na dysku danych zainicjowanych. Jeśli ta kwota wynosi 100%, aplikacja będzie działać w ramach operacji we/wy ograniczonej do limitu liczby IOPS dysku danych.
- **Procent zużywanej przepustowości dysku danych** — wartość procentowa obliczona przez przepływność dysku danych ukończona w ramach przepływności dysku danych z zainicjowaną obsługą. Jeśli ta kwota wynosi 100%, aplikacja będzie działać w ramach operacji we/wy ograniczonej do limitu przepustowości dysku danych.
- **Procent zużywanych** operacji we/wy dysku systemu operacyjnego — procent obliczony przez operacje we/wy dysku systemu operacyjnego zakończony przez operacje we/wy na dysku systemu operacyjnego. Jeśli ta kwota wynosi 100%, aplikacja będzie działać w ramach limitu liczby operacji we/wy na dysku systemu operacyjnego.
- **Procent zużytej przepustowości dysku systemu operacyjnego** — wartość procentowa obliczona przez przepływność dysku systemu operacyjnego zakończona przez zainicjowaną przepływność dysku systemu operacyjnego. Jeśli ta kwota wynosi 100%, aplikacja będzie działać w ramach limitu przepustowości dysku systemu operacyjnego.

Metryki pomagające zdiagnozować limitów operacji we/wy maszyny wirtualnej:
- **Procent użycia buforowanej** liczby operacji we/wy w trybie failover — wartość procentowa obliczona przez łączną liczbę IOPS zakończonych w ramach maksymalnego limitu liczby IOPS pamięci w trybie Jeśli ta kwota wynosi 100%, aplikacja będzie działać w ramach operacji we/wy ograniczonego limitu liczby IOPS w pamięci podręcznej maszyny wirtualnej.
- **Procent wykorzystania przepustowości w pamięci podręcznej maszyny** wirtualnej — wartość procentowa obliczona przez łączną przepływność dysku zakończyła się w maksymalnej przepływności pamięci podręcznej. Jeśli ta kwota wynosi 100%, aplikacja będzie działać w ramach operacji we/wy ograniczonej do limitu przepustowości pamięci podręcznej maszyny wirtualnej.
- **Procent użycia operacji wejścia/wyjścia w pamięci podręcznej maszyny** wirtualnej — wartość procentowa obliczona przez łączną liczbę IOPS na maszynę wirtualną zakończyła się przez maksymalną wartość limitu liczby IOPS niebuforowanej maszyny wirtualnej. Jeśli ta kwota wynosi 100%, aplikacja będzie działać w ramach operacji we/wy ograniczonego limitu liczby IOPS niebuforowanej maszyny wirtualnej.
- **Procent wykorzystania przepustowości niebuforowanej przez maszynę** wirtualną — wartość procentowa obliczona przez łączną przepływność dysku na maszynie wirtualnej została zakończona przez maksymalną przepływność maszyny wirtualnej, która została zainicjowana. Jeśli ta kwota wynosi 100%, aplikacja będzie działać w ramach operacji we/wy ograniczonej do limitu przepustowości dla maszyny wirtualnej.

