---
title: Przewodnik Szybki start dotyczący zarządzania udziałami plików platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure
description: W tym przewodniku szybki start dowiesz się, jak zarządzać Azure Files przy użyciu interfejsu wiersza polecenia platformy Azure. Utwórz grupę zasobów i konto magazynu, a następnie Utwórz udział plików platformy Azure i użyj go.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli
ms.openlocfilehash: d665ee11a0688c2a112858ce0c02d359ed86e973
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629873"
---
# <a name="quickstart-create-and-manage-azure-file-shares-using-azure-cli"></a>Szybki start: tworzenie udziałów plików platformy Azure i zarządzanie nimi przy użyciu interfejsu wiersza polecenia platformy Azure
W tym przewodniku przedstawiono podstawowe informacje dotyczące pracy z [udziałami plików platformy Azure](storage-files-introduction.md) przy użyciu interfejsu wiersza polecenia platformy Azure. Udziały plików platformy Azure są podobne do innych udziałów plików, ale są przechowywane w chmurze i obsługiwane przez platformę Azure. Udziały plików platformy Azure obsługują protokół SMB (Server Message Block), Protokół NFS (Network File System) (wersja zapoznawcza) i umożliwia udostępnianie plików na wielu komputerach, aplikacjach i wystąpieniach. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli podejmiesz decyzję o zainstalowaniu i używaniu interfejsu wiersza polecenia platformy Azure lokalnie, to aby wykonać kroki opisane w tym artykule, musisz mieć wersję 2.0.4 lub nowszą. Uruchom polecenie **az --version** , aby określić wersję używanego interfejsu wiersza polecenia platformy Azure. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli). 

Domyślnie polecenia interfejsu wiersza polecenia platformy Azure zwracają kod JSON (JavaScript Object Notation). Kod JSON to standardowy sposób wysyłania i odbierania komunikatów w ramach komunikacji z interfejsami API REST. W celu ułatwienia pracy z odpowiedziami JSON przykłady w tym artykule korzystają z parametru *query* w poleceniach interfejsu wiersza polecenia platformy Azure. Ten parametr używa [języka zapytań JMESPath](http://jmespath.org/) do analizowania danych JSON. Aby dowiedzieć się więcej na temat używania wyników poleceń interfejsu wiersza polecenia platformy Azure, zobacz [samouczek języka zapytań JMESPath](http://jmespath.org/tutorial.html).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Jeśli nie masz jeszcze grupy zasobów platformy Azure, możesz utworzyć nową grupę za pomocą polecenia [az group create](/cli/azure/group). 

Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *zachodnie stany USA 2* :

```azurecli-interactive 
export resourceGroupName="myResourceGroup"
region="westus2"

az group create \
    --name $resourceGroupName \
    --location $region \
    --output none
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu
Konto magazynu to udostępniona pula magazynu, w której można wdrażać udziały plików platformy Azure lub inne zasoby magazynu, takie jak obiekty blob i kolejki. Konto magazynu może zawierać nieograniczoną liczbę udziałów plików. W udziale można przechowywać nieograniczoną liczbę plików, aż do osiągnięcia limitów pojemności konta magazynu.

Poniższy przykład tworzy konto magazynu za pomocą polecenia [AZ Storage account Create](/cli/azure/storage/account) . Nazwy kont magazynów muszą być unikatowe, użyj zatem polecenia `$RANDOM` w celu dołączenia liczby do nazwy, aby była unikatowa.

```azurecli-interactive 
export storageAccountName="mystorageacct$RANDOM"

az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --location $region \
    --kind StorageV2 \
    --sku Standard_LRS \
    --enable-large-file-share \
    --output none
```

> [!Note]  
> Udziały większe niż 5 TiB (maksymalnie 100 TiB na udział) są dostępne tylko na kontach magazynu lokalnie nadmiarowy (LRS) i strefowo nadmiarowe (ZRS). Aby utworzyć konto magazynu geograficznie nadmiarowego (GRS) lub strefy geograficznie nadmiarowe (GZRS), Usuń `--enable-large-file-share` parametr.

### <a name="get-the-storage-account-key"></a>Pobieranie klucza konta magazynu
Klucze konta magazynu kontrolują dostęp do zasobów na koncie magazynu. Klucze są tworzone automatycznie podczas tworzenia konta magazynu. Klucze konta magazynu możesz pobrać przy użyciu polecenia [az storage account keys list](/cli/azure/storage/account/keys): 

```azurecli-interactive 
export storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')
```

## <a name="create-an-azure-file-share"></a>Tworzenie udziału plików platformy Azure
Teraz możesz utworzyć swój pierwszy udział plików platformy Azure. Udziały plików tworzy się przy użyciu polecenia [az storage share create](/cli/azure/storage/share). W tym przykładzie jest tworzony udział plików platformy Azure o nazwie *myshare* : 

```azurecli-interactive
shareName="myshare"

az storage share create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --quota 1024 \
    --enabled-protocols SMB \
    --output none
```

Nazwy udziałów mogą zawierać tylko małe litery, cyfry i pojedyncze łączniki (ale nie mogą zaczynać się łącznikiem). Aby uzyskać szczegółowe informacje o nazewnictwie udziałów plików i plików, zobacz [nazewnictwo i odwoływanie się do udziałów, katalogów, plików i metadanych](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="use-your-azure-file-share"></a>Korzystanie z udziału plików platformy Azure
Usługa Azure Files oferuje dwie metody pracy z plikami i folderami w obrębie udziału plików platformy Azure: zgodny ze standardem branżowym [protokół Server Message Block (SMB)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) i [protokół REST usługi Files](/rest/api/storageservices/file-service-rest-api). 

Aby zainstalować udział plików za pomocą protokołu SMB, zobacz następujący dokument zgodny z używanym systemem operacyjnym:
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)
- [Windows](storage-how-to-use-files-windows.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Korzystanie z udziału plików platformy Azure za pomocą protokołu REST usługi Files 
Istnieje możliwość bezpośredniej pracy bezpośrednio z protokołem REST plików (handcrafting HTTP REST), ale najbardziej typowym sposobem korzystania z protokołu REST jest użycie interfejsu wiersza polecenia platformy Azure, [modułu Azure PowerShell](storage-how-to-use-files-powershell.md)lub zestawu SDK usługi Azure Storage, z którego wszystkie zapewniają całkiem otokę z protokołem REST usługi File w wybranym języku skryptów/programowania.  

Zdajemy sobie sprawę, że większość użytkowników usługi Azure Files będzie chciało pracować z udziałami plików platformy Azure za pośrednictwem protokołu SMB, ponieważ umożliwi im to skorzystanie z istniejących aplikacji i narzędzi, ale istnieje kilka powodów, dla których użycie interfejsu API REST usługi Files wydaje się korzystniejsze niż użycie protokołu SMB:

- Udziały plików przeglądasz za pomocą powłoki Azure Bash w usłudze Cloud Shell (w której nie można instalować udziałów plików za pośrednictwem protokołu SMB).
- Korzystasz z zasobów bezserwerowych, takich jak [usługa Azure Functions](../../azure-functions/functions-overview.md). 
- Tworzysz usługę wartości doprowadzającej, która będzie współdziałać z wieloma udziałami plików platformy Azure, takimi jak wykonywanie skanowania kopii zapasowej lub skanowanie antywirusowe.

W poniższych przykładach przedstawiono, jak używać interfejsu wiersza polecenia platformy Azure do obsługi udziałów plików platformy Azure przy użyciu protokołu REST usługi Files. 

### <a name="create-a-directory"></a>Tworzenie katalogu
Aby utworzyć nowy katalog o nazwie *myDirectory* w katalogu głównym udziału plików platformy Azure, użyj polecenia [`az storage directory create`](/cli/azure/storage/directory):

```azurecli-interactive
az storage directory create \
   --account-name $storageAccountName \
   --account-key $storageAccountKey \
   --share-name $shareName \
   --name "myDirectory" \
   --output none
```

### <a name="upload-a-file"></a>Przekazywanie pliku
Aby zademonstrować sposób przekazywania pliku przy użyciu [`az storage file upload`](/cli/azure/storage/file) polecenia, najpierw utwórz plik do przekazania na Cloud Shell dysku zapasowego. Poniższy przykład umożliwia utworzenie, a następnie przekazanie pliku:

```azurecli-interactive
cd ~/clouddrive/
date > SampleUpload.txt

az storage file upload \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --source "SampleUpload.txt" \
    --path "myDirectory/SampleUpload.txt"
```

Jeśli korzystasz z interfejsu wiersza polecenia platformy Azure lokalnie, zastąp ciąg `~/clouddrive` ścieżką, która istnieje na Twojej maszynie.

Po przekazaniu pliku możesz użyć [`az storage file list`](/cli/azure/storage/file) polecenia, aby upewnić się, że plik został przekazany do udziału plików platformy Azure:

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory" \
    --output table
```

### <a name="download-a-file"></a>Pobieranie pliku
Możesz użyć polecenia, [`az storage file download`](/cli/azure/storage/file) Aby pobrać kopię pliku, który został przekazany do Cloud Shell dysku zapasowego:

```azurecli-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists, because you've run this example before
rm -f SampleDownload.txt

az storage file download \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory/SampleUpload.txt" \
    --dest "SampleDownload.txt" \
    --output none
```

### <a name="copy-files"></a>Kopiowanie plików
Typowym zadaniem jest kopiowanie plików z jednego udziału plików do innego udziału plików. Aby zademonstrować tę funkcję, utwórz nowy udział. Skopiuj przekazany plik do nowego udziału za pomocą polecenia [az storage file copy](/cli/azure/storage/file/copy): 

```azurecli-interactive
otherShareName="myshare2"

az storage share create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $otherShareName \
    --quota 1024 \
    --enabled-protocols SMB \
    --output none

az storage directory create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $otherShareName \
    --name "myDirectory2" \
    --output none

az storage file copy start \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --source-share $shareName \
    --source-path "myDirectory/SampleUpload.txt" \
    --destination-share $otherShareName \
    --destination-path "myDirectory2/SampleCopy.txt"
```

Teraz, po wyświetleniu listy plików w nowym udziale, skopiowany plik powinien być widoczny:

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $otherShareName \
    --path "myDirectory2" \
    --output table
```

Chociaż `az storage file copy start` polecenie jest wygodne w przypadku przenoszenia plików między udziałami plików platformy Azure, w przypadku migracji i większych przepływów danych zalecamy `rsync` w systemach MacOS i Linux oraz `robocopy` w systemie Windows. `rsync` i `robocopy` Użyj protokołu SMB, aby wykonać przesunięcia danych zamiast interfejsu API FileREST.

## <a name="create-and-manage-share-snapshots"></a>Tworzenie migawek udziałów i zarządzanie nimi
Inną przydatną czynnością, którą można wykonywać na udziałach plików platformy Azure, jest tworzenie migawek udziałów. Migawka zachowuje kopię udziału plików Azure w określonym momencie w czasie. Migawki udziałów są podobne do niektórych technologii systemów operacyjnych, które być może już znasz:

- Migawki [Menedżera woluminów logicznych (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) dla systemów Linux.
- Migawki [Systemu plików firmy Apple (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) dla systemu macOS
- [Usługa kopiowania woluminów w tle (VSS)](/windows/desktop/VSS/volume-shadow-copy-service-portal) dla systemów plików systemu Windows, takich jak NTFS i ReFS.
 
Migawkę udziału można utworzyć przy użyciu [`az storage share snapshot`](/cli/azure/storage/share) polecenia:

```azurecli-interactive
snapshot=$(az storage share snapshot \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --query "snapshot" | tr -d '"')
```

### <a name="browse-share-snapshot-contents"></a>Przeglądanie zawartości migawki udziału
Zawartość migawki udziału można przeglądać, przekazując sygnaturę czasową migawki udziału przechwyconą w zmiennej `$snapshot` do polecenia `az storage file list`:

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --snapshot $snapshot \
    --output table
```

### <a name="list-share-snapshots"></a>Wyświetlanie listy migawek udziałów
Za pomocą następującego polecenia możesz wyświetlić listę migawek utworzonych dla udziału:

```azurecli-interactive
az storage share list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --include-snapshot \
    --query "[? name== '$shareName' && snapshot!=null].snapshot" \
    --output tsv
```

### <a name="restore-from-a-share-snapshot"></a>Przywracanie na podstawie migawki udziału
Plik możesz przywrócić za pomocą polecenia `az storage file copy start` użytego wcześniej. Najpierw usuń przekazany plik SampleUpload.txt, aby można go było przywrócić z migawki:

```azurecli-interactive
# Delete SampleUpload.txt
az storage file delete \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory/SampleUpload.txt" \
    --output none

# Build the source URI for a snapshot restore
URI=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "primaryEndpoints.file" | tr -d '"')

URI=$URI$shareName"/myDirectory/SampleUpload.txt?sharesnapshot="$snapshot

# Restore SampleUpload.txt from the share snapshot
az storage file copy start \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --source-uri $URI \
    --destination-share $shareName \
    --destination-path "myDirectory/SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Usuwanie migawki udziału
Migawkę udziału można usunąć za pomocą [`az storage share delete`](/cli/azure/storage/share) polecenia. Użyj zmiennej, która zawiera odwołanie `$SNAPSHOT` do parametru `--snapshot`:

```azurecli-interactive
az storage share delete \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --snapshot $snapshot \
    --output none
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów
Gdy skończysz, możesz użyć [`az group delete`](/cli/azure/group) polecenia, aby usunąć grupę zasobów i wszystkie powiązane zasoby: 

```azurecli-interactive 
az group delete --name $resourceGroupName
```

Alternatywnie możesz usunąć zasoby pojedynczo.
- Aby usunąć udziały plików platformy Azure utworzone w tym artykule:

    ```azurecli-interactive
    az storage share list \
            --account-name $storageAccountName \
            --account-key $storageAccountKey \
            --query "[].name" \
            --output tsv | \
        xargs -L1 bash -ec '\
            az storage share delete \
                --account-name "$storageAccountName" \
                --account-key "$storageAccountKey" \
                --name $0 \
                --delete-snapshots include \
                --output none'
    ```

- Aby usunąć samo konto magazynu. (To powoduje niejawne usunięcie utworzonych udziałów plików platformy Azure i wszystkich innych utworzonych zasobów magazynu, takich jak kontener usługi Azure Blob Storage).

    ```azurecli-interactive
    az storage account delete \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --yes
    ```

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Co to jest usługa Azure Files?](storage-files-introduction.md)