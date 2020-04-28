---
title: Szybki Start — Tworzenie obiektu BLOB za pomocą interfejsu wiersza polecenia platformy Azure
titleSuffix: Azure Storage
description: W tym przewodniku szybki start dowiesz się, jak za pomocą interfejsu wiersza polecenia platformy Azure przekazać obiekt BLOB do usługi Azure Storage, pobrać obiekt BLOB i wyświetlić listę obiektów BLOB w kontenerze.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 04/23/2020
ms.author: tamram
ms.openlocfilehash: 333d9f12ff817a5264183666cd1b858075a93077
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "82176689"
---
# <a name="quickstart-create-download-and-list-blobs-with-azure-cli"></a>Szybki Start: Tworzenie, pobieranie i wyświetlanie listy obiektów BLOB za pomocą interfejsu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure to środowisko wiersza polecenia platformy Azure do zarządzania jej zasobami. Można używać go w przeglądarce za pośrednictwem usługi Azure Cloud Shell. Istnieje także możliwość zainstalowania go w systemach macOS, Linux lub Windows, a następnie uruchomienia z poziomu wiersza polecenia. Ten przewodnik Szybki start zawiera opis użycia interfejsu wiersza polecenia platformy Azure do przekazywania danych do/pobierania danych z usługi Azure Blob Storage.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="install-the-azure-cli-locally"></a>Instalowanie interfejsu wiersza polecenia platformy Azure lokalnie

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia platformy Azure i korzystać z niego lokalnie, ten przewodnik Szybki Start będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.46 lub nowszej. Uruchom polecenie `az --version`, aby sprawdzić wersję. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, musisz się zalogować i uwierzytelnić. Ten krok nie jest konieczny, jeśli używasz Azure Cloud Shell. Aby zalogować się do interfejsu wiersza polecenia `az login` platformy Azure, Uruchom i Uwierzytelnij w oknie przeglądarki:

```azurecli
az login
```

Aby uzyskać więcej informacji o uwierzytelnianiu za pomocą interfejsu wiersza polecenia platformy Azure, zobacz [Logowanie za pomocą interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli).

## <a name="authorize-access-to-blob-storage"></a>Autoryzuj dostęp do magazynu obiektów BLOB

Dostęp do magazynu obiektów BLOB można autoryzować z poziomu interfejsu wiersza polecenia platformy Azure przy użyciu poświadczeń usługi Azure AD lub klucza dostępu do konta magazynu. Zalecane jest korzystanie z poświadczeń usługi Azure AD. W tym artykule przedstawiono sposób autoryzacji operacji usługi BLOB Storage przy użyciu usług Azure AD.

Polecenie interfejsu wiersza polecenia platformy Azure dla operacji na danych w `--auth-mode` usłudze BLOB Storage obsługuje parametr, który umożliwia określenie sposobu autoryzacji danej operacji. Ustaw `--auth-mode` parametr na `login` , aby autoryzować się przy użyciu poświadczeń usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Autoryzuj dostęp do danych obiektu BLOB lub kolejki za pomocą interfejsu wiersza polecenia platformy Azure](../common/authorize-data-operations-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Tylko operacje na danych magazynu obiektów BLOB `--auth-mode` obsługują ten parametr. Operacje zarządzania, takie jak tworzenie grupy zasobów lub konta magazynu, automatycznie używają poświadczeń usługi Azure AD do autoryzacji.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupę zasobów platformy Azure można utworzyć za pomocą polecenia [az group create](/cli/azure/group). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach ostrych własnymi wartościami:

```azurecli
az group create \
    --name <resource-group> \
    --location <location>
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Do utworzenia konta magazynu (ogólnego przeznaczenia) służy polecenie [az storage account create](/cli/azure/storage/account). Konta magazynu można używać z wszystkimi czterema usługami: obiektami blob, plikami, tabelami i kolejkami.

Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach ostrych własnymi wartościami:

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_ZRS \
    --encryption-services blob
```

## <a name="create-a-container"></a>Tworzenie kontenera

Obiekty blob są zawsze przesyłane do kontenera. Grupy obiektów BLOB można organizować w kontenerach podobnie jak w przypadku organizowania plików na komputerze w folderach.

Do tworzenia kontenera do przechowywania obiektów blob służy polecenie [az storage container create](/cli/azure/storage/container). Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach ostrych własnymi wartościami:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name <container> \
    --auth-mode login
```

## <a name="upload-a-blob"></a>Przesyłanie obiektów blob

Usługa Blob Storage obsługuje blokowe, uzupełnialne i stronicowe obiekty blob. Przykłady w tym przewodniku szybki start pokazują, jak korzystać z blokowych obiektów BLOB.

Najpierw utwórz plik do przekazania do blokowego obiektu BLOB. Jeśli używasz Azure Cloud Shell, użyj następującego polecenia, aby utworzyć plik:

```bash
vi helloworld
```

Gdy plik zostanie otwarty, naciśnij klawisz **INSERT**. Wpisz *Hello World*, a następnie naciśnij klawisz **ESC**. Następnie wpisz *: x*, a następnie naciśnij klawisz **Enter**.

W tym przykładzie do kontenera utworzonego w ostatnim kroku zostanie przekazany obiekt blob za pomocą polecenia [az storage blob upload](/cli/azure/storage/blob). Nie trzeba określać ścieżki pliku, ponieważ plik został utworzony w katalogu głównym. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach ostrych własnymi wartościami:

```azurecli
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --name helloworld \
    --file helloworld \
    --auth-mode login
```

Jeśli obiekt blob nie istnieje, zostanie utworzony. W przeciwnym razie zostanie zastąpiony. Przed kontynuowaniem można przesłać dowolną liczbę plików.

Aby przekazać jednocześnie wiele plików, możesz użyć polecenia [az storage blob upload-batch](/cli/azure/storage/blob).

## <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze

Do wyświetlania listy obiektów blob w kontenerze służy polecenie [az storage blob list](/cli/azure/storage/blob). Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach ostrych własnymi wartościami:

```azurecli
az storage blob list \
    --account-name <storage-account> \
    --container-name <container> \
    --output table \
    --auth-mode login
```

## <a name="download-a-blob"></a>Pobieranie obiektu blob

Użyj polecenia [az storage blob download](/cli/azure/storage/blob), aby pobrać przesłany wcześniej obiekt blob. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach ostrych własnymi wartościami:

```azurecli
az storage blob download \
    --account-name <storage-account> \
    --container-name <container> \
    --name helloworld \
    --file ~/destination/path/for/file \
    --auth-mode login
```

## <a name="data-transfer-with-azcopy"></a>Transfer danych przy użyciu narzędzia AzCopy

Narzędzie wiersza polecenia AzCopy oferuje wysoce wydajny, oparty na skryptach transfer danych dla usługi Azure Storage. Można użyć AzCopy do transferowania danych do i z usługi BLOB Storage i Azure Files. Aby uzyskać więcej informacji na temat AzCopy V10, najnowszej wersji programu AzCopy, zobacz [wprowadzenie do AzCopy](../common/storage-use-azcopy-v10.md). Aby dowiedzieć się więcej o korzystaniu z usługi AzCopy V10 z usługą BLOB Storage, zobacz [transfer danych za pomocą AzCopy i BLOB Storage](../common/storage-use-azcopy-blobs.md).

Poniższy przykład używa AzCopy do przekazania pliku lokalnego do obiektu BLOB. Pamiętaj, aby zastąpić przykładowe wartości własnymi wartościami:

```bash
azcopy login
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz usunąć zasoby utworzone w ramach tego przewodnika Szybki Start, w tym konto magazynu, Usuń grupę zasobów za pomocą polecenia [AZ Group Delete](/cli/azure/group) . Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach ostrych własnymi wartościami:

```azurecli
az group delete \
    --name <resource-group> \
    --no-wait
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób transferu plików między lokalnym systemem plików i kontenerem w usłudze Azure Blob Storage. Aby dowiedzieć się więcej na temat pracy z usługą BLOB Storage za pomocą interfejsu wiersza polecenia platformy Azure, zobacz przykłady interfejsu wiersza polecenia platformy Azure dla usługi BLOB Storage.

> [!div class="nextstepaction"]
> [Przykłady interfejsu wiersza polecenia platformy Azure dla usługi BLOB Storage](/azure/storage/blobs/storage-samples-blobs-cli?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
