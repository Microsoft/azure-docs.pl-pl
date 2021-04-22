---
title: Aktualizowanie Azure HPC Cache docelowych magazynu
description: Jak edytować obiekty docelowe Azure HPC Cache magazynu
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/29/2021
ms.author: v-erkel
ms.openlocfilehash: ebf68c1eb06984e2de8114c53e1bb55d52aed70a
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862637"
---
# <a name="edit-storage-targets"></a>Edytowanie lokalizacji docelowych magazynu

Obiekty docelowe magazynu można usuwać lub modyfikować za pomocą Azure Portal lub przy użyciu interfejsu wiersza polecenia platformy Azure.

W zależności od typu magazynu można zmodyfikować następujące wartości docelowe magazynu:

* W przypadku obiektów docelowych usługi Blob Storage można zmienić ścieżkę przestrzeni nazw i zasady dostępu.

* W przypadku obiektów docelowych magazynu NFS można zmienić następujące wartości:

  * Ścieżki przestrzeni nazw
  * Zasady dostępu
  * Podkatalog eksportu lub eksportu magazynu skojarzony ze ścieżką przestrzeni nazw
  * Model użycia

* W przypadku obiektów docelowych magazynu ADLS-NFS można zmienić ścieżkę przestrzeni nazw, zasady dostępu i model użycia.

Nie można edytować nazwy, typu ani systemu magazynu na zakładzie docelowym magazynu (kontenera obiektów blob lub nazwy hosta/adresu IP systemu plików NFS). Jeśli musisz zmienić te właściwości, usuń miejsce docelowe magazynu i utwórz zastąpienie nową wartością.

> [!TIP]
> W [filmie Azure HPC Cache wideo pokazano,](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) jak edytować miejsce docelowe magazynu w Azure Portal.

## <a name="remove-a-storage-target"></a>Usuwanie miejsca docelowego magazynu

### <a name="portal"></a>[Portal](#tab/azure-portal)

Aby usunąć miejsce docelowe magazynu, otwórz **stronę Miejsca docelowe** magazynu. Wybierz miejsce docelowe magazynu z listy i kliknij **przycisk** Usuń.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

[Skonfiguruj interfejs wiersza polecenia platformy Azure dla Azure HPC Cache](./az-cli-prerequisites.md).

Użyj [az hpc-cache storage-target remove,](/cli/azure/hpc-cache/storage-target#az_hpc_cache_storage_target_remove) aby usunąć obiekt docelowy magazynu z pamięci podręcznej.

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

Usunięcie obiektu docelowego magazynu powoduje usunięcie skojarzenia systemu magazynu z tym Azure HPC Cache magazynu, ale nie powoduje zmiany systemu magazynu na zakładzie. Jeśli na przykład używasz kontenera usługi Azure Blob Storage, kontener i jego zawartość nadal istnieją po usunięciu go z pamięci podręcznej. Możesz dodać kontener do innego Azure HPC Cache, ponownie dodać go do tej pamięci podręcznej lub usunąć za pomocą Azure Portal.

Wszelkie zmiany plików przechowywane w pamięci podręcznej są zapisywane w systemie magazynowania na zakładzie przed usunięciem obiektu docelowego magazynu. Ten proces może potrwać godzinę lub dłużej, jeśli w pamięci podręcznej znajduje się wiele zmienionych danych.

## <a name="change-a-blob-storage-targets-namespace-path"></a>Zmienianie ścieżki przestrzeni nazw obiektu docelowego magazynu obiektów blob

Ścieżki przestrzeni nazw to ścieżki, których klienci używają do instalacji tego obiektu docelowego magazynu. Aby dowiedzieć się więcej, zapoznaj się z [tematami Planowanie zagregowanej przestrzeni nazw](hpc-cache-namespace.md) i [Konfigurowanie zagregowanej przestrzeni nazw](add-namespace-paths.md).

Ścieżka przestrzeni nazw jest jedyną aktualizacją, którą można wprowadzić w obiekcie docelowym usługi Azure Blob Storage. Aby go Azure Portal, użyj interfejsu wiersza polecenia platformy Azure lub interfejsu wiersza polecenia platformy Azure.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Użyj strony **Przestrzeń nazw** dla Azure HPC Cache. Strona przestrzeni nazw jest opisana bardziej szczegółowo w artykule [Konfigurowanie zagregowanej przestrzeni nazw](add-namespace-paths.md).

Kliknij nazwę ścieżki, którą chcesz zmienić, i utwórz nową ścieżkę w wyświetlonym oknie edycji.

![Zrzut ekranu przedstawiający stronę przestrzeni nazw po kliknięciu ścieżki przestrzeni nazw obiektu blob — pola edycji są wyświetlane w okienku po prawej stronie](media/update-namespace-blob.png)

Po w związku z wprowadzeniem zmian kliknij przycisk **OK,** aby zaktualizować miejsce docelowe magazynu, lub kliknij przycisk **Anuluj,** aby odrzucić zmiany.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

[Skonfiguruj interfejs wiersza polecenia platformy Azure dla Azure HPC Cache](./az-cli-prerequisites.md).

Aby zmienić przestrzeń nazw obiektu docelowego magazynu obiektów blob za pomocą interfejsu wiersza polecenia platformy Azure, użyj polecenia [az hpc-cache blob-storage-target update.](/cli/azure/hpc-cache/blob-storage-target#az_hpc_cache_blob_storage_target_update) Można `--virtual-namespace-path` zmienić tylko wartość.

  ```azurecli
  az hpc-cache blob-storage-target update --cache-name cache-name --name target-name \
    --resource-group rg --virtual-namespace-path "/new-path"
  ```

---

## <a name="update-an-nfs-storage-target"></a>Aktualizowanie obiektu docelowego magazynu NFS

W przypadku obiektów docelowych magazynu NFS można zmienić lub dodać ścieżki wirtualnej przestrzeni nazw, zmienić wartości eksportu lub podkatalogu systemu plików NFS, na które wskazuje ścieżka przestrzeni nazw, i zmienić model użycia.

Obiekty docelowe magazynu w pamięciach podręcznych z niektórymi typami niestandardowych ustawień DNS mają również kontrolę odświeżania ich adresów IP. (Ten rodzaj konfiguracji występuje rzadko).

Poniżej znajdują się szczegółowe informacje:

* [Zmienianie wartości zagregowanych przestrzeni nazw](#change-aggregated-namespace-values) (ścieżka wirtualnej przestrzeni nazw, zasady dostępu, eksportowanie i eksportowanie podkatalogu)
* [Zmienianie modelu użycia](#change-the-usage-model)
* [Odświeżanie systemu DNS](#update-ip-address-custom-dns-configurations-only)

### <a name="change-aggregated-namespace-values"></a>Zmienianie wartości zagregowanych przestrzeni nazw

Za pomocą interfejsu Azure Portal wiersza polecenia platformy Azure możesz zmienić ścieżkę przestrzeni nazw klienta, eksport magazynu i podkatalog eksportu (jeśli jest używany).

Zapoznaj się z wytycznymi w te tematu Dodawanie ścieżek przestrzeni nazw systemu plików [NFS,](add-namespace-paths.md#nfs-namespace-paths) jeśli potrzebujesz przypomnienia o tym, jak utworzyć wiele prawidłowych ścieżek na jednym docelowym magazynie.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Użyj strony **Przestrzeń nazw** dla Azure HPC Cache, aby zaktualizować wartości przestrzeni nazw. Ta strona jest opisana bardziej szczegółowo w artykule [Konfigurowanie zagregowanej przestrzeni nazw](add-namespace-paths.md).

![zrzut ekranu przedstawiający stronę przestrzeni nazw portalu z otwartą stroną aktualizacji systemu plików NFS po prawej stronie](media/update-namespace-nfs.png)

1. Kliknij nazwę ścieżki, którą chcesz zmienić.
1. W oknie edycji wpisz nową ścieżkę wirtualną, eksportuj lub podkatalog lub wybierz inne zasady dostępu.
1. Po w związku z wprowadzeniem zmian kliknij **przycisk OK,** aby zaktualizować miejsce docelowe magazynu, lub **przycisk Anuluj,** aby odrzucić zmiany.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

[Skonfiguruj interfejs wiersza polecenia platformy Azure dla Azure HPC Cache](./az-cli-prerequisites.md).

Użyj opcji ``--junction`` w [poleceniu az hpc-cache nfs-storage-target update,](/cli/azure/hpc-cache/nfs-storage-target) aby zmienić ścieżkę przestrzeni nazw, eksport systemu plików NFS lub podkatalog eksportu.

Parametr ``--junction`` używa tych wartości:

* ``namespace-path`` — ścieżka pliku wirtualnego skierowanego do klienta
* ``nfs-export`` — Eksport systemu magazynu do skojarzenia ze ścieżką klienta
* ``target-path`` (opcjonalnie) — podkatalog eksportu, jeśli jest potrzebny

Przykład: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

Należy podać wszystkie trzy wartości dla każdej ścieżki w ``--junction`` instrukcji . Użyj istniejących wartości dla wszystkich wartości, których nie chcesz zmieniać.

Nazwa pamięci podręcznej, nazwa miejsca docelowego magazynu i grupa zasobów są również wymagane we wszystkich poleceniach aktualizacji.

Przykładowe polecenie:

```azurecli
az hpc-cache nfs-storage-target update --cache-name mycache \
  --name st-name --resource-group doc-rg0619 \
  --junction namespace-path="/new-path" nfs-export="/my-export" target-path="my-subdirectory"
```

---

### <a name="change-the-usage-model"></a>Zmienianie modelu użycia

Model użycia ma wpływ na sposób przechowywania danych w pamięci podręcznej. Aby dowiedzieć się więcej, zapoznaj się z tematem Understand [cache usage models (Informacje](cache-usage-models.md) o modelach użycia pamięci podręcznej).

> [!NOTE]
> W przypadku zmiany modeli użycia może być konieczne ponowne odinstalowanie klientów w celu uniknięcia błędów NLM. Przeczytaj [informacje o tym, kiedy ponownie instalować klientów,](cache-usage-models.md#know-when-to-remount-clients-for-nlm) aby uzyskać szczegółowe informacje.

Aby zmienić model użycia dla obiektu docelowego magazynu NFS, użyj jednej z tych metod.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Zmień model użycia ze strony **Miejsca docelowe** magazynu w Azure Portal. Kliknij nazwę miejsca docelowego magazynu do zmiany.

![zrzut ekranu przedstawiający stronę edycji obiektu docelowego magazynu NFS](media/edit-storage-nfs.png)

Użyj selektora listy rozwijanej, aby wybrać nowy model użycia. Kliknij **przycisk OK,** aby zaktualizować miejsce docelowe magazynu, lub kliknij przycisk **Anuluj,** aby odrzucić zmiany.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

[Skonfiguruj interfejs wiersza polecenia platformy Azure dla Azure HPC Cache](./az-cli-prerequisites.md).

Użyj [polecenia az hpc-cache nfs-storage-target update.](/cli/azure/hpc-cache/nfs-storage-target#az_hpc_cache_nfs_storage_target_update)

Polecenie update jest niemal identyczne z poleceniem, które umożliwia dodanie obiektu docelowego magazynu NFS. Aby uzyskać szczegółowe informacje i przykłady, zobacz Create an NFS storage target (Tworzenie obiektu docelowego magazynu [NFS).](hpc-cache-add-storage.md#create-an-nfs-storage-target)

Aby zmienić model użycia, zaktualizuj ``--nfs3-usage-model`` opcję . Przykład: ``--nfs3-usage-model WRITE_WORKLOAD_15``

Wymagane są również nazwa pamięci podręcznej, nazwa miejsca docelowego magazynu i wartości grupy zasobów.

Jeśli chcesz sprawdzić nazwy modeli użycia, użyj polecenia [az hpc-cache usage-model list](/cli/azure/hpc-cache/usage-model#az_hpc_cache_usage-model-list).

Jeśli pamięć podręczna jest zatrzymana lub nie jest w dobrej kondycji, aktualizacja zostanie zastosowania, gdy pamięć podręczna będzie w dobrej kondycji.

---

### <a name="update-ip-address-custom-dns-configurations-only"></a>Aktualizowanie adresu IP (tylko niestandardowe konfiguracje DNS)

Jeśli pamięć podręczna używa nie domyślnej konfiguracji DNS, adres IP obiektu docelowego magazynu systemu plików NFS może ulec zmianie z powodu zmian w systemie DNS na zadomowie. Jeśli serwer DNS zmieni adres IP systemu magazynu Azure HPC Cache może utracić dostęp do systemu magazynowania.

W idealnym przypadku należy współpracować z menedżerem niestandardowego systemu DNS pamięci podręcznej, aby zaplanować wszelkie aktualizacje, ponieważ te zmiany sprawiają, że magazyn jest niedostępny.

Jeśli musisz zaktualizować adres IP dostarczony przez serwer DNS obiektu docelowego magazynu, na liście Miejsca docelowe magazynu znajduje się przycisk. Kliknij **pozycję Odśwież system DNS,** aby odpytować niestandardowy serwer DNS o nowy adres IP.

![Zrzut ekranu przedstawiający listę docelowych elementów magazynu. W przypadku jednego miejsca docelowego magazynu wartość "..." otwarte menu w prawej kolumnie po prawej stronie i są wyświetlane dwie opcje: Usuń i Odśwież system DNS.](media/refresh-dns.png)

W przypadku powodzenia aktualizacja powinna potrwać mniej niż dwie minuty. Jednocześnie można odświeżać tylko jeden obiekt docelowy magazynu. Przed podjęciem próby wykonania kolejnej operacji poczekaj na zakończenie poprzedniej operacji.

## <a name="update-an-adls-nfs-storage-target-preview"></a>Aktualizowanie obiektu docelowego magazynu ADLS-NFS (wersja zapoznawcza)

Podobnie jak w przypadku obiektów docelowych systemu plików NFS, można zmienić ścieżkę przestrzeni nazw i model użycia obiektów docelowych magazynu ADLS-NFS.

### <a name="change-an-adls-nfs-namespace-path"></a>Zmienianie ścieżki przestrzeni nazw ADLS-NFS

Użyj strony **Przestrzeń nazw** dla Azure HPC Cache, aby zaktualizować wartości przestrzeni nazw. Ta strona jest opisana bardziej szczegółowo w artykule [Konfigurowanie zagregowanej przestrzeni nazw](add-namespace-paths.md).

![zrzut ekranu przedstawiający stronę przestrzeni nazw portalu z otwartą stroną aktualizacji ADS-NFS po prawej stronie](media/update-namespace-adls.png)

1. Kliknij nazwę ścieżki, którą chcesz zmienić.
1. Użyj okna edycji, aby wpisać nową ścieżkę wirtualną lub zaktualizować zasady dostępu.
1. Po w związku z wprowadzeniem zmian kliknij **przycisk OK,** aby zaktualizować miejsce docelowe magazynu, lub **przycisk Anuluj,** aby odrzucić zmiany.

### <a name="change-adls-nfs-usage-models"></a>Zmienianie modeli użycia adls-NFS

Konfiguracja modeli użycia usług ADLS-NFS jest taka sama jak w przypadku wyboru modelu użycia systemu plików NFS. Zapoznaj się z instrukcjami w portalu [w sekcji Zmiana modelu](#change-the-usage-model) użycia w powyższej sekcji systemu plików NFS. Dodatkowe narzędzia do aktualizowania obiektów docelowych magazynu ADLS-NFS są w rozwoju.


## <a name="next-steps"></a>Następne kroki

* Przeczytaj [temat Dodawanie obiektów docelowych](hpc-cache-add-storage.md) magazynu, aby dowiedzieć się więcej o tych opcjach.
* Przeczytaj [temat Planowanie zagregowanej przestrzeni nazw,](hpc-cache-namespace.md) aby uzyskać więcej porad na temat używania ścieżek wirtualnych.
