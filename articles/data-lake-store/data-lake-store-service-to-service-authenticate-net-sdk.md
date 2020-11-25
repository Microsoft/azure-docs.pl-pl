---
title: Uwierzytelnianie między usługami — Data Lake Storage Gen1
description: Dowiedz się, jak uzyskać uwierzytelnianie między usługami Azure Data Lake Storage Gen1 przy użyciu Azure Active Directory przy użyciu zestawu .NET SDK
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 19b4ac619ec3e72c787efc8e9f043f42dbd8b09b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010303"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>Uwierzytelnianie między usługami Azure Data Lake Storage Gen1 przy użyciu zestawu .NET SDK
> [!div class="op_single_selector"]
> * [Korzystanie z języka Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Korzystanie z zestawu SDK dla platformy .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Korzystanie z języka Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Korzystanie z interfejsu API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>

Ten artykuł zawiera informacje na temat korzystania z zestawu SDK platformy .NET do uwierzytelniania między usługami przy użyciu Azure Data Lake Storage Gen1. W przypadku uwierzytelniania użytkownika końcowego za pomocą Data Lake Storage Gen1 przy użyciu zestawu .NET SDK zobacz [uwierzytelnianie użytkowników końcowych za pomocą Data Lake Storage Gen1 przy użyciu zestawu .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).

## <a name="prerequisites"></a>Wymagania wstępne
* **Visual Studio 2013 lub wyższy**. W poniższych instrukcjach użyto programu Visual Studio 2019.

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Utwórz Azure Active Directory aplikacji "Web"**. Należy wykonać kroki opisane w temacie [uwierzytelnianie między usługami Data Lake Storage Gen1 przy użyciu Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

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

5. Otwórz plik **Program.cs**, usuń istniejący kod, a następnie dołącz poniższe instrukcje, aby dodać odwołania do przestrzeni nazw.

```csharp
using System;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading;
using System.Collections.Generic;
using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates

using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.DataLake.Store;
using Microsoft.Azure.Management.DataLake.Store.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

## <a name="service-to-service-authentication-with-client-secret"></a>Uwierzytelnianie między usługami z kluczem tajnym klienta
Dodaj ten fragment kodu w aplikacji klienckiej platformy .NET. Zastąp wartości symboli zastępczych wartościami pobranymi z aplikacji sieci Web usługi Azure AD (wymienionym jako warunek wstępny). Ten fragment kodu umożliwia uwierzytelnianie aplikacji w sposób **nieinteraktywny** za pomocą Data Lake Storage Gen1 przy użyciu klucza tajnego klienta/klucz dla aplikacji sieci Web usługi Azure AD.

```csharp
private static void Main(string[] args)
{
    // Service principal / application authentication with client secret / key
    // Use the client ID of an existing AAD "Web App" application.
    string TENANT = "<AAD-directory-domain>";
    string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
    System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
    System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
    string secret_key = "<AAD_WEB_APP_SECRET_KEY>";
    var armCreds = GetCreds_SPI_SecretKey(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, secret_key);
    var adlCreds = GetCreds_SPI_SecretKey(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, secret_key);
}
```

Poprzedni fragment kodu używa funkcji pomocnika `GetCreds_SPI_SecretKey` . Kod dla tej funkcji pomocnika jest dostępny [w witrynie GitHub](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_secretkey).

## <a name="service-to-service-authentication-with-certificate"></a>Uwierzytelnianie między usługami i certyfikatem

Dodaj ten fragment kodu w aplikacji klienckiej platformy .NET. Zastąp wartości symboli zastępczych wartościami pobranymi z aplikacji sieci Web usługi Azure AD (wymienionym jako warunek wstępny). Ten fragment kodu umożliwia uwierzytelnianie aplikacji w sposób **nieinterakcyjny** za pomocą Data Lake Storage Gen1 przy użyciu certyfikatu dla aplikacji sieci Web usługi Azure AD. Aby uzyskać instrukcje dotyczące sposobu tworzenia aplikacji usługi Azure AD, zobacz [Tworzenie jednostki usługi z certyfikatami](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

```csharp
private static void Main(string[] args)
{
    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    string TENANT = "<AAD-directory-domain>";
    string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
    System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
    System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
    var cert = new X509Certificate2(@"d:\cert.pfx", "<certpassword>");
    var armCreds = GetCreds_SPI_Cert(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, cert);
    var adlCreds = GetCreds_SPI_Cert(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, cert);
}
```

Poprzedni fragment kodu używa funkcji pomocnika `GetCreds_SPI_Cert` . Kod dla tej funkcji pomocnika jest dostępny [w witrynie GitHub](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_cert).

## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono sposób użycia uwierzytelniania między usługami w celu uwierzytelniania za pomocą usługi Data Lake Storage Gen1 przy użyciu zestawu .NET SDK. Teraz możesz zapoznać się z następującymi artykułami dotyczącymi używania zestawu .NET SDK do pracy z Data Lake Storage Gen1.

* [Operacje zarządzania kontem na Data Lake Storage Gen1 przy użyciu zestawu .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Operacje na danych na Data Lake Storage Gen1 przy użyciu zestawu .NET SDK](data-lake-store-data-operations-net-sdk.md)
