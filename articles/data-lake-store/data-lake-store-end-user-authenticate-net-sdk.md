---
title: Uwierzytelnianie użytkowników końcowych — platforma .NET z Data Lake Storage Gen1 — Azure
description: Dowiedz się, jak uzyskać uwierzytelnianie użytkowników końcowych za pomocą Azure Data Lake Storage Gen1 przy użyciu Azure Active Directory z zestawem SDK dla platformy .NET
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: devx-track-csharp
ms.openlocfilehash: 67ba4f12aec9e987d79109b7197d03301bf40650
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89004786"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>Uwierzytelnianie użytkowników końcowych za pomocą Azure Data Lake Storage Gen1 przy użyciu zestawu .NET SDK
> [!div class="op_single_selector"]
> * [Korzystanie z języka Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Korzystanie z zestawu SDK dla platformy .NET](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Korzystanie z języka Python](data-lake-store-end-user-authenticate-python.md)
> * [Korzystanie z interfejsu API REST](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

Ten artykuł zawiera informacje na temat używania zestawu .NET SDK do uwierzytelniania użytkowników końcowych przy użyciu Azure Data Lake Storage Gen1. Aby uzyskać uwierzytelnianie między usługami i Data Lake Storage Gen1 przy użyciu zestawu .NET SDK, zobacz Uwierzytelnianie między usługami i usługą [Data Lake Storage Gen1 przy użyciu zestawu .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="prerequisites"></a>Wymagania wstępne
* **Visual Studio 2013 lub wyższy**. W poniższych instrukcjach użyto programu Visual Studio 2019.

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Utwórz Azure Active Directory aplikację "native"**. Należy wykonać czynności opisane w temacie [uwierzytelnianie użytkowników końcowych z Data Lake Storage Gen1 przy użyciu Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="create-a-net-application"></a>Tworzenie aplikacji .NET
1. W programie Visual Studio wybierz menu **plik** , **Nowy** i **projekt**.
2. Wybierz pozycję **aplikacja konsoli (.NET Framework)**, a następnie wybierz przycisk **dalej**.
3. W polu **Nazwa projektu** wprowadź, `CreateADLApplication` a następnie wybierz pozycję **Utwórz**.

4. Dodaj pakiety NuGet do swojego projektu.

   1. Kliknij prawym przyciskiem myszy nazwę projektu w Eksploratorze rozwiązań i kliknij polecenie **Zarządzaj pakietami NuGet**.
   2. Na karcie **Menedżer pakietów NuGet** upewnij się, że **Źródło pakietów** jest ustawione na **NuGet.org** i że zaznaczone jest pole wyboru **Uwzględnij wersję wstępną** .
   3. Wyszukaj i zainstaluj następujące pakiety NuGet:

      * `Microsoft.Azure.Management.DataLake.Store` — w tym samouczku jest używana wersja v2.1.3-preview.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` — w tym samouczku jest używana wersja v2.2.12.

        ![Dodaj źródło narzędzia NuGet](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Utwórz nowe konto Azure Data Lake")
   4. Zamknij **Menedżera pakietów NuGet**.

5. Otwieranie **programu program. cs**
6. Zastąp instrukcje using następującymi wierszami:

    ```csharp
    using System;
    using System.IO;
    using System.Linq;
    using System.Text;
    using System.Threading;
    using System.Collections.Generic;
            
    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Store.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```     

## <a name="end-user-authentication"></a>Uwierzytelnianie użytkowników końcowych
Dodaj ten fragment kodu w aplikacji klienckiej platformy .NET. Zastąp wartości symboli zastępczych wartościami pobranymi z aplikacji natywnych usługi Azure AD (wymienionym jako warunek wstępny). Ten fragment kodu umożliwia **interakcyjne** uwierzytelnianie aplikacji przy użyciu Data Lake Storage Gen1, co oznacza, że zostanie wyświetlony monit o wprowadzenie poświadczeń platformy Azure.

Aby ułatwić korzystanie z programu, Poniższy fragment kodu używa wartości domyślnych dla identyfikatora klienta i identyfikatora URI przekierowania, które są prawidłowe dla każdej subskrypcji platformy Azure. W poniższym fragmencie kodu należy podać wartość identyfikatora dzierżawy. Identyfikator dzierżawy można pobrać przy użyciu instrukcji podanych w [polu Uzyskaj identyfikator dzierżawy](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).
    
- Zastąp funkcję Main () następującym kodem:

    ```csharp
    private static void Main(string[] args)
    {
        //User login via interactive popup
        string TENANT = "<AAD-directory-domain>";
        string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
        System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
        System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
        string MY_DOCUMENTS = System.Environment.GetFolderPath(System.Environment.SpecialFolder.MyDocuments);
        string TOKEN_CACHE_PATH = System.IO.Path.Combine(MY_DOCUMENTS, "my.tokencache");
        var tokenCache = GetTokenCache(TOKEN_CACHE_PATH);
        var armCreds = GetCreds_User_Popup(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, tokenCache);
        var adlCreds = GetCreds_User_Popup(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, tokenCache);
    }
    ```

Kilka rzeczy, które należy znać w poprzednim fragmencie kodu:

* Poprzedni fragment kodu używa funkcji pomocnika `GetTokenCache` i `GetCreds_User_Popup` . Kod dla tych funkcji pomocnika jest dostępny [w witrynie GitHub](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#gettokencache).
* Aby ułatwić ukończenie tego samouczka, wstawka używa natywnego identyfikatora klienta aplikacji, który jest domyślnie dostępny dla wszystkich subskrypcji platformy Azure. Dzięki temu można **użyć tego fragmentu w aplikacji w niezmienionej formie**.
* Jeśli jednak chcesz użyć własnej domeny usługi Azure AD i własnego identyfikatora klienta aplikacji, musisz utworzyć natywną aplikację usługi Azure AD, a następnie użyć identyfikatora dzierżawy usługi Azure AD, identyfikatora klienta i identyfikatora URI przekierowania utworzonej aplikacji. Aby uzyskać instrukcje [, zobacz Tworzenie aplikacji Active Directory na potrzeby uwierzytelniania użytkowników końcowych z Data Lake Storage Gen1](data-lake-store-end-user-authenticate-using-active-directory.md) .

  
## <a name="next-steps"></a>Następne kroki
W tym artykule pokazano, jak za pomocą uwierzytelniania użytkownika końcowego uwierzytelniać się za pomocą usługi Azure Data Lake Storage Gen1 przy użyciu zestawu .NET SDK. Teraz możesz zapoznać się z następującymi artykułami dotyczącymi używania zestawu .NET SDK do pracy z Azure Data Lake Storage Gen1.

* [Operacje zarządzania kontem na Data Lake Storage Gen1 przy użyciu zestawu .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Operacje na danych na Data Lake Storage Gen1 przy użyciu zestawu .NET SDK](data-lake-store-data-operations-net-sdk.md)

