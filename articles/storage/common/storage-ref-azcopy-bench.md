---
title: azcopy bench | Microsoft Docs
description: Ten artykuł zawiera informacje referencyjne dotyczące polecenia azcopy bench.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 1e49e787854069c2fcea30df7a43c3aacdd21b9e
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502032"
---
# <a name="azcopy-benchmark"></a>azcopy benchmark

Uruchamia test porównawczy wydajności, przesyłając lub pobierając dane testowe do lub z określonego miejsca docelowego. W przypadku przekazywania dane testowe są generowane automatycznie.

Polecenie testu porównawczego uruchamia ten sam proces co polecenie "copy", z tą różnicą, że: 

  - Zamiast wymagać parametrów źródłowych i docelowych, test porównawczy przyjmuje tylko jeden z nich. Jest to kontener obiektów blob, Azure Files Share lub Azure Data Lake Storage Gen2 plików, do którego chcesz przekazać dane lub z którego chcesz je pobrać.

  - Parametr "mode" opisuje, czy program AzCopy powinien testować przekazywanie do lub pobieranie z danego obiektu docelowego. Prawidłowe wartości to "Przekaż" i "Pobierz". Wartość domyślna to "Przekaż".

  - W przypadku testów porównawczych przekazywania ładunek jest opisywany przez parametry wiersza polecenia, które kontrolują, ile plików jest automatycznie wygenerowanych i jak istotne są pliki. Proces generowania odbywa się całkowicie w pamięci. Dysk nie jest używany.

  - W przypadku pobierania ładunek składa się z plików, które już istnieją w źródle. (Zobacz poniższy przykład na temat sposobu generowania plików testowych w razie potrzeby).
  
  - Obsługiwanych jest tylko kilka parametrów opcjonalnych dostępnych dla polecenia kopiowania.
  
  - Dodatkowa diagnostyka jest mierzona i raportowa.
  
  - W przypadku przekazywania domyślnym zachowaniem jest usunięcie przekazanych danych na końcu uruchomienia testowego.  W przypadku pobierania dane nigdy nie są zapisywane lokalnie.

Tryb testu porównawczego automatycznie dostosuje się do liczby równoległych połączeń TCP, które dają maksymalną przepływność. Ta liczba będzie wyświetlana na końcu. Aby zapobiec automatycznemu dostrajania, AZCOPY_CONCURRENCY_VALUE zmienną środowiskową na określoną liczbę połączeń. 

Obsługiwane są wszystkie typy uwierzytelniania. Jednak najbardziej wygodnym podejściem do testów porównawczych przekazywania jest zazwyczaj utworzenie pustego kontenera z tokenem SAS i użycie uwierzytelniania sygnatury dostępu współdzielonego. (Tryb pobierania wymaga, aby zestaw danych testowych był obecny w kontenerze docelowym).

## <a name="related-conceptual-articles"></a>Powiązane artykuły koncepcyjne

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)
- [Optymalizowanie wydajności narzędzia AzCopy w wersji 10 przy użyciu usługi Azure Storage](storage-use-azcopy-optimize.md)


## <a name="examples"></a>Przykłady

```azcopy
azcopy benchmark [destination] [flags]
```

Uruchom test porównawczy z parametrami domyślnymi (odpowiednie do testów porównawczych sieci o przepustowości do 1 Gb/s):

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>"
```
Uruchom test porównawczy, który przekaże 100 plików o rozmiarze 2 GiB: (odpowiednie do testów porównawczych w szybkiej sieci, na przykład 10 Gb/s):'

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>"--file-count 100 --size-per-file 2G
```
Uruchom test porównawczy, ale użyj 50 000 plików, z których każdy ma rozmiar 8 MiB, i oblicz swoje skróty MD5 (w taki sam sposób, jak flaga robi to w poleceniu `--put-md5` kopiowania). Celem testów porównawczych jest przetestowanie, czy obliczenia MD5 wpływają na przepływność dla wybranej `--put-md5` liczby plików i rozmiaru:

```azcopy
azcopy bench --mode='Upload' "https://[account].blob.core.windows.net/[container]?<SAS>" --file-count 50000 --size-per-file 8M --put-md5
```

Uruchamianie testu porównawczego, który pobiera istniejące pliki z obiektu docelowego

```azcopy
azcopy bench --mode='Download' "https://[account].blob.core.windows.net/[container]?<SAS?"
```

Uruchom przekazywanie, które nie spowoduje usunięcia przeniesionych plików. (Te pliki mogą następnie służyć jako ładunek dla testu pobierania)

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>" --file-count 100 --delete-test-data=false
```

## <a name="options"></a>Opcje

**--blob-type** string Definiuje typ obiektu blob w miejscu docelowym. Służy do umożliwiania testów porównawczych różnych typów obiektów blob. Identyczny z parametrem o tej samej nazwie w poleceniu kopiowania (domyślnie "Wykryj").

**--block-size-mb** float Użyj tego rozmiaru bloku (określonego w MiB). Wartość domyślna jest obliczana automatycznie na podstawie rozmiaru pliku. Dozwolone są ułamki dziesiętne — na przykład 0,25. Taka sama jak parametr o tej samej nazwie w poleceniu kopiowania.

**--check-length**  Sprawdź długość pliku w miejscu docelowym po transferze. W przypadku niezgodności między źródłem i miejscem docelowym transfer jest oznaczony jako nieudany. (wartość domyślna true)

**--delete-test-data**  W przypadku wartości true dane testu porównawczego zostaną usunięte po zakończeniu testu porównawczego.  Ustaw wartość false, jeśli chcesz przechowywać dane w miejscu docelowym — na przykład aby używać ich do testów ręcznych poza trybem testu porównawczego (wartość domyślna to true).

**--file-count** uint.  Liczba automatycznie wygenerowanych plików danych do użycia (domyślnie 100).

**— pomoc**  Pomoc dla bench

**--log-level** string Define the log verbosity for the log file, available levels: INFO(all requests/responses), WARNING(slow responses), ERROR(only failed requests) and NONE (no output logs). (domyślnie "INFO")

**--mode** string Określa, czy azcopy ma testować przekazywanie lub pobieranie z tego obiektu docelowego. Prawidłowe wartości to "przekaż" i "pobierz". Opcja domyślna to "przekaż". (domyślnie "przekaż")

**--number-of-folders** uint Jeśli jest większa niż 0, utwórz foldery, aby podzielić dane.

**--put-md5**  Utwórz skrót MD5 każdego pliku i zapisz skrót jako właściwość Content-MD5 docelowego obiektu blob/pliku. (Domyślnie skrót NIE jest tworzony). Taka sama jak parametr o tej samej nazwie w poleceniu kopiowania.

**--size-per-file** string Rozmiar każdego automatycznie wygenerowanego pliku danych. Musi być liczbą bezpośrednio po niej, po której następuje K, M lub np. 12 tys. lub 200 G (wartość domyślna to "250 mln").

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

**--cap-mb/s float**  Limituje szybkość transferu w megabitach na sekundę. Przepływność moment po chwili może się nieznacznie różnić od limitu. Jeśli ta opcja jest ustawiona na zero lub zostanie pominięta, przepływność nie jest ograniczona.

**--output-type** ciąg Format danych wyjściowych polecenia. Dostępne opcje to: text, json. Wartość domyślna to "text". (domyślnie "text").

**--trusted-microsoft-suffixes** ciąg Określa dodatkowe sufiksy domeny, Azure Active Directory tokeny logowania mogą być wysyłane.  Wartość domyślna to '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net". Wszystkie wymienione w tym miejscu są dodawane do wartości domyślnej. Ze względów bezpieczeństwa należy w tym miejscu Microsoft Azure tylko domen. Oddziel wiele wpisów średnikami.


## <a name="see-also"></a>Zobacz też

- [azcopy](storage-ref-azcopy.md)
