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
ms.openlocfilehash: be9b2d9a31d4affc9615f5d2f4b2585b7533a0f6
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95545911"
---
# <a name="get-started-with-storage-explorer"></a>Wprowadzenie do Eksploratora usługi Storage

## <a name="overview"></a>Omówienie

Eksplorator usługi Microsoft Azure Storage jest autonomiczną aplikacją, która ułatwia obsługę danych w usłudze Azure Storage w systemach Windows, macOS i Linux. W tym artykule przedstawiono kilka sposobów łączenia się z kontami usługi Azure Storage i zarządzania nimi.

![Microsoft Azure Storage Explorer][0]

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

W przypadku obsługiwanych dystrybucji zapoznaj się ze [stroną instalacja przyciągania](https://snapcraft.io/docs/installing-snapd).

Eksplorator usługi Storage wymaga użycia Menedżera haseł. Może być konieczne ręczne nawiązanie połączenia z menedżerem haseł. Możesz połączyć Eksplorator usługi Storage z menedżerem haseł systemu, uruchamiając następujące polecenie:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Eksplorator usługi Storage jest również dostępny jako pobieranie *tar. gz* . Należy ręcznie zainstalować zależności. Następujące dystrybucje pomocy technicznej systemu Linux — instalacja *tar. gz* :

* Ubuntu 20,04 x64
* Ubuntu 18,04 x64
* Ubuntu 16,04 x64

Instalacja *. tar. gz* może współpracować z innymi dystrybucjami, ale tylko te wymienione na liście są oficjalnie obsługiwane.

Aby uzyskać więcej informacji na temat instalowania Eksplorator usługi Storage w systemie Linux, zobacz [zależności systemu Linux](./storage/common/storage-explorer-troubleshooting.md#linux-dependencies) w przewodniku rozwiązywania problemów Eksplorator usługi Azure Storage.

---

## <a name="download-and-install"></a>Pobieranie i instalowanie

Aby pobrać i zainstalować Eksplorator usługi Storage, zobacz [Eksplorator usługi Azure Storage](https://www.storageexplorer.com).

## <a name="connect-to-a-storage-account-or-service"></a>Łączenie się z usługą lub kontem magazynu

Eksplorator usługi Storage oferuje kilka sposobów nawiązywania połączenia z kontami magazynu. Ogólnie można:

* [Zaloguj się do platformy Azure, aby uzyskać dostęp do subskrypcji i ich zasobów](#sign-in-to-azure)
* [Dołącz określony magazyn lub zasób CosmosDB](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>Logowanie się do platformy Azure

> [!NOTE]
> Aby w pełni uzyskać dostęp do zasobów po zalogowaniu, Eksplorator usługi Storage wymaga zarówno uprawnień do zarządzania (Azure Resource Manager), jak i warstwy danych. Oznacza to, że potrzebne są uprawnienia Azure Active Directory (Azure AD), które zapewniają dostęp do konta magazynu, kontenerów na koncie i danych w kontenerach. Jeśli masz uprawnienia tylko w warstwie danych, rozważ [dodanie zasobu za pomocą usługi Azure AD](#add-a-resource-via-azure-ad). Aby uzyskać więcej informacji na temat Eksplorator usługi Storage wymaganych uprawnień, zobacz [Eksplorator usługi Azure Storage Przewodnik rozwiązywania problemów](./storage/common/storage-explorer-troubleshooting.md#azure-rbac-permissions-issues).

1. W Eksplorator usługi Storage wybierz pozycję **Wyświetl**  >  **Zarządzanie kontami** lub wybierz przycisk **Zarządzaj kontami** .

    ![Zarządzaj kontami][1]

1. W obszarze **Zarządzanie kontami** zostaną wyświetlone wszystkie konta platformy Azure, do których się zalogowano. Aby połączyć się z innym kontem, wybierz pozycję **Dodaj konto**.

1. W obszarze **nawiązywanie połączenia z usługą Azure Storage** Wybierz chmurę platformy Azure ze **środowiska platformy Azure** , aby zalogować się do chmury krajowej lub Azure Stack. Po wybraniu środowiska wybierz pozycję **dalej**.

    ![Opcja logowania][2]

    Eksplorator usługi Storage otwiera stronę umożliwiającą zalogowanie się. Aby uzyskać więcej informacji, zobacz [łączenie Eksploratora usługi Storage z subskrypcją Azure Stack lub kontem magazynu](/azure-stack/user/azure-stack-storage-connect-se).

1. Po pomyślnym zalogowaniu się przy użyciu konta platformy Azure konto i subskrypcje platformy Azure skojarzone z tym kontem będą wyświetlane w obszarze **Zarządzanie kontem**. Wybierz pozycję **wszystkie subskrypcje** , aby przełączyć wybór między wszystkimi lub żadną z wymienionych subskrypcji platformy Azure. Wybierz subskrypcje platformy Azure, z którymi chcesz pracować, a następnie wybierz przycisk **Zastosuj**.

    ![Wybieranie subskrypcji platformy Azure][3]

    **Eksplorator** wyświetla konta magazynu skojarzone z wybranymi subskrypcjami platformy Azure.

    ![Wybrane subskrypcje platformy Azure][4]

### <a name="attach-a-specific-resource"></a>Dołączanie określonego zasobu

Istnieje kilka sposobów dołączenia do zasobu w Eksplorator usługi Storage:

* [Dodaj zasób za pośrednictwem usługi Azure AD](#add-a-resource-via-azure-ad). Jeśli masz uprawnienia tylko w warstwie danych, Użyj tej opcji, aby dodać kontener obiektów blob lub Azure Data Lake Storage Gen2 kontener magazynu obiektów BLOB.
* [Użyj parametrów połączenia](#use-a-connection-string). Użyj tej opcji, jeśli masz parametry połączenia z kontem magazynu. Eksplorator usługi Storage obsługuje zarówno parametry połączenia klucza, jak i [sygnatury dostępu współdzielonego](./storage/common/storage-sas-overview.md) .
* [Użyj identyfikatora URI sygnatury dostępu współdzielonego](#use-a-shared-access-signature-uri). Jeśli masz [Identyfikator URI sygnatury dostępu współdzielonego](./storage/common/storage-sas-overview.md) do kontenera obiektów blob, udziału plików, kolejki lub tabeli, użyj go do dołączenia do zasobu. Aby uzyskać identyfikator URI sygnatury dostępu współdzielonego, można użyć [Eksplorator usługi Storage](#generate-a-sas-in-storage-explorer) lub [Azure Portal](https://portal.azure.com).
* [Użyj nazwy i klucza](#use-a-name-and-key). Jeśli znasz jeden z kluczy konta do konta magazynu, możesz użyć tej opcji, aby szybko nawiązać połączenie. Znajdź klucze na stronie konta magazynu, wybierając pozycję **Ustawienia**  >  **klucze dostępu** w [Azure Portal](https://portal.azure.com).
* [Dołącz do lokalnego emulatora](#attach-to-a-local-emulator). Jeśli używasz jednego z dostępnych emulatorów usługi Azure Storage, Użyj tej opcji, aby łatwo połączyć się z emulatorem.
* [Połącz się z kontem Azure Cosmos dB przy użyciu parametrów połączenia](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string). Użyj tej opcji, jeśli masz parametry połączenia z wystąpieniem CosmosDB.
* [Połącz się z Azure Data Lake Store według identyfikatora URI](#connect-to-azure-data-lake-store-by-uri). Użyj tej opcji, jeśli masz identyfikator URI do Azure Data Lake Store.

#### <a name="add-a-resource-via-azure-ad"></a>Dodawanie zasobu za pośrednictwem usługi Azure AD

1. Wybierz symbol **połączenia** , aby otworzyć okno **łączenie z usługą Azure Storage**.

    ![Opcja Połącz z usługą Azure Storage][9]

1. Jeśli jeszcze tego nie zrobiono, użyj opcji **Dodaj konto platformy Azure** , aby zalogować się do konta platformy Azure, które ma dostęp do zasobu. Po zalogowaniu Wróć do **usługi Azure Storage**.

1. Wybierz pozycję **Dodaj zasób za pośrednictwem Azure Active Directory (Azure AD)**, a następnie wybierz pozycję **dalej**.

1. Wybierz konto platformy Azure i dzierżawcę. Te wartości muszą mieć dostęp do zasobu magazynu, do którego chcesz dołączyć. Wybierz pozycję **Dalej**.

1. Wybierz typ zasobu, który chcesz dołączyć. Wprowadź informacje konieczne do nawiązania połączenia. 

   Informacje wprowadzane na tej stronie zależą od typu dodawanego zasobu. Upewnij się, że wybrano poprawny typ zasobu. Po wprowadzeniu wymaganych informacji wybierz pozycję **dalej**.

1. Przejrzyj **Podsumowanie połączenia** , aby upewnić się, że wszystkie informacje są poprawne. Jeśli tak jest, wybierz pozycję **Połącz**. W przeciwnym razie wybierz pozycję **Wstecz** , aby powrócić do poprzedniej strony, aby naprawić wszelkie nieprawidłowe informacje.

Po pomyślnym dodaniu połączenia drzewo zasobów przechodzi do węzła, który reprezentuje połączenie. Zasób jest wyświetlany w obszarze **lokalne & dołączone**  >  **Storage Accounts**  >  **(Attached Containers)**  >  **kontenery obiektów BLOB**(dołączone kontenery). Jeśli Eksplorator usługi Storage nie może dodać połączenia lub jeśli nie możesz uzyskać dostępu do danych po pomyślnym dodaniu połączenia, zobacz [Eksplorator usługi Azure Storage Przewodnik rozwiązywania problemów](./storage/common/storage-explorer-troubleshooting.md).

#### <a name="use-a-connection-string"></a>Używanie parametrów połączenia

1. Wybierz symbol **połączenia** , aby otworzyć okno **łączenie z usługą Azure Storage**.

    ![Opcja Połącz z usługą Azure Storage][9]

1. Wybierz pozycję **Użyj parametrów połączenia**, a następnie wybierz przycisk **dalej**.

1. Wybierz nazwę wyświetlaną dla połączenia i wprowadź parametry połączenia. Następnie wybierz pozycję **Dalej**.

1. Przejrzyj **Podsumowanie połączenia** , aby upewnić się, że wszystkie informacje są poprawne. Jeśli tak jest, wybierz pozycję **Połącz**. W przeciwnym razie wybierz pozycję **Wstecz** , aby powrócić do poprzedniej strony, aby naprawić wszelkie nieprawidłowe informacje.

Po pomyślnym dodaniu połączenia drzewo zasobów przechodzi do węzła, który reprezentuje połączenie. Zasób jest wyświetlany w obszarze **lokalne & dołączone**  >  **konta magazynu**. Jeśli Eksplorator usługi Storage nie może dodać połączenia lub jeśli nie możesz uzyskać dostępu do danych po pomyślnym dodaniu połączenia, zobacz [Eksplorator usługi Azure Storage Przewodnik rozwiązywania problemów](./storage/common/storage-explorer-troubleshooting.md).

#### <a name="use-a-shared-access-signature-uri"></a>Używanie identyfikatora URI sygnatury dostępu współdzielonego

1. Wybierz symbol **połączenia** , aby otworzyć okno **łączenie z usługą Azure Storage**.

    ![Opcja Połącz z usługą Azure Storage][9]

1. Wybierz pozycję **Użyj identyfikatora URI sygnatury dostępu współdzielonego (SAS)**, a następnie wybierz przycisk **dalej**.

1. Wybierz nazwę wyświetlaną dla połączenia i wprowadź swój identyfikator URI sygnatury dostępu współdzielonego. Punkt końcowy usługi dla typu zasobu, który jest dołączany, powinien mieć wartość Autowypełnianie. Jeśli używasz niestandardowego punktu końcowego, możliwe, że może to nie być. Wybierz pozycję **Dalej**.

1. Przejrzyj **Podsumowanie połączenia** , aby upewnić się, że wszystkie informacje są poprawne. Jeśli tak jest, wybierz pozycję **Połącz**. W przeciwnym razie wybierz pozycję **Wstecz** , aby powrócić do poprzedniej strony, aby naprawić wszelkie nieprawidłowe informacje.

Po pomyślnym dodaniu połączenia drzewo zasobów przechodzi do węzła, który reprezentuje połączenie. Zasób jest wyświetlany w obszarze **lokalne & dołączone**  >  **konta magazynu**  >  **(dołączone kontenery)**  >  *węzeł usługi dla typu dołączonego kontenera*. Jeśli nie można dodać połączenia Eksplorator usługi Storage, zobacz [Eksplorator usługi Azure Storage Przewodnik rozwiązywania problemów](./storage/common/storage-explorer-troubleshooting.md). Zapoznaj się z przewodnikiem rozwiązywania problemów, jeśli nie możesz uzyskać dostępu do danych po pomyślnym dodaniu połączenia.

#### <a name="use-a-name-and-key"></a>Używanie nazwy i klucza

1. Wybierz symbol **połączenia** , aby otworzyć okno **łączenie z usługą Azure Storage**.

    ![Opcja Połącz z usługą Azure Storage][9]

1. Wybierz pozycję **Użyj nazwy i klucza konta magazynu**, a następnie wybierz przycisk **dalej**.

1. Wybierz nazwę wyświetlaną dla połączenia.

1. Wprowadź nazwę konta magazynu i jeden z jego kluczy dostępu.

1. Wybierz **domenę magazynu** , która ma zostać użyta, a następnie wybierz pozycję **dalej**.

1. Przejrzyj **Podsumowanie połączenia** , aby upewnić się, że wszystkie informacje są poprawne. Jeśli tak jest, wybierz pozycję **Połącz**. W przeciwnym razie wybierz pozycję **Wstecz** , aby powrócić do poprzedniej strony, aby naprawić wszelkie nieprawidłowe informacje.

Po pomyślnym dodaniu połączenia drzewo zasobów przechodzi do węzła, który reprezentuje połączenie. Zasób jest wyświetlany w obszarze **lokalne & dołączone**  >  **konta magazynu**. Jeśli Eksplorator usługi Storage nie może dodać połączenia lub jeśli nie możesz uzyskać dostępu do danych po pomyślnym dodaniu połączenia, zobacz [Eksplorator usługi Azure Storage Przewodnik rozwiązywania problemów](./storage/common/storage-explorer-troubleshooting.md).

#### <a name="attach-to-a-local-emulator"></a>Dołączanie do emulatora lokalnego

Eksplorator usługi Storage obecnie obsługuje dwa oficjalne emulatory magazynu:

* [Emulator usługi Azure Storage](storage/common/storage-use-emulator.md) (tylko system Windows)
* [Azurite](https://github.com/azure/azurite) (Windows, MacOS lub Linux)

Jeśli emulator nasłuchuje na portach domyślnych, można użyć węzła **emulator-default Ports** , aby uzyskać dostęp do emulatora. Wyszukaj **porty domyślne emulatora** w obszarze **lokalne & dołączone**  >  **konta magazynu**.

Jeśli chcesz użyć innej nazwy dla połączenia lub jeśli emulator nie jest uruchomiony na portach domyślnych, wykonaj następujące kroki:

1. Uruchom emulator. Wprowadź polecenie, `AzureStorageEmulator.exe status` Aby wyświetlić porty dla każdego typu usługi.

   > [!IMPORTANT]
   > Eksplorator usługi Storage nie uruchamia automatycznie emulatora. Należy ją uruchomić ręcznie.

1. Wybierz symbol **połączenia** , aby otworzyć okno **łączenie z usługą Azure Storage**.

    ![Opcja Połącz z usługą Azure Storage][9]

1. Wybierz opcję **Dołącz do lokalnego emulatora**, a następnie wybierz przycisk **dalej**.

1. Wybierz nazwę wyświetlaną dla połączenia i wprowadź porty, dla których emulator nasłuchuje dla każdego typu usługi. **Dołączanie do lokalnego emulatora** sugeruje domyślne wartości portów dla większości emulatorów. **Port plików** jest pusty, ponieważ żaden z oficjalnych emulatorów aktualnie nie obsługuje usługi Files. Jeśli używany emulator obsługuje pliki, możesz wprowadzić port do użycia. Następnie wybierz pozycję **Dalej**.

1. Przejrzyj **Podsumowanie połączenia** i upewnij się, że wszystkie informacje są poprawne. Jeśli tak jest, wybierz pozycję **Połącz**. W przeciwnym razie wybierz pozycję **Wstecz** , aby powrócić do poprzedniej strony, aby naprawić wszelkie nieprawidłowe informacje.

Po pomyślnym dodaniu połączenia drzewo zasobów przechodzi do węzła, który reprezentuje połączenie. Węzeł powinien pojawić się w obszarze **lokalne & dołączone**  >  **konta magazynu**. Jeśli Eksplorator usługi Storage nie może dodać połączenia lub jeśli nie możesz uzyskać dostępu do danych po pomyślnym dodaniu połączenia, zobacz [Eksplorator usługi Azure Storage Przewodnik rozwiązywania problemów](./storage/common/storage-explorer-troubleshooting.md).

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
>
>

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie zasobami usługi Azure Blob Storage za pomocą Eksplorator usługi Storage](vs-azure-tools-storage-explorer-blobs.md)
* [Praca z danymi za pomocą Eksploratora usługi Azure Storage](./cosmos-db/storage-explorer.md)
* [Zarządzanie zasobami Azure Data Lake Store przy użyciu Eksplorator usługi Storage](./data-lake-store/data-lake-store-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Overview.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageAccounts.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-azure-environment.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/account-panel-subscriptions-apply.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SubscriptionNode.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/PortalAccessKeys.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccessKeys.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-AddWithKeySelected.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-NameAndKeyPage.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount-Detach.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-shared-access-signature-for-storage-explorer.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/create-shared-access-signature-for-storage-explorer.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-WithConnStringOrSASSelected.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-1.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithSASAccount.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-2.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ServiceAttachedWithSAS.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-cosmos-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string-for-cosmos-db.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-search-for-resource.png