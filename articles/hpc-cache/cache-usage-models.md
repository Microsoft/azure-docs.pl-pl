---
title: Modele użycia pamięci podręcznej platformy Azure HPC
description: W tym artykule opisano różne modele użycia pamięci podręcznej i sposoby ich wyboru, aby ustawić buforowanie w trybie tylko do odczytu lub odczyt/zapis i kontrolować inne ustawienia buforowania.
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/08/2021
ms.author: v-erkel
ms.openlocfilehash: 856f2c15d2bd0b39212e8962a92b1df50cada29e
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103472858"
---
# <a name="understand-cache-usage-models"></a>Omówienie modeli użycia pamięci podręcznej

Modele użycia pamięci podręcznej umożliwiają dostosowanie sposobu przechowywania plików w pamięci podręcznej Azure HPC w celu przyspieszenia przepływu pracy.

## <a name="basic-file-caching-concepts"></a>Podstawowe pojęcia związane z buforowaniem plików

Buforowanie plików to sposób, w jaki usługa Azure HPC cache przyspiesza żądania klientów. Używa tych podstawowych rozwiązań:

* **Buforowanie odczytu** — pamięć podręczna Azure HPC przechowuje kopię plików, z których klienci żądają z systemu magazynu. Następnym razem, gdy klient zażąda tego samego pliku, pamięć podręczna HPC może udostępnić wersję w swojej pamięci podręcznej, zamiast pobierać ją z systemu magazynu zaplecza ponownie.

* **Buforowanie zapisu** — opcjonalnie pamięć podręczna Azure HPC może przechowywać kopię wszystkich zmienionych plików wysyłanych z komputerów klienckich. Jeśli wielu klientów wprowadzi zmiany w tym samym pliku w krótkim czasie, pamięć podręczna może zbierać wszystkie zmiany w pamięci podręcznej, zamiast zapisywać każdą zmianę indywidualnie dla systemu magazynu zaplecza.

  Po upływie określonego czasu bez zmian pamięć podręczna przenosi plik do magazynu długoterminowego.

  Jeśli buforowanie zapisu jest wyłączone, pamięć podręczna nie przechowuje zmienionego pliku i natychmiast zapisuje je w systemie magazynu zaplecza.

* **Opóźnienie zapisu** — w przypadku pamięci podręcznej z włączonym buforowaniem zapisu opóźnienie zapisu to czas, przez jaki pamięć podręczna czeka na dodatkowe zmiany plików przed przeniesieniem pliku do systemu magazynu zaplecza.

* **Weryfikacja zaplecza** — ustawienie weryfikacji zaplecza określa, jak często pamięć podręczna porównuje lokalną kopię pliku ze zdalną wersją w systemie magazynu zaplecza. Jeśli kopia zaplecza jest nowsza niż buforowana kopia, pamięć podręczna pobiera kopię zdalną i zapisuje ją na potrzeby przyszłych żądań.

  Ustawienie weryfikacji zaplecza pokazuje, *kiedy pamięć* podręczna porównuje pliki z plikami źródłowymi w magazynie zdalnym. W pamięci podręcznej platformy Azure HPC można jednak wymusić Porównanie plików przez wykonanie operacji katalogu zawierającej żądanie READDIRPLUS. READDIRPLUS to standardowy interfejs API systemu plików NFS (nazywany także rozszerzonym odczytem), który zwraca metadane katalogu, co powoduje, że pamięć podręczna będzie porównywać i aktualizować pliki.

Modele użycia wbudowane w pamięć podręczną platformy Azure HPC mają różne wartości dla tych ustawień, dzięki czemu można wybrać najlepszą kombinację dla danej sytuacji.

## <a name="choose-the-right-usage-model-for-your-workflow"></a>Wybierz odpowiedni model użycia dla przepływu pracy

Należy wybrać model użycia dla każdego używanego miejsca docelowego magazynu zainstalowanego w systemie plików NFS. Cele magazynu obiektów blob platformy Azure mają wbudowany model użycia, którego nie można dostosować.

Modele użycia pamięci podręcznej HPC umożliwiają wybranie sposobu zrównoważenia szybkiej reakcji z ryzykiem pobierania starych danych. Jeśli chcesz zoptymalizować szybkość odczytywania plików, możesz nie zadbać o to, czy pliki w pamięci podręcznej są sprawdzane względem plików zaplecza. Z drugiej strony, jeśli chcesz upewnić się, że pliki są zawsze aktualne z magazynem zdalnym, wybierz model, który sprawdza się często.

Istnieje kilka opcji:

* **Czytaj duże, rzadko** występujące zapisy — Użyj tej opcji, jeśli chcesz przyspieszyć dostęp do odczytu do plików, które są statyczne lub rzadko zmieniane.

  Ta opcja powoduje buforowanie odczytów klienta, ale nie buforuje operacji zapisu. Natychmiast przejdzie zapis do magazynu zaplecza.
  
  Pliki przechowywane w pamięci podręcznej nie są automatycznie porównywane z plikami znajdującymi się na woluminie magazynu NFS. (Przeczytaj opis weryfikacji zaplecza powyżej, aby dowiedzieć się, jak porównać je ręcznie).

  Nie należy używać tej opcji, jeśli istnieje ryzyko, że plik może być modyfikowany bezpośrednio w systemie magazynu bez wcześniejszego zapisania go w pamięci podręcznej. W takim przypadku buforowana wersja pliku nie jest zsynchronizowana z plikiem zaplecza.

* **Więcej niż 15% zapisów** — ta opcja przyspiesza zarówno wydajność odczytu, jak i zapisu. W przypadku korzystania z tej opcji Wszyscy klienci muszą uzyskać dostęp do plików za pośrednictwem pamięci podręcznej platformy Azure HPC zamiast bezpośrednio zainstalować magazyn zaplecza. Pliki w pamięci podręcznej będą miały ostatnio wprowadzone zmiany, które nie są przechowywane w zapleczu.

  W tym modelu użycia pliki w pamięci podręcznej są sprawdzane tylko względem plików w magazynie zaplecza co osiem godzin. Założono, że w pamięci podręcznej znajduje się nowsza wersja pliku. Zmodyfikowany plik w pamięci podręcznej jest zapisywana w systemie magazynu zaplecza, gdy był w pamięci podręcznej przez 20 minut<!-- an hour --> bez dodatkowych zmian.

* **Klienci zapisują w miejscu DOCELOWYM NFS, pomijając pamięć podręczną** — wybierz tę opcję, jeśli dowolni klienci w przepływie pracy zapisują dane bezpośrednio w systemie magazynu bez wcześniejszego zapisu w pamięci podręcznej lub jeśli chcesz zoptymalizować spójność danych. Pliki, które żądania klientów są buforowane (odczyt), ale wszelkie zmiany plików z klienta (zapis) nie są buforowane. Są one przenoszone bezpośrednio do systemu magazynu zaplecza.

  W tym modelu użycia pliki w pamięci podręcznej są często sprawdzane względem wersji zaplecza dla aktualizacji. Ta weryfikacja pozwala na zmianę plików poza pamięcią podręczną przy zachowaniu spójności danych.

* Ponad **15% zapisów, sprawdzanie serwera zapasowego pod kątem zmian co 30 sekund** i ponad **15% zapisów, sprawdzanie serwera zapasowego pod kątem zmian co 60 sekund** — te opcje są przeznaczone dla przepływów pracy, w których chcesz przyspieszyć operacje odczytu i zapisu, ale istnieje szansa, że inny użytkownik będzie zapisywać bezpośrednio w systemie magazynu zaplecza. Na przykład jeśli wiele zestawów klientów pracuje nad tymi samymi plikami z różnych lokalizacji, te modele użycia mogą mieć sens, aby zrównoważyć potrzebę szybkiego dostępu do plików z niską tolerancją dla starej zawartości ze źródła.

* **Więcej niż 15% zapisów, zapisuj z powrotem na serwerze co 30 sekund** — ta opcja jest przeznaczona dla scenariusza, w którym wielu klientów aktywnie modyfikuje te same pliki lub jeśli niektórzy klienci uzyskują dostęp do magazynu zaplecza bezpośrednio zamiast przy instalowaniu pamięci podręcznej.

  Częste zapisy zaplecza mają wpływ na wydajność pamięci podręcznej, dlatego należy rozważyć użycie **więcej niż 15% zapisów** w przypadku, gdy istnieje niewielkie ryzyko konfliktu plików — na przykład, Jeśli wiesz, że różni klienci pracują w różnych obszarach tego samego zestawu plików.

* **Odczytaj duże, sprawdzaj serwer zapasowy co 3 godziny** — ta opcja określa priorytety szybkiego odczytu po stronie klienta, ale również odświeża buforowane pliki z systemu magazynu zaplecza regularnie, w przeciwieństwie do **odczytu dużego, rzadko** występujące zapisy.

Ta tabela zawiera podsumowanie różnic między modelami użycia:

| Model użycia                   | Tryb buforowania | Weryfikacja zaplecza | Maksymalne opóźnienie zapisu |
|-------------------------------|--------------|-----------------------|--------------------------|
| Czytaj duże, rzadko występujące zapisy | Odczyt         | Nigdy                 | Brak                     |
| Ponad 15% zapisów       | Odczyt/zapis   | 8 godzin               | 20 minut               |
| Klienci pomijają pamięć podręczną      | Odczyt         | 30 sekund            | Brak                     |
| Więcej niż 15% zapisów, częste sprawdzanie zaplecza (30 sekund) | Odczyt/zapis | 30 sekund | 20 minut |
| Więcej niż 15% zapisów, częste sprawdzanie zaplecza (60 sekund) | Odczyt/zapis | 60 sekund | 20 minut |
| Większe niż 15% zapisów, częste zapisywanie | Odczyt/zapis | 30 sekund | 30 sekund |
| Czytaj duże i sprawdzaj serwer zapasowy co 3 godziny | Odczyt | 3 godziny | Brak |

Jeśli masz pytania dotyczące najlepszego modelu użycia dla przepływu pracy pamięci podręcznej platformy Azure HPC, skontaktuj się z przedstawicielem platformy Azure lub Otwórz żądanie pomocy technicznej, aby uzyskać pomoc.

## <a name="next-steps"></a>Następne kroki

* [Dodawanie obiektów docelowych magazynu](hpc-cache-add-storage.md) do pamięci podręcznej platformy Azure HPC
