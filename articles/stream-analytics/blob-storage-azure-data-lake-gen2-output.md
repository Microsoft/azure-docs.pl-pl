---
title: Magazyn obiektów blob i Azure Data Lake dane wyjściowe Gen2 z Azure Stream Analytics
description: W tym artykule opisano magazyn obiektów blob i Azure Data Lake Gen 2 jako dane wyjściowe dla Azure Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 3ce22837da2ca30249b399a297b6188d950b1ea4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98935150"
---
# <a name="blob-storage-and-azure-data-lake-gen2-output-from-azure-stream-analytics"></a>Magazyn obiektów blob i Azure Data Lake dane wyjściowe Gen2 z Azure Stream Analytics

Data Lake Storage Gen2 sprawia, że usługa Azure Storage jest podstawą do tworzenia jezior danych przedsiębiorstwa na platformie Azure. Zaprojektowana od początku do obsługi wielu petabajtów informacji przy zachowaniu setek gigabitowej przepływności, Data Lake Storage Gen2 umożliwia łatwe zarządzanie ogromnymi ilościami danych. Podstawową częścią Data Lake Storage Gen2 jest dodanie hierarchicznej przestrzeni nazw do magazynu obiektów BLOB.

Usługa Azure Blob Storage oferuje ekonomiczne i skalowalne rozwiązanie służące do przechowywania dużych ilości danych bez struktury w chmurze. Aby zapoznać się z wprowadzeniem do usługi BLOB Storage i jej użycia, zobacz [przekazywanie, pobieranie i wyświetlanie listy obiektów BLOB za pomocą Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="output-configuration"></a>Konfiguracja wyjściowa

W poniższej tabeli wymieniono nazwy właściwości i ich opisy dotyczące tworzenia obiektów blob lub ADLS Gen2 danych wyjściowych.

| Nazwa właściwości       | Opis                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Alias danych wyjściowych        | Przyjazna nazwa używana w zapytaniach do kierowania danych wyjściowych zapytania do tego magazynu obiektów BLOB. |
| Konto magazynu     | Nazwa konta magazynu, do którego wysyłane są dane wyjściowe.               |
| Klucz konta magazynu | Klucz tajny skojarzony z kontem magazynu.                              |
| Kontener magazynu   | Grupowanie logiczne dla obiektów BLOB przechowywanych w usłudze Azure Blob service. Po przekazaniu obiektu BLOB do Blob service należy określić kontener dla tego obiektu BLOB. |
| Wzorzec ścieżki | Opcjonalny. Wzorzec ścieżki pliku używany do pisania obiektów BLOB w określonym kontenerze. <br /><br /> We wzorcu ścieżki można wybrać, aby użyć jednego lub większej liczby wystąpień zmiennych daty i godziny, aby określić częstotliwość zapisywania obiektów blob: <br /> {Date}, {Time} <br /><br />Możesz użyć niestandardowego partycjonowania obiektów blob, aby określić jedną niestandardową nazwę {Field} z danych zdarzenia do partycjonowania obiektów BLOB. Nazwa pola jest alfanumeryczna i może zawierać spacje, łączniki i podkreślenia. Ograniczenia dotyczące pól niestandardowych są następujące: <ul><li>W nazwach pól nie jest rozróżniana wielkość liter. Na przykład usługa nie może rozróżnić między kolumnami "ID" i kolumną "ID".</li><li>Zagnieżdżone pola są niedozwolone. Zamiast tego należy użyć aliasu w zapytaniu zadania do "Spłaszcz" pola.</li><li>Wyrażenia nie mogą być używane jako nazwa pola.</li></ul> <br />Ta funkcja umożliwia użycie niestandardowych konfiguracji specyfikatora formatu daty i godziny w ścieżce. Niestandardowe formaty daty i godziny muszą być określone pojedynczo, ujęte w słowo kluczowe {DateTime: \<specifier> }. Dozwolone dane wejściowe \<specifier> to rrrr, mm, m, DD, d, hh, H, mm, m, SS lub s. Słowo kluczowe {DateTime: \<specifier> } może być używane wiele razy w ścieżce, aby utworzyć niestandardową konfigurację daty/godziny. <br /><br />Przykłady: <ul><li>Przykład 1: Klaster1/dzienniki/{Date}/{Time}</li><li>Przykład 2: Klaster1/dzienniki/{Date}</li><li>Przykład 3: Klaster1/{client_id}/{Date}/{Time}</li><li>Przykład 4: Klaster1/{DateTime: SS}/{myField}, gdzie zapytanie jest: Wybierz dane. moje pole z danych wejściowych;</li><li>Przykład 5: Klaster1/Year = {DateTime: RRRR}/miesiąc = {DateTime: MM}/Day = {DateTime: DD}</ul><br />Sygnatura czasowa utworzonej struktury folderów następuje po UTC, a nie na czas lokalny. [System. timestamp](./stream-analytics-time-handling.md#choose-the-best-starting-time) to czas używany na potrzeby całego partycjonowania opartego na czasie.<br /><br />Nazwa pliku używa następującej konwencji: <br /><br />{Wzorzec prefiksu ścieżki}/schemaHashcode_Guid_Number. rozszerzenie<br /><br /> Tutaj identyfikator GUID reprezentuje unikatowy identyfikator przypisany do wewnętrznego składnika zapisywania, który został utworzony w celu zapisu w pliku obiektu BLOB. Liczba reprezentuje indeks bloku obiektu BLOB. <br /><br /> Przykładowe pliki wyjściowe:<ul><li>Dane wyjściowe/20170901/00/45434_gguid_1.csv</li>  <li>Dane wyjściowe/20170901/01/45434_gguid_1.csv</li></ul> <br />Aby uzyskać więcej informacji na temat tej funkcji, zobacz [niestandardowe Partycjonowanie danych wyjściowych obiektu blob Azure Stream Analytics](stream-analytics-custom-path-patterns-blob-storage-output.md). |
| Format daty | Opcjonalny. Jeśli token daty jest używany w ścieżce prefiksu, można wybrać format daty, w którym są zorganizowane pliki. Przykład: RRRR/MM/DD |
| Format czasu | Opcjonalny. Jeśli token czasu jest używany w ścieżce prefiksu, określ format czasu, w którym są zorganizowane pliki. Obecnie jedyną obsługiwaną wartością jest HH. |
| Format serializacji zdarzeń | Format serializacji danych wyjściowych. Obsługiwane są kod JSON, CSV, Avro i Parquet. |
|Minimalna ilość wierszy |Liczba minimalnych wierszy na partię. Dla Parquet każda partia utworzy nowy plik. Bieżąca wartość domyślna to 2 000 wierszy, a maksymalna dozwolona liczba wierszy to 10 000.|
|Maksymalny czas |Maksymalny czas oczekiwania na partię. Po upływie tego czasu partia zostanie zapisywana w danych wyjściowych, nawet jeśli nie spełniono wymagań dotyczących minimalnych wierszy. Bieżąca wartość domyślna to 1 minuta, a maksymalna dozwolona liczba to 2 godziny. Jeśli dane wyjściowe obiektu BLOB mają częstotliwość tworzenia wzorców ścieżki, czas oczekiwania nie może być wyższy niż zakres czasu partycji.|
| Encoding    | Jeśli używasz formatu CSV lub JSON, należy określić kodowanie. W tym momencie jedynym obsługiwanym formatem kodowania jest UTF-8. |
| Ogranicznik   | Dotyczy tylko serializacji woluminu CSV. Stream Analytics obsługuje wiele ograniczników na potrzeby serializowania danych woluminu CSV. Obsługiwane wartości to przecinek, średnik, spacja, tabulator i pionowy pasek. |
| Format      | Dotyczy tylko serializacji JSON. **Linia rozdzielona** określa, że dane wyjściowe są formatowane przy użyciu każdego obiektu JSON oddzielonego przez nowy wiersz. W przypadku wybrania opcji **rozdzielone linią** kod JSON jest odczytywany po jednym obiekcie naraz. Cała zawartość nie będzie prawidłowym kodem JSON. **Tablica** określa, że dane wyjściowe są formatowane jako tablica obiektów JSON. Ta tablica jest zamykana tylko wtedy, gdy zadanie zostało zatrzymane lub Stream Analytics zostało przeniesione do następnego przedziału czasu. Ogólnie rzecz biorąc, zalecane jest użycie kodu JSON rozdzielonego wierszem, ponieważ nie wymaga żadnej specjalnej obsługi, gdy plik wyjściowy jest nadal w trakcie zapisywania. |

## <a name="blob-output-files"></a>Pliki wyjściowe obiektów BLOB

W przypadku korzystania z usługi BLOB Storage jako danych wyjściowych w następujących przypadkach tworzony jest nowy plik:

* Jeśli rozmiar pliku przekracza maksymalną dozwoloną liczbę bloków (obecnie 50 000). Może nawiązać połączenie z maksymalną dozwoloną liczbą bloków bez osiągnięcia maksymalnego dozwolonego rozmiaru obiektu BLOB. Na przykład jeśli szybkość danych wyjściowych jest wysoka, można zobaczyć więcej bajtów na blok, a rozmiar pliku jest większy. Jeśli szybkość danych wyjściowych jest niska, każdy blok ma mniejszą ilość danych, a rozmiar pliku jest mniejszy.
* Jeśli istnieje zmiana schematu w danych wyjściowych, a format danych wyjściowych wymaga stałego schematu (CSV, Avro, parquet).
* Jeśli zadanie zostanie ponownie uruchomione zewnętrznie przez użytkownika, zatrzymując je i uruchamiając, lub wewnętrznie do konserwacji systemu lub odzyskiwania błędów.
* Jeśli zapytanie jest w pełni partycjonowane i tworzony jest nowy plik dla każdej partycji wyjściowej.
* Jeśli użytkownik usunie plik lub kontener konta magazynu.
* Jeśli dane wyjściowe są podzielone na partycje przy użyciu wzorca prefiksu ścieżki, a nowy obiekt BLOB jest używany podczas przenoszenia zapytania do kolejnej godziny.
* Jeśli dane wyjściowe są podzielone na partycje za pomocą pola niestandardowego, a dla klucza partycji zostanie utworzony nowy obiekt BLOB, jeśli nie istnieje.
* Jeśli dane wyjściowe są podzielone na partycje za pomocą pola niestandardowego, w którym Kardynalność klucza partycji przekracza 8 000, a dla klucza partycji tworzony jest nowy obiekt BLOB.

## <a name="partitioning"></a>Partycjonowanie

W przypadku klucza partycji Użyj tokenów {date} i {Time} z pól zdarzeń we wzorcu ścieżki. Wybierz format daty, na przykład RRRR/MM/DD, DD/MM/RRRR lub MM-DD-RRRR. HH jest używany w formacie czasu. Dane wyjściowe obiektu BLOB mogą być podzielone na partycje za pomocą jednego niestandardowego atrybutu zdarzenia {FieldName} lub {DateTime: \<specifier> }. Liczba składników zapisywania danych wyjściowych następuje po partycjonowaniu danych wejściowych dla w [pełni działania równoległegoych zapytań](stream-analytics-scale-jobs.md).

## <a name="output-batch-size"></a>Rozmiar partii wyjściowej

Maksymalny rozmiar komunikatu znajduje się w temacie [limity usługi Azure Storage](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). Maksymalny rozmiar bloku obiektu BLOB to 4 MB, a maksymalna liczba Bock obiektów BLOB to 50 000. |

## <a name="next-steps"></a>Następne kroki

* [Użyj tożsamości zarządzanej (wersja zapoznawcza) w celu uwierzytelnienia zadania Azure Stream Analytics na platformie Azure Blob Storage](blob-output-managed-identity.md)
* [Szybki start: tworzenie zadania usługi Stream Analytics przy użyciu witryny Azure Portal](stream-analytics-quick-create-portal.md)
