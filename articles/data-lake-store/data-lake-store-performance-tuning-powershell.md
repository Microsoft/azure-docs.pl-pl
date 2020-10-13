---
title: Dostrajanie wydajności Azure Data Lake Storage Gen1 — PowerShell
description: Wskazówki dotyczące poprawy wydajności podczas korzystania z Azure PowerShell z Azure Data Lake Storage Gen1.
author: stewu
ms.service: data-lake-store
ms.topic: how-to
ms.date: 01/09/2018
ms.author: stewu
ms.openlocfilehash: f5e6f6601a563a387476e4e2eaf353c8bef384ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85504699"
---
# <a name="performance-tuning-guidance-for-using-powershell-with-azure-data-lake-storage-gen1"></a>Wskazówki dotyczące dostrajania wydajności dotyczące korzystania z programu PowerShell z Azure Data Lake Storage Gen1

W tym artykule opisano właściwości, które można dostosować w celu uzyskania lepszej wydajności podczas pracy z Data Lake Storage Gen1mi przy użyciu programu PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="performance-related-properties"></a>Właściwości związane z wydajnością

| Właściwość            | Domyślne | Opis |
|---------------------|---------|-------------|
| PerFileThreadCount  | 10      | Ten parametr umożliwia wybór liczby wątków równoległych na potrzeby przekazywania lub pobierania każdego pliku. Ta liczba reprezentuje maksymalną liczbę wątków, które mogą być przydzieloną dla każdego pliku, ale możesz uzyskać mniejszą liczbę wątków w zależności od danego scenariusza (na przykład w przypadku przekazywania pliku o wartości 1 KB uzyskasz jeden wątek nawet w przypadku podawania 20 wątków).  |
| ConcurrentFileCount | 10      | Ten parametr jest przeznaczony konkretnie na potrzeby przekazywania lub pobierania folderów. Określa liczbę współbieżnych plików, które można przekazać lub pobrać. Ta liczba reprezentuje maksymalną liczbę współbieżnych plików, które można przekazać lub pobrać jednocześnie, ale możesz uzyskać mniejszą współbieżność w zależności od scenariusza (na przykład w przypadku przekazywania dwóch plików są odbierane dwa współbieżne pliki, nawet jeśli zostanie wyświetlony monit o 15). |

**Przykład:**

To polecenie umożliwia pobranie plików z Data Lake Storage Gen1 na dysk lokalny użytkownika przy użyciu 20 wątków na plik i 100 współbieżnych plików.

```PowerShell
Export-AzDataLakeStoreItem -AccountName "Data Lake Storage Gen1 account name" `
    -PerFileThreadCount 20 `
    -ConcurrentFileCount 100 `
    -Path /Powershell/100GB `
    -Destination C:\Performance\ `
    -Force `
    -Recurse
```

## <a name="how-to-determine-property-values"></a>Jak określić wartości właściwości

Następnym pytaniem, co może być, jest określenie wartości, która ma być dostępna dla właściwości związanych z wydajnością. Oto kilka użytecznych wskazówek.

* **Krok 1. określenie całkowitej liczby wątków** — Rozpocznij od obliczenia łącznej liczby wątków do użycia. Ogólnie rzecz biorąc, należy używać sześciu wątków dla każdego rdzenia fizycznego.

    `Total thread count = total physical cores * 6`

    **Przykład:**

    Przy założeniu, że polecenia programu PowerShell są uruchamiane z poziomu maszyny wirtualnej D14 o 16 rdzeniach

    `Total thread count = 16 cores * 6 = 96 threads`

* **Krok 2. Obliczanie PerFileThreadCount** — obliczamy nasze PerFileThreadCount na podstawie rozmiaru plików. W przypadku plików mniejszych niż 2,5 GB nie ma potrzeby zmiany tego parametru, ponieważ wartością domyślną jest 10. W przypadku plików o rozmiarze większym niż 2,5 GB należy używać 10 wątków jako podstawy dla pierwszych 2,5 GB i dodać 1 wątek dla każdego dodatkowego wzrostu 256-MB w rozmiarze pliku. Podczas kopiowania folderu zawierającego pliki o szerokim zakresie rozmiarów warto podzielić je na grupy złożone z plików o podobnym rozmiarze. Różne rozmiary plików mogą spowodować utratę optymalnej wydajności. Jeśli pogrupowanie plików o podobnym rozmiarze jest niemożliwe, wartość parametru PerFileThreadCount należy ustawić na podstawie największego rozmiaru pliku.

    `PerFileThreadCount = 10 threads for the first 2.5 GB + 1 thread for each additional 256 MB increase in file size`

    **Przykład:**

    Przy założeniu, że masz 100 plików z zakresu od 1 GB do 10 GB, użyjemy 10 GB jako największego rozmiaru pliku dla równania, który będzie wyglądać podobnie do poniższego.

    `PerFileThreadCount = 10 + ((10 GB - 2.5 GB) / 256 MB) = 40 threads`

* **Krok 3. Obliczanie ConcurrentFilecount** — Użyj całkowitej liczby wątków i PerFileThreadCount do obliczenia ConcurrentFilecount na podstawie następującego równania:

    `Total thread count = PerFileThreadCount * ConcurrentFileCount`

    **Przykład:**

    Na podstawie użytych przez nas przykładowych wartości

    `96 = 40 * ConcurrentFileCount`

    Wartość parametru **ConcurrentFileCount** wynosi zatem **2,4**, co możemy zaokrąglić do **2**.

## <a name="further-tuning"></a>Dalsze dostosowywanie

Ze względu na zakres rozmiarów plików, z jakimi można pracować, może być konieczne dalsze dostosowanie. Poprzednie obliczenie działa prawidłowo, jeśli wszystkie lub większość plików są większe i zbliżone do zakresu 10 GB. Jeśli natomiast istnieje wiele różnych rozmiarów plików, z czego wiele plików jest mniejszych, można zmniejszyć wartość parametru PerFileThreadCount. Dzięki zmniejszeniu wartości parametru PerFileThreadCount można zwiększyć wartość parametru ConcurrentFileCount. Dlatego jeśli założono, że większość naszych plików jest mniejsza w zakresie 5 GB, możemy ponownie wykonać nasze obliczenia:

`PerFileThreadCount = 10 + ((5 GB - 2.5 GB) / 256 MB) = 20`

**ConcurrentFileCount** to 96/20, czyli 4,8, zaokrąglone do **4**.

Te ustawienia można jeszcze bardziej dostosować, zwiększając lub zmniejszając wartość parametru **PerFileThreadCount** zależnie od rozkładu rozmiarów plików.

### <a name="limitation"></a>Ograniczenie

* **Liczba plików jest mniejsza niż wartość parametru ConcurrentFileCount**: jeśli liczba przekazywanych plików jest mniejsza niż obliczona wartość parametru **ConcurrentFileCount**, należy zmniejszyć wartość parametru **ConcurrentFileCount** tak, aby była równa liczbie plików. Przy użyciu pozostałych wątków można zwiększyć wartość parametru **PerFileThreadCount**.

* **Zbyt wiele wątków**: jeśli liczba wątków zostanie nadmiernie zwiększona bez zwiększenia rozmiaru klastra, istnieje ryzyko obniżonej wydajności. Podczas przełączania kontekstu na procesorze mogą wystąpić problemy z rywalizacją o zasoby.

* **Niewystarczająca współbieżność**: jeśli współbieżność nie jest wystarczająca, to klaster może być za mały. Można zwiększyć liczbę węzłów w klastrze, co zapewnia większą współbieżność.

* **Błędy ograniczania przepływności**: błędy ograniczania przepływności mogą wystąpić wówczas, gdy współbieżność jest zbyt wysoka. W przypadku błędów ograniczania przepływności należy albo zmniejszyć współbieżność, albo skontaktować się z nami.

## <a name="next-steps"></a>Następne kroki

* [Użyj Azure Data Lake Storage Gen1, aby uzyskać wymagania dotyczące danych Big Data](data-lake-store-data-scenarios.md) 
* [Zabezpieczanie danych w usłudze Data Lake Storage 1. generacji](data-lake-store-secure-data.md)
* [Użyj Azure Data Lake Analytics z Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Korzystanie z usługi Azure HDInsight z usługą Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

