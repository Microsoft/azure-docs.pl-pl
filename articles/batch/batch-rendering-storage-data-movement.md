---
title: Przechowywanie i przenoszenie danych w celu renderowania
description: Dowiedz się więcej na temat różnych opcji przechowywania i przenoszenia danych w przypadku renderowania zasobów i obciążeń plików wyjściowych.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: how-to
ms.openlocfilehash: 55ec04df2a107dabfc72298bc8849c13f3a926e0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86147304"
---
# <a name="storage-and-data-movement-options-for-rendering-asset-and-output-files"></a>Opcje przenoszenia magazynu i danych na potrzeby renderowania zasobów i plików wyjściowych

Istnieje wiele opcji udostępniania plików sceny i zasobów dla aplikacji do renderowania na maszynach wirtualnych puli:

* [Magazyn obiektów blob platformy Azure](../storage/blobs/storage-blobs-introduction.md):
  * Pliki sceny i elementów zawartości są przekazywane do magazynu obiektów blob z lokalnego systemu plików. Gdy aplikacja jest uruchamiana przez zadanie, wymagane pliki są kopiowane z magazynu obiektów BLOB do maszyny wirtualnej, dzięki czemu można uzyskać do nich dostęp za pomocą aplikacji renderowania. Pliki wyjściowe są zapisywane przez aplikację renderowania na dysku maszyny wirtualnej, a następnie kopiowane do magazynu obiektów BLOB.  W razie potrzeby pliki wyjściowe można pobrać z usługi BLOB Storage do lokalnego systemu plików.
  * Usługa Azure Blob Storage to prosta i ekonomiczna opcja dla mniejszych projektów.  Wszystkie pliki zasobów są wymagane na każdej maszynie wirtualnej puli, a następnie liczba i rozmiar plików zasobów zwiększa potrzebę działania, aby zapewnić, że transfery plików są możliwie wydajne.  
* Usługa Azure Storage jako system plików przy użyciu [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md):
  * W przypadku maszyn wirtualnych z systemem Linux konto magazynu może być uwidocznione i używane jako system plików, gdy jest używany sterownik wirtualnego systemu plików blobfuse.
  * Ta opcja ma bardzo ekonomiczne zalety, ponieważ nie są wymagane żadne maszyny wirtualne dla systemu plików, a buforowanie blobfuse na maszynach wirtualnych pozwala uniknąć wielokrotnego pobierania tych samych plików dla wielu zadań i zadań.  Przenoszenie danych jest również proste, ponieważ pliki są po prostu obiektami BLOB i standardowymi interfejsami API i narzędziami, takimi jak AzCopy, mogą służyć do kopiowania plików między lokalnym systemem plików i usługą Azure Storage.
* System plików lub udział plików:
  * W zależności od systemu operacyjnego maszyny wirtualnej i wymagań dotyczących wydajności/skalowalności opcje obejmują [Azure Files](../storage/files/storage-files-introduction.md), przy użyciu maszyny wirtualnej z dołączonymi dyskami dla systemu plików NFS, używając wielu maszyn wirtualnych z dołączonymi dyskami dla rozproszonego systemu plików, takiego jak GlusterFS, lub korzystając z oferty innej firmy.
  * [Systemy avere](https://www.averesystems.com/) są teraz częścią firmy Microsoft i będą mieć rozwiązania w najbliższej przyszłości, które doskonale nadają się do wysokiej wydajności.  Rozwiązanie avere umożliwia tworzenie pamięci podręcznej systemu plików NFS lub SMB opartej na platformie Azure, która działa w połączeniu z usługą BLOB Storage lub lokalnymi urządzeniami NAS.
  * W systemie plików, pliki mogą być odczytywane lub zapisywane bezpośrednio w systemie plików lub mogą być kopiowane między systemem plików i maszynami wirtualnymi puli.
  * Udostępniony system plików umożliwia korzystanie z dużej liczby zasobów współużytkowanych między projektami i zadaniami, które mają być używane, z zadaniami renderowania, które uzyskują dostęp tylko do tego, co jest wymagane.

## <a name="using-azure-blob-storage"></a>Korzystanie z usługi Azure Blob Storage

Powinno być używane konto magazynu obiektów blob lub konto magazynu ogólnego przeznaczenia w wersji 2.  Te dwa typy kont magazynu można skonfigurować przy użyciu znacznie wyższych limitów w porównaniu z kontem magazynu ogólnego przeznaczenia w wersji 1, zgodnie z opisem w [tym wpisie w blogu](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).  Po skonfigurowaniu wyższe limity umożliwią znacznie lepszą wydajność i skalowalność, zwłaszcza w przypadku wielu maszyn wirtualnych puli uzyskujących dostęp do konta magazynu.

### <a name="copying-files-between-client-and-blob-storage"></a>Kopiowanie plików między klientem a magazynem obiektów BLOB

Aby skopiować pliki do i z usługi Azure Storage, można użyć różnych mechanizmów, w tym interfejsu API magazynu obiektów blob, [biblioteki przenoszenia danych usługi Azure Storage](https://github.com/Azure/azure-storage-net-data-movement), narzędzia wiersza polecenia AzCopy dla [systemu Windows](../storage/common/storage-use-azcopy-v10.md) lub [Linux](../storage/common/storage-use-azcopy-v10.md), [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/)i [Azure Batch Explorer](https://azure.github.io/BatchExplorer/).

Na przykład przy użyciu AzCopy wszystkie zasoby w folderze można przenieść w następujący sposób:


`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /Y`

Aby skopiować tylko zmodyfikowane pliki, można użyć parametru/XO:

`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /XO /Y`

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Kopiowanie wejściowych plików zasobów z magazynu obiektów BLOB do maszyn wirtualnych puli usługi Batch

Istnieje kilka różnych podejścia do kopiowania plików z najlepszym podejściem ustalonym przez rozmiar zasobów zadania.
Najprostszym podejściem jest skopiowanie wszystkich plików zasobów do maszyn wirtualnych puli dla każdego zadania:

* Jeśli istnieją jakieś pliki, które są unikatowe dla zadania, ale są wymagane dla wszystkich zadań zadania, można określić [zadanie przygotowania zadania](/rest/api/batchservice/job/add#jobpreparationtask) w celu skopiowania wszystkich plików.  Zadanie przygotowania zadania jest uruchamiane raz, gdy pierwsze zadanie zadania jest wykonywane na maszynie wirtualnej, ale nie jest uruchamiane ponownie w przypadku kolejnych zadań zadania.
* [Zadanie zwolnienia zadania](/rest/api/batchservice/job/add#jobreleasetask) należy określić w celu usunięcia plików poszczególnych zadań po zakończeniu zadania. pozwoli to uniknąć zapełnienia dysku maszyny wirtualnej przez wszystkie pliki zasobów zadania.
* Jeśli istnieje wiele zadań korzystających z tych samych zasobów i tylko przyrostowe zmiany zasobów dla każdego zadania, wszystkie pliki zasobów są nadal kopiowane, nawet jeśli tylko podzbiór został zaktualizowany.  Będzie to niewydajne, gdy istnieje wiele plików zasobów.

Gdy pliki zasobów są ponownie używane między zadaniami, a jedynie przyrostowe zmiany między zadaniami, a następnie bardziej wydajnym podejściem jest przechowywanie zasobów w folderze udostępnionym na maszynie wirtualnej i synchronizowanie zmienionych plików.

* Zadanie przygotowania zadania wykona kopię przy użyciu AzCopy z parametrem/XO do udostępnionego folderu maszyny wirtualnej określonego przez zmienną środowiskową AZ_BATCH_NODE_SHARED_DIR.  Spowoduje to skopiowanie zmienionych plików na każdą maszynę wirtualną.
* Należy zwrócić szczególną część zasobów, aby upewnić się, że będą one dopasowane do tymczasowego dysku maszyn wirtualnych puli.

Azure Batch ma wbudowaną obsługę kopiowania plików między dyskami i maszynami wirtualnymi puli zadań magazynu.  [Pliki zasobów](/rest/api/batchservice/job/add#resourcefile) zadań Kopiuj pliki z magazynu do maszyn wirtualnych puli i można je określić dla zadania przygotowania zadania.  Niestety, gdy istnieją setki plików, możliwe jest osiągnięcie limitu i zadań zakończonych niepowodzeniem.  W przypadku dużej liczby zasobów zaleca się użycie wiersza polecenia AzCopy w zadaniu przygotowania zadania, które może używać symboli wieloznacznych i nie ma limitu.

### <a name="copying-output-files-to-blob-storage-from-batch-pool-vms"></a>Kopiowanie plików wyjściowych do magazynu obiektów blob z maszyn wirtualnych puli usługi Batch

[Pliki wyjściowe](/rest/api/batchservice/task/add#outputfile) mogą być używane do przechowywania kopii plików z maszyny wirtualnej puli.  Co najmniej jeden plik można skopiować z maszyny wirtualnej na określone konto magazynu po zakończeniu zadania.  Renderowane dane wyjściowe powinny być kopiowane, ale mogą być również wymagane do przechowywania plików dziennika.

## <a name="using-a-blobfuse-virtual-file-system-for-linux-vm-pools"></a>Korzystanie z blobfuse wirtualnego systemu plików dla pul maszyn wirtualnych z systemem Linux

Blobfuse to wirtualny sterownik systemu plików dla Blob Storage platformy Azure, który umożliwia dostęp do plików przechowywanych jako obiekty blob na koncie magazynu za pomocą systemu plików Linux.

Węzły puli mogą zainstalować system plików po rozpoczęciu lub instalacji może wystąpić w ramach zadania przygotowania zadania — zadanie, które jest uruchamiane tylko wtedy, gdy pierwsze zadanie w zadaniu jest uruchamiane w węźle.  Blobfuse można skonfigurować w taki sposób, aby korzystały z dysku RAMDisk i lokalnych dysków SSD na potrzeby buforowania plików, co znacznie zwiększa wydajność, jeśli wiele zadań w węźle ma dostęp do niektórych z tych samych plików.

[Szablony przykładowe są dostępne](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/vray/render-linux-with-blobfuse-mount) do uruchamiania autonomicznego renderowania w języku V-Ray przy użyciu systemu plików blobfuse i mogą być używane jako podstawa szablonów dla innych aplikacji.

### <a name="accessing-files"></a>Uzyskiwanie dostępu do plików

Zadania zadań określają ścieżki plików wejściowych i plików wyjściowych przy użyciu zainstalowanego systemu plików.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Kopiowanie wejściowych plików zasobów z magazynu obiektów BLOB do maszyn wirtualnych puli usługi Batch

Ponieważ pliki są po prostu obiektami BLOB w usłudze Azure Storage, standardowe interfejsy API obiektów blob, narzędzia i interfejsów użytkownika mogą służyć do kopiowania plików między lokalnym systemem plików i magazynem obiektów BLOB. na przykład AzCopy, Eksplorator usługi Storage, Batch Explorer itd.

## <a name="using-azure-files-with-windows-vms"></a>Używanie Azure Files z maszynami wirtualnymi z systemem Windows

[Azure Files](../storage/files/storage-files-introduction.md) oferuje w pełni zarządzane udziały plików w chmurze, które są dostępne za pośrednictwem protokołu SMB.  Azure Files jest oparta na usłudze Azure Blob Storage; jest to [koszt wydajny](https://azure.microsoft.com/pricing/details/storage/files/) i można go skonfigurować z replikacją danych w innym regionie, tak aby globalnie nadmiarowo.  [Elementy docelowe skali](../storage/files/storage-files-scale-targets.md#azure-files-scale-targets) powinny być przeglądane w celu określenia, czy Azure Files powinny być używane w celu uwzględnienia rozmiaru puli prognozy i liczby plików zasobów.

[Dokumentacja dotycząca](../storage/files/storage-how-to-use-files-windows.md) sposobu instalowania udziału plików platformy Azure.

### <a name="mounting-an-azure-files-share"></a>Instalowanie udziału Azure Files

Aby można było korzystać z usługi Batch, należy wykonać operację instalacji za każdym razem, gdy zadanie w trakcie działania nie jest możliwe, aby zachować połączenie między zadaniami.  Najprostszym sposobem jest użycie polecenia cmdkey w celu utrwalenia poświadczeń przy użyciu zadania uruchamiania w konfiguracji puli, a następnie zainstalowanie udziału przed każdym zadaniem.

Przykładowe użycie polecenia cmdkey w szablonie puli (w przypadku użycia w pliku JSON) — należy pamiętać, że w przypadku oddzielenia wywołania cmdkey od wywołania net use kontekst użytkownika dla zadania uruchomieniowego musi być taki sam, jak w przypadku uruchamiania zadań podrzędnych:

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

Zadania zadań określają ścieżki plików wejściowych i plików wyjściowych przy użyciu zainstalowanego systemu plików przy użyciu dysku zamapowanego lub ścieżki UNC.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Kopiowanie wejściowych plików zasobów z magazynu obiektów BLOB do maszyn wirtualnych puli usługi Batch

Azure Files są obsługiwane przez wszystkie główne interfejsy API i narzędzia obsługujące usługę Azure Storage. np. AzCopy, interfejs wiersza polecenia platformy Azure, Eksplorator usługi Storage, Azure PowerShell, Batch Explorer itd.

[Azure File Sync](../storage/files/storage-sync-files-planning.md) jest dostępny do automatycznego synchronizowania plików między lokalnym systemem plików i udziałem plików platformy Azure.

## <a name="next-steps"></a>Następne kroki

Więcej informacji o opcjach magazynu znajduje się w szczegółowej dokumentacji:

* [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)
* [Blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md)
* [Azure Files](../storage/files/storage-files-introduction.md)
