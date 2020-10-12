---
title: Transferowanie danych do obiektów blob platformy Azure za pomocą usługi Azure Import/Export | Microsoft Docs
description: Dowiedz się, jak tworzyć zadania importu i eksportu w Azure Portal, aby przesyłać dane do i z obiektów blob platformy Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 03/12/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 6d12c0ce0df44c37f4e7df49df2c11301513917c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85514224"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>Importowanie danych do platformy Azure Blob Storage przy użyciu usługi Azure Import/Export

Ten artykuł zawiera instrukcje krok po kroku dotyczące korzystania z usługi Azure Import/Export do bezpiecznego importowania dużych ilości danych do usługi Azure Blob Storage. Aby zaimportować dane do obiektów blob platformy Azure, usługa wymaga dostarczania szyfrowanych dysków zawierających dane do centrum danych platformy Azure.  

## <a name="prerequisites"></a>Wymagania wstępne

Przed utworzeniem zadania importowania w celu przesyłania danych do usługi Azure Blob Storage należy uważnie przejrzeć i wykonać poniższą listę wymagań wstępnych dla tej usługi.
Należy:

* Mieć aktywną subskrypcję platformy Azure, która może być używana w usłudze Import/Export.
* Mieć co najmniej jedno konto usługi Azure Storage z kontenerem magazynu. Zapoznaj się z listą [obsługiwanych kont magazynu i typów magazynów dla usługi Import/Export](storage-import-export-requirements.md).
  * Aby uzyskać informacje dotyczące tworzenia nowego konta magazynu, zobacz [jak utworzyć konto magazynu](storage-account-create.md).
  * Aby uzyskać informacje na temat kontenera magazynu, przejdź do obszaru [Tworzenie kontenera magazynu](../blobs/storage-quickstart-blobs-portal.md#create-a-container).
* Ma wystarczającą liczbę dysków [obsługiwanych typów](storage-import-export-requirements.md#supported-disks).
* System Windows z uruchomioną [obsługiwaną wersją systemu operacyjnego](storage-import-export-requirements.md#supported-operating-systems).
* Włącz funkcję BitLocker w systemie Windows. Zobacz [jak włączyć funkcję BitLocker](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
* [Pobierz najnowszą wersję WAImportExport 1](https://www.microsoft.com/download/details.aspx?id=42659) w systemie Windows. Najnowsza wersja tego narzędzia zawiera aktualizacje zabezpieczeń zezwalające na zewnętrzną ochronę klucza funkcji BitLocker oraz zaktualizowaną funkcję trybu odblokowywania.

  * Rozpakuj do folderu domyślnego `waimportexportv1` . Na przykład `C:\WaImportExportV1`.
* Mieć konto FedEx/DHL. Jeśli chcesz użyć operatora innego niż FedEx/DHL, skontaktuj się z zespołem operacyjnym Azure Data Box `adbops@microsoft.com` .  
  * Konto musi być prawidłowe, powinno mieć saldo i musi mieć możliwości wysyłki zwrotnej.
  * Generuj numer śledzenia dla zadania eksportu.
  * Każde zadanie powinno mieć oddzielny numer śledzenia. Nie ma możliwości obsługi wielu zadań o tym samym numerze śledzenia.
  * Jeśli nie masz konta nośnego, przejdź do:
    * [Utwórz konto FedEx](https://www.fedex.com/en-us/create-account.html)lub
    * [Utwórz konto DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-prepare-the-drives"></a>Krok 1. Przygotowanie dysków

Ten krok powoduje wygenerowanie pliku dziennika. W pliku dziennika są przechowywane podstawowe informacje, takie jak numer seryjny dysku, klucz szyfrowania i szczegóły konta magazynu.

Wykonaj poniższe kroki, aby przygotować dyski.

1. Podłącz stacje dysków do systemu Windows za pomocą łączników SATA.
2. Utwórz pojedynczy wolumin NTFS na każdym dysku. Przypisz literę dysku do woluminu. Nie należy używać mountpoints.
3. Włącz szyfrowanie funkcją BitLocker na woluminie NTFS. W przypadku korzystania z systemu Windows Server wykonaj instrukcje podane w temacie [jak włączyć funkcję BitLocker w systemie Windows Server 2012 R2](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
4. Kopiuj dane do woluminu szyfrowanego. Użyj przeciągania i upuszczania lub Robocopy lub dowolnego takiego narzędzia do kopiowania. Plik dziennika (*. jrn*) jest tworzony w tym samym folderze, w którym uruchomiono narzędzie.

   Jeśli dysk jest zablokowany i musisz odblokować dysk, kroki do odblokowania mogą się różnić w zależności od przypadku użycia.

   * Jeśli dane zostały dodane do wstępnie zaszyfrowanego dysku (Narzędzie WAImportExport nie zostało użyte do szyfrowania), użyj klucza funkcji BitLocker (określonego przez użytkownika hasła numerycznego) w celu odblokowania dysku.

   * Jeśli dodano dane do dysku, który został zaszyfrowany za pomocą narzędzia WAImportExport, użyj następującego polecenia, aby odblokować dysk:

        `WAImportExport Unlock /externalKey:<BitLocker key (base 64 string) copied from journal (*.jrn*) file>`

5. Otwórz okno programu PowerShell lub wiersza polecenia z uprawnieniami administracyjnymi. Aby zmienić katalog na folder niespakowany, uruchom następujące polecenie:

    `cd C:\WaImportExportV1`
6. Aby uzyskać klucz funkcji BitLocker dysku, uruchom następujące polecenie:

    `manage-bde -protectors -get <DriveLetter>:`
7. Aby przygotować dysk, uruchom następujące polecenie. **W zależności od rozmiaru danych może to potrwać kilka godzin.**

    ```powershell
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session#<session number> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /blobtype:<BlockBlob or PageBlob> /skipwrite
    ```

    Plik dziennika jest tworzony w tym samym folderze, w którym uruchomiono narzędzie. Tworzone są również dwa inne pliki — plik *XML* (folder, w którym jest uruchamiane narzędzie) i plik *drive-manifest.xml* (folder, w którym znajdują się dane).

    Użyte parametry są opisane w poniższej tabeli:

    |Opcja  |Opis  |
    |---------|---------|
    |/j     |Nazwa pliku dziennika z rozszerzeniem JRN. Plik dziennika jest generowany na dysku. Zalecamy użycie numeru seryjnego dysku jako nazwy pliku dziennika.         |
    |/ Identyfikator:     |Identyfikator sesji. Użyj unikatowego numeru sesji dla każdego wystąpienia polecenia.      |
    |/t:     |Litera dysku do wysłania. Na przykład dysk `D` .         |
    |/bk:     |Klucz funkcji BitLocker dla dysku. Hasło liczbowe z danych wyjściowych `manage-bde -protectors -get D:`      |
    |/srcdir:     |Litera dysku, po którym następuje wydanie `:\` . Na przykład `D:\`.         |
    |/dstdir:     |Nazwa kontenera docelowego w usłudze Azure Storage.         |
    |/blobtype:     |Ta opcja określa typ obiektów blob, do których mają zostać zaimportowane dane. W przypadku blokowych obiektów BLOB jest to `BlockBlob` i dla stronicowych obiektów BLOB `PageBlob` .         |
    |/skipwrite:     |Opcja, która określa, że nie są wymagane żadne nowe dane do skopiowania, a istniejące dane na dysku muszą zostać przygotowane.          |
    |/enablecontentmd5:     |Opcja po włączeniu zapewnia, że MD5 jest obliczany i ustawiany jako `Content-md5` Właściwość dla każdego obiektu BLOB. Użyj tej opcji tylko wtedy, gdy chcesz użyć `Content-md5` pola po przekazaniu danych na platformę Azure. <br> Ta opcja nie ma wpływu na sprawdzanie integralności danych (domyślnie występuje). Ustawienie to zwiększa czas przekazywania danych do chmury.          |
8. Powtórz poprzedni krok dla każdego dysku, który należy dostarczyć. Plik dziennika o podanej nazwie jest tworzony dla każdego przebiegu wiersza polecenia.

    > [!IMPORTANT]
    > * Wraz z plikiem dziennika `<Journal file name>_DriveInfo_<Drive serial ID>.xml` tworzony jest również plik w tym samym folderze, w którym znajduje się narzędzie. Plik. XML jest używany zamiast pliku dziennika podczas tworzenia zadania, jeśli plik dziennika jest zbyt duży.

## <a name="step-2-create-an-import-job"></a>Krok 2. Tworzenie zadania importu

Wykonaj następujące kroki, aby utworzyć zadanie importowania w Azure Portal.

1. Zaloguj się do https://portal.azure.com/ .
2. Przejdź do obszaru **wszystkie usługi > magazyn > zadania importowania/eksportowania**.

    ![Przejdź do zadań importu/eksportu](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Kliknij pozycję **Utwórz zadanie importu/eksportu**.

    ![Kliknij pozycję Utwórz zadanie importu/eksportu](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. **Podstawowe informacje**:

   * Wybierz pozycję **Importuj na platformie Azure**.
   * Wprowadź opisową nazwę zadania importu. Użyj nazwy, aby śledzić postęp zadań.
       * Nazwa może zawierać tylko małe litery, cyfry i łączniki.
       * Nazwa musi rozpoczynać się od litery i nie może zawierać spacji.
   * Wybierz subskrypcję.
   * Wprowadź lub wybierz grupę zasobów.  

     ![Tworzenie zadania importowania — krok 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

5. W **szczegółach zadania**:

   * Przekaż pliki dziennika stacji, które zostały uzyskane podczas kroku przygotowywania dysku. Jeśli `waimportexport.exe version1` został użyty, Przekaż jeden plik dla każdego przygotowanego dysku. Jeśli rozmiar pliku dziennika przekracza 2 MB, można użyć `<Journal file name>_DriveInfo_<Drive serial ID>.xml` również utworzonego w pliku dziennika.
   * Wybierz docelowe konto magazynu, na którym będą przechowywane dane.
   * Lokalizacja Dropoff jest automatycznie wypełniana na podstawie regionu wybranego konta magazynu.

   ![Tworzenie zadania importowania — krok 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

6. W oknie **Informacje o wysyłce zwrotu**:

   * Wybierz operatora z listy rozwijanej. Jeśli chcesz użyć operatora innego niż FedEx/DHL, wybierz istniejącą opcję z listy rozwijanej. Skontaktuj się z zespołem ds. operacyjnych Azure Data Box `adbops@microsoft.com`  z informacjami dotyczącymi przewoźnika, którego zamierzasz używać.
   * Wprowadź prawidłowy numer konta nośnego, który został utworzony za pomocą tego operatora. Firma Microsoft korzysta z tego konta, aby po zakończeniu zadania importowania dostarczać dyski z powrotem do użytkownika. Jeśli nie masz numeru konta, Utwórz konto z systemem [FedEx](https://www.fedex.com/us/oadr/) lub [DHL](https://www.dhl.com/) .
   * Podaj pełną i poprawną nazwę kontaktu, numer telefonu, adres e-mail, ulica, miasto, kod pocztowy, Województwo i kraj/region.

       > [!TIP]
       > Zamiast określania adresu e-mail dla pojedynczego użytkownika, podaj adres e-mail grupy. Dzięki temu będziesz otrzymywać powiadomienia nawet w przypadku opuszczenia przez administratora.

     ![Tworzenie zadania importowania — krok 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)

7. **Podsumowanie**:

   * Przejrzyj informacje o zadaniu podane w podsumowaniu. Zanotuj nazwę zadania i adres wysyłkowy centrum danych platformy Azure, aby dostarczać dyski z powrotem do platformy Azure. Te informacje są używane później na etykiecie wysyłkowej.
   * Kliknij przycisk **OK** , aby utworzyć zadanie importowania.

     ![Tworzenie zadania importowania — krok 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-optional-configure-customer-managed-key"></a>Krok 3 (opcjonalny): Konfigurowanie klucza zarządzanego przez klienta

Pomiń ten krok i przejdź do następnego kroku, jeśli chcesz użyć klucza zarządzanego przez firmę Microsoft w celu ochrony kluczy funkcji BitLocker dla dysków. Aby skonfigurować własny klucz do ochrony klucza funkcji BitLocker, postępuj zgodnie z instrukcjami podanymi w temacie [Konfigurowanie kluczy zarządzanych przez klienta w Azure Key Vault na potrzeby importowania/eksportowania na platformie Azure w Azure Portal](storage-import-export-encryption-key-portal.md)

## <a name="step-4-ship-the-drives"></a>Krok 4. dostarczenie dysków

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-5-update-the-job-with-tracking-information"></a>Krok 5. aktualizowanie zadania przy użyciu informacji śledzenia

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-6-verify-data-upload-to-azure"></a>Krok 6. Weryfikacja przekazywania danych na platformę Azure

Śledź zadanie do ukończenia. Po zakończeniu zadania Sprawdź, czy dane zostały przekazane do platformy Azure. Usuń dane lokalne dopiero po sprawdzeniu, że przekazywanie zakończyło się pomyślnie.

## <a name="next-steps"></a>Następne kroki

* [Wyświetlanie stanu zadania i dysku](storage-import-export-view-drive-status.md)
* [Przejrzyj wymagania dotyczące importu/eksportu](storage-import-export-requirements.md)
