---
title: 'Samouczek: automatyzacja zmiany rozmiaru przekazanych obrazów za pomocą usługi Azure Event Grid'
description: 'Samouczek: Usługa Azure Event Grid może wyzwolić przekazywanie obiektów blob w usłudze Azure Storage. W ten sposób można wysyłać obrazy przekazane do usługi Azure Storage do innych usług, takich jak Azure Functions, zmieniać ich rozmiar i wprowadzać inne ulepszenia.'
services: event-grid, functions
author: spelluru
manager: jpconnoc
editor: ''
ms.service: event-grid
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2020
ms.author: spelluru
ms.custom: mvc
ms.openlocfilehash: 1d1da88d1e7eaf06ebf71da999ef8fb25c7cf066
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81482195"
---
# <a name="tutorial-automate-resizing-uploaded-images-using-event-grid"></a>Samouczek: automatyzacja zmiany rozmiaru przesłanych obrazów przy użyciu siatki zdarzeń

[Azure Event Grid](overview.md) to usługa do obsługi zdarzeń dla chmury. Usługa Event Grid pozwala tworzyć subskrypcje zdarzeń zgłaszanych przez usługi platformy Azure lub zasoby innych firm.  

Ten samouczek to druga część serii samouczków na temat usługi Storage. Stanowi rozszerzenie [poprzedniego samouczka na temat usługi Storage][previous-tutorial] o dodanie bezserwerowego, automatycznego generowania miniatur za pomocą usług Azure Event Grid i Azure Functions. Dzięki usłudze Event Grid usługa [Azure Functions](../azure-functions/functions-overview.md) może reagować na zdarzenia usługi [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) i generować miniatury przekazanych obrazów. Subskrypcja zdarzeń jest tworzona dla zdarzenia tworzenia usługi Blob Storage. Gdy do konkretnego kontenera usługi Blob Storage dodawany jest obiekt blob, następuje wywołanie punktu końcowego funkcji. Za pomocą danych przekazanych do powiązania funkcji z usługi Event Grid uzyskiwany jest dostęp do obiektu blob i generowana jest miniatura obrazu.

Aby dodać funkcję zmiany rozmiaru do istniejącej aplikacji do przekazywania obrazów, używane są interfejs wiersza polecenia platformy Azure i witryna Azure Portal.

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

![Opublikowana aplikacja internetowa w przeglądarce](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

# <a name="nodejs-v10-sdk"></a>[Zestaw Node.js V10 SDK](#tab/nodejsv10)

![Opublikowana aplikacja internetowa w przeglądarce](./media/resize-images-on-storage-blob-upload-event/upload-app-nodejs-thumb.png)

---

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie konta usługi Azure Storage
> * Wdrażanie bezserwerowego kodu za pomocą usługi Azure Functions
> * Tworzenie subskrypcji zdarzeń usługi Blob Storage w usłudze Event Grid

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W celu ukończenia tego samouczka:

Musisz wcześniej ukończyć poprzedni samouczek na temat usługi Blob Storage: [Przekazywanie danych obrazu w chmurze za pomocą usługi Azure Storage][previous-tutorial].

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.14 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Jeśli nie korzystasz z usługi Cloud Shell, musisz się najpierw zalogować za pomocą polecenia `az login`.

Jeśli wcześniej dostawca zasobów usługi Event Grid nie został zarejestrowany w ramach subskrypcji, upewnij się, że teraz jest on zarejestrowany.

```azurecli-interactive
az provider register --namespace Microsoft.EventGrid
```

## <a name="create-an-azure-storage-account"></a>Tworzenie konta usługi Azure Storage

Usługa Azure Functions wymaga konta magazynu ogólnego. Oprócz konta usługi Blob Storage utworzonego w poprzednim samouczku utwórz oddzielne konto magazynu ogólnego w grupie zasobów przy użyciu polecenia [az storage account create](/cli/azure/storage/account). Nazwy kont usługi Magazyn muszą mieć długość od 3 do 24 znaków i mogą zawierać tylko cyfry i małe litery.

1. Ustaw zmienną do przechowywania nazwy grupy zasobów utworzonej w poprzednim samouczku.

    ```azurecli-interactive
    resourceGroupName="myResourceGroup"
    ```
2. Ustaw zmienną dla nazwy nowego konta magazynu wymaganego przez usługę Azure Functions.
    ```azurecli-interactive
    functionstorage="<name of the storage account to be used by the function>"
    ```
3. Utwórz konto magazynu dla funkcji platformy Azure.

    ```azurecli-interactive
    az storage account create --name $functionstorage --location southeastasia \
    --resource-group $resourceGroupName --sku Standard_LRS --kind StorageV2
    ```

## <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji  

Do obsługi wykonywania funkcji potrzebna jest aplikacja funkcji. Aplikacja funkcji zapewnia środowisko do bezserwerowego wykonywania kodu funkcji. Utwórz aplikację funkcji przy użyciu polecenia [az functionapp create](/cli/azure/functionapp).

W poniższym poleceniu podaj własną, unikatową nazwę aplikacji funkcji. Nazwa aplikacji funkcji jest używana jako domyślna domena DNS aplikacji funkcji, więc nazwa ta musi być unikatowa wśród wszystkich aplikacji na platformie Azure.

1. Określ nazwę aplikacji funkcji, która ma zostać utworzona.

    ```azurecli-interactive
    functionapp="<name of the function app>"
    ```
2. Utwórz funkcję platformy Azure.

    ```azurecli-interactive
    az functionapp create --name $functionapp --storage-account $functionstorage \
      --resource-group $resourceGroupName --consumption-plan-location southeastasia \
      --functions-version 2
    ```

Teraz skonfiguruj aplikację funkcji, aby połączyć się z kontem magazynu obiektów Blob utworzonym w [poprzednim samouczku][previous-tutorial].

## <a name="configure-the-function-app"></a>Konfigurowanie aplikacji funkcji

Funkcja wymaga poświadczeń dla konta usługi Blob Storage, które są dodawane do ustawień aplikacji funkcji za pomocą polecenia [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings).

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

```azurecli-interactive
blobStorageAccount="<name of the Blob storage account you created in the previous tutorial>"
storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName \
  --name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName \
  --settings AzureWebJobsStorage=$storageConnectionString THUMBNAIL_CONTAINER_NAME=thumbnails \
  THUMBNAIL_WIDTH=100 FUNCTIONS_EXTENSION_VERSION=~2
```

# <a name="nodejs-v10-sdk"></a>[Zestaw Node.js V10 SDK](#tab/nodejsv10)

```azurecli-interactive
blobStorageAccount="<name of the Blob storage account you created in the previous tutorial>"

blobStorageAccountKey=$(az storage account keys list -g $resourceGroupName \
  -n $blobStorageAccount --query [0].value --output tsv)

storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName \
  --name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName \
  --settings FUNCTIONS_EXTENSION_VERSION=~2 BLOB_CONTAINER_NAME=thumbnails \
  AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount \
  AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey \
  AZURE_STORAGE_CONNECTION_STRING=$storageConnectionString
```

---

Ustawienie `FUNCTIONS_EXTENSION_VERSION=~2` sprawia, że aplikacja funkcji jest uruchamiana w ramach wersji 2.x środowiska uruchomieniowego usługi Azure Functions.

Teraz możesz wdrożyć projekt kodu funkcji do tej aplikacji funkcji.

## <a name="deploy-the-function-code"></a>Wdrażanie kodu funkcji 

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

Przykładowa funkcja zmiany rozmiaru w formacie C# jest dostępna w usłudze [GitHub](https://github.com/Azure-Samples/function-image-upload-resize). Wdróż ten projekt kodu w aplikacji funkcji, używając polecenia [az functionapp deployment source config](/cli/azure/functionapp/deployment/source).

```azurecli-interactive
az functionapp deployment source config --name $functionapp --resource-group $resourceGroupName \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/function-image-upload-resize
```

# <a name="nodejs-v10-sdk"></a>[Zestaw Node.js V10 SDK](#tab/nodejsv10)

Przykładowa funkcja zmiany rozmiaru w formacie Node.js jest dostępna w usłudze [GitHub](https://github.com/Azure-Samples/storage-blob-resize-function-node-v10). Wdróż ten projekt kodu funkcji do aplikacji funkcji, używając polecenia [az functionapp deployment source config](/cli/azure/functionapp/deployment/source).

```azurecli-interactive
az functionapp deployment source config --name $functionapp \
  --resource-group $resourceGroupName --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/storage-blob-resize-function-node-v10
```
---

Funkcja zmiany rozmiaru obrazu jest wyzwalana przez żądania HTTP wysyłane do niej z usługi Event Grid. Utwórz subskrypcję zdarzeń, aby określić w usłudze Event Grid, że chcesz otrzymywać te powiadomienia pod adresem URL funkcji. W tym samouczku zasubskrybowano zdarzenia tworzone przez obiekty blob.

Dane przekazane do funkcji z powiadomienia usługi Event Grid zawierają adres URL obiektu blob. Ten adres URL jest następnie przekazywany do powiązania wejściowego w celu pobrania przekazanego obrazu z magazynu Blob Storage. Funkcja generuje obraz miniatury i zapisuje wynikowy strumień do oddzielnego kontenera w usłudze Blob Storage.

Ten projekt używa wartości `EventGridTrigger` dla typu wyzwalacza. Zamiast ogólnych wyzwalaczy HTTP zaleca się korzystanie z wyzwalacza usługi Event Grid. Usługa Event Grid automatycznie weryfikuje wyzwalacze funkcji usługi Event Grid. W przypadku ogólnych wyzwalaczy HTTP trzeba zaimplementować [odpowiedź weryfikacji](security-authentication.md).

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

Aby dowiedzieć się więcej na temat tej funkcji, zobacz [pliki function.json i run.csx](https://github.com/Azure-Samples/function-image-upload-resize/tree/master/ImageFunctions).

# <a name="nodejs-v10-sdk"></a>[Zestaw Node.js V10 SDK](#tab/nodejsv10)

Aby dowiedzieć się więcej na temat tej funkcji, zobacz [pliki function.json i index.js](https://github.com/Azure-Samples/storage-blob-resize-function-node-v10/tree/master/Thumbnail).

---

Kod projektu funkcji jest wdrażany bezpośrednio z publicznego przykładowego repozytorium. Aby dowiedzieć się więcej na temat opcji wdrażania dla usługi Azure Functions, zobacz [Ciągłe wdrażanie dla usługi Azure Functions](../azure-functions/functions-continuous-deployment.md).

## <a name="create-an-event-subscription"></a>Tworzenie subskrypcji zdarzeń

Subskrypcja zdarzeń wskazuje, które zdarzenia generowane przez dostawcę mają być wysyłane do określonego punktu końcowego. W tym przypadku punkt końcowy jest uwidaczniany przez Twoją funkcję. Poniżej przedstawiono procedurę tworzenia subskrypcji zdarzeń wysyłającej powiadomienia do funkcji w witrynie Azure Portal:

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz pozycję **Wszystkie usługi** z menu po lewej stronie, a następnie wybierz pozycję **Aplikacje funkcji**.

    ![Przechodzenie do aplikacji funkcji w witrynie Azure portal](./media/resize-images-on-storage-blob-upload-event/portal-find-functions.png)

2. Rozwiń swoją aplikację funkcji, wybierz funkcję **Thumbnail**, a następnie wybierz pozycję **Dodaj subskrypcję usługi Event Grid**.

    ![Przejdź do subskrypcji Dodaj siatkę zdarzeń w witrynie Azure portal](./media/resize-images-on-storage-blob-upload-event/add-event-subscription.png)

3. Użyj ustawień subskrypcji zdarzeń w sposób określony w tabeli poniżej.
    
    ![Tworzenie subskrypcji zdarzeń z funkcji w witrynie Azure Portal](./media/resize-images-on-storage-blob-upload-event/event-subscription-create.png)

    | Ustawienie      | Sugerowana wartość  | Opis                                        |
    | ------------ | ---------------- | -------------------------------------------------- |
    | **Nazwa** | imageresizersub | Nazwa identyfikująca nową subskrypcję zdarzeń. |
    | **Typ tematu** | Konta magazynu | Wybierz dostawcę zdarzeń konta usługi Storage. |
    | **Subskrypcja** | Twoja subskrypcja platformy Azure | Domyślnie jest wybrana Twoja bieżąca subskrypcja platformy Azure. |
    | **Grupa zasobów** | myResourceGroup | Wybierz pozycję **Użyj istniejącej** i wybierz grupę zasobów używaną w tym samouczku. |
    | **Zasób** | Konto usługi Blob Storage | Wybierz utworzone konto usługi Blob Storage. |
    | **Typy zdarzeń** | Utworzony obiekt blob | Anuluj zaznaczenie wszystkich typów innych niż **Utworzony obiekt blob**. Tylko typy zdarzeń `Microsoft.Storage.BlobCreated` są przekazywane do funkcji. |
    | **Typ punktu końcowego** | generowany automatycznie | Wstępnie zdefiniowana jako **funkcja platformy Azure**. |
    | **Punktu końcowego** | generowany automatycznie | Nazwa funkcji. W tym przypadku jest to **miniatura**. |

4. Przełącz się na kartę **Filtry** i wykonaj następujące czynności:
    1. Zaznacz pole wyboru **Enable subject filtering** (Włącz filtrowanie tematów).
    2. W polu **Subject begins with** (Temat rozpoczyna się od) wprowadź następującą wartość: **/blobServices/default/containers/images/blobs/**.

        ![Określ filtr dla subskrypcji zdarzeń](./media/resize-images-on-storage-blob-upload-event/event-subscription-filter.png)

5. Wybierz pozycję **Create** (Utwórz), aby dodać subskrypcję zdarzeń. Spowoduje to utworzenie subskrypcji zdarzenia, która wyzwala `Thumbnail` funkcję po `images` dodaniu obiektu blob do kontenera. Funkcja zmieni rozmiar obrazów i doda je do kontenera `thumbnails`.

Ponieważ usługi zaplecza zostały już skonfigurowane, można przetestować funkcję zmieniania rozmiaru obrazów w przykładowej aplikacji internetowej.

## <a name="test-the-sample-app"></a>Testowanie przykładowej aplikacji

Aby przetestować zmienianie rozmiaru obrazów w aplikacji internetowej, przejdź pod adres URL Twojej opublikowanej aplikacji. Domyślnym adresem URL aplikacji internetowej jest `https://<web_app>.azurewebsites.net`.

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

Kliknij region **Upload photos** (Przekazywanie zdjęć), aby wybrać i przekazać plik. Możesz także przeciągnąć zdjęcia do tego obszaru.

Należy zauważyć, że po zniknięciu przesłanego obrazu kopia przesłanego obrazu jest wyświetlana w karuzeli **Wygenerowane miniatury.** Rozmiar obrazu został zmieniony przez funkcję, obraz został dodany do kontenera *thumbnails* i pobrany przez klienta internetowego.

![Opublikowana aplikacja internetowa w przeglądarce](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

# <a name="nodejs-v10-sdk"></a>[Zestaw Node.js V10 SDK](#tab/nodejsv10)

Kliknij pozycję **Choose File** (Wybierz plik), aby wybrać plik, a następnie kliknij przycisk **Upload Image** (Przekaż obraz). Po pomyślnym zakończeniu przekazywania w przeglądarce pojawi się strona z informacją o powodzeniu operacji. Kliknij link, aby powrócić do strony głównej. Kopia przesłanego obrazu jest wyświetlana w obszarze **Wygenerowane miniatury.** (Jeśli obraz nie pojawia się na początku, spróbuj ponownie załadować stronę). Ten obraz został przesunięty przez funkcję, dodany do kontenera *miniatur* i pobrany przez klienta sieci web.

![Opublikowana aplikacja internetowa w przeglądarce](./media/resize-images-on-storage-blob-upload-event/upload-app-nodejs-thumb.png)

---

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie ogólnego konta usługi Azure Storage
> * Wdrażanie bezserwerowego kodu za pomocą usługi Azure Functions
> * Tworzenie subskrypcji zdarzeń usługi Blob Storage w usłudze Event Grid

Przejdź do trzeciej części serii samouczków na temat usługi Storage, aby dowiedzieć się, jak zabezpieczyć dostęp do konta magazynu.

> [!div class="nextstepaction"]
> [Zabezpieczanie dostępu do danych aplikacji w chmurze](../storage/blobs/storage-secure-access-application.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

+ Aby dowiedzieć się więcej na temat usługi Event Grid, zobacz [Wprowadzenie do usługi Azure Event Grid](overview.md).
+ Aby wypróbować inny samouczek na temat usługi Azure Functions, zobacz [Tworzenie funkcji integrującej się z usługą Azure Logic Apps](../azure-functions/functions-twitter-email.md).

[previous-tutorial]: ../storage/blobs/storage-upload-process-images.md
