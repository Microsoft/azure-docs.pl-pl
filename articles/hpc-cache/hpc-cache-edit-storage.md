---
title: Aktualizuj cele magazynu pamięci podręcznej platformy Azure HPC
description: Jak edytować cele magazynu pamięci podręcznej platformy Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: f97ff1c20b7edbf24e5a2c58e22097f88883ae4f
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102204035"
---
# <a name="edit-storage-targets"></a>Edytowanie lokalizacji docelowych magazynu

Możesz usunąć lub zmodyfikować cele magazynu za pomocą Azure Portal lub przy użyciu interfejsu wiersza polecenia platformy Azure.

W zależności od typu magazynu można modyfikować te wartości docelowe magazynu:

* W przypadku obiektów docelowych usługi BLOB Storage można zmienić ścieżkę przestrzeni nazw.

* W przypadku docelowych magazynów NFS można zmienić następujące wartości:

  * Ścieżki przestrzeni nazw
  * Podkatalog eksportu lub eksportu magazynu skojarzony ze ścieżką przestrzeni nazw
  * Model użycia

Nie można edytować nazwy, typu lub systemu magazynu zaplecza magazynu (kontenera obiektów blob lub nazwy hosta lub adresu IP systemu plików NFS). Aby zmienić te właściwości, należy usunąć miejsce docelowe magazynu i utworzyć zamiennik z nową wartością.

> [!TIP]
> W obszarze [Zarządzanie wideo w pamięci podręcznej platformy Azure HPC](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) pokazano, jak edytować miejsce docelowe magazynu w Azure Portal.

## <a name="remove-a-storage-target"></a>Usuń miejsce docelowe magazynu

### <a name="portal"></a>[Portal](#tab/azure-portal)

Aby usunąć miejsce docelowe magazynu, Otwórz stronę **miejsca do magazynowania** . Wybierz docelowy magazyn z listy i kliknij przycisk **Usuń** .

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

[Konfigurowanie interfejsu wiersza polecenia platformy Azure dla pamięci podręcznej platformy Azure HPC](./az-cli-prerequisites.md).

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

Usunięcie obiektu docelowego magazynu spowoduje usunięcie skojarzenia systemu magazynu z tym systemem pamięci podręcznej platformy Azure HPC, ale nie powoduje zmiany systemu magazynu zaplecza. Na przykład jeśli użyto kontenera magazynu obiektów blob platformy Azure, kontener i jego zawartość nadal istnieją po usunięciu z pamięci podręcznej. Możesz dodać kontener do innej pamięci podręcznej platformy Azure HPC, dodać go ponownie do tej pamięci podręcznej lub usunąć za pomocą Azure Portal.

Wszelkie zmiany plików przechowywane w pamięci podręcznej są zapisywane w systemie magazynu zaplecza przed usunięciem miejsca docelowego magazynu. Ten proces może potrwać godzinę lub dłużej, jeśli wiele zmienionych danych znajduje się w pamięci podręcznej.

## <a name="change-a-blob-storage-targets-namespace-path"></a>Zmiana ścieżki przestrzeni nazw docelowego magazynu obiektów BLOB

Ścieżki przestrzeni nazw to ścieżki używane przez klientów do instalowania tego miejsca docelowego magazynu. (Aby dowiedzieć się więcej, przeczytaj temat [Planowanie zagregowanej przestrzeni nazw](hpc-cache-namespace.md) i [Konfigurowanie zagregowanej przestrzeni nazw](add-namespace-paths.md)).

Ścieżka przestrzeni nazw jest jedyną aktualizacją, którą można utworzyć na serwerze docelowym usługi Azure Blob Storage. Użyj Azure Portal lub interfejsu wiersza polecenia platformy Azure, aby go zmienić.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Użyj strony **przestrzeni nazw** dla swojej pamięci podręcznej platformy Azure HPC. Strona przestrzeni nazw została szczegółowo opisana w artykule [Konfigurowanie zagregowanej przestrzeni nazw](add-namespace-paths.md).

Kliknij nazwę ścieżki, którą chcesz zmienić, i Utwórz nową ścieżkę w wyświetlonym oknie edycji.

![Zrzut ekranu strony przestrzeni nazw po kliknięciu ścieżki przestrzeni nazw obiektów BLOB — pola edycji są wyświetlane w okienku po prawej stronie](media/edit-namespace-blob.png)

Po wprowadzeniu zmian kliknij przycisk **OK** , aby zaktualizować miejsce docelowe magazynu, lub kliknij przycisk **Anuluj** , aby odrzucić zmiany.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

[Konfigurowanie interfejsu wiersza polecenia platformy Azure dla pamięci podręcznej platformy Azure HPC](./az-cli-prerequisites.md).

Aby zmienić przestrzeń nazw obiektu docelowego magazynu obiektów BLOB za pomocą interfejsu wiersza polecenia platformy Azure, użyj polecenie [AZ HPC-cache BLOB-Storage-Target Update](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-update). `--virtual-namespace-path`Można zmienić tylko wartość.

  ```azurecli
  az hpc-cache blob-storage-target update --cache-name cache-name --name target-name \
    --resource-group rg --virtual-namespace-path "/new-path"
  ```

---

## <a name="update-an-nfs-storage-target"></a>Aktualizowanie miejsca docelowego magazynu NFS

W przypadku obiektów docelowych magazynu NFS można zmienić lub dodać ścieżki wirtualnego obszaru nazw, zmienić wartości eksportowania lub podkatalogów systemu plików NFS, na które wskazuje ścieżka przestrzeni nazw, i zmienić model użycia.

Poniżej znajdują się szczegóły:

* [Zmień zagregowane wartości przestrzeni nazw](#change-aggregated-namespace-values) (ścieżka wirtualnej przestrzeni nazw, eksport i katalog podkatalogu eksportu)
* [Zmień model użycia](#change-the-usage-model)

### <a name="change-aggregated-namespace-values"></a>Zmień wartości zagregowanych przestrzeni nazw

Za pomocą Azure Portal lub interfejsu wiersza polecenia platformy Azure można zmienić ścieżkę przestrzeni nazw klienta, eksport magazynu i podkatalog eksportu (jeśli jest używany).

Zapoznaj się z instrukcjami w temacie [Dodawanie ścieżek przestrzeni nazw systemu plików NFS](add-namespace-paths.md#nfs-namespace-paths) , jeśli potrzebujesz przypomnienia o sposobie tworzenia wielu prawidłowych ścieżek w jednym miejscu docelowym magazynu.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Użyj strony **przestrzeni nazw** dla pamięci podręcznej platformy Azure HPC, aby zaktualizować wartości przestrzeni nazw. Ta strona została szczegółowo opisana w artykule [Konfigurowanie zagregowanej przestrzeni nazw](add-namespace-paths.md).

![zrzut ekranu strony przestrzeni nazw portalu z otwartą stroną aktualizacji NFS z prawej strony](media/update-namespace-nfs.png)

1. Kliknij nazwę ścieżki, którą chcesz zmienić.
1. Użyj okna Edycja, aby wpisać nową ścieżkę wirtualną, eksport lub wartości podkatalogów.
1. Po wprowadzeniu zmian kliknij przycisk **OK** , aby zaktualizować obiekt docelowy magazynu, lub przycisk **Anuluj** , aby odrzucić zmiany.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

[Konfigurowanie interfejsu wiersza polecenia platformy Azure dla pamięci podręcznej platformy Azure HPC](./az-cli-prerequisites.md).

``--junction``Aby zmienić ścieżkę przestrzeni nazw, eksport systemu plików NFS lub podkatalog eksportu, użyj opcji w poleceniem [AZ HPC-cache NFS-Storage-Target Update](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target) .

``--junction``Parametr używa tych wartości:

* ``namespace-path`` -Ścieżka pliku wirtualnego po stronie klienta
* ``nfs-export`` — Eksport systemu magazynu w celu skojarzenia ze ścieżką skierowaną do klienta
* ``target-path`` (opcjonalnie) — podkatalog eksportu, w razie konieczności

Przykład: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

Należy podać wszystkie trzy wartości dla każdej ścieżki w ``--junction`` instrukcji. Użyj istniejących wartości dla dowolnych wartości, które nie mają być zmieniane.

Wszystkie polecenia aktualizacji wymagają również nazwy pamięci podręcznej, nazwy docelowej magazynu i grupy zasobów.

Przykładowe polecenie:

```azurecli
az hpc-cache nfs-storage-target update --cache-name mycache \
  --name st-name --resource-group doc-rg0619 \
  --junction namespace-path="/new-path" nfs-export="/my-export" target-path="my-subdirectory"
```

---

### <a name="change-the-usage-model"></a>Zmień model użycia

Model użycia ma wpływ na sposób przechowywania danych w pamięci podręcznej. Aby dowiedzieć się więcej, zapoznaj się z tematem [Wybieranie modelu użycia](hpc-cache-add-storage.md#choose-a-usage-model) .

Aby zmienić model użycia dla miejsca docelowego magazynu NFS, użyj jednej z tych metod.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Zmień model użycia na stronie **miejsce docelowe magazynu** w Azure Portal. Kliknij nazwę docelowego magazynu do zmiany.

![zrzut ekranu przedstawiający stronę Edytowanie miejsca docelowego magazynu NFS](media/edit-storage-nfs.png)

Użyj selektora listy rozwijanej, aby wybrać nowy model użycia. Kliknij przycisk **OK** , aby zaktualizować miejsce docelowe magazynu, lub kliknij przycisk **Anuluj** , aby odrzucić zmiany.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

[Konfigurowanie interfejsu wiersza polecenia platformy Azure dla pamięci podręcznej platformy Azure HPC](./az-cli-prerequisites.md).

Użyj polecenia [AZ HPC-cache NFS-Storage-Target](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target#ext-hpc-cache-az-hpc-cache-nfs-storage-target-update) .

Polecenie Update jest niemal identyczne z poleceniem, które służy do dodawania miejsca docelowego magazynu NFS. Aby uzyskać szczegółowe informacje i przykłady, zapoznaj się z tematem [Tworzenie docelowego magazynu NFS](hpc-cache-add-storage.md#create-an-nfs-storage-target) .

Aby zmienić model użycia, zaktualizuj ``--nfs3-usage-model`` opcję. Przykład: ``--nfs3-usage-model WRITE_WORKLOAD_15``

Wymagane są również wartości Nazwa pamięci podręcznej, nazwa obiektu docelowego magazynu i grupy zasobów.

Jeśli chcesz sprawdzić nazwy modeli użycia, użyj polecenia [AZ HPC-cache-model list](/cli/azure/ext/hpc-cache/hpc-cache/usage-model#ext-hpc-cache-az-hpc-cache-usage-model-list).

Jeśli pamięć podręczna jest zatrzymana lub nie jest w dobrej kondycji, aktualizacja zostanie zastosowana, gdy pamięć podręczna będzie w dobrej kondycji.

---

## <a name="next-steps"></a>Następne kroki

* Przeczytaj temat [Dodawanie elementów docelowych magazynu](hpc-cache-add-storage.md) , aby dowiedzieć się więcej o tych opcjach.
* Zapoznaj [się z tematem planowanie zagregowanej przestrzeni nazw,](hpc-cache-namespace.md) Aby uzyskać więcej porad dotyczących używania ścieżek wirtualnych.
