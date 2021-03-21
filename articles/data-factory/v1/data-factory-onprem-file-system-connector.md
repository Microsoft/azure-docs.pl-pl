---
title: Kopiowanie danych do/z systemu plików przy użyciu Azure Data Factory
description: Informacje o kopiowaniu danych do i z lokalnego systemu plików przy użyciu Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 9acd2adb68347978bb123d2239eedbb3e5595bc3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100387397"
---
# <a name="copy-data-to-and-from-an-on-premises-file-system-by-using-azure-data-factory"></a>Kopiowanie danych do i z lokalnego systemu plików przy użyciu Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](data-factory-onprem-file-system-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-file-system.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Łącznik systemu plików w wersji 2](../connector-file-system.md).


W tym artykule wyjaśniono, jak używać działania kopiowania w Azure Data Factory do kopiowania danych do/z lokalnego systemu plików. Jest on używany w artykule dotyczącym [przenoszenia danych](data-factory-data-movement-activities.md) , który przedstawia ogólne omówienie przenoszenia danych za pomocą działania kopiowania.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
Dane **z lokalnego systemu plików** można kopiować do następujących magazynów danych:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Dane z następujących magazynów danych można kopiować **do lokalnego systemu plików**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Działanie kopiowania nie usuwa pliku źródłowego po pomyślnym skopiowaniu do miejsca docelowego. Jeśli musisz usunąć plik źródłowy po pomyślnej kopii, Utwórz niestandardowe działanie, aby usunąć plik i użyć działania w potoku.

## <a name="enabling-connectivity"></a>Włączanie łączności
Data Factory obsługuje łączenie z lokalnym systemem plików i z niego za pośrednictwem **bramy zarządzanie danymi Gateway**. Aby usługa Data Factory mogła łączyć się z dowolnym obsługiwanym lokalnym magazynem danych, w tym systemem plików, należy zainstalować bramę Zarządzanie danymi w środowisku lokalnym. Aby dowiedzieć się więcej na temat bramy Zarządzanie danymi i instrukcje krok po kroku dotyczące konfigurowania bramy, zobacz [przenoszenie danych między źródłami lokalnymi i chmurą przy użyciu bramy zarządzanie danymi Gateway](data-factory-move-data-between-onprem-and-cloud.md). Poza bramą Zarządzanie danymi nie trzeba instalować żadnych innych plików binarnych, aby komunikować się z lokalnym systemem plików i z niego. Należy zainstalować bramę Zarządzanie danymi i korzystać z niej nawet wtedy, gdy system plików znajduje się na maszynie wirtualnej IaaS platformy Azure. Aby uzyskać szczegółowe informacje o bramie, zobacz [Zarządzanie danymi Gateway](data-factory-data-management-gateway.md).

Aby użyć udziału plików systemu Linux, zainstaluj program [Samba](https://www.samba.org/) na serwerze z systemem Linux i zainstaluj bramę zarządzanie danymi na serwerze z systemem Windows. Instalowanie bramy Zarządzanie danymi na serwerze z systemem Linux nie jest obsługiwane.

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok z działaniem kopiowania, które przenosi dane do/z systemu plików przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem utworzenia potoku jest użycie **Kreatora kopiowania**. Zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) na potrzeby szybkiego instruktażu dotyczącego tworzenia potoku przy użyciu Kreatora kopiowania danych.

Do utworzenia potoku można także użyć następujących narzędzi: **Visual Studio**, **Azure PowerShell**, **szablon Azure Resource Manager**, interfejs API **platformy .NET** i **interfejs API REST**. Aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoku za pomocą działania kopiowania, zobacz [Samouczek dotyczący działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Niezależnie od tego, czy używasz narzędzi, czy interfejsów API, wykonaj następujące kroki, aby utworzyć potok służący do przenoszenia danych ze źródłowego magazynu danych do magazynu danych ujścia:

1. Utwórz **fabrykę danych**. Fabryka danych może zawierać jeden lub więcej potoków.
2. Utwórz **połączone usługi** , aby połączyć magazyny danych wejściowych i wyjściowych z fabryką danych. Na przykład w przypadku kopiowania danych z usługi Azure Blob Storage do lokalnego systemu plików należy utworzyć dwie połączone usługi, aby połączyć lokalny system plików i konto usługi Azure Storage z fabryką danych. Aby uzyskać właściwości połączonej usługi, które są specyficzne dla lokalnego systemu plików, zobacz sekcję [Właściwości połączonej usługi](#linked-service-properties) .
3. Utwórz **zestawy** danych, aby reprezentować dane wejściowe i wyjściowe dla operacji kopiowania. W przykładzie opisanym w ostatnim kroku utworzysz zestaw danych, aby określić kontener obiektów blob i folder zawierający dane wejściowe. Ponadto utworzysz kolejny zestaw danych, aby określić folder i nazwę pliku (opcjonalnie) w systemie plików. Dla właściwości zestawu danych, które są specyficzne dla lokalnego systemu plików, zobacz sekcję [Właściwości zestawu danych](#dataset-properties) .
4. Utwórz **potok** z działaniem kopiowania, które pobiera zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe. W powyższym przykładzie użyto BlobSource jako źródła i FileSystemSink jako ujścia dla działania kopiowania. Podobnie w przypadku kopiowania z lokalnego systemu plików na platformę Azure Blob Storage należy użyć funkcji FileSystemSource i wartość blobsink w działaniu kopiowania. Aby uzyskać właściwości działania kopiowania specyficzne dla lokalnego systemu plików, zobacz sekcję [właściwości działania kopiowania](#copy-activity-properties) . Aby uzyskać szczegółowe informacje na temat używania magazynu danych jako źródła lub ujścia, kliknij link w poprzedniej sekcji dla magazynu danych.

Gdy używasz Kreatora, definicje JSON dla tych Data Factory jednostek (połączone usługi, zestawy danych i potok) są automatycznie tworzone. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API .NET), należy zdefiniować te Data Factory jednostki przy użyciu formatu JSON.  Przykłady zawierające definicje JSON dla Data Factory jednostek, które są używane do kopiowania danych do/z systemu plików, zobacz sekcję [przykłady JSON](#json-examples-for-copying-data-to-and-from-file-system) w tym artykule.

Poniższe sekcje zawierają szczegółowe informacje na temat właściwości JSON, które są używane do definiowania jednostek Data Factory specyficznych dla systemu plików:

## <a name="linked-service-properties"></a>Właściwości połączonej usługi
Lokalny system plików można połączyć z fabryką danych Azure przy użyciu połączonej usługi **lokalnego serwera plików** . Poniższa tabela zawiera opisy elementów JSON, które są specyficzne dla połączonej usługi lokalnego serwera plików.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| typ |Upewnij się, że Właściwość Type ma wartość **OnPremisesFileServer**. |Tak |
| host |Określa ścieżkę katalogu głównego folderu, który ma zostać skopiowany. Użyj znaku ucieczki "\" dla znaków specjalnych w ciągu. Przykłady można znaleźć w temacie [przykładowe połączone usługi i zestawy danych](#sample-linked-service-and-dataset-definitions) . |Tak |
| userid |Określ identyfikator użytkownika, który ma dostęp do serwera. |Nie (w przypadku wybrania opcji encryptedCredential) |
| hasło |Określ hasło użytkownika (UserID). |Nie (w przypadku wybrania opcji encryptedCredential |
| encryptedCredential |Określ zaszyfrowane poświadczenia, które można uzyskać, uruchamiając polecenie cmdlet New-AzDataFactoryEncryptValue. |Nie (Jeśli wybierzesz opcję określenia identyfikatora użytkownika i hasła w postaci zwykłego tekstu) |
| gatewayName |Określa nazwę bramy, która Data Factory powinna być używana do nawiązywania połączenia z lokalnym serwerem plików. |Tak |


### <a name="sample-linked-service-and-dataset-definitions"></a>Przykładowa połączona usługa i definicje zestawu danych
| Scenariusz | Definicja hosta w połączonej usłudze | folderPath w definicji zestawu danych |
| --- | --- | --- |
| Folder lokalny na maszynie Zarządzanie danymi bramy: <br/><br/>Przykłady: D: \\ \* lub D:\folder\subfolder\\\* |D: \\ \\ (for Zarządzanie danymi Gateway 2,0 i nowsze wersje) <br/><br/> localhost (dla wcześniejszych wersji niż Zarządzanie danymi Gateway 2,0) |.\\\\ lub \\ \\ podfolder folderu (dla Zarządzanie danymi Gateway 2,0 i nowsze wersje) <br/><br/>D: \\ \\ lub d: podfolder \\ \\ folderu \\ \\ (dla wersji bramy poniżej 2,0) |
| Zdalny folder udostępniony: <br/><br/>Przykłady: \\ \\ \\ udział \\ \* /folder \\ \\ \\ udziału \\ \\ w udziale\\\* |\\\\\\\\\\ \\ Udostępnij udział |.\\\\ lub \\ \\ podfolder folderu |

>[!NOTE]
>Podczas tworzenia za pośrednictwem interfejsu użytkownika nie trzeba wprowadzać podwójnego ukośnika odwrotnego ( `\\` ) w taki sposób, jak w przypadku kodu JSON, należy określić pojedynczy ukośnik odwrotny.

### <a name="example-using-username-and-password-in-plain-text"></a>Przykład: używanie nazwy użytkownika i hasła w postaci zwykłego tekstu

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

### <a name="example-using-encryptedcredential"></a>Przykład: korzystanie z encryptedcredential

```JSON
{
  "Name": " OnPremisesFileServerLinkedService ",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "D:\\",
      "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
      "gatewayName": "mygateway"
    }
  }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby zapoznać się z pełną listą sekcji i właściwości, które są dostępne do definiowania zestawów danych, zobacz [Tworzenie zestawów danych](data-factory-create-datasets.md). Sekcje, takie jak struktura, dostępność i zasady JSON zestawu danych, są podobne dla wszystkich typów zestawów danych.

Sekcja typeProperties jest inna dla każdego typu zestawu danych. Zawiera informacje, takie jak lokalizacja i format danych w magazynie danych. Sekcja typeProperties dla zestawu **danych typu "** DataSet" ma następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| folderPath |Określa ścieżkę podrzędną do folderu. Użyj znaku ucieczki " \' dla znaków specjalnych w ciągu. Filtr symboli wieloznacznych nie jest obsługiwany. Przykłady można znaleźć w temacie [przykładowe połączone usługi i zestawy danych](#sample-linked-service-and-dataset-definitions) .<br/><br/>Możesz połączyć tę właściwość z **partitionBy** , aby mieć ścieżki folderu na podstawie daty rozpoczęcia/zakończenia wycinka. |Tak |
| fileName |Określ nazwę pliku w **folderPath** , jeśli chcesz, aby tabela odnosiła się do określonego pliku w folderze. Jeśli nie określisz żadnej wartości dla tej właściwości, tabela wskazuje wszystkie pliki w folderze.<br/><br/>Jeśli nie określono **nazwy pliku** dla wyjściowego zestawu danych, a **preserveHierarchy** nie jest określona w ujścia aktywności, nazwa wygenerowanego pliku ma następujący format: <br/><br/>`Data.<Guid>.txt` (Przykład: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Nie |
| fileFilter |Określ filtr, który ma być używany do wybierania podzbioru plików w folderPath, a nie wszystkich plików. <br/><br/>Dozwolone wartości to: `*` (wiele znaków) i `?` (pojedynczy znak).<br/><br/>Przykład 1: "fileFilter": "*. log"<br/>Przykład 2: "fileFilter": 2014-1-?. zawierającego<br/><br/>Należy zauważyć, że fileFilter ma zastosowanie do wejściowego zestawu danych. |Nie |
| partitionedBy |Możesz użyć partitionedBy, aby określić dynamiczny folderPath/fileName dla danych szeregów czasowych. Przykładem jest folderPath sparametryzowane dla każdej godziny danych. |Nie |
| format | Obsługiwane są następujące typy formatów: **TextFormat**, **formatu jsonformat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw właściwość **Type** w polu Format na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz [format tekstowy](data-factory-supported-file-and-compression-formats.md#text-format), [Format JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Format Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Format Orc](data-factory-supported-file-and-compression-formats.md#orc-format)i sekcje [formatu Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Jeśli chcesz **skopiować pliki** między magazynami opartymi na plikach (kopia binarna), Pomiń sekcję format w definicjach zestawu danych wejściowych i wyjściowych. |Nie |
| kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to: **gzip**, **Wklęśnięcie**, **BZip2** i **ZipDeflate**. Obsługiwane poziomy to: **optymalne** i **najszybszy**. Zobacz [formaty plików i kompresji w Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |

> [!NOTE]
> Nie można jednocześnie używać fileName i fileFilter.

### <a name="using-partitionedby-property"></a>Używanie właściwości partitionedBy
Jak wspomniano w poprzedniej sekcji, można określić dynamiczne folderPath i nazwa pliku dla danych szeregów czasowych z właściwością **partitionedBy** , [funkcjami Data Factory i zmiennymi systemowymi](data-factory-functions-variables.md).

Aby poznać więcej szczegółów na temat zestawów danych, planowania i wycinków szeregów czasowych, zobacz [Tworzenie zestawów danych](data-factory-create-datasets.md), [Planowanie i wykonywanie](data-factory-scheduling-and-execution.md)oraz [Tworzenie potoków](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>Przykład 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

W tym przykładzie {Slice} jest zastępowana wartością zmiennej systemowej Data Factory parametru slicestart w formacie (YYYYMMDDHH). Parametru slicestart odnosi się do czasu rozpoczęcia wycinka. FolderPath różni się dla każdego wycinka. Na przykład: wikidatagateway/wikisampledataout/2014100103 lub wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Przykład 2:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
[
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```

W tym przykładzie rok, miesiąc, dzień i czas parametru slicestart są wyodrębniane do oddzielnych zmiennych, których właściwości folderPath i fileName używają.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania działań, zobacz artykuł [Tworzenie potoków](data-factory-create-pipelines.md) . Właściwości, takie jak nazwa, opis, wejściowe i wyjściowe zestawy danych, oraz zasady są dostępne dla wszystkich typów działań. Natomiast właściwości dostępne w sekcji **typeProperties** działania różnią się w zależności od typu działania.

W przypadku działania kopiowania różnią się w zależności od typów źródeł i ujścia. Jeśli przenosisz dane z lokalnego systemu plików, należy ustawić typ źródła w działaniu kopiowania na **FileSystemSource**. Podobnie, Jeśli przenosisz dane do lokalnego systemu plików, należy ustawić typ ujścia w działaniu kopiowania na **FileSystemSink**. Ta sekcja zawiera listę właściwości obsługiwanych przez FileSystemSource i FileSystemSink.

**FileSystemSource** obsługuje następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| rozpoznawania |Wskazuje, czy dane są odczytane cyklicznie z podfolderów, czy tylko z określonego folderu. |Prawda, FAŁSZ (wartość domyślna) |Nie |

**FileSystemSink** obsługuje następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| copyBehavior |Definiuje zachowanie kopiowania, gdy źródłem jest BlobSource lub system plików. |**PreserveHierarchy:** Zachowuje hierarchię plików w folderze docelowym. Oznacza to, że ścieżka względna pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna pliku docelowego do folderu docelowego.<br/><br/>**FlattenHierarchy:** Wszystkie pliki z folderu źródłowego są tworzone przy użyciu pierwszego poziomu folderu docelowego. Pliki docelowe są tworzone z automatycznie wygenerowaną nazwą.<br/><br/>**MergeFiles:** Scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku/obiektu BLOB jest określona, scalona nazwa pliku jest podaną nazwą. W przeciwnym razie jest to automatycznie wygenerowana nazwa pliku. |Nie |

### <a name="recursive-and-copybehavior-examples"></a>Przykłady cykliczne i copyBehavior
W tej sekcji opisano zachowanie operacji kopiowania dla różnych kombinacji wartości właściwości cyklicznych i copyBehavior.

| wartość cykliczna | copyBehavior wartość | Wynikowe działanie |
| --- | --- | --- |
| true |preserveHierarchy |Dla folderu źródłowego Folder1 z następującą strukturą,<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>folder docelowy Folder1 jest tworzony z tą samą strukturą co Źródło:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy |Dla folderu źródłowego Folder1 z następującą strukturą,<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>docelowy Folder1 jest tworzony z następującą strukturą: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla file3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla File5 |
| true |mergeFiles |Dla folderu źródłowego Folder1 z następującą strukturą,<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>docelowy Folder1 jest tworzony z następującą strukturą: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1 + plik2 + file3 + File4 + zawartość pliku 5 są scalane w jeden plik z automatycznie wygenerowaną nazwą pliku. |
| fałsz |preserveHierarchy |Dla folderu źródłowego Folder1 z następującą strukturą,<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>folder docelowy Folder1 jest tworzony z następującą strukturą:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/><br/>Subfolder1 z file3, File4 i File5 nie są pobierane. |
| fałsz |flattenHierarchy |Dla folderu źródłowego Folder1 z następującą strukturą,<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>folder docelowy Folder1 jest tworzony z następującą strukturą:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla plik2<br/><br/>Subfolder1 z file3, File4 i File5 nie są pobierane. |
| fałsz |mergeFiles |Dla folderu źródłowego Folder1 z następującą strukturą,<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>folder docelowy Folder1 jest tworzony z następującą strukturą:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Wartość plik1 + plik2 jest scalana w jeden plik z automatycznie wygenerowaną nazwą pliku.<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatycznie wygenerowana nazwa dla plik1<br/><br/>Subfolder1 z file3, File4 i File5 nie są pobierane. |

## <a name="supported-file-and-compression-formats"></a>Obsługiwane formaty plików i kompresji
Aby uzyskać szczegółowe informacje [, zobacz formaty plików i kompresji w artykule Azure Data Factory](data-factory-supported-file-and-compression-formats.md) .

## <a name="json-examples-for-copying-data-to-and-from-file-system"></a>Przykłady JSON kopiowania danych do i z systemu plików
W poniższych przykładach przedstawiono przykładowe definicje JSON, których można użyć do utworzenia potoku przy użyciu [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Pokazują one sposób kopiowania danych do i z lokalnego systemu plików i usługi Azure Blob Storage. Można jednak skopiować dane *bezpośrednio* z dowolnego źródła do dowolnego ujścia wymienionego w obszarze [obsługiwane źródła i ujścia](data-factory-data-movement-activities.md#supported-data-stores-and-formats) przy użyciu działania kopiowania w Azure Data Factory.

### <a name="example-copy-data-from-an-on-premises-file-system-to-azure-blob-storage"></a>Przykład: kopiowanie danych z lokalnego systemu plików do usługi Azure Blob Storage
Ten przykład pokazuje, jak skopiować dane z lokalnego systemu plików do usługi Azure Blob Storage. Przykład zawiera następujące jednostki Data Factory:

* Połączona usługa typu [OnPremisesFileServer](#linked-service-properties).
* Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [udziału](#dataset-properties).
* Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* [Potok](data-factory-create-pipelines.md) z działaniem kopiowania korzystającym z [FileSystemSource](#copy-activity-properties) i [wartość blobsink](data-factory-azure-blob-connector.md#copy-activity-properties).

Poniższy przykład kopiuje dane szeregów czasowych z lokalnego systemu plików do usługi Azure Blob Storage co godzinę. Właściwości JSON, które są używane w tych przykładach, są opisane w sekcjach po przykładach.

Najpierw należy skonfigurować bramę Zarządzanie danymi zgodnie z instrukcjami w temacie [przenoszenie danych między źródłami lokalnymi i chmurą przy użyciu bramy zarządzanie danymi Gateway](data-factory-move-data-between-onprem-and-cloud.md).

**Połączona usługa lokalnego serwera plików:**

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

Zalecamy użycie właściwości **encryptedCredential** zamiast właściwości **UserID** i **Password** . Aby uzyskać szczegółowe informacje na temat tej połączonej usługi, zobacz [połączoną usługę serwera plików](#linked-service-properties) .

**Połączona usługa Azure Storage:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**Lokalny zestaw danych wejściowy systemu plików:**

Dane są pobierane z nowego pliku co godzinę. Właściwości folderPath i fileName są określane na podstawie czasu rozpoczęcia wycinka.

Ustawienie `"external": "true"` informuje Data Factory, że zestaw danych jest zewnętrzny względem fabryki danych i nie jest tworzony przez działanie w fabryce danych.

```JSON
{
  "name": "OnpremisesFileSystemInput",
  "properties": {
    "type": " FileShare",
    "linkedServiceName": " OnPremisesFileServerLinkedService ",
    "typeProperties": {
      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ]
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Wyjściowy zestaw danych usługi Azure Blob Storage:**

Dane są zapisywane w nowym obiekcie blob co godzinę (częstotliwość: godzina, interwał: 1). Ścieżka folderu dla obiektu BLOB jest obliczana dynamicznie na podstawie czasu rozpoczęcia przetwarzanego wycinka. Ścieżka folderu używa części roku, miesiąca, dnia i godziny rozpoczęcia.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Działanie kopiowania w potoku ze źródłem systemu plików i obiektem sink obiektów blob:**

Potok zawiera działanie kopiowania, które jest skonfigurowane do korzystania z wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** ma wartość **FileSystemSource**, a typ **ujścia** to **wartość blobsink**.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2015-06-01T18:00:00",
    "end":"2015-06-01T19:00:00",
    "description":"Pipeline for copy activity",
    "activities":[
      {
        "name": "OnpremisesFileSystemtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "OnpremisesFileSystemInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "FileSystemSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```

### <a name="example-copy-data-from-azure-sql-database-to-an-on-premises-file-system"></a>Przykład: kopiowanie danych z Azure SQL Database do lokalnego systemu plików
Poniższy przykład pokazuje:

* Połączona usługa typu [AzureSqlDatabase.](data-factory-azure-sql-connector.md#linked-service-properties)
* Połączona usługa typu [OnPremisesFileServer](#linked-service-properties).
* Wejściowy zestaw danych typu [wartość azuresqltable](data-factory-azure-sql-connector.md#dataset-properties).
* Wyjściowy zestaw [danych typu.](#dataset-properties)
* Potok z działaniem kopiowania, który używa elementu [sqlsource](data-factory-azure-sql-connector.md#copy-activity-properties) i [FileSystemSink](#copy-activity-properties).

Przykład kopiuje dane szeregów czasowych z tabeli usługi Azure SQL do lokalnego systemu plików co godzinę. Właściwości JSON, które są używane w tych przykładach, są opisane w sekcjach po przykładach.

**Azure SQL Database połączona usługa:**

```JSON
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```

**Połączona usługa lokalnego serwera plików:**

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

Zalecamy użycie właściwości **encryptedCredential** zamiast używania właściwości **UserID** i **Password** . Aby uzyskać szczegółowe informacje o tej połączonej usłudze, zobacz [połączoną usługę File System](#linked-service-properties) .

**Zestaw danych wejściowych usługi Azure SQL:**

W przykładzie przyjęto założenie, że utworzono tabelę "MyTable" w usłudze Azure SQL, która zawiera kolumnę o nazwie "timestampcolumn" dla danych szeregów czasowych.

Ustawienie ``"external": "true"`` informuje Data Factory, że zestaw danych jest zewnętrzny względem fabryki danych i nie jest tworzony przez działanie w fabryce danych.

```JSON
{
  "name": "AzureSqlInput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Lokalny zestaw danych wyjściowy system plików:**

Dane są kopiowane do nowego pliku co godzinę. FolderPath i nazwa pliku dla obiektu BLOB są określane na podstawie czasu rozpoczęcia wycinka.

```JSON
{
  "name": "OnpremisesFileSystemOutput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": " OnPremisesFileServerLinkedService ",
    "typeProperties": {
      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ]
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Działanie kopiowania w potoku ze źródłem SQL i ujścia systemu plików:**

Potok zawiera działanie kopiowania, które jest skonfigurowane do korzystania z wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** ma wartość **sqlsource**, a typ **ujścia** to **FileSystemSink**. Zapytanie SQL określone dla właściwości **SqlReaderQuery** wybiera dane w ostatniej godzinie do skopiowania.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2015-06-01T18:00:00",
    "end":"2015-06-01T20:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "AzureSQLtoOnPremisesFile",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSQLInput"
          }
        ],
        "outputs": [
          {
            "name": "OnpremisesFileSystemOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "FileSystemSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 3,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```

Można również mapować kolumny ze źródłowego zestawu danych do kolumn z zestawu danych ujścia w definicji działania kopiowania. Aby uzyskać szczegółowe informacje, zobacz [Mapowanie kolumn zestawu danych w Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Wydajności i dostosowywanie
 Aby dowiedzieć się więcej na temat kluczowych czynników wpływających na wydajność przenoszenia danych (działanie kopiowania) w Azure Data Factory i różne sposoby jego optymalizacji, zobacz [Przewodnik dotyczący wydajności i dostrajania działania kopiowania](data-factory-copy-activity-performance.md).
