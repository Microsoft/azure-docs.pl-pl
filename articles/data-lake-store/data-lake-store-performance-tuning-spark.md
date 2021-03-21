---
title: Dostrajanie wydajności — Spark z Azure Data Lake Storage Gen1
description: Poznaj wskazówki dotyczące dostrajania wydajności dla platformy Spark w usłudze Azure HDInsight i Azure Data Lake Storage Gen1.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 12/19/2016
ms.author: twooley
ms.openlocfilehash: 8a39e14b70827947687b7613b9ff86f18017ddfe
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97722508"
---
# <a name="performance-tuning-guidance-for-spark-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Wskazówki dotyczące dostrajania wydajności dla platformy Spark w usłudze HDInsight i Azure Data Lake Storage Gen1

Podczas dostrajania wydajności na platformie Spark należy wziąć pod uwagę liczbę aplikacji, które będą działać w klastrze. Domyślnie można uruchamiać cztery aplikacje jednocześnie w klastrze HDI (Uwaga: ustawienie domyślne może ulec zmianie). Możesz zdecydować się na użycie mniejszej liczby aplikacji, aby można było zastąpić ustawienia domyślne i używać więcej klastrów dla tych aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto Azure Data Lake Storage Gen1**. Aby uzyskać instrukcje dotyczące sposobu tworzenia takiego elementu, zobacz Wprowadzenie [do Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Klaster usługi Azure HDInsight** z dostępem do konta Data Lake Storage Gen1. Zobacz [Tworzenie klastra usługi HDInsight przy użyciu Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Upewnij się, że Pulpit zdalny dla klastra są włączone.
* **Uruchamianie klastra Spark na Data Lake Storage Gen1**. Aby uzyskać więcej informacji, zobacz [Korzystanie z klastra usługi HDInsight Spark do analizowania danych w Data Lake Storage Gen1](../hdinsight/spark/apache-spark-use-with-data-lake-store.md)
* **Wskazówki dotyczące dostrajania wydajności na Data Lake Storage Gen1**. Ogólne pojęcia dotyczące wydajności znajdują się w temacie [Data Lake Storage Gen1 wskazówki dotyczące dostrajania wydajności](./data-lake-store-performance-tuning-guidance.md) 

## <a name="parameters"></a>Parametry

W przypadku uruchamiania zadań platformy Spark poniżej przedstawiono najważniejsze ustawienia, które można dostrajać w celu zwiększenia wydajności Data Lake Storage Gen1:

* **NUM-wykonawcze** — liczba współbieżnych zadań, które można wykonać.

* Moduł **wykonawczy pamięci** — ilość pamięci przydzieloną do każdego wykonawcy.

* Program **wykonujący rdzenie** — liczbę rdzeni przypisanych do każdego wykonawcy.

**NUM-wykonawcy** Program NUM-wykonawczy ustawi maksymalną liczbę zadań, które mogą być uruchamiane równolegle. Rzeczywista liczba zadań, które mogą być uruchamiane równolegle, jest ograniczona przez zasoby pamięci i procesora CPU dostępne w klastrze.

**Wykonawca pamięci** Jest to ilość pamięci, która jest przydzielna do każdego wykonawcy. Pamięć wymagana dla każdego wykonawcy zależy od zadania. W przypadku złożonych operacji pamięć musi być wyższa. W przypadku prostych operacji, takich jak Odczyt i zapis, wymagania dotyczące pamięci będą mniejsze. Ilość pamięci dla każdego wykonawcy można wyświetlić w Ambari. W Ambari przejdź do platformy Spark i **Wyświetl kartę konfiguracje** .

Program **wykonujący rdzenie** Ustawia to ilość rdzeni używanych w ramach wykonawcy, który określa liczbę wątków równoległych, które mogą być uruchamiane na wykonawcę. Na przykład jeśli program wykonujący rdzenie = 2, każdy wykonawca może uruchomić 2 równoległe zadania w programie wykonującym. Wymagające rdzeni wykonawcze będą zależne od zadania. Duże zadania we/wy nie wymagają dużej ilości pamięci na zadanie, więc każdy wykonawca może obsłużyć więcej zadań równoległych.

Domyślnie dwie wirtualne rdzenie PRZĘDZy są definiowane dla każdego fizycznego rdzenia podczas uruchamiania platformy Spark w usłudze HDInsight. Ta liczba zapewnia dobry balans współbieżności i liczby przełączeń kontekstu z wielu wątków.

## <a name="guidance"></a>Wskazówki

Podczas uruchamiania obciążeń analitycznych platformy Spark w celu pracy z danymi w Data Lake Storage Gen1 zalecamy użycie najnowszej wersji usługi HDInsight w celu uzyskania najlepszej wydajności z Data Lake Storage Gen1. Gdy zadanie ma większą liczbę operacji we/wy, można skonfigurować pewne parametry w celu zwiększenia wydajności. Data Lake Storage Gen1 to wysoce skalowalna platforma magazynu, która może obsługiwać wysoką przepływność. Jeśli zadanie polega głównie na odczycie lub zapisie, zwiększenie współbieżności dla operacji we/wy do i z Data Lake Storage Gen1 może zwiększyć wydajność.

Istnieje kilka ogólnych sposobów zwiększania współbieżności zadań intensywnie korzystających z operacji we/wy.

**Krok 1. określenie, ile aplikacji jest uruchomionych w klastrze** — należy wiedzieć, ile aplikacji jest uruchomionych w klastrze, w tym dla bieżącego. Wartości domyślne dla poszczególnych ustawień platformy Spark zakładają, że istnieją 4 aplikacje jednocześnie uruchomione. W związku z tym dla każdej aplikacji będzie dostępny tylko 25% klastra. Aby uzyskać lepszą wydajność, można zastąpić wartości domyślne, zmieniając liczbę modułów wykonujących.

**Krok 2. Ustawianie pamięci programu wykonującego** — w pierwszej kolejności, w której ma zostać ustawiony moduł wykonawczy pamięci. Pamięć będzie zależna od zadania, które ma zostać uruchomione. Współbieżność można zwiększyć, przydzielając mniejszą ilość pamięci na wykonawcę. Jeśli podczas uruchamiania zadania widzisz wyjątki dotyczące braku pamięci, należy zwiększyć wartość tego parametru. Alternatywą jest uzyskanie większej ilości pamięci przy użyciu klastra o większej ilości pamięci lub zwiększenie rozmiaru klastra. Większa ilość pamięci umożliwi użycie większej liczby modułów wykonujących, co oznacza większą współbieżność.

**Krok 3. Ustawianie rdzeni wykonawców** — w przypadku obciążeń intensywnie korzystających z operacji we/wy, które nie mają złożonych działań, warto zacząć od dużej liczby rdzeni wykonawców, aby zwiększyć liczbę zadań równoległych na wykonawcę. Ustawienie "wykonawca-rdzenie na 4" jest dobrym uruchomieniem.

```console
executor-cores = 4
```

Zwiększenie liczby rdzeni programu wykonującego zapewnia większą równoległość, dzięki czemu można eksperymentować z różnymi rdzeniami wykonawcy. W przypadku zadań, które mają bardziej skomplikowane operacje, należy zmniejszyć liczbę rdzeni na wykonawcę. Jeśli liczba rdzeni programu wykonującego jest większa niż 4, wówczas wyrzucanie elementów bezużytecznych może stać się niewydajne i spadek wydajności.

**Krok 4. Określanie ilości pamięci przędzy w klastrze** — te informacje są dostępne w Ambari. Przejdź do PRZĘDZy i obejrzyj kartę Contigs. W tym oknie zostanie wyświetlona pamięć PRZĘDZy.
Pamiętaj, że w oknie można także zobaczyć domyślny rozmiar kontenera PRZĘDZy. Rozmiar kontenera PRZĘDZy jest taki sam jak pamięć na parametr wykonawcy.

Łączna ilość pamięci PRZĘDZy = węzły * pamięć PRZĘDZy na węzeł

**Krok 5. Obliczanie NUM-wykonawców**

**Obliczanie ograniczenia pamięci** — parametr NUM-wykonawczy jest ograniczany przez pamięć lub przez procesor CPU. Ograniczenie pamięci zależy od ilości dostępnej pamięci PRZĘDZy dla aplikacji. Zrób łączną ilość pamięci PRZĘDZy i Podziel ją przez program wykonujący pamięć. Ograniczenie musi być w nieskalowanej liczbie aplikacji, aby można było podzielić je na liczbę aplikacji.

Ograniczenie pamięci = (Łączna ilość pamięci/pamięci pakietu)/liczba aplikacji

**Oblicz ograniczenie procesora CPU** — ograniczenie procesora CPU jest obliczane jako łączne rdzenie wirtualne podzielone przez liczbę rdzeni na wykonawcę. Każdy rdzeń fizyczny ma 2 rdzenie wirtualne. Podobnie jak w przypadku ograniczeń pamięci, Podzielmy na liczbę aplikacji.

rdzenie wirtualne = (węzły w klastrze * # rdzeni fizycznych w węźle * 2) ograniczenie procesora = (łączna liczba rdzeni wirtualnych/liczba rdzeni na wykonawcę)/liczba aplikacji

**Ustaw NUM-wykonawcze** — parametr NUM-wykonawcs jest określany przez pobranie minimalnej wartości ograniczenia pamięci i ograniczenia procesora CPU. 

NUM-wykonawcze = min (całkowita liczba rdzeni wirtualnych/liczba rdzeni na wykonawcę, dostępna pamięć PRZĘDZy/moduł wykonawczy-pamięć) ustawienie większej liczby programów wykonujących nie musi zwiększać wydajności. Należy wziąć pod uwagę, że dodanie większej liczby modułów wykonujących spowoduje dodanie dodatkowych obciążeń dla każdego dodatkowego wykonawcy, co może spowodować spadek wydajności. Liczba modułów wykonujących jest ograniczona przez zasoby klastra.

## <a name="example-calculation"></a>Przykładowe obliczenie

Załóżmy, że obecnie masz klaster składający się z 8 D4v2 węzłów, na których uruchomiono dwie aplikacje, w tym te, które chcesz uruchomić.

**Krok 1. określenie, ile aplikacji jest uruchomionych w klastrze** — masz dwie aplikacje w klastrze, w tym te, które chcesz uruchomić.

**Krok 2. Ustawianie pamięci programu wykonującego** — w tym przykładzie określimy, że 6 GB pamięci podręcznej jest wystarczająca dla zadania intensywnie korzystających z operacji we/wy.

```console
executor-memory = 6GB
```

**Krok 3. Ustawianie rdzeni wykonawcy** — ponieważ jest to zadanie intensywnie korzystające z operacji we/wy, można ustawić liczbę rdzeni dla każdego wykonawcy na cztery. Ustawienie rdzeni na wykonawcę do większej niż cztery może spowodować problemy z wyrzucaniem elementów bezużytecznych.

```console
executor-cores = 4
```

**Krok 4. określenie ilości pamięci przędzy w klastrze** — Ambari, aby dowiedzieć się, że każdy D4V2 ma 25 GB pamięci przędzenia. Ponieważ istnieją 8 węzłów, dostępna pamięć PRZĘDZy jest mnożona przez 8.

Łączna ilość pamięci PRZĘDZy = węzły * PRZĘDZa pamięć * na węzeł Łączna ilość pamięci PRZĘDZy = 8 węzłów * 25 GB = 200 GB

**Krok 5. Obliczanie liczby programów wykonujących liczbę-wykonawcze** — parametr NUM-wykonawczy jest określany przez przejęcie minimalnej wartości ograniczenia pamięci i ograniczenia procesora CPU podzielone przez liczbę aplikacji uruchomionych na platformie Spark.

**Ograniczenie pamięci** — ograniczenie pamięci jest obliczane jako łączna ilość pamięci przędzy podzielona przez pamięć na wykonawcę.

Ograniczenie pamięci = (Łączna ilość pamięci lub pamięci programu do wykonania)/liczba aplikacji ograniczenie pamięci = (200 GB/6 GB)/2 ograniczenie pamięci = 16 (zaokrąglone) **Obliczanie ograniczenia procesora** — ograniczenie procesora CPU jest obliczane jako łączna liczba rdzeni przędzy, które są podzielone przez liczbę rdzeni na wykonawcę.

Rdzenie PRZĘDZy = węzły w klastrze * liczba rdzeni na węzeł * 2 rdzenie PRZĘDZy = 8 węzłów * 8 rdzeni na D14 * 2 = 128 ograniczenie procesora CPU = (łączna liczba rdzeni PRZĘDZy/liczba rdzeni na wykonawcę)/liczba ograniczeń procesora (128/4)/2 ograniczenie procesora CPU = 16

**Ustaw NUM-wykonawcze**

NUM-wykonawcy = min (ograniczenie pamięci, ograniczenie procesora) NUM-wykonawcy = min (16, 16) NUM-wykonawcy = 16