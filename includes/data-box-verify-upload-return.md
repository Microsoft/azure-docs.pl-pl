---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 09/30/2019
ms.author: alkohli
ms.openlocfilehash: ca7b83d24f2416b224963559361faf5a7775cd0d
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631544"
---
Gdy firma Microsoft odbierze i zeskanuje urządzenie, stan zamówienia zmieni się na **Odebrane**. Następnie urządzenie zostanie poddane fizycznej weryfikacji pod kątem uszkodzeń lub śladów naruszenia.

Po zakończeniu tej weryfikacji urządzenie Data Box zostanie podłączone do sieci w centrum danych platformy Azure. Kopiowanie danych rozpocznie się automatycznie. W zależności od rozmiaru danych operacja kopiowania może potrwać od kilku godzin do kilku dni. Postęp kopiowania możesz monitorować w portalu.

Po zakończeniu kopiowania danych stan zamówienia zmieni się na **Zakończone**.

Sprawdź, czy dane zostały przekazane na platformę Azure, zanim usuniesz je ze źródła. Twoje dane mogą się znajdować w następujących lokalizacjach:

- Twoje konta usługi Azure Storage. Po skopiowaniu danych na urządzenie Data Box są one przekazywane do jednej z poniższych ścieżek w ramach konta usługi Azure Storage:

  - W przypadku blokowych obiektów blob i stronicowych obiektów blob: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - W przypadku usługi Azure Files: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Możesz też przejść do swojego konta usługi Azure Storage w witrynie Azure Portal i nawigować z poziomu tej witryny.

- Twoje grupy zasobów dysku zarządzanego. Podczas tworzenia dysków zarządzanych wirtualne dyski twarde są przekazywane jako stronicowe obiekty blob, a następnie konwertowane na dyski zarządzane. Dyski zarządzane są dołączone do grup zasobów określonych w momencie tworzenia zamówienia. 

    - Jeśli operacja kopiowania na dyski zarządzane na platformie Azure zakończyła się pomyślnie, możesz przejść do strony **Szczegóły zamówienia** w witrynie Azure Portal i zanotować grupy zasobów określone dla dysków zarządzanych.

        ![Identyfikowanie grup zasobów dysków zarządzanych](media/data-box-verify-upload-return/order-details-managed-disk-resource-groups.png)

        Przejdź do zanotowanej grupy zasobów i znajdź swoje dyski zarządzane.

        ![Dysk zarządzany dołączony do grup zasobów](media/data-box-verify-upload-return/managed-disks-resource-group.png)

    - Jeśli skopiowano plik VHDX albo dynamiczny lub różnicowy dysk VHD, plik VHDX/VHD zostanie przekazany do konta magazynu przejściowego jako stronicowy obiekt blob, ale konwersja dysku VHD na dysk zarządzany zakończy się niepowodzeniem. Wybierz pozycję **Konto magazynu > Obiekty blob** w magazynie przejściowym, a następnie wybierz odpowiedni kontener — SSD w warstwie Standardowa, HDD w warstwie Standardowa lub SSD w warstwie Premium. Wirtualne dyski twarde są przekazywane do przejściowego konta magazynu jako stronicowe obiekty blob, co powoduje naliczanie opłat.


## <a name="erasure-of-data-from-data-box"></a>Wymazywanie danych z urządzenia Data Box
 
Po zakończeniu przekazywania danych na platformę Azure dane z dysków urządzenia Data Box są bezpiecznie wymazywane zgodnie z wytycznymi [NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).


## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące usługi Azure Data Box, takie jak:

> [!div class="checklist"]
> * Wymagania wstępne
> * Przygotowanie do wysłania
> * Wysyłanie urządzenia Data Box do firmy Microsoft
> * Weryfikowanie przekazania danych na platformę Azure
> * Wymazywanie danych z urządzenia Data Box

Przejdź do następnego artykułu, aby dowiedzieć się, jak zarządzać urządzeniem Data Box za pomocą lokalnego internetowego interfejsu użytkownika.

> [!div class="nextstepaction"]
> [Use local web UI to administer Azure Data Box (Administrowanie urządzeniem Azure Data Box za pomocą lokalnego internetowego interfejsu użytkownika)](../articles/databox/data-box-local-web-ui-admin.md)