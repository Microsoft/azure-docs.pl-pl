---
title: AzCopy | Microsoft Docs
description: Ten artykuł zawiera informacje referencyjne na temat polecenia AzCopy kanapie.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 40ff6c6c76e255945681e678ef296ffcf9978f61
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2020
ms.locfileid: "84485187"
---
# <a name="azcopy-benchmark"></a>wzorzec AzCopy

Uruchamia wzorzec wydajności, przekazując dane testowe do określonego miejsca docelowego. Dane testowe są generowane automatycznie.

Polecenie testu porównawczego uruchamia ten sam proces przekazywania co "Copy", z tą różnicą, że:

  - Brak parametru źródłowego.  Polecenie wymaga tylko docelowego adresu URL. 
  
  - Ładunek jest opisany przez parametry wiersza polecenia, które kontrolują, ile plików jest generowanych automatycznie i ich rozmiar. Proces generacji odbywa się w całości w pamięci. Dysk nie jest używany.
  
  - Obsługiwane jest tylko kilka opcjonalnych parametrów, które są dostępne dla polecenia copy.
  
  - Dodatkowa Diagnostyka jest mierzona i raportowana.
  
  - Domyślnie transferowane dane są usuwane na końcu przebiegu testu.

Tryb testu porównawczego automatycznie dostosowuje się do liczby równoległych połączeń TCP, które dają maksymalną przepływność. Ten numer zostanie wyświetlony na końcu. Aby zapobiec autodostrajania, należy ustawić zmienną środowiskową AZCOPY_CONCURRENCY_VALUE na określoną liczbę połączeń.

Obsługiwane są wszystkie standardowe typy uwierzytelniania. Jednak najbardziej wygodnym podejściem do korzystania z testów porównawczych jest zwykle utworzenie pustego kontenera z tokenem SAS i użycie uwierzytelniania SAS.

## <a name="examples"></a>Przykłady

```azcopy
azcopy benchmark [destination] [flags]
```

Uruchom test testu porównawczego z domyślnymi parametrami (odpowiednie dla sieci testowych do 1 GB/s):

- AzCopy "https://[Account]. blob. Core. Windows. NET/[Container]? <SAS> "

Uruchom test testu porównawczego, który przekazuje 100 plików, każdy 2 GiB w rozmiarze: (odpowiednie dla tworzenia wzorców w szybkiej sieci, na przykład 10 GB/s): "

- AzCopy "https://[Account]. blob. Core. Windows. NET/[Container]? <SAS> " --File-Count 100--size-na-File 2G

Uruchom test testu porównawczego, ale Użyj 50 000 plików, każdy 8 baz MiB w rozmiarze i Oblicz skróty MD5 (w taki sam sposób, jak `--put-md5` w przypadku polecenia copy). Celem przeprowadzania `--put-md5` testów porównawczych jest sprawdzenie, czy obliczenia MD5 wpływają na przepływność dla wybranej liczby plików i rozmiaru:

- AzCopy "https://[Account]. blob. Core. Windows. NET/[Container]? <SAS> " --File-Count 50000--size-na-File 8 M--Put-MD5

## <a name="options"></a>Opcje

**--ciąg typu BLOB** definiuje typ obiektu BLOB w miejscu docelowym. Służy do zezwalania na testy porównawcze różnych typów obiektów BLOB. Identyczny z tym samym parametrem-nazwanym w poleceniu Copy (domyślnie "Detect").

**--rozmiar bloku-MB** zmiennoprzecinkowy Użyj tego rozmiaru bloku (określonego w MIB). Wartość domyślna jest obliczana automatycznie na podstawie rozmiaru pliku. Dozwolone są ułamki dziesiętne — np. 0,25. Identyczny z tym samym parametrem-nazwanym w poleceniu Copy.

**--delete-Test-Data**  W przypadku wartości true dane testu porównawczego zostaną usunięte na końcu przebiegu testu porównawczego.  Ustaw wartość false, jeśli chcesz przechowywać dane w miejscu docelowym — na przykład, aby użyć go do ręcznego testowania poza trybem testowym (wartość domyślna to true).

**--File-Count-** liczba automatycznie generowanych plików danych do użycia (domyślnie 100).

**-h,--pomoc**  Pomoc dla kanap

**--ciąg na poziomie dziennika** definiuje szczegółowość dziennika dla pliku dziennika, dostępne poziomy: info (wszystkie żądania/odpowiedzi), ostrzeżenie (wolne odpowiedzi), błąd (tylko Nieudane żądania) i brak (bez dzienników wyjściowych). (wartość domyślna: "INFO")

**--Put-MD5**  Utwórz skrót MD5 każdego pliku i Zapisz skrót jako właściwość Content-MD5 docelowego obiektu blob/pliku. (Domyślnie skrót nie jest tworzony). Identyczny z tym samym parametrem-nazwanym w poleceniu Copy.

**--** rozmiar ciągu pliku dla każdego automatycznie generowanego pliku danych. Musi być liczbą bezpośrednio, po której następuje K, M lub G. na przykład. 12k lub 200G (domyślnie "250M").

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

**--Cap-MB/s UInt32**  Szybkość transferu w megabitach na sekundę. Przepływność czasu na chwilę może się nieco różnić od końca. Jeśli ta opcja jest ustawiona na zero lub zostanie pominięta, przepływność nie zostanie ograniczona.

**--** format ciągu typu danych wyjściowych polecenia. Dostępne opcje to: text, JSON. Wartość domyślna to "text". (domyślny "tekst").

**--Zaufane — ciąg sufiksów firmy Microsoft** określa dodatkowe sufiksy domeny, w których mogą być wysyłane Azure Active Directory tokeny logowania.  Wartość domyślna to "*. Core.Windows.NET;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Wszystkie wymienione tutaj są dodawane do ustawień domyślnych. W celu zapewnienia bezpieczeństwa należy tu umieścić tylko domeny Microsoft Azure. Rozdziel wiele wpisów średnikami.

## <a name="see-also"></a>Zobacz także

- [azcopy](storage-ref-azcopy.md)
