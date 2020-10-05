---
title: 'Szybki Start: Biblioteka usługi Azure Blob Storage V12 — JavaScript w przeglądarce'
description: W tym przewodniku szybki start dowiesz się, jak używać biblioteki klienckiej usługi Azure Blob Storage w wersji 12 dla języka JavaScript w przeglądarce. Tworzenie kontenera i obiektu w usłudze BLOB Storage. Następnie dowiesz się, jak wyświetlić listę wszystkich obiektów BLOB w kontenerze. Na koniec dowiesz się, jak usuwać obiekty blob i usuwać kontenery.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: eebfa61632bc49d5df35c17ba2d2faca0382001c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91336143"
---
<!-- Customer intent: As a web application developer I want to interface with Azure Blob storage entirely on the client so that I can build a SPA application that is able to upload and delete files on blob storage. -->

# <a name="quickstart-manage-blobs-with-javascript-v12-sdk-in-a-browser"></a>Szybki Start: Zarządzanie obiektami BLOB za pomocą zestawu SDK V12 języka JavaScript w przeglądarce

Usługa Azure Blob Storage jest zoptymalizowana pod kątem przechowywania dużych ilości danych bez struktury. Obiekty blob są obiektami, które mogą przechowywać dane tekstowe lub binarne, w tym obrazy, dokumenty, multimedia strumieniowe i dane archiwalne. W tym przewodniku szybki start dowiesz się, jak zarządzać obiektami BLOB przy użyciu języka JavaScript w przeglądarce. Zostaną przekazane i wyświetlone obiekty blob, a będziesz tworzyć i usuwać kontenery.

Dodatkowe zasoby:

* [Dokumentacja referencyjna interfejsu API](/javascript/api/@azure/storage-blob)
* [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob)
* [Pakiet (npm)](https://www.npmjs.com/package/@azure/storage-blob)
* [Samples](https://docs.microsoft.com/azure/storage/common/storage-samples-javascript?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Wymagania wstępne

* [Konto platformy Azure z aktywną subskrypcją](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Konto usługi Azure Storage](../common/storage-account-create.md)
* [Node.js](https://nodejs.org)
* [Microsoft Visual Studio Code](https://code.visualstudio.com)
* Rozszerzenie Visual Studio Code do debugowania przeglądarki, takie jak:
    * [Debuger dla przeglądarki Microsoft Edge](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-edge)
    * [Debuger dla programu Chrome](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-chrome)
    * [Debuger dla programu Firefox](https://marketplace.visualstudio.com/items?itemName=firefox-devtools.vscode-firefox-debug)


[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="object-model"></a>Model obiektów

Magazyn obiektów blob oferuje trzy typy zasobów:

* Konto magazynu
* Kontener na koncie magazynu
* Obiekt BLOB w kontenerze

Na poniższym diagramie przedstawiono relacje między tymi zasobami.

![Diagram architektury magazynu obiektów blob](./media/storage-blobs-introduction/blob1.png)

W tym przewodniku szybki start będziesz używać następujących klas języka JavaScript do korzystania z tych zasobów:

* [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient): `BlobServiceClient` Klasa umożliwia manipulowanie zasobami usługi Azure Storage i kontenerami obiektów BLOB.
* [ContainerClient](/javascript/api/@azure/storage-blob/containerclient): `ContainerClient` Klasa umożliwia manipulowanie kontenerami usługi Azure Storage i ich obiektami BLOB.
* [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient): `BlockBlobClient` Klasa umożliwia manipulowanie obiektami BLOB usługi Azure Storage.

## <a name="setting-up"></a>Konfigurowanie

W tej sekcji omówiono przygotowanie projektu do pracy z biblioteką klienta usługi Azure Blob Storage V12 dla języka JavaScript.

### <a name="create-a-cors-rule"></a>Tworzenie reguły CORS

Zanim aplikacja sieci Web będzie mogła uzyskać dostęp do magazynu obiektów blob z klienta, należy skonfigurować konto, aby umożliwić [udostępnianie zasobów między źródłami](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)lub CORS.

W Azure Portal wybierz konto magazynu. Aby zdefiniować nową regułę CORS, przejdź do sekcji **Ustawienia** i wybierz pozycję **CORS**. W tym przewodniku Szybki start utworzymy otwartą regułę CORS:

![Ustawienia mechanizmu CORS konta usługi Azure Blob Storage](media/quickstart-blobs-javascript-browser/azure-blob-storage-cors-settings.png)

W poniższej tabeli przedstawiono poszczególne ustawienia mechanizmu CORS i wyjaśniono, jakie wartości zostały użyte do zdefiniowania reguły.

|Ustawienie  |Wartość  | Opis |
|---------|---------|---------|
| **DOZWOLONE ŹRÓDŁA** | **\*** | Dopuszczalne źródła można podać w postaci listy domen rozdzielonych przecinkami. Podanie wartości `*` umożliwia dostęp do konta magazynu ze wszystkich domen. |
| **DOZWOLONE METODY** | **Usuwanie**, **pobieranie**, **Tworzenie**, **scalanie**, **Publikowanie**, **Opcje**i **Umieszczanie** | Wyświetla listę poleceń HTTP, które można wykonać na koncie magazynu. Na potrzeby tego przewodnika Szybki start wybierz wszystkie dostępne opcje. |
| **DOZWOLONE NAGŁÓWKI** | **\*** | Definiuje listę nagłówków żądań (łącznie z nagłówkami z prefiksem) dozwolonych na koncie magazynu. Ustawienie wartości `*` zezwala na dostęp za pomocą wszystkich nagłówków. |
| **UWIDOCZNIONE NAGŁÓWKI** | **\*** | Wyświetla listę dozwolonych nagłówków odpowiedzi według kont. Ustawienie wartości `*` zezwala kontu na wysyłanie dowolnego nagłówka. |
| **MAKSYMALNY WIEK** | **86400** | Maksymalny czas buforowania żądania opcji inspekcji wstępnej przez przeglądarkę w sekundach. Podanie wartości *86400* powoduje, że buforowanie trwa całą dobę. |

Po wypełnieniu pól wartościami z tej tabeli kliknij przycisk **Zapisz** .

> [!IMPORTANT]
> Upewnij się, że wszystkie ustawienia używane w środowisku produkcyjnym uwidaczniają minimalny dostęp wymagany do konta magazynu w celu zapewnienia bezpiecznego dostępu. Opisane w tym przewodniku Szybki start ustawienia mechanizmu CORS definiują łagodne zasady zabezpieczeń. Te ustawienia nie są jednak zalecane w rzeczywistym kontekście.

### <a name="create-a-shared-access-signature"></a>Tworzenie sygnatury dostępu współdzielonego

Sygnatura dostępu współdzielonego (SAS) jest używana przez kod uruchomiony w przeglądarce w celu autoryzowania żądań usługi Azure Blob Storage. Sygnatura dostępu współdzielonego umożliwia klientowi autoryzację dostępu do zasobów magazynu bez użycia klucza dostępu konta ani parametrów połączenia. Zobacz [Using shared access signatures (SAS) (Używanie sygnatur dostępu współdzielonego)](../common/storage-sas-overview.md), aby uzyskać więcej informacji.

Wykonaj następujące kroki, aby uzyskać adres URL sygnatury dostępu współdzielonego Blob service:

1. W Azure Portal wybierz konto magazynu.
2. Przejdź do sekcji **Ustawienia** i wybierz pozycję **sygnatura dostępu współdzielonego**.
3. Przewiń w dół i kliknij przycisk **Generuj sygnaturę dostępu współdzielonego i parametry połączenia** .
4. Przewiń w dół i Znajdź pole **adresu URL SAS BLOB Service**
5. Kliknij przycisk **Kopiuj do schowka** po prawej stronie pola **adresu URL SAS BLOB Service** .
6. Zapisz skopiowany adres URL w miejscu do użycia w nadchodzącym kroku.

### <a name="add-the-azure-blob-storage-client-library"></a>Dodawanie biblioteki klienckiej usługi Azure Blob Storage

Na komputerze lokalnym Utwórz nowy folder o nazwie *Azure-Blobs-js-Browser* i otwórz go w Visual Studio Code.

Wybierz pozycję **wyświetl > Terminal** , aby otworzyć okno konsoli w programie Visual Studio Code. Uruchom następujące polecenie Node.js Package Manager (npm) w oknie terminalu, aby utworzyć [package.jsna](https://docs.npmjs.com/files/package.json) pliku.

```console
npm init -y
```

Zestaw Azure SDK składa się z wielu oddzielnych pakietów. Możesz wybrać potrzebne pakiety w oparciu o usługi, których zamierzasz używać. Uruchom następujące `npm` polecenie w oknie terminalu, aby zainstalować `@azure/storage-blob` pakiet.

```console
npm install --save @azure/storage-blob
```

#### <a name="bundle-the-azure-blob-storage-client-library"></a>Pakiet biblioteki klienckiej usługi Azure Blob Storage

Aby korzystać z bibliotek zestawu Azure SDK w witrynie sieci Web, przekonwertuj swój kod, aby działał w przeglądarce. Można to zrobić za pomocą narzędzia o nazwie pakiet. Zgrupowanie Pobiera kod JavaScript zapisany przy użyciu konwencji [Node.js](https://nodejs.org) i konwertuje go na format zrozumiały dla przeglądarek. Ten artykuł szybkiego startu używa pakietu [Parcel](https://parceljs.org/) .

Zainstaluj program Parcel, uruchamiając następujące `npm` polecenie w oknie terminalu:

```console
npm install -g parcel-bundler
```

W Visual Studio Code Otwórz *package.js* pliku i Dodaj `browserlist` między nimi `license` `dependencies` . Dotyczy to `browserlist` najnowszej wersji trzech popularnych przeglądarek. Pełny *package.jsw* pliku powinien teraz wyglądać następująco:

:::code language="json" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/package.json" highlight="12-16":::

Zapisz *package.js* pliku.

### <a name="import-the-azure-blob-storage-client-library"></a>Importowanie biblioteki klienckiej usługi Azure Blob Storage

Aby użyć bibliotek zestawu Azure SDK w języku JavaScript, zaimportuj `@azure/storage-blob` pakiet. Utwórz nowy plik w Visual Studio Code zawierający poniższy kod JavaScript.

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_ImportLibrary":::

Zapisz plik jako *index.js* w katalogu *Azure-Blobs-js-Browser* .

### <a name="implement-the-html-page"></a>Implementowanie strony HTML

Utwórz nowy plik w Visual Studio Code i Dodaj następujący kod HTML.

:::code language="html" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.html":::

Zapisz plik jako *index.html* w folderze *Azure-Blobs-js-Browser* .

## <a name="code-examples"></a>Przykłady kodu

Przykładowy kod przedstawia sposób wykonywania następujących zadań za pomocą biblioteki klienckiej usługi Azure Blob Storage dla języka JavaScript:

* [Zadeklaruj pola dla elementów interfejsu użytkownika](#declare-fields-for-ui-elements)
* [Dodawanie informacji o koncie magazynu](#add-your-storage-account-info)
* [Tworzenie obiektów klienta](#create-client-objects)
* [Tworzenie i usuwanie kontenera magazynu](#create-and-delete-a-storage-container)
* [Wyświetlanie listy obiektów blob](#list-blobs)
* [Przekazywanie obiektów BLOB](#upload-blobs)
* [Usuwać obiekty blob](#delete-blobs)

Kod zostanie uruchomiony po dodaniu wszystkich fragmentów kodu do pliku *index.js* .

### <a name="declare-fields-for-ui-elements"></a>Zadeklaruj pola dla elementów interfejsu użytkownika

Dodaj następujący kod na końcu pliku *index.js* .

:::code language="JavaScript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_DeclareVariables":::

Zapisz plik *index.js* .

Ten kod deklaruje pola dla każdego elementu HTML i implementuje `reportStatus` funkcję do wyświetlania danych wyjściowych.

W poniższych sekcjach Dodaj każdy nowy blok kodu JavaScript po poprzednim bloku.

### <a name="add-your-storage-account-info"></a>Dodawanie informacji o koncie magazynu

Dodaj kod, aby uzyskać dostęp do konta magazynu. Zamień symbol zastępczy na wygenerowany wcześniej adres URL sygnatury dostępu współdzielonego Blob service. Dodaj następujący kod na końcu pliku *index.js* .

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_StorageAcctInfo":::

Zapisz plik *index.js* .

### <a name="create-client-objects"></a>Tworzenie obiektów klienta

Utwórz obiekty [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient) i [ContainerClient](/javascript/api/@azure/storage-blob/containerclient) do współpracy z usługą Azure Blob Storage. Dodaj następujący kod na końcu pliku *index.js* .

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_CreateClientObjects":::

Zapisz plik *index.js* .

### <a name="create-and-delete-a-storage-container"></a>Tworzenie i usuwanie kontenera magazynu

Utwórz i Usuń kontener magazynu po kliknięciu odpowiedniego przycisku na stronie sieci Web. Dodaj następujący kod na końcu pliku *index.js* .

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_CreateDeleteContainer":::

Zapisz plik *index.js* .

### <a name="list-blobs"></a>Wyświetlanie listy obiektów blob

Wyświetl zawartość kontenera magazynu po kliknięciu przycisku **listy plików** . Dodaj następujący kod na końcu pliku *index.js* .

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_ListBlobs":::

Zapisz plik *index.js* .

Ten kod wywołuje funkcję [ContainerClient. listBlobsFlat](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) , a następnie używa iteratora, aby pobrać nazwę każdej zwróconej [BlobItem](/javascript/api/@azure/storage-blob/blobitem) . Dla każdego z `BlobItem` nich aktualizuje listę **plików** o wartość właściwości [Nazwa](/javascript/api/@azure/storage-blob/blobitem#name) .

### <a name="upload-blobs"></a>Przekazywanie obiektów BLOB

Przekaż pliki do kontenera magazynu po kliknięciu przycisku **Wybierz i przekaż pliki** . Dodaj następujący kod na końcu pliku *index.js* .

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_UploadBlobs":::

Zapisz plik *index.js* .

Ten kod łączy przycisk **Wybierz i przekaż pliki** do ukrytego `file-input` elementu. Zdarzenie przycisku `click` wyzwala zdarzenie wejściowe pliku `click` i wyświetla selektor plików. Po wybraniu plików i zamknięciu okna dialogowego `input` zdarzenie występuje, a `uploadFiles` Funkcja jest wywoływana. Ta funkcja tworzy obiekt [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient) , a następnie wywołuje funkcję [uploadBrowserData](/javascript/api/@azure/storage-blob/blockblobclient#uploadbrowserdata-blob---arraybuffer---arraybufferview--blockblobparalleluploadoptions-) tylko przeglądarki dla każdego wybranego pliku. Każde wywołanie zwraca obiekt `Promise` . Każda z nich `Promise` jest dodawana do listy, dzięki czemu można oczekiwać, że pliki będą jednocześnie przesyłane równolegle.

### <a name="delete-blobs"></a>Usuwać obiekty blob

Usuń pliki z kontenera magazynu po kliknięciu przycisku **Usuń wybrane pliki** . Dodaj następujący kod na końcu pliku *index.js* .

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_DeleteBlobs":::

Zapisz plik *index.js* .

Ten kod wywołuje funkcję [ContainerClient. deleteBlob](/javascript/api/@azure/storage-blob/containerclient#deleteblob-string--blobdeleteoptions-) , aby usunąć każdy plik wybrany na liście. Następnie wywołuje `listFiles` funkcję wskazaną wcześniej w celu odświeżenia zawartości listy **plików** .

## <a name="run-the-code"></a>Uruchamianie kodu

Aby uruchomić kod w debugerze Visual Studio Code, skonfiguruj *launch.jsna* pliku dla przeglądarki.

### <a name="configure-the-debugger"></a>Skonfiguruj debuger

Aby skonfigurować rozszerzenie debugera w Visual Studio Code:

1. Wybierz pozycję **uruchom > Dodaj konfigurację**
2. Wybierz pozycję **Edge**, **Chrome**lub **Firefox**, w zależności od tego, które rozszerzenie zostało zainstalowane wcześniej w sekcji [wymagania wstępne](#prerequisites) .

Dodanie nowej konfiguracji powoduje utworzenie *launch.jsw* pliku i otwarcie go w edytorze. Zmodyfikuj *launch.jsw* pliku, aby `url` wartość była `http://localhost:1234/index.html` , jak pokazano poniżej:

:::code language="json" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/.vscode/launch.json" highlight="11":::

Po uaktualnieniu Zapisz *launch.js* pliku. Ta konfiguracja informuje Visual Studio Code, która przeglądarka ma być otwarta i który adres URL do załadowania.

### <a name="launch-the-web-server"></a>Uruchom serwer sieci Web

Aby uruchomić lokalny serwer sieci Web, wybierz pozycję **wyświetl > Terminal** , aby otworzyć okno konsoli wewnątrz Visual Studio Code, a następnie wprowadź następujące polecenie.

```console
parcel index.html
```

Pakiet zbiorczy dzieli kod i uruchamia lokalny serwer programistyczny dla Twojej strony `http://localhost:1234/index.html` . Zmiany wprowadzane do *index.js* zostaną automatycznie skompilowane i odzwierciedlone na serwerze deweloperskim za każdym razem, gdy zapiszesz plik.

Jeśli zostanie wyświetlony komunikat informujący o tym, że nie można **użyć skonfigurowanego portu 1234**, można zmienić port, uruchamiając polecenie `parcel -p <port#> index.html` . W *launch.js* pliku, zaktualizuj port w ścieżce adresu URL, aby dopasować.

### <a name="start-debugging"></a>Rozpocznij debugowanie

Uruchom stronę w debugerze i zapoznaj się z tematem działania usługi BLOB Storage. Jeśli wystąpią błędy, w okienku **stan** na stronie sieci Web zostanie wyświetlony komunikat o błędzie otrzymany.

Aby otworzyć *index.html* w przeglądarce z dołączonym debugerem Visual Studio Code, wybierz pozycję **Uruchom > Rozpocznij debugowanie** lub naciśnij klawisz F5 w Visual Studio Code.

### <a name="use-the-web-app"></a>Korzystanie z aplikacji sieci Web

W [Azure Portal](https://portal.azure.com)można sprawdzić wyniki wywołań interfejsu API, wykonując poniższe kroki.

#### <a name="step-1---create-a-container"></a>Krok 1. Tworzenie kontenera

1. W aplikacji sieci Web wybierz pozycję **Utwórz kontener**. Stan wskazuje, że kontener został utworzony.
2. Aby sprawdzić w Azure Portal, wybierz konto magazynu. W obszarze **Blob service** wybierz pozycję **Kontenery**. Sprawdź, czy nowy kontener zostanie wyświetlony. (Może być konieczne wybranie opcji **Odśwież**).

#### <a name="step-2---upload-a-blob-to-the-container"></a>Krok 2. przekazywanie obiektu BLOB do kontenera

1. Na komputerze lokalnym Utwórz i Zapisz plik testowy, taki jak *test.txt*.
2. W aplikacji sieci Web kliknij pozycję **Wybierz i przekaż pliki**.
3. Przejdź do pliku testowego, a następnie wybierz pozycję **Otwórz**. Stan wskazuje, że plik został przekazany i lista plików została pobrana.
4. W Azure Portal wybierz nazwę nowego kontenera, który został utworzony wcześniej. Sprawdź, czy plik testowy jest wyświetlany.

#### <a name="step-3---delete-the-blob"></a>Krok 3. Usuwanie obiektu BLOB

1. W aplikacji sieci Web w obszarze **pliki**wybierz plik testowy.
2. Wybierz pozycję **Usuń wybrane pliki**. Stan wskazuje, że plik został usunięty i kontener nie zawiera plików.
3. W Azure Portal wybierz pozycję **Odśwież**. Sprawdź, czy **nie znaleziono obiektów BLOB**.

#### <a name="step-4---delete-the-container"></a>Krok 4. Usuwanie kontenera

1. W aplikacji sieci Web wybierz pozycję **Usuń kontener**. Stan wskazuje, że kontener został usunięty.
2. W Azure Portal wybierz ** \<account-name\> | Link kontenerów** w lewym górnym rogu okienka portalu.
3. Wybierz pozycję **Odśwież**. Nowy kontener zniknie.
4. Zamknij aplikację sieci Web.

### <a name="clean-up-resources"></a>Czyszczenie zasobów

Kliknij konsolę **terminala** w Visual Studio Code i naciśnij klawisze CTRL + C, aby zatrzymać serwer sieci Web.

Aby wyczyścić zasoby utworzone w ramach tego przewodnika Szybki Start, przejdź do [Azure Portal](https://portal.azure.com) i Usuń grupę zasobów utworzoną w sekcji [wymagania wstępne](#prerequisites) .

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób przekazywania, wyświetlania i usuwania obiektów BLOB przy użyciu języka JavaScript. Poznasz również sposób tworzenia i usuwania kontenera magazynu obiektów BLOB.

Samouczki, przykłady, Przewodniki Szybki Start i inne dokumenty można znaleźć w temacie:

> [!div class="nextstepaction"]
> [Dokumentacja platformy Azure dla języka JavaScript](/azure/developer/javascript/)

* Aby dowiedzieć się więcej, zobacz [Biblioteka klienta usługi Azure Blob Storage dla języka JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob).
* Aby wyświetlić przykładowe aplikacje magazynu obiektów blob, przejdź do [biblioteki klienta usługi Azure Blob Storage V12 Samples JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples).
