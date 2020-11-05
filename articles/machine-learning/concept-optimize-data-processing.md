---
title: Optymalizowanie przetwarzania danych
titleSuffix: Azure Machine Learning
description: Zapoznaj się z najlepszymi rozwiązaniami dotyczącymi optymalizowania szybkości przetwarzania danych oraz tego, jakie integracje Azure Machine Learning obsługują przetwarzanie danych na dużą skalę.
services: machine-learning
ms.service: machine-learning
ms.author: sgilley
author: sdgilley
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 06/26/2020
ms.custom: data4ml
ms.openlocfilehash: 77edac14ef13901725eed656835e1a937d4f4ddf
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360823"
---
# <a name="optimize-data-processing-with-azure-machine-learning"></a>Optymalizowanie przetwarzania danych za pomocą Azure Machine Learning

Ten artykuł zawiera informacje o najlepszych rozwiązaniach ułatwiających Optymalizowanie szybkości przetwarzania danych lokalnie i na dużą skalę.

Azure Machine Learning jest zintegrowana z pakietami i platformami Open Source na potrzeby przetwarzania danych. Korzystając z tych integracji i stosując zalecenia dotyczące najlepszych rozwiązań w tym artykule, można poprawić szybkość przetwarzania danych zarówno lokalnie, jak i w odpowiedniej skali.

## <a name="parquet-and-csv-file-formats"></a>Parquet i formaty plików CSV

Pliki z wartościami rozdzielanymi przecinkami (CSV) są typowymi formatami plików na potrzeby przetwarzania danych. Jednak formaty plików Parquet są zalecane w przypadku zadań uczenia maszynowego.

[Pliki Parquet](https://parquet.apache.org/) przechowują dane w formacie binarnym kolumnowym. Ten format jest przydatny, jeśli trzeba podzielić dane na wiele plików. Format ten umożliwia również określanie odpowiednich pól dla eksperymentów uczenia maszynowego. Zamiast odczytywania plików danych o 20 GB, można zmniejszyć obciążenie danych, wybierając odpowiednie kolumny, aby szkolić model ML. Pliki Parquet można również skompresować, aby zminimalizować moc przetwarzania i zajmują mniej miejsca.

Pliki CSV są często używane do importowania i eksportowania danych, ponieważ są one łatwe do edytowania i odczytywania w programie Excel. Dane w CSV są przechowywane jako ciągi w formacie opartym na wierszach, a pliki można skompresować, aby zmniejszyć obciążenie transferów danych. Nieskompresowany CSV może wzrosnąć o współczynnik około 2-10, a skompresowany CSV może jeszcze bardziej zwiększyć. Tak więc woluminy CSV 5 GB w pamięci rozszerzają się do rozmiaru 8 GB pamięci RAM na maszynie. Takie zachowanie kompresji może zwiększyć opóźnienia transferu danych, co nie jest idealne, jeśli masz duże ilości danych do przetworzenia. 

## <a name="pandas-dataframe"></a>Ramka dataPandas

[Pandas dataframes](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html) są często używane na potrzeby manipulowania i analizowania danych. `Pandas` sprawdza się najlepiej w przypadku rozmiarów danych mniejszych niż 1 GB, ale czasy przetwarzania dla `pandas` ramek dataframes spowalniają się, gdy rozmiar plików osiągnie około 1 GB. To spowolnienie wynika z faktu, że rozmiar danych w magazynie nie jest taki sam jak rozmiar danych w ramce Dataframe. Na przykład dane w plikach CSV mogą rozszerzać do 10 razy w ramce Dataframe, więc plik CSV o pojemności 1 GB może być 10 GB w ramce Dataframe.

`Pandas` jest jednowątkowy, co oznacza, że operacje są wykonywane pojedynczo na pojedynczym procesorze CPU. Możesz łatwo zrównoleglanie obciążenia na wiele wirtualnych procesorów CPU w jednym Azure Machine Learning wystąpienia obliczeniowego z pakietami, takimi jak [Modin](https://modin.readthedocs.io/en/latest/) , które są zawijane `Pandas` przy użyciu rozproszonego zaplecza.

Aby zrównoleglanie swoje zadania za pomocą `Modin` i [Dask](https://dask.org), po prostu zmień ten wiersz kodu `import pandas as pd` na `import modin.pandas as pd` .

## <a name="dataframe-out-of-memory-error"></a>Ramka danych: Wystąpił błąd braku pamięci 

Zwykle błąd *braku pamięci* występuje, gdy ramka danych zostanie rozwinięta nad DOSTĘPną pamięcią RAM na komputerze. Pojęcie to dotyczy również struktury rozproszonej, takiej jak `Modin` lub `Dask` .  Oznacza to, że operacja próbuje załadować ramkę danych w pamięci na każdym węźle w klastrze, ale nie jest dostępna wystarczająca ilość pamięci RAM.

Jednym z rozwiązań jest zwiększenie ilości pamięci RAM w celu dopasowania jej do danych w pamięci. Zalecamy, aby rozmiar obliczeń i moc obliczeniowa zawierały dwa razy większy rozmiar pamięci RAM. Dlatego jeśli ramka danych ma rozmiar 10 GB, użyj obiektu docelowego obliczeń z co najmniej 20 GB pamięci RAM, aby upewnić się, że ramka danych może wygodnie dopasować się do pamięci i przetworzyć. 

W przypadku wielu wirtualnych procesorów CPU vCPU należy pamiętać, że jedną partycję można wygodnie dopasować do pamięci RAM, każdy vCPU może mieć na komputerze. Oznacza to, że jeśli masz 16 GB pamięci RAM 4 procesorów wirtualnych vCPU, chcesz mieć około 2 GB ramek danych dla każdego vCPUu.

### <a name="local-vs-remote"></a>Lokalna a zdalna

Niektóre polecenia Pandas Dataframe mogą działać szybciej podczas pracy na komputerze lokalnym i na zdalnej maszynie wirtualnej, która została zainicjowana przy użyciu Azure Machine Learning. Na komputerze lokalnym jest zwykle włączony plik stronicowania, który umożliwia załadowanie większej ilości pamięci fizycznej, czyli dysku twardego, który jest używany jako rozszerzenie ilości pamięci RAM. Obecnie Azure Machine Learning maszyny wirtualne są uruchamiane bez pliku stronicowania, w związku z tym można ładować tylko tyle danych, ile jest dostępna fizyczna pamięć RAM. 

W przypadku zadań intensywnie korzystających z obliczeń zalecamy wybranie większej maszyny wirtualnej w celu zwiększenia szybkości przetwarzania.

Dowiedz się więcej o [dostępnych seriach i rozmiarach maszyn wirtualnych](concept-compute-target.md#supported-vm-series-and-sizes) dla Azure Machine Learning. 

W przypadku specyfikacji pamięci RAM Zobacz odpowiednie strony serii maszyn wirtualnych, takie jak [Dv2-Dsv2 Series](../virtual-machines/dv2-dsv2-series-memory.md) lub [NC Series](../virtual-machines/nc-series.md).

### <a name="minimize-cpu-workloads"></a>Minimalizacja obciążeń procesora CPU

Jeśli nie możesz dodać więcej pamięci RAM do maszyny, możesz zastosować następujące techniki, aby zminimalizować obciążenia procesora i zoptymalizować czas przetwarzania. Te zalecenia dotyczą zarówno systemów pojedynczych, jak i rozproszonych.

Technika | Opis
----|----
Kompresja | Użyj innej reprezentacji danych w sposób, który używa mniejszej ilości pamięci i nie wpływa znacząco na wyniki obliczeń.<br><br>*Przykład:* Zamiast przechowywać wpisy jako ciąg z około 10 bajtów lub więcej na wpis, należy przechowywać je jako wartość logiczną, wartość true lub false, którą można przechowywać w 1 bajcie.
Segmentu | Ładowanie danych do pamięci w podzestawach (fragmenty), przetwarzanie danych po jednym podzbiorze w czasie lub wiele podzestawów równoległych. Ta metoda działa najlepiej, jeśli trzeba przetwarzać wszystkie dane, ale nie trzeba ładować wszystkich danych do pamięci jednocześnie. <br><br>*Przykład:* Zamiast przetwarzać jednocześnie pełnych danych roku, ładować i Przetwarzaj dane miesięcznie.
Indeksowanie | Zastosowanie i użycie indeksu — podsumowanie informujące o tym, gdzie można znaleźć interesujące Cię dane. Indeksowanie jest przydatne, gdy trzeba używać tylko podzbioru danych, a nie do pełnego zestawu<br><br>*Przykład:* Jeśli masz pełny rok danych sprzedaży posortowanych według miesiąca, indeks pomaga szybko wyszukać żądany miesiąc, który ma zostać przetworzony.

## <a name="scale-data-processing"></a>Skalowanie przetwarzania danych

Jeśli poprzednie zalecenia są niewystarczające i nie możesz uzyskać maszyny wirtualnej, która pasuje do danych, możesz 

* Użyj struktury, takiej jak `Spark` lub `Dask` , aby przetworzyć dane za mało pamięci. W przypadku tej opcji ramka Dataframe jest ładowana do partycji RAM przez partycję i przetworzoną, a końcowy wynik jest zbierany na końcu.  

* Skalowanie w poziomie do klastra przy użyciu rozproszonej struktury. W tej opcji obciążenia przetwarzania danych są dzielone i przetwarzane na wielu procesorach, które pracują równolegle, wraz z końcowym wynikiem zebranym na końcu.

### <a name="recommended-distributed-frameworks"></a>Zalecane platformy rozproszone

W poniższej tabeli zaleca się dystrybuowanie struktur zintegrowanych z Azure Machine Learning w oparciu o preferencję kodu lub rozmiar danych.

Środowisko lub rozmiar danych | Zalecenie
------|------
Jeśli znasz już program `Pandas`| `Modin` lub `Dask` ramka danych
Jeśli wolisz `Spark` | `PySpark`
Dla danych mniejszych niż 1 GB | `Pandas` lokalne **lub** zdalne wystąpienie obliczeniowe Azure Machine Learning
Dla danych większych niż 10 GB| Przejdź do klastra przy użyciu `Ray` , `Dask` lub `Spark`

Możesz tworzyć `Dask` klastry w klastrze obliczeniowym Azure ml przy użyciu pakietu [dask-cloudprovider](https://cloudprovider.dask.org/en/latest/#azure) . Lub można uruchomić `Dask` lokalnie w wystąpieniu obliczeniowym.

## <a name="next-steps"></a>Następne kroki

* [Opcje pozyskiwania danych z Azure Machine Learning](concept-data-ingestion.md).
* [Tworzenie i Rejestrowanie zestawów danych](how-to-create-register-datasets.md).
