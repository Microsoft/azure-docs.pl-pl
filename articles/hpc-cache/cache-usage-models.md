---
title: Modele użycia pamięci podręcznej platformy Azure HPC
description: W tym artykule opisano różne modele użycia pamięci podręcznej i sposoby ich wyboru, aby ustawić buforowanie w trybie tylko do odczytu lub odczyt/zapis i kontrolować inne ustawienia buforowania.
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 04/08/2021
ms.author: v-erkel
ms.openlocfilehash: a22f4b257476e96c51ae491b8570e3798f7b3ab7
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259731"
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

Należy wybrać model użycia dla każdego używanego docelowego magazynu systemu plików NFS. Cele magazynu obiektów blob platformy Azure mają wbudowany model użycia, którego nie można dostosować.

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

Jeśli masz pytania dotyczące najlepszego modelu użycia dla przepływu pracy pamięci podręcznej platformy Azure HPC, skontaktuj się z przedstawicielem platformy Azure lub Otwórz żądanie pomocy technicznej, aby uzyskać pomoc.

## <a name="know-when-to-remount-clients-for-nlm"></a>Informacje o tym, kiedy należy ponownie zainstalować klientów dla usługi NLM

W niektórych sytuacjach może być konieczne ponowne zainstalowanie klientów w przypadku zmiany modelu użycia miejsca docelowego magazynu. Jest to niezbędny sposób, ponieważ różne modele użycia obsługują żądania programu Network Lock Manager (NLM).

Pamięć podręczna HPC znajduje się między klientami a systemem magazynu zaplecza. Zwykle pamięć podręczna przekazuje żądania NLM do systemu magazynu zaplecza, ale w niektórych sytuacjach sama pamięć podręczna potwierdza żądanie NLM i zwraca wartość do klienta. W pamięci podręcznej platformy Azure HPC dzieje się to tylko wtedy, gdy używasz modelu użycia **Odczytaj duże, rzadko** występujące zapisy (lub w standardowym miejscu docelowym magazynu obiektów blob, które nie mają konfigurowalnych modeli użycia).

Istnieje niewielkie ryzyko konfliktu plików w przypadku zmiany między **odczytanym i nierzadko zapisywanym** modelem użycia i innym modelem użycia. Nie ma sposobu na przeniesienie bieżącego stanu NLM z pamięci podręcznej do systemu magazynu ani na odwrót. Stan blokady klienta jest niedokładny.

Zainstaluj ponownie klientów, aby upewnić się, że mają prawidłowy stan NLM z nowym menedżerem blokad.

Jeśli klient wysyła żądanie NLM, gdy model użycia lub magazyn zaplecza nie obsługuje tego żądania, spowoduje to wyświetlenie błędu.

### <a name="disable-nlm-at-client-mount-time"></a>Wyłącz funkcję NLM na czas instalacji klienta

Nie zawsze jest łatwo wiadomo, czy systemy klienckie będą wysyłać żądania NLM.

Można wyłączyć NLM, gdy klienci instalują klaster przy użyciu opcji ``-o nolock`` w ``mount`` poleceniu.

Dokładne zachowanie ``nolock`` opcji zależy od systemu operacyjnego klienta, dlatego należy zapoznać się z dokumentacją instalacji (Man 5 NFS) dla systemu operacyjnego klienta. W większości przypadków blokada jest przenoszona lokalnie na klienta. Należy zachować ostrożność, jeśli aplikacja blokuje pliki na wielu klientach.

> [!NOTE]
> ADLS — system plików NFS nie obsługuje NLM. W przypadku korzystania z miejsca docelowego magazynu ADLS-NFS należy wyłączyć funkcję NLM z powyższej opcji instalacji.

## <a name="next-steps"></a>Następne kroki

* [Dodawanie obiektów docelowych magazynu](hpc-cache-add-storage.md) do pamięci podręcznej platformy Azure HPC
