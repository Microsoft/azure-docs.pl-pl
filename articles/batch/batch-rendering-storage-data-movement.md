---
title: Magazyn i ruch danych do renderowania
description: Dowiedz się więcej o różnych opcjach magazynu i ruchu danych na potrzeby renderowania obciążeń zasobów i plików wyjściowych.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: how-to
ms.openlocfilehash: 0a18ee6961cb601b0fa9db7213eb6115afa20096
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765201"
---
# <a name="storage-and-data-movement-options-for-rendering-asset-and-output-files"></a>Opcje magazynu i ruchu danych do renderowania plików zasobów i plików wyjściowych

Istnieje wiele opcji, dzięki czemu pliki sceny i elementów zawartości są dostępne dla aplikacji renderowania na maszyn wirtualnych puli:

* [Azure Blob Storage:](../storage/blobs/storage-blobs-introduction.md)
  * Pliki sceny i zasobów są przekazywane do magazynu obiektów blob z lokalnego systemu plików. Gdy aplikacja jest uruchamiana przez zadanie, wymagane pliki są kopiowane z magazynu obiektów blob na maszynę wirtualną, aby były dostępne dla aplikacji renderowania. Pliki wyjściowe są zapisywane przez aplikację renderowania na dysku maszyny wirtualnej, a następnie kopiowane do magazynu obiektów blob.  W razie potrzeby pliki wyjściowe można pobrać z magazynu obiektów blob do lokalnego systemu plików.
  * Usługa Azure Blob Storage jest prostą i opłacalnym rozwiązaniem dla mniejszych projektów.  Ponieważ wszystkie pliki zasobów są wymagane na każdej maszynie wirtualnej puli, w przypadku zwiększenia liczby i rozmiaru plików zasobów należy zadbać o to, aby transfery plików były jak najbardziej wydajne.  
* Usługa Azure Storage jako system plików [używający systemu blobfuse:](../storage/blobs/storage-how-to-mount-container-linux.md)
  * W przypadku maszyn wirtualnych z systemem Linux konto magazynu może być udostępniane i używane jako system plików, gdy jest używany sterownik wirtualnego systemu plików blobfuse.
  * Ta opcja ma tę zaletę, że jest bardzo opłacalny, ponieważ dla systemu plików nie są wymagane żadne maszyny wirtualne, a ponadto buforowanie obiektów blobfuse na maszyny wirtualne pozwala uniknąć wielokrotnego pobierania tych samych plików dla wielu zadań i zadań.  Przepływ danych jest również prosty, ponieważ pliki są po prostu obiektami blob, a standardowe interfejsy API i narzędzia, takie jak azcopy, mogą służyć do kopiowania plików między lokalnym systemem plików i usługą Azure Storage.
* System plików lub udział plików:
  * W zależności od systemu operacyjnego maszyny wirtualnej i wymagań dotyczących wydajności/skalowania dostępne są następujące opcje: [Azure Files](../storage/files/storage-files-introduction.md), użycie maszyny wirtualnej z dołączonymi dyskami dla systemu plików NFS, użycie wielu maszyn wirtualnych z dołączonymi dyskami w rozproszonym systemie plików, np. System System plikówKlesterFS, lub użycie oferty innej firmy.
  * [Firma Avere Systems](https://www.averesystems.com/) jest teraz częścią firmy Microsoft i w najbliższej przyszłości będzie mieć rozwiązania idealne do renderowania o wysokiej wydajności na dużą skalę.  Rozwiązanie Avere umożliwi utworzenia opartej na platformie Azure pamięci podręcznej NFS lub SMB, która działa w połączeniu z magazynem obiektów blob lub lokalnymi urządzeniami NAS.
  * W systemie plików pliki mogą być odczytywane lub zapisywane bezpośrednio w systemie plików lub kopiowane między systemem plików a maszynami wirtualnych puli.
  * Udostępniony system plików umożliwia korzystanie z dużej liczby zasobów współdzielonych między projektami i zadaniami, a zadania renderowania mają dostęp tylko do wymaganych zasobów.

## <a name="using-azure-blob-storage"></a>Korzystanie z usługi Azure Blob Storage

Należy użyć konta magazynu obiektów blob lub konta magazynu ogólnego przeznaczenia w wersji 2.  Te dwa typy kont magazynu można skonfigurować ze znacznie wyższymi limitami w porównaniu z kontem magazynu ogólnego przeznaczenia w wersji 1, zgodnie z informacjami w [tym wpisie w blogu.](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)  Po skonfigurowaniu wyższe limity umożliwią znacznie lepszą wydajność i skalowalność, szczególnie w przypadku, gdy wiele maszyn wirtualnych puli uzyskuje dostęp do konta magazynu.

### <a name="copying-files-between-client-and-blob-storage"></a>Kopiowanie plików między klientem a magazynem obiektów blob

Aby skopiować pliki do i z usługi Azure Storage, można użyć różnych mechanizmów, w tym interfejsu API obiektu blob magazynu, biblioteki przemieszczania danych usługi [Azure Storage,](https://github.com/Azure/azure-storage-net-data-movement)narzędzia wiersza polecenia azcopy dla systemów [Windows](../storage/common/storage-use-azcopy-v10.md) lub [Linux,](../storage/common/storage-use-azcopy-v10.md) [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/)i [Azure Batch Explorer](https://azure.github.io/BatchExplorer/).

Na przykład za pomocą narzędzia azcopy można przenieść wszystkie zasoby w folderze w następujący sposób:


`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /Y`

Aby skopiować tylko zmodyfikowane pliki, można użyć /XO parametru:

`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /XO /Y`

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Kopiowanie plików wejściowych zasobów z magazynu obiektów blob do maszyn wirtualnych puli usługi Batch

Istnieje kilka różnych metod kopiowania plików z najlepszym podejściem określanym na podstawie rozmiaru zasobów zadań.
Najprostszym podejściem jest skopiowanie wszystkich plików zasobów do maszyn wirtualnych puli dla każdego zadania:

* Jeśli istnieją pliki unikatowe dla zadania, ale są wymagane dla wszystkich [](/rest/api/batchservice/job/add#jobpreparationtask) zadań, można określić zadanie przygotowania zadania, aby skopiować wszystkie pliki.  Zadanie przygotowania zadania jest uruchamiane raz, gdy pierwsze zadanie jest wykonywane na maszynie wirtualnej, ale nie jest ponownie uruchamiane dla kolejnych zadań.
* Należy [określić zadanie zwolnienia](/rest/api/batchservice/job/add#jobreleasetask) zadania, aby usunąć pliki 1 zadania po zakończeniu zadania. Pozwoli to uniknąć wypełnienia dysku maszyny wirtualnej przez wszystkie pliki zasobów zadania.
* Jeśli istnieje wiele zadań korzystających z tych samych zasobów, z tylko przyrostowymi zmianami zasobów dla każdego zadania, wszystkie pliki elementów zawartości są nadal kopiowane, nawet jeśli zaktualizowano tylko podzbiór.  Byłoby to nieefektywne, gdy istnieje wiele dużych plików zasobów.

Gdy pliki zasobów są ponownie używane między zadaniami, przy tylko przyrostowych zmianach między zadaniami, bardziej wydajnym, ale nieco bardziej zaangażowanym podejściem jest przechowywanie zasobów w folderze udostępnionym na maszynie wirtualnej i synchronizowanie zmienionych plików.

* Zadanie przygotowania zadania wykona kopię przy użyciu narzędzia azcopy z parametrem /XO do folderu udostępnionego maszyny wirtualnej określonego przez AZ_BATCH_NODE_SHARED_DIR środowiskowej.  Spowoduje to skopiowanie tylko zmienionych plików na każdą maszynę wirtualną.
* Należy przemyśleć rozmiar wszystkich zasobów, aby upewnić się, że zmieścią się one na dysku tymczasowym maszyn wirtualnych puli.

Azure Batch ma wbudowaną obsługę kopiowania plików między kontem magazynu a maszynami wirtualnych puli usługi Batch.  Pliki [zasobów zadania](/rest/api/batchservice/job/add#resourcefile) kopiują pliki z magazynu do maszyn wirtualnych puli i można je określić dla zadania przygotowania zadania.  Niestety, jeśli istnieją setki plików, możliwe jest przekroenie limitu i niepowodzenie zadań.  W przypadku dużej liczby zasobów zaleca się użycie wiersza polecenia azcopy w zadaniu przygotowywania zadania, które może używać symboli wieloznacznych i nie ma limitu.

### <a name="copying-output-files-to-blob-storage-from-batch-pool-vms"></a>Kopiowanie plików wyjściowych do magazynu obiektów blob z maszyn wirtualnych puli usługi Batch

[Plików wyjściowych](/rest/api/batchservice/task/add#outputfile) można użyć do kopiowania plików z maszyny wirtualnej puli do magazynu.  Po zakończeniu zadania można skopiować co najmniej jeden plik z maszyny wirtualnej na określone konto magazynu.  Powinny zostać skopiowane renderowane dane wyjściowe, ale może być również pożądane przechowywanie plików dziennika.

## <a name="using-a-blobfuse-virtual-file-system-for-linux-vm-pools"></a>Używanie wirtualnego systemu plików blobfuse dla pul maszyn wirtualnych z systemem Linux

Blobfuse to sterownik wirtualnego systemu plików dla systemu Azure Blob Storage, który umożliwia dostęp do plików przechowywanych jako obiekty blob na koncie magazynu za pośrednictwem systemu plików Linux.

Węzły puli mogą zainstalować system plików po uruchomieniu lub w ramach zadania przygotowania zadania — zadania uruchamianego tylko wtedy, gdy pierwsze zadanie podrzędne w zadaniu jest uruchamiane w węźle.  System Blobfuse można skonfigurować do buforowania plików przy użyciu dysku ramdisk i lokalnego dysku SSD maszyn wirtualnych, co znacznie zwiększy wydajność, jeśli wiele zadań w węźle uzyskuje dostęp do niektórych z tych samych plików.

[Dostępne są przykładowe szablony](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/vray/render-linux-with-blobfuse-mount) do uruchamiania autonomicznych renderów V-Ray przy użyciu systemu plików blobfuse i mogą służyć jako podstawa dla szablonów dla innych aplikacji.

### <a name="accessing-files"></a>Uzyskiwanie dostępu do plików

Zadania zadań określają ścieżki dla plików wejściowych i wyjściowych przy użyciu zainstalowanego systemu plików.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Kopiowanie plików wejściowych zasobów z magazynu obiektów blob do maszyn wirtualnych puli usługi Batch

Ponieważ pliki są po prostu obiektami blob w usłudze Azure Storage, standardowe interfejsy API obiektów blob, narzędzia i interfejsy użytkownika mogą służyć do kopiowania plików między lokalnym systemem plików i magazynem obiektów blob. na przykład azcopy, Eksplorator usługi Storage, Batch Explorer itp.

## <a name="using-azure-files-with-windows-vms"></a>Używanie Azure Files maszyn wirtualnych z systemem Windows

[Azure Files](../storage/files/storage-files-introduction.md) udostępnia w pełni zarządzane udziały plików w chmurze, które są dostępne za pośrednictwem protokołu SMB.  Azure Files jest oparta na usłudze Azure Blob Storage; Jest to [ekonomiczne i można](https://azure.microsoft.com/pricing/details/storage/files/) je skonfigurować przy użyciu replikacji danych do innego regionu, tak aby była globalnie nadmiarowa.  [Cele skalowania](../storage/files/storage-files-scale-targets.md#azure-files-scale-targets) należy przejrzeć, aby określić, Azure Files należy użyć, biorąc pod uwagę rozmiar puli prognozy i liczbę plików zasobów.

Istnieje dokumentacja [dotycząca](../storage/files/storage-how-to-use-files-windows.md) sposobu instalacji udziału plików platformy Azure.

### <a name="mounting-an-azure-files-share"></a>Instalowanie udziału Azure Files plików

Aby można było użyć w udaniu Batch, należy wykonać operację instalacji za każdym razem, gdy zadanie jest uruchamiane, ponieważ nie jest możliwe utrwalanie połączenia między zadaniami.  Najprostszym sposobem wykonania tej akcji jest użycie klucza cmdkey w celu utrwalenia poświadczeń przy użyciu zadania uruchamiania w konfiguracji puli, a następnie instalacji udziału przed każdym zadaniem.

Przykładowe użycie polecenia cmdkey w szablonie puli (z ucieczką do użycia w pliku JSON) — należy pamiętać, że podczas oddzielania wywołania cmdkey od wywołania net use kontekst użytkownika zadania uruchamiania musi być taki sam jak używany do uruchamiania zadań:

```
"startTask": {
  "commandLine": "cmdkey /add:storageaccountname.file.core.windows.net
    /user:AZURE\\markscuscusbatch /pass:storage_account_key",
  "userIdentity":{
    "autoUser": {
      "elevationLevel": "nonadmin",
      "scope": "pool"
    }
}
```

Przykładowy wiersz polecenia zadania:
```
"commandLine":"net use S:
  \\\\storageaccountname.file.core.windows.net\\rendering &
3dsmaxcmdio.exe -v:5 -rfw:0 -10 -end:10
  -bitmapPath:\"s:\\3dsMax\\Dragon\\Assets\"
  -outputName:\"s:\\3dsMax\\Dragon\\RenderOutput\\dragon.jpg\"
  -w:1280 -h:720
  \"s:\\3dsMax\\Dragon\\Assets\\Dragon_Character_Rig.max\""
```

### <a name="accessing-files"></a>Uzyskiwanie dostępu do plików

Zadania zadań określają ścieżki dla plików wejściowych i wyjściowych przy użyciu zainstalowanego systemu plików przy użyciu dysku zmapowanych lub ścieżki UNC.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Kopiowanie plików wejściowych zasobów z magazynu obiektów blob do maszyn wirtualnych puli usługi Batch

Azure Files są obsługiwane przez wszystkie główne interfejsy API i narzędzia, które obsługują usługę Azure Storage; np. azcopy, interfejs wiersza polecenia platformy Azure, Eksplorator usługi Storage, Azure PowerShell, Batch Explorer itp.

[Azure File Sync](../storage/file-sync/file-sync-planning.md) jest dostępna do automatycznego synchronizowania plików między lokalnym systemem plików i udziałem plików platformy Azure.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat opcji magazynu, zobacz szczegółową dokumentację:

* [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)
* [Blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md)
* [Azure Files](../storage/files/storage-files-introduction.md)
