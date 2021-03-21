---
title: Modele użycia pamięci podręcznej platformy Azure HPC
description: W tym artykule opisano różne modele użycia pamięci podręcznej i sposoby ich wyboru, aby ustawić buforowanie w trybie tylko do odczytu lub odczyt/zapis i kontrolować inne ustawienia buforowania.
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/15/2021
ms.author: v-erkel
ms.openlocfilehash: b23afb17b9b7152e82049ca4f6127e2811913296
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563457"
---
# <a name="understand-cache-usage-models"></a>Omówienie modeli użycia pamięci podręcznej

Modele użycia pamięci podręcznej umożliwiają dostosowanie sposobu przechowywania plików w pamięci podręcznej Azure HPC w celu przyspieszenia przepływu pracy.

## <a name="basic-file-caching-concepts"></a>Podstawowe pojęcia związane z buforowaniem plików

Buforowanie plików to sposób, w jaki usługa Azure HPC cache przyspiesza żądania klientów. Używa tych podstawowych rozwiązań:

* **Buforowanie odczytu** — pamięć podręczna Azure HPC przechowuje kopię plików, z których klienci żądają z systemu magazynu. Następnym razem, gdy klient zażąda tego samego pliku, pamięć podręczna HPC może udostępnić wersję w swojej pamięci podręcznej, zamiast pobierać ją z systemu magazynu zaplecza ponownie.

* **Buforowanie zapisu** — opcjonalnie pamięć podręczna Azure HPC może przechowywać kopię wszystkich zmienionych plików wysyłanych z komputerów klienckich. Jeśli wielu klientów wprowadzi zmiany w tym samym pliku w krótkim czasie, pamięć podręczna może zbierać wszystkie zmiany w pamięci podręcznej, zamiast zapisywać każdą zmianę indywidualnie dla systemu magazynu zaplecza.

  Po upływie określonego czasu bez zmian pamięć podręczna przenosi plik do magazynu długoterminowego.

  Jeśli buforowanie zapisu jest wyłączone, pamięć podręczna nie przechowuje zmienionego pliku i natychmiast zapisuje je w systemie magazynu zaplecza.

* **Opóźnienie zapisu** — w przypadku pamięci podręcznej z włączonym buforowaniem zapisu opóźnienie zapisu to czas, przez jaki pamięć podręczna czeka na dodatkowe zmiany plików przed skopiowaniem pliku do systemu magazynu zaplecza.

* **Weryfikacja zaplecza** — ustawienie weryfikacji zaplecza określa, jak często pamięć podręczna porównuje lokalną kopię pliku ze zdalną wersją w systemie magazynu zaplecza. Jeśli kopia zaplecza jest nowsza niż buforowana kopia, pamięć podręczna pobiera kopię zdalną i zapisuje ją na potrzeby przyszłych żądań.

  Ustawienie weryfikacji zaplecza pokazuje, *kiedy pamięć* podręczna porównuje pliki z plikami źródłowymi w magazynie zdalnym. W pamięci podręcznej platformy Azure HPC można jednak wymusić Porównanie plików przez wykonanie operacji katalogu zawierającej żądanie READDIRPLUS. READDIRPLUS to standardowy interfejs API systemu plików NFS (nazywany także rozszerzonym odczytem), który zwraca metadane katalogu, co powoduje, że pamięć podręczna będzie porównywać i aktualizować pliki.

Modele użycia wbudowane w pamięć podręczną platformy Azure HPC mają różne wartości dla tych ustawień, dzięki czemu można wybrać najlepszą kombinację dla danej sytuacji.

## <a name="choose-the-right-usage-model-for-your-workflow"></a>Wybierz odpowiedni model użycia dla przepływu pracy

Należy wybrać model użycia dla każdego używanego miejsca docelowego magazynu zainstalowanego w systemie plików NFS. Cele magazynu obiektów blob platformy Azure mają wbudowany model użycia, którego nie można dostosować.

Modele użycia pamięci podręcznej HPC umożliwiają wybranie sposobu zrównoważenia szybkiej reakcji z ryzykiem pobierania starych danych. Jeśli chcesz zoptymalizować szybkość odczytywania plików, możesz nie zadbać o to, czy pliki w pamięci podręcznej są sprawdzane względem plików zaplecza. Z drugiej strony, jeśli chcesz upewnić się, że pliki są zawsze aktualne z magazynem zdalnym, wybierz model, który sprawdza się często.

Są to opcje modelu użycia:

* **Czytaj duże, rzadko** występujące zapisy — Użyj tej opcji, jeśli chcesz przyspieszyć dostęp do odczytu do plików, które są statyczne lub rzadko zmieniane.

  Ta opcja powoduje buforowanie odczytów klienta, ale nie buforuje operacji zapisu. Natychmiast przejdzie zapis do magazynu zaplecza.
  
  Pliki przechowywane w pamięci podręcznej nie są automatycznie porównywane z plikami znajdującymi się na woluminie magazynu NFS. (Przeczytaj opis weryfikacji zaplecza powyżej, aby dowiedzieć się, jak porównać je ręcznie).

  Nie należy używać tej opcji, jeśli istnieje ryzyko, że plik może być modyfikowany bezpośrednio w systemie magazynu bez wcześniejszego zapisania go w pamięci podręcznej. W takim przypadku buforowana wersja pliku nie jest zsynchronizowana z plikiem zaplecza.

* **Więcej niż 15% zapisów** — ta opcja przyspiesza zarówno wydajność odczytu, jak i zapisu. W przypadku korzystania z tej opcji Wszyscy klienci muszą uzyskać dostęp do plików za pośrednictwem pamięci podręcznej platformy Azure HPC zamiast bezpośrednio zainstalować magazyn zaplecza. Pliki w pamięci podręcznej będą miały ostatnio wprowadzone zmiany, które nie zostały jeszcze skopiowane do zaplecza.

  W tym modelu użycia pliki w pamięci podręcznej są sprawdzane tylko względem plików w magazynie zaplecza co osiem godzin. Założono, że w pamięci podręcznej znajduje się nowsza wersja pliku. Zmodyfikowany plik w pamięci podręcznej jest zapisywana w systemie magazynu zaplecza, gdy był w pamięci podręcznej przez 20 minut<!-- an hour --> bez dodatkowych zmian.

* **Klienci zapisują w miejscu DOCELOWYM NFS, pomijając pamięć podręczną** — wybierz tę opcję, jeśli dowolni klienci w przepływie pracy zapisują dane bezpośrednio w systemie magazynu bez wcześniejszego zapisu w pamięci podręcznej lub jeśli chcesz zoptymalizować spójność danych. Pliki, które żądania klientów są buforowane (odczyt), ale wszelkie zmiany plików z klienta (zapis) nie są buforowane. Są one przenoszone bezpośrednio do systemu magazynu zaplecza.

  W tym modelu użycia pliki w pamięci podręcznej są często sprawdzane względem wersji zaplecza aktualizacji — co 30 sekund. Ta weryfikacja pozwala na zmianę plików poza pamięcią podręczną przy zachowaniu spójności danych.

  > [!TIP]
  > Pierwsze trzy podstawowe modele użycia mogą służyć do obsługi większości przepływów pracy pamięci podręcznej platformy Azure HPC. Kolejne opcje dotyczą mniej typowych scenariuszy.

* Ponad **15% zapisów, sprawdzanie serwera zapasowego pod kątem zmian co 30 sekund** i ponad **15% zapisów, sprawdzanie serwera zapasowego pod kątem zmian co 60 sekund** — te opcje są przeznaczone dla przepływów pracy, w których chcesz przyspieszyć operacje odczytu i zapisu, ale istnieje szansa, że inny użytkownik będzie zapisywać bezpośrednio w systemie magazynu zaplecza. Na przykład jeśli wiele zestawów klientów pracuje nad tymi samymi plikami z różnych lokalizacji, te modele użycia mogą mieć sens, aby zrównoważyć potrzebę szybkiego dostępu do plików z niską tolerancją dla starej zawartości ze źródła.

* **Więcej niż 15% zapisów, zapisuj z powrotem na serwerze co 30 sekund** — ta opcja jest przeznaczona dla scenariusza, w którym wielu klientów aktywnie modyfikuje te same pliki lub jeśli niektórzy klienci uzyskują dostęp do magazynu zaplecza bezpośrednio zamiast przy instalowaniu pamięci podręcznej.

  Częste zapisy zaplecza mają wpływ na wydajność pamięci podręcznej, dlatego należy rozważyć użycie **więcej niż 15% zapisów** w przypadku, gdy istnieje niewielkie ryzyko konfliktu plików — na przykład, Jeśli wiesz, że różni klienci pracują w różnych obszarach tego samego zestawu plików.

* **Odczytaj duże, sprawdzaj serwer zapasowy co 3 godziny** — ta opcja określa priorytety szybkiego odczytu po stronie klienta, ale również odświeża buforowane pliki z systemu magazynu zaplecza regularnie, w przeciwieństwie do **odczytu dużego, rzadko** występujące zapisy.

Ta tabela zawiera podsumowanie różnic między modelami użycia:

[!INCLUDE [usage-models-table.md](includes/usage-models-table.md)]

<!-- | Usage model                   | Caching mode | Back-end verification | Maximum write-back delay |
|-------------------------------|--------------|-----------------------|--------------------------|
| Read heavy, infrequent writes | Read         | Never                 | None                     |
| Greater than 15% writes       | Read/write   | 8 hours               | 20 minutes               |
| Clients bypass the cache      | Read         | 30 seconds            | None                     |
| Greater than 15% writes, frequent back-end checking (30 seconds) | Read/write | 30 seconds | 20 minutes |
| Greater than 15% writes, frequent back-end checking (60 seconds) | Read/write | 60 seconds | 20 minutes |
| Greater than 15% writes, frequent write-back | Read/write | 30 seconds | 30 seconds |
| Read heavy, checking the backing server every 3 hours | Read | 3 hours | None |
-->
Jeśli masz pytania dotyczące najlepszego modelu użycia dla przepływu pracy pamięci podręcznej platformy Azure HPC, skontaktuj się z przedstawicielem platformy Azure lub Otwórz żądanie pomocy technicznej, aby uzyskać pomoc.

## <a name="next-steps"></a>Następne kroki

* [Dodawanie obiektów docelowych magazynu](hpc-cache-add-storage.md) do pamięci podręcznej platformy Azure HPC
