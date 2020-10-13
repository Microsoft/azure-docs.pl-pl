---
title: Jak wdrożyć Azure Files | Microsoft Docs
description: Dowiedz się, jak wdrożyć Azure Files od początku do końca. Przenieś dane do Azure Files. Zainstaluj automatycznie na wymaganych komputerach lub serwerach.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/22/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 08bcb41dd8d9f4643b03d855960d8632b778ff84
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88034502"
---
# <a name="how-to-deploy-azure-files"></a>Jak wdrożyć usługę Pliki Azure
[Azure Files](storage-files-introduction.md) oferuje w pełni zarządzane udziały plików w chmurze, które są dostępne za pośrednictwem standardowego protokołu SMB. W tym artykule przedstawiono, jak praktycznie wdrożyć Azure Files w organizacji.

Zdecydowanie zalecamy zapoznanie się z [planowaniem wdrożenia Azure Files](storage-files-planning.md) przed wykonaniem kroków opisanych w tym artykule.

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że wykonano już następujące czynności:

- Utworzono konto usługi Azure Storage z żądanymi opcjami odporności i szyfrowania w żądanym regionie. Zobacz [Tworzenie konta magazynu,](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) Aby uzyskać instrukcje krok po kroku dotyczące sposobu tworzenia konta magazynu.
- Utworzono udział plików platformy Azure z żądanym limitem przydziału na koncie magazynu. Zobacz [Tworzenie udziału plików](storage-how-to-create-file-share.md) , aby uzyskać instrukcje krok po kroku dotyczące sposobu tworzenia udziału plików.

## <a name="transfer-data-into-azure-files"></a>Przesyłanie danych do Azure Files
Możesz chcieć migrować istniejące udziały plików, takie jak te przechowywane lokalnie, do nowego udziału plików platformy Azure. W tej sekcji przedstawiono sposób przenoszenia danych do udziału plików platformy Azure za pomocą kilku popularnych metod, które opisano szczegółowo w [przewodniku planowania](storage-files-planning.md#migration)

### <a name="azure-file-sync"></a>Azure File Sync
Usługa Azure File Sync umożliwia scentralizowanie udziałów plików Twojej organizacji w usłudze Azure Files bez rezygnacji z elastyczności, wydajności i zgodności lokalnego serwera plików. Robi to poprzez transformowanie serwerów z systemem Windows do szybkiej pamięci podręcznej udziału plików platformy Azure. Możesz użyć dowolnego dostępnego protokołu w systemie Windows Server w celu uzyskania lokalnego dostępu do danych (w tym protokołu SMB, systemu plików NFS i protokołu FTPS) i możesz mieć dowolną potrzebną Ci liczbę pamięci podręcznych na całym świecie.

Azure File Sync może służyć do migrowania danych do udziału plików platformy Azure, nawet jeśli mechanizm synchronizacji nie jest potrzebny do długoterminowego użycia. Więcej informacji na temat sposobu korzystania z Azure File Sync do transferowania danych do udziału plików platformy Azure można znaleźć w temacie [Planowanie wdrożenia Azure File Sync](storage-sync-files-planning.md) i [sposób wdrażania Azure File Sync](storage-sync-files-deployment-guide.md).

### <a name="azure-importexport"></a>Usługa Azure Import/Export
Usługa Azure Import/Export umożliwia bezpieczne przesyłanie dużych ilości danych do udziału plików platformy Azure przez wysyłanie dysków twardych do centrum danych platformy Azure. Aby zapoznać się z bardziej szczegółowym omówieniem usługi, zobacz temat [Używanie usługi Microsoft Azure Import/Export do transferowania danych do magazynu platformy Azure](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) .

> [!Note]  
> Usługa Azure Import/Export nie obsługuje teraz eksportowania plików z udziału plików platformy Azure.

Poniższe kroki spowodują zaimportowanie danych z lokalizacji lokalnej do udziału plików platformy Azure.

1. Pozyskaj wymaganą liczbę dysków twardych do obsługi poczty na platformie Azure. Dyski twarde mogą mieć rozmiar dowolnego dysku, ale musi być dysk SSD lub dysk twardy 2,5 "lub 3,5" obsługujący standard SATA II lub SATA III. 

2. Połącz i zainstaluj każdy dysk na serwerze/komputerze wykonującym transfer danych. W celu uzyskania optymalnej wydajności zaleca się lokalne uruchamianie lokalnego zadania eksportu na serwerze zawierającym dane. W niektórych przypadkach, na przykład gdy serwer plików, który obsługuje dane, jest urządzeniem NAS, może to nie być możliwe. W takim przypadku jest doskonale akceptowalny do zainstalowania każdego dysku na komputerze.

3. Upewnij się, że każdy dysk jest w trybie online, został zainicjowany i ma przypisaną literę dysku. Aby przełączyć dysk do trybu online, zainicjować i przypisać literę dysku, Otwórz przystawkę MMC Zarządzanie dyskami (diskmgmt. msc).

    - Aby przełączyć dysk do trybu online (jeśli nie jest jeszcze w trybie online), kliknij prawym przyciskiem myszy dysk w dolnym okienku programu MMC Zarządzanie dyskami i wybierz polecenie "online".
    - Aby zainicjować dysk, kliknij prawym przyciskiem myszy dysk w dolnym okienku (gdy dysk jest w trybie online) i wybierz polecenie "zainicjuj". Pamiętaj o wybraniu opcji "GPT" po wyświetleniu monitu.

        ![Zrzut ekranu przedstawiający menu Inicjowanie dysku w programie MMC Zarządzanie dyskami](media/storage-files-deployment-guide/transferdata-importexport-1.PNG)

    - Aby przypisać literę dysku do dysku, kliknij prawym przyciskiem myszy miejsce "nieprzydzielony" w trybie online i zainicjowany dysk, a następnie kliknij pozycję "nowy wolumin prosty". Umożliwi to przypisanie litery dysku. Należy pamiętać, że nie ma potrzeby formatowania woluminu, ponieważ zostanie to zrobione później.

        ![Zrzut ekranu przedstawiający Kreatora nowych woluminów prostych w programie MMC Zarządzanie dyskami](media/storage-files-deployment-guide/transferdata-importexport-2.png)

4. Utwórz plik CSV zestawu danych. Plik CSV zestawu danych jest mapowanie między ścieżką do danych lokalnych i żądanym udziałem plików platformy Azure, do którego mają zostać skopiowane dane. Na przykład następujący plik CSV zestawu danych mapuje lokalny udział plików ("F:\shares\scratch") w udział plików platformy Azure ("MyAzureFileShare"):
    
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\shares\scratch\","MyAzureFileShare/",file,rename,"None",None
    ```

    Można określić wiele udziałów z kontem magazynu. Aby uzyskać więcej informacji [, zobacz przygotowanie pliku CSV zestawu danych](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) .

5. Utwórz plik CSV driveset. Plik CSV driveset zawiera listę dysków dostępnych dla lokalnego agenta eksportu. Na przykład następujące pliki CSV driveset zawierają listę `X:` dysków, `Y:` i, `Z:` które mają być używane w zadaniu lokalnego eksportu:

    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    X,Format,SilentMode,Encrypt,
    Y,Format,SilentMode,Encrypt,
    Z,Format,SilentMode,Encrypt,
    ```
    
    Aby uzyskać więcej informacji [, zobacz przygotowanie pliku CSV driveset](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) .

6. Użyj [Narzędzia WAImportExport](https://www.microsoft.com/download/details.aspx?id=55280) , aby skopiować dane na jeden lub więcej dysków twardych.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
    ```

    > [!Warning]  
    > Nie należy modyfikować danych na dyskach twardych ani w pliku dziennika po zakończeniu przygotowywania dysku.

7. [Utwórz zadanie importu](../common/storage-import-export-data-to-files.md#step-2-create-an-import-job).
    
### <a name="robocopy"></a>Robocopy
Robocopy to dobrze znane narzędzie do kopiowania, które jest dostarczane z systemami Windows i Windows Server. Robocopy może służyć do transferowania danych do Azure Files przez zainstalowanie udziału plików lokalnie, a następnie użycie zainstalowanej lokalizacji jako miejsca docelowego w poleceniu Robocopy. Korzystanie z Robocopy jest bardzo proste:

1. [Zainstaluj udział plików platformy Azure](storage-how-to-use-files-windows.md). W celu uzyskania optymalnej wydajności zalecamy zainstalowanie udziału plików platformy Azure lokalnie na serwerze, który zawiera dane. W niektórych przypadkach, na przykład gdy serwer plików, który obsługuje dane, jest urządzeniem NAS, może to nie być możliwe. W takim przypadku warto zainstalować udział plików platformy Azure na komputerze. W tym przykładzie `net use` jest używany w wierszu polecenia, aby zainstalować udział plików:

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. Użyj `robocopy` w wierszu polecenia, aby przenieść dane do udziału plików platformy Azure:

    ```
    robocopy <path-to-local-share> <path-to-azure-file-share> /E /Z /MT:32
    ```
    
    Robocopy ma znaczną liczbę opcji w celu zmodyfikowania zachowania kopiowania zgodnie z potrzebami. Aby uzyskać więcej informacji, zobacz stronę ręczną [Robocopy](https://technet.microsoft.com/library/cc733145.aspx) .

### <a name="azcopy"></a>Narzędzie AzCopy
AzCopy to narzędzie wiersza polecenia przeznaczone do kopiowania danych do i z Azure Files, a także do usługi Azure Blob Storage przy użyciu prostych poleceń z optymalną wydajnością. Korzystanie z AzCopy jest łatwe:

1. Pobierz [najnowszą wersję programu AzCopy w systemie Windows](https://aka.ms/downloadazcopy) lub [Linux](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux#download-and-install-azcopy).
2. Użyj `azcopy` w wierszu polecenia, aby przenieść dane do udziału plików platformy Azure. Składnia w systemie Windows jest następująca: 

    ```
    azcopy /Source:<path-to-local-share> /Dest:https://<storage-account>.file.core.windows.net/<file-share>/ /DestKey:<storage-account-key> /S
    ```

    W systemie Linux Składnia polecenia jest nieco inna:

    ```
    azcopy --source <path-to-local-share> --destination https://<storage-account>.file.core.windows.net/<file-share>/ --dest-key <storage-account-key> --recursive
    ```

    AzCopy ma znaczną liczbę opcji w celu zmodyfikowania zachowania kopiowania zgodnie z potrzebami. Aby uzyskać więcej informacji, zobacz [AzCopy w systemie Windows](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) i [AzCopy w systemie Linux](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="automatically-mount-on-needed-pcsservers"></a>Automatycznie Instaluj na wymaganych komputerach/serwerach
Aby zastąpić udział plików lokalnych, warto wstępnie zainstalować udziały na maszynach, na których będą używane. Można to zrobić automatycznie na liście maszyn.

> [!Note]  
> Instalowanie udziału plików platformy Azure wymaga użycia klucza konta magazynu jako hasła, dlatego zalecamy Instalowanie tylko w środowiskach zaufanych. 

### <a name="windows"></a>Windows
Za pomocą programu PowerShell można uruchomić polecenie instalacji na wielu komputerach. W poniższym przykładzie `$computers` jest wypełniany ręcznie, ale można wygenerować listę komputerów, które mają być instalowane automatycznie. Na przykład można wypełnić tę zmienną wynikami z Active Directory.

```powershell
$computer = "MyComputer1", "MyComputer2", "MyComputer3", "MyComputer4"
$computer | ForEach-Object { Invoke-Command -ComputerName $_ -ScriptBlock { net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name> /PERSISTENT:YES } }
```

### <a name="linux"></a>Linux
Prosty skrypt bash połączony z protokołem SSH może dać ten sam wynik w poniższym przykładzie. `$computer`Zmienna jest podobnie pozostała do wypełnienia przez użytkownika:

```
computer = ("MyComputer1" "MyComputer2" "MyComputer3" "MyComputer4")
for item in "${computer[@]}"
do
    ssh $item "sudo bash -c 'echo \"//<storage-account-name>.file.core.windows.net/<share-name> /mymountpoint cifs vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino\" >> /etc/fstab'", "sudo mount -a"
done
```

## <a name="next-steps"></a>Następne kroki
- [Planowanie wdrożenia Azure File Sync](storage-sync-files-planning.md)
- [Rozwiązywanie problemów z usługą Azure Files w systemie Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Rozwiązywanie problemów z usługą Azure Files w systemie Linux](storage-troubleshoot-linux-file-connection-problems.md)
