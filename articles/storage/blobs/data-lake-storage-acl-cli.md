---
title: Używanie interfejsu wiersza polecenia platformy Azure do ustawiania list kontroli dostępu w Azure Data Lake Storage Gen2
description: Użyj interfejsu wiersza polecenia platformy Azure, aby zarządzać listami kontroli dostępu (ACL) na kontach magazynu, które mają hierarchiczną przestrzeń nazw.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5ec7d2b243a5eadab2d22dea14ebeac8eabb1722
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563168"
---
# <a name="use-azure-cli-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Używanie interfejsu wiersza polecenia platformy Azure do zarządzania listami ACL w Azure Data Lake Storage Gen2

W tym artykule pokazano, jak za pomocą [interfejsu wiersza polecenia platformy Command-Line Azure](/cli/azure/) można pobrać, ustawić i zaktualizować listy kontroli dostępu do katalogów i plików.

Dziedziczenie listy ACL jest już dostępne dla nowych elementów podrzędnych, które są tworzone w katalogu nadrzędnym. Można również dodawać, aktualizować i usuwać listy ACL cyklicznie na istniejących elementach podrzędnych katalogu nadrzędnego bez konieczności wprowadzania tych zmian indywidualnie dla każdego elementu podrzędnego.

[Dokumentacja](/cli/azure/storage/fs/access)  |  [Przykłady](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)  |  [Przekaż opinię](https://github.com/Azure/azure-cli-extensions/issues)

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

- Konto magazynu, dla którego włączono hierarchiczną przestrzeń nazw. Postępuj zgodnie z [tymi](create-data-lake-storage-account.md) instrukcjami, aby je utworzyć.

- Interfejs wiersza polecenia platformy Azure w wersji `2.14.0` lub nowszej.

- Jedno z następujących uprawnień zabezpieczeń:

  - Przydzielona jednostka [zabezpieczeń](../../role-based-access-control/overview.md#security-principal) Azure Active Directory (Azure AD), do której przypisano rolę [właściciela danych obiektów blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) w zakresie kontenera docelowego, nadrzędnej grupy zasobów lub subskrypcji.  

  - Użytkownik będący właścicielem docelowego kontenera lub katalogu, do którego planujesz zastosować ustawienia listy ACL. Aby ustawić listy ACL cyklicznie, obejmuje to wszystkie elementy podrzędne w kontenerze lub katalogu docelowym.
  
  - Klucz konta magazynu.

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>Upewnij się, że masz zainstalowaną odpowiednią wersję interfejsu wiersza polecenia platformy Azure

1. Otwórz [Azure Cloud Shell](../../cloud-shell/overview.md)lub jeśli interfejs wiersza polecenia platformy Azure został [zainstalowany](/cli/azure/install-azure-cli) lokalnie, Otwórz aplikację konsoli poleceń, taką jak Windows PowerShell.

2. Sprawdź, czy wersja interfejsu wiersza polecenia platformy Azure z zainstalowanym systemem jest `2.14.0` lub nowsza przy użyciu następującego polecenia.

   ```azurecli
    az --version
   ```

   Jeśli wersja interfejsu wiersza polecenia platformy Azure jest niższa niż `2.14.0` , zainstaluj nowszą wersję. Zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

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

## <a name="get-acls"></a>Pobierz listy ACL

Pobierz listę kontroli dostępu do **katalogu** za pomocą `az storage fs access show` polecenia.

Ten przykład pobiera listę ACL katalogu, a następnie drukuje listę kontroli dostępu do konsoli programu.

```azurecli
az storage fs access show -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Uzyskaj uprawnienia dostępu do **pliku** za pomocą `az storage fs access show` polecenia. 

Ten przykład pobiera listę ACL pliku, a następnie drukuje listę kontroli dostępu do konsoli programu.

```azurecli
az storage fs access show -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

Na poniższej ilustracji przedstawiono dane wyjściowe po uzyskaniu listy ACL katalogu.

![Pobieranie danych wyjściowych listy ACL](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

W tym przykładzie użytkownik będący właścicielem ma uprawnienia do odczytu, zapisu i wykonania. Grupa będąca właścicielem ma tylko uprawnienia do odczytu i wykonywania. Aby uzyskać więcej informacji na temat list kontroli dostępu, zobacz [Kontrola dostępu w Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

## <a name="set-acls"></a>Ustawianie list ACL

Po *ustawieniu* listy ACL **zastąpi** całą listę kontroli dostępu, w tym wszystkie jej wpisy. Jeśli chcesz zmienić poziom uprawnień podmiotu zabezpieczeń lub dodać nowego podmiot zabezpieczeń do listy kontroli dostępu bez wpływu na inne istniejące wpisy, należy zamiast tego *zaktualizować* listę kontroli dostępu. Aby zaktualizować listę ACL zamiast zastępować, zobacz sekcję [listy ACL aktualizacji](#update-acls) w tym artykule.  

Jeśli zdecydujesz się *ustawić* listę kontroli dostępu, musisz dodać wpis dla użytkownika będącego właścicielem, wpis dla grupy będącej właścicielem i wpis dla wszystkich innych użytkowników. Aby dowiedzieć się więcej na temat użytkownika będącego właścicielem, grupy będącej właścicielem i wszystkich innych użytkowników, zobacz [Użytkownicy i tożsamości](data-lake-storage-access-control.md#users-and-identities).

W tej sekcji pokazano, jak:

- Ustawianie listy ACL
- Cykliczne ustawianie list kontroli dostępu

### <a name="set-an-acl"></a>Ustawianie listy ACL

Użyj `az storage fs access set` polecenia, aby ustawić listę kontroli dostępu dla **katalogu**. 

Ten przykład ustawia listę ACL dla katalogu dla użytkownika będącego właścicielem, grupy będącej właścicielem lub innych użytkowników, a następnie drukuje listę kontroli dostępu do konsoli programu.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Ten przykład ustawia *domyślną* listę ACL dla katalogu dla użytkownika będącego właścicielem, grupy będącej właścicielem lub innych użytkowników, a następnie drukuje listę kontroli dostępu do konsoli programu.

```azurecli
az storage fs access set --acl "default:user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Użyj `az storage fs access set` polecenia, aby ustawić listę kontroli dostępu do **pliku**. 

Ten przykład ustawia listę ACL dla pliku dla użytkownika będącego właścicielem, grupy będącej właścicielem lub innych użytkowników, a następnie drukuje listę kontroli dostępu do konsoli programu.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Aby ustawić listę kontroli dostępu dla określonej grupy lub użytkownika, należy użyć odpowiednich identyfikatorów obiektów. Na przykład: `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` lub `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

Na poniższej ilustracji przedstawiono dane wyjściowe po ustawieniu listy ACL pliku.

![Pobierz dane wyjściowe listy ACL 2](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

W tym przykładzie użytkownik będący właścicielem i grupa będąca właścicielem mają tylko uprawnienia do odczytu i zapisu. Wszyscy inni użytkownicy mają uprawnienia do zapisu i wykonywania. Aby uzyskać więcej informacji na temat list kontroli dostępu, zobacz [Kontrola dostępu w Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-acls-recursively"></a>Cykliczne ustawianie list kontroli dostępu

Ustawianie list ACL cyklicznie przy użyciu polecenia [AZ Storage FS Access Set-rekursywnego](/cli/azure/storage/fs/access#az_storage_fs_access_set_recursive) .

Ten przykład ustawia listę ACL katalogu o nazwie `my-parent-directory` . Te wpisy dają użytkownikowi właściciela uprawnienia do odczytu, zapisu i wykonywania, dają członkom należącym do grupy uprawnienia Odczyt i wykonywanie, a inni nie mają dostępu. Ostatni wpis listy ACL w tym przykładzie daje określonemu użytkownikowi IDENTYFIKATORowi "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" uprawnień do odczytu i wykonywania.

```azurecli
az storage fs access set-recursive --acl "user::rwx,group::r-x,other::---,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Jeśli chcesz ustawić **domyślny** wpis listy kontroli dostępu, Dodaj prefiks `default:` do każdego wpisu. Na przykład: `default:user::rwx` lub `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`.

## <a name="update-acls"></a>Aktualizowanie list ACL

Podczas *aktualizowania* listy kontroli dostępu należy zmodyfikować listę ACL zamiast zastępować listę kontroli dostępu. Na przykład można dodać nowego podmiot zabezpieczeń do listy kontroli dostępu bez wpływu na inne podmioty zabezpieczeń wymienione na liście ACL.  Aby zastąpić listę ACL zamiast aktualizacji, zobacz sekcję [Ustawianie list ACL](#set-acls) w tym artykule.

Aby zaktualizować listę kontroli dostępu, Utwórz nowy obiekt listy ACL z wpisem listy ACL, który chcesz zaktualizować, a następnie użyj tego obiektu w operacji aktualizowania listy ACL. Nie pobieraj istniejącej listy kontroli dostępu, po prostu podaj wpisy listy ACL do zaktualizowania.

W tej sekcji pokazano, jak:

- Aktualizowanie listy ACL
- Aktualizuje rekursywnie listy ACL

### <a name="update-an-acl"></a>Aktualizowanie listy ACL

Innym sposobem ustawienia tego uprawnienia jest użycie `az storage fs access set` polecenia. 

Aktualizowanie listy ACL katalogu lub pliku przez ustawienie `-permissions` parametru na krótką postać listy ACL.

Ten przykład aktualizuje listę ACL **katalogu**.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Ten przykład aktualizuje listę ACL **pliku**.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Aby zaktualizować listę kontroli dostępu dla określonej grupy lub użytkownika, należy użyć odpowiednich identyfikatorów obiektów. Na przykład: `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` lub `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

Możesz również zaktualizować użytkownika i grupę będącą właścicielem katalogu lub pliku, ustawiając `--owner` `group` parametry lub dla identyfikatora jednostki lub głównej nazwy użytkownika (UPN) użytkownika.

Ten przykład zmienia właściciela katalogu.

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Ten przykład zmienia właściciela pliku. 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login

```

### <a name="update-acls-recursively"></a>Aktualizuje rekursywnie listy ACL

Aktualizowanie list ACL rekursywnie za pomocą polecenia  [AZ Storage FS Access Update-rekursywnego](/cli/azure/storage/fs/access#az_storage_fs_access_update_recursive) .

Ten przykład aktualizuje wpis listy ACL z uprawnieniami do zapisu.

```azurecli
az storage fs access update-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Jeśli chcesz zaktualizować **domyślny** wpis listy kontroli dostępu, Dodaj prefiks `default:` do każdego wpisu. Na przykład `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`.

## <a name="remove-acl-entries-recursively"></a>Usuń rekursywnie wpisów listy ACL

Co najmniej jeden wpis listy ACL można usunąć cyklicznie. Aby usunąć wpis listy ACL, Utwórz nowy obiekt ACL dla wpisu listy ACL, który ma zostać usunięty, a następnie użyj tego obiektu w operacji usuwania listy ACL. Nie pobieraj istniejącej listy kontroli dostępu, po prostu podaj wpisy listy ACL do usunięcia.

Usuń wpisy listy ACL za pomocą polecenia [AZ Storage FS Access Remove-rekursywny](/cli/azure/storage/fs/access#az_storage_fs_access_remove_recursive) .

Ten przykład usuwa wpis listy ACL z katalogu głównego kontenera.  

```azurecli
az storage fs access remove-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Jeśli chcesz usunąć **domyślny** wpis listy kontroli dostępu, Dodaj prefiks `default:` do każdego wpisu. Na przykład `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

## <a name="recover-from-failures"></a>Odzyskiwanie po awarii

Podczas cyklicznego modyfikowania list ACL mogą wystąpić błędy środowiska uruchomieniowego lub uprawnień. W przypadku błędów środowiska uruchomieniowego należy ponownie uruchomić proces od początku. Błędy uprawnień mogą wystąpić, jeśli podmiot zabezpieczeń nie ma wystarczających uprawnień do modyfikowania listy ACL katalogu lub pliku znajdującego się w modyfikowanej hierarchii katalogów. Rozwiąż problem z uprawnieniami, a następnie wybierz wznowienie procesu od punktu awarii przy użyciu tokenu kontynuacji lub ponownie uruchom proces od początku. Nie musisz używać tokenu kontynuacji, jeśli wolisz uruchomić ponownie od początku. Możesz ponownie zastosować wpisy ACL bez żadnego negatywnego wpływu.

W przypadku awarii można zwrócić token kontynuacji przez ustawienie `--continue-on-failure` parametru na `false` . Po rozpoczęciu błędów można wznowić proces od momentu awarii, ponownie uruchamiając polecenie, a następnie ustawiając `--continuation` parametr na token kontynuacji.

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure false --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

Jeśli chcesz, aby proces został zakończony nieprzerwanie przez błędy uprawnień, możesz określić, czy.

Aby upewnić się, że proces kończy się nieprzerwanie, ustaw `--continue-on-failure` parametr na `true` .

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure true --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Zobacz też

- [Samples](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)
- [Przekaż opinię](https://github.com/Azure/azure-cli-extensions/issues)
- [Znane problemy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Model kontroli dostępu w Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Listy kontroli dostępu (ACL) w Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)