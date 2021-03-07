---
title: Zarządzanie kontem Azure Data Lake Storage Gen1 przy użyciu platformy .NET
description: Dowiedz się, jak używać zestawu SDK .NET dla Azure Data Lake Storage Gen1 operacji zarządzania kontami.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: devx-track-csharp
ms.openlocfilehash: 2c4ed092604e9511023a7025a69dc7a78652ea56
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102430483"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-net-sdk"></a>Operacje zarządzania kontem na Azure Data Lake Storage Gen1 przy użyciu zestawu .NET SDK
> [!div class="op_single_selector"]
> * [Zestaw SDK platformy .NET](data-lake-store-get-started-net-sdk.md)
> * [Interfejs API REST](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

W tym artykule dowiesz się, jak wykonywać operacje zarządzania kontami na Azure Data Lake Storage Gen1 przy użyciu zestawu .NET SDK. Operacje zarządzania kontem obejmują tworzenie konta Data Lake Storage Gen1, wyświetlanie listy kont w subskrypcji platformy Azure, usuwanie kont itp.

Aby uzyskać instrukcje dotyczące wykonywania operacji zarządzania danymi na Data Lake Storage Gen1 przy użyciu zestawu .NET SDK, zobacz [operacje systemu plików na Data Lake Storage Gen1 przy użyciu zestawu .NET SDK](data-lake-store-data-operations-net-sdk.md).

## <a name="prerequisites"></a>Wymagania wstępne
* **Visual Studio 2013 lub wyższy**. W poniższych instrukcjach użyto programu Visual Studio 2019.

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

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

6. Zadeklaruj zmienne i podaj wartości dla symboli zastępczych. Upewnij się również, że podana ścieżka lokalna i nazwa pliku istnieją na komputerze.

    ```csharp
    namespace SdkSample
    {
        class Program
        {
            private static DataLakeStoreAccountManagementClient _adlsClient;
                
            private static string _adlsAccountName;
            private static string _resourceGroupName;
            private static string _location;
            private static string _subId;

            private static void Main(string[] args)
            {
                _adlsAccountName = "<DATA-LAKE-STORAGE-GEN1-NAME>.azuredatalakestore.net"; 
                _resourceGroupName = "<RESOURCE-GROUP-NAME>"; 
                _location = "East US 2";
                _subId = "<SUBSCRIPTION-ID>";                    
            }
        }
    }
    ```

Z pozostałych akapitów tego artykułu możesz dowiedzieć się, w jaki sposób używać dostępnych metod .NET w celu wykonywania operacji, takich jak uwierzytelnianie, przekazywanie plików itp.

## <a name="authentication"></a>Authentication

* W przypadku uwierzytelniania użytkowników końcowych w aplikacji zobacz [uwierzytelnianie użytkowników końcowych za pomocą Data Lake Storage Gen1 przy użyciu zestawu .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).
* Aby przeprowadzić uwierzytelnianie między usługami dla aplikacji, zobacz Uwierzytelnianie między usługami i [Data Lake Storage Gen1 przy użyciu zestawu .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="create-client-object"></a>Tworzenie obiektu klienta
Poniższy fragment kodu tworzy obiekt klienta konta Data Lake Storage Gen1, który jest używany do wystawiania żądań zarządzania kontem do usługi, takich jak tworzenie konta, usuwanie konta itp.

```csharp
// Create client objects and set the subscription ID
_adlsClient = new DataLakeStoreAccountManagementClient(armCreds) { SubscriptionId = _subId };
```
    
## <a name="create-a-data-lake-storage-gen1-account"></a>Tworzenie konta usługi Data Lake Storage Gen1
Poniższy fragment kodu tworzy konto Data Lake Storage Gen1 w ramach subskrypcji platformy Azure podanej podczas tworzenia obiektu klienta konta Data Lake Storage Gen1.

```csharp
// Create Data Lake Storage Gen1 account
var adlsParameters = new DataLakeStoreAccount(location: _location);
_adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);
```

## <a name="list-all-data-lake-storage-gen1-accounts-within-a-subscription"></a>Wyświetlanie listy wszystkich kont Data Lake Storage Gen1 w ramach subskrypcji
Dodaj następującą metodę do swojej definicji klasy. Poniższy fragment kodu zawiera listę wszystkich kont Data Lake Storage Gen1 w ramach danej subskrypcji platformy Azure.

```csharp
// List all Data Lake Storage Gen1 accounts within the subscription
public static List<DataLakeStoreAccountBasic> ListAdlStoreAccounts()
{
    var response = _adlsClient.Account.List(_adlsAccountName);
    var accounts = new List<DataLakeStoreAccountBasic>(response);

    while (response.NextPageLink != null)
    {
        response = _adlsClient.Account.ListNext(response.NextPageLink);
        accounts.AddRange(response);
    }

    return accounts;
}
```

## <a name="delete-a-data-lake-storage-gen1-account"></a>Usuwanie konta Data Lake Storage Gen1
Poniższy fragment kodu usuwa utworzone wcześniej konto Data Lake Storage Gen1.

```csharp
// Delete Data Lake Storage Gen1 account
_adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);
```

## <a name="see-also"></a>Zobacz też
* [Operacje systemu plików na Data Lake Storage Gen1 przy użyciu zestawu .NET SDK](data-lake-store-data-operations-net-sdk.md)
* [Dokumentacja zestawu SDK platformy .NET Data Lake Storage Gen1](/dotnet/api/overview/azure/data-lake-store)

## <a name="next-steps"></a>Następne kroki
* [Zabezpieczanie danych w usłudze Data Lake Storage 1. generacji](data-lake-store-secure-data.md)