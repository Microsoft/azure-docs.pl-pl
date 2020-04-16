---
title: Kopiowanie danych ze źródła REST przy użyciu usługi Azure Data Factory
description: Dowiedz się, jak skopiować dane z chmury lub lokalnego źródła REST do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: jingwang
ms.openlocfilehash: 2657f1998e3ca908bc52166154ac3353e1e5a66b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415047"
---
# <a name="copy-data-from-a-rest-endpoint-by-using-azure-data-factory"></a>Kopiowanie danych z punktu końcowego REST przy użyciu usługi Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano, jak używać działania kopiowania w usłudze Azure Data Factory do kopiowania danych z punktu końcowego REST. Artykuł opiera się na [copy activity w usłudze Azure Data Factory](copy-activity-overview.md), który przedstawia ogólne omówienie działania kopiowania.

Różnica między tym łącznikiem REST, [łącznikiem HTTP](connector-http.md) i [łącznikiem tabeli sieci Web](connector-web-table.md) to:

- **Złącze REST** obsługuje w szczególności kopiowanie danych z interfejsów API RESTful; 
- **Łącznik HTTP** jest ogólny, aby pobrać dane z dowolnego punktu końcowego HTTP, na przykład do pobrania pliku. Zanim ten łącznik REST stanie się dostępny, może się zdarzyć użycie łącznika HTTP do kopiowania danych z interfejsu API RESTful, który jest obsługiwany, ale mniej funkcjonalny w porównaniu do łącznika REST.
- **Łącznik tabeli sieci Web** wyodrębnia zawartość tabeli ze strony html.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Można skopiować dane ze źródła REST do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych obsługiwanych przez działanie kopiowania jako źródła i pochłaniacze, zobacz [Obsługiwane magazyny danych i formaty](copy-activity-overview.md#supported-data-stores-and-formats).

W szczególności to ogólne złącze REST obsługuje:

- Pobieranie danych z punktu końcowego REST przy użyciu **GET** lub **POST** metody.
- Pobieranie danych przy użyciu jednego z następujących uwierzytelniania: **Anonimowy**, **Podstawowy,** **Podmiot usługi AAD**i **tożsamości zarządzane dla zasobów platformy Azure.**
- **[Podział na strony](#pagination-support)** w interfejsach API REST.
- Kopiowanie odpowiedzi REST JSON [w stanie stanu jest](#export-json-response-as-is) lub analizowanie jej przy użyciu [mapowania schematu](copy-activity-schema-and-type-mapping.md#schema-mapping). Obsługiwane jest tylko ładowność odpowiedzi w **u. JSON.**

> [!TIP]
> Aby przetestować żądanie pobierania danych przed skonfigurowaniem łącznika REST w fabryce danych, zapoznaj się ze specyfikacją interfejsu API dla wymagań nagłówka i treści. Do sprawdzania poprawności można użyć narzędzi, takich jak Listonosz lub przeglądarka internetowa.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje na temat właściwości, których można użyć do zdefiniowania elementów fabryki danych, które są specyficzne dla łącznika REST.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Następujące właściwości są obsługiwane dla usługi połączonej REST:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość **typu** musi być ustawiona na **RestService**. | Tak |
| url | Podstawowy adres URL usługi REST. | Tak |
| enableServerCertificateWwadacja | Czy podczas łączenia się z punktem końcowym ma być sprawdzana weryfikacja certyfikatu TLS/SSL po stronie serwera. | Nie<br /> (wartość domyślna jest **prawdziwa)** |
| authenticationType | Typ uwierzytelniania używanego do łączenia się z usługą REST. Dozwolone wartości to **Anonimowe,** **Podstawowe,** **AadServicePrincipal** i **ManagedServiceIdentity**. Zapoznaj się z odpowiednimi sekcjami poniżej, aby uzyskać więcej właściwości i przykładów. | Tak |
| connectVia | [Środowisko wykonawcze integracji](concepts-integration-runtime.md) do nawiązywania połączenia z magazynem danych. Dowiedz się więcej z sekcji [Wymagania wstępne.](#prerequisites) Jeśli nie zostanie określony, ta właściwość używa domyślnego środowiska wykonawczego integracji platformy Azure. |Nie |

### <a name="use-basic-authentication"></a>Korzystanie z uwierzytelniania podstawowego

Ustaw właściwość **authenticationType** na **Basic**. Oprócz właściwości ogólnych, które są opisane w poprzedniej sekcji, należy określić następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| userName | Nazwa użytkownika, który ma być używany do uzyskiwania dostępu do punktu końcowego REST. | Tak |
| hasło | Hasło użytkownika (wartość **userName).** Oznacz to pole jako typ **SecureString,** aby bezpiecznie przechowywać go w fabryce danych. Można również [odwoływać się do klucza tajnego przechowywanego w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Tak |

**Przykład**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<REST endpoint>",
            "userName": "<user name>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-aad-service-principal-authentication"></a>Korzystanie z uwierzytelniania głównego usługi AAD

Ustaw właściwość **authenticationType** na **AadServicePrincipal**. Oprócz właściwości ogólnych, które są opisane w poprzedniej sekcji, należy określić następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| servicePrincipalId | Określ identyfikator klienta aplikacji usługi Azure Active Directory. | Tak |
| servicePrincipalKey | Określ klucz aplikacji usługi Azure Active Directory. Oznacz to pole jako **SecureString,** aby bezpiecznie przechowywać go w fabryce danych lub [odwołaj się do klucza tajnego przechowywanego w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) | Tak |
| Dzierżawy | Określ informacje o dzierżawie (nazwa domeny lub identyfikator dzierżawy), w którym znajduje się aplikacja. Pobierz go, najeżdżając myszą w prawym górnym rogu witryny Azure portal. | Tak |
| aadResourceId | Określ zasób AAD, którego chcesz autoryzacji, np. `https://management.core.windows.net`| Tak |

**Przykład**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Używanie tożsamości zarządzanych do uwierzytelniania zasobów platformy Azure

Ustaw właściwość **authenticationType** na **ManagedServiceIdentity**. Oprócz właściwości ogólnych, które są opisane w poprzedniej sekcji, należy określić następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| aadResourceId | Określ zasób AAD, którego chcesz autoryzacji, np. `https://management.core.windows.net`| Tak |

**Przykład**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "ManagedServiceIdentity",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Ta sekcja zawiera listę właściwości, które obsługuje zestaw danych REST. 

Aby uzyskać pełną listę sekcji i właściwości, które są dostępne do definiowania zestawów danych, zobacz [Zestawy danych i połączone usługi](concepts-datasets-linked-services.md). 

Aby skopiować dane z restusz, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość **typu** zestawu danych musi być ustawiona na **RestResource**. | Tak |
| względnyUrl | Względny adres URL do zasobu, który zawiera dane. Jeśli ta właściwość nie jest określona, używany jest tylko adres URL określony w definicji połączonej usługi. Łącznik HTTP kopiuje dane z `[URL specified in linked service]/[relative URL specified in dataset]`połączonego adresu URL: . | Nie |

Jeśli ustawienie `requestMethod`, `additionalHeaders` `requestBody` i `paginationRules` w zestawie danych, nadal jest obsługiwany jako — jest, podczas gdy zaleca się użycie nowego modelu w źródle aktywności w przyszłości.

**Przykład:**

```json
{
    "name": "RESTDataset",
    "properties": {
        "type": "RestResource",
        "typeProperties": {
            "relativeUrl": "<relative url>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<REST linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiowanie właściwości działania

Ta sekcja zawiera listę właściwości, które obsługuje źródło REST.

Aby uzyskać pełną listę sekcji i właściwości, które są dostępne do definiowania działań, zobacz [Potoki](concepts-pipelines-activities.md). 

### <a name="rest-as-source"></a>REST jako źródło

Następujące właściwości są obsługiwane w sekcji **źródła** działania kopiowania:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość **typu** źródła działania kopiowania musi być ustawiona na **RestSource**. | Tak |
| wniosekMetoda | Metoda HTTP. Dozwolone wartości **to Get** (default) i **Post**. | Nie |
| dodatkowePozyty | Dodatkowe nagłówki żądań HTTP. | Nie |
| requestBody (Ciało) | Treść żądania HTTP. | Nie |
| podział na stronyRules | Reguły podziałów na strony, aby skomponować następne żądania strony. Szczegółowe informacje można znaleźć w sekcji pomocy technicznej dotyczącej podziałów na [strony.](#pagination-support) | Nie |
| httpRequestTimeout | Limit czasu (wartość **TimeSpan)** dla żądania HTTP, aby uzyskać odpowiedź. Ta wartość jest limit czasu, aby uzyskać odpowiedź, a nie limit czasu do odczytu danych odpowiedzi. Wartość domyślna **to 00:01:40**.  | Nie |
| żądanieInterwalne | Czas oczekiwania przed wysłaniem żądania następnej strony. Wartość domyślna **to 00:00:01** |  Nie |

>[!NOTE]
>Złącze REST ignoruje dowolny `additionalHeaders`nagłówek "Akceptuj" określony w pliku . Jako złącze REST obsługuje tylko odpowiedź w JSON, automatycznie wygeneruje nagłówek `Accept: application/json`.

**Przykład 1: Korzystanie z metody Pobierz z podziałem na strony**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "RestSource",
                "additionalHeaders": {
                    "x-user-defined": "helloworld"
                },
                "paginationRules": {
                    "AbsoluteUrl": "$.paging.next"
                },
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Przykład 2: Używanie metody Post**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "RestSource",
                "requestMethod": "Post",
                "requestBody": "<body for POST REST request>",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="pagination-support"></a>Obsługa paginacji

Zwykle interfejs API REST ogranicza rozmiar ładunku odpowiedzi pojedynczego żądania pod rozsądną liczbą; podczas zwracania dużej ilości danych, dzieli wynik na wiele stron i wymaga od wywołujących do wysyłania kolejnych żądań, aby uzyskać następną stronę wyniku. Zwykle żądanie dla jednej strony jest dynamiczne i składa się z informacji zwróconych z odpowiedzi poprzedniej strony.

Ten ogólny złącze REST obsługuje następujące wzorce podziałki na strony: 

* Bezwzględny lub względny adres URL następnego żądania = wartość właściwości w bieżącej treści odpowiedzi
* Bezwzględny lub względny adres URL następnego żądania = wartość nagłówka w bieżących nagłówkach odpowiedzi
* Parametr kwerendy następnego żądania = wartość właściwości w bieżącej treści odpowiedzi
* Parametr kwerendy następnego żądania = wartość nagłówka w bieżących nagłówkach odpowiedzi
* Nagłówek następnego żądania = wartość właściwości w bieżącej treści odpowiedzi
* Nagłówek następnego żądania = wartość nagłówka w bieżących nagłówkach odpowiedzi

**Reguły podziałów na strony** są definiowane jako słownik w zestawie danych, który zawiera jedną lub więcej par klucz-wartość z uwzględnieniem wielkości liter. Konfiguracja będzie używana do generowania żądania, począwszy od drugiej strony. Łącznik zatrzyma iteracji, gdy pobiera kod stanu HTTP 204 (Brak zawartości) lub dowolnego wyrażenia JSONPath w "paginationRules" zwraca wartość null.

**Obsługiwane klucze** w regułach podziałów na strony:

| Klucz | Opis |
|:--- |:--- |
| Bezwzględny | Wskazuje adres URL do wydania następnego żądania. Może to być **bezwzględny adres URL lub względny adres URL**. |
| QueryParameters. *request_query_parameter* OR QueryParameters['request_query_parameter'] | "request_query_parameter" jest zdefiniowane przez użytkownika, które odwołuje się do nazwy jednego parametru kwerendy w następnym adresie URL żądania HTTP. |
| Nagłówki. *request_header* Or Nagłówki['request_header'] | "request_header" jest zdefiniowana przez użytkownika, która odwołuje się do jednej nazwy nagłówka w następnym żądaniu HTTP. |

**Obsługiwane wartości** w regułach podziałów na strony:

| Wartość | Opis |
|:--- |:--- |
| Nagłówki. *response_header* Or Nagłówki['response_header'] | "response_header" jest zdefiniowane przez użytkownika, który odwołuje się do jednej nazwy nagłówka w bieżącej odpowiedzi HTTP, której wartość będzie używana do wystawiania następnego żądania. |
| Wyrażenie JSONPath zaczynające się od "$" (reprezentujące katalog główny treści odpowiedzi) | Treść odpowiedzi powinna zawierać tylko jeden obiekt JSON. Wyrażenie JSONPath należy zwrócić pojedynczą wartość pierwotną, która będzie używana do wystawiania następnego żądania. |

**Przykład:**

Interfejs API wykresu Facebooka zwraca odpowiedź w następującej strukturze, w którym to przypadku adres URL następnej strony jest reprezentowany w ***pliku stronicowania.next:***

```json
{
    "data": [
        {
            "created_time": "2017-12-12T14:12:20+0000",
            "name": "album1",
            "id": "1809938745705498_1809939942372045"
        },
        {
            "created_time": "2017-12-12T14:14:03+0000",
            "name": "album2",
            "id": "1809938745705498_1809941802371859"
        },
        {
            "created_time": "2017-12-12T14:14:11+0000",
            "name": "album3",
            "id": "1809938745705498_1809941879038518"
        }
    ],
    "paging": {
        "cursors": {
            "after": "MTAxNTExOTQ1MjAwNzI5NDE=",
            "before": "NDMyNzQyODI3OTQw"
        },
        "previous": "https://graph.facebook.com/me/albums?limit=25&before=NDMyNzQyODI3OTQw",
        "next": "https://graph.facebook.com/me/albums?limit=25&after=MTAxNTExOTQ1MjAwNzI5NDE="
    }
}
```

Odpowiednia konfiguracja źródła działania `paginationRules` kopiowania REST, w szczególności, jest następująca:

```json
"typeProperties": {
    "source": {
        "type": "RestSource",
        "paginationRules": {
            "AbsoluteUrl": "$.paging.next"
        },
        ...
    },
    "sink": {
        "type": "<sink type>"
    }
}
```

## <a name="use-oauth"></a>Korzystanie z protokołu OAuth
W tej sekcji opisano sposób kopiowania danych ze łącznika REST do usługi Azure Data Lake Storage w formacie JSON przy użyciu funkcji OAuth. 

### <a name="about-the-solution-template"></a>Informacje o szablonie rozwiązania

Szablon zawiera dwa działania:
- Działanie **sieci Web** pobiera token nośnika, a następnie przekazuje go do kolejnych działań kopiowania jako autoryzacji.
- **Skopiuj** działanie kopiuje dane z rest do usługi Azure Data Lake Storage.

Szablon definiuje dwa parametry:
- **SinkContainer** to ścieżka folderu głównego, do której dane są kopiowane w magazynie usługi Azure Data Lake. 
- **SinkDirectory** to ścieżka katalogu w katalogu głównym, do którego dane są kopiowane w magazynie usługi Azure Data Lake. 

### <a name="how-to-use-this-solution-template"></a>Jak korzystać z tego szablonu rozwiązania

1. Przejdź do **kopii z REST lub HTTP przy użyciu szablonu OAuth.** Utwórz nowe połączenie dla połączenia źródłowego. 
    ![Tworzenie nowych połączeń](media/solution-template-copy-from-rest-or-http-using-oauth/source-connection.png)

    Poniżej znajdują się kluczowe kroki dla nowych ustawień usługi połączonej (REST):Below are key steps for new linked service (REST) settings:
    
     1. W obszarze **Podstawowy adres URL**określ parametr adresu URL dla własnej źródłowej usługi REST. 
     2. W przypadku **typu Uwierzytelnianie**wybierz pozycję *Anonimowy*.
        ![Nowe połączenie REST](media/solution-template-copy-from-rest-or-http-using-oauth/new-rest-connection.png)

2. Utwórz nowe połączenie dla połączenia docelowego.  
    ![Nowe połączenie Gen2](media/solution-template-copy-from-rest-or-http-using-oauth/destination-connection.png)

3. Wybierz pozycję **Użyj tego szablonu**.
    ![Użyj tego szablonu](media/solution-template-copy-from-rest-or-http-using-oauth/use-this-template.png)

4. Zostanie wyświetlony potok utworzony w sposób pokazany ![w poniższym przykładzie: Potok](media/solution-template-copy-from-rest-or-http-using-oauth/pipeline.png)

5. Wybierz pozycję **Aktywność w sieci Web.** W **ustawieniach**określ odpowiedni **adres URL**, **Metoda**, **Nagłówki**i **Treść,** aby pobrać token na okaziciela OAuth z interfejsu API logowania usługi, z której chcesz skopiować dane. Symbol zastępczy w szablonie prezentuje przykład usługi Azure Active Directory (AAD) OAuth. Uwaga Uwierzytelnianie AAD jest natywnie obsługiwane przez łącznik REST, oto tylko przykład przepływu OAuth. 

    | Właściwość | Opis |
    |:--- |:--- |:--- |
    | Adres URL |Określ adres URL, z jaki ma pobrać token okaziciela OAuth. np.https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/token |. 
    | Metoda | Metoda HTTP. Dozwolone wartości to **Księguj** i **Pobierz**. | 
    | Nagłówki | Nagłówek jest zdefiniowany przez użytkownika, który odwołuje się do jednej nazwy nagłówka w żądaniu HTTP. | 
    | Treść | Treść żądania HTTP. | 

    ![Potok](media/solution-template-copy-from-rest-or-http-using-oauth/web-settings.png)

6. W **obszarze Kopiowanie danych** aktywność wybierz *źródło* kartę, można zobaczyć, że token nośnika (access_token) pobrany z poprzedniego kroku zostanie przekazany do działania Kopiowania danych jako **autoryzacja** w obszarze Dodatkowe nagłówki. Przed rozpoczęciem uruchamiania potoku potwierdź ustawienia następujących właściwości.

    | Właściwość | Opis |
    |:--- |:--- |:--- | 
    | Metoda żądania | Metoda HTTP. Dozwolone wartości **to Get** (default) i **Post**. | 
    | Dodatkowe nagłówki | Dodatkowe nagłówki żądań HTTP.| 

   ![Uwierzytelnianie źródła kopiowania](media/solution-template-copy-from-rest-or-http-using-oauth/copy-data-settings.png)

7. Wybierz **debugowanie**, wprowadź **parametry**, a następnie wybierz **zakończ**.
   ![Przebieg rurociągu](media/solution-template-copy-from-rest-or-http-using-oauth/pipeline-run.png) 

8. Po pomyślnym zakończeniu wykonywania uruchomienia potoku wynik podobny do ![następującego przykładu: Wynik uruchomienia potoku](media/solution-template-copy-from-rest-or-http-using-oauth/run-result.png) 

9. Kliknij ikonę "Dane wyjściowe" WebActivity w **akcji** kolumny, zobaczysz access_token zwrócone przez usługę.

   ![Dane wyjściowe tokenu](media/solution-template-copy-from-rest-or-http-using-oauth/token-output.png) 

10. Kliknij ikonę "Wejście" **copyactivity** w akcji kolumny, zobaczysz access_token pobrane przez WebActivity jest przekazywana do CopyActivity do uwierzytelniania. 

    ![Dane wejściowe tokenu](media/solution-template-copy-from-rest-or-http-using-oauth/token-input.png)
        
    >[!CAUTION] 
    >Aby uniknąć logowania tokenu w postaci zwykłego tekstu, włącz opcję "Bezpieczne dane wyjściowe" w aktywności sieci Web i "Bezpieczne dane wejściowe" w działaniu kopiowania.


## <a name="export-json-response-as-is"></a>Eksportowanie odpowiedzi JSON w stanie stanu

Za pomocą tego łącznika REST można eksportować odpowiedź JSON interfejsu API REST w stanie takim, w jakim jest w różnych magazynach opartych na plikach. Aby osiągnąć taką kopię niezależną od schematu, pomiń sekcję "struktura" (zwaną również *schematem)* w zestawie danych i mapowanie schematu w działaniu kopiowania.

## <a name="schema-mapping"></a>Mapowanie schematu

Aby skopiować dane z punktu końcowego REST do ujścia tabelaryczne, zapoznaj się [z mapowaniem schematu](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać listę magazynów danych, które działanie kopiowania obsługuje jako źródła i pochłaniacze w usłudze Azure Data Factory, zobacz [Obsługiwane magazyny danych i formaty](copy-activity-overview.md#supported-data-stores-and-formats).
