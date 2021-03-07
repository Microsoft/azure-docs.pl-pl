---
title: Wprowadzenie do Eksplorator usługi Storage | Microsoft Docs
description: Rozpocznij zarządzanie zasobami usługi Azure Storage za pomocą Eksplorator usługi Storage. Pobierz i zainstaluj Eksplorator usługi Azure Storage, Połącz się z kontem magazynu lub usługą i nie tylko.
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 11/08/2019
ms.author: cawa
ms.openlocfilehash: 3a8fe3ded6608059cc6ad50901ffe6df5dcf1b08
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102441592"
---
# <a name="get-started-with-storage-explorer"></a>Wprowadzenie do Eksploratora usługi Storage

## <a name="overview"></a>Omówienie

Eksplorator usługi Microsoft Azure Storage jest autonomiczną aplikacją, która ułatwia obsługę danych w usłudze Azure Storage w systemach Windows, macOS i Linux.

W tym artykule przedstawiono kilka sposobów łączenia się z kontami usługi Azure Storage i zarządzania nimi.

:::image type="content" alt-text="Microsoft Azure Storage Explorer" source="./vs-storage-explorer-overview.png":::

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="windows"></a>[Windows](#tab/windows)

Następujące wersje Eksplorator usługi Storage pomocy technicznej systemu Windows:

* Windows 10 (zalecane)
* Windows 8
* Windows 7

W przypadku wszystkich wersji systemu Windows Eksplorator usługi Storage wymaga co najmniej .NET Framework 4.7.2.

# <a name="macos"></a>[macOS](#tab/macos)

Następujące wersje macOS obsługują Eksplorator usługi Storage:

* macOS 10,12 Sierra i nowsze wersje

# <a name="linux"></a>[Linux](#tab/linux)

Eksplorator usługi Storage jest dostępny w [magazynie Snap](https://snapcraft.io/storage-explorer) dla najbardziej typowych dystrybucji systemu Linux. Zalecamy przeprowadzenie przyciągania do tej instalacji. Przystawka Eksplorator usługi Storage instaluje wszystkie jej zależności i aktualizuje, gdy nowe wersje są publikowane w magazynie Snap.

Obsługiwane dystrybucje znajdują się na [ `snapd` stronie instalacji](https://snapcraft.io/docs/installing-snapd).

Eksplorator usługi Storage wymaga użycia Menedżera haseł. Może być konieczne ręczne nawiązanie połączenia z menedżerem haseł. Możesz połączyć Eksplorator usługi Storage z menedżerem haseł systemu, uruchamiając następujące polecenie:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Eksplorator usługi Storage jest również dostępny jako pobieranie *tar. gz* . W przypadku używania *. tar. gz* należy ręcznie zainstalować zależności. Następujące dystrybucje pomocy technicznej systemu Linux — instalacja *tar. gz* :

* Ubuntu 20,04 x64
* Ubuntu 18,04 x64
* Ubuntu 16,04 x64

Instalacja *. tar. gz* może współpracować z innymi dystrybucjami, ale tylko te wymienione na liście są oficjalnie obsługiwane.

Aby uzyskać więcej informacji na temat instalowania Eksplorator usługi Storage w systemie Linux, zobacz [zależności systemu Linux](./storage/common/storage-explorer-troubleshooting.md#linux-dependencies) w przewodniku rozwiązywania problemów Eksplorator usługi Azure Storage.

---

## <a name="download-and-install"></a>Pobieranie i instalowanie

Aby pobrać i zainstalować Eksplorator usługi Storage, zobacz [Eksplorator usługi Azure Storage](https://www.storageexplorer.com).

## <a name="connect-to-a-storage-account-or-service"></a>Łączenie się z usługą lub kontem magazynu

Eksplorator usługi Storage oferuje kilka sposobów nawiązywania połączenia z zasobami platformy Azure:

* [Zaloguj się do platformy Azure, aby uzyskać dostęp do subskrypcji i ich zasobów](#sign-in-to-azure)
* [Dołącz do indywidualnego zasobu usługi Azure Storage](#attach-to-an-individual-resource)
* [Dołącz do zasobu CosmosDB](#connect-to-azure-cosmos-db)

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

> [!NOTE]
> Aby w pełni uzyskać dostęp do zasobów po zalogowaniu, Eksplorator usługi Storage wymaga zarówno uprawnień do zarządzania (Azure Resource Manager), jak i warstwy danych. Oznacza to, że potrzebujesz uprawnień Azure Active Directory (Azure AD), aby uzyskać dostęp do konta magazynu, kontenerów na koncie i danych w kontenerach. Jeśli masz uprawnienia tylko w warstwie danych, rozważ wybranie opcji **Zaloguj się przy użyciu Azure Active Directory (Azure AD)** podczas dołączania do zasobu. Aby uzyskać więcej informacji na temat Eksplorator usługi Storage wymaganych uprawnień, zobacz [Eksplorator usługi Azure Storage Przewodnik rozwiązywania problemów](./storage/common/storage-explorer-troubleshooting.md#azure-rbac-permissions-issues).

1. W Eksplorator usługi Storage wybierz pozycję **Wyświetl**  >  **Zarządzanie kontami** lub wybierz przycisk **Zarządzaj kontami** .

    :::image type="content" alt-text="Zarządzanie kontami" source ="./vs-storage-explorer-manage-accounts.png":::

1. W obszarze **Zarządzanie kontami** zostaną wyświetlone wszystkie konta platformy Azure, do których się zalogowano. Aby połączyć się z innym kontem, wybierz pozycję **Dodaj konto.**...

1. Zostanie otwarte okno dialogowe **łączenie z usługą Azure Storage** . W panelu **Wybierz zasób** wybierz pozycję **subskrypcja**.

    :::image type="content" alt-text="Okno dialogowe łączenia" source="./vs-storage-explorer-connect-dialog.png":::

1. W panelu **środowisko platformy Azure** Wybierz środowisko platformy Azure, do którego chcesz się zalogować. Możesz zalogować się do globalnej platformy Azure, chmury krajowej lub wystąpienia Azure Stack. Następnie wybierz przycisk **Dalej**.

    :::image type="content" alt-text="Opcja logowania" source="./vs-storage-explorer-connect-environment.png":::

    > [!TIP]
    > Aby uzyskać więcej informacji na temat Azure Stack, zobacz [łączenie Eksplorator usługi Storage z subskrypcją Azure Stack lub kontem magazynu](/azure-stack/user/azure-stack-storage-connect-se).

1. Eksplorator usługi Storage otworzy stronę sieci Web, aby się zalogować.

1. Po pomyślnym zalogowaniu się przy użyciu konta platformy Azure konto i subskrypcje platformy Azure skojarzone z tym kontem będą wyświetlane w obszarze **Zarządzanie kontem**. Wybierz subskrypcje platformy Azure, z którymi chcesz pracować, a następnie wybierz przycisk **Zastosuj**.

    :::image type="content" alt-text="Wybieranie subskrypcji platformy Azure" source="./vs-storage-explorer-account-panel.png":::

1. **Eksplorator** wyświetla konta magazynu skojarzone z wybranymi subskrypcjami platformy Azure.

    :::image type="content" alt-text="Wybrane subskrypcje platformy Azure" source="./vs-storage-explorer-subscription-node.png":::

### <a name="attach-to-an-individual-resource"></a>Dołącz do pojedynczego zasobu

Eksplorator usługi Storage umożliwia łączenie się z poszczególnymi zasobami, takimi jak kontener Azure Data Lake Storage Gen2, przy użyciu różnych metod uwierzytelniania. Niektóre metody uwierzytelniania są obsługiwane tylko dla określonych typów zasobów.

| Typ zasobu    | Azure AD | Nazwa i klucz konta | Sygnatura dostępu współdzielonego  | Publiczne (anonimowe) |
|------------------|----------|----------------------|--------------------------------|--------------------|
| Konta magazynu | Tak      | Tak                  | Tak (parametry połączenia lub adres URL) | Nie                 |
| Kontenery obiektów blob  | Tak      | Nie                   | Tak (adres URL)                      | Tak                |
| Kontenery Gen2  | Tak      | Nie                   | Tak (adres URL)                      | Tak                |
| Katalogi Gen2 | Tak      | Nie                   | Tak (adres URL)                      | Tak                |
| Udziały plików      | Nie       | Nie                   | Tak (adres URL)                      | Nie                 |
| Kolejki           | Tak      | Nie                   | Tak (adres URL)                      | Nie                 |
| Tabele           | Nie       | Nie                   | Tak (adres URL)                      | Nie                 |
 
Eksplorator usługi Storage może również nawiązać połączenie z [lokalnym emulatorem magazynu](#local-storage-emulator) przy użyciu skonfigurowanych portów emulatora.

Aby nawiązać połączenie z pojedynczym zasobem, wybierz przycisk **Połącz** na pasku narzędzi po lewej stronie. Następnie postępuj zgodnie z instrukcjami dotyczącymi typu zasobu, z którym chcesz nawiązać połączenie.

:::image type="content" alt-text="Opcja Połącz z usługą Azure Storage" source="./vs-storage-explorer-connect-button.png":::

Po pomyślnym dodaniu połączenia z kontem magazynu w obszarze **lokalny & dołączone**  >  **konta magazynu** zostanie wyświetlony nowy węzeł drzewa.

W przypadku innych typów zasobów dodawany jest nowy węzeł w obszarze **lokalne & dołączone**  >  **konta magazynu**  >  **(dołączone kontenery)**. Węzeł zostanie wyświetlony w węźle grupy pasującym do jego typu. Na przykład nowe połączenie z kontenerem Azure Data Lake Storage Gen2 pojawi się w obszarze **kontenery obiektów BLOB**.

Jeśli Eksplorator usługi Storage nie może dodać połączenia lub jeśli nie możesz uzyskać dostępu do danych po pomyślnym dodaniu połączenia, zobacz [Eksplorator usługi Azure Storage Przewodnik rozwiązywania problemów](./storage/common/storage-explorer-troubleshooting.md).

W poniższych sekcjach opisano różne metody uwierzytelniania, których można użyć do nawiązania połączenia z poszczególnymi zasobami.

#### <a name="azure-ad"></a>Azure AD

Eksplorator usługi Storage można używać konta platformy Azure do łączenia się z następującymi typami zasobów:
* Kontenery obiektów blob
* Kontenery Azure Data Lake Storage Gen2
* Katalogi Azure Data Lake Storage Gen2
* Kolejki
 
Usługa Azure AD jest preferowaną opcją, jeśli masz dostęp do warstwy danych do zasobu, ale nie masz dostępu do warstwy zarządzania.

1. Zaloguj się do co najmniej jednego konta platformy Azure, wykonując [kroki opisane powyżej](#sign-in-to-azure).
1. W panelu **Wybieranie zasobów** okna dialogowego **łączenie z usługą Azure Storage** wybierz pozycję **kontener obiektów BLOB**, **kontener ADLS Gen2** lub **Kolejka**.
1. Wybierz pozycję **Zaloguj się przy użyciu Azure Active Directory (Azure AD)** i wybierz pozycję **dalej**.
1. Wybierz konto platformy Azure i dzierżawcę. Konto i dzierżawca muszą mieć dostęp do zasobu magazynu, do którego chcesz dołączyć. Wybierz opcję **Dalej**.
1. Wprowadź nazwę wyświetlaną dla połączenia i adres URL zasobu. Wybierz opcję **Dalej**.
1. Przejrzyj informacje o połączeniu w panelu **Podsumowanie** . Jeśli informacje o połączeniu są poprawne, wybierz pozycję **Połącz**.

#### <a name="account-name-and-key"></a>Nazwa i klucz konta

Eksplorator usługi Storage może nawiązać połączenie z kontem magazynu przy użyciu nazwy i klucza konta magazynu.

Klucze kont można znaleźć w [Azure Portal](https://portal.azure.com). Otwórz stronę konta magazynu i wybierz pozycję **Ustawienia**  >  **klucze dostępu**.

1. W panelu **Wybieranie zasobów** okna dialogowego **łączenie z usługą Azure Storage** wybierz pozycję **konto magazynu**.
1. Wybierz pozycję **nazwa konta i klucz** , a następnie wybierz pozycję **dalej**.
1. Wprowadź nazwę wyświetlaną dla połączenia, nazwę konta i jeden z kluczy konta. Wybierz odpowiednie środowisko platformy Azure. Wybierz opcję **Dalej**.
1. Przejrzyj informacje o połączeniu w panelu **Podsumowanie** . Jeśli informacje o połączeniu są poprawne, wybierz pozycję **Połącz**.

#### <a name="shared-access-signature-sas-connection-string"></a>Parametry połączenia sygnatury dostępu współdzielonego (SAS)

Eksplorator usługi Storage może nawiązać połączenie z kontem magazynu przy użyciu parametrów połączenia z sygnaturą dostępu współdzielonego (SAS). Parametry połączenia sygnatury dostępu współdzielonego wyglądają następująco:

```text
SharedAccessSignature=sv=2020-04-08&ss=btqf&srt=sco&st=2021-03-02T00%3A22%3A19Z&se=2020-03-03T00%3A22%3A19Z&sp=rl&sig=fFFpX%2F5tzqmmFFaL0wRffHlhfFFLn6zJuylT6yhOo%2FY%3F;
BlobEndpoint=https://contoso.blob.core.windows.net/;
FileEndpoint=https://contoso.file.core.windows.net/;
QueueEndpoint=https://contoso.queue.core.windows.net/;
TableEndpoint=https://contoso.table.core.windows.net/;
```

1. W panelu **Wybieranie zasobów** okna dialogowego **łączenie z usługą Azure Storage** wybierz pozycję **konto magazynu**.
1. Wybierz pozycję **sygnatura dostępu współdzielonego (SAS)** i wybierz przycisk **dalej**.
1. Wprowadź nazwę wyświetlaną dla połączenia oraz parametry połączenia sygnatury dostępu współdzielonego dla konta magazynu. Wybierz opcję **Dalej**.
1. Przejrzyj informacje o połączeniu w panelu **Podsumowanie** . Jeśli informacje o połączeniu są poprawne, wybierz pozycję **Połącz**.

#### <a name="shared-access-signature-sas-url"></a>Adres URL sygnatury dostępu współdzielonego (SAS)

Eksplorator usługi Storage mogą łączyć się z następującymi typami zasobów przy użyciu identyfikatora URI sygnatury dostępu współdzielonego:
* Kontener obiektów blob
* Kontener Azure Data Lake Storage Gen2 lub katalog
* Udział plików
* Kolejka
* Tabela

Identyfikator URI sygnatury dostępu współdzielonego wygląda następująco:

```text
https://contoso.blob.core.windows.net/container01?sv=2020-04-08&st=2021-03-02T00%3A30%3A33Z&se=2020-03-03T00%3A30%3A33Z&sr=c&sp=rl&sig=z9VFdWffrV6FXU51T8b8HVfipZPOpYOFLXuQw6wfkFY%3F
```

1. W panelu **Wybieranie zasobów** okna dialogowego **łączenie z usługą Azure Storage** wybierz zasób, z którym chcesz nawiązać połączenie.
1. Wybierz pozycję **sygnatura dostępu współdzielonego (SAS)** i wybierz przycisk **dalej**.
1. Wprowadź nazwę wyświetlaną dla połączenia oraz identyfikator URI sygnatury dostępu współdzielonego dla zasobu. Wybierz opcję **Dalej**.
1. Przejrzyj informacje o połączeniu w panelu **Podsumowanie** . Jeśli informacje o połączeniu są poprawne, wybierz pozycję **Połącz**.

#### <a name="local-storage-emulator"></a>Emulator magazynu lokalnego

Eksplorator usługi Storage może nawiązać połączenie z emulatorem usługi Azure Storage. Obecnie istnieją dwa obsługiwane emulatory:

* [Emulator usługi Azure Storage](storage/common/storage-use-emulator.md) (tylko system Windows)
* [Azurite](https://github.com/azure/azurite) (Windows, MacOS lub Linux)

Jeśli emulator nasłuchuje na domyślnych portach, można użyć **lokalnego & dołączonego** do  >    >  **emulatora** kont magazynu, aby uzyskać dostęp do emulatora.

Jeśli chcesz użyć innej nazwy dla połączenia lub jeśli emulator nie jest uruchomiony na portach domyślnych:

1. Uruchom emulator.

   > [!IMPORTANT]
   > Eksplorator usługi Storage nie uruchamia automatycznie emulatora. Należy ją uruchomić ręcznie.

1. W panelu **Wybieranie zasobów** okna dialogowego **łączenie z usługą Azure Storage** wybierz pozycję **lokalny emulator magazynu**.
1. Wprowadź nazwę wyświetlaną dla połączenia i numer portu dla każdej emulowanej usługi, która ma być używana. Jeśli nie chcesz używać usługi, pozostaw odpowiedni port pusty. Wybierz opcję **Dalej**.
1. Przejrzyj informacje o połączeniu w panelu **Podsumowanie** . Jeśli informacje o połączeniu są poprawne, wybierz pozycję **Połącz**.

### <a name="connect-to-azure-cosmos-db"></a>Łączenie z usługą Azure Cosmos DB

Eksplorator usługi Storage obsługuje również łączenie z zasobami Azure Cosmos DB.

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Nawiązywanie połączenia z kontem Azure Cosmos DB przy użyciu parametrów połączenia

Zamiast zarządzać kontami Azure Cosmos DB za pośrednictwem subskrypcji platformy Azure, możesz połączyć się z Azure Cosmos DB przy użyciu parametrów połączenia. Aby nawiązać połączenie, wykonaj następujące kroki:

1. W obszarze **Eksplorator** rozwiń pozycję **lokalna & dołączone**, kliknij prawym przyciskiem myszy pozycję **konta Cosmos DB** i wybierz polecenie **Połącz z Azure Cosmos DB**.

    ![Łączenie się z usługą Azure Cosmos DB przy użyciu parametrów połączenia][21]

1. Wybierz interfejs API Azure Cosmos DB, wprowadź dane **parametrów połączenia** , a następnie wybierz przycisk **OK** , aby połączyć konto Azure Cosmos DB. Aby uzyskać informacje na temat pobierania parametrów połączenia, zobacz [Zarządzanie kontem usługi Azure Cosmos](./cosmos-db/how-to-manage-database-account.md).

    ![Parametry połączenia][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Połącz z Azure Data Lake Store według identyfikatora URI

Możesz uzyskać dostęp do zasobu, który nie znajduje się w Twojej subskrypcji. Potrzebujesz komuś, kto ma dostęp do tego zasobu, aby uzyskać identyfikator URI zasobu. Po zalogowaniu się Połącz się z Data Lake Store przy użyciu identyfikatora URI. Aby nawiązać połączenie, wykonaj następujące kroki:

1. W obszarze **Eksplorator** rozwiń pozycję **lokalna & dołączona**.

1. Kliknij prawym przyciskiem myszy **Data Lake Storage Gen1** i wybierz pozycję **Połącz z Data Lake Storage Gen1**.

    ![Menu kontekstowe łączenia z Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-connect-data-lake-storage.png)

1. Wprowadź identyfikator URI, a następnie wybierz przycisk **OK**. Data Lake Store zostanie wyświetlona w obszarze **Data Lake Storage**.

    ![Łączenie z wynikami Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-attach-data-lake-finished.png)

Ten przykład używa Data Lake Storage Gen1. Azure Data Lake Storage Gen2 jest teraz dostępna. Aby uzyskać więcej informacji, zobacz [co to jest Azure Data Lake Storage Gen1](./data-lake-store/data-lake-store-overview.md).

## <a name="generate-a-shared-access-signature-in-storage-explorer"></a>Generowanie sygnatury dostępu współdzielonego w Eksplorator usługi Storage<a name="generate-a-sas-in-storage-explorer"></a>

### <a name="account-level-shared-access-signature"></a>Sygnatura dostępu współdzielonego na poziomie konta

1. Kliknij prawym przyciskiem myszy konto magazynu, które chcesz udostępnić, a następnie wybierz pozycję **Pobierz sygnaturę dostępu współdzielonego**.

    ![Pobierz opcję menu kontekstowego sygnatury dostępu współdzielonego][14]

1. W obszarze **sygnatura dostępu współdzielonego** Określ przedział czasu i uprawnienia dla konta, a następnie wybierz pozycję **Utwórz**.

    ![Uzyskaj sygnaturę dostępu współdzielonego][15]

1. Skopiuj **ciąg połączenia** lub nieprzetworzony **ciąg zapytania** do Schowka.

### <a name="service-level-shared-access-signature"></a>Sygnatura dostępu współdzielonego na poziomie usługi

Możesz uzyskać sygnaturę dostępu współdzielonego na poziomie usługi. Aby uzyskać więcej informacji, zobacz [Uzyskiwanie sygnatury dostępu współdzielonego dla kontenera obiektów BLOB](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container).

## <a name="search-for-storage-accounts"></a>Wyszukiwanie kont magazynu

Aby znaleźć zasób magazynu, można wyszukać w okienku **Eksploratora** .

Podczas wprowadzania tekstu w polu wyszukiwania Eksplorator usługi Storage wyświetla wszystkie zasoby, które pasują do podanej wartości wyszukiwania. Ten przykład przedstawia wyszukiwanie **punktów końcowych**:

![Wyszukiwanie kont magazynu][23]

> [!NOTE]
> Aby przyspieszyć wyszukiwanie, użyj **zarządzania kontami** , aby usunąć wszystkie subskrypcje, które nie zawierają szukanego elementu. Możesz również kliknąć prawym przyciskiem myszy węzeł i w **tym miejscu** wybrać pozycję Wyszukaj, aby rozpocząć wyszukiwanie od określonego węzła.

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie zasobami usługi Azure Blob Storage za pomocą Eksplorator usługi Storage](vs-azure-tools-storage-explorer-blobs.md)
* [Praca z danymi za pomocą Eksploratora usługi Azure Storage](./cosmos-db/storage-explorer.md)
* [Zarządzanie zasobami Azure Data Lake Store przy użyciu Eksplorator usługi Storage](./data-lake-store/data-lake-store-in-storage-explorer.md)

[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-shared-access-signature-for-storage-explorer.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/create-shared-access-signature-for-storage-explorer.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-cosmos-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string-for-cosmos-db.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-search-for-resource.png
