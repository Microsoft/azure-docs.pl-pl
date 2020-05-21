---
title: Samouczek — dostęp do usługi BLOB Storage przy użyciu magazynu kluczy przy użyciu Azure Databricks
description: W tym samouczku opisano, jak uzyskać dostęp do usługi Azure Blob Storage z Azure Databricks przy użyciu wpisów tajnych przechowywanych w magazynie kluczy.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 07/19/2019
ms.openlocfilehash: 026165c7c2052992e8ab485f9ab81c8964f38235
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83647967"
---
# <a name="tutorial-access-azure-blob-storage-from-azure-databricks-using-azure-key-vault"></a>Samouczek: Dostęp do usługi Azure Blob Storage z usługi Azure Databricks przy użyciu usługi Azure Key Vault

W tym samouczku opisano, jak uzyskać dostęp do usługi Azure Blob Storage z Azure Databricks przy użyciu wpisów tajnych przechowywanych w magazynie kluczy.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Tworzenie konta magazynu i kontenera obiektów BLOB
> * Tworzenie Azure Key Vault i Dodawanie wpisu tajnego
> * Tworzenie obszaru roboczego Azure Databricks i Dodawanie zakresu tajnego
> * Dostęp do kontenera obiektów blob z Azure Databricks

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

> [!Note]
> Tego samouczka nie można przeprowadzić za pomocą **subskrypcji bezpłatnej wersji próbnej platformy Azure**.
> Jeśli masz bezpłatne konto, przejdź do swojego profilu i Zmień subskrypcję na **płatność zgodnie z rzeczywistym**użyciem. Aby uzyskać więcej informacji, zobacz [Bezpłatne konto platformy Azure](https://azure.microsoft.com/free/). Następnie [Usuń limit wydatków](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)i [Poproś o zwiększenie limitu przydziału](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) dla procesorów wirtualnych vCPU w Twoim regionie. Podczas tworzenia obszaru roboczego Azure Databricks możesz wybrać warstwę cenową **wersji próbnej (Premium-14-Days Free dBu)** , aby umożliwić dostęp do obszaru roboczego bezpłatnie Azure Databricks DBU przez 14 dni.

## <a name="create-a-storage-account-and-blob-container"></a>Tworzenie konta magazynu i kontenera obiektów BLOB

1. W Azure Portal wybierz pozycję **Utwórz**  >  **Magazyn**zasobów. Następnie wybierz pozycję **konto magazynu**.

   ![Znajdź zasób konta usługi Azure Storage](./media/store-secrets-azure-key-vault/create-storage-account-resource.png)

2. Wybierz swoją subskrypcję i grupę zasobów lub Utwórz nową grupę zasobów. Następnie wprowadź nazwę konta magazynu i wybierz lokalizację. Wybierz pozycję **Recenzja + Utwórz**.

   ![Ustawianie właściwości konta magazynu](./media/store-secrets-azure-key-vault/create-storage-account.png)

3. Jeśli sprawdzanie poprawności zakończy się niepowodzeniem, Rozwiąż problemy i spróbuj ponownie. Jeśli sprawdzanie poprawności zakończy się pomyślnie, wybierz pozycję **Utwórz** i poczekaj na utworzenie konta magazynu.

4. Przejdź do nowo utworzonego konta magazynu i wybierz pozycję **obiekty blob** w obszarze **usługi** na stronie **Przegląd** . Następnie wybierz pozycję **+ kontener** i wprowadź nazwę kontenera. Wybierz przycisk **OK**.

   ![Utwórz nowy kontener](./media/store-secrets-azure-key-vault/create-blob-storage-container.png)

5. Znajdź plik, który chcesz przekazać do kontenera magazynu obiektów BLOB. Jeśli nie masz pliku, użyj edytora tekstów, aby utworzyć nowy plik tekstowy z niektórymi informacjami. W tym przykładzie plik o nazwie **HW. txt** zawiera tekst "Hello World". Zapisz plik tekstowy lokalnie i przekaż go do kontenera magazynu obiektów BLOB.

   ![Przekaż plik do kontenera](./media/store-secrets-azure-key-vault/upload-txt-file.png)

6. Wróć do konta magazynu i wybierz pozycję **klucze dostępu** w obszarze **Ustawienia**. Skopiuj **nazwę konta magazynu** i **klucz 1** do edytora tekstów w celu późniejszego użycia w tym samouczku.

   ![Znajdowanie kluczy dostępu do konta magazynu](./media/store-secrets-azure-key-vault/storage-access-keys.png)

## <a name="create-an-azure-key-vault-and-add-a-secret"></a>Tworzenie Azure Key Vault i Dodawanie wpisu tajnego

1. W Azure Portal wybierz pozycję **Utwórz zasób** i wprowadź **Key Vault** w polu wyszukiwania.

   ![Utwórz pole wyszukiwania zasobów platformy Azure](./media/store-secrets-azure-key-vault/find-key-vault-resource.png)

2. Zasób Key Vault jest wybierany automatycznie. Wybierz przycisk **Utwórz**.

   ![Tworzenie zasobu Key Vault](./media/store-secrets-azure-key-vault/create-key-vault-resource.png)

3. Na stronie **Tworzenie magazynu kluczy** wprowadź następujące informacje i Zachowaj wartości domyślne dla pozostałych pól:

   |Właściwość|Opis|
   |--------|-----------|
   |Nazwa|Unikatowa nazwa magazynu kluczy.|
   |Subskrypcja|wybierz subskrypcję.|
   |Grupa zasobów|Wybierz grupę zasobów lub Utwórz nową.|
   |Lokalizacja|Wybierz lokalizację.|

   ![Właściwości magazynu kluczy Azure](./media/store-secrets-azure-key-vault/create-key-vault-properties.png)

3. Po podaniu powyższych informacje wybierz przycisk **Utwórz**. 

4. Przejdź do nowo utworzonego magazynu kluczy w Azure Portal i wybierz pozycję wpisy **tajne**. Następnie wybierz pozycję **+ Generuj/Importuj**. 

   ![Generuj nowy wpis tajny magazynu kluczy](./media/store-secrets-azure-key-vault/generate-import-secrets.png)

5. Na stronie **Tworzenie wpisu tajnego** podaj następujące informacje i Zachowaj wartości domyślne dla pozostałych pól:

   |Właściwość|Wartość|
   |--------|-----------|
   |Opcje przekazywania|Ręcznie|
   |Nazwa|Przyjazna nazwa klucza konta magazynu.|
   |Wartość|Klucz1 z konta magazynu.|

   ![Właściwości nowego wpisu tajnego magazynu kluczy](./media/store-secrets-azure-key-vault/create-storage-secret.png)

6. Zapisz nazwę klucza w edytorze tekstu do użycia w dalszej części tego samouczka, a następnie wybierz pozycję **Utwórz**. Następnie przejdź do menu **Właściwości** . Skopiuj **nazwę DNS** i **Identyfikator zasobu** do edytora tekstu do użycia w dalszej części tego samouczka.

   ![Kopiuj Azure Key Vault nazw DNS i identyfikator zasobu](./media/store-secrets-azure-key-vault/copy-dns-resource.png)

## <a name="create-an-azure-databricks-workspace-and-add-a-secret-scope"></a>Tworzenie obszaru roboczego Azure Databricks i Dodawanie zakresu tajnego

1. W Azure Portal wybierz pozycję **Utwórz**  >  **Analytics**  >  **Azure Databricks**analizy zasobów.

    ![Datakostki na Azure Portal](./media/store-secrets-azure-key-vault/azure-databricks-on-portal.png)

2. W obszarze **Azure Databricks Service**podaj następujące wartości, aby utworzyć obszar roboczy datakostki.

   |Właściwość  |Opis  |
   |---------|---------|
   |Nazwa obszaru roboczego     | Podaj nazwę obszaru roboczego usługi Databricks.        |
   |Subskrypcja     | Z listy rozwijanej wybierz subskrypcję platformy Azure.        |
   |Grupa zasobów     | Wybierz tę samą grupę zasobów, która zawiera Twój Magazyn kluczy. |
   |Lokalizacja     | Wybierz tę samą lokalizację, w której znajduje się Azure Key Vault. W przypadku wszystkich dostępnych regionów zobacz [usługi platformy Azure dostępne według regionów](https://azure.microsoft.com/regions/services/).        |
   |Warstwa cenowa     |  Wybierz warstwę **Standardowa** lub **Premium**. Aby uzyskać więcej informacji o tych warstwach, zobacz [stronę usługi Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

   ![Właściwości obszaru roboczego datakosteks](./media/store-secrets-azure-key-vault/create-databricks-service.png)

   Wybierz przycisk **Utwórz**.

3. Przejdź do nowo utworzonego zasobu Azure Databricks w Azure Portal i wybierz pozycję **Uruchom obszar roboczy**.

   ![Uruchom Azure Databricks obszar roboczy](./media/store-secrets-azure-key-vault/launch-databricks-workspace.png)

4. Gdy obszar roboczy Azure Databricks zostanie otwarty w osobnym oknie, Dołącz **#secrets/CreateScope** do adresu URL. Adres URL powinien mieć następujący format: 

   **https://< \location>. azuredatabricks.NET/?o =< \orgid> # Secret/**.
   

5. Wprowadź nazwę zakresu i wprowadź wcześniej zapisany Azure Key Vault nazwę DNS i identyfikator zasobu. Zapisz nazwę zakresu w edytorze tekstu do użycia w dalszej części tego samouczka. Następnie wybierz przycisk **Create** (Utwórz).

   ![Utwórz zakres tajny w obszarze roboczym Azure Databricks](./media/store-secrets-azure-key-vault/create-secret-scope.png)

## <a name="access-your-blob-container-from-azure-databricks"></a>Dostęp do kontenera obiektów blob z Azure Databricks

1. Na stronie głównej obszaru roboczego Azure Databricks wybierz pozycję **nowy klaster** w obszarze **typowe zadania**.

   ![Tworzenie nowego notesu Azure Databricks](./media/store-secrets-azure-key-vault/create-new-cluster.png)

2. Wprowadź nazwę klastra i wybierz pozycję **Utwórz klaster**. Utworzenie klastra trwa kilka minut.

3. Po utworzeniu klastra przejdź do strony głównej obszaru roboczego Azure Databricks, a następnie wybierz pozycję **Nowy Notes** w obszarze **typowe zadania**.

   ![Tworzenie nowego notesu Azure Databricks](./media/store-secrets-azure-key-vault/create-new-notebook.png)

4. Wprowadź nazwę notesu i Ustaw język na Python. Ustaw klaster na nazwę klastra utworzonego w poprzednim kroku.

5. Uruchom następujące polecenie, aby zainstalować kontener magazynu obiektów BLOB. Pamiętaj, aby zmienić wartości następujących właściwości:

   * nazwa kontenera
   * Nazwa konta magazynu
   * Nazwa instalacji
   * Konfiguracja — klucz
   * Nazwa zakresu
   * Nazwa klucza

   ```python
   dbutils.fs.mount(
   source = "wasbs://<your-container-name>@<your-storage-account-name>.blob.core.windows.net",
   mount_point = "/mnt/<mount-name>",
   extra_configs = {"<conf-key>":dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>")})
   ```

   * **Nazwa instalacji** jest ścieżką DBFS reprezentującą lokalizację kontenera BLOB Storage lub folderu wewnątrz kontenera (określonego w źródle).
   * **conf-Key** może `fs.azure.account.key.<\your-storage-account-name>.blob.core.windows.net` być albo`fs.azure.sas.<\your-container-name>.<\your-storage-account-name>.blob.core.windows.net`
   * **SCOPE-Name** to nazwa zakresu tajnego utworzonego w poprzedniej sekcji. 
   * **Key-Name** to nazwa klucza tajnego utworzonego dla klucza konta magazynu w magazynie kluczy.

   ![Utwórz instalację magazynu obiektów BLOB w notesie](./media/store-secrets-azure-key-vault/command1.png)

6. Uruchom następujące polecenie, aby odczytać plik tekstowy z kontenera magazynu obiektów BLOB do ramki Dataframe. Zmień wartości w poleceniu, aby pasowały do nazwy instalacji i nazwy pliku.

   ```python
   df = spark.read.text("/mnt/<mount-name>/<file-name>")
   ```

   ![Odczytaj plik do ramki danych](./media/store-secrets-azure-key-vault/command2.png)

7. Użyj poniższego polecenia, aby wyświetlić zawartość pliku.

   ```python
   df.show()
   ```
   ![Pokaż ramkę danych](./media/store-secrets-azure-key-vault/command3.png)

8. Aby odinstalować magazyn obiektów blob, uruchom następujące polecenie:

   ```python
   dbutils.fs.unmount("/mnt/<mount-name>")
   ```

   ![Odinstalowanie konta magazynu](./media/store-secrets-azure-key-vault/command4.png)

9. Zwróć uwagę, że po odinstalowaniu instalacji nie można już czytać danych z konta usługi BLOB Storage.

   ![Błąd podczas odinstalowywania konta magazynu](./media/store-secrets-azure-key-vault/command5.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, Usuń całą grupę zasobów, wykonując następujące czynności:

1. W menu po lewej stronie w obszarze Azure Portal wybierz pozycję **grupy zasobów** i przejdź do grupy zasobów.

2. Wybierz pozycję **Usuń grupę zasobów** , a następnie wpisz nazwę grupy zasobów. Następnie wybierz pozycję **Usuń**. 

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak zaimplementować środowisko datakostki sieci wirtualnej z włączonym punktem końcowym usługi dla Cosmos DB.
> [!div class="nextstepaction"]
> [Samouczek: implementowanie Azure Databricks przy użyciu punktu końcowego Cosmos DB](service-endpoint-cosmosdb.md)
