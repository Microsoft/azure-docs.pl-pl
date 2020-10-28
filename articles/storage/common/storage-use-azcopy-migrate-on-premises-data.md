---
title: 'Samouczek: Migrowanie danych lokalnych do usługi Azure Storage za pomocą AzCopy | Microsoft Docs'
description: W tym samouczku narzędzie AzCopy zostanie użyte do migracji lub kopiowania danych z lub do obiektu blob, tabeli i zawartości pliku. W łatwy sposób przeprowadź migrację danych z magazynu lokalnego do usługi Azure Storage.
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: normesta
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: 154a7b17fc09c55e83b65eef8d479904c36e87eb
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791192"
---
#  <a name="tutorial-migrate-on-premises-data-to-cloud-storage-with-azcopy"></a>Samouczek: Migrowanie danych lokalnych do magazynu w chmurze za pomocą usługi AzCopy

Narzędzie AzCopy to narzędzie wiersza polecenia służące do kopiowania danych do lub z magazynu Azure Blob Storage, Azure Files lub Azure Table przy użyciu prostych poleceń. Polecenia te zostały zaprojektowane w celu uzyskania optymalnej wydajności. Za pomocą narzędzia AzCopy można kopiować dane między systemem plików i kontem magazynu lub między kontami magazynu. Narzędzia AzCopy można użyć do skopiowania danych lokalnych do konta magazynu.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie konta magazynu 
> * Używanie narzędzia AzCopy do przekazania wszystkich danych
> * Modyfikowanie danych do celów testowych
> * Tworzenie zaplanowanego zadania lub usługi cron do identyfikowania nowych plików do przekazania

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, Pobierz najnowszą wersję programu AzCopy. Zobacz Rozpoczynanie [pracy z usługą AzCopy](storage-use-azcopy-v10.md).

W systemie Windows konieczne będzie użycie polecenia [Schtasks](/windows/win32/taskschd/schtasks), ponieważ w tym samouczku jest ono używane w celu zaplanowania zadania. Użytkownicy systemu Linux zamiast tego użyją polecenia crontab.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

## <a name="create-a-container"></a>Tworzenie kontenera

Pierwszym krokiem jest utworzenie kontenera, ponieważ obiekty blob należy zawsze przekazywać do kontenera. Kontenery są używane jako sposób organizowania grup obiektów blob w taki sposób, jak pliki w folderach na komputerze.

Wykonaj następujące kroki, aby utworzyć kontener:

1. Wybierz przycisk **Konta magazynu** ze strony głównej, a następnie wybierz utworzone konto magazynu.
2. Wybierz pozycję **Obiekty blob** w obszarze **Usługi** , a następnie wybierz pozycję **Kontener** .

   ![Zrzut ekranu przedstawiający tworzenie kontenera](media/storage-azcopy-migrate-on-premises-data/CreateContainer.png)
 
Nazwy kontenerów muszą zaczynać się literą lub cyfrą. Mogą one zawierać tylko litery, cyfry i znaki łącznika (-). Aby uzyskać dodatkowe informacje o regułach nazewnictwa kontenerów i obiektów blob, zobacz temat [Naming and Referencing Containers, Blobs, and Metadata (Nazewnictwo i odwoływanie się do kontenerów, obiektów blob i metadanych)](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

## <a name="download-azcopy"></a>Pobierz AzCopy

Pobierz plik wykonywalny AzCopy v10.

- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Umieść plik AzCopy w dowolnym miejscu na komputerze. Dodaj lokalizację pliku do zmiennej PATH systemu, aby można było odwołać się do tego pliku wykonywalnego z dowolnego folderu na komputerze.

## <a name="authenticate-with-azure-ad"></a>Uwierzytelnianie za pomocą usługi Azure AD

Najpierw Przypisz rolę [współautor danych obiektów blob magazynu](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor) do swojej tożsamości. Zobacz [używanie Azure Portal, aby przypisać rolę platformy Azure na potrzeby dostępu do danych obiektów blob i kolejek](./storage-auth-aad-rbac-portal.md).

Następnie otwórz wiersz polecenia, wpisz poniższe polecenie i naciśnij klawisz ENTER.

```azcopy
azcopy login
```

To polecenie zwraca kod uwierzytelniania i adres URL witryny sieci Web. Otwórz witrynę sieci Web, podaj kod, a następnie wybierz przycisk **dalej** .

![Zrzut ekranu przedstawiający monit logowania](media/storage-use-azcopy-v10/azcopy-login.png)

Zostanie wyświetlone okno logowania. W tym oknie Zaloguj się do konta platformy Azure przy użyciu poświadczeń konta platformy Azure. Po pomyślnym zalogowaniu możesz zamknąć okno przeglądarki i zacząć korzystać z AzCopy.

## <a name="upload-contents-of-a-folder-to-blob-storage"></a>Przekazywanie zawartości folderu do magazynu obiektów blob

Za pomocą narzędzia AzCopy możesz przekazać wszystkie pliki w folderze do magazynu obiektów blob w systemie [Windows](./storage-use-azcopy-v10.md) lub [Linux](./storage-use-azcopy-v10.md). Aby przekazać wszystkie obiekty blob w folderze, wprowadź następujące polecenie narzędzia AzCopy:

```AzCopy
azcopy copy "<local-folder-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive=true
```

* Zastąp `<local-folder-path>` symbol zastępczy ścieżką do folderu, który zawiera pliki (na przykład: `C:\myFolder` lub `/mnt/myFolder` ).

* Zastąp symbol zastępczy `<storage-account-name>` nazwą konta magazynu.

* Zastąp `<container-name>` symbol zastępczy nazwą utworzonego kontenera.

Aby przekazać zawartość określonego katalogu cyklicznie do magazynu obiektów blob, określ `--recursive` opcję. Po uruchomieniu AzCopy z tą opcją wszystkie podfoldery i ich pliki są również przekazywane.

## <a name="upload-modified-files-to-blob-storage"></a>Przekazywanie zmodyfikowanych plików do magazynu obiektów blob

Narzędzia AzCopy można użyć do przekazania plików na podstawie daty ich ostatniej modyfikacji. 

Aby z tego skorzystać, zmodyfikuj pliki lub utwórz nowe pliki w katalogu źródłowym do celów testowych. Następnie użyj `sync` polecenia AzCopy.

```AzCopy
azcopy sync "<local-folder-path>" "https://<storage-account-name>.blob.core.windows.net/<container-name>" --recursive=true
```

* Zastąp `<local-folder-path>` symbol zastępczy ścieżką do folderu, który zawiera pliki (na przykład `C:\myFolder` : lub `/mnt/myFolder` .

* Zastąp symbol zastępczy `<storage-account-name>` nazwą konta magazynu.

* Zastąp `<container-name>` symbol zastępczy nazwą utworzonego kontenera.

Aby dowiedzieć się więcej na temat tego `sync` polecenia, zobacz [Synchronizing Files](storage-use-azcopy-blobs.md#synchronize-files).

## <a name="create-a-scheduled-task"></a>Tworzenie zaplanowanego zadania

Możliwe jest utworzenie zaplanowanego zadania lub zadania cron służącego do uruchamiania skryptu polecenia narzędzia AzCopy. Skrypt identyfikuje i przekazuje nowe dane lokalne do magazynu w chmurze zgodnie z określonym interwałem.

Skopiuj polecenia narzędzia AzCopy do edytora tekstu. Zaktualizuj wartości parametrów polecenia narzędzia AzCopy na odpowiednie wartości. Zapisz plik jako `script.sh` (system Linux) lub `script.bat` (system Windows) na potrzeby narzędzia AzCopy. 

W tych przykładach przyjęto założenie, że folder ma nazwę `myFolder` , nazwa konta magazynu to `mystorageaccount` i nazwa kontenera `mycontainer` .

> [!NOTE]
> Przykład systemu Linux dołącza token SAS. Musisz podać ją w poleceniu. Bieżąca wersja programu AzCopy v10 nie obsługuje autoryzacji usługi Azure AD w zadaniach firmy cronus.

# <a name="linux"></a>[Linux](#tab/linux)

```bash
azcopy sync "/mnt/myfiles" "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-05-30T06:57:40Z&st=2019-05-29T22:57:40Z&spr=https&sig=BXHippZxxx54hQn%2F4tBY%2BE2JHGCTRv52445rtoyqgFBUo%3D" --recursive=true
```

# <a name="windows"></a>[Windows](#tab/windows)

```bash
azcopy sync "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive=true
```

---

W tym samouczku polecenie [Schtasks](/windows/win32/taskschd/schtasks) jest używane do utworzenia zaplanowanego zadania w systemie Windows. Polecenie [Crontab](http://crontab.org/) służy do tworzenia zadania cron w systemie Linux.

 Polecenie **Schtasks** umożliwia administratorowi tworzenie, usuwanie, zmienianie, uruchamianie i kończenie zaplanowanych zadań oraz wykonywanie względem nich zapytań na komputerze lokalnym lub zdalnym. Zadanie **cron** umożliwia użytkownikom systemów Linux i Unix uruchamianie poleceń lub skryptów w określonym dniu i godzinie za pomocą [wyrażeń cron](https://en.wikipedia.org/wiki/Cron#CRON_expression).

# <a name="linux"></a>[Linux](#tab/linux)

Aby utworzyć zadanie cron w systemie Linux, wprowadź następujące polecenie z poziomu terminalu:

```bash
crontab -e
*/5 * * * * sh /path/to/script.sh
```

Określenie wyrażenia cron `*/5 * * * *` w poleceniu wskazuje, że skrypt powłoki `script.sh` powinien być uruchamiany co pięć minut. Możesz zaplanować uruchamianie skryptu codziennie, co miesiąc lub co rok o określonej godzinie. Aby dowiedzieć się więcej na temat ustawiania daty i godziny wykonywania zadań, zobacz [wyrażenia cron](https://en.wikipedia.org/wiki/Cron#CRON_expression).

# <a name="windows"></a>[Windows](#tab/windows)

Aby utworzyć zaplanowane zadanie w systemie Windows, wpisz następujące polecenie w wierszu polecenia lub w programie PowerShell:

W tym przykładzie przyjęto założenie, że skrypt znajduje się na dysku głównym komputera, ale skrypt może znajdować się w dowolnym miejscu.

```cmd
schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\script.bat
```

Polecenie używa:
- Parametru `/SC` w celu określenia harmonogramu minutowego.
- Parametru `/MO` w celu określenia interwału wynoszącego 5 minut.
- Parametru `/TN` w celu określenia nazwy zadania.
- Parametru `/TR` w celu określenia ścieżki do pliku `script.bat`.

Aby dowiedzieć się więcej o tworzeniu zaplanowanego zadania w systemie Windows, zobacz [Schtasks](/previous-versions/orphan-topics/ws.10/cc772785(v=ws.10)#BKMK_minutes).

---

Aby sprawdzić, czy zaplanowane zadanie lub zadanie cron jest działa poprawnie, utwórz nowe pliki w katalogu `myFolder`. Poczekaj pięć minut, aby sprawdzić, czy nowe pliki zostały przekazane do konta magazynu. Przejdź do katalogu dzienników, aby wyświetlić dzienniki danych wyjściowych zaplanowanego zadania lub zadania cron.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o sposobach przenoszenia danych lokalnych do usługi Azure Storage i na odwrót, kliknij następujący link:

* [Przenoszenie danych do i z usługi Azure Storage](./storage-choose-data-transfer-solution.md?toc=%252fazure%252fstorage%252ffiles%252ftoc.json).  

Aby uzyskać więcej informacji na temat AzCopy, zobacz dowolny z następujących artykułów:

* [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)

* [Transferowanie danych za pomocą AzCopy i magazynu obiektów BLOB](storage-use-azcopy-blobs.md)

* [Transferowanie danych za pomocą narzędzia AzCopy i magazynu plików](storage-use-azcopy-files.md)

* [Transferowanie danych za pomocą zasobników AzCopy i Amazon S3](storage-use-azcopy-s3.md)
 
* [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z AzCopy](storage-use-azcopy-configure.md)