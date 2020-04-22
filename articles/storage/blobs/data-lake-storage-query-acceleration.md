---
title: Akceleracja zapytań usługi Azure Data Lake Storage (wersja zapoznawcza)
description: Przyspieszenie zapytań (wersja zapoznawcza) to nowa funkcja usługi Azure Data Lake Storage, która umożliwia aplikacjom i strukturom analitycznym radykalną optymalizację przetwarzania danych przez pobranie tylko danych wymaganych do operacji przetwarzania.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.reviewer: jamesbak
ms.date: 04/21/2020
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 191a3280075403c8c5b57c5ffca1c7707d1ddb11
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81771822"
---
# <a name="azure-data-lake-storage-query-acceleration-preview"></a>Akceleracja zapytań usługi Azure Data Lake Storage (wersja zapoznawcza)

Przyspieszenie zapytań (wersja zapoznawcza) to nowa funkcja usługi Azure Data Lake Storage, która umożliwia aplikacjom i platformom analitycznym radykalną optymalizację przetwarzania danych przez pobranie tylko danych, które są potrzebne do wykonania danej operacji. Skraca to czas i moc obliczeniową, która jest wymagana do uzyskania krytycznego wglądu w przechowywane dane.

> [!NOTE]
> Funkcja przyspieszania kwerend jest dostępna w publicznej wersji zapoznawczej i jest dostępna w regionach Kanada Środkowa i Francja Central. Aby przejrzeć ograniczenia, zobacz artykuł [Znane problemy.](data-lake-storage-known-issues.md) Aby zarejestrować się w wersji zapoznawczej, zobacz [ten formularz](https://aka.ms/adls/qa-preview-signup).  

## <a name="overview"></a>Omówienie

Akceleracja zapytań akceptuje *predykaty* filtrowania i *rzuty kolumn,* które umożliwiają aplikacjom filtrowanie wierszy i kolumn w czasie odczytu danych z dysku. Tylko dane, które spełniają warunki predykatu są przesyłane za pośrednictwem sieci do aplikacji. Zmniejsza to opóźnienia sieci i koszty obliczeń.  

Za pomocą języka SQL można określić predykaty filtru wiersza i rzutowania kolumn w żądaniu przyspieszenia kwerendy. Żądanie przetwarza tylko jeden plik. W związku z tym zaawansowane funkcje relacyjne języka SQL, takie jak sprzężenia i grupowanie według agregatów, nie są obsługiwane. Akceleracja zapytań obsługuje dane sformatowane w formacie CSV i JSON jako dane wejściowe do każdego żądania.

Funkcja przyspieszania kwerend nie jest ograniczona do usługi Data Lake Storage (konta magazynu, na których włączono hierarchiczną przestrzeń nazw). Akceleracja kwerend jest całkowicie zgodna z obiektami blob na kontach magazynu, które **nie** mają włączonego hierarchicznego obszaru nazw. Oznacza to, że można osiągnąć takie samo zmniejszenie opóźnienia sieci i kosztów obliczeniowych podczas przetwarzania danych, które zostały już zapisane jako obiekty blob na kontach magazynu.

Na przykład sposobu używania akceleracji kwerend w aplikacji klienckiej zobacz [Filtrowanie danych przy użyciu akceleracji zapytań usługi Azure Data Lake.](data-lake-storage-query-acceleration-how-to.md)

## <a name="data-flow"></a>Przepływ danych

Na poniższym diagramie przedstawiono, jak typowa aplikacja używa akceleracji zapytań do przetwarzania danych.

> [!div class="mx-imgBorder"]
> ![Omówienie akceleracji kwerend](./media/data-lake-storage-query-acceleration/query-acceleration.png)

1. Aplikacja kliencka żąda danych pliku, określając predykaty i projekcje kolumn.

2. Przyspieszenie kwerend analizuje określoną kwerendę SQL i dystrybuuje pracę do analizowania i filtrowania danych.

3. Procesory odczytują dane z dysku, analizują dane przy użyciu odpowiedniego formatu, a następnie filtrują dane, stosując określone predykaty i rzuty kolumnowe.

4. Przyspieszenie kwerendy łączy fragmenty odpowiedzi do strumienia z powrotem do aplikacji klienckiej.

5. Aplikacja kliencka odbiera i analizuje odpowiedzi przesyłane strumieniowo. Aplikacja nie musi filtrować żadnych dodatkowych danych i może bezpośrednio zastosować żądane obliczenia lub transformację.

## <a name="better-performance-at-a-lower-cost"></a>Lepsza wydajność przy niższych kosztach

Przyspieszenie kwerendy optymalizuje wydajność, zmniejszając ilość danych, które są przesyłane i przetwarzane przez aplikację.

Aby obliczyć wartość zagregowane, aplikacje często pobierają **wszystkie** dane z pliku, a następnie przetwarzają i filtrują dane lokalnie. Analiza wzorców wejścia/wyjścia dla obciążeń analitycznych wynika, że aplikacje zwykle wymagają tylko 20% danych, które odczytują do wykonywania dowolnych obliczeń. Ta statystyka jest prawdziwa nawet po zastosowaniu technik, takich jak [przycinanie partycji](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-optimize-hive-query#hive-partitioning). Oznacza to, że 80% tych danych jest niepotrzebnie przesyłane przez sieć, analizowane i filtrowane przez aplikacje. Ten wzorzec, zasadniczo zaprojektowany w celu usunięcia niepotrzebnych danych, wiąże się ze znacznymi kosztami obliczeniowymi.  

Mimo że platforma Azure oferuje wiodącą w branży sieć, zarówno pod względem przepływności, jak i opóźnienia, niepotrzebne przesyłanie danych w tej sieci jest nadal kosztowne dla wydajności aplikacji. Odfiltrowanie niechcianych danych podczas żądania magazynu, akceleracja zapytań eliminuje ten koszt.

Ponadto obciążenie procesora CPU, który jest wymagany do analizowania i filtrowania niepotrzebnych danych wymaga aplikacji do aprowizowania większej liczby i większych maszyn wirtualnych, aby wykonać swoją pracę. Przenosząc to obciążenie obliczeniowe do przyspieszenia zapytań, aplikacje mogą osiągnąć znaczne oszczędności kosztów.

## <a name="applications-that-can-benefit-from-query-acceleration"></a>Aplikacje, które mogą korzystać z przyspieszenia zapytań

Akceleracja zapytań jest przeznaczona dla rozproszonych struktur analitycznych i aplikacji do przetwarzania danych. 

Struktury analizy rozproszonej, takie jak Apache Spark i Apache Hive, zawierają warstwę abstrakcji magazynu w ramach. Aparaty te obejmują również optymalizatory zapytań, które mogą zawierać wiedzę na temat możliwości podstawowej usługi We/Wy podczas określania optymalnego planu zapytań dla zapytań użytkowników. Te struktury zaczynają integrować przyspieszanie zapytań. W rezultacie użytkownicy tych struktur zobaczą lepsze opóźnienie kwerendy i niższy całkowity koszt własności bez konieczności wprowadzania jakichkolwiek zmian w kwerendach. 

Akceleracja zapytań jest również przeznaczona dla aplikacji do przetwarzania danych. Te typy aplikacji zazwyczaj wykonują przekształcenia danych na dużą skalę, które mogą nie prowadzić bezpośrednio do szczegółowych informacji analizy, więc nie zawsze używają ustalonych struktur analizy rozproszonej. Te aplikacje często mają bardziej bezpośredni związek z podstawową usługą magazynu, dzięki czemu mogą korzystać bezpośrednio z funkcji, takich jak przyspieszenie zapytań. 

Na przykład, jak aplikacja może integrować akcelerację zapytań, zobacz [Filtrowanie danych przy użyciu akceleracji zapytań usługi Azure Data Lake.](data-lake-storage-query-acceleration-how-to.md)

## <a name="pricing"></a>Cennik

Ze względu na zwiększone obciążenie obliczeń w ramach usługi Azure Data Lake Storage model cenowy do korzystania z akceleracji zapytań różni się od normalnego modelu transakcji usługi Azure Data Lake Storage. Przyspieszenie kwerendy pobiera koszt za ilość skanowanych danych, a także koszt ilości danych zwróconych do obiektu wywołującego.

Pomimo zmiany modelu rozliczeń, model cenowy akceleracji query jest przeznaczony do obniżenia całkowitego kosztu posiadania dla obciążenia, biorąc pod uwagę zmniejszenie znacznie droższych kosztów maszyn wirtualnych.

## <a name="next-steps"></a>Następne kroki

- [Formularz rejestracji akceleracji kwerend](https://aka.ms/adls/qa-preview-signup)    
- [Filtrowanie danych przy użyciu akceleracji zapytań usługi Azure Data Storage](data-lake-storage-query-acceleration-how-to.md)
- [Odwołanie do języka SQL akceleracji kwerendy (wersja zapoznawcza)](query-acceleration-sql-reference.md)
- Odwołanie do interfejsu API REST akceleracji kwerend



