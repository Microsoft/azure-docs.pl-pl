---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 4/05/2021
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: 57d14ae6e6da7cfa883f1aed74cce390c0185d98
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491689"
---
Szybkość i szybkość powodzeń danego uruchomienia RoboCopy będą zależeć od kilku czynników:

* Liczba operacji we/wy na magazynie źródłowym i docelowym
* dostępna przepustowość sieci między źródłowym i docelowym
* możliwość szybkiego przetwarzania plików i folderów w przestrzeni nazw
* Liczba zmian między RoboCopy uruchomieniami


### <a name="iops-and-bandwidth-considerations"></a>Informacje o liczbie IOPS i przepustowości

W tej kategorii należy wziąć pod uwagę możliwości **magazynu źródłowego**, **magazynu docelowego** i **sieci** łączącej się z nimi. Maksymalna możliwa przepływność jest określana na podstawie najwolniejszych z tych trzech składników. Upewnij się, że infrastruktura sieciowa jest skonfigurowana tak, aby obsługiwała optymalne szybkości transferu do swoich najlepszych umiejętności.

> [!CAUTION]
> Podczas kopiowania tak szybko, jak to możliwe jest często najbardziej trudniejsze, rozważ użycie sieci lokalnej i urządzenia NAS dla innych, często ważnych zadań dla działalności biznesowej.

Kopiowanie tak szybko, jak to możliwe może być niepożądane, gdy istnieje ryzyko, że migracja może monopolize dostępne zasoby.

* Należy wziąć pod uwagę, kiedy w środowisku najlepiej jest uruchamiać migracje: w ciągu dnia, godziny lub w weekendy.
* Należy również rozważyć ustawienia QoS sieci na serwerze z systemem Windows, aby ograniczyć szybkość RoboCopy.
* Unikaj niepotrzebnych zadań dla narzędzi migracji.

RobCopy może wstawiać opóźnienia między pakietami, określając `/IPG:n` przełącznik, gdzie `n` jest mierzony w milisekundach między pakietami Robocopy. Użycie tego przełącznika może pomóc w uniknięciu monopolization zasobów na urządzeniach z ograniczeniami we/wy i natłoku łączy sieciowych.

`/IPG:n` nie można użyć do precyzyjnego ograniczania przepustowości sieci do określonej liczby MB/s. Zamiast tego użyj funkcji QoS sieci systemu Windows Server. RoboCopy całkowicie opiera się na protokole SMB dla wszystkich potrzeb sieciowych. Użycie protokołu SMB to powód, dla którego RoboCopy nie może wpływać na przepływność sieci, ale może spowolnić korzystanie z usługi. 

Podobna linia myśli dotyczy operacji we/wy zaobserwowanej na serwerze NAS. Rozmiar klastra w woluminie NAS, rozmiary pakietów i tablica innych czynników wpływają na zaobserwowane operacje we/wy na sekundę. Wprowadzenie opóźnienia między pakietami jest często najprostszym sposobem sterowania obciążeniem serwera NAS. Przetestuj wiele wartości, na przykład od około 20 milisekund (n = 20) do wielokrotności tej liczby. Po wprowadzeniu opóźnienia możesz sprawdzić, czy inne aplikacje mogą teraz funkcjonować zgodnie z oczekiwaniami. Ta strategia optymalizacji umożliwi znalezienie optymalnej szybkości RoboCopy w danym środowisku.

### <a name="processing-speed"></a>Szybkość przetwarzania

RoboCopy przejdzie do obszaru nazw, do którego się wskazywał, i Oceń każdy plik i folder do kopiowania. Każdy plik zostanie oszacowany podczas kopiowania wstępnego i podczas przechwytywania kopii. Przykładowo powtórzone uruchomienia programu RoboCopy/MIR w odniesieniu do tych samych źródłowej i docelowej lokalizacji przechowywania. Te powtarzające się uruchomienia są przydatne w celu zminimalizowania przestojów użytkowników i aplikacji oraz zwiększenia ogólnej częstotliwości powodzeń migracji plików.

Często domyślnie rozważamy wykorzystanie przepustowości jako czynnik najbardziej ograniczającego w migracji — i to może być prawdziwe. Jednak możliwość wyliczenia przestrzeni nazw może mieć wpływ na łączny czas kopiowania jeszcze więcej dla większych przestrzeni nazw o mniejszych plikach. Należy wziąć pod uwagę, że kopiowanie 1 TiB małych plików zajmie znacznie dłużej niż kopiowanie 1 TiB mniej niż więcej plików. Przy założeniu, że wszystkie pozostałe zmienne pozostają takie same.

Przyczyną tej różnicy jest moc obliczeniowa, która jest wymagana do przeszukania w przestrzeni nazw. RoboCopy obsługuje kopie wielowątkowe za pomocą parametru, `/MT:n` gdzie n oznacza liczbę wątków procesora. Dlatego w przypadku inicjowania obsługi maszyny przeznaczonej dla RoboCopy należy wziąć pod uwagę liczbę rdzeni procesora i ich powiązania z liczbą wątków, które zapewnia. Najczęstsze są dwa wątki na rdzeń. Liczba rdzeni i wątków maszyny jest ważnym punktem danych, aby zdecydować, jakie wartości wielowątkowości należy `/MT:n` określić. Należy również wziąć pod uwagę liczbę zadań RoboCopy, które planujesz uruchamiać równolegle na danym komputerze.

Więcej wątków skopiuje nasz przykład 1-TiB z małych plików znacznie szybciej niż w przypadku mniejszej liczby wątków. W tym samym czasie, dodatkowe inwestycje zasobów na nasze 1 TiB większych plików mogą nie przynieść korzyści proporcjonalnie. Duża liczba wątków podejmie próbę skopiowania większej liczby dużych plików za pośrednictwem sieci jednocześnie. Ta dodatkowa aktywność sieciowa zwiększa prawdopodobieństwo uzyskania ograniczenia przepustowości lub operacji wejścia/wyjścia magazynu.

### <a name="avoid-unnecessary-work"></a>Unikaj niepotrzebnej pracy

Unikaj zmian w dużej skali w przestrzeni nazw. Na przykład przeniesienie plików między katalogami, zmiana właściwości na dużą skalę lub zmiana uprawnień (listy ACL systemu plików NTFS). Szczególnie zmiany listy ACL mogą mieć duży wpływ, ponieważ często mają efekt kaskadowy zmiany w plikach niższych w hierarchii folderów. Konsekwencje mogą być następujące:

* rozszerzony czas wykonywania zadania RoboCopy, ponieważ każdy plik i folder, do którego ma wpływ zmiana listy ACL, musi zostać zaktualizowany
* ponowne użycie przeniesionych wcześniej danych może wymagać skopiowania. Na przykład należy skopiować więcej danych, gdy struktury folderów zmienią się, gdy pliki zostały już wcześniej skopiowane. Zadanie RoboCopy nie może odwrócić zmiany przestrzeni nazw. Następne zadanie musi przeczyścić wcześniej przetransportowane pliki do starej struktury folderów i ponownie przekazać pliki w nowej strukturze folderów.

Innym ważnym aspektem jest użycie narzędzia RoboCopy efektywnie. Przy użyciu zalecanego skryptu RoboCopy utworzysz i zapiszesz plik dziennika pod kątem błędów. Błędy kopiowania mogą wystąpić — to jest normalne. Te błędy często sprawiają, że konieczne jest uruchomienie wielu rund narzędzia kopiowania, takiego jak RoboCopy. Początkowy przebieg, powiedzmy od elementu NAS do DataBox lub serwera do udziału plików platformy Azure. I co najmniej jeden dodatkowy przebieg z przełącznikiem/MIR do przechwytywania i ponawiania prób plików, które nie zostały skopiowane.

Należy przygotować się do uruchamiania wielu rund RoboCopy dla danego zakresu przestrzeni nazw. Kolejne uruchomienia zakończą się szybciej, ponieważ mają mniej do skopiowania, ale są ograniczone przez szybkość przetwarzania przestrzeni nazw. W przypadku uruchamiania wielu operacji zaokrąglania można przyspieszyć poszczególne operacje, nie mając RoboCopy, co nie ma na celu zbyt trudne do skopiowania wszystkiego w danym przebiegu. Te przełączniki RoboCopy mogą mieć znaczącą różnicę:

* `/R:n` n = częstotliwość ponawiania próby skopiowania pliku zakończonego niepowodzeniem i 
* `/W:n` n = liczba sekund oczekiwania między ponownymi próbami

`/R:5 /W:5` jest odpowiednim ustawieniem, które można dostosować do własnych potrzeb. W tym przykładzie plik, który uległ awarii, zostanie ponowiony pięć razy, z pięciu sekund czasu oczekiwania między ponownymi próbami. Jeśli skopiowanie pliku nadal nie powiedzie się, następne zadanie RoboCopy zostanie ponowione. Często pliki, które zakończyły się niepowodzeniem, ponieważ są używane lub problemy z limitem czasu mogą zostać pomyślnie skopiowane w ten sposób.
   
