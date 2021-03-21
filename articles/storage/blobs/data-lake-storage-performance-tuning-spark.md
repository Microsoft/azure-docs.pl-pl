---
title: 'Dostrajanie wydajności: Spark, & HDInsight Azure Data Lake Storage Gen2 | Microsoft Docs'
description: Poznaj wskazówki dotyczące dostrajania wydajności platformy Spark za pomocą usługi Azure HDInsight i Azure Data Lake Storage Gen2.
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: c99d57ddd86ecff71c35ad6c0f2c2561e279b4b0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95912811"
---
# <a name="tune-performance-spark-hdinsight--azure-data-lake-storage-gen2"></a>Dostrajanie wydajności: Spark, & usługi HDInsight Azure Data Lake Storage Gen2

Podczas dostrajania wydajności na platformie Spark należy wziąć pod uwagę liczbę aplikacji, które będą działać w klastrze.  Domyślnie można uruchamiać 4 aplikacje jednocześnie w klastrze HDI (Uwaga: ustawienie domyślne może ulec zmianie).  Możesz zdecydować się na użycie mniejszej liczby aplikacji, aby można było zastąpić ustawienia domyślne i używać więcej klastrów dla tych aplikacji.  

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto Azure Data Lake Storage Gen2**. Aby uzyskać instrukcje dotyczące sposobu tworzenia takiego elementu, zobacz [Szybki Start: Tworzenie konta magazynu Azure Data Lake Storage Gen2](../common/storage-account-create.md).
* **Klaster usługi Azure HDInsight** z dostępem do konta Data Lake Storage Gen2. Zobacz [Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md). Upewnij się, że Pulpit zdalny dla klastra są włączone.
* **Uruchamianie klastra Spark na Data Lake Storage Gen2**.  Aby uzyskać więcej informacji, zobacz [Korzystanie z klastra usługi HDInsight Spark do analizowania danych w Data Lake Storage Gen2](../../hdinsight/spark/apache-spark-use-with-data-lake-store.md)
* **Wskazówki dotyczące dostrajania wydajności na Data Lake Storage Gen2**.  Ogólne pojęcia dotyczące wydajności znajdują się w temacie [Data Lake Storage Gen2 wskazówki dotyczące dostrajania wydajności](data-lake-storage-performance-tuning-guidance.md) 

## <a name="parameters"></a>Parametry

W przypadku uruchamiania zadań platformy Spark poniżej przedstawiono najważniejsze ustawienia, które można dostrajać w celu zwiększenia wydajności Data Lake Storage Gen2:

* **NUM-wykonawcze** — liczba współbieżnych zadań, które można wykonać.

* Moduł **wykonawczy pamięci** — ilość pamięci przydzieloną do każdego wykonawcy.

* Program **wykonujący rdzenie** — liczbę rdzeni przypisanych do każdego wykonawcy.                     

**NUM-wykonawcy** Program NUM-wykonawczy ustawi maksymalną liczbę zadań, które mogą być uruchamiane równolegle.  Rzeczywista liczba zadań, które mogą być uruchamiane równolegle, jest ograniczona przez zasoby pamięci i procesora CPU dostępne w klastrze.

**Wykonawca pamięci** Jest to ilość pamięci, która jest przydzielna do każdego wykonawcy.  Pamięć wymagana dla każdego wykonawcy zależy od zadania.  W przypadku złożonych operacji pamięć musi być wyższa.  W przypadku prostych operacji, takich jak Odczyt i zapis, wymagania dotyczące pamięci będą mniejsze.  Ilość pamięci dla każdego wykonawcy można wyświetlić w Ambari.  W Ambari przejdź do platformy Spark i Wyświetl kartę konfiguracje.  

Program **wykonujący rdzenie** Ustawia liczbę rdzeni używanych dla wykonawcy, który określa liczbę równoległych wątków, które mogą być uruchamiane na wykonawcę.  Na przykład jeśli program wykonujący rdzenie = 2, każdy wykonawca może uruchomić 2 równoległe zadania w programie wykonującym.  Wymagające rdzeni wykonawcze będą zależne od zadania.  Duże zadania we/wy nie wymagają dużej ilości pamięci na zadanie, więc każdy wykonawca może obsłużyć więcej zadań równoległych.

Domyślnie dwie wirtualne rdzenie PRZĘDZy są definiowane dla każdego fizycznego rdzenia podczas uruchamiania platformy Spark w usłudze HDInsight.  Ta liczba zapewnia dobry balans współbieżności i liczby przełączeń kontekstu z wielu wątków.  

## <a name="guidance"></a>Wskazówki

Podczas uruchamiania obciążeń analitycznych platformy Spark w celu pracy z danymi w Data Lake Storage Gen2 zalecamy użycie najnowszej wersji usługi HDInsight w celu uzyskania najlepszej wydajności z Data Lake Storage Gen2. Gdy zadanie ma większą liczbę operacji we/wy, można skonfigurować pewne parametry w celu zwiększenia wydajności.  Data Lake Storage Gen2 to wysoce skalowalna platforma magazynu, która może obsługiwać wysoką przepływność.  Jeśli zadanie polega głównie na odczycie lub zapisie, zwiększenie współbieżności dla operacji we/wy do i z Data Lake Storage Gen2 może zwiększyć wydajność.

Istnieje kilka ogólnych sposobów zwiększania współbieżności zadań intensywnie korzystających z operacji we/wy.

**Krok 1. określenie, ile aplikacji jest uruchomionych w klastrze** — należy wiedzieć, ile aplikacji jest uruchomionych w klastrze, w tym dla bieżącego.  Wartości domyślne dla poszczególnych ustawień platformy Spark zakładają, że istnieją 4 aplikacje jednocześnie uruchomione.  W związku z tym dla każdej aplikacji będzie dostępny tylko 25% klastra.  Aby uzyskać lepszą wydajność, można zastąpić wartości domyślne, zmieniając liczbę modułów wykonujących.  

**Krok 2. Ustawianie pamięci programu wykonującego** — w pierwszej kolejności, w której ma zostać ustawiony moduł wykonawczy pamięci.  Pamięć będzie zależna od zadania, które ma zostać uruchomione.  Współbieżność można zwiększyć, przydzielając mniejszą ilość pamięci na wykonawcę.  Jeśli podczas uruchamiania zadania widzisz wyjątki dotyczące braku pamięci, należy zwiększyć wartość tego parametru.  Alternatywą jest uzyskanie większej ilości pamięci przy użyciu klastra o większej ilości pamięci lub zwiększenie rozmiaru klastra.  Większa ilość pamięci umożliwi użycie większej liczby modułów wykonujących, co oznacza większą współbieżność.

**Krok 3. Ustawianie rdzeni wykonawców** — w przypadku obciążeń intensywnie korzystających z operacji we/wy, które nie mają złożonych działań, warto zacząć od dużej liczby rdzeni wykonawców, aby zwiększyć liczbę zadań równoległych na wykonawcę.  Ustawienie "wykonawca-rdzenie na 4" jest dobrym uruchomieniem.   

Wykonawca-rdzenie = 4

Zwiększenie liczby rdzeni programu wykonującego zapewnia większą równoległość, dzięki czemu można eksperymentować z różnymi rdzeniami wykonawcy.  W przypadku zadań, które mają bardziej skomplikowane operacje, należy zmniejszyć liczbę rdzeni na wykonawcę.  Jeśli liczba rdzeni programu wykonującego jest większa niż 4, wówczas wyrzucanie elementów bezużytecznych może stać się niewydajne i spadek wydajności.

**Krok 4. Określanie ilości pamięci przędzy w klastrze** — te informacje są dostępne w Ambari.  Przejdź do PRZĘDZy i Wyświetl kartę konfiguracje.  W tym oknie zostanie wyświetlona pamięć PRZĘDZy.  
Pamiętaj, że w oknie można także zobaczyć domyślny rozmiar kontenera PRZĘDZy.  Rozmiar kontenera PRZĘDZy jest taki sam jak pamięć na parametr wykonawcy.

Łączna ilość pamięci PRZĘDZy = węzły * pamięć PRZĘDZy na węzeł

**Krok 5. Obliczanie NUM-wykonawców**

**Obliczanie ograniczenia pamięci** — parametr NUM-wykonawczy jest ograniczany przez pamięć lub przez procesor CPU.  Ograniczenie pamięci zależy od ilości dostępnej pamięci PRZĘDZy dla aplikacji.  Należy pobrać łączną ilość pamięci PRZĘDZy i podzielić ją przez program wykonujący pamięć.  Ograniczenie musi być w nieskalowanej liczbie aplikacji, aby można było podzielić je na liczbę aplikacji.

Ograniczenie pamięci = (Łączna ilość pamięci/pamięci pakietu)/liczba aplikacji

**Oblicz ograniczenie procesora CPU** — ograniczenie procesora CPU jest obliczane jako łączne rdzenie wirtualne podzielone przez liczbę rdzeni na wykonawcę.  Każdy rdzeń fizyczny ma 2 rdzenie wirtualne.  Podobnie jak w przypadku ograniczenia pamięci, musimy podzielić na liczbę aplikacji.

- rdzenie wirtualne = (węzły w klastrze * # rdzeni fizycznych w węźle * 2)
- Ograniczenie procesora CPU = (łączna liczba rdzeni wirtualnych/liczba rdzeni na wykonawcę)/liczba aplikacji

**Ustaw NUM-wykonawcze** — parametr NUM-wykonawcs jest określany przez pobranie minimalnej wartości ograniczenia pamięci i ograniczenia procesora CPU. 

NUM-wykonawcy = min (całkowita liczba rdzeni wirtualnych/liczba rdzeni na wykonawcę, dostępna pamięć PRZĘDZy/wykonawca pamięci)

Ustawienie większej liczby programów wykonujących liczbę NUM-wykonawców nie musi zwiększyć wydajności.  Należy wziąć pod uwagę, że dodanie większej liczby modułów wykonujących spowoduje dodanie dodatkowych obciążeń dla każdego dodatkowego wykonawcy, co może spowodować spadek wydajności.  Liczba modułów wykonujących jest ograniczona przez zasoby klastra.    

## <a name="example-calculation"></a>Przykładowe obliczenie

Załóżmy, że obecnie klaster składa się z 8 D4v2 węzłów, w których jest uruchomionych 2 aplikacji, w tym tych, które mają być uruchamiane.  

**Krok 1. określenie, ile aplikacji jest uruchomionych w klastrze** — masz 2 aplikacje w klastrze, w tym te, które chcesz uruchomić.  

**Krok 2. Ustawianie pamięci programu wykonującego** — w tym przykładzie określimy, że 6 GB pamięci podręcznej jest wystarczająca dla zadania intensywnie korzystających z operacji we/wy.  

moduł wykonawczy-Memory = 6 GB

**Krok 3. Ustawianie rdzeni wykonawcy** — ponieważ jest to zadanie intensywnie korzystające z operacji we/wy, można ustawić liczbę rdzeni dla każdego wykonawcy na 4.  Ustawienie rdzeni na wykonawcę do większej niż 4 może spowodować problemy z wyrzucaniem elementów bezużytecznych.  

Wykonawca-rdzenie = 4

**Krok 4. określenie ilości pamięci przędzy w klastrze** — Ambari, aby dowiedzieć się, że każdy D4V2 ma 25 GB pamięci przędzenia.  Ponieważ istnieją 8 węzłów, dostępna pamięć PRZĘDZy jest mnożona przez 8.

- Łączna ilość pamięci PRZĘDZy = węzły * pamięć PRZĘDZy * na węzeł
- Łączna ilość pamięci PRZĘDZy = 8 węzłów * 25 GB = 200 GB

**Krok 5. Obliczanie liczby programów wykonujących liczbę-wykonawcze** — parametr NUM-wykonawczy jest określany przez przejęcie minimalnej wartości ograniczenia pamięci i ograniczenia procesora CPU podzielone przez liczbę aplikacji uruchomionych na platformie Spark.    

**Ograniczenie pamięci** — ograniczenie pamięci jest obliczane jako łączna ilość pamięci przędzy podzielona przez pamięć na wykonawcę.

- Ograniczenie pamięci = (Łączna ilość pamięci/pamięci pakietu)/liczba aplikacji
- Ograniczenie pamięci = (200 GB/6 GB)/2
- Ograniczenie pamięci = 16 (zaokrąglone)

**Obliczanie ograniczenia procesora CPU** — ograniczenie procesora CPU jest obliczane jako łączna liczba rdzeni przędzy podzielona przez liczbę rdzeni na wykonawcę.

- Rdzenie PRZĘDZy = węzły w klastrze * liczba rdzeni na węzeł * 2
- Rdzenie PRZĘDZy = 8 węzłów * 8 rdzeni na D14 * 2 = 128
- Ograniczenie procesora CPU = (łączna liczba rdzeni PRZĘDZy/liczba rdzeni na wykonawcę)/liczba aplikacji
- Ograniczenie procesora CPU = (128/4)/2
- Ograniczenie procesora CPU = 16

**Ustaw NUM-wykonawcze**

- NUM-wykonawcy = min (ograniczenie pamięci, ograniczenie procesora CPU)
- NUM-wykonawcy = min (16, 16)
- Liczba modułów wykonujących = 16