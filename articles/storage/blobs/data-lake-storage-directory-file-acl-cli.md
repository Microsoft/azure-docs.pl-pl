---
title: Zarządzanie danymi (Azure Data Lake Storage Gen2) za pomocą interfejsu wiersza polecenia platformy Azure
description: Użyj interfejsu wiersza polecenia platformy Azure do zarządzania katalogami i plikami na kontach magazynu, które mają hierarchiczną przestrzeń nazw.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3e9afd4617eb7445ba83948d46eef0890832e2be
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100650358"
---
# <a name="use-azure-cli-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure do zarządzania katalogami i plikami w Azure Data Lake Storage Gen2

W tym artykule pokazano, jak za pomocą [interfejsu wiersza polecenia platformy Command-Line Azure](/cli/azure/) można tworzyć katalogi i pliki oraz zarządzać nimi w ramach kont magazynu, które mają hierarchiczną przestrzeń nazw.

Aby dowiedzieć się, jak pobierać, ustawiać i aktualizować listy kontroli dostępu (ACL) katalogów i plików, zobacz [Używanie interfejsu wiersza polecenia platformy Azure do zarządzania listami ACL w Azure Data Lake Storage Gen2](data-lake-storage-acl-cli.md).

[Przykłady](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)  |  [Przekaż opinię](https://github.com/Azure/azure-cli-extensions/issues)

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

- Konto magazynu, dla którego włączono hierarchiczną przestrzeń nazw. Postępuj zgodnie z [tymi](create-data-lake-storage-account.md) instrukcjami, aby je utworzyć.

- Interfejs wiersza polecenia platformy Azure w wersji `2.6.0` lub nowszej.

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>Upewnij się, że masz zainstalowaną odpowiednią wersję interfejsu wiersza polecenia platformy Azure

1. Otwórz [Azure Cloud Shell](../../cloud-shell/overview.md)lub jeśli interfejs wiersza polecenia platformy Azure został [zainstalowany](/cli/azure/install-azure-cli) lokalnie, Otwórz aplikację konsoli poleceń, taką jak Windows PowerShell.

2. Sprawdź, czy wersja interfejsu wiersza polecenia platformy Azure z zainstalowanym systemem jest `2.6.0` lub nowsza przy użyciu następującego polecenia.

   ```azurecli
    az --version
   ```

   Jeśli wersja interfejsu wiersza polecenia platformy Azure jest niższa niż `2.6.0` , zainstaluj nowszą wersję. Zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="connect-to-the-account"></a>Połącz z kontem

1. Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, uruchom polecenie logowania.

   ```azurecli
   az login
   ```

   Jeśli interfejs wiersza polecenia może otworzyć domyślną przeglądarkę, spowoduje to załadowanie strony logowania platformy Azure.

   W przeciwnym razie Otwórz stronę przeglądarki pod adresem [https://aka.ms/devicelogin](https://aka.ms/devicelogin) i wprowadź kod autoryzacji wyświetlany w terminalu. Następnie zaloguj się przy użyciu poświadczeń konta w przeglądarce.

   Aby dowiedzieć się więcej na temat różnych metod uwierzytelniania, zobacz [Autoryzuj dostęp do danych obiektu BLOB lub kolejki za pomocą interfejsu wiersza polecenia platformy Azure](./authorize-data-operations-cli.md).

2. Jeśli Twoja tożsamość jest skojarzona z więcej niż jedną subskrypcją, ustaw aktywną subskrypcję na konto magazynu, które będzie hostować statyczną witrynę sieci Web.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Zastąp `<subscription-id>` wartość symbolu zastępczego identyfikatorem subskrypcji.

> [!NOTE]
> W przykładzie przedstawionym w tym artykule przedstawiono autoryzację Azure Active Directory (Azure AD). Aby dowiedzieć się więcej o metodach autoryzacji, zobacz [Autoryzuj dostęp do danych obiektu BLOB lub kolejki za pomocą interfejsu wiersza polecenia platformy Azure](./authorize-data-operations-cli.md).

## <a name="create-a-container"></a>Tworzenie kontenera

Kontener działa jako system plików dla plików. Można go utworzyć za pomocą `az storage fs create` polecenia. 

Ten przykład tworzy kontener o nazwie `my-file-system` .

```azurecli
az storage fs create -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-container-properties"></a>Pokaż właściwości kontenera

Właściwości kontenera można wydrukować do konsoli programu za pomocą `az storage fs show` polecenia.

```azurecli
az storage fs show -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="list-container-contents"></a>Wyświetlanie zawartości kontenera

Wyświetl zawartość katalogu za pomocą `az storage fs file list` polecenia.

Ten przykład zawiera listę zawartości kontenera o nazwie `my-file-system` .

```azurecli
az storage fs file list -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-container"></a>Usuwanie kontenera

Usuń kontener za pomocą `az storage fs delete` polecenia.

Ten przykład służy do usuwania kontenera o nazwie `my-file-system` . 

```azurecli
az storage fs delete -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="create-a-directory"></a>Tworzenie katalogu

Utwórz odwołanie do katalogu za pomocą `az storage fs directory create` polecenia. 

Ten przykład dodaje katalog o nazwie `my-directory` do kontenera o nazwie `my-file-system` , który znajduje się na koncie o nazwie `mystorageaccount` .

```azurecli
az storage fs directory create -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-directory-properties"></a>Pokaż właściwości katalogu

Właściwości katalogu można wydrukować do konsoli programu za pomocą `az storage fs directory show` polecenia.

```azurecli
az storage fs directory show -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-directory"></a>Zmienianie nazwy lub przenoszenie katalogu

Zmień nazwę lub Przenieś katalog przy użyciu `az storage fs directory move` polecenia.

Ten przykład zmienia nazwę katalogu z nazwy `my-directory` na nazwę `my-new-directory` w tym samym kontenerze.

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

Ten przykład przenosi katalog do kontenera o nazwie `my-second-file-system` .

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-second-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-directory"></a>Usuwanie katalogu

Usuń katalog przy użyciu `az storage fs directory delete` polecenia.

Ten przykład usuwa katalog o nazwie `my-directory` . 

```azurecli
az storage fs directory delete -n my-directory -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="check-if-a-directory-exists"></a>Sprawdź, czy katalog istnieje

Ustal, czy określony katalog istnieje w kontenerze za pomocą `az storage fs directory exists` polecenia.

Ten przykład pokazuje, czy katalog o nazwie `my-directory` istnieje w `my-file-system` kontenerze. 

```azurecli
az storage fs directory exists -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login 
```

## <a name="download-from-a-directory"></a>Pobierz z katalogu

Pobierz plik z katalogu za pomocą `az storage fs file download` polecenia.

Ten przykład pobiera plik o nazwie `upload.txt` z katalogu o nazwie `my-directory` . 

```azurecli
az storage fs file download -p my-directory/upload.txt -f my-file-system -d "C:\myFolder\download.txt" --account-name mystorageaccount --auth-mode login
```

## <a name="list-directory-contents"></a>Wyświetlanie zawartości katalogu

Wyświetl zawartość katalogu za pomocą `az storage fs file list` polecenia.

W tym przykładzie wymieniono zawartość katalogu o nazwie `my-directory` znajdującego się w `my-file-system` kontenerze konta magazynu o nazwie `mystorageaccount` . 

```azurecli
az storage fs file list -f my-file-system --path my-directory --account-name mystorageaccount --auth-mode login
```

## <a name="upload-a-file-to-a-directory"></a>Przekaż plik do katalogu

Przekaż plik do katalogu za pomocą `az storage fs directory upload` polecenia.

Ten przykład przekazuje plik o nazwie `upload.txt` do katalogu o nazwie `my-directory` . 

```azurecli
az storage fs file upload -s "C:\myFolder\upload.txt" -p my-directory/upload.txt  -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-file-properties"></a>Pokaż właściwości pliku

Właściwości pliku można wydrukować do konsoli programu za pomocą `az storage fs file show` polecenia.

```azurecli
az storage fs file show -p my-file.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-file"></a>Zmienianie nazwy lub przenoszenie pliku

Zmień nazwę lub Przenieś plik za pomocą `az storage fs file move` polecenia.

Ten przykład zmienia nazwę pliku z nazwy `my-file.txt` na nazwę `my-file-renamed.txt` .

```azurecli
az storage fs file move -p my-file.txt -f my-file-system --new-path my-file-system/my-file-renamed.txt --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-file"></a>Usuwanie pliku

Usuń plik za pomocą `az storage fs file delete` polecenia.

Ten przykład usuwa plik o nazwie `my-file.txt`

```azurecli
az storage fs file delete -p my-directory/my-file.txt -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="see-also"></a>Zobacz też

- [Samples](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)
- [Prześlij opinię](https://github.com/Azure/azure-cli-extensions/issues)
- [Znane problemy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Używanie interfejsu wiersza polecenia platformy Azure do zarządzania listami ACL w Azure Data Lake Storage Gen2](data-lake-storage-acl-cli.md)