---
title: Uwierzytelnianie REST-Service-to-Service — Data Lake Storage Gen1 na platformie Azure
description: Dowiedz się, jak uzyskać uwierzytelnianie między usługami Azure Data Lake Storage Gen1 i Azure Active Directory przy użyciu interfejsu API REST.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 4947bab58a6d23bcc3e3212a9e3e7dc0c4fa8578
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92106709"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Uwierzytelnianie między usługami Azure Data Lake Storage Gen1 przy użyciu interfejsu API REST
> [!div class="op_single_selector"]
> * [Korzystanie z języka Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Korzystanie z zestawu SDK dla platformy .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Korzystanie z języka Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Korzystanie z interfejsu API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
> 

W tym artykule dowiesz się, jak używać interfejsu API REST do uwierzytelniania między usługami przy użyciu Azure Data Lake Storage Gen1. W przypadku uwierzytelniania użytkowników końcowych przy użyciu Data Lake Storage Gen1 korzystania z interfejsu API REST zobacz [uwierzytelnianie użytkowników końcowych za pomocą Data Lake Storage Gen1 przy użyciu interfejsu API REST](data-lake-store-end-user-authenticate-rest-api.md).

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Utwórz Azure Active Directory aplikacji "Web"**. Należy wykonać kroki opisane w temacie [uwierzytelnianie między usługami Data Lake Storage Gen1 przy użyciu Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="service-to-service-authentication"></a>Uwierzytelnianie między usługami

W tym scenariuszu aplikacja udostępnia własne poświadczenia do wykonywania operacji. W tym celu należy wydać żądanie POST, tak jak pokazano w poniższym fragmencie kodu:

```console
curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
  -F grant_type=client_credentials \
  -F resource=https://management.core.windows.net/ \
  -F client_id=<CLIENT-ID> \
  -F client_secret=<AUTH-KEY>
```

Dane wyjściowe żądania zawierają Token autoryzacji (wskazywane przez `access-token` w danych wyjściowych poniżej), które następnie są przekazywane do wywołań interfejsu API REST. Zapisz token uwierzytelniania w pliku tekstowym; będzie on potrzebny podczas tworzenia wywołań REST do Data Lake Storage Gen1.

```output
{"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}
```

W tym artykule wykorzystano podejście **nieinterakcyjne**. Więcej informacji na temat podejścia nieinterakcyjnego (wywołań między usługami) zawiera temat [Service to service calls using credentials](/previous-versions/azure/dn645543(v=azure.100)) (Wywołania między usługami przy użyciu poświadczeń).

## <a name="next-steps"></a>Następne kroki

W tym artykule pokazano, jak za pomocą uwierzytelniania między usługami uwierzytelniać się za pomocą usługi Data Lake Storage Gen1 przy użyciu interfejsu API REST. Teraz możesz zapoznać się z następującymi artykułami dotyczącymi korzystania z interfejsu API REST w celu pracy z Data Lake Storage Gen1.

* [Operacje zarządzania kontem na Data Lake Storage Gen1 przy użyciu interfejsu API REST](data-lake-store-get-started-rest-api.md)
* [Operacje na danych na Data Lake Storage Gen1 przy użyciu interfejsu API REST](data-lake-store-data-operations-rest-api.md)