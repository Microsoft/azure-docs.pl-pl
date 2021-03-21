---
title: Kopiuj dane do i z Azure Data Lake Storage Gen1
description: Informacje o kopiowaniu danych do i z Data Lake Store przy użyciu Azure Data Factory
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
ms.custom: devx-track-csharp
robots: noindex
ms.openlocfilehash: 8bdfceff562ae2501a9d95774f7134669e929b7e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100379407"
---
# <a name="copy-data-to-and-from-data-lake-storage-gen1-by-using-data-factory"></a>Kopiowanie danych do i z Data Lake Storage Gen1 przy użyciu Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](data-factory-azure-datalake-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-azure-data-lake-store.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zapoznaj się z tematem [Azure Data Lake Storage Gen1 Connector w wersji 2](../connector-azure-data-lake-store.md).

W tym artykule wyjaśniono, jak używać działania kopiowania w Azure Data Factory do przenoszenia danych do i z Azure Data Lake Storage Gen1 (wcześniej znanych jako Azure Data Lake Store). W [tym artykule omówiono przenoszenie danych za](data-factory-data-movement-activities.md) pomocą działania kopiowania.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
Dane **z Azure Data Lake Store** można kopiować do następujących magazynów danych:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Dane z następujących magazynów danych można kopiować **do Azure Data Lake Store**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Utwórz konto Data Lake Store przed utworzeniem potoku za pomocą działania kopiowania. Aby uzyskać więcej informacji, zobacz [wprowadzenie do Azure Data Lake Store](../../data-lake-store/data-lake-store-get-started-portal.md).

## <a name="supported-authentication-types"></a>Obsługiwane typy uwierzytelniania
Łącznik Data Lake Store obsługuje następujące typy uwierzytelniania:
* Uwierzytelnianie jednostki usługi
* Uwierzytelnianie poświadczeń użytkownika (OAuth)

Zalecamy używanie uwierzytelniania nazwy głównej usługi, szczególnie w przypadku zaplanowanego kopiowania danych. Zachowanie wygaśnięcia tokenu może wystąpić z uwierzytelnianiem poświadczeń użytkownika. Aby uzyskać szczegółowe informacje dotyczące konfiguracji, zobacz sekcję [Właściwości połączonej usługi](#linked-service-properties) .

## <a name="get-started"></a>Rozpoczęcie pracy
Można utworzyć potok z działaniem kopiowania, które przenosi dane do/z Azure Data Lake Store przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem utworzenia potoku w celu skopiowania danych jest użycie **Kreatora kopiowania**. Aby zapoznać się z samouczkiem dotyczącym tworzenia potoku przy użyciu Kreatora kopiowania, zobacz [Samouczek: Tworzenie potoku przy użyciu Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md).

Do utworzenia potoku można także użyć następujących narzędzi: **Visual Studio**, **Azure PowerShell**, **szablon Azure Resource Manager**, interfejs API **platformy .NET** i **interfejs API REST**. Aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoku za pomocą działania kopiowania, zobacz [Samouczek dotyczący działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Niezależnie od tego, czy używasz narzędzi, czy interfejsów API, wykonaj następujące kroki, aby utworzyć potok służący do przenoszenia danych ze źródłowego magazynu danych do magazynu danych ujścia:

1. Utwórz **fabrykę danych**. Fabryka danych może zawierać jeden lub więcej potoków.
2. Utwórz **połączone usługi** , aby połączyć magazyny danych wejściowych i wyjściowych z fabryką danych. Na przykład w przypadku kopiowania danych z usługi Azure Blob Storage do Azure Data Lake Store można utworzyć dwie połączone usługi, aby połączyć konto usługi Azure Storage i Magazyn Azure Data Lake z fabryką danych. Aby uzyskać właściwości połączonej usługi, które są specyficzne dla Azure Data Lake Store, zobacz sekcję [Właściwości połączonej usługi](#linked-service-properties) .
2. Utwórz **zestawy** danych, aby reprezentować dane wejściowe i wyjściowe dla operacji kopiowania. W przykładzie opisanym w ostatnim kroku utworzysz zestaw danych, aby określić kontener obiektów blob i folder zawierający dane wejściowe. Ponadto utworzysz kolejny zestaw danych, aby określić folder i ścieżkę do pliku w magazynie Data Lake, który przechowuje dane skopiowane z magazynu obiektów BLOB. Dla właściwości zestawu danych, które są specyficzne dla Azure Data Lake Store, zobacz sekcję [Właściwości zestawu danych](#dataset-properties) .
3. Utwórz **potok** z działaniem kopiowania, które pobiera zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe. W powyższym przykładzie użyto BlobSource jako źródła i AzureDataLakeStoreSink jako ujścia dla działania kopiowania. Podobnie, jeśli kopiujesz z Azure Data Lake Store do Blob Storage platformy Azure, używasz AzureDataLakeStoreSource i wartość blobsink w działaniu kopiowania. Aby uzyskać właściwości działania kopiowania, które są specyficzne dla Azure Data Lake Store, zobacz sekcję [właściwości działania kopiowania](#copy-activity-properties) . Aby uzyskać szczegółowe informacje na temat używania magazynu danych jako źródła lub ujścia, kliknij link w poprzedniej sekcji dla magazynu danych.

Gdy używasz Kreatora, definicje JSON dla tych Data Factory jednostek (połączone usługi, zestawy danych i potok) są automatycznie tworzone. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API .NET), należy zdefiniować te Data Factory jednostki przy użyciu formatu JSON. Przykłady zawierające definicje JSON dla Data Factory jednostek, które są używane do kopiowania danych do/z Azure Data Lake Store, zobacz sekcję [przykłady JSON](#json-examples-for-copying-data-to-and-from-data-lake-store) w tym artykule.

Poniższe sekcje zawierają szczegółowe informacje na temat właściwości JSON, które są używane do definiowania Data Factory jednostek specyficznych dla Data Lake Store.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi
Połączona usługa łączy magazyn danych z fabryką danych. Utworzysz połączoną usługę typu **AzureDataLakeStore** , aby połączyć dane Data Lake Store z fabryką danych. W poniższej tabeli opisano elementy JSON charakterystyczne dla Data Lake Store połączonych usług. Można wybrać między jednostką usługi a uwierzytelnianiem poświadczeń użytkownika.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| **Wprowadź** | Właściwość Type musi być ustawiona na wartość **AzureDataLakeStore**. | Tak |
| **dataLakeStoreUri** | Informacje o koncie Azure Data Lake Store. Te informacje mają jeden z następujących formatów: `https://[accountname].azuredatalakestore.net/webhdfs/v1` lub `adl://[accountname].azuredatalakestore.net/` . | Tak |
| **subscriptionId** | Identyfikator subskrypcji platformy Azure, do której należy konto Data Lake Store. | Wymagane dla ujścia |
| **resourceGroupName** | Nazwa grupy zasobów platformy Azure, do której należy konto Data Lake Store. | Wymagane dla ujścia |

### <a name="service-principal-authentication-recommended"></a>Uwierzytelnianie jednostki usługi (zalecane)
Aby użyć uwierzytelniania nazwy głównej usługi, zarejestruj jednostkę aplikacji w Azure Active Directory (Azure AD) i Udziel jej dostępu do Data Lake Store. Aby uzyskać szczegółowe instrukcje, zobacz [uwierzytelnianie między usługami](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md). Należy zwrócić uwagę na następujące wartości, których można użyć do zdefiniowania połączonej usługi:
* Identyfikator aplikacji
* Klucz aplikacji
* Identyfikator dzierżawy

> [!IMPORTANT]
> Upewnij się, że przyznano mu odpowiednie uprawnienia w Azure Data Lake Store:
>- **Aby użyć Data Lake Store jako źródła**, udziel co najmniej uprawnienia do **odczytu i wykonania** dostępu do danych, aby wyświetlić listę i skopiować zawartość folderu, lub uprawnienia do **odczytu** w celu skopiowania pojedynczego pliku. Brak wymagania kontroli dostępu na poziomie konta.
>- **Aby użyć Data Lake Store jako ujścia**, udziel co najmniej uprawnienia do **zapisu i wykonywania** danych, aby utworzyć elementy podrzędne w folderze. Jeśli używasz Azure IR do upoważnienia do kopiowania (zarówno źródła, jak i ujścia w chmurze), w celu umożliwienia Data Factory wykrywania regionu Data Lake Store, udziel co najmniej roli **czytelnika** w kontroli dostępu do konta (IAM). Jeśli chcesz uniknąć tej roli mapy IAM, [Określ lokalizacja ExecutionLocation](data-factory-data-movement-activities.md#global) z lokalizacją Data Lake Store w działaniu kopiowania.
>- Jeśli **używasz Kreatora kopiowania do tworzenia potoków**, udziel co najmniej roli **czytelnika** w kontroli dostępu do konta (IAM). Ponadto Udziel co najmniej uprawnień do **odczytu i wykonywania** dla Data Lake Store głównego ("/") i jego elementów podrzędnych. W przeciwnym razie może zostać wyświetlony komunikat "podane poświadczenia są nieprawidłowe".

Użyj uwierzytelniania nazwy głównej usługi, określając następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| **servicePrincipalId** | Określ identyfikator klienta aplikacji. | Tak |
| **servicePrincipalKey** | Określ klucz aplikacji. | Tak |
| **tenant** | Określ informacje o dzierżawie (nazwę domeny lub identyfikator dzierżawy), w których znajduje się Twoja aplikacja. Możesz ją pobrać, aktywując wskaźnik myszy w prawym górnym rogu Azure Portal. | Tak |

**Przykład: Uwierzytelnianie jednostki usługi**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

### <a name="user-credential-authentication"></a>Uwierzytelnianie poświadczeń użytkownika
Alternatywnie można użyć uwierzytelniania poświadczeń użytkownika do kopiowania z lub do Data Lake Store, określając następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| **zgody** | Kliknij przycisk **Autoryzuj** w edytorze Data Factory i wprowadź poświadczenia, które przypisuje automatycznie wygenerowany adres URL autoryzacji do tej właściwości. | Tak |
| **sessionId** | Identyfikator sesji OAuth z sesji autoryzacji OAuth. Każdy identyfikator sesji jest unikatowy i może być używany tylko raz. To ustawienie jest generowane automatycznie, gdy jest używany Edytor Data Factory. | Tak |

> [!IMPORTANT]
> Upewnij się, że przyznano użytkownikowi odpowiednie uprawnienia w Azure Data Lake Store:
>- **Aby użyć Data Lake Store jako źródła**, udziel co najmniej uprawnienia do **odczytu i wykonania** dostępu do danych, aby wyświetlić listę i skopiować zawartość folderu, lub uprawnienia do **odczytu** w celu skopiowania pojedynczego pliku. Brak wymagania kontroli dostępu na poziomie konta.
>- **Aby użyć Data Lake Store jako ujścia**, udziel co najmniej uprawnienia do **zapisu i wykonywania** danych, aby utworzyć elementy podrzędne w folderze. Jeśli używasz Azure IR do upoważnienia do kopiowania (zarówno źródła, jak i ujścia w chmurze), w celu umożliwienia Data Factory wykrywania regionu Data Lake Store, udziel co najmniej roli **czytelnika** w kontroli dostępu do konta (IAM). Jeśli chcesz uniknąć tej roli mapy IAM, [Określ lokalizacja ExecutionLocation](data-factory-data-movement-activities.md#global) z lokalizacją Data Lake Store w działaniu kopiowania.
>- Jeśli **używasz Kreatora kopiowania do tworzenia potoków**, udziel co najmniej roli **czytelnika** w kontroli dostępu do konta (IAM). Ponadto Udziel co najmniej uprawnień do **odczytu i wykonywania** dla Data Lake Store głównego ("/") i jego elementów podrzędnych. W przeciwnym razie może zostać wyświetlony komunikat "podane poświadczenia są nieprawidłowe".

**Przykład: uwierzytelnianie poświadczeń użytkownika**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

#### <a name="token-expiration"></a>Wygaśnięcie tokenu
Kod autoryzacji generowany przy użyciu przycisku **Autoryzuj** zostanie wygaśnie po upływie określonego czasu. Następujący komunikat oznacza, że token uwierzytelniania wygasł:

Błąd operacji poświadczeń: invalid_grant-AADSTS70002: Wystąpił błąd podczas weryfikowania poświadczeń. AADSTS70008: podany przydział dostępu wygasł lub został odwołany. Identyfikator śledzenia: identyfikator korelacji d18629e8-af88-43c5-88e3-d8419eb1fca1: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 sygnatura czasowa: 2015-12-15 21-09-31Z.

W poniższej tabeli przedstawiono czasy wygaśnięcia różnych typów kont użytkowników:

| Typ użytkownika | Wygasa po |
|:--- |:--- |
| *Konta użytkowników* niezarządzane przez Azure Active Directory (na przykład @hotmail.com lub @live.com ) |12 godzin |
| Konta użytkowników zarządzane przez Azure Active Directory |14 dni od ostatniego uruchomienia wycinka <br/><br/>90 dni, jeśli wycinek oparty na połączonej usłudze opartej na protokole OAuth jest uruchamiany co najmniej raz na 14 dni |

Jeśli zmienisz hasło przed upływem czasu wygaśnięcia tokenu, token natychmiast wygaśnie. Zobaczysz komunikat wymieniony wcześniej w tej sekcji.

Możesz ponownie autoryzować konto za pomocą przycisku **Autoryzuj** , gdy token wygaśnie, aby ponownie wdrożyć połączoną usługę. Możesz również generować wartości właściwości **SessionID** i **Authorization** programowo przy użyciu następującego kodu:


```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```
Aby uzyskać szczegółowe informacje na temat klas Data Factory używanych w kodzie, zobacz tematy klasy [AzureDataLakeStoreLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice), [AzureDataLakeAnalyticsLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice)i [AuthorizationSessionGetResponse](/dotnet/api/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse) . Dodaj odwołanie do wersji `2.9.10826.1824` programu `Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll` dla `WindowsFormsWebAuthenticationDialog` klasy używanej w kodzie.

## <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów

**Objaw:** Podczas kopiowania danych **do** Azure Data Lake Store, jeśli działanie kopiowania zakończy się niepowodzeniem z powodu następującego błędu:

  ```
  Failed to detect the region for Azure Data Lake account {your account name}. Please make sure that the Resource Group name: {resource group name} and subscription ID: {subscription ID} of this Azure Data Lake Store resource are correct.
  ```

**Główna przyczyna:** Istnieją 2 możliwe przyczyny:

1. `resourceGroupName`I/lub `subscriptionId` określone w Azure Data Lake Store połączona usługa jest niepoprawna;
2. Użytkownik lub jednostka usługi nie ma wymaganego uprawnienia.

**Rozwiązanie:**

1. Upewnij się, że `subscriptionId` i `resourceGroupName` określisz wartość w polu połączona usługa `typeProperties` jest rzeczywiście tym, do których należy konto usługi Data Lake.

2. Upewnij się, że masz co najmniej rolę **czytelnika** dla użytkownika lub nazwy głównej usługi na koncie Data Lake. Oto jak to zrobić:

    1. Przejdź do Azure Portal — > Twoje konto Data Lake Store
    2. Kliknij pozycję **Kontrola dostępu (IAM)** w bloku Data Lake Store
    3. Kliknij pozycję **Dodaj przypisanie roli**
    4. Ustaw **rolę** jako **czytnik** i wybierz użytkownika lub jednostkę usługi, która jest używana do kopiowania, aby udzielić dostępu

3. Jeśli nie chcesz udzielić roli **czytnika** użytkownikowi lub jednostce usługi, alternatywą jest [jawne określenie lokalizacji wykonywania](data-factory-data-movement-activities.md#global) w działaniu kopiowania z lokalizacją Data Lake Store. Przykład:

    ```json
    {
      "name": "CopyToADLS",
      "type": "Copy",
      ......
      "typeProperties": {
        "source": {
          "type": "<source type>"
        },
        "sink": {
          "type": "AzureDataLakeStoreSink"
        },
        "exeuctionLocation": "West US"
      }
    }
    ```

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby określić zestaw danych do reprezentowania danych wejściowych w Data Lake Store, należy ustawić właściwość **Type** zestawu danych na **AzureDataLakeStore**. Ustaw właściwość **linkedServiceName** zestawu danych na nazwę połączonej usługi Data Lake Store. Aby uzyskać pełną listę sekcji i właściwości JSON dostępnych do definiowania zestawów danych, zobacz artykuł [Tworzenie zestawów danych](data-factory-create-datasets.md) . Sekcje zestawu danych w formacie JSON, takie jak **Struktura**, **dostępność** i **zasady**, są podobne do wszystkich typów zestawów danych (na przykład Azure SQL Database, Azure BLOB i Azure Table). Sekcja **typeProperties** jest inna dla każdego typu zestawu danych i zawiera informacje, takie jak lokalizacja i format danych w magazynie danych.

Sekcja **typeProperties** zestawu danych typu **AzureDataLakeStore** zawiera następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| **folderPath** |Ścieżka do kontenera i folderu w Data Lake Store. |Tak |
| **fileName** |Nazwa pliku w Azure Data Lake Store. Właściwość **filename** jest opcjonalna i uwzględnia wielkość liter. <br/><br/>W przypadku określenia **nazwy pliku** działanie (łącznie z kopią) działa w określonym pliku.<br/><br/>Jeśli **Nazwa pliku** nie jest określona, Copy zawiera wszystkie pliki w **folderPath** w wejściowym zestawie danych.<br/><br/>Gdy nie określono **nazwy pliku** dla wyjściowego zestawu danych, a **preserveHierarchy** nie jest określona w ujścia aktywności, nazwa wygenerowanego pliku jest w formacie `Data._Guid_.txt` . Na przykład: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt. |Nie |
| **partitionedBy** |Właściwość **partitionedBy** jest opcjonalna. Można jej użyć do określenia ścieżki dynamicznej i nazwy pliku dla danych szeregów czasowych. Na przykład **folderPath** może być sparametryzowany dla każdej godziny danych. Aby uzyskać szczegółowe informacje i przykłady, zobacz Właściwość partitionedBy. |Nie |
| **Formatowanie** | Obsługiwane są następujące typy formatów: **TextFormat**, **formatu jsonformat**, **AvroFormat**, **OrcFormat** i **ParquetFormat**. Ustaw właściwość **Type** w polu **Format** na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz sekcję [Format tekstu](data-factory-supported-file-and-compression-formats.md#text-format), [Format JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Format Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Format Orc](data-factory-supported-file-and-compression-formats.md#orc-format)i [Parquet format](data-factory-supported-file-and-compression-formats.md#parquet-format) w [plikach i formatach kompresji obsługiwanych przez Azure Data Factory](data-factory-supported-file-and-compression-formats.md) artykuł. <br><br> Jeśli chcesz skopiować pliki "AS-IS" między magazynami opartymi na plikach (kopia binarna), Pomiń `format` sekcję w definicjach zestawu danych wejściowych i wyjściowych. |Nie |
| **skompresowane** | Określ typ i poziom kompresji danych. Obsługiwane typy to **gzip**, **Wklęśnięcie**, **BZip2** i **ZipDeflate**. Obsługiwane poziomy są **optymalne** i **najszybciej**. Aby uzyskać więcej informacji, zobacz [formaty plików i kompresji obsługiwane przez Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |

### <a name="the-partitionedby-property"></a>Właściwość partitionedBy
Można określić dynamiczne właściwości **folderPath** i **filename** dla danych szeregów czasowych z właściwością **partitionedBy** , funkcjami Data Factory i zmiennymi systemowymi. Aby uzyskać szczegółowe informacje, zobacz artykuł [Azure Data Factory-Functions i zmienne systemowe](data-factory-functions-variables.md) .


W poniższym przykładzie `{Slice}` jest zastępowana wartością zmiennej systemowej Data Factory `SliceStart` w określonym formacie ( `yyyyMMddHH` ). Nazwa `SliceStart` odwołuje się do godziny rozpoczęcia wycinka. `folderPath`Właściwość jest inna dla każdego wycinka, jak w `wikidatagateway/wikisampledataout/2014100103` lub `wikidatagateway/wikisampledataout/2014100104` .

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

W poniższym przykładzie rok, miesiąc, dzień i czas `SliceStart` są wyodrębniane do oddzielnych zmiennych, które są używane przez `folderPath` `fileName` właściwości i:
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
Aby uzyskać więcej informacji na temat zestawów danych, planowania i wycinków szeregów czasowych, zobacz [zestawy danych w Azure Data Factory](data-factory-create-datasets.md) i Data Factory artykuły dotyczące [planowania i wykonywania](data-factory-scheduling-and-execution.md) .


## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [Tworzenie potoków](data-factory-create-pipelines.md) . Właściwości, takie jak nazwa, opis, tabele wejściowe i wyjściowe, oraz zasady są dostępne dla wszystkich typów działań.

Właściwości dostępne w sekcji **typeProperties** działania różnią się w zależności od typu działania. W przypadku działania kopiowania różnią się w zależności od typów źródeł i ujścia.

**AzureDataLakeStoreSource** obsługuje następującą właściwość w sekcji **typeProperties** :

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| **rozpoznawania** |Wskazuje, czy dane są odczytane cyklicznie z podfolderów, czy tylko z określonego folderu. |True (wartość domyślna), FAŁSZ |Nie |

**AzureDataLakeStoreSink** obsługuje następujące właściwości w sekcji **typeProperties** :

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| **copyBehavior** |Określa zachowanie kopiowania. |<b>PreserveHierarchy</b>: zachowuje hierarchię plików w folderze docelowym. Ścieżka względna pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna pliku docelowego do folderu docelowego.<br/><br/><b>FlattenHierarchy</b>: wszystkie pliki z folderu źródłowego są tworzone na pierwszym poziomie folderu docelowego. Pliki docelowe są tworzone przy użyciu automatycznie generowanych nazw.<br/><br/><b>MergeFiles</b>: Scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku lub obiektu BLOB jest określona, scalona nazwa pliku jest podaną nazwą. W przeciwnym razie nazwa pliku jest generowana automatycznie. |Nie |

### <a name="recursive-and-copybehavior-examples"></a>Przykłady cykliczne i copyBehavior
W tej sekcji opisano zachowanie operacji kopiowania dla różnych kombinacji wartości cyklicznych i copyBehavior.

| rozpoznawania | copyBehavior | Wynikowe działanie |
| --- | --- | --- |
| true |preserveHierarchy |Dla folderu źródłowego Folder1 z następującą strukturą: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>folder docelowy Folder1 jest tworzony z tą samą strukturą co Źródło<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy |Dla folderu źródłowego Folder1 z następującą strukturą: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>docelowy Folder1 jest tworzony z następującą strukturą: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla file3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla File5 |
| true |mergeFiles |Dla folderu źródłowego Folder1 z następującą strukturą: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>docelowy Folder1 jest tworzony z następującą strukturą: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1 + plik2 + file3 + File4 + zawartość pliku 5 są scalane w jeden plik z automatycznie wygenerowaną nazwą pliku |
| fałsz |preserveHierarchy |Dla folderu źródłowego Folder1 z następującą strukturą: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>folder docelowy Folder1 jest tworzony z następującą strukturą<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/><br/><br/>Subfolder1 z file3, File4 i File5 nie są odbierane. |
| fałsz |flattenHierarchy |Dla folderu źródłowego Folder1 z następującą strukturą:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>folder docelowy Folder1 jest tworzony z następującą strukturą<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla plik2<br/><br/><br/>Subfolder1 z file3, File4 i File5 nie są odbierane. |
| fałsz |mergeFiles |Dla folderu źródłowego Folder1 z następującą strukturą:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>folder docelowy Folder1 jest tworzony z następującą strukturą<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Zawartość plik1 + plik2 są scalane w jeden plik z automatycznie wygenerowaną nazwą pliku. automatycznie wygenerowana nazwa dla plik1<br/><br/>Subfolder1 z file3, File4 i File5 nie są odbierane. |

## <a name="supported-file-and-compression-formats"></a>Obsługiwane formaty plików i kompresji
Aby uzyskać szczegółowe informacje, zobacz [formaty plików i kompresji w artykule Azure Data Factory](data-factory-supported-file-and-compression-formats.md) .

## <a name="json-examples-for-copying-data-to-and-from-data-lake-store"></a>Przykłady JSON kopiowania danych do i z Data Lake Store
W poniższych przykładach przedstawiono przykładowe definicje JSON. Tych przykładowych definicji można użyć do utworzenia potoku przy użyciu [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Przykłady przedstawiają sposób kopiowania danych do i z Data Lake Store i usługi Azure Blob Storage. Dane można jednak skopiować _bezpośrednio_ z dowolnego źródła do dowolnego z obsługiwanych ujścia. Aby uzyskać więcej informacji, zobacz sekcję "obsługiwane magazyny i formaty danych" w artykule [przenoszenie danych za pomocą działania kopiowania](data-factory-data-movement-activities.md) .

### <a name="example-copy-data-from-azure-blob-storage-to-azure-data-lake-store"></a>Przykład: kopiowanie danych z platformy Azure Blob Storage do Azure Data Lake Store
Przykładowy kod w tej sekcji przedstawia:

* Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Połączona usługa typu [AzureDataLakeStore](#linked-service-properties).
* Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureDataLakeStore](#dataset-properties).
* [Potok](data-factory-create-pipelines.md) z działaniem kopiowania korzystającym z [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) i [AzureDataLakeStoreSink](#copy-activity-properties).

Przykłady pokazują, jak dane szeregów czasowych z platformy Azure Blob Storage są kopiowane do Data Lake Store co godzinę.

**Połączona usługa Azure Storage**

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

**Azure Data Lake Store połączona usługa**

```JSON
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

> [!NOTE]
> Aby uzyskać szczegółowe informacje dotyczące konfiguracji, zobacz sekcję [Właściwości połączonej usługi](#linked-service-properties) .
>

**Wejściowy zestaw danych obiektów blob platformy Azure**

W poniższym przykładzie dane są pobierane z nowego obiektu BLOB co godzinę ( `"frequency": "Hour", "interval": 1` ). Ścieżka folderu i nazwa pliku obiektu BLOB są dynamicznie oceniane na podstawie czasu rozpoczęcia przetwarzanego wycinka. Ścieżka folderu używa części Year, month i Day czasu rozpoczęcia. Nazwa pliku używa części godzinowej godziny rozpoczęcia. To `"external": true` ustawienie informuje usługę Data Factory, że tabela znajduje się poza fabryką danych i nie jest generowana przez działanie w fabryce danych.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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

**Azure Data Lake Store wyjściowy zestaw danych**

Poniższy przykład kopiuje dane do Data Lake Store. Nowe dane są kopiowane do Data Lake Store co godzinę.

```JSON
{
    "name": "AzureDataLakeStoreOutput",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Działanie Copy w potoku ze źródłem obiektów blob i Data Lake Store ujścia**

W poniższym przykładzie potok zawiera działanie kopiowania, które jest skonfigurowane do korzystania z wejściowych i wyjściowych zestawów danych. Działanie kopiowania jest zaplanowane do uruchomienia co godzinę. W definicji JSON potoku `source` Typ jest ustawiony na `BlobSource` , a `sink` Typ jest ustawiony na `AzureDataLakeStoreSink` .

```json
{
    "name":"SamplePipeline",
    "properties":
    {
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":
        [
            {
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureDataLakeStoreOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "AzureDataLakeStoreSink"
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

### <a name="example-copy-data-from-azure-data-lake-store-to-an-azure-blob"></a>Przykład: kopiowanie danych z Azure Data Lake Store do obiektu blob platformy Azure
Przykładowy kod w tej sekcji przedstawia:

* Połączona usługa typu [AzureDataLakeStore](#linked-service-properties).
* Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureDataLakeStore](#dataset-properties).
* Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* [Potok](data-factory-create-pipelines.md) z działaniem kopiowania korzystającym z [AzureDataLakeStoreSource](#copy-activity-properties) i [wartość blobsink](data-factory-azure-blob-connector.md#copy-activity-properties).

Kod kopiuje dane szeregów czasowych z Data Lake Store do obiektu blob platformy Azure co godzinę.

**Azure Data Lake Store połączona usługa**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        }
    }
}
```

> [!NOTE]
> Aby uzyskać szczegółowe informacje dotyczące konfiguracji, zobacz sekcję [Właściwości połączonej usługi](#linked-service-properties) .
>

**Połączona usługa Azure Storage**

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
**Azure Data Lake wejściowy zestaw danych**

W tym przykładzie ustawienie `"external"` `true` informuje usługę Data Factory, że tabela jest zewnętrzna z fabryką danych i nie jest generowana przez działanie w fabryce danych.

```json
{
    "name": "AzureDataLakeStoreInput",
    "properties":
    {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
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
**Wyjściowy zestaw danych obiektów blob platformy Azure**

W poniższym przykładzie dane są zapisywane w nowym obiekcie blob co godzinę ( `"frequency": "Hour", "interval": 1` ). Ścieżka folderu dla obiektu BLOB jest obliczana dynamicznie na podstawie czasu rozpoczęcia przetwarzanego wycinka. Ścieżka folderu używa części Year, month, Day i Hour godziny rozpoczęcia.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Działanie kopiowania w potoku ze źródłem Azure Data Lake Store i obiektem sink obiektów BLOB**

W poniższym przykładzie potok zawiera działanie kopiowania, które jest skonfigurowane do korzystania z wejściowych i wyjściowych zestawów danych. Działanie kopiowania jest zaplanowane do uruchomienia co godzinę. W definicji JSON potoku `source` Typ jest ustawiony na `AzureDataLakeStoreSource` , a `sink` Typ jest ustawiony na `BlobSink` .

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[
            {
                "name": "AzureDakeLaketoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureDataLakeStoreInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "AzureDataLakeStoreSource",
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

W definicji działania kopiowania można także mapować kolumny z zestawu danych źródłowych do kolumn w zestawie danych ujścia. Aby uzyskać szczegółowe informacje, zobacz [Mapowanie kolumn zestawu danych w Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Wydajności i dostosowywanie
Aby dowiedzieć się więcej o czynnikach wpływających na wydajność działania kopiowania i sposobach jego optymalizacji, zobacz artykuł [dotyczący wydajności działania kopiowania i dostrajania](data-factory-copy-activity-performance.md) .