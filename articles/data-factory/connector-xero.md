---
title: Kopiowanie danych z Xero za pomocą Azure Data Factory
description: Informacje o kopiowaniu danych z programu Xero do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: jingwang
ms.openlocfilehash: 342d0aabe2222393f33aa4ce93646da9f29cf1fb
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2020
ms.locfileid: "92926465"
---
# <a name="copy-data-from-xero-using-azure-data-factory"></a>Kopiowanie danych z Xero za pomocą Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z Xero. Jest ona oparta na [przeglądzie działania kopiowania](copy-activity-overview.md) , która przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik Xero jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Dane z Xero można skopiować do dowolnego obsługiwanego magazynu danych ujścia. Listę magazynów danych obsługiwanych jako źródła/ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) .

Ten łącznik Xero obsługuje:

- Xero [aplikację prywatną](https://developer.xero.com/documentation/getting-started/getting-started-guide) , ale nie aplikację publiczną.
- Wszystkie tabele Xero (punkty końcowe interfejsu API) z wyjątkiem "Reports".
- Uwierzytelnianie OAuth 1,0 i OAuth 2,0.

Azure Data Factory udostępnia wbudowany sterownik umożliwiający połączenie, dlatego nie trzeba ręcznie instalować żadnego sterownika przy użyciu tego łącznika.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Data Factory specyficznych dla łącznika Xero.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Dla połączonej usługi Xero są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type musi mieć wartość: **Xero** | Tak |
| connectionProperties | Grupa właściwości, która definiuje sposób nawiązywania połączenia z Xero. | Tak |
| **_W obszarze `connectionProperties` :_* _ | | |
| host | Punkt końcowy serwera Xero ( `api.xero.com` ).  | Tak |
| authenticationType | Dozwolone wartości to `OAuth_2.0` i `OAuth_1.0` . | Tak |
| consumerKey | Klucz klienta skojarzony z aplikacją Xero. Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). | Tak |
| privateKey | Klucz prywatny z pliku PEM, który został wygenerowany dla aplikacji prywatnej Xero, zobacz [Tworzenie pary kluczy publicznych/prywatnych](https://developer.xero.com/documentation/auth-and-limits/create-publicprivate-key). Uwaga do *wygenerowania PrivateKey. pem z numbitsem 512* * using `openssl genrsa -out privatekey.pem 512` , 1024 nie jest obsługiwana. Dołącz cały tekst z pliku PEM włącznie z zakończeniami wierszy systemu UNIX (\n), zobacz przykład poniżej.<br/>Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). | Tak |
| tenantId | Identyfikator dzierżawy skojarzony z aplikacją Xero. Dotyczy uwierzytelniania OAuth 2,0.<br>Dowiedz się, jak uzyskać identyfikator dzierżawy od [sprawdzenia dzierżawców, do których](https://developer.xero.com/documentation/oauth2/auth-flow)masz uprawnienia dostępu. | Tak dla uwierzytelniania OAuth 2,0 |
| refreshToken | Dotyczy uwierzytelniania OAuth 2,0.<br/>Token odświeżania OAuth 2,0 jest skojarzony z aplikacją Xero i używany do odświeżania tokenu dostępu. token dostępu wygasa po 30 minutach. Dowiedz się więcej o tym, jak działa przepływ autoryzacji Xero oraz jak uzyskać token odświeżania z [tego artykułu](https://developer.xero.com/documentation/oauth2/auth-flow). Aby uzyskać token odświeżania, należy zażądać [zakresu offline_access](https://developer.xero.com/documentation/oauth2/scopes). <br/>Informacje o **ograniczeniu** : Uwaga Xero resetuje token odświeżania, gdy jest używany do odświeżania tokenu dostępu. W przypadku obciążenia operacyjnego przed każdym uruchomieniem działania kopiowania należy ustawić prawidłowy token odświeżania na potrzeby funkcji ADF.<br/>Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). | Tak dla uwierzytelniania OAuth 2,0 |
| useEncryptedEndpoints | Określa, czy punkty końcowe źródła danych są szyfrowane przy użyciu protokołu HTTPS. Wartością domyślną jest true.  | Nie |
| useHostVerification | Określa, czy nazwa hosta jest wymagana w certyfikacie serwera, aby odpowiadała nazwie hosta serwera podczas nawiązywania połączenia za pośrednictwem protokołu TLS. Wartością domyślną jest true.  | Nie |
| usePeerVerification | Określa, czy należy zweryfikować tożsamość serwera podczas łączenia za pośrednictwem protokołu TLS. Wartością domyślną jest true.  | Nie |

**Przykład: uwierzytelnianie OAuth 2,0**

```json
{
    "name": "XeroLinkedService",
    "properties": {
        "type": "Xero",
        "typeProperties": {
            "connectionProperties": { 
                "host": "api.xero.com",
                "authenticationType":"OAuth_2.0", 
                "consumerKey": {
                    "type": "SecureString",
                    "value": "<consumer key>"
                },
                "privateKey": {
                    "type": "SecureString",
                    "value": "<private key>"
                },
                "tenantId": "<tenant ID>", 
                "refreshToken": {
                    "type": "SecureString",
                    "value": "<refresh token>"
                }, 
                "useEncryptedEndpoints": true, 
                "useHostVerification": true, 
                "usePeerVerification": true
            }            
        }
    }
}
```

**Przykład: uwierzytelnianie OAuth 1,0**

```json
{
    "name": "XeroLinkedService",
    "properties": {
        "type": "Xero",
        "typeProperties": {
            "connectionProperties": {
                "host": "api.xero.com", 
                "authenticationType":"OAuth_1.0", 
                "consumerKey": {
                    "type": "SecureString",
                    "value": "<consumer key>"
                },
                "privateKey": {
                    "type": "SecureString",
                    "value": "<private key>"
                }, 
                "useEncryptedEndpoints": true,
                "useHostVerification": true,
                "usePeerVerification": true
            }
        }
    }
}
```

**Wartość przykładowego klucza prywatnego:**

Dołącz cały tekst z pliku PEM włącznie z zakończeniami wierszy systemu UNIX (\n).

```
"-----BEGIN RSA PRIVATE KEY-----\nMII***************************************************P\nbu****************************************************s\nU/****************************************************B\nA*****************************************************W\njH****************************************************e\nsx*****************************************************l\nq******************************************************X\nh*****************************************************i\nd*****************************************************s\nA*****************************************************dsfb\nN*****************************************************M\np*****************************************************Ly\nK*****************************************************Y=\n-----END RSA PRIVATE KEY-----"
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Xero.

Aby skopiować dane z Xero, ustaw właściwość Type zestawu danych na **XeroObject** . Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type zestawu danych musi być ustawiona na wartość: **XeroObject** | Tak |
| tableName | Nazwa tabeli. | Nie (Jeśli określono "zapytanie" w źródle aktywności) |

**Przykład**

```json
{
    "name": "XeroDataset",
    "properties": {
        "type": "XeroObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Xero linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło Xero.

### <a name="xero-as-source"></a>Xero jako źródło

Aby skopiować dane z Xero, ustaw typ źródła w działaniu Copy na **XeroSource** . W sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type źródła działania Copy musi być ustawiona na wartość: **XeroSource** | Tak |
| query | Użyj niestandardowego zapytania SQL, aby odczytać dane. Na przykład: `"SELECT * FROM Contacts"`. | Nie (Jeśli określono "TableName" w zestawie danych) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromXero",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Xero input dataset name>",
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
                "type": "XeroSource",
                "query": "SELECT * FROM Contacts"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Podczas określania zapytania Xero należy zwrócić uwagę na następujące kwestie:

- Tabele ze złożonymi elementami zostaną podzielone na wiele tabel. Na przykład transakcje bankowe mają złożoną strukturę danych "LineItems", więc dane transakcji bankowej są mapowane na tabelę `Bank_Transaction` i `Bank_Transaction_Line_Items` , za pomocą `Bank_Transaction_ID` klucza obcego, aby połączyć je ze sobą.

- Dane Xero są dostępne w dwóch schematach: `Minimal` (domyślnie) i `Complete` . Kompletny schemat zawiera tabele wywołań wymaganych wstępnie, które wymagają dodatkowych danych (np. kolumny identyfikatora) przed wykonaniem żądanego zapytania.

Poniższe tabele zawierają te same informacje w schemacie minimalnej i kompletnej. Aby zmniejszyć liczbę wywołań interfejsu API, użyj minimalnego schematu (domyślnie).

- Bank_Transactions
- Contact_Groups 
- Kontakty 
- Contacts_Sales_Tracking_Categories 
- Contacts_Phones 
- Contacts_Addresses 
- Contacts_Purchases_Tracking_Categories 
- Credit_Notes 
- Credit_Notes_Allocations 
- Expense_Claims 
- Expense_Claim_Validation_Errors
- Faktury 
- Invoices_Credit_Notes
- Invoices_ przedpłaty 
- Invoices_Overpayments 
- Manual_Journals 
- Nadpłaty 
- Overpayments_Allocations 
- Opłat 
- Prepayments_Allocations 
- Rachunki 
- Receipt_Validation_Errors 
- Tracking_Categories

Tylko następujące tabele mogą być zapytania z kompletnym schematem:

- Complete.Bank_Transaction_Line_Items 
- Complete.Bank_Transaction_Line_Item_Tracking 
- Complete.Contact_Group_Contacts 
- Complete.Contacts_Contact_ osoby 
- Complete.Credit_Note_Line_Items 
- Complete.Credit_Notes_Line_Items_Tracking 
- Complete.Expense_Claim_ płatności 
- Complete.Expense_Claim_Receipts 
- Complete.Invoice_Line_Items 
- Complete.Invoices_Line_Items_Tracking
- Complete.Manual_Journal_Lines 
- Complete.Manual_Journal_Line_Tracking 
- Complete.Overpayment_Line_Items 
- Complete.Overpayment_Line_Items_Tracking 
- Complete.Prepayment_Line_Items 
- Complete.Prepayment_Line_Item_Tracking 
- Complete.Receipt_Line_Items 
- Complete.Receipt_Line_Item_Tracking 
- Complete.Tracking_Category_Options

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).


## <a name="next-steps"></a>Następne kroki
Listę obsługiwanych magazynów danych przez działanie kopiowania można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
