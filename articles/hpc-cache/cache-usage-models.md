---
title: Azure HPC Cache modeli użycia
description: Opisuje różne modele użycia pamięci podręcznej i sposób wyboru spośród nich, aby ustawić buforowanie tylko do odczytu lub odczytu/zapisu i kontrolować inne ustawienia buforowania
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 04/08/2021
ms.author: v-erkel
ms.openlocfilehash: 7e1b11fd15cca9b11fc627222318f08d31743336
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719191"
---
# <a name="understand-cache-usage-models"></a>Informacje o modelach użycia pamięci podręcznej

Modele użycia pamięci podręcznej umożliwiają dostosowanie sposobu Azure HPC Cache plików w celu przyspieszenia przepływu pracy.

## <a name="basic-file-caching-concepts"></a>Podstawowe pojęcia dotyczące buforowania plików

Buforowanie plików to sposób, Azure HPC Cache przyspiesza żądania klientów. Używa ona tych podstawowych rozwiązań:

* **Buforowanie odczytu** — Azure HPC Cache przechowuje kopię plików, których klienci żądają z systemu magazynu. Następnym razem, gdy klient zażąda tego samego pliku, HPC Cache udostępnić wersję w swojej pamięci podręcznej, zamiast ponownie pobierać ją z systemu magazynowania na zakładzie.

* **Buforowanie zapisu** — opcjonalnie Azure HPC Cache przechowywać kopię wszelkich zmienionych plików wysyłanych z maszyn klienckich. Jeśli wielu klientów wprowadza w krótkim czasie zmiany w tym samym pliku, pamięć podręczna może zebrać wszystkie zmiany w pamięci podręcznej, zamiast zapisywać każdą zmianę osobno w systemie magazynu na zakładzie.

  Po upływie określonego czasu bez zmian pamięć podręczna przenosi plik do systemu magazynu długoterminowego.

  Jeśli buforowanie zapisu jest wyłączone, pamięć podręczna nie przechowuje zmienionego pliku i natychmiast zapisuje go w systemie magazynu na zakładzie.

* Opóźnienie zapisu **—** w przypadku pamięci podręcznej z włączonym buforowaniem zapisu opóźnienie zapisu to czas oczekiwania pamięci podręcznej na dodatkowe zmiany plików przed skopiowaniem pliku do systemu magazynu na zakładzie.

* Weryfikacja za pomocą serwera **końcowego** — ustawienie weryfikacji serwera końcowego określa, jak często pamięć podręczna porównuje jego lokalną kopię pliku z wersją zdalną w systemie magazynu na zakładzie. Jeśli kopia zapasowa jest nowsza niż kopia w pamięci podręcznej, pamięć podręczna pobiera kopię zdalną i zapisuje ją do przyszłych żądań.

  Ustawienie weryfikacji serwera końcowego pokazuje, kiedy pamięć podręczna *automatycznie* porównuje swoje pliki z plikami źródłowymi w magazynie zdalnym. Można jednak wymusić porównywanie Azure HPC Cache, wykonując operację katalogu, która zawiera żądanie readdirplus. Readdirplus to standardowy interfejs API systemu plików NFS (nazywany również rozszerzonym odczytem), który zwraca metadane katalogu, co powoduje, że pamięć podręczna porównuje i aktualizuje pliki.

Modele użycia wbudowane w Azure HPC Cache mają różne wartości dla tych ustawień, dzięki czemu można wybrać najlepszą kombinację dla swojej sytuacji.

## <a name="choose-the-right-usage-model-for-your-workflow"></a>Wybieranie odpowiedniego modelu użycia dla przepływu pracy

Należy wybrać model użycia dla każdego obiektu docelowego magazynu protokołu NFS, który jest używany. Obiekty docelowe usługi Azure Blob Storage mają wbudowany model użycia, których nie można dostosować.

HPC Cache modeli użycia pozwalają wybrać sposób równoważenia szybkiej reakcji z ryzykiem uzyskania nieaktualnych danych. Jeśli chcesz zoptymalizować szybkość odczytywania plików, może nie być konieczne, czy pliki w pamięci podręcznej są sprawdzane względem plików serwera końcowego. Z drugiej strony, jeśli chcesz upewnić się, że pliki są zawsze aktualne w magazynie zdalnym, wybierz model, który często sprawdza.

Oto opcje modelu użycia:

* **Odczyt dużych, rzadkich** zapisu — użyj tej opcji, jeśli chcesz przyspieszyć dostęp do odczytu do plików, które są statyczne lub rzadko zmieniane.

  Ta opcja buforuje odczyty klienta, ale nie buforuje zapisu. Natychmiast przekazuje on zapis do magazynu na zadomowie.
  
  Pliki przechowywane w pamięci podręcznej nie są automatycznie porównywane z plikami na woluminie magazynu NFS. (Przeczytaj powyższy opis weryfikacji za pomocą usługi Back-End, aby dowiedzieć się, jak porównać je ręcznie).

  Nie używaj tej opcji, jeśli istnieje ryzyko, że plik może zostać zmodyfikowany bezpośrednio w systemie magazynu bez wcześniejszego zapisywania go w pamięci podręcznej. W takim przypadku buforowana wersja pliku nie będzie zsynchronizowana z plikiem back-end.

* **Więcej niż 15%** zapisu — ta opcja przyspiesza zarówno wydajność odczytu, jak i zapisu. W przypadku korzystania z tej opcji wszyscy klienci muszą uzyskać dostęp do plików za pośrednictwem Azure HPC Cache zamiast bezpośrednio instalowanie magazynu na zadomowie. Buforowane pliki będą mieć ostatnio wprowadzone zmiany, które nie zostały jeszcze skopiowane do serwera back end.

  W tym modelu użycia pliki w pamięci podręcznej są sprawdzane tylko pod kątem plików w magazynie back-end co osiem godzin. Zakłada się, że buforowana wersja pliku jest bardziej aktualna. Zmodyfikowany plik w pamięci podręcznej jest zapisywany w systemie magazynu na zakładzie przez 20 minut<!-- an hour --> bez żadnych dodatkowych zmian.

* Klienci zapis do obiektu docelowego **systemu plików NFS z** pominięciem pamięci podręcznej — wybierz tę opcję, jeśli dowolny klient w przepływie pracy zapisuje dane bezpośrednio w systemie magazynu bez wcześniejszego zapisu w pamięci podręcznej lub jeśli chcesz zoptymalizować spójność danych. Pliki, których żądają klienci, są buforowane (odczyty), ale wszelkie zmiany tych plików z klienta (zapisu) nie są buforowane. Są one przekazywane bezpośrednio do systemu magazynowania na zakładzie.

  W przypadku tego modelu użycia pliki w pamięci podręcznej są często sprawdzane pod kątem aktualizacji w wersjach serwera końcowego — co 30 sekund. Ta weryfikacja umożliwia zmiany plików poza pamięcią podręczną przy zachowaniu spójności danych.

  > [!TIP]
  > Pierwsze trzy podstawowe modele użycia mogą być używane do obsługi większości Azure HPC Cache przepływów pracy. Następne opcje są dostępne dla mniej typowych scenariuszy.

* Więcej niż **15% zapisu,** sprawdzanie serwera zapasowego pod kątem zmian co 30 sekund i więcej niż **15% zapisu,** sprawdzanie serwera zapasowego pod kątem zmian co 60 sekund — te opcje są przeznaczone dla przepływów pracy, w których chcesz przyspieszyć zarówno odczyt, jak i zapis, ale istnieje możliwość, że inny użytkownik będzie zapisywać bezpośrednio w systemie magazynu na zakładzie. Jeśli na przykład wiele zestawów klientów pracuje nad tymi samymi plikami z różnych lokalizacji, te modele użycia mogą mieć sens, aby zrównoważyć potrzebę szybkiego dostępu do plików z niską tolerancją nieaktualnej zawartości ze źródła.

* Więcej niż **15% zapisu,** zapisuj z powrotem na serwerze co 30 sekund — ta opcja jest przeznaczona dla scenariusza, w którym wielu klientów aktywnie modyfikuje te same pliki lub jeśli niektórzy klienci bezpośrednio uzyskają dostęp do magazynu zadomowienia zamiast instalowanie pamięci podręcznej.

  Częste zapis w zapotrzebowanej pamięci podręcznej ma wpływ na wydajność pamięci podręcznej, dlatego należy rozważyć użycie modelu użycia większe niż **15%** zapisu w przypadku niskiego ryzyka konfliktu plików — na przykład jeśli wiesz, że różni klienci pracują w różnych obszarach tego samego zestawu plików.

* Odczyt intensywne, sprawdzanie serwera zapasowego co **3** godziny — ta opcja określa priorytet szybkich odczytów po stronie klienta, a także regularnie odświeża pliki buforowane z systemu magazynowania na zakładzie, w przeciwieństwie do modelu użycia Odczyt z dużym obciążeniem, rzadkimi zapisami. 

Ta tabela zawiera podsumowanie różnic między modelami użycia:

[!INCLUDE [usage-models-table.md](includes/usage-models-table.md)]

Jeśli masz pytania dotyczące najlepszego modelu użycia dla przepływu pracy usługi Azure HPC Cache, porozmawiaj z przedstawicielem platformy Azure lub otwórz wniosek o pomoc techniczną, aby uzyskać pomoc.

## <a name="know-when-to-remount-clients-for-nlm"></a>Wiedzieć, kiedy ponownie instalować klientów dla nlm

W niektórych sytuacjach może być konieczne ponowne odinstalowanie klientów w przypadku zmiany modelu użycia obiektu docelowego magazynu. Jest to wymagane ze względu na sposób, w jaki różne modele użycia obsługują żądania menedżera blokady sieci (NLM).

Klient HPC Cache między klientami a systemem magazynu na zakładzie. Zazwyczaj pamięć podręczna przekazuje żądania NLM do systemu magazynu na zadomowie, ale w niektórych sytuacjach sama pamięć podręczna potwierdza żądanie NLM i zwraca wartość do klienta. W Azure HPC Cache dzieje się tak tylko w przypadku używania modelu użycia Odczyt **duży,** rzadki zapis (lub w przypadku standardowego obiektu docelowego magazynu obiektów blob, który nie ma konfigurowalnych modeli użycia).

Istnieje niewielkie ryzyko konfliktu plików w przypadku zmiany między modelem użycia odczytu o dużym **lub** rzadkim użyciu zapisu a innym modelem użycia. Nie ma możliwości przeniesienia bieżącego stanu NLM z pamięci podręcznej do systemu magazynu lub na odwrót. W związku z tym stan blokady klienta jest niedokładny.

Ponownie odinstaluj klientów, aby upewnić się, że nowy menedżer blokady ma dokładny stan NLM.

Jeśli klienci wysyłają żądanie NLM, gdy model użycia lub magazyn wewnętrzny go nie obsługuje, zostanie wyświetlony błąd.

### <a name="disable-nlm-at-client-mount-time"></a>Wyłączanie funkcji NLM podczas instalacji klienta

Nie zawsze jest łatwo ustalić, czy systemy klienckie będą wysyłać żądania NLM.

Funkcję NLM można wyłączyć, gdy klienci zainstalują klaster przy użyciu opcji ``-o nolock`` w ``mount`` poleceniu .

Dokładne zachowanie opcji zależy od systemu operacyjnego klienta, dlatego sprawdź dokumentację instalacji ``nolock`` (man 5 nfs) dla systemu operacyjnego klienta. W większości przypadków blokada jest przenosina lokalnie do klienta. Należy zachować ostrożność, jeśli aplikacja blokuje pliki na wielu klientach.

> [!NOTE]
> AdLS-NFS nie obsługuje nlm. W przypadku korzystania z obiektu docelowego magazynu ADLS-NFS należy wyłączyć funkcję NLM z opcją instalacji powyżej.

## <a name="next-steps"></a>Następne kroki

* [Dodawanie obiektów docelowych](hpc-cache-add-storage.md) magazynu do Azure HPC Cache
