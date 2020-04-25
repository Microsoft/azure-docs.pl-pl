---
title: Przyspieszenie zapytań Azure Data Lake Storage (wersja zapoznawcza)
description: Przyspieszenie zapytań (wersja zapoznawcza) to nowa funkcja dla Azure Data Lake Storage, która umożliwia aplikacjom i platformom analitycznym znacznie zoptymalizować przetwarzanie danych przez pobranie tylko danych wymaganych do wykonania operacji przetwarzania.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.reviewer: jamesbak
ms.date: 04/21/2020
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 70a087e106e632d697052461928f3e1123a06b1b
ms.sourcegitcommit: 1ed0230c48656d0e5c72a502bfb4f53b8a774ef1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82137539"
---
# <a name="azure-data-lake-storage-query-acceleration-preview"></a>Przyspieszenie zapytań Azure Data Lake Storage (wersja zapoznawcza)

Przyspieszenie zapytań (wersja zapoznawcza) to nowa funkcja Azure Data Lake Storage, która umożliwia aplikacjom i platformom analitycznym znaczne Optymalizowanie przetwarzania danych przez pobranie tylko tych danych, których potrzebują do wykonania danej operacji. Pozwala to skrócić czas i moc obliczeniową, która jest wymagana do uzyskania kluczowych informacji o przechowywanych danych.

> [!NOTE]
> Funkcja przyspieszenia zapytań jest w publicznej wersji zapoznawczej i jest dostępna w regionach Kanada Środkowa i Francja środkowa. Aby zapoznać się z ograniczeniami, zobacz artykuł [znane problemy](data-lake-storage-known-issues.md) . Aby zarejestrować się w wersji zapoznawczej, zobacz [ten formularz](https://aka.ms/adls/qa-preview-signup).  

## <a name="overview"></a>Omówienie

Przyspieszenie zapytań akceptuje *predykaty* filtrowania i *projekcje kolumn* , które umożliwiają aplikacjom filtrowanie wierszy i kolumn w czasie, gdy dane są odczytywane z dysku. Tylko dane, które spełniają warunki predykatu, są transferowane przez sieć do aplikacji. Zmniejsza to opóźnienia sieci i koszt obliczeniowy.  

Aby określić predykaty filtru wierszy i projekcje kolumn w żądaniu przyspieszenia zapytania, można użyć języka SQL. Żądanie przetwarza tylko jeden plik. W związku z tym zaawansowane funkcje relacyjne SQL, takie jak sprzężenia i grupowanie według agregacji, nie są obsługiwane. Przyspieszenie zapytań obsługuje dane w formacie CSV i JSON jako dane wejściowe do każdego żądania.

Funkcja przyspieszania zapytań nie jest ograniczona do Data Lake Storage (konta magazynu, na których włączono hierarchiczną przestrzeń nazw). Przyspieszenie zapytań jest w pełni zgodne z obiektami BLOB na kontach magazynu, na których **nie** włączono hierarchicznej przestrzeni nazw. Oznacza to, że można osiągnąć takie samo zmniejszenie opóźnienia sieci i kosztów obliczeniowych podczas przetwarzania danych, które są już przechowywane jako obiekty blob na kontach magazynu.

Aby zapoznać się z przykładem użycia przyspieszenia zapytania w aplikacji klienckiej, zobacz [filtrowanie danych za pomocą Azure Data Lake Storage przyspieszania zapytań](data-lake-storage-query-acceleration-how-to.md).

## <a name="data-flow"></a>Przepływ danych

Na poniższym diagramie przedstawiono sposób, w jaki Typowa aplikacja używa przyspieszenia zapytań do przetwarzania danych.

> [!div class="mx-imgBorder"]
> ![Przyspieszenie zapytań — Omówienie](./media/data-lake-storage-query-acceleration/query-acceleration.png)

1. Aplikacja kliencka żąda danych pliku, określając predykaty i projekcje kolumn.

2. Przyspieszenie zapytań analizuje określone zapytanie SQL i dystrybuuje prace do analizy i filtrowania danych.

3. Procesory odczytują dane z dysku, analizują dane przy użyciu odpowiedniego formatu, a następnie filtrują dane, stosując określone predykaty i projekcje kolumn.

4. Przyspieszenie zapytań łączy fragmentów odpowiedzi, aby przesłać strumieniowo do aplikacji klienckiej.

5. Aplikacja kliencka odbiera i analizuje przesyłaną strumieniowo odpowiedź. Aplikacja nie musi odfiltrować żadnych dodatkowych danych i może bezpośrednio zastosować wymagane obliczenia lub przekształcenie.

## <a name="better-performance-at-a-lower-cost"></a>Lepsza wydajność z niższym kosztem

Przyspieszenie zapytań optymalizuje wydajność, zmniejszając ilość danych, które są przesyłane i przetwarzane przez aplikację.

Aby obliczyć zagregowaną wartość, aplikacje zwykle pobierają **wszystkie** dane z pliku, a następnie przetwarzają i filtrują dane lokalnie. Analiza wzorców wejścia/wyjścia dla obciążeń analitycznych ujawnia, że aplikacje wymagają zwykle tylko 20% danych, które są przez nich odczytywane w celu wykonania danego obliczenia. Ta statystyka jest prawdziwa nawet po zastosowaniu technik takich jak [oczyszczanie partycji](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-optimize-hive-query#hive-partitioning). Oznacza to, że 80% tych danych jest niepotrzebnie przesyłane przez sieć, przeanalizowane i filtrowane według aplikacji. Ten wzorzec, głównie przeznaczony do usuwania niepotrzebnych danych, wiąże się z istotnym kosztem obliczeniowym.  

Mimo że platforma Azure oferuje wiodącą w branży sieć, pod względem przepływności i opóźnień, niepotrzebnie transfer danych w sieci jest w dalszym ciągu kosztowny dla wydajności aplikacji. Dzięki filtrowaniu niechcianych danych podczas żądania magazynu przyspieszenie zapytań eliminuje ten koszt.

Ponadto obciążenie procesora CPU, które jest wymagane do analizowania i filtrowania niepotrzebnych danych, wymaga, aby aplikacja mogła zapewnić większą liczbę i większe maszyny wirtualne w celu wykonania tej czynności. Dzięki przeniesieniu tego obciążenia obliczeń do przyspieszenia zapytań aplikacje mogą korzystać z znaczących oszczędności kosztów.

## <a name="applications-that-can-benefit-from-query-acceleration"></a>Aplikacje, które mogą korzystać z przyspieszania zapytań

Przyspieszenie zapytań jest przeznaczone do obsługi rozproszonych platform analitycznych i aplikacji do przetwarzania danych. 

Struktury analizy rozproszonej, takie jak Apache Spark i Apache Hive, obejmują warstwę abstrakcji magazynu w ramach struktury. Te aparaty obejmują również optymalizacje zapytań, które mogą uwzględniać wiedzę o możliwościach podstawowej usługi we/wy podczas określania optymalnego planu zapytania dla zapytań użytkowników. Te platformy zaczynają integrować przyspieszenie zapytań. W związku z tym użytkownicy tych struktur zobaczą ulepszone opóźnienia zapytań i niższy całkowity koszt posiadania bez konieczności wprowadzania jakichkolwiek zmian w zapytaniach. 

Przyspieszenie zapytań jest również przeznaczone dla aplikacji do przetwarzania danych. Te typy aplikacji zwykle wykonują przekształcenia danych o dużej skali, które mogą nie prowadzić bezpośrednio do analizy szczegółowych informacji, aby nie zawsze używały ustalonych struktur analizy rozproszonej. Te aplikacje często mają bardziej bezpośrednią relację z podstawową usługą magazynu, dzięki czemu mogą korzystać bezpośrednio z funkcji, takich jak przyspieszenie zapytań. 

Aby zapoznać się z przykładem, jak aplikacja może zintegrować przyspieszenie zapytań, zobacz [filtrowanie danych za pomocą Azure Data Lake Storage przyspieszania zapytań](data-lake-storage-query-acceleration-how-to.md).

## <a name="pricing"></a>Cennik

Ze względu na zwiększone obciążenie obliczeniowe w ramach usługi Azure Data Lake Storage Model cenowy dla użycia przyspieszania zapytań różni się od normalnego Azure Data Lake Storage modelu transakcji. Przyspieszenie zapytań obciąża koszt ilości skanowanych danych, a także koszt ilości danych zwracanych do obiektu wywołującego.

Pomimo zmiany modelu rozliczeń, model cen przyspieszania zapytań został zaprojektowany w celu obniżenia całkowitego kosztu posiadania obciążenia, z uwzględnieniem zmniejszenia znacznie kosztownych kosztów maszyn wirtualnych.

## <a name="next-steps"></a>Następne kroki

- [Formularz rejestracji przyspieszania zapytań](https://aka.ms/adls/qa-preview-signup)    
- [Filtrowanie danych przy użyciu przyspieszania zapytań Azure Data Lake Storage (wersja zapoznawcza)](data-lake-storage-query-acceleration-how-to.md)
- [Informacje dotyczące języka SQL przyspieszania zapytań (wersja zapoznawcza)](query-acceleration-sql-reference.md)


