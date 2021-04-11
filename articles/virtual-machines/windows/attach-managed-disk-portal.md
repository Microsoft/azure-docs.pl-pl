---
title: Dołączanie zarządzanego dysku danych do maszyny wirtualnej z systemem Windows — Azure
description: Jak dołączyć dysk danych zarządzanych do maszyny wirtualnej z systemem Windows przy użyciu Azure Portal.
author: roygara
ms.service: virtual-machines
ms.collection: windows
ms.topic: how-to
ms.date: 02/06/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 8c64b0ff5b7a9abfa58ec17d0ebcabe05b0ed6e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102550811"
---
# <a name="attach-a-managed-data-disk-to-a-windows-vm-by-using-the-azure-portal"></a>Dołączanie zarządzanego dysku danych do maszyny wirtualnej z systemem Windows przy użyciu Azure Portal

W tym artykule opisano sposób dołączania nowego zarządzanego dysku danych do maszyny wirtualnej z systemem Windows za pomocą Azure Portal. Rozmiar maszyny wirtualnej określa liczbę dysków z danymi, które można dołączyć. Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych](../sizes.md).


## <a name="add-a-data-disk"></a>Dodawanie dysku z danymi

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby dodać dysk danych. Wyszukaj i wybierz pozycję **maszyny wirtualne**.
2. Wybierz maszynę wirtualną z listy.
3. Na stronie **maszyna wirtualna** wybierz pozycję **dyski**.
4. Na stronie **dyski** wybierz pozycję **Dodaj dysk danych**.
5. Na liście rozwijanej dla nowego dysku wybierz pozycję **Utwórz dysk**.
6. Na stronie **Utwórz dysk zarządzany** wpisz nazwę dysku i dostosuj inne ustawienia w razie potrzeby. Gdy wszystko będzie gotowe, wybierz przycisk **Utwórz**.
7. Na stronie **dyski** wybierz pozycję **Zapisz** , aby zapisać nową konfigurację dysków dla maszyny wirtualnej.
8. Po utworzeniu dysku przez platformę Azure i dołączeniu go do maszyny wirtualnej nowy dysk zostanie wyświetlony na liście ustawień dyskowych maszyny wirtualnej w obszarze **dyski danych**.


## <a name="initialize-a-new-data-disk"></a>Zainicjuj nowy dysk danych

1. Nawiąż połączenie z maszyną wirtualną.
1. Wybierz menu **Start** systemu Windows w ramach URUCHOMIONEJ maszyny wirtualnej i w polu wyszukiwania wpisz **diskmgmt. msc** . Zostanie otwarta konsola **Zarządzanie dyskami** .
2. Funkcja zarządzania dyskami rozpoznaje, że masz nowy, niezainicjowany dysk i zostanie wyświetlone okno **Inicjowanie dysku** .
3. Sprawdź, czy wybrano nowy dysk, a następnie wybierz przycisk **OK** , aby go zainicjować.
4. Nowy dysk zostanie wyświetlony jako **przydzielony**. Kliknij prawym przyciskiem myszy w dowolnym miejscu na dysku i wybierz pozycję **Nowy wolumin prosty**. Zostanie otwarte okno **Kreator nowych woluminów prostych** .
5. Wykonaj czynności wykonywane przez kreatora, zachowując wszystkie ustawienia domyślne i po zakończeniu wybierz pozycję **Zakończ**.
6. Zamknij **przystawkę Zarządzanie dyskami**.
7. Zostanie wyświetlone okno podręczne informujące o konieczności sformatowania nowego dysku przed jego użyciem. Wybierz pozycję **Formatuj dysk**.
8. W oknie **Formatuj nowy dysk** Sprawdź ustawienia, a następnie wybierz pozycję **Uruchom**.
9. Zostanie wyświetlone ostrzeżenie z informacją o tym, że formatowanie dysków spowoduje wymazanie wszystkich danych. Wybierz przycisk **OK**.
10. Po zakończeniu formatowania wybierz **przycisk OK**.

## <a name="next-steps"></a>Następne kroki

- [Dysk danych można również dołączyć przy użyciu programu PowerShell](attach-disk-ps.md).
- Jeśli aplikacja musi używać dysku *D:* do przechowywania danych, można [zmienić literę dysku na dysku tymczasowym systemu Windows](change-drive-letter.md).