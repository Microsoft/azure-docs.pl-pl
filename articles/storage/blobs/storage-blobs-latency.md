---
title: Opóźnienie w usłudze BLOB Storage — Azure Storage
description: Poznaj i mierz opóźnienia dla operacji usługi BLOB Storage i Dowiedz się, jak projektować aplikacje usługi BLOB Storage w celu uzyskania małych opóźnień.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 09/05/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 78440b8150a0992bed2e2a3e597fdac8e7a1c7b0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "75749728"
---
# <a name="latency-in-blob-storage"></a>Opóźnienia w magazynie obiektów blob

Opóźnienie, czasami przywoływany jako czas odpowiedzi, to ilość czasu, jaką aplikacja musi oczekiwać na ukończenie żądania. Opóźnienie może bezpośrednio wpływać na wydajność aplikacji. Małe opóźnienia często są ważne w przypadku scenariuszy z ludźmi w pętli, takich jak przeprowadzenie transakcji karty kredytowej lub załadowanie stron sieci Web. Systemy, które muszą przetwarzać zdarzenia przychodzące przy wysokich stawkach, takie jak rejestrowanie danych telemetrycznych lub zdarzenia IoT, wymagają również małych opóźnień. W tym artykule opisano sposób zrozumienia i mierzenia opóźnienia operacji na blokowych obiektach Blob oraz sposobu projektowania aplikacji pod kątem małych opóźnień.

Usługa Azure Storage oferuje dwie różne opcje wydajności dla blokowych obiektów blob: Premium i Standard. Blokowe obiekty blob w warstwie Premium oferują znacznie niższe i bardziej spójne opóźnienia niż w przypadku standardowych blokowych obiektów BLOB za pośrednictwem dysków SSD o wysokiej wydajności. Aby uzyskać więcej informacji, zobacz **Magazyn obiektów BLOB wydajności** w warstwie Premium w [usłudze Azure Blob Storage: warstwy dostępu gorąca, chłodna i archiwalna](storage-blob-storage-tiers.md).

## <a name="about-azure-storage-latency"></a>Informacje o opóźnieniu usługi Azure Storage

Opóźnienie usługi Azure Storage jest związane ze stawkami za żądania operacji usługi Azure Storage. Stawki żądania są również znane jako operacje wejścia/wyjścia na sekundę (IOPS).

Aby obliczyć stawkę żądania, należy najpierw określić długość czasu, przez który każde żądanie trwa, a następnie obliczyć liczbę żądań, które mogą być przetwarzane na sekundę. Załóżmy na przykład, że żądanie trwa 50 milisekund (MS). Aplikacja używająca jednego wątku z jedną zaległą operacją odczytu lub zapisu powinna osiągnąć 20 operacji we/wy (1 sekundę lub 1000 MS/50 MS na żądanie). Teoretycznie, jeśli liczba wątków jest dwukrotnie równa dwa, aplikacja powinna mieć możliwość osiągnięcia 40 operacji we/wy na sekundę. Jeśli zaległe asynchroniczne operacje odczytu lub zapisu dla każdego wątku są podwójne do dwóch, aplikacja powinna mieć możliwość osiągnięcia 80 operacji we/wy na sekundę.

W tej sprawie stawki żądania nie zawsze są skalowane w sposób liniowy z powodu narzutu na klienta z harmonogramu zadań, przełączania kontekstu i tak dalej. Po stronie usługi może wystąpić zmienność opóźnienia z powodu nacisku na system Azure Storage, różnice w używanym nośniku magazynu, szum z innych obciążeń, zadań konserwacyjnych i innych czynników. Na koniec połączenie sieciowe między klientem i serwerem może wpłynąć na opóźnienie usługi Azure Storage z powodu przeciążenia, ponownego routingu lub innych zakłóceń.

Przepustowość usługi Azure Storage, określana również jako przepływność, jest związana z szybkością żądań i można ją obliczyć przez pomnożenie liczby żądań (IOPS) przez rozmiar żądania. Na przykład przy założeniu 160 żądań na sekundę, każdy 256 KiB danych powoduje przepływność 40 960 KiB na sekundę lub 40 MiB na sekundę.

## <a name="latency-metrics-for-block-blobs"></a>Metryki opóźnienia dla blokowych obiektów BLOB

Usługa Azure Storage udostępnia dwie metryki opóźnienia dla blokowych obiektów BLOB. Te metryki można wyświetlać w Azure Portal:

- **Opóźnienie (E2E)** mierzy interwał od momentu, w którym usługa Azure Storage otrzymuje pierwszy pakiet żądania do momentu otrzymania przez usługę Azure Storage potwierdzenia ostatniego pakietu odpowiedzi.

- **Opóźnienie serwera** mierzy interwał od momentu, gdy usługa Azure Storage odbierze ostatni pakiet żądania do momentu zwrócenia pierwszego pakietu odpowiedzi z usługi Azure Storage.

Na poniższej ilustracji przedstawiono **średnie pomyślne opóźnienie E2E** i **Średni czas opóźnienia serwera** dla przykładowego obciążenia wywołującego `Get Blob` operację:

![Zrzut ekranu przedstawiający metryki opóźnienia dla operacji pobierania obiektu BLOB](media/storage-blobs-latency/latency-metrics-get-blob.png)

W normalnych warunkach występuje niewielka różnica między końcem kompleksu i opóźnieniem serwera, który jest obrazem dla przykładowego obciążenia.

W przypadku przejrzenia metryk na potrzeby kompleksowych i opóźnień serwera oraz ustalenia, że opóźnienie na zakończenie jest znacznie większe niż opóźnienia serwera, zbadaj i zapoznaj się ze źródłem dodatkowego opóźnienia.

Jeśli opóźnienie kompleksowe i serwerowe są podobne, ale potrzebujesz małych opóźnień, rozważ migrację do magazynu blokowych obiektów BLOB w warstwie Premium.

## <a name="factors-influencing-latency"></a>Czynniki wpływające na opóźnienie

Głównym czynnikiem wpływającym na opóźnienie jest rozmiar operacji. Wykonanie większej liczby operacji trwa dłużej, ponieważ ilość danych przesyłanych przez sieć jest przetwarzana przez usługę Azure Storage.

Na poniższym diagramie przedstawiono łączny czas operacji dla różnych rozmiarów. W przypadku małych ilości danych interwał opóźnienia jest głównie poświęcany na obsługę żądania, a nie transfer danych. Interwał opóźnień zwiększa się tylko nieznacznie w miarę wzrostu rozmiaru operacji (oznaczony 1 na poniższym diagramie). W miarę zwiększania rozmiaru operacji jest poświęcany więcej czasu na transfer danych, dzięki czemu łączny interwał opóźnienia jest dzielony między obsługą żądań a transferem danych (oznaczonym 2 na poniższym diagramie). W przypadku większych rozmiarów operacji interwał opóźnień jest niemal wyłącznie poświęcany na transfer danych, a obsługa żądań jest w znacznym stopniu nieistotny (oznaczone jako 3 na poniższym diagramie).

![Zrzut ekranu przedstawiający łączny czas operacji według rozmiaru operacji](media/storage-blobs-latency/operation-time-size-chart.png)

Czynniki konfiguracji klientów, takie jak współbieżność i wątkowość, wpływają również na opóźnienia. Ogólna przepływność zależy od liczby żądań magazynowania w danym momencie, a także od tego, jak aplikacja obsługuje wątki. Zasoby klienta, w tym procesor CPU, pamięć, Magazyn lokalny i interfejsy sieciowe, mogą również wpływać na opóźnienia.

Przetwarzanie żądań usługi Azure Storage wymaga zasobów procesora i pamięci klienta. Jeśli klient jest pod ciśnieniem ze względu na nieelektryczną maszynę wirtualną lub kilka procesów przemijających w systemie, jest dostępnych mniej zasobów do przetwarzania żądań usługi Azure Storage. Wszelkie rywalizacje lub brak zasobów klienta spowoduje wzrost opóźnień końcowych bez wzrostu opóźnień serwera, zwiększając przerwy między dwiema metrykami.

Równie ważne jest interfejs sieciowy i potok sieciowy między klientem a magazynem platformy Azure. Sama odległość fizyczna może być istotnym czynnikiem, na przykład jeśli maszyna wirtualna klienta znajduje się w innym regionie świadczenia usługi Azure lub lokalnie. Inne czynniki, takie jak przeskoki sieci, routing usługodawców internetowych i stan Internetu, mogą mieć wpływ na ogólne opóźnienie magazynu.

Aby ocenić opóźnienia, najpierw Ustanów metryki bazowe dla danego scenariusza. Metryki linii bazowej umożliwiają oczekiwanie na zakończenie i opóźnienie serwera w kontekście środowiska aplikacji, w zależności od profilu obciążenia, ustawień konfiguracji aplikacji, zasobów klienta, potoku sieci i innych czynników. Gdy masz metryki bazowe, możesz łatwiej identyfikować nietypowe i normalne warunki. Metryki linii bazowej umożliwiają również przestrzeganie efektów zmienionych parametrów, takich jak konfiguracja aplikacji lub rozmiary maszyn wirtualnych.

## <a name="next-steps"></a>Następne kroki

- [Cele skalowalności i wydajności dla usługi BLOB Storage](scalability-targets.md)
- [Lista kontrolna wydajności i skalowalności usługi BLOB Storage](storage-performance-checklist.md)
