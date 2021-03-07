---
title: .NET SDK — operacje na systemie plików na Data Lake Storage Gen1 — Azure
description: Użyj zestawu SDK Azure Data Lake Storage Gen1 .NET dla systemu plików na Data Lake Storage Gen1, takich jak tworzenie folderów itp.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 01/03/2020
ms.author: twooley
ms.custom: devx-track-dotnet
ms.openlocfilehash: faa539119bbb8d73e66788b8ba472cf0db26a2e4
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102431741"
---
# <a name="filesystem-operations-on-data-lake-storage-gen1-using-the-net-sdk"></a>Operacje systemu plików na Data Lake Storage Gen1 przy użyciu zestawu .NET SDK

> [!div class="op_single_selector"]
> * [Zestaw SDK platformy .NET](data-lake-store-data-operations-net-sdk.md)
> * [Zestaw SDK Java](data-lake-store-get-started-java-sdk.md)
> * [Interfejs API REST](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
>

W tym artykule dowiesz się, jak wykonywać operacje systemu plików na Data Lake Storage Gen1 przy użyciu zestawu .NET SDK. Operacje systemu plików obejmują tworzenie folderów na koncie Data Lake Storage Gen1, przekazywanie plików, pobieranie plików itp.

Aby uzyskać instrukcje dotyczące sposobu wykonywania operacji zarządzania kontem na Data Lake Storage Gen1 przy użyciu zestawu .NET SDK, zobacz [operacje zarządzania kontami na Data Lake Storage Gen1 przy użyciu zestawu .NET SDK](data-lake-store-get-started-net-sdk.md).

## <a name="prerequisites"></a>Wymagania wstępne

* **Visual Studio 2013 lub wyższy**. Instrukcje zawarte w tym artykule korzystają z programu Visual Studio 2019.

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Konto Azure Data Lake Storage Gen1**. Aby uzyskać instrukcje dotyczące sposobu tworzenia konta, zobacz Wprowadzenie [do Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).

## <a name="create-a-net-application"></a>Tworzenie aplikacji .NET

Przykładowy kod dostępny [w usłudze GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted) przeprowadzi Cię przez proces tworzenia plików w magazynie, łączenia plików, pobierania pliku i usuwania niektórych plików z magazynu. Ta część artykułu przeprowadzi Cię przez najważniejsze fragmenty kodu.

1. W programie Visual Studio wybierz menu **plik** , **Nowy** i **projekt**.
1. Wybierz pozycję **aplikacja konsoli (.NET Framework)**, a następnie wybierz przycisk **dalej**.
1. W polu **Nazwa projektu** wprowadź, `CreateADLApplication` a następnie wybierz pozycję **Utwórz**.
1. Dodaj pakiety NuGet do swojego projektu.

   1. Kliknij prawym przyciskiem myszy nazwę projektu w Eksploratorze rozwiązań i kliknij polecenie **Zarządzaj pakietami NuGet**.
   1. Upewnij **się, że** na karcie **Menedżer pakietów NuGet** jest ustawiona wartość **NuGet.org**. Upewnij się również, że zaznaczone jest pole wyboru **Uwzględnij wersję wstępną** .
   1. Wyszukaj i zainstaluj następujące pakiety NuGet:

      * `Microsoft.Azure.DataLake.Store` — W tym artykule jest stosowane 1.0.0 v.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` — W tym artykule jest stosowany program v 2.3.1.

      Zamknij **Menedżera pakietów NuGet**.

1. Otwórz plik **Program.cs**, usuń istniejący kod, a następnie dołącz poniższe instrukcje, aby dodać odwołania do przestrzeni nazw.

    ```
    using System;
    using System.IO;using System.Threading;
    using System.Linq;
    using System.Text;
    using System.Collections.Generic;
    using System.Security.Cryptography.X509Certificates; // Required only if you're using an Azure AD application created with certificates
                
    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.DataLake.Store;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

1. Zadeklaruj zmienne w pokazany poniżej sposób i podaj wartości dla symboli zastępczych. Upewnij się również, że podana tutaj ścieżka lokalna i nazwa pliku istnieją na komputerze.

    ```
    namespace SdkSample
    {
        class Program
        {
            private static string _adlsg1AccountName = "<DATA-LAKE-STORAGE-GEN1-NAME>.azuredatalakestore.net";
        }
    }
    ```

W pozostałych sekcjach artykułu można zobaczyć, jak używać dostępnych metod .NET do wykonywania operacji, takich jak uwierzytelnianie, przekazywanie plików itp.

## <a name="authentication"></a>Authentication

* W przypadku uwierzytelniania użytkowników końcowych w aplikacji zobacz [uwierzytelnianie użytkowników końcowych za pomocą Data Lake Storage Gen1 przy użyciu zestawu .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).
* Aby przeprowadzić uwierzytelnianie między usługami dla aplikacji, zobacz Uwierzytelnianie między usługami i [Data Lake Storage Gen1 przy użyciu zestawu .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="create-client-object"></a>Tworzenie obiektu klienta

Poniższy fragment kodu tworzy obiekt klienta systemu plików Data Lake Storage Gen1, który jest używany do wystawiania żądań do usługi.

```
// Create client objects
AdlsClient client = AdlsClient.CreateClient(_adlsg1AccountName, adlCreds);
```

## <a name="create-a-file-and-directory"></a>Tworzenie plików i katalogów

Dodaj poniższy fragment kodu do aplikacji. Ten fragment kodu dodaje plik i dowolny katalog nadrzędny, który nie istnieje.

```
// Create a file - automatically creates any parent directories that don't exist
// The AdlsOutputStream preserves record boundaries - it does not break records while writing to the store

using (var stream = client.CreateFile(fileName, IfExists.Overwrite))
{
    byte[] textByteArray = Encoding.UTF8.GetBytes("This is test data to write.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);

    textByteArray = Encoding.UTF8.GetBytes("This is the second line.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);
}
```

## <a name="append-to-a-file"></a>Dołączanie do pliku

Poniższy fragment kodu dołącza dane do istniejącego pliku na koncie Data Lake Storage Gen1.

```
// Append to existing file

using (var stream = client.GetAppendStream(fileName))
{
    byte[] textByteArray = Encoding.UTF8.GetBytes("This is the added line.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);
}
```

## <a name="read-a-file"></a>Odczytywanie pliku

Poniższy fragment kodu odczytuje zawartość pliku w Data Lake Storage Gen1.

```
//Read file contents

using (var readStream = new StreamReader(client.GetReadStream(fileName)))
{
    string line;
    while ((line = readStream.ReadLine()) != null)
    {
        Console.WriteLine(line);
    }
}
```

## <a name="get-file-properties"></a>Pobieranie właściwości pliku

Poniższy fragment kodu zwraca właściwości skojarzone z plikiem lub katalogiem.

```
// Get file properties
var directoryEntry = client.GetDirectoryEntry(fileName);
PrintDirectoryEntry(directoryEntry);
```

Definicja `PrintDirectoryEntry` metody jest dostępna w ramach przykładu [w witrynie GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted).

## <a name="rename-a-file"></a>Zmienianie nazwy pliku

Poniższy fragment kodu zmienia nazwę istniejącego pliku na koncie Data Lake Storage Gen1.

```
// Rename a file
string destFilePath = "/Test/testRenameDest3.txt";
client.Rename(fileName, destFilePath, true);
```

## <a name="enumerate-a-directory"></a>Wyliczanie katalogu

Poniższy fragment kodu wylicza katalogi na koncie Data Lake Storage Gen1.

```
// Enumerate directory
foreach (var entry in client.EnumerateDirectory("/Test"))
{
    PrintDirectoryEntry(entry);
}
```

Definicja `PrintDirectoryEntry` metody jest dostępna w ramach przykładu [w witrynie GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted).

## <a name="delete-directories-recursively"></a>Rekursywne usuwanie katalogów

Poniższy fragment kodu Usuwa katalog i wszystkie jego podkatalogi cyklicznie.

```
// Delete a directory and all its subdirectories and files
client.DeleteRecursive("/Test");
```

## <a name="samples"></a>Samples

Poniżej przedstawiono kilka przykładów, które pokazują, jak korzystać z zestawu SDK systemu Data Lake Storage Gen1.

* [Podstawowy przykład w witrynie GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted)
* [Zaawansowany przykład w witrynie GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-samples)

## <a name="see-also"></a>Zobacz też

* [Operacje zarządzania kontem na Data Lake Storage Gen1 przy użyciu zestawu .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Dokumentacja zestawu SDK platformy .NET Data Lake Storage Gen1](/dotnet/api/overview/azure/data-lake-store)

## <a name="next-steps"></a>Następne kroki

* [Zabezpieczanie danych w usłudze Data Lake Storage 1. generacji](data-lake-store-secure-data.md)