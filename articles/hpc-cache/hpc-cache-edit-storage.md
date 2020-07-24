---
title: Aktualizuj cele magazynu pamięci podręcznej platformy Azure HPC
description: Jak edytować cele magazynu pamięci podręcznej platformy Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/02/2020
ms.author: v-erkel
ms.openlocfilehash: f11e12c4f30977514e04b09c7e1c3012eb7888a7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87092460"
---
# <a name="edit-storage-targets"></a>Edytowanie lokalizacji docelowych magazynu

Możesz usunąć lub zmodyfikować miejsce docelowe magazynu z poziomu strony portalu **magazynu obiektów docelowych** pamięci podręcznej lub za pomocą interfejsu wiersza polecenia platformy Azure.

> [!TIP]
> W obszarze [Zarządzanie wideo w pamięci podręcznej platformy Azure HPC](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) pokazano, jak edytować miejsce docelowe magazynu w Azure Portal.

## <a name="remove-a-storage-target"></a>Usuń miejsce docelowe magazynu

### <a name="portal"></a>[Portal](#tab/azure-portal)

Aby usunąć miejsce docelowe magazynu, wybierz je z listy i kliknij przycisk **Usuń** .

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Użyj [AZ HPC-cache Storage-Target Remove](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-remove) , aby usunąć miejsce docelowe magazynu z pamięci podręcznej.

```azurecli
$ az hpc-cache storage-target remove --resource-group cache-rg --cache-name doc-cache0629 --name blob1

{- Finished ..
  "endTime": "2020-07-09T21:45:06.1631571+00:00",
  "name": "2f95eac1-aded-4860-b19c-3f089531a7ec",
  "startTime": "2020-07-09T21:43:38.5461495+00:00",
  "status": "Succeeded"
}
```

---

Ta akcja powoduje usunięcie skojarzenia obiektu docelowego magazynu z tym systemem pamięci podręcznej platformy Azure HPC, ale nie powoduje zmiany systemu magazynu zaplecza. Na przykład jeśli użyto kontenera magazynu obiektów blob platformy Azure, kontener i jego zawartość nadal istnieją po usunięciu z pamięci podręcznej. Możesz dodać kontener do innej pamięci podręcznej platformy Azure HPC, dodać go ponownie do tej pamięci podręcznej lub usunąć za pomocą Azure Portal.

Wszelkie zmiany plików przechowywane w pamięci podręcznej są zapisywane w systemie magazynu zaplecza przed usunięciem miejsca docelowego magazynu. Ten proces może potrwać godzinę lub dłużej, jeśli wiele zmienionych danych znajduje się w pamięci podręcznej.

## <a name="update-storage-targets"></a>Aktualizuj cele magazynu

Można edytować cele magazynu w celu zmodyfikowania niektórych ich właściwości. Różne właściwości można edytować w przypadku różnych typów magazynu:

* W przypadku obiektów docelowych usługi BLOB Storage można zmienić ścieżkę przestrzeni nazw.

* W przypadku obiektów docelowych magazynu NFS można zmienić następujące właściwości:

  * Ścieżka przestrzeni nazw
  * Model użycia
  * Eksportowanie
  * Eksportuj podkatalog

Nie można edytować nazwy, typu lub systemu magazynu zaplecza magazynu (kontenera obiektów blob lub nazwy hosta lub adresu IP systemu plików NFS). Aby zmienić te właściwości, należy usunąć miejsce docelowe magazynu i utworzyć zamiennik z nową wartością.

W Azure Portal można zobaczyć, które pola są edytowalne, klikając nazwę obiektu docelowego magazynu i otwierając jego stronę szczegółów. Możesz również modyfikować cele magazynu za pomocą interfejsu wiersza polecenia platformy Azure.

![zrzut ekranu przedstawiający stronę Edytowanie miejsca docelowego magazynu NFS](media/hpc-cache-edit-storage-nfs.png)

## <a name="update-an-nfs-storage-target"></a>Aktualizowanie miejsca docelowego magazynu NFS

W przypadku miejsca docelowego magazynu NFS można zaktualizować kilka właściwości. (Skorzystaj z powyższego zrzutu ekranu, aby zapoznać się z przykładową stroną Edytuj).

* **Model użycia** — model użycia ma wpływ na sposób przechowywania danych w pamięci podręcznej. Aby dowiedzieć się więcej, zapoznaj się z tematem [Wybieranie modelu użycia](hpc-cache-add-storage.md#choose-a-usage-model) .
* **Ścieżka wirtualnej przestrzeni nazw** — ścieżka używana przez klientów do instalowania tego miejsca docelowego magazynu. Zapoznaj [się z tematem planowanie zagregowanej przestrzeni nazw](hpc-cache-namespace.md) w celu uzyskania szczegółów.
* **Ścieżka eksportu NFS** — eksport systemu magazynu do użycia dla tej ścieżki przestrzeni nazw.
* **Ścieżka podkatalogu** — podkatalog (w ramach eksportu) do skojarzenia z tą ścieżką przestrzeni nazw. Pozostaw to pole puste, jeśli nie musisz określać podkatalogu.

Każda ścieżka przestrzeni nazw musi mieć unikatową kombinację eksportu i podkatalogu. Oznacza to, że nie można wykonać dwóch różnych ścieżek związanych z klientem do dokładnie tego samego katalogu w systemie magazynu zaplecza.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Po wprowadzeniu zmian kliknij przycisk **OK** , aby zaktualizować miejsce docelowe magazynu, lub kliknij przycisk **Anuluj** , aby odrzucić zmiany.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Użyj polecenia [AZ NFS-Storage-Target](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target) , aby zmienić model użycia, ścieżkę wirtualnej przestrzeni nazw oraz wartości eksportowania lub podkatalogów systemu plików NFS dla miejsca docelowego magazynu.

* Aby zmienić model użycia, użyj ``--nfs3-usage-model`` opcji. Przykład: ``--nfs3-usage-model WRITE_WORKLOAD_15``

* Aby zmienić ścieżkę przestrzeni nazw, wyeksportować lub wyeksportować podkatalog, użyj ``--junction`` opcji.

  ``--junction``Parametr używa tych wartości:

  * ``namespace-path``-Ścieżka pliku wirtualnego po stronie klienta
  * ``nfs-export``— Eksport systemu magazynu w celu skojarzenia ze ścieżką skierowaną do klienta
  * ``target-path``(opcjonalnie) — podkatalog eksportu, w razie konieczności

  Przykład: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

Wszystkie polecenia aktualizacji wymagają nazwy pamięci podręcznej, nazwy docelowej magazynu i grupy zasobów.

Przykład polecenia: <!-- having problem testing this -->

```azurecli
az hpc-cache nfs-storage-target update --cache-name mycache \
    --name rivernfs0 --resource-group doc-rg0619 \
    --nfs3-usage-model READ_HEAVY_INFREQ
```

Jeśli pamięć podręczna jest zatrzymana lub nie jest w dobrej kondycji, aktualizacja ma zastosowanie, gdy pamięć podręczna jest w dobrej kondycji.

---

## <a name="update-an-azure-blob-storage-target"></a>Aktualizowanie celu usługi Azure Blob Storage

Dla obiektu docelowego usługi BLOB Storage można zmodyfikować ścieżkę przestrzeni nazw wirtualnej.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Strona szczegóły obiektu docelowego usługi BLOB Storage umożliwia modyfikowanie ścieżki wirtualnej przestrzeni nazw.

![zrzut ekranu przedstawiający stronę Edytowanie elementu docelowego usługi BLOB Storage](media/hpc-cache-edit-storage-blob.png)

Po zakończeniu kliknij przycisk **OK** , aby zaktualizować miejsce docelowe magazynu, lub kliknij przycisk **Anuluj** , aby odrzucić zmiany.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Użyj [AZ HPC-cache BLOB-Storage-Target Update](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-update) , aby zaktualizować ścieżkę przestrzeni nazw obiektu docelowego.

```azurecli
az hpc-cache blob-storage-target update --cache-name cache-name --name target-name \
    --resource-group rg --storage-account "/subscriptions/<subscription_ID>/resourceGroups/erinazcli/providers/Microsoft.Storage/storageAccounts/rg"  \
    --container-name "container-name" --virtual-namespace-path "/new-path"
```

Jeśli pamięć podręczna jest zatrzymana lub nie jest w dobrej kondycji, aktualizacja zostanie zastosowana, gdy pamięć podręczna będzie w dobrej kondycji.

---

## <a name="next-steps"></a>Następne kroki

* Przeczytaj temat [Dodawanie elementów docelowych magazynu](hpc-cache-add-storage.md) , aby dowiedzieć się więcej o tych opcjach.
* Zapoznaj [się z tematem planowanie zagregowanej przestrzeni nazw,](hpc-cache-namespace.md) Aby uzyskać więcej porad dotyczących używania ścieżek wirtualnych.
