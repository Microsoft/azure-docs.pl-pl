---
title: Zarządzanie kontem Azure Data Lake Storage Gen1 przy użyciu interfejsu REST
description: Użyj interfejsu API REST WebHDFS w celu wykonania operacji zarządzania kontami na koncie Azure Data Lake Storage Gen1.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 13467a51b2a06dbc0ca0ec5eadd139fde8b82ad0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92103496"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>Operacje zarządzania kontem na Azure Data Lake Storage Gen1 przy użyciu interfejsu API REST
> [!div class="op_single_selector"]
> * [Zestaw SDK platformy .NET](data-lake-store-get-started-net-sdk.md)
> * [Interfejs API REST](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

W tym artykule dowiesz się, jak wykonywać operacje zarządzania kontami na Azure Data Lake Storage Gen1 przy użyciu interfejsu API REST. Operacje zarządzania kontem obejmują tworzenie konta Data Lake Storage Gen1, usuwanie konta Data Lake Storage Gen1 itd. Aby uzyskać instrukcje dotyczące sposobu wykonywania operacji systemu plików na Data Lake Storage Gen1 przy użyciu interfejsu API REST, zobacz [operacje systemu plików na Data Lake Storage Gen1 przy użyciu interfejsu API REST](data-lake-store-data-operations-rest-api.md).

## <a name="prerequisites"></a>Wymagania wstępne
* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **[zwinięcie](https://curl.haxx.se/)**. W tym artykule opisano sposób tworzenia wywołań interfejsu API REST na koncie Data Lake Storage Gen1.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Jak wykonać uwierzytelnianie przy użyciu usługi Azure Active Directory?
Dostępne są dwa podejścia do uwierzytelniania za pomocą usługi Azure Active Directory.

* W przypadku uwierzytelniania użytkownika końcowego dla aplikacji (Interactive) zobacz [uwierzytelnianie użytkowników końcowych za pomocą Data Lake Storage Gen1 przy użyciu zestawu .NET SDK](data-lake-store-end-user-authenticate-rest-api.md).
* Aby uzyskać uwierzytelnianie między usługami dla aplikacji (nieinteraktywną), zobacz Uwierzytelnianie między usługami i [Data Lake Storage Gen1 przy użyciu zestawu .NET SDK](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-a-data-lake-storage-gen1-account"></a>Tworzenie konta usługi Data Lake Storage Gen1
Ta operacja jest oparta na wywołaniu interfejsu API REST zdefiniowanym [tutaj](/rest/api/datalakestore/accounts/create).

Użyj następującego polecenia cURL. Zamień **\<yourstoragegen1name>** na nazwę Data Lake Storage Gen1.

```console
curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview -d@"C:\temp\input.json"
```

W poleceniu powyżej zastąp ciąg \<`REDACTED`\> tokenem autoryzacji pobranym wcześniej. Ładunek żądania dla tego polecenia jest zawarty w pliku **input.json**, który jest udostępniany dla parametru `-d` powyżej. Zawartość pliku input.json przypomina następujący fragment kodu:

```json
{
"location": "eastus2",
"tags": {
    "department": "finance"
    },
"properties": {}
}
```

## <a name="delete-a-data-lake-storage-gen1-account"></a>Usuwanie konta Data Lake Storage Gen1
Ta operacja jest oparta na wywołaniu interfejsu API REST zdefiniowanym [tutaj](/rest/api/datalakestore/accounts/delete).

Aby usunąć konto Data Lake Storage Gen1, użyj następującego polecenia. Zastąp ciąg **\<yourstoragegen1name>** nazwą konta Data Lake Storage Gen1.

```console
curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview
```

Powinny pojawić się dane wyjściowe podobne do następującego fragmentu kodu:

```output
HTTP/1.1 200 OK
...
...
```

## <a name="next-steps"></a>Następne kroki
* [Operacje systemu plików na Data Lake Storage Gen1 przy użyciu interfejsu API REST](data-lake-store-data-operations-rest-api.md).

## <a name="see-also"></a>Zobacz też
* [Dokumentacja interfejsu API REST Azure Data Lake Storage Gen1](/rest/api/datalakestore/)
* [Aplikacje do obsługi dużych ilości danych open source zgodne z Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)