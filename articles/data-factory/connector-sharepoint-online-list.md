---
title: Kopiowanie danych z listy usługi SharePoint Online przy użyciu Azure Data Factory
description: Informacje o kopiowaniu danych z listy usługi SharePoint Online do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: jingwang
ms.openlocfilehash: f560a01c4ec00649157a9c43aedf0ed6cfc2e050
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83871918"
---
# <a name="copy-data-from-sharepoint-online-list-by-using-azure-data-factory"></a>Kopiowanie danych z listy usługi SharePoint Online przy użyciu Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z listy usługi SharePoint Online. Artykuł jest oparty na [działaniu kopiowania w Azure Data Factory](copy-activity-overview.md), co przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik listy usługi SharePoint Online jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Dane z listy usługi SharePoint Online można kopiować do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych obsługiwanych przez działanie kopiowania jako źródła i ujścia, zobacz [obsługiwane magazyny i formaty danych](copy-activity-overview.md#supported-data-stores-and-formats).

W każdym przypadku ten łącznik online listy programu SharePoint używa uwierzytelniania jednostki usługi i pobiera dane za pośrednictwem protokołu OData.

> [!TIP]
> Ten łącznik obsługuje kopiowanie danych z **listy** usługi SharePoint Online, ale nie pliku. Dowiedz się, jak skopiować plik z sekcji [Kopiuj plik z usługi SharePoint Online](#copy-file-from-sharepoint-online) .

## <a name="prerequisites"></a>Wymagania wstępne

Łącznik online z listą programu SharePoint używa uwierzytelniania jednostki usługi w celu nawiązania połączenia z programem SharePoint. Wykonaj następujące kroki, aby je skonfigurować:

1. Zarejestruj jednostkę aplikacji w Azure Active Directory (Azure AD), wykonując czynności opisane w temacie [Rejestrowanie aplikacji w dzierżawie usługi Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Należy zwrócić uwagę na następujące wartości, których można użyć do zdefiniowania połączonej usługi:

    - Identyfikator aplikacji
    - Klucz aplikacji
    - Identyfikator dzierżawy

2. Przyznaj witrynie usługi SharePoint Online uprawnienia do zarejestrowanej aplikacji: 

    > [!NOTE]
    > Ta operacja wymaga uprawnienia właściciela witryny usługi SharePoint Online. Właściciela można znaleźć, przechodząc do strony głównej witryny — > kliknij "składowe X" w prawym górnym rogu — > Sprawdź, kto ma rolę "Owner".

    1. Otwórz łącze witryny usługi SharePoint Online, np. `https://[your_site_url]/_layouts/15/appinv.aspx` (Zastąp adres URL witryny).
    2. Wyszukaj zarejestrowany identyfikator aplikacji, Wypełnij puste pola i kliknij przycisk "Utwórz".

        - Domena aplikacji: `localhost.com`
        - Adres URL przekierowania: `https://www.localhost.com`
        - KOD XML żądania uprawnień:

        ```xml
        <AppPermissionRequests AllowAppOnlyPolicy="true">
            <AppPermissionRequest Scope="http://sharepoint/content/sitecollection/web" Right="Read"/>
        </AppPermissionRequests>
        ```

        ![uprawnienie Grant programu SharePoint](media/connector-sharepoint-online-list/sharepoint-online-grant-permission.png)

    3. Kliknij pozycję "ufaj temu" dla tej aplikacji.

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, których można użyć do definiowania jednostek Data Factory, które są specyficzne dla łącznika list usługi SharePoint Online.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Następujące właściwości są obsługiwane dla połączonej usługi programu SharePoint Online list:

| **Właściwość**        | **Opis**                                              | **Wymagane** |
| ------------------- | ------------------------------------------------------------ | ------------ |
| typ                | Właściwość Type musi mieć wartość: **SharePointOnlineList**.  | Tak          |
| siteUrl             | Adres URL witryny usługi SharePoint Online, np. `https://contoso.sharepoint.com/sites/siteName` . | Tak          |
| servicePrincipalId  | Identyfikator aplikacji (klienta) zarejestrowanej w Azure Active Directory. | Tak          |
| servicePrincipalKey | Klucz aplikacji. Oznacz to pole jako element **SecureString** , aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). | Tak          |
| tenantId            | Identyfikator dzierżawy, pod którym znajduje się Twoja aplikacja.          | Tak          |
| Właściwością connectvia          | [Integration Runtime](concepts-integration-runtime.md) używany do nawiązywania połączenia z magazynem danych. Więcej informacji znajduje się w sekcji [wymagania wstępne](#prerequisites)w tym artykule. Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. | Nie           |

**Przykład:**

```json
{
    "name": "SharePointOnlineList",
    "properties": {
        "type": "SharePointOnlineList",
        "typeProperties": {
            "siteUrl": "<site URL>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenantId": "<tenant ID>"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby zapoznać się z pełną listą sekcji i właściwości, które są dostępne do definiowania zestawów danych, zobacz [zestawy danych i połączone usługi](concepts-datasets-linked-services.md). Poniższa sekcja zawiera listę właściwości obsługiwanych przez zestaw danych tabeli SAP.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość **Type** zestawu danych musi być ustawiona na wartość **SharePointOnlineLResource**. | Tak |
| listName | Nazwa listy usługi SharePoint Online. | Tak |

**Przykład**

```json
{
    "name": "SharePointOnlineListDataset",
    "properties":
    {
        "type": "SharePointOnlineListResource",
        "linkedServiceName": {
            "referenceName": "<SharePoint Online List linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties":
        {
            "listName": "<name of the list>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby zapoznać się z pełną listą sekcji i właściwości, które są dostępne do definiowania działań, zobacz [potoki](concepts-pipelines-activities.md).  Poniższa sekcja zawiera listę właściwości obsługiwanych przez źródło listy usługi SharePoint Online.

### <a name="sharepoint-online-list-as-source"></a>Lista usługi SharePoint Online jako źródło

Aby skopiować dane z listy usługi SharePoint Online, w sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość **Type** źródła działania Copy musi być ustawiona na wartość **SharePointOnlineListSource**. | Tak |
| query | Niestandardowe opcje zapytania OData dotyczące filtrowania danych. Przykład: `"$top=10&$select=Title,Number"`. | Nie |
| httpRequestTimeout | Limit czasu (w drugim) żądania HTTP w celu uzyskania odpowiedzi. Wartość domyślna to 300 (5 minut). | Nie |

**Przykład**

```json
"activities":[
    {
        "name": "CopyFromSharePointOnlineList",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SharePoint Online List input dataset name>",
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
            "source": {
                "type": "SharePointOnlineListSource",
                "query": "<OData query e.g. $top=10&$select=Title,Number>"
            }, 
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sharepoint-online-list"></a>Mapowanie typu danych dla listy usługi SharePoint Online

Podczas kopiowania danych z listy usługi SharePoint Online następujące mapowania są używane między typami danych list usługi SharePoint Online a Azure Data Factory pośrednimi typami danych. 

| **Typ danych usługi SharePoint Online**                 | **Typ danych OData**                                  | **Azure Data Factory typ danych pośrednich** |
| ----------------------------------------------- | ---------------------------------------------------- | ---------------------------------------- |
| Pojedynczy wiersz tekstu                             | Edm.String                                           | Ciąg                                   |
| Wiele wierszy tekstu                          | Edm.String                                           | Ciąg                                   |
| Wybór (menu do wyboru)                    | Edm.String                                           | Ciąg                                   |
| Liczba (1, 1,0, 100)                            | Edm.Double                                           | Double                                   |
| Waluta ($, ¥, €)                              | Edm.Double                                           | Double                                   |
| Data i godzina                                   | EDM. DateTime                                         | DateTime                                 |
| Odnośnik (informacje znajdujące się już w tej lokacji)       | Edm.Int32                                            | Int32                                    |
| Tak/nie (pole wyboru)                              | Edm.Boolean                                          | Boolean (wartość logiczna)                                  |
| Osoba lub grupa                                 | Edm.Int32                                            | Int32                                    |
| Hiperłącze lub obraz                            | Edm.String                                           | Ciąg                                   |
| Obliczone (obliczanie na podstawie innych kolumn) | EDM. String/EDM. Double/EDM. DateTime/EDM. Boolean | Ciąg/Double/DateTime/wartość logiczna     |
| Załącznik                                      | Nieobsługiwane                                        |                                          |
| Wynik zadania                                    | Nieobsługiwane                                        |                                          |
| Dane zewnętrzne                                   | Nieobsługiwane                                        |                                          |
| Zarządzane metadane                                | Nieobsługiwane                                        |                                          |

## <a name="copy-file-from-sharepoint-online"></a>Kopiuj plik z usługi SharePoint Online

Możesz skopiować plik z usługi SharePoint Online za pomocą **działania sieci Web** , aby uwierzytelnić i uzyskać token dostępu z spo, a następnie przekazać do kolejnego **działania kopiowania** , aby skopiować dane z **łącznikiem http jako źródło**.

![przepływ pliku kopiowania programu SharePoint](media/connector-sharepoint-online-list/sharepoint-online-copy-file-flow.png)

1. Postępuj zgodnie z sekcją [wymagania wstępne](#prerequisites) , aby utworzyć aplikację usługi AAD i udzielić uprawnienia do usługi SharePoint Online. 

2. Utwórz **działanie sieci Web** , aby uzyskać token dostępu z usługi SharePoint Online:

    - **Adres URL**: `https://accounts.accesscontrol.windows.net/[Tenant-ID]/tokens/OAuth/2` . Zastąp identyfikator dzierżawy.
    - **Metoda**: post
    - **Nagłówki**:
        - Content-Type: application/x-www-form-urlencoded
    - **Treść**:  `grant_type=client_credentials&client_id=[Client-ID]@[Tenant-ID]&client_secret=[Client-Secret]&resource=00000003-0000-0ff1-ce00-000000000000/[Tenant-Name].sharepoint.com@[Tenant-ID]` . Zastąp identyfikator klienta, klucz tajny klienta, identyfikator dzierżawy i nazwę dzierżawy.

    > [!CAUTION]
    > Ustaw opcję bezpiecznego wyjścia na true w działaniu sieci Web, aby zapobiec zarejestrowaniu wartości tokenu w postaci zwykłego tekstu. Wszelkie dalsze działania, które zużywają tę wartość, powinny mieć ustawioną opcję bezpiecznego wprowadzania wartość true.

3. Łańcuch z **działaniem kopiowania** z łącznikiem http jako źródło do kopiowania zawartości pliku usługi SharePoint Online:

    - Połączona usługa HTTP:
        - **Podstawowy adres URL**: `https://[site-url]/_api/web/GetFileByServerRelativeUrl('[relative-path-to-file]')/$value` . Zastąp adres URL witryny i ścieżkę względną do pliku. Przykład ścieżki względnej do pliku jako `/sites/site2/Shared Documents/TestBook.xlsx` .
        - **Typ uwierzytelniania:** Anonimowe *(aby użyć tokenu okaziciela skonfigurowanego w źródle działania kopiowania później)*
    - Zestaw danych: wybierz odpowiedni format. Aby skopiować plik jako-is, wybierz typ "Binary".
    - Źródło działania kopiowania:
        - **Metoda żądania**: Get
        - **Dodatkowy nagłówek**: Użyj następującego wyrażenia `@{concat('Authorization: Bearer ', activity('<Web-activity-name>').output.access_token)}` , które używa tokenu okaziciela wygenerowanego przez nadrzędne działanie sieci Web jako nagłówek autoryzacji. Zastąp nazwę działania sieci Web.
    - Skonfiguruj jako zwykły obiekt ujścia działania kopiowania.

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać listę magazynów danych obsługiwanych przez działanie kopiowania jako źródła i ujścia w Azure Data Factory, zobacz [obsługiwane magazyny i formaty danych](copy-activity-overview.md#supported-data-stores-and-formats).
