---
title: Samouczek dotyczący odsyłania z powrotem urządzenia Azure Data Box Heavy | Microsoft Docs
description: Dowiedz się, jak odesłać urządzenie Azure Data Box Heavy do firmy Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 3aedfed980a61ea6718a02a188ff88d4941026d9
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83199176"
---
::: zone target = "docs"

# <a name="tutorial-return-azure-data-box-heavy-and-verify-data-upload-to-azure"></a>Samouczek: Wysyłka zwrotna urządzenia Azure Data Box Heavy i weryfikowanie przekazania danych na platformę Azure

::: zone-end

::: zone target = "chromeless"

## <a name="return-azure-data-box-heavy-and-verify-data-upload-to-azure"></a>Wysyłka zwrotna urządzenia Azure Data Box Heavy i weryfikowanie przekazania danych na platformę Azure

::: zone-end

::: zone target = "docs"

W tym samouczku opisano sposób zwrotu urządzenia Azure Data Box Heavy i weryfikacji danych przekazanych na platformę Azure.

Ten samouczek zawiera informacje na następujące tematy:

> [!div class="checklist"]
> * Wymagania wstępne
> * Przygotowanie do wysłania
> * Wysyłanie urządzenia Data Box Heavy do firmy Microsoft
> * Weryfikowanie przekazania danych na platformę Azure
> * Wymazywanie danych z urządzenia Data Box Heavy

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że spełniono następujące warunki:

- Ukończono [Samouczek: kopiowanie danych na urządzenie Azure Data Box i ich weryfikacja](data-box-heavy-deploy-copy-data.md).
- Zadania kopiowania zostały wykonane. Przygotowanie do wysłania nie będzie działać, jeśli zadania kopiowania są w toku.


## <a name="prepare-to-ship"></a>Przygotowanie do wysłania

[!INCLUDE [data-box-heavy-prepare-to-ship](../../includes/data-box-heavy-prepare-to-ship.md)]

::: zone-end

::: zone target = "chromeless"

## <a name="prepare-to-ship"></a>Przygotowanie do wysłania

Przed przygotowaniem do wysłania upewnij się, że zadania kopiowania zostały ukończone.

1. Przejdź do strony Prepare to ship (Przygotowanie do wysłania) w lokalnym internetowym interfejsie użytkownika i rozpocznij przygotowanie do wysłania.
2. Wyłącz urządzenie za pomocą lokalnego internetowego interfejsu użytkownika. Odłącz przewody od urządzenia.

Teraz możesz przystąpić do wysyłania zwrotnego urządzenia.

::: zone-end

## <a name="ship-data-box-heavy-back"></a>Wysyłanie zwrotne urządzenia Data Box Heavy

1. Upewnij się, że wyłączono zasilanie urządzenia i wszystkie kable zostały odłączone. Zwiń 4 przewody zasilające i umieść je bezpiecznie w zasobniku, do którego można uzyskać dostęp z tyłu urządzenia.
2. Urządzenie jest wysyłane transportem drobnicowym (LTL) za pośrednictwem firmy FedEx w Stanach Zjednoczonych i firmy DHL w Unii Europejskiej

    1. Skontaktuj się z [działem operacji Data Box](mailto:DataBoxOps@microsoft.com), aby poinformować o odbiorze i uzyskać etykietę wysyłki zwrotnej.
    2. Zadzwoń do lokalnego oddziału przewoźnika, aby uzgodnić odbiór.
    3. Upewnij się, że etykieta wysyłkowa została umieszczona w widocznym miejscu na zewnątrz przesyłki.
    4. Upewnij się, że stare etykiety wysyłkowe z poprzedniego transportu zostały usunięte z urządzenia.
3. Po odebraniu urządzenia Data Box Heavy i zeskanowaniu go przez kuriera stan zamówienia w portalu zostanie zmieniony na **Pobrane**. Będzie też wyświetlany identyfikator śledzenia przesyłki.

::: zone target = "docs"

## <a name="verify-data-upload-to-azure"></a>Weryfikowanie przekazania danych na platformę Azure

Gdy firma Microsoft odbierze i zeskanuje urządzenie, stan zamówienia zmieni się na **Odebrane**. Następnie urządzenie zostanie poddane fizycznej weryfikacji pod kątem uszkodzeń lub śladów naruszenia.

Po zakończeniu tej weryfikacji urządzenie Data Box Heavy zostanie podłączone do sieci w centrum danych platformy Azure. Kopiowanie danych rozpocznie się automatycznie. W zależności od rozmiaru danych operacja kopiowania może potrwać od kilku godzin do kilku dni. Postęp kopiowania możesz monitorować w portalu.

Po zakończeniu kopiowania danych stan zamówienia zmieni się na **Zakończone**.

Sprawdź, czy dane zostały przekazane na platformę Azure, zanim usuniesz je ze źródła. Twoje dane mogą się znajdować w następujących lokalizacjach:

- Twoje konta usługi Azure Storage. Po skopiowaniu danych na urządzenie Data Box są one zależnie od typu przekazywane do jednej z poniższych ścieżek w ramach konta usługi Azure Storage.

  - W przypadku blokowych obiektów blob i stronicowych obiektów blob: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - W przypadku usługi Azure Files: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Możesz też przejść do swojego konta usługi Azure Storage w witrynie Azure Portal i nawigować z poziomu tej witryny.

- Twoje grupy zasobów dysku zarządzanego. Podczas tworzenia dysków zarządzanych wirtualne dyski twarde są przekazywane jako stronicowe obiekty blob, a następnie konwertowane na dyski zarządzane. Dyski zarządzane są dołączone do grup zasobów określonych w momencie tworzenia zamówienia. 

    - Jeśli operacja kopiowania na dyski zarządzane na platformie Azure zakończyła się pomyślnie, możesz przejść do strony **Szczegóły zamówienia** w witrynie Azure Portal i zanotować grupy zasobów określone dla dysków zarządzanych.

        ![Identyfikowanie grup zasobów dysków zarządzanych](media/data-box-deploy-copy-data-from-vhds/order-details-managed-disk-resource-groups.png)

        Przejdź do zanotowanej grupy zasobów i znajdź swoje dyski zarządzane.

        ![Dysk zarządzany dołączony do grup zasobów](media/data-box-deploy-copy-data-from-vhds/managed-disks-resource-group.png)

    - Jeśli skopiowano plik VHDX lub dynamiczny/różnicowy dysk VHD, pliki VHDX/VHD zostaną przekazane do konta magazynu przejściowego jako stronicowe obiekty blob, ale konwersja dysku VHD na dysk zarządzany zakończy się niepowodzeniem. Przejdź do pozycji **Konto magazynu > Obiekty blob** w magazynie przejściowym, a następnie wybierz odpowiedni kontener — SSD w warstwie Standardowa, HDD w warstwie Standardowa lub SSD w warstwie Premium. Wirtualne dyski twarde są przekazywane do przejściowego konta magazynu jako stronicowe obiekty blob.
    
::: zone-end

::: zone target = "chromeless"

## <a name="verify-data-upload-to-azure"></a>Weryfikowanie przekazania danych na platformę Azure

Gdy urządzenie Data Box Heavy zostanie podłączone do sieci w centrum danych platformy Azure, automatycznie rozpocznie się przekazywanie danych na platformę Azure. Usługa Data Box powiadomi Cię o zakończeniu kopiowania danych w witrynie Azure Portal.

- Sprawdź dzienniki błędów pod kątem ewentualnych niepowodzeń i podejmij odpowiednie działania.
- Sprawdź, czy dane znajdują się na kontach magazynu, zanim usuniesz je ze źródła.

::: zone-end

## <a name="erasure-of-data-from-data-box-heavy"></a>Wymazywanie danych z urządzenia Data Box Heavy
 
Po zakończeniu przekazywania danych na platformę Azure dane z dysków urządzenia Data Box są bezpiecznie wymazywane zgodnie z wytycznymi [NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi). Po ukończeniu wymazywania można [pobrać historię zamówień](data-box-portal-admin.md#download-order-history).

::: zone target = "docs"

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące usługi Azure Data Box, takie jak:

> [!div class="checklist"]
> * Wymagania wstępne
> * Przygotowanie do wysłania
> * Wysyłanie urządzenia Data Box Heavy do firmy Microsoft
> * Weryfikowanie przekazania danych na platformę Azure
> * Wymazywanie danych z urządzenia Data Box Heavy

Przejdź do następnego artykułu, aby dowiedzieć się, jak zarządzać urządzeniem Data Box Heavy za pomocą lokalnego internetowego interfejsu użytkownika.

> [!div class="nextstepaction"]
> [Use local web UI to administer Azure Data Box (Administrowanie urządzeniem Azure Data Box za pomocą lokalnego internetowego interfejsu użytkownika)](./data-box-local-web-ui-admin.md)

::: zone-end


