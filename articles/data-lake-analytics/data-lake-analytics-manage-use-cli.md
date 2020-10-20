---
title: Zarządzanie Azure Data Lake Analytics przy użyciu interfejsu wiersza polecenia platformy Azure
description: W tym artykule opisano, jak używać interfejsu wiersza polecenia platformy Azure do zarządzania zadaniami Data Lake Analytics, źródłami danych & użytkownikami.
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 01/29/2018
ms.openlocfilehash: 19b471d85a52fe38b72ad55847d022fb56b3c4f0
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92220928"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-command-line-interface-cli"></a>Zarządzanie Azure Data Lake Analytics przy użyciu interfejsu wiersza polecenia (CLI) platformy Azure

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Dowiedz się, jak zarządzać kontami Azure Data Lake Analytics, źródłami danych, użytkownikami i zadaniami za pomocą interfejsu wiersza polecenia platformy Azure. Aby wyświetlić tematy dotyczące zarządzania przy użyciu innych narzędzi, kliknij kartę powyżej.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy z tym samouczkiem należy dysponować następującymi zasobami:

- Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

- Interfejs wiersza polecenia platformy Azure. Zobacz temat [Instalowanie i konfigurowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

  - Aby ukończyć ten przewodnik, pobierz i zainstaluj **wersję wstępną** [narzędzi interfejsu wiersza polecenia platformy Azure](https://github.com/MicrosoftBigData/AzureDataLake/releases).

- Uwierzytelnij się przy użyciu `az login` polecenia i wybierz subskrypcję, której chcesz użyć. Więcej informacji na temat uwierzytelniania za pomocą konta służbowego lub szkolnego znajdziesz w temacie [Połączenie z subskrypcją platformy Azure z poziomu interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli).

   ```azurecli
   az login
   az account set --subscription <subscription id>
   ```

   Teraz możesz uzyskać dostęp do poleceń Data Lake Analytics i Data Lake Store. Uruchom następujące polecenie, aby wyświetlić listę poleceń Data Lake Store i Data Lake Analytics:

   ```azurecli
   az dls -h
   az dla -h
   ```

## <a name="manage-accounts"></a>Zarządzanie kontami

Przed uruchomieniem jakichkolwiek Data Lake Analytics zadań musisz mieć konto Data Lake Analytics. W przeciwieństwie do usługi Azure HDInsight nie płacisz za konto analizy, gdy nie jest ono uruchomione. Płacisz tylko za czas, w którym uruchomiono zadanie.  Aby uzyskać więcej informacji, zobacz [Azure Data Lake Analytics przegląd](data-lake-analytics-overview.md).

### <a name="create-accounts"></a>Utwórz konta

Uruchom następujące polecenie, aby utworzyć konto Data Lake,

   ```azurecli
   az dla account create --account "<Data Lake Analytics account name>" --location "<Location Name>" --resource-group "<Resource Group Name>" --default-data-lake-store "<Data Lake Store account name>"
   ```

### <a name="update-accounts"></a>Aktualizowanie kont

Następujące polecenie aktualizuje właściwości istniejącego konta Data Lake Analytics

   ```azurecli
   az dla account update --account "<Data Lake Analytics Account Name>" --firewall-state "Enabled" --query-store-retention 7
   ```

### <a name="list-accounts"></a>Wyświetlanie listy kont

Wyświetlanie listy kont Data Lake Analytics w ramach określonej grupy zasobów

   ```azurecli
   az dla account list "<Resource group name>"
   ```

## <a name="get-details-of-an-account"></a>Pobierz szczegóły konta

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

### <a name="delete-an-account"></a>Usuwanie konta

   ```azurecli
   az dla account delete --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

## <a name="manage-data-sources"></a>Zarządzanie źródłami danych

Data Lake Analytics obecnie obsługuje następujące dwa źródła danych:

- [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
- [Azure Storage](../storage/common/storage-introduction.md)

Podczas tworzenia konta usługi Analytics należy wyznaczyć konto Azure Data Lake Storage jako domyślne konto magazynu. Domyślne konto magazynu Data Lake służy do przechowywania metadanych zadań i dzienników inspekcji zadań. Po utworzeniu konta usługi Analytics można dodać dodatkowe konta Data Lake Storage i/lub konto magazynu platformy Azure.

### <a name="find-the-default-data-lake-store-account"></a>Znajdź domyślne konto Data Lake Store

Można wyświetlić domyślne konto Data Lake Store używane przez uruchomienie `az dla account show` polecenia. Domyślna nazwa konta jest wyświetlana pod właściwością defaultDataLakeStoreAccount.

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>"
   ```

### <a name="add-additional-blob-storage-accounts"></a>Dodawanie dodatkowych kont magazynu obiektów BLOB

   ```azurecli
   az dla account blob-storage add --access-key "<Azure Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Storage account name>"
   ```

> [!NOTE]
> Obsługiwane są tylko krótkie nazwy magazynu obiektów BLOB. Nie używaj nazwy FQDN, na przykład "myblob.blob.core.windows.net".
>

### <a name="add-additional-data-lake-store-accounts"></a>Dodawanie dodatkowych kont Data Lake Store

Następujące polecenie aktualizuje określone konto Data Lake Analytics przy użyciu dodatkowego konta Data Lake Store:

   ```azurecli
   az dla account data-lake-store add --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Data Lake Store account name>"
   ```

### <a name="update-existing-data-source"></a>Aktualizowanie istniejącego źródła danych

Aby zaktualizować istniejący klucz konta magazynu obiektów blob:

   ```azurecli
   az dla account blob-storage update --access-key "<New Blob Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

### <a name="list-data-sources"></a>Wyświetl listę źródeł danych

Aby wyświetlić listę kont Data Lake Store:

   ```azurecli
   az dla account data-lake-store list --account "<Data Lake Analytics account name>"
   ```

Aby wyświetlić listę konta magazynu obiektów blob:

   ```azurecli
   az dla account blob-storage list --account "<Data Lake Analytics account name>"
   ```

![Zrzut ekranu przedstawiający platformę Azure C L z wyróżnionymi informacjami "dataLakeStoreAccounts:".](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-data-source.png)

### <a name="delete-data-sources"></a>Usuń źródła danych

Aby usunąć konto Data Lake Store:

   ```azurecli
   az dla account data-lake-store delete --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Azure Data Lake Store account name>"
   ```

Aby usunąć konto magazynu obiektów blob:

   ```azurecli
   az dla account blob-storage delete --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

## <a name="manage-jobs"></a>Zarządzanie zadaniami

Aby można było utworzyć zadanie, musisz mieć konto Data Lake Analytics.  Aby uzyskać więcej informacji, zobacz [Zarządzanie kontami Data Lake Analytics](#manage-accounts).

### <a name="list-jobs"></a>Wyświetl listę zadań

   ```azurecli
   az dla job list --account "<Data Lake Analytics account name>"
   ```

   ![Źródło danych listy Data Lake Analytics](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-jobs.png)

### <a name="get-job-details"></a>Pobierz szczegóły zadania

   ```azurecli
   az dla job show --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

### <a name="submit-jobs"></a>Prześlij zadania

> [!NOTE]
> Domyślnym priorytetem zadania jest 1000, a domyślny stopień równoległości dla zadania wynosi 1.
>
>    ```azurecli
>    az dla job submit --account "<Data Lake Analytics account name>" --job-name "<Name of your job>" --script "<Script to submit>"
>    ```

### <a name="cancel-jobs"></a>Anuluj zadania

Użyj polecenia list, aby znaleźć identyfikator zadania, a następnie użyj przycisku Anuluj, aby anulować zadanie.

   ```azurecli
   az dla job cancel --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

## <a name="pipelines-and-recurrences"></a>Potoki i cykle

### <a name="get-information-about-pipelines-and-recurrences"></a>Uzyskaj informacje na temat potoków i cykli

Użyj poleceń `az dla job pipeline`, aby wyświetlić informacje o potoku wcześniej przesłanych zadań.

```azurecli
az dla job pipeline list --account "<Data Lake Analytics Account Name>"

az dla job pipeline show --account "<Data Lake Analytics Account Name>" --pipeline-identity "<Pipeline ID>"
```

Użyj poleceń `az dla job recurrence`, aby wyświetlić informacje o cyklu wcześniej przesłanych zadań.

```azurecli
az dla job recurrence list --account "<Data Lake Analytics Account Name>"

az dla job recurrence show --account "<Data Lake Analytics Account Name>" --recurrence-identity "<Recurrence ID>"
```

## <a name="next-steps"></a>Następne kroki

- [Omówienie usługi Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
- [Wprowadzenie do Data Lake Analytics przy użyciu Azure Portal](data-lake-analytics-get-started-portal.md)
- [Zarządzanie Azure Data Lake Analytics przy użyciu Azure Portal](data-lake-analytics-manage-use-portal.md)
- [Monitorowanie zadań usługi Azure Data Lake Analytics i rozwiązywanie problemów przy użyciu witryny Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
