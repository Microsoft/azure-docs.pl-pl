---
title: Wprowadzenie do Azure Data Lake Storage Gen1 — PowerShell | Microsoft Docs
description: Użyj Azure PowerShell, aby utworzyć konto Azure Data Lake Storage Gen1 i wykonać podstawowe operacje.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 06/27/2018
ms.author: twooley
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 485b14f85d68290c5447c885b9bc4974318f7952
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92103734"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-azure-powershell"></a>Wprowadzenie do Azure Data Lake Storage Gen1 przy użyciu Azure PowerShell

> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [Program PowerShell](data-lake-store-get-started-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](data-lake-store-get-started-cli-2.0.md)
>
>

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Dowiedz się, jak za pomocą Azure PowerShell utworzyć konto Azure Data Lake Storage Gen1 i wykonać podstawowe operacje, takie jak tworzenie folderów, przekazywanie i pobieranie plików danych, usuwanie konta itp. Aby uzyskać więcej informacji na temat Data Lake Storage Gen1, zobacz [omówienie Data Lake Storage Gen1](data-lake-store-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Program Azure PowerShell 1.0 lub nowszy**. Zobacz artykuł [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/).

## <a name="authentication"></a>Authentication

W tym artykule jest używana prostsze podejście uwierzytelniania z Data Lake Storage Gen1, w którym jest wyświetlany monit o wprowadzenie poświadczeń konta platformy Azure. Poziom dostępu do Data Lake Storage Gen1 konta i systemu plików podlega następnie poziom dostępu zalogowanego użytkownika. Istnieją jednak inne podejścia do uwierzytelniania za pomocą Data Lake Storage Gen1, które są uwierzytelnianiem użytkowników końcowych lub uwierzytelnianiem między usługami. Instrukcje i dodatkowe informacje na temat uwierzytelniania można znaleźć w następujących artykułach: [End-user authentication](data-lake-store-end-user-authenticate-using-active-directory.md) (Uwierzytelnianie użytkowników końcowych) lub [Service-to-service authentication](./data-lake-store-service-to-service-authenticate-using-active-directory.md) (Uwierzytelnianie między usługami).

## <a name="create-a-data-lake-storage-gen1-account"></a>Tworzenie konta usługi Data Lake Storage Gen1

1. Z poziomu pulpitu otwórz nowe okno programu Windows PowerShell. Wprowadź Poniższy fragment kodu, aby zalogować się do konta platformy Azure, ustawić subskrypcję i zarejestrować dostawcę Data Lake Storage Gen1. Po wyświetleniu monitu o zalogowanie się upewnij się, że logujesz się jako jeden z administratorów subskrypcji/właściciela:

    ```PowerShell
    # Log in to your Azure account
    Connect-AzAccount

    # List all the subscriptions associated to your account
    Get-AzSubscription

    # Select a subscription
    Set-AzContext -SubscriptionId <subscription ID>

    # Register for Azure Data Lake Storage Gen1
    Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
    ```

1. Konto Data Lake Storage Gen1 jest skojarzone z grupą zasobów platformy Azure. Zacznij od utworzenia grupy zasobów.

    ```PowerShell
    $resourceGroupName = "<your new resource group name>"
    New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"
    ```

    ![Tworzenie grupy zasobów platformy Azure](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Tworzenie grupy zasobów platformy Azure")

1. Utwórz konto Data Lake Storage Gen1. Wybrana nazwa konta może zawierać tylko małe litery i cyfry.

    ```PowerShell
    $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 account name>"
    New-AzDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"
    ```

    ![Tworzenie konta usługi Data Lake Storage Gen1](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Tworzenie konta usługi Data Lake Storage Gen1")

1. Sprawdź, czy konto zostało utworzone pomyślnie.

    ```PowerShell
    Test-AzDataLakeStoreAccount -Name $dataLakeStorageGen1Name
    ```

    Dane wyjściowe polecenia cmdlet powinny mieć wartość **True**.

## <a name="create-directory-structures"></a>Utwórz struktury katalogów

Możesz tworzyć katalogi na koncie Data Lake Storage Gen1, aby zarządzać danymi i ich przechowywać.

1. Określ katalog główny.

    ```PowerShell
    $myrootdir = "/"
    ```

1. Utwórz nowy katalog o nazwie **mynewdirectory** w określonym katalogu głównym.

    ```PowerShell
    New-AzDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/mynewdirectory
    ```

1. Sprawdź, czy nowy katalog został utworzony pomyślnie.

    ```PowerShell
    Get-AzDataLakeStoreChildItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir
    ```

    Powinny być widoczne dane wyjściowe pokazane na poniższym zrzucie ekranu:

    ![Sprawdzanie katalogu](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Sprawdzanie katalogu")

## <a name="upload-data"></a>Przekazywanie danych

Dane można przekazać do Data Lake Storage Gen1 bezpośrednio na poziomie głównym lub do katalogu utworzonego w ramach konta. Fragmenty kodu w tej sekcji przedstawiają sposób przekazywania przykładowych danych do katalogu (**mynewdirectory**), który został utworzony w poprzedniej sekcji.

Jeśli szukasz przykładowych danych do przekazania, możesz pobrać folder **Ambulance Data** z [repozytorium Git usługi Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Pobierz plik i zapisz go w katalogu lokalnym na komputerze, na przykład C:\sampledata\.

```PowerShell
Import-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
   -Path "C:\sampledata\vehicle1_09142014.csv" `
   -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv
```

## <a name="rename-download-and-delete-data"></a>Zmiana nazwy, pobieranie i usuwanie danych

Aby zmienić nazwę pliku, użyj następującego polecenia:

```PowerShell
Move-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv `
    -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv
```

Aby pobrać plik, użyj następującego polecenia.

```PowerShell
Export-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv `
    -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"
```

Aby usunąć plik, użyj następującego polecenia:

```PowerShell
Remove-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv
```

Po wyświetleniu monitu wpisz **Y**, aby usunąć element. Jeśli masz więcej niż jeden plik do usunięcia, możesz podać wszystkie ścieżki oddzielone przecinkami.

```PowerShell
Remove-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv
```

## <a name="delete-your-account"></a>Usuń konto

Użyj poniższego polecenia, aby usunąć konto Data Lake Storage Gen1.

```PowerShell
Remove-AzDataLakeStoreAccount -Name $dataLakeStorageGen1Name
```

Po wyświetleniu monitu wpisz **Y**, aby usunąć konto.

## <a name="next-steps"></a>Następne kroki

* [Wskazówki dotyczące dostrajania wydajności dotyczące korzystania z programu PowerShell z Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-powershell.md)
* [Użyj Azure Data Lake Storage Gen1, aby uzyskać wymagania dotyczące danych Big Data](data-lake-store-data-scenarios.md)
* [Zabezpieczanie danych w usłudze Data Lake Storage 1. generacji](data-lake-store-secure-data.md)
* [Użyj Azure Data Lake Analytics z Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Korzystanie z usługi Azure HDInsight z usługą Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)