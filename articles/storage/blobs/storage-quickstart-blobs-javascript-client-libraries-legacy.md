---
title: 'Szybki Start: usługa Azure Blob Storage dla języka JavaScript v10 w przeglądarce'
description: Dowiedz się, jak przekazywać, wyświetlać i usuwać obiekty blob za pomocą zestawu SDK języka JavaScript v10 na stronie HTML.
services: storage
author: twooley
ms.custom: mvc, devx-track-js
ms.service: storage
ms.author: twooley
ms.date: 07/24/2020
ms.topic: quickstart
ms.subservice: blobs
ms.openlocfilehash: b3cca61fe272b61f2227e7546b37e4ea37cfd693
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280143"
---
<!-- Customer intent: As a web application developer I want to interface with Azure Blob storage entirely on the client so that I can build a SPA application that is able to upload and delete files on blob storage. -->

# <a name="quickstart-manage-blobs-with-javascript-v10-sdk-in-browser"></a>Szybki Start: Zarządzanie obiektami BLOB za pomocą zestawu SDK v10 języka JavaScript w przeglądarce

W tym przewodniku szybki start dowiesz się, jak zarządzać obiektami BLOB za pomocą kodu JavaScript działającego całkowicie w przeglądarce. Obiekty blob są obiektami, które mogą przechowywać duże ilości danych tekstowych lub binarnych, w tym obrazy, dokumenty, multimedia strumieniowe i dane archiwalne. Musisz użyć wymaganych środków zabezpieczeń, aby zapewnić chroniony dostęp do konta magazynu obiektów BLOB.

> [!NOTE]
> Ten przewodnik Szybki Start używa starszej wersji biblioteki klienta usługi Azure Blob Storage. Aby rozpocząć pracę z najnowszą wersją, zobacz [Szybki Start: Zarządzanie obiektami BLOB za pomocą zestawu SDK V12 języka JavaScript w przeglądarce](quickstart-blobs-javascript-browser.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Konto usługi Azure Storage. [Utwórz konto magazynu](../common/storage-account-create.md).
- Lokalny serwer sieci Web. W tym artykule [Node.js](https://nodejs.org) otworzyć podstawowego serwera programu.
- [Program Visual Studio Code](https://code.visualstudio.com)
- VS Code rozszerzenie do debugowania przeglądarki, takie jak [debuger dla programu Chrome](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-chrome) lub [debuger dla Microsoft Edge](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-edge).

## <a name="setting-up-storage-account-cors-rules"></a>Konfigurowanie reguł CORS konta magazynu

Zanim aplikacja sieci Web będzie mogła uzyskać dostęp do magazynu obiektów blob z klienta, musisz skonfigurować konto, aby umożliwić [udostępnianie zasobów między źródłami](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)lub CORS.

Wróć do witryny Azure Portal i wybierz konto magazynu. Aby zdefiniować nową regułę CORS, przejdź do sekcji **Ustawienia** i kliknij link **CORS** . Następnie kliknij przycisk **Dodaj**, aby otworzyć okno **Dodawanie reguły CORS**. W tym przewodniku Szybki start utworzymy otwartą regułę CORS:

![Ustawienia mechanizmu CORS konta usługi Azure Blob Storage](media/storage-quickstart-blobs-javascript-client-libraries-v10/azure-blob-storage-cors-settings.png)

W poniższej tabeli przedstawiono poszczególne ustawienia mechanizmu CORS i wyjaśniono, jakie wartości zostały użyte do zdefiniowania reguły.

|Ustawienie  |Wartość  | Opis |
|---------|---------|---------|
| Dozwolone źródła | * | Dopuszczalne źródła można podać w postaci listy domen rozdzielonych przecinkami. Podanie wartości `*` umożliwia dostęp do konta magazynu ze wszystkich domen. |
| Dozwolone metody     | delete, get, head, merge, post, options i put | Wyświetla listę poleceń HTTP, które można wykonać na koncie magazynu. Na potrzeby tego przewodnika Szybki start wybierz wszystkie dostępne opcje. |
| Dozwolone nagłówki | * | Definiuje listę nagłówków żądań (łącznie z nagłówkami z prefiksem) dozwolonych na koncie magazynu. Ustawienie wartości `*` zezwala na dostęp za pomocą wszystkich nagłówków. |
| Uwidocznione nagłówki | * | Wyświetla listę dozwolonych nagłówków odpowiedzi według kont. Ustawienie wartości `*` zezwala kontu na wysyłanie dowolnego nagłówka.  |
| Maksymalny wiek (w sekundach) | 86400 | Maksymalny czas buforowania żądania wstępnego OPTIONS przez przeglądarkę. Podanie wartości *86400* powoduje, że buforowanie trwa całą dobę. |

> [!IMPORTANT]
> Upewnij się, że wszystkie ustawienia używane w środowisku produkcyjnym uwidaczniają minimalny dostęp wymagany do konta magazynu w celu zapewnienia bezpiecznego dostępu. Opisane w tym przewodniku Szybki start ustawienia mechanizmu CORS definiują łagodne zasady zabezpieczeń. Te ustawienia nie są jednak zalecane w rzeczywistym kontekście.

Następnie utworzymy token zabezpieczający za pomocą usługi Azure Cloud Shell.

[!INCLUDE [Open the Azure cloud shell](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-shared-access-signature"></a>Tworzenie sygnatury dostępu współdzielonego

Sygnatura dostępu współdzielonego (SAS, Shared Access Signature) jest używana przez kod uruchomiony w przeglądarce do autoryzowania żądań w usłudze Blob Storage. Sygnatura dostępu współdzielonego umożliwia klientowi autoryzację dostępu do zasobów magazynu bez użycia klucza dostępu konta ani parametrów połączenia. Zobacz [Using shared access signatures (SAS) (Używanie sygnatur dostępu współdzielonego)](../common/storage-sas-overview.md), aby uzyskać więcej informacji.

Sygnaturę dostępu współdzielonego można utworzyć przy użyciu interfejsu wiersza polecenia platformy Azure za pośrednictwem usługi Azure Cloud Shell lub z Azure Portal lub Eksplorator usługi Azure Storage. W poniższej tabeli opisano parametry, które należy podać wartości w celu wygenerowania sygnatury dostępu współdzielonego za pomocą interfejsu wiersza polecenia.

| Parametr      |Opis  | Symbol zastępczy |
|----------------|-------------|-------------|
| *wygaśnięcia*       | Data wygaśnięcia tokenu dostępu w formacie RRRR-MM-DD. Na potrzeby tego przewodnika Szybki start wprowadź jutrzejszą datę. | *FUTURE_DATE* |
| *Nazwa konta* | Nazwa konta magazynu. Użyj nazwy zanotowanej w poprzednim kroku. | *YOUR_STORAGE_ACCOUNT_NAME* |
| *account-key*  | Klucz konta magazynu. Użyj klucza zanotowanego w poprzednim kroku. | *YOUR_STORAGE_ACCOUNT_KEY* |

Aby wygenerować sygnaturę dostępu współdzielonego, która może być używana w kodzie JavaScript, użyj poniższego polecenia CLI z wartościami rzeczywistymi.

```azurecli-interactive
az storage account generate-sas \
  --permissions racwdl \
  --resource-types sco \
  --services b \
  --expiry FUTURE_DATE \
  --account-name YOUR_STORAGE_ACCOUNT_NAME \
  --account-key YOUR_STORAGE_ACCOUNT_KEY
```

Serie wartości występujące po każdym parametrze mogą wydawać się niezrozumiałe. Te wartości parametrów odpowiadają pierwszym literom odpowiednich uprawnień. W poniższej tabeli wyjaśniono pochodzenie tych wartości:

| Parametr        | Wartość   | Opis  |
|------------------|---------|---------|
| *uprawnienia*    | racwdl  | Ta sygnatura dostępu współdzielonego umożliwia używanie poleceń *read*, *append*, *create*, *write*, *delete* i *list*. |
| *resource-types* | sco     | Ta sygnatura dostępu współdzielonego dotyczy następujących zasobów: *usługa*, *kontener* i *obiekt*. |
| *Services*       | b       | Ta sygnatura dostępu współdzielonego dotyczy usługi *obiektów blob*. |

Po wygenerowaniu sygnatury dostępu współdzielonego skopiuj wartość zwracaną i Zapisz ją w miejscu do użycia w nadchodzącym kroku. Jeśli sygnatura dostępu współdzielonego została wygenerowana przy użyciu metody innej niż interfejs wiersza polecenia platformy Azure, należy usunąć pierwotną wartość, `?` Jeśli jest ona obecna. Ten znak jest separatorem adresów URL, który jest już podany w szablonie adresu URL w dalszej części tego tematu, w którym jest używane sygnatura dostępu współdzielonego.

> [!IMPORTANT]
> W środowisku produkcyjnym zawsze przekazuj tokeny SAS przy użyciu protokołu TLS. Ponadto tokeny sygnatur dostępu współdzielonego powinny być generowane na serwerze i wysyłane do strony HTML w celu przekazania ich z powrotem do usługi Azure Blob Storage. Jedna z metod polega na wygenerowaniu tokenów sygnatur dostępu współdzielonego przy użyciu funkcji bezserwerowej. Witryna Azure Portal zawiera szablony funkcji umożliwiające generowanie sygnatury dostępu współdzielonego za pomocą funkcji języka JavaScript.

## <a name="implement-the-html-page"></a>Implementowanie strony HTML

W tej sekcji utworzysz podstawową stronę internetową i skonfigurujesz VS Code do uruchamiania i debugowania strony. Przed uruchomieniem programu należy jednak użyć Node.js, aby uruchomić lokalny serwer sieci Web i obsłużyć stronę, gdy przeglądarka zażąda. Następnie dodasz kod JavaScript, który wywoła różne interfejsy API magazynu obiektów blob i wyświetli wyniki na stronie. Wyniki tych wywołań można także zobaczyć w [Azure Portal](https://portal.azure.com), [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer)i [rozszerzenia usługi Azure Storage](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) dla vs Code.

### <a name="set-up-the-web-application"></a>Konfigurowanie aplikacji internetowej

Najpierw utwórz nowy folder o nazwie *Azure-Blobs-JavaScript* i otwórz go w vs Code. Następnie utwórz nowy plik w VS Code, Dodaj następujący kod HTML i Zapisz go jako *index.html* w folderze *Azure-Blobs-JavaScript* .

```html
<!DOCTYPE html>
<html>

<body>
    <button id="create-container-button">Create container</button>
    <button id="delete-container-button">Delete container</button>
    <button id="select-button">Select and upload files</button>
    <input type="file" id="file-input" multiple style="display: none;" />
    <button id="list-button">List files</button>
    <button id="delete-button">Delete selected files</button>
    <p><b>Status:</b></p>
    <p id="status" style="height:160px; width: 593px; overflow: scroll;" />
    <p><b>Files:</b></p>
    <select id="file-list" multiple style="height:222px; width: 593px; overflow: scroll;" />
</body>

<!-- You'll add code here later in this quickstart. -->

</html>
```

### <a name="configure-the-debugger"></a>Skonfiguruj debuger

Aby skonfigurować rozszerzenie debugera w VS Code, wybierz pozycję **debuguj > Dodaj konfigurację...**, a następnie wybierz pozycję **Chrome** lub **Edge**, w zależności od rozszerzenia zainstalowanego wcześniej w sekcji wymagania wstępne. Ta akcja powoduje utworzenie *launch.jsw* pliku i otwarcie go w edytorze.

Następnie zmodyfikuj *launch.jsw* pliku, aby `url` zawierała wartość, tak `/index.html` jak pokazano:

```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "type": "chrome",
            "request": "launch",
            "name": "Launch Chrome against localhost",
            "url": "http://localhost:8080/index.html",
            "webRoot": "${workspaceFolder}"
        }
    ]
}
```

Ta konfiguracja informuje VS Code, która przeglądarka zostanie uruchomiona i który adres URL ma zostać załadowany.

### <a name="launch-the-web-server"></a>Uruchom serwer sieci Web

Aby uruchomić lokalny serwer sieci Web Node.js, wybierz opcję **Wyświetl Terminal >** , aby otworzyć okno konsoli w vs Code, a następnie wprowadź następujące polecenie.

```console
npx http-server
```

To polecenie spowoduje zainstalowanie pakietu *http-Server* i uruchomienie serwera, udostępniając bieżący folder za pośrednictwem domyślnych adresów URL, włącznie z wymienionym w poprzednim kroku.

### <a name="start-debugging"></a>Rozpocznij debugowanie

Aby uruchomić *index.html* w przeglądarce z dołączonym debugerem vs Code, wybierz pozycję **Debuguj > Rozpocznij debugowanie** lub naciśnij klawisz F5 w vs Code.

Wyświetlany interfejs użytkownika nie wykonuje jeszcze niczego, ale dodasz kod JavaScript w poniższej sekcji, aby zaimplementować każdą pokazaną funkcję. Następnie można ustawić punkty przerwania i korzystać z debugera, gdy jest on wstrzymany w kodzie.

Gdy wprowadzisz zmiany w *index.html*, pamiętaj, aby ponownie załadować stronę, aby zobaczyć zmiany w przeglądarce. W VS Code można również wybrać **debuguj > ponownie debugowanie** lub nacisnąć klawisze Ctrl + Shift + F5.

### <a name="add-the-blob-storage-client-library"></a>Dodaj bibliotekę klienta magazynu obiektów BLOB

Aby włączyć wywołania interfejsu API magazynu obiektów blob, najpierw [Pobierz zestaw SDK usługi Azure Storage dla języka JavaScript — Biblioteka klienta obiektów BLOB](https://aka.ms/downloadazurestoragejsblob), Wyodrębnij zawartość pliku zip i umieść plik *azure-storage-blob.js* w folderze *Azure-Blobs-JavaScript* .

Następnie wklej następujący kod HTML do *index.html* po `</body>` tagu zamykającym, zastępując komentarz symbol zastępczy.

```html
<script src="azure-storage-blob.js" charset="utf-8"></script>

<script>
// You'll add code here in the following sections.
</script>
```

Ten kod dodaje odwołanie do pliku skryptu i zapewnia miejsce na własny kod JavaScript. Na potrzeby tego przewodnika Szybki Start używamy pliku skryptu *azure-storage-blob.js* , aby można było go otworzyć w vs Code, odczytać jego zawartość i ustawić punkty przerwania. W środowisku produkcyjnym należy używać bardziej kompaktowego pliku *azure-storage.blob.min.js* , który jest również dostępny w pliku zip.

Więcej informacji na temat każdej funkcji magazynu obiektów BLOB można znaleźć w [dokumentacji referencyjnej](/javascript/api/%40azure/storage-blob/index). Należy pamiętać, że niektóre funkcje w zestawie SDK są dostępne tylko w Node.js lub dostępne tylko w przeglądarce.

Kod w *azure-storage-blob.js* eksportuje zmienną globalną o nazwie `azblob` , która będzie używana w kodzie JavaScript w celu uzyskania dostępu do interfejsów API magazynu obiektów BLOB.

### <a name="add-the-initial-javascript-code"></a>Dodaj początkowy kod JavaScript

Następnie wklej poniższy kod do `<script>` elementu pokazanego w poprzednim bloku kodu, zastępując komentarz symbol zastępczy.

```javascript
const createContainerButton = document.getElementById("create-container-button");
const deleteContainerButton = document.getElementById("delete-container-button");
const selectButton = document.getElementById("select-button");
const fileInput = document.getElementById("file-input");
const listButton = document.getElementById("list-button");
const deleteButton = document.getElementById("delete-button");
const status = document.getElementById("status");
const fileList = document.getElementById("file-list");

const reportStatus = message => {
    status.innerHTML += `${message}<br/>`;
    status.scrollTop = status.scrollHeight;
}
```

Ten kod tworzy pola dla każdego elementu HTML, który będzie używany przez Poniższy kod, i implementuje `reportStatus` funkcję do wyświetlania danych wyjściowych.

W poniższych sekcjach Dodaj każdy nowy blok kodu JavaScript po poprzednim bloku.

### <a name="add-your-storage-account-info"></a>Dodawanie informacji o koncie magazynu

Następnie Dodaj kod, aby uzyskać dostęp do konta magazynu, zastępując symbole zastępcze nazwą konta i sygnaturą dostępu współdzielonego wygenerowaną w poprzednim kroku.

```javascript
const accountName = "<Add your storage account name>";
const sasString = "<Add the SAS you generated earlier>";
const containerName = "testcontainer";
const containerURL = new azblob.ContainerURL(
    `https://${accountName}.blob.core.windows.net/${containerName}?${sasString}`,
    azblob.StorageURL.newPipeline(new azblob.AnonymousCredential));
```

Ten kod używa informacji o koncie i sygnatury dostępu współdzielonego w celu utworzenia wystąpienia [obiektu containerurl](/javascript/api/@azure/storage-blob/ContainerURL) , które jest przydatne do tworzenia kontenera magazynu i manipulowania nim.

### <a name="create-and-delete-a-storage-container"></a>Tworzenie i usuwanie kontenera magazynu

Następnie Dodaj kod do tworzenia i usuwania kontenera magazynu po naciśnięciu odpowiedniego przycisku.

```javascript
const createContainer = async () => {
    try {
        reportStatus(`Creating container "${containerName}"...`);
        await containerURL.create(azblob.Aborter.none);
        reportStatus(`Done.`);
    } catch (error) {
        reportStatus(error.body.message);
    }
};

const deleteContainer = async () => {
    try {
        reportStatus(`Deleting container "${containerName}"...`);
        await containerURL.delete(azblob.Aborter.none);
        reportStatus(`Done.`);
    } catch (error) {
        reportStatus(error.body.message);
    }
};

createContainerButton.addEventListener("click", createContainer);
deleteContainerButton.addEventListener("click", deleteContainer);
```

Ten kod wywołuje funkcje obiektu containerurl [Create](/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) i [delete](/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-) bez używania wystąpienia elementu [Abort](/javascript/api/@azure/storage-blob/aborter) . Aby zachować prostotę w tym przewodniku Szybki Start, ten kod zakłada, że konto magazynu zostało utworzone i jest włączone. W polu kod produkcyjny Użyj wystąpienia przerwania, aby dodać funkcję limitu czasu.

### <a name="list-blobs"></a>Wyświetlanie listy obiektów blob

Następnie Dodaj kod, aby wyświetlić listę zawartości kontenera magazynu po naciśnięciu przycisku **listy pliki** .

```javascript
const listFiles = async () => {
    fileList.size = 0;
    fileList.innerHTML = "";
    try {
        reportStatus("Retrieving file list...");
        let marker = undefined;
        do {
            const listBlobsResponse = await containerURL.listBlobFlatSegment(
                azblob.Aborter.none, marker);
            marker = listBlobsResponse.nextMarker;
            const items = listBlobsResponse.segment.blobItems;
            for (const blob of items) {
                fileList.size += 1;
                fileList.innerHTML += `<option>${blob.name}</option>`;
            }
        } while (marker);
        if (fileList.size > 0) {
            reportStatus("Done.");
        } else {
            reportStatus("The container does not contain any files.");
        }
    } catch (error) {
        reportStatus(error.body.message);
    }
};

listButton.addEventListener("click", listFiles);
```

Ten kod wywołuje funkcję [obiektu containerurl. listBlobFlatSegment](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) w pętli, aby upewnić się, że wszystkie segmenty są pobierane. Dla każdego segmentu jest on pętli nad listą elementów obiektów blob, które zawiera i aktualizuje listę **plików** .

### <a name="upload-blobs"></a>Przekazywanie obiektów BLOB

Następnie Dodaj kod do przekazywania plików do kontenera magazynu po naciśnięciu przycisku **Wybierz i przekaż pliki** .

```javascript
const uploadFiles = async () => {
    try {
        reportStatus("Uploading files...");
        const promises = [];
        for (const file of fileInput.files) {
            const blockBlobURL = azblob.BlockBlobURL.fromContainerURL(containerURL, file.name);
            promises.push(azblob.uploadBrowserDataToBlockBlob(
                azblob.Aborter.none, file, blockBlobURL));
        }
        await Promise.all(promises);
        reportStatus("Done.");
        listFiles();
    } catch (error) {
        reportStatus(error.body.message);
    }
}

selectButton.addEventListener("click", () => fileInput.click());
fileInput.addEventListener("change", uploadFiles);
```

Ten kod łączy przycisk **Wybierz i przekaż pliki** do ukrytego `file-input` elementu. W ten sposób `click` zdarzenie przycisku wyzwala zdarzenie wejściowe pliku `click` i wyświetla selektor plików. Po wybraniu plików i zamknięciu okna dialogowego `input` zdarzenie występuje, a `uploadFiles` Funkcja jest wywoływana. Ta funkcja wywołuje funkcję [uploadBrowserDataToBlockBlob](/javascript/api/@azure/storage-blob/blockblobclient#uploadbrowserdata-blob---arraybuffer---arraybufferview--blockblobparalleluploadoptions-) tylko dla przeglądarki dla każdego wybranego pliku. Każde wywołanie zwraca obietnicę, która jest dodawana do listy, dzięki czemu można oczekiwać, że wszystkie jednocześnie czekają na przekazanie plików.

### <a name="delete-blobs"></a>Usuwać obiekty blob

Następnie Dodaj kod w celu usunięcia plików z kontenera magazynu po naciśnięciu przycisku **Usuń wybrane pliki** .

```javascript
const deleteFiles = async () => {
    try {
        if (fileList.selectedOptions.length > 0) {
            reportStatus("Deleting files...");
            for (const option of fileList.selectedOptions) {
                const blobURL = azblob.BlobURL.fromContainerURL(containerURL, option.text);
                await blobURL.delete(azblob.Aborter.none);
            }
            reportStatus("Done.");
            listFiles();
        } else {
            reportStatus("No files selected.");
        }
    } catch (error) {
        reportStatus(error.body.message);
    }
};

deleteButton.addEventListener("click", deleteFiles);
```

Ten kod wywołuje funkcję [BlobURL. Delete](/javascript/api/@azure/storage-blob/BlobURL#delete-aborter--iblobdeleteoptions-) , aby usunąć każdy plik wybrany na liście. Następnie wywołuje `listFiles` funkcję wskazaną wcześniej w celu odświeżenia zawartości listy **plików** .

### <a name="run-and-test-the-web-application"></a>Uruchamianie i testowanie aplikacji sieci Web

W tym momencie możesz uruchomić stronę i eksperyment, aby poznać sposób działania usługi BLOB Storage. Jeśli wystąpią błędy (na przykład podczas próby wyświetlenia listy plików przed utworzeniem kontenera), w okienku **stan** zostanie wyświetlony komunikat o błędzie otrzymany. Możesz również ustawić punkty przerwania w kodzie JavaScript, aby przeanalizować wartości zwracane przez interfejsy API magazynu.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby wyczyścić zasoby utworzone w ramach tego przewodnika Szybki Start, przejdź do [Azure Portal](https://portal.azure.com) i Usuń grupę zasobów utworzoną w sekcji wymagania wstępne.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono prostą witrynę sieci Web, która uzyskuje dostęp do usługi BLOB Storage z poziomu języka JavaScript opartego na przeglądarce. Aby dowiedzieć się, jak można hostować witrynę internetową w usłudze BLOB Storage, przejdź do następującego samouczka:

> [!div class="nextstepaction"]
> [Hostowanie statycznej witryny internetowej w usłudze Blob Storage](./storage-blob-static-website-host.md)