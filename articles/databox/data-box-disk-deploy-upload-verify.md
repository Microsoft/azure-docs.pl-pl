---
title: Samouczek dotyczący weryfikowania przekazania danych z urządzenia Azure Data Box Disk na konto magazynu | Microsoft Docs
description: Skorzystaj z tego samouczka, aby dowiedzieć się, jak zweryfikować przekazanie danych z urządzenia Azure Data Box Disk na konto usługi Azure Storage.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.localizationpriority: high
ms.date: 09/17/2019
ms.author: alkohli
ms.openlocfilehash: abe09cf10e241ec95ceed767e7038cde07667fc9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91322737"
---
::: zone target="docs"

# <a name="tutorial-verify-data-upload-from-azure-data-box-disk"></a>Samouczek: Weryfikowanie przekazania danych z urządzenia Azure Data Box Disk

Jest to ostatni samouczek z serii: Wdrażanie urządzenia Azure Data Box Disk. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Weryfikowanie przekazania danych na platformę Azure
> * Wymazywanie danych z urządzenia Data Box Disk

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że zostały wykonane kroki opisane w artykule [Samouczek: Zwracanie urządzenia Azure Data Box Disk](data-box-disk-deploy-picked-up.md).


## <a name="verify-data-upload-to-azure"></a>Weryfikowanie przekazania danych na platformę Azure

Po odebraniu dysków przez kuriera stan zamówienia w portalu zostanie zmieniony na **Pobrane**. Będzie też wyświetlany identyfikator śledzenia przesyłki.

![Dyski zostały pobrane](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

Gdy firma Microsoft odbierze i zeskanuje dysk, stan zadania zmieni się na **Odebrane**. 

![Dyski zostały odebrane](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

Dane zostaną skopiowane automatycznie po podłączeniu dysków do serwera w centrum danych Azure. W zależności od rozmiaru danych operacja kopiowania może potrwać od kilku godzin do kilku dni. Postęp kopiowania możesz monitorować w portalu.

Po zakończeniu kopiowania danych stan zamówienia zmieni się na **Zakończone**.

![Kopiowanie danych zostało zakończone](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

Jeśli kopiowanie zakończy się z błędami, zobacz [rozwiązywanie problemów z błędami przekazywania](data-box-disk-troubleshoot-upload.md).

Sprawdź, czy dane znajdują się na kontach magazynu, zanim usuniesz je ze źródła. Twoje dane mogą się znajdować w następujących lokalizacjach:

- Twoje konta usługi Azure Storage. Po skopiowaniu danych na urządzenie Data Box są one zależnie od typu przekazywane do jednej z poniższych ścieżek w ramach konta usługi Azure Storage.

  - W przypadku blokowych obiektów blob i stronicowych obiektów blob: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - W przypadku usługi Azure Files: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Możesz również przejść do swojego konta usługi Azure Storage w witrynie Azure Portal i nawigować z poziomu tej witryny.

- Twoje grupy zasobów dysku zarządzanego. Podczas tworzenia dysków zarządzanych wirtualne dyski twarde są przekazywane jako stronicowe obiekty blob, a następnie konwertowane na dyski zarządzane. Dyski zarządzane są dołączone do grup zasobów określonych w momencie tworzenia zamówienia.

  - Jeśli operacja kopiowania na dyski zarządzane na platformie Azure zakończyła się pomyślnie, możesz przejść do strony **Szczegóły zamówienia** w witrynie Azure Portal i zanotować grupę zasobów określoną dla dysków zarządzanych.

      ![Wyświetlanie szczegółów zamówienia](media/data-box-disk-deploy-picked-up/order-details-resource-group.png)

    Przejdź do zanotowanej grupy zasobów i znajdź swoje dyski zarządzane.

      ![Grupa zasobów dla dysków zarządzanych](media/data-box-disk-deploy-picked-up/resource-group-attached-managed-disk.png)

    > [!NOTE]
    > Jeśli stronicowy obiekt blob nie zostanie pomyślnie przekonwertowany na dysk zarządzany podczas kopiowania danych, pozostanie on na koncie magazynu i będą naliczane opłaty za magazyn.

  -  Jeśli skopiowano dysk VHDX lub dynamiczny/różnicowy dysk VHD, dysk VHDX/VHD zostanie przekazany do konta magazynu przejściowego jako blokowy obiekt blob. Przejdź do pozycji **Konto magazynu > Obiekty blob** w magazynie przejściowym, a następnie wybierz odpowiedni kontener — SSD w warstwie Standardowa, HDD w warstwie Standardowa lub SSD w warstwie Premium. Dyski VHDX/VHD powinny być wyświetlane jako blokowe obiekty blob na przejściowym koncie magazynu.
  

  
::: zone-end

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Weryfikowanie przekazania danych na platformę Azure

Po przekazaniu danych na platformę Azure sprawdź, czy dane znajdują się na kontach magazynu, zanim usuniesz je ze źródła. Twoje dane mogą się znajdować w następujących lokalizacjach:

- Twoje konta usługi Azure Storage. Po skopiowaniu danych na urządzenie Data Box są one zależnie od typu przekazywane do jednej z poniższych ścieżek w ramach konta usługi Azure Storage.

    - **W przypadku blokowych obiektów blob i stronicowych obiektów blob**: https://<nazwa_konta_magazynu>.blob.core.windows.net/<containername>/files/a.txt

    - **W przypadku usługi Azure Files**: https://<nazwa_konta_magazynu>.file.core.windows.net/<sharename>/files/a.txt

- Twoje grupy zasobów dysku zarządzanego. Podczas tworzenia dysków zarządzanych wirtualne dyski twarde są przekazywane jako stronicowe obiekty blob, a następnie konwertowane na dyski zarządzane. Dyski zarządzane są dołączone do grup zasobów określonych w momencie tworzenia zamówienia.

::: zone-end

Aby sprawdzić, czy dane zostały przekazane na platformę Azure, wykonaj następujące czynności:

1. Przejdź do konta magazynu skojarzonego z zamówieniem dysku.
2. Przejdź do pozycji **Blob Service > Przeglądaj obiekty blob**. Zostanie wyświetlona lista kontenerów. Na koncie magazynu są tworzone kontenery o nazwach odpowiadających nazwom podfolderów utworzonych przez Ciebie w folderach *BlockBlob* i *PageBlob*.
    Jeśli nazwy folderów są niezgodne z konwencją nazewnictwa platformy Azure, przekazywanie danych na platformę Azure zakończy się niepowodzeniem.

3. Aby upewnić się, że cały zestaw danych został przekazany, użyj Eksploratora usługi Microsoft Azure Storage. Dołącz konto magazynu powiązane z zamówieniem urządzenia Data Box Disk, a następnie sprawdź listę kontenerów obiektów blob. Wybierz kontener, kliknij pozycję **Więcej**, a następnie pozycję **Statystyka folderu**. W okienku **Działania** zostaną wyświetlone statystyki dotyczące tego folderu, w tym liczba i łączny rozmiar obiektów blob. Łączny rozmiar obiektów blob w bajtach powinien być taki sam, jak rozmiar zestawu danych.

    ![Statystyka folderu w Eksploratorze usługi Storage](media/data-box-disk-deploy-picked-up/folder-statistics-storage-explorer.png)

## <a name="erasure-of-data-from-data-box-disk"></a>Wymazywanie danych z urządzenia Data Box Disk

Po zakończeniu przekazywania danych na platformę Azure urządzenie Data Box Disk bezpiecznie wymazuje dane ze swoich dysków zgodnie z normą [NIST SP 800-88](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone target="docs"

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące urządzenia Azure Data Box Disk, takie jak:

> [!div class="checklist"]
> * Weryfikowanie przekazania danych na platformę Azure
> * Wymazywanie danych z urządzenia Data Box Disk


Przejdź do następnego tematu, aby zapoznać się z instrukcjami zarządzania usługą Data Box Disk w witrynie Azure Portal.

> [!div class="nextstepaction"]
> [Administrowanie usługą Data Box Disk w witrynie Azure Portal](./data-box-portal-ui-admin.md)

::: zone-end




