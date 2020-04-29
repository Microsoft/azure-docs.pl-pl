---
title: Korzystanie z interfejsu wiersza polecenia platformy Azure dla plików & list ACL w Azure Data Lake Storage Gen2 (wersja zapoznawcza)
description: Użyj interfejsu wiersza polecenia platformy Azure do zarządzania katalogami oraz list kontroli dostępu do plików i katalogów (ACL) na kontach magazynu, które mają hierarchiczną przestrzeń nazw.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 11/24/2019
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: ce2b4200496938e6cffb935207df8c7027eaf37a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77486138"
---
# <a name="use-azure-cli-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure do zarządzania katalogami, plikami i listami ACL w Azure Data Lake Storage Gen2 (wersja zapoznawcza)

W tym artykule pokazano, jak używać [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) do tworzenia katalogów, plików i uprawnień w ramach kont magazynu, które mają hierarchiczną przestrzeń nazw, oraz zarządzania nimi. 

> [!IMPORTANT]
> `storage-preview` Rozszerzenie, które jest proponowane w tym artykule, jest obecnie dostępne w publicznej wersji zapoznawczej.

[Przykładowe](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#adls-gen2-support) |  | [Mapowanie Gen1 do Gen2](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)[Przekaż opinię](https://github.com/Azure/azure-cli-extensions/issues)
## <a name="prerequisites"></a>Wymagania wstępne

> [!div class="checklist"]
> * Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Konto magazynu z włączoną hierarchiczną przestrzenią nazw (SNS). Postępuj zgodnie z [tymi](data-lake-storage-quickstart-create-account.md) instrukcjami, aby je utworzyć.
> * Interfejs wiersza polecenia `2.0.67` platformy Azure w wersji lub nowszej.

## <a name="install-the-storage-cli-extension"></a>Instalowanie rozszerzenia interfejsu wiersza polecenia magazynu

1. Otwórz [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)lub jeśli interfejs wiersza polecenia platformy Azure został [zainstalowany](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) lokalnie, Otwórz aplikację konsoli poleceń, taką jak Windows PowerShell.

2. Sprawdź, czy wersja interfejsu wiersza polecenia platformy Azure z zainstalowanym systemem jest `2.0.67` lub nowsza przy użyciu następującego polecenia.

   ```azurecli
    az --version
   ```
   Jeśli wersja interfejsu wiersza polecenia platformy Azure jest niższa `2.0.67`niż, zainstaluj nowszą wersję. Zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

3. Należy zainstalować rozszerzenie `storage-preview`.

   ```azurecli
   az extension add -n storage-preview
   ```

## <a name="connect-to-the-account"></a>Połącz z kontem

1. Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, uruchom polecenie logowania.

   ```azurecli
   az login
   ```

   Jeśli interfejs wiersza polecenia może otworzyć domyślną przeglądarkę, spowoduje to załadowanie strony logowania platformy Azure.

   W przeciwnym razie Otwórz stronę przeglądarki pod [https://aka.ms/devicelogin](https://aka.ms/devicelogin) adresem i wprowadź kod autoryzacji wyświetlany w terminalu. Następnie zaloguj się przy użyciu poświadczeń konta w przeglądarce.

   Aby dowiedzieć się więcej na temat różnych metod uwierzytelniania, zobacz Logowanie się za pomocą interfejsu wiersza polecenia platformy Azure.

2. Jeśli Twoja tożsamość jest skojarzona z więcej niż jedną subskrypcją, ustaw aktywną subskrypcję na konto magazynu, które będzie hostować statyczną witrynę sieci Web.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Zastąp `<subscription-id>` wartość symbolu ZASTĘPCZego identyfikatorem subskrypcji.

## <a name="create-a-file-system"></a>Tworzenie systemu plików

System plików działa jako kontener dla plików. Można go utworzyć za pomocą `az storage container create` polecenia. 

Ten przykład tworzy system plików o nazwie `my-file-system`.

```azurecli
az storage container create --name my-file-system --account-name mystorageaccount
```

## <a name="create-a-directory"></a>Tworzenie katalogu

Utwórz odwołanie do katalogu za pomocą `az storage blob directory create` polecenia. 

Ten przykład dodaje katalog o nazwie `my-directory` do systemu plików o nazwie `my-file-system` znajdującego się na koncie o nazwie `mystorageaccount`.

```azurecli
az storage blob directory create -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="show-directory-properties"></a>Pokaż właściwości katalogu

Właściwości katalogu można wydrukować do konsoli programu za pomocą `az storage blob show` polecenia.

```azurecli
az storage blob directory show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="rename-or-move-a-directory"></a>Zmienianie nazwy lub przenoszenie katalogu

Zmień nazwę lub Przenieś katalog przy użyciu `az storage blob directory move` polecenia.

Ten przykład zmienia nazwę katalogu z nazwy `my-directory` na nazwę. `my-new-directory`

```azurecli
az storage blob directory move -c my-file-system -d my-new-directory -s my-directory --account-name mystorageaccount
```

## <a name="delete-a-directory"></a>Usuwanie katalogu

Usuń katalog przy użyciu `az storage blob directory delete` polecenia.

Ten przykład usuwa katalog o nazwie `my-directory`. 

```azurecli
az storage blob directory delete -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="check-if-a-directory-exists"></a>Sprawdź, czy katalog istnieje

Ustal, `az storage blob directory exist` czy określony katalog istnieje w systemie plików przy użyciu polecenia.

Ten przykład pokazuje, czy katalog o `my-directory` nazwie istnieje w `my-file-system` systemie plików. 

```azurecli
az storage blob directory exists -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="download-from-a-directory"></a>Pobierz z katalogu

Pobierz plik z katalogu za pomocą `az storage blob directory download` polecenia.

Ten przykład pobiera plik o nazwie `upload.txt` z katalogu o nazwie `my-directory`. 

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/upload.txt" -d "C:\mylocalfolder\download.txt"
```

Ten przykład pobiera cały katalog.

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/" -d "C:\mylocalfolder" --recursive
```

## <a name="list-directory-contents"></a>Wyświetlanie zawartości katalogu

Wyświetl zawartość katalogu za pomocą `az storage blob directory list` polecenia.

W tym przykładzie wymieniono zawartość katalogu o nazwie `my-directory` znajdującego się w systemie `my-file-system` plików konta magazynu o nazwie `mystorageaccount`. 

```azurecli
az storage blob directory list -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="upload-a-file-to-a-directory"></a>Przekaż plik do katalogu

Przekaż plik do katalogu za pomocą `az storage blob directory upload` polecenia.

Ten przykład przekazuje plik o nazwie `upload.txt` do katalogu o nazwie. `my-directory` 

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\upload.txt" -d my-directory
```

Ten przykład przekazuje cały katalog.

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\" -d my-directory --recursive 
```

## <a name="show-file-properties"></a>Pokaż właściwości pliku

Właściwości pliku można wydrukować do konsoli programu za pomocą `az storage blob show` polecenia.

```azurecli
az storage blob show -c my-file-system -b my-file.txt --account-name mystorageaccount
```

## <a name="rename-or-move-a-file"></a>Zmienianie nazwy lub przenoszenie pliku

Zmień nazwę lub Przenieś plik za pomocą `az storage blob move` polecenia.

Ten przykład zmienia nazwę pliku z nazwy `my-file.txt` na nazwę. `my-file-renamed.txt`

```azurecli
az storage blob move -c my-file-system -d my-file-renamed.txt -s my-file.txt --account-name mystorageaccount
```

## <a name="delete-a-file"></a>Usuwanie pliku

Usuń plik za pomocą `az storage blob delete` polecenia.

Ten przykład usuwa plik o nazwie`my-file.txt`

```azurecli
az storage blob delete -c my-file-system -b my-file.txt --account-name mystorageaccount 
```

## <a name="manage-permissions"></a>Zarządzaj uprawnieniami

Uprawnienia dostępu do katalogów i plików można uzyskiwać, ustawiać i aktualizować.

> [!NOTE]
> Jeśli używasz Azure Active Directory (Azure AD) do autoryzacji poleceń, upewnij się, że podmiot zabezpieczeń ma przypisaną [rolę właściciela danych obiektu blob magazynu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Aby dowiedzieć się więcej na temat sposobu stosowania uprawnień ACL i skutków ich zmiany, zobacz [Kontrola dostępu w Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

### <a name="get-directory-and-file-permissions"></a>Uzyskiwanie uprawnień do katalogów i plików

Pobierz listę kontroli dostępu do **katalogu** za pomocą `az storage blob directory access show` polecenia.

Ten przykład pobiera listę ACL katalogu, a następnie drukuje listę kontroli dostępu do konsoli programu.

```azurecli
az storage blob directory access show -d my-directory -c my-file-system --account-name mystorageaccount
```

Uzyskaj uprawnienia dostępu do **pliku** za pomocą `az storage blob access show` polecenia. 

Ten przykład pobiera listę ACL pliku, a następnie drukuje listę kontroli dostępu do konsoli programu.

```azurecli
az storage blob access show -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

Na poniższej ilustracji przedstawiono dane wyjściowe po uzyskaniu listy ACL katalogu.

![Pobieranie danych wyjściowych listy ACL](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

W tym przykładzie użytkownik będący właścicielem ma uprawnienia do odczytu, zapisu i wykonania. Grupa będąca właścicielem ma tylko uprawnienia do odczytu i wykonywania. Aby uzyskać więcej informacji na temat list kontroli dostępu, zobacz [Kontrola dostępu w Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-directory-and-file-permissions"></a>Ustawianie uprawnień do katalogów i plików

Użyj `az storage blob directory access set` polecenia, aby ustawić listę kontroli dostępu dla **katalogu**. 

Ten przykład ustawia listę ACL dla katalogu dla użytkownika będącego właścicielem, grupy będącej właścicielem lub innych użytkowników, a następnie drukuje listę kontroli dostępu do konsoli programu.

```azurecli
az storage blob directory access set -a "user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

Ten przykład ustawia *domyślną* listę ACL dla katalogu dla użytkownika będącego właścicielem, grupy będącej właścicielem lub innych użytkowników, a następnie drukuje listę kontroli dostępu do konsoli programu.

```azurecli
az storage blob directory access set -a "default:user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

Użyj `az storage blob access set` polecenia, aby ustawić listę kontroli dostępu do **pliku**. 

Ten przykład ustawia listę ACL dla pliku dla użytkownika będącego właścicielem, grupy będącej właścicielem lub innych użytkowników, a następnie drukuje listę kontroli dostępu do konsoli programu.

```azurecli
az storage blob access set -a "user::rw-,group::rw-,other::-wx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
Na poniższej ilustracji przedstawiono dane wyjściowe po ustawieniu listy ACL pliku.

![Pobieranie danych wyjściowych listy ACL](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

W tym przykładzie użytkownik będący właścicielem i grupa będąca właścicielem mają tylko uprawnienia do odczytu i zapisu. Wszyscy inni użytkownicy mają uprawnienia do zapisu i wykonywania. Aby uzyskać więcej informacji na temat list kontroli dostępu, zobacz [Kontrola dostępu w Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="update-directory-and-file-permissions"></a>Aktualizowanie uprawnień do katalogów i plików

Innym sposobem ustawienia tego uprawnienia jest użycie polecenia `az storage blob directory access update` lub. `az storage blob access update` 

Aktualizowanie listy ACL katalogu lub pliku przez ustawienie `-permissions` parametru na krótką postać listy ACL.

Ten przykład aktualizuje listę ACL **katalogu**.

```azurecli
az storage blob directory access update --permissions "rwxrwxrwx" -d my-directory -c my-file-system --account-name mystorageaccount
```

Ten przykład aktualizuje listę ACL **pliku**.

```azurecli
az storage blob access update --permissions "rwxrwxrwx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

Możesz również zaktualizować użytkownika i grupę będącą właścicielem katalogu lub pliku, ustawiając parametry `--owner` lub `group` dla identyfikatora jednostki lub głównej nazwy użytkownika (UPN) użytkownika. 

Ten przykład zmienia właściciela katalogu. 

```azurecli
az storage blob directory access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -d my-directory -c my-file-system --account-name mystorageaccount
```

Ten przykład zmienia właściciela pliku. 

```azurecli
az storage blob access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
## <a name="manage-user-defined-metadata"></a>Zarządzanie metadanymi zdefiniowanymi przez użytkownika

Możesz dodać metadane zdefiniowane przez użytkownika do pliku lub katalogu przy użyciu `az storage blob directory metadata update` polecenia z jedną lub większą liczbą par nazwa-wartość.

W tym przykładzie dodano metadane zdefiniowane przez użytkownika dla katalogu o `my-directory` nazwie katalog.

```azurecli
az storage blob directory metadata update --metadata tag1=value1 tag2=value2 -c my-file-system -d my-directory --account-name mystorageaccount
```

Ten przykład przedstawia wszystkie metadane zdefiniowane przez użytkownika dla katalogu o `my-directory`nazwie.

```azurecli
az storage blob directory metadata show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="see-also"></a>Zobacz także

* [Przykład](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview)
* [Mapowanie Gen1 do Gen2](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)
* [Przesyłanie opinii](https://github.com/Azure/azure-cli-extensions/issues)
* [Znane problemy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Kod źródłowy](https://github.com/Azure/azure-cli-extensions/tree/master/src)

