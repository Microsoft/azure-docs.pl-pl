---
title: Wysyłaj dane do gorącej, zimnej, zarchiwizowanej warstwy obiektów BLOB za pośrednictwem Azure Data Box/Azure Data Box Heavy
description: Opisuje sposób użycia Azure Data Box lub Azure Data Box Heavy do wysyłania danych do odpowiedniej warstwy magazynu blokowych obiektów blob, takiej jak gorąca, zimna lub archiwalna
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: how-to
ms.date: 05/24/2019
ms.author: alkohli
ms.openlocfilehash: 80c4d8a70454c007ac45f588e59c03ef45f10933
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92125068"
---
# <a name="use-azure-data-box-or-azure-data-box-heavy-to-send-data-to-appropriate-azure-storage-blob-tier"></a>Używanie Azure Data Box lub Azure Data Box Heavy do wysyłania danych do odpowiedniej warstwy obiektów BLOB usługi Azure Storage

Azure Data Box przenosi duże ilości danych na platformę Azure, wysyłając własne urządzenie magazynujące. Wypełnianie urządzenia danymi i zwracanie go. Dane z urządzenie Data Box są przekazywane do warstwy domyślnej skojarzonej z kontem magazynu. Następnie można przenieść dane do innej warstwy magazynowania.

W tym artykule opisano, jak dane przekazywane przez urządzenie Data Box można przenieść do warstwy obiektów BLOB gorąca, zimna lub archiwalna. Ten artykuł dotyczy zarówno Azure Data Box, jak i Azure Data Box Heavy.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="choose-the-correct-storage-tier-for-your-data"></a>Wybieranie odpowiedniej warstwy magazynowania dla danych

Usługa Azure Storage umożliwia korzystanie z trzech różnych warstw do przechowywania danych w najbardziej opłacalny sposób — gorąca, chłodna lub archiwalna. Gorąca warstwa magazynowania jest zoptymalizowana pod kątem przechowywania danych, które są często dostępne. Magazyn gorąca ma wyższe koszty magazynowania niż chłodna i archiwum magazynu, ale najniższy koszt dostępu.

Warstwa magazynowania chłodna jest dla rzadko używanych danych, które muszą być przechowywane przez co najmniej 30 dni. Koszt magazynu dla warstwy zimnej jest niższy niż warstwa magazynowania gorąca, ale opłaty za dostęp do danych są wysokie w porównaniu z warstwą gorącą.

Warstwa usługi Azure Archive jest w trybie offline i oferuje najniższe koszty magazynowania, ale również najwyższe koszty dostępu. Ta warstwa jest przeznaczona dla danych, które pozostają w magazynie archiwalnym przez co najmniej 180 dni. Aby uzyskać szczegółowe informacje o każdej z tych warstw i modelu cen, przejdź do [porównania warstw magazynowania](../storage/blobs/storage-blob-storage-tiers.md).

Dane z urządzenie Data Box lub Data Box Heavy są przekazywane do warstwy magazynowania skojarzonej z kontem magazynu. Podczas tworzenia konta magazynu można określić warstwę dostępu jako gorąca lub zimna. W zależności od wzorca dostępu do obciążenia i kosztu można przenieść te dane z warstwy domyślnej do innej warstwy magazynowania.

Możesz tylko warstwy magazynu obiektów w usłudze BLOB Storage lub Ogólnego przeznaczenia v2 (GPv2). Konta ogólnego przeznaczenia w wersji 1 (GPv1) nie obsługują warstw. Aby wybrać poprawną warstwę magazynowania danych, zapoznaj się z zagadnieniami opisanymi w temacie [Azure Blob Storage: warstwy Premium, gorąca, chłodna i archiwalna](../storage/blobs/storage-blob-storage-tiers.md).

## <a name="set-a-default-blob-tier"></a>Ustawianie domyślnej warstwy obiektów BLOB

Domyślna warstwa obiektów BLOB jest określana podczas tworzenia konta magazynu w Azure Portal. Po wybraniu typu magazynu jako GPv2 lub magazynu obiektów blob, można określić atrybut Warstwa dostępu. Domyślnie jest wybierana warstwa gorąca.

Nie można określić warstw, jeśli próbujesz utworzyć nowe konto podczas porządkowania urządzenie Data Box lub Data Box Heavy. Po utworzeniu konta można zmodyfikować konto w portalu, aby ustawić domyślną warstwę dostępu.

Alternatywnie utworzysz konto magazynu z określonym atrybutem warstwy dostępu. Podczas tworzenia urządzenie Data Box lub zamówienia Data Box Heavy Wybierz istniejące konto magazynu. Aby uzyskać więcej informacji na temat sposobu ustawiania domyślnej warstwy obiektów BLOB podczas tworzenia konta magazynu, przejdź do obszaru [Tworzenie konta magazynu w Azure Portal](../storage/common/storage-account-create.md?tabs=portal).

## <a name="move-data-to-a-non-default-tier"></a>Przenoszenie danych do warstwy innej niż domyślna

Po przekazaniu danych z urządzenia urządzenie Data Box do warstwy domyślnej można przenieść dane do warstwy innej niż domyślna. Istnieją dwa sposoby przenoszenia tych danych do warstwy innej niż domyślna.

- **Zarządzanie cyklem życia usługi Azure Blob Storage** — możesz użyć podejścia opartego na zasadach, aby automatycznie warstwy lub wygasnąć po zakończeniu cyklu życia. Aby uzyskać więcej informacji, przejdź do [obszaru Zarządzanie cyklem życia usługi Azure Blob Storage](../storage/blobs/storage-lifecycle-management-concepts.md).
- **Obsługa skryptów** — można użyć podejścia skryptowego za pośrednictwem Azure PowerShell, aby włączyć obsługę warstw na poziomie obiektów BLOB. Możesz wywołać operację, `SetBlobTier` Aby ustawić warstwę dla obiektu BLOB.

## <a name="use-azure-powershell-to-set-the-blob-tier"></a>Użyj Azure PowerShell, aby ustawić warstwę obiektów BLOB

W poniższych krokach opisano, jak można ustawić warstwę obiektów BLOB do archiwizacji przy użyciu skryptu Azure PowerShell.

1. Otwórz sesję programu Windows PowerShell z podwyższonym poziomem uprawnień. Upewnij się, że używany jest program PowerShell w wersji 5,0 lub nowszej. Wpisz:

   `$PSVersionTable.PSVersion`     

2. Zaloguj się do Azure PowerShell. 

   `Login-AzAccount`  

3. Zdefiniuj zmienne dla konta magazynu, klucza dostępu, kontenera i kontekstu magazynu.

    ```powershell
    $StorageAccountName = "<enter account name>"
    $StorageAccountKey = "<enter account key>"
    $ContainerName = "<enter container name>"
    $ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    ```

4. Pobierz wszystkie obiekty blob w kontenerze.

    `$blobs = Get-AzStorageBlob -Container "<enter container name>" -Context $ctx`
 
5. Ustaw warstwę wszystkich obiektów BLOB w kontenerze do archiwizacji.

    ```powershell
    Foreach ($blob in $blobs) {
    $blob.ICloudBlob.SetStandardBlobTier("Archive")
    }
    ```

    Poniżej przedstawiono przykładowe dane wyjściowe:

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    PS C:\WINDOWS\system32> $PSVersionTable.PSVersion

    Major  Minor  Build  Revision
    -----  -----  -----  --------
    5      1      17763  134
    PS C:\WINDOWS\system32> Login-AzAccount

    Account          : gus@contoso.com
    SubscriptionName : MySubscription
    SubscriptionId   : subscription-id
    TenantId         : tenant-id
    Environment      : AzureCloud

    PS C:\WINDOWS\system32> $StorageAccountName = "mygpv2storacct"
    PS C:\WINDOWS\system32> $StorageAccountKey = "mystorageacctkey"
    PS C:\WINDOWS\system32> $ContainerName = "test"
    PS C:\WINDOWS\system32> $ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    PS C:\WINDOWS\system32> $blobs = Get-AzStorageBlob -Container "test" -Context $ctx
    PS C:\WINDOWS\system32> Foreach ($blob in $blobs) {
    >> $blob.ICloudBlob.SetStandardBlobTier("Archive")
    >> }
    PS C:\WINDOWS\system32>
    ```
   > [!TIP]
   > Jeśli chcesz, aby dane były archiwizowane po pozyskaniu, Ustaw domyślną warstwę konta na gorąca. Jeśli warstwa domyślna to chłodna, nastąpi 30-dniowe wczesne usunięcie, jeśli dane są natychmiastowo przenoszone do archiwum.

## <a name="next-steps"></a>Następne kroki

-  Dowiedz się, jak rozwiązywać [typowe scenariusze dotyczące warstw danych przy użyciu reguł zasad cyklu życia](../storage/blobs/storage-lifecycle-management-concepts.md#examples)