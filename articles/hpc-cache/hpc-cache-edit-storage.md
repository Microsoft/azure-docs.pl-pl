---
title: Aktualizuj cele magazynu pamięci podręcznej platformy Azure HPC
description: Jak edytować cele magazynu pamięci podręcznej platformy Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 1/08/2020
ms.author: rohogue
ms.openlocfilehash: 5635bfc6ea5faea41b125037c76c0b8635e0f528
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75867006"
---
# <a name="edit-storage-targets"></a>Edytowanie lokalizacji docelowych magazynu

Można usunąć lub zmodyfikować miejsce docelowe magazynu z poziomu strony **miejsca docelowe magazynu** pamięci podręcznej.

## <a name="remove-a-storage-target"></a>Usuń miejsce docelowe magazynu

Aby usunąć miejsce docelowe magazynu, wybierz je z listy i kliknij przycisk **Usuń** .

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

Aby zmodyfikować miejsce docelowe magazynu, kliknij nazwę obiektu docelowego magazynu, aby otworzyć jego stronę szczegółów. Niektóre pola na stronie można edytować.

![zrzut ekranu przedstawiający stronę Edytowanie miejsca docelowego magazynu NFS](media/hpc-cache-edit-storage-nfs.png)

## <a name="update-an-nfs-storage-target"></a>Aktualizowanie miejsca docelowego magazynu NFS

W przypadku miejsca docelowego magazynu NFS można zaktualizować kilka właściwości. (Skorzystaj z powyższego zrzutu ekranu, aby zapoznać się z przykładową stroną Edytuj).

* **Model użycia** — model użycia ma wpływ na sposób przechowywania danych w pamięci podręcznej. Aby dowiedzieć się więcej, zapoznaj się z tematem [Wybieranie modelu użycia](hpc-cache-add-storage.md#choose-a-usage-model) .
* **Ścieżka wirtualnej przestrzeni nazw** — ścieżka używana przez klientów do instalowania tego miejsca docelowego magazynu. Zapoznaj [się z tematem planowanie zagregowanej przestrzeni nazw](hpc-cache-namespace.md) w celu uzyskania szczegółów.
* **Ścieżka eksportu NFS** — eksport systemu magazynu do użycia dla tej ścieżki przestrzeni nazw.
* **Ścieżka podkatalogu** — podkatalog (w ramach eksportu) do skojarzenia z tą ścieżką przestrzeni nazw. Pozostaw to pole puste, jeśli nie musisz określać podkatalogu.

Każda ścieżka przestrzeni nazw musi mieć unikatową kombinację eksportu i podkatalogu. Oznacza to, że nie można wykonać dwóch różnych ścieżek związanych z klientem do dokładnie tego samego katalogu w systemie magazynu zaplecza.

Po wprowadzeniu zmian kliknij przycisk **OK** , aby zaktualizować miejsce docelowe magazynu, lub kliknij przycisk **Anuluj** , aby odrzucić zmiany.

## <a name="update-an-azure-blob-storage-target"></a>Aktualizowanie celu usługi Azure Blob Storage

Strona szczegóły obiektu docelowego usługi BLOB Storage umożliwia modyfikowanie ścieżki wirtualnej przestrzeni nazw.

![zrzut ekranu przedstawiający stronę Edytowanie elementu docelowego usługi BLOB Storage](media/hpc-cache-edit-storage-blob.png)

Po zakończeniu kliknij przycisk **OK** , aby zaktualizować miejsce docelowe magazynu, lub kliknij przycisk **Anuluj** , aby odrzucić zmiany.

## <a name="next-steps"></a>Następne kroki

* Przeczytaj temat [Dodawanie elementów docelowych magazynu](hpc-cache-add-storage.md) , aby dowiedzieć się więcej o tych opcjach.
* Zapoznaj [się z tematem planowanie zagregowanej przestrzeni nazw,](hpc-cache-namespace.md) Aby uzyskać więcej porad dotyczących używania ścieżek wirtualnych.
