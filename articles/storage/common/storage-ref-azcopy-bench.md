---
title: AzCopy | Microsoft Docs
description: Ten artykuł zawiera informacje referencyjne na temat polecenia AzCopy kanapie.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: c1028d0a4a458746c08fd6fa4f16aa952d9962a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87282011"
---
# <a name="azcopy-benchmark"></a>wzorzec AzCopy

Uruchamia wzorzec wydajności przez przekazywanie lub pobieranie danych testowych do lub z określonego miejsca docelowego. W przypadku przekazywania dane testowe są generowane automatycznie.

Polecenie testu porównawczego uruchamia ten sam proces co "Copy", z tą różnicą, że: 

  - Zamiast wymagać parametrów źródłowych i docelowych, test porównawczy pobiera tylko jeden. Jest to kontener obiektów blob, udział Azure Files lub Azure Data Lake Storage Gen2 system plików, z którego chcesz przekazać lub pobrać.

  - Parametr "Mode" opisuje, czy AzCopy powinien testować przekazywanie do lub pobierać z danego elementu docelowego. Prawidłowe wartości to "upload" i "Download". Wartość domyślna to "upload".

  - W przypadku testów porównawczych, ładunek jest opisywany przez parametry wiersza polecenia, które kontrolują, ile plików jest generowanych automatycznie i jak istotne są pliki. Proces generacji odbywa się w całości w pamięci. Dysk nie jest używany.

  - W przypadku plików do pobrania ładunek składa się z niezależnych plików, które już istnieją w źródle. (Zobacz przykład poniżej o sposobie generowania plików testowych, jeśli jest to konieczne).
  
  - Obsługiwane jest tylko kilka opcjonalnych parametrów, które są dostępne dla polecenia copy.
  
  - Dodatkowa Diagnostyka jest mierzona i raportowana.
  
  - W przypadku przekazywania, zachowanie domyślne polega na usunięciu przesłanych danych na końcu przebiegu testu.  W przypadku plików do pobrania dane nigdy nie są zapisywane lokalnie.

Tryb testu porównawczego automatycznie dostosowuje się do liczby równoległych połączeń TCP, które dają maksymalną przepływność. Ten numer zostanie wyświetlony na końcu. Aby zapobiec autodostrajania, należy ustawić zmienną środowiskową AZCOPY_CONCURRENCY_VALUE na określoną liczbę połączeń. 

Obsługiwane są wszystkie standardowe typy uwierzytelniania. Najlepszym podejściem do przekazywania testów porównawczych jest jednak zwykle utworzenie pustego kontenera z tokenem sygnatury dostępu współdzielonego i użycie uwierzytelniania SAS. (Tryb pobierania wymaga, aby zestaw danych testowych znajdował się w kontenerze docelowym).

## <a name="examples"></a>Przykłady

```azcopy
azcopy benchmark [destination] [flags]
```

Uruchom test testu porównawczego z domyślnymi parametrami (odpowiednie dla sieci testowych do 1 GB/s):

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>"
```
Uruchom test testu porównawczego, który przekazuje 100 plików, każdy 2 GiB w rozmiarze: (odpowiednie dla tworzenia wzorców w szybkiej sieci, na przykład 10 GB/s): "

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>"--file-count 100 --size-per-file 2G
```
Uruchom test testu porównawczego, ale Użyj 50 000 plików, każdy 8 baz MiB w rozmiarze i Oblicz skróty MD5 (w taki sam sposób, jak `--put-md5` w przypadku polecenia copy). Celem przeprowadzania `--put-md5` testów porównawczych jest sprawdzenie, czy obliczenia MD5 wpływają na przepływność dla wybranej liczby plików i rozmiaru:

```azcopy
azcopy bench --mode='Upload' "https://[account].blob.core.windows.net/[container]?<SAS>" --file-count 50000 --size-per-file 8M --put-md5
```

Uruchom test testu porównawczego, który pobiera istniejące pliki z elementu docelowego

```azcopy
azcopy bench --mode='Download' "https://[account].blob.core.windows.net/[container]?<SAS?"
```

Uruchom przekazywanie, które nie usuwa transferowanych plików. (Te pliki mogą następnie stanowić ładunek dla testu pobierania)

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>" --file-count 100 --delete-test-data=false
```

## <a name="options"></a>Opcje

**--ciąg typu BLOB** definiuje typ obiektu BLOB w miejscu docelowym. Służy do zezwalania na testy porównawcze różnych typów obiektów BLOB. Identyczny z tym samym parametrem-nazwanym w poleceniu Copy (domyślnie "Detect").

**--rozmiar bloku-MB** zmiennoprzecinkowy Użyj tego rozmiaru bloku (określonego w MIB). Wartość domyślna jest obliczana automatycznie na podstawie rozmiaru pliku. Dozwolone są ułamki dziesiętne — na przykład 0,25. Identyczny z tym samym parametrem-nazwanym w poleceniu Copy.

**--check-Length**  Sprawdź długość pliku w miejscu docelowym po przeniesieniu. Jeśli występuje niezgodność między źródłem i miejscem docelowym, transfer zostanie oznaczony jako niepowodzenie. (wartość domyślna to true)

**--delete-Test-Data**  W przypadku wartości true dane testu porównawczego zostaną usunięte na końcu przebiegu testu porównawczego.  Ustaw wartość false, jeśli chcesz przechowywać dane w miejscu docelowym — na przykład, aby użyć go do ręcznego testowania poza trybem testowym (wartość domyślna to true).

**--File-Count** uint.  Liczba automatycznie generowanych plików danych do użycia (domyślnie 100).

**--Pomoc**  Pomoc dla kanap

**--ciąg na poziomie dziennika** definiuje szczegółowość dziennika dla pliku dziennika, dostępne poziomy: info (wszystkie żądania/odpowiedzi), ostrzeżenie (wolne odpowiedzi), błąd (tylko Nieudane żądania) i brak (bez dzienników wyjściowych). (wartość domyślna: "INFO")

**--mode** ciąg Określa, czy AzCopy ma testować przekazywanie lub pobieranie z tego obiektu docelowego. Prawidłowe wartości to "upload" i "Download". Opcja domyślna to "upload". (domyślnie "upload")

**--liczba-folderów** uint, jeśli większa niż 0, Utwórz foldery, aby podzielić dane.

**--Put-MD5**  Utwórz skrót MD5 każdego pliku i Zapisz skrót jako właściwość Content-MD5 docelowego obiektu blob/pliku. (Domyślnie skrót nie jest tworzony). Identyczny z tym samym parametrem-nazwanym w poleceniu Copy.

**--** rozmiar ciągu pliku dla każdego pliku danych generowanych automatycznie. Musi być liczbą bezpośrednio, po której następuje K, M lub G. na przykład. 12k lub 200G (domyślnie "250M").

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

**--Cap-MB/s**  Szybkość transferu w megabitach na sekundę. Przepływność czasu na chwilę może się nieco różnić od końca. Jeśli ta opcja jest ustawiona na zero lub zostanie pominięta, przepływność nie zostanie ograniczona.

**--** format ciągu typu danych wyjściowych polecenia. Dostępne opcje to: text, JSON. Wartość domyślna to "text". (domyślny "tekst").

**--Zaufane — ciąg sufiksów firmy Microsoft** określa dodatkowe sufiksy domeny, w których mogą być wysyłane Azure Active Directory tokeny logowania.  Wartość domyślna to "*. Core.Windows.NET;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Wszystkie wymienione tutaj są dodawane do ustawień domyślnych. W celu zapewnienia bezpieczeństwa należy tu umieścić tylko domeny Microsoft Azure. Rozdziel wiele wpisów średnikami.


## <a name="see-also"></a>Zobacz też

- [AzCopy](storage-ref-azcopy.md)
