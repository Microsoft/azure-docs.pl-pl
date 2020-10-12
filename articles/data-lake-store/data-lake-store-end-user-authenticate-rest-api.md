---
title: Uwierzytelnianie użytkowników końcowych — zaplecze Data Lake Storage Gen1 — Azure
description: Dowiedz się, jak uzyskać uwierzytelnianie użytkowników końcowych za pomocą Azure Data Lake Storage Gen1 przy użyciu Azure Active Directory przy użyciu interfejsu API REST
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 84e85e6e817972b8ec0bee0e8b441b3585d2d9dd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85984855"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Uwierzytelnianie użytkowników końcowych za pomocą Azure Data Lake Storage Gen1 korzystania z interfejsu API REST
> [!div class="op_single_selector"]
> * [Korzystanie z języka Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Korzystanie z zestawu SDK dla platformy .NET](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Korzystanie z języka Python](data-lake-store-end-user-authenticate-python.md)
> * [Korzystanie z interfejsu API REST](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

Ten artykuł zawiera informacje na temat korzystania z interfejsu API REST w celu uwierzytelniania użytkowników końcowych przy użyciu Azure Data Lake Storage Gen1. Aby uzyskać uwierzytelnianie między usługami Data Lake Storage Gen1 przy użyciu interfejsu API REST, zobacz Uwierzytelnianie między usługami i usługą [Data Lake Storage Gen1 przy użyciu interfejsu API REST](data-lake-store-service-to-service-authenticate-rest-api.md).

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Utwórz Azure Active Directory aplikację "native"**. Należy wykonać czynności opisane w temacie [uwierzytelnianie użytkowników końcowych z Data Lake Storage Gen1 przy użyciu Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

* **[zwinięcie](https://curl.haxx.se/)**. W tym artykule opisano sposób tworzenia wywołań interfejsu API REST na koncie Data Lake Storage Gen1.

## <a name="end-user-authentication"></a>Uwierzytelnianie użytkowników końcowych
Zaleca się, aby użytkownik mógł zalogować się do aplikacji przy użyciu usługi Azure AD. Aplikacja może uzyskiwać dostęp do zasobów platformy Azure z takim samym poziomem dostępu jak zalogowany użytkownik. Użytkownik musi okresowo podawać poświadczenia, aby aplikacja mogła zachować dostęp.

Wynik posiadania logowania użytkownika końcowego polega na tym, że aplikacja uzyskuje token dostępu i token odświeżania. Token dostępu jest dołączany do każdego żądania wykonanego do Data Lake Storage Gen1 lub Data Lake Analytics i jest domyślnie ważny przez jedną godzinę. Tokenu odświeżania można użyć, aby uzyskać nowy token dostępu i jest on ważny przez maksymalnie dwa tygodnie domyślnie, jeśli jest używany regularnie. Możesz użyć dwóch różnych metod logowania użytkownika końcowego.

W tym scenariuszu aplikacja wyświetla monit o zalogowanie się i wówczas wszystkie operacje są wykonywane w kontekście zalogowanego użytkownika. Wykonaj następujące czynności:

1. Za pomocą aplikacji przekieruj użytkownika pod następujący adres URL:

    `https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<APPLICATION-ID>&response_type=code&redirect_uri=<REDIRECT-URI>`

   > [!NOTE]
   > \<REDIRECT-URI> należy zakodować do użycia w adresie URL. Tak więc, for https://localhost , USE `https%3A%2F%2Flocalhost` )

    Na potrzeby tego samouczka możesz zastąpić symbole zastępcze w powyższym adresie URL i wkleić go w pasku adresu przeglądarki sieci web. Nastąpi przekierowanie w celu uwierzytelniania przy użyciu identyfikatora logowania do platformy Azure. Gdy pomyślnie się zalogujesz, odpowiedź zostanie wyświetlona na pasku adresu przeglądarki. Odpowiedź będzie miała następujący format:

    `http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>`

2. Przechwyć kod autoryzacji z odpowiedzi. Na potrzeby tego samouczka można skopiować kod autoryzacji z paska adresu przeglądarki sieci Web i przekazać go w żądaniu POST do punktu końcowego tokenu, jak pokazano w poniższym fragmencie kodu:

    ```console
    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
    -F redirect_uri=<REDIRECT-URI> \
    -F grant_type=authorization_code \
    -F resource=https://management.core.windows.net/ \
    -F client_id=<APPLICATION-ID> \
    -F code=<AUTHORIZATION-CODE>
    ```

   > [!NOTE]
   > W takim przypadku \<REDIRECT-URI> nie trzeba zakodować.
   > 
   > 

3. Odpowiedź jest obiektem JSON, który zawiera token dostępu (na przykład `"access_token": "<ACCESS_TOKEN>"` ) i token odświeżania (na przykład `"refresh_token": "<REFRESH_TOKEN>"` ). Aplikacja używa tokenu dostępu podczas uzyskiwania dostępu do Azure Data Lake Storage Gen1 i tokenu odświeżania, aby uzyskać inny token dostępu po wygaśnięciu tokenu dostępu.

    ```json
    {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":    "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}
    ```

4. Po wygaśnięciu tokenu dostępu można zażądać nowego tokenu dostępu przy użyciu tokenu odświeżania, jak pokazano w poniższym fragmencie kodu:

    ```console
    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
         -F grant_type=refresh_token \
         -F resource=https://management.core.windows.net/ \
         -F client_id=<APPLICATION-ID> \
         -F refresh_token=<REFRESH-TOKEN>
    ```

Więcej informacji na temat interakcyjnego uwierzytelniania użytkownika zawiera temat [Authorization code grant flow](https://msdn.microsoft.com/library/azure/dn645542.aspx) (Przepływ udzielania kodu autoryzacji).

## <a name="next-steps"></a>Następne kroki
W tym artykule pokazano, jak za pomocą uwierzytelniania między usługami uwierzytelniać się za pomocą usługi Azure Data Lake Storage Gen1 przy użyciu interfejsu API REST. Teraz możesz zapoznać się z następującymi artykułami dotyczącymi korzystania z interfejsu API REST w celu pracy z Azure Data Lake Storage Gen1.

* [Operacje zarządzania kontem na Data Lake Storage Gen1 przy użyciu interfejsu API REST](data-lake-store-get-started-rest-api.md)
* [Operacje na danych na Data Lake Storage Gen1 przy użyciu interfejsu API REST](data-lake-store-data-operations-rest-api.md)

