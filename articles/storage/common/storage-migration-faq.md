---
title: Migracja magazynu Azure — często zadawane pytania | Microsoft Docs
description: Odpowiedzi na często zadawane pytania dotyczące migrowania usługi Azure Storage
services: storage
author: genlin
manager: dcscontentpm
ms.service: storage
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.subservice: common
ms.openlocfilehash: 1d8275d11b845df43238dce82beabe89d6464799
ms.sourcegitcommit: 51977b63624dfd3b4f22fb9fe68761d26eed6824
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84944699"
---
# <a name="frequently-asked-questions-about-azure-storage-migration"></a>Często zadawane pytania dotyczące migracji usługi Azure Storage

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące migracji usługi Azure Storage.

## <a name="copy-upload-or-download"></a>Kopiowanie, przekazywanie lub pobieranie

**Jak mogę utworzyć skrypt do kopiowania plików z jednego kontenera do innego?**

Aby skopiować pliki między kontenerami, możesz użyć AzCopy. Zobacz poniższy przykład:

    AzCopy /Source:https://xxx.blob.core.windows.net/xxx
    /Dest:https://xxx.blob.core.windows.net/xxx /SourceKey:xxx /DestKey:xxx
    /S

AzCopy używa [interfejsu API kopiowania obiektów BLOB](https://docs.microsoft.com/rest/api/storageservices/copy-blob) do kopiowania każdego pliku w kontenerze.  

Do uruchomienia AzCopy można użyć dowolnej maszyny wirtualnej lub komputera lokalnego z dostępem do Internetu. Możesz również użyć harmonogramu Azure Batch, aby to zrobić automatycznie, ale jest to bardziej skomplikowane.  

Skrypt automatyzacji został zaprojektowany na potrzeby wdrażania Azure Resource Manager zamiast manipulowania zawartością magazynu. Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów za pomocą szablonów Menedżer zasobów i Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md).

**Czy jest naliczana opłata za kopiowanie danych między dwoma udziałami plików na jednym koncie magazynu w tym samym regionie?**

Nie. Za ten proces nie są naliczane opłaty.

**Jak mogę pobrać 1-2 TB danych z Azure Portal?**

Użyj AzCopy, aby pobrać dane. Aby uzyskać więcej informacji, zobacz [transfer danych za pomocą usługi AzCopy w systemie Windows](storage-use-azcopy.md) i [Transferowanie danych przy użyciu usługi AzCopy w systemie Linux](storage-use-azcopy-linux.md).

**Jak pobrać dysk VHD na maszynę lokalną, inną niż przy użyciu opcji pobierania w portalu?**

Za pomocą [Eksplorator usługi Storage](https://azure.microsoft.com/features/storage-explorer/) można pobrać dysk VHD.

**Jak mogę pobrać danych na komputer z systemem Linux z konta usługi Azure Storage lub przekazać dane z maszyny z systemem Linux?**

Możesz użyć interfejsu wiersza polecenia platformy Azure.

- Pobierz pojedynczy obiekt BLOB:

      azure storage blob download -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -b "<Remote File Name>" -d "<Local path where the file will be downloaded to>"

- Przekaż pojedynczy obiekt BLOB:

      azure storage blob upload -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -f "<Local File Name>"

**Jak mogę Migrowanie obiektów blob z jednego konta magazynu do innego?**

 Można to zrobić przy użyciu naszego [skryptu migracji obiektów BLOB](../scripts/storage-common-transfer-between-storage-accounts.md).
 
## <a name="migration-or-backup"></a>Migracja lub kopia zapasowa

**Jak mogę przenieść dane z jednego kontenera magazynu do innego?**

Wykonaj następujące kroki:

1.  Utwórz kontener (folder) w docelowym obiekcie blob.

2.  Użyj [AzCopy](https://azure.microsoft.com/blog/azcopy-5-1-release/) , aby skopiować zawartość z oryginalnego kontenera obiektów BLOB do innego kontenera obiektów BLOB.

**Jak mogę utworzyć skrypt programu PowerShell służący do przenoszenia danych z jednego udziału plików platformy Azure do innego w usłudze Azure Storage?**

Użyj AzCopy, aby przenieść dane z jednego udziału plików platformy Azure do innego w usłudze Azure Storage. Aby uzyskać więcej informacji, zobacz [transfer danych za pomocą usługi AzCopy w systemie Windows](storage-use-azcopy.md) i [Transferowanie danych przy użyciu usługi AzCopy w systemie Linux](storage-use-azcopy-linux.md).

**Jak mogę przekazać duże pliki CSV do usługi Azure Storage?**

Użyj AzCopy, aby przekazać duże pliki CSV do usługi Azure Storage. Aby uzyskać więcej informacji, zobacz [transfer danych za pomocą usługi AzCopy w systemie Windows](storage-use-azcopy.md) i [Transferowanie danych przy użyciu usługi AzCopy w systemie Linux](storage-use-azcopy-linux.md).

**Muszę przenieść dzienniki z dysku D na konto usługi Azure Storage codziennie. Jak mogę zautomatyzować ten proces?**

Możesz użyć AzCopy i utworzyć zadanie w Harmonogram zadań. Przekaż pliki na konto usługi Azure Storage za pomocą skryptu wsadowego AzCopy. Aby uzyskać więcej informacji, zobacz [jak skonfigurować i uruchomić zadania uruchamiania dla usługi w chmurze](../../cloud-services/cloud-services-startup-tasks.md).

**Jak mogę przenieść konto magazynu między subskrypcjami?**

Użyj AzCopy, aby przenieść konto magazynu między subskrypcjami. Aby uzyskać więcej informacji, zobacz [transfer danych za pomocą usługi AzCopy w systemie Windows](storage-use-azcopy.md) i [Transferowanie danych przy użyciu usługi AzCopy w systemie Linux](storage-use-azcopy-linux.md).

**Jak można przenieść około 10 TB danych do magazynu w innym regionie?**

Użyj AzCopy, aby przenieść dane. Aby uzyskać więcej informacji, zobacz [transfer danych za pomocą usługi AzCopy w systemie Windows](storage-use-azcopy.md) i [Transferowanie danych przy użyciu usługi AzCopy w systemie Linux](storage-use-azcopy-linux.md).

**Jak mogę skopiować dane z lokalizacji lokalnej do usługi Azure Storage?**

Użyj AzCopy, aby skopiować dane. Aby uzyskać więcej informacji, zobacz [transfer danych za pomocą usługi AzCopy w systemie Windows](storage-use-azcopy.md) i [Transferowanie danych przy użyciu usługi AzCopy w systemie Linux](storage-use-azcopy-linux.md).

**Jak przenieść dane z lokalizacji lokalnej do Azure Files?**

Użyj AzCopy, aby przenieść dane. Aby uzyskać więcej informacji, zobacz [transfer danych za pomocą usługi AzCopy w systemie Windows](storage-use-azcopy.md) i [Transferowanie danych przy użyciu usługi AzCopy w systemie Linux](storage-use-azcopy-linux.md).

**Jak mogę przenieść dyski zarządzane na inne konto magazynu?**

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Wykonaj następujące kroki:

1.  Zatrzymaj maszynę wirtualną, do której jest dołączony dysk zarządzany.

2.  Skopiuj wirtualny dysk twardy dysku zarządzanego z jednego obszaru do drugiego, uruchamiając następujący skrypt Azure PowerShell:

    ```
    Connect-AzAccount

    Select-AzSubscription -SubscriptionId <ID>

    $sas = Grant-AzDiskAccess -ResourceGroupName <RG name> -DiskName <Disk name> -DurationInSecond 3600 -Access Read

    $destContext = New-AzStorageContext –StorageAccountName contosostorageav1 -StorageAccountKey <your account key>

    Start-AzStorageBlobCopy -AbsoluteUri $sas.AccessSAS -DestContainer 'vhds' -DestContext $destContext -DestBlob 'MyDestinationBlobName.vhd'
    ```

3.  Utwórz dysk zarządzany przy użyciu pliku VHD w innym regionie, do którego skopiowano dysk VHD. Aby to zrobić, uruchom następujący skrypt Azure PowerShell:  

    ```
    $resourceGroupName = 'MDDemo'

    $diskName = 'contoso\_os\_disk1'

    $vhdUri = 'https://contoso.storageaccou.com.vhd

    $storageId = '/subscriptions/<ID>/resourceGroups/<RG name>/providers/Microsoft.Storage/storageAccounts/contosostorageaccount1'

    $location = 'westus'

    $storageType = 'StandardLRS'

    $diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Import -SourceUri $vhdUri -StorageAccountId $storageId -DiskSizeGB 128

    $osDisk = New-AzDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
    ```

Aby uzyskać więcej informacji na temat sposobu wdrażania maszyny wirtualnej z dysku zarządzanego, zobacz [CreateVmFromManagedOsDisk.ps1](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/blob/master/CreateVmFromManagedOsDisk.ps1).

**Jak mogę przenieść lub pobrać dane z konta magazynu?**

Użyj AzCopy, aby pobrać dane. Aby uzyskać więcej informacji, zobacz [transfer danych za pomocą usługi AzCopy w systemie Windows](storage-use-azcopy.md) i [Transferowanie danych przy użyciu usługi AzCopy w systemie Linux](storage-use-azcopy-linux.md).

**Jak mogę przenieść z konta usługi Premium Storage do konta magazynu w warstwie Standardowa?**

Wykonaj następujące kroki:

1.  Utwórz konto magazynu w warstwie Standardowa. (Lub Użyj istniejącego konta magazynu w ramach subskrypcji).

2.  Pobierz AzCopy. Uruchom jedno z następujących poleceń AzCopy.

    Aby skopiować całe dyski na koncie magazynu:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /S

    Aby skopiować tylko jeden dysk, podaj nazwę dysku we **wzorcu**:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /Pattern:abc.vhd

Ukończenie operacji może potrwać kilka godzin.

Aby upewnić się, że transfer zakończył się pomyślnie, sprawdź kontener docelowego konta magazynu w Azure Portal. Po skopiowaniu dysków na konto magazynu w warstwie Standardowa możesz dołączyć je do maszyny wirtualnej jako istniejący dysk. Aby uzyskać więcej informacji, zobacz [jak dołączyć dysk danych zarządzanych do maszyny wirtualnej z systemem Windows w Azure Portal](../../virtual-machines/windows/attach-managed-disk-portal.md).  

**Jak mogę przeniesieniu z klasycznego konta magazynu na konto magazynu Azure Resource Manager?**

Można użyć polecenia cmdlet **Move-AzureStorageAccount** . To polecenie cmdlet zawiera wiele kroków (Walidacja, przygotowanie, zatwierdzenie). Możesz sprawdzić poprawność przenoszenia przed jego wprowadzeniem.

Jeśli masz maszyny wirtualne, musisz wykonać dodatkowe czynności przed przeprowadzeniem migracji danych konta magazynu. Aby uzyskać więcej informacji, zobacz [Migrowanie zasobów IaaS z wersji klasycznej do Azure Resource Manager przy użyciu Azure PowerShell](../..//virtual-machines/windows/migration-classic-resource-manager-ps.md).

**Jak mogę utworzyć kopię zapasową całego konta magazynu na innym koncie magazynu?**

Nie ma możliwości bezpośredniej kopii zapasowej całego konta magazynu. Można jednak ręcznie przenieść kontener z tego konta magazynu na inne konto za pomocą AzCopy lub Eksplorator usługi Storage. W poniższych krokach pokazano, jak przenieść kontener przy użyciu AzCopy:  

1.  Zainstaluj narzędzie wiersza polecenia [AzCopy](storage-use-azcopy.md) . To narzędzie pomaga przenieść plik VHD między kontami magazynu.

2.  Po zainstalowaniu programu AzCopy w systemie Windows za pomocą Instalatora Otwórz okno wiersza polecenia, a następnie przejdź do folderu instalacyjnego AzCopy na komputerze. Domyślnie AzCopy jest instalowany w folderze **% ProgramFiles (x86)% \ Microsoft SDKs\Azure\AzCopy** lub **%ProgramFiles%\Microsoft SDKs\Azure\AzCopy**.

3.  Uruchom następujące polecenie, aby przenieść kontener. Należy zastąpić tekst wartościami rzeczywistymi.   

            AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
            /Dest:https://destaccount.blob.core.windows.net/mycontainer2
            /SourceKey:key1 /DestKey:key2 /S

    - `/Source`: Podaj identyfikator URI dla źródłowego konta magazynu (do kontenera).  
    - `/Dest`: Podaj identyfikator URI dla docelowego konta magazynu (do kontenera).  
    - `/SourceKey`: Podaj klucz podstawowy dla konta magazynu źródłowego. Możesz skopiować ten klucz z Azure Portal, wybierając konto magazynu.  
    - `/DestKey`: Podaj klucz podstawowy dla docelowego konta magazynu. Możesz skopiować ten klucz z portalu, wybierając konto magazynu.

Po uruchomieniu tego polecenia pliki kontenera są przenoszone na docelowe konto magazynu.

> [!NOTE]
> Interfejs wiersza polecenia AzCopy nie współpracuje ze specyfikatorem **wzorca** podczas kopiowania z jednego obiektu blob platformy Azure do innego.
>
> Można bezpośrednio kopiować i edytować polecenie AzCopy oraz sprawdzać krzyżowo, aby upewnić się, że **wzorzec** jest zgodny ze źródłem. Upewnij **się również, że** symbole wieloznaczne są włączone. Aby uzyskać więcej informacji, zobacz [Parametry AzCopy](storage-use-azcopy.md).

**Jak mogę utworzyć kopii zapasowej usługi Azure File Storage?**

Nie istnieje rozwiązanie do tworzenia kopii zapasowych. Jednak Azure Files obsługuje również kopiowanie asynchroniczne. Można więc kopiować pliki:

- Z udziału w innym udziale w ramach konta magazynu lub na inne konto magazynu.

- Z udziału do kontenera obiektów BLOB w ramach konta magazynu lub na inne konto magazynu.

Aby uzyskać więcej informacji, zobacz [transfer danych za pomocą usługi AzCopy w systemie Windows](storage-use-azcopy.md).

## <a name="configuration"></a>Konfiguracja

**Jak mogę zmienić lokalizację dodatkową na Region Europa dla konta magazynu?**

Podczas tworzenia konta magazynu należy wybrać region podstawowy dla konta. Wybór regionu pomocniczego jest oparty na regionie podstawowym i nie można go zmienić. Aby uzyskać więcej informacji, zobacz [Magazyn Geograficznie nadmiarowy (GRS): replikacja między regionami w ramach usługi Azure Storage](storage-redundancy.md).

**Gdzie można uzyskać więcej informacji na temat szyfrowania usługi Azure Storage?**  

Zobacz następujące artykuły:

- [Szyfrowanie w usłudze Azure Storage dla danych magazynowanych](storage-service-encryption.md)
- [Używanie kluczy zarządzanych przez klienta w usłudze Azure Key Vault do zarządzania szyfrowaniem usługi Azure Storage](encryption-customer-managed-keys.md)
- [Podaj klucz szyfrowania w żądaniu do magazynu obiektów BLOB (wersja zapoznawcza)](encryption-customer-provided-keys.md)

**Jak mogę zaszyfrować dane na koncie magazynu?**

Po włączeniu szyfrowania na koncie magazynu istniejące dane nie są szyfrowane. Aby zaszyfrować istniejące dane, należy przekazać je ponownie na konto magazynu.

Użyj AzCopy, aby skopiować dane na inne konto magazynu, a następnie ponownie Przenieś dane. Można również użyć [szyfrowania w stanie spoczynku](storage-service-encryption.md).

**Czy istnieją jakieś wymagania wstępne dotyczące zmiany replikacji konta magazynu z magazynu geograficznie nadmiarowego na magazyn lokalnie nadmiarowy?**

Nie.

**Jak mogę skonwertować do Premium Storage platformy Azure dla udziału plików?**

Premium Storage nie jest dozwolony w udziale plików platformy Azure.

**Jak mogę uaktualnić konto magazynu w warstwie Standardowa do konta magazynu w warstwie Premium? Jak mogę obniżanie poziomu konta magazynu w warstwie Premium do konta magazynu w warstwie Standardowa?**

Należy utworzyć docelowe konto magazynu, skopiować dane z konta źródłowego na konto docelowe, a następnie usunąć konto źródłowe. Aby skopiować dane, można użyć narzędzia, takiego jak AzCopy.

Jeśli masz maszyny wirtualne, musisz wykonać dodatkowe czynności przed przeprowadzeniem migracji danych konta magazynu. Aby uzyskać więcej informacji, zobacz [Migrowanie do usługi Azure Premium Storage (dysków niezarządzanych)](storage-migration-to-premium-storage.md).

**Jak umożliwić innym osobom dostęp do zasobów magazynu?**

Aby umożliwić innym osobom dostęp do zasobów magazynu:

- Użyj tokenu sygnatury dostępu współdzielonego (SAS), aby zapewnić dostęp do zasobu.

- Podaj użytkownikowi klucz podstawowy lub pomocniczy dla konta magazynu. Aby uzyskać więcej informacji, zobacz [Zarządzanie kluczami dostępu do konta magazynu](storage-account-keys-manage.md).

- Zmień zasady dostępu, aby zezwolić na dostęp anonimowy. Aby uzyskać więcej informacji, zobacz [udzielanie użytkownikom anonimowym uprawnień do kontenerów i obiektów BLOB](../blobs/storage-manage-access-to-resources.md#grant-anonymous-users-permissions-to-containers-and-blobs).

**Gdzie jest zainstalowana AzCopy?**

- Jeśli uzyskujesz dostęp do AzCopy z wiersza polecenia Microsoft Azure Storage, wpisz **AzCopy**. Wiersz polecenia jest instalowany razem z AzCopy.

- Jeśli zainstalowano wersję 32-bitową, znajduje się ona tutaj: **% ProgramFiles (x86)% \\ Microsoft SDK \\ Azure \\ AzCopy**.

- Jeśli zainstalowano wersję 64-bitową, znajduje się ona tutaj: **% ProgramFiles% \\ Microsoft SDK \\ Azure \\ AzCopy**.

**Jak mogę użyć domeny niestandardowej protokołu HTTPS z kontem magazynu? Na przykład jak utworzyć "https: \/ /mystorageaccountname.blob.core.windows.net/images/image.gif" jest wyświetlany jako "https: \/ /www.contoso.com/images/image.gif"?**

Protokoły TLS/SSL nie są obecnie obsługiwane na kontach magazynu z domenami niestandardowymi.
Można jednak używać domen niestandardowych innych niż HTTPS. Aby uzyskać więcej informacji, zobacz [Konfigurowanie niestandardowej nazwy domeny dla punktu końcowego usługi BLOB Storage](../blobs/storage-custom-domain-name.md).

## <a name="access-to-storage"></a>Dostęp do magazynu

**Jak mogę zmapować folderu kontenerów na maszynę wirtualną?**

Użyj udziału plików platformy Azure.

**Jak mogę dostępu Azure Files magazynu nadmiarowego?**

Magazyn Geograficznie nadmiarowy do odczytu jest wymagany w celu uzyskania dostępu do magazynu nadmiarowego. Jednak Azure Files obsługuje tylko Magazyn lokalnie nadmiarowy i standardowy magazyn Geograficznie nadmiarowy, który nie zezwala na dostęp tylko do odczytu.

**W jaki sposób można uzyskać dostęp do danych przechowywanych w regionie pomocniczym dla zreplikowanego konta magazynu (takiego jak magazyn strefowo nadmiarowy, magazyn Geograficznie nadmiarowy lub magazyn Geograficznie nadmiarowy do odczytu).**

- W przypadku korzystania z magazynu Strefowo nadmiarowego lub magazynu geograficznie nadmiarowego nie można uzyskać dostępu do danych z regionu pomocniczego, chyba że zostanie zainicjowany tryb failover w tym regionie. Aby uzyskać więcej informacji na temat procesu przełączania do trybu failover, zobacz [odzyskiwanie po awarii i konto magazynu w trybie failover](storage-disaster-recovery-guidance.md).

- W przypadku korzystania z magazynu geograficznie nadmiarowego dostępnego do odczytu można w dowolnym momencie uzyskać dostęp do danych z regionu pomocniczego. Użyj jednej z następujących metod:  

  - **AzCopy**: Dołącz do nazwy konta magazynu w adresie URL **, aby uzyskać** dostęp do pomocniczego punktu końcowego. Przykład:  

      `https://storageaccountname-secondary.blob.core.windows.net/vhds/BlobName.vhd`

  - **Token SAS**: Użyj tokenu sygnatury dostępu współdzielonego, aby uzyskać dostęp do danych z punktu końcowego. Aby uzyskać więcej informacji, zobacz [Używanie sygnatur dostępu współdzielonego](storage-sas-overview.md).

**Jak mogę używać protokołu FTP do uzyskiwania dostępu do danych znajdujących się na koncie magazynu?**

Nie ma możliwości uzyskania dostępu do konta magazynu bezpośrednio przy użyciu protokołu FTP. Można jednak skonfigurować maszynę wirtualną platformy Azure, a następnie zainstalować na niej serwer FTP. Serwer FTP może przechowywać pliki w udziale Azure Files lub na dysku z danymi, który jest dostępny dla maszyny wirtualnej.

Jeśli chcesz tylko pobrać dane bez konieczności używania Eksplorator usługi Storage lub podobnej aplikacji, może być możliwe użycie tokenu SAS. Aby uzyskać więcej informacji, zobacz [Używanie sygnatur dostępu współdzielonego](storage-sas-overview.md).

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli nadal potrzebujesz pomocy, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), aby szybko rozwiązać problem.
