---
title: Instalowanie aktualizacji w Microsoft Azure StorSimple macierzy wirtualnej | Microsoft Docs
description: Opis używania interfejsu użytkownika sieci Web macierzy wirtualnej StorSimple do zastosowania aktualizacji przy użyciu portalu i metody hotfix
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 9997a97b-9382-43ed-b56e-61369335c987
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3bd6f298ad2bb01503492b52c2d50dec82ec0ca5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94534053"
---
# <a name="install-updates-on-your-storsimple-virtual-array---azure-portal"></a>Zainstaluj aktualizacje w wirtualnej macierzy StorSimple — Azure Portal

## <a name="overview"></a>Omówienie

W tym artykule opisano kroki wymagane do zainstalowania aktualizacji w macierzy wirtualnej StorSimple za pośrednictwem lokalnego interfejsu użytkownika sieci Web i za pośrednictwem Azure Portal. Aby zapewnić aktualność macierzy wirtualnej StorSimple, należy zastosować aktualizacje lub poprawki oprogramowania. 

Należy pamiętać, że zainstalowanie aktualizacji lub poprawki powoduje ponowne uruchomienie urządzenia. W przypadku, gdy Macierz wirtualna StorSimple jest urządzeniem z jednym węzłem, wszystkie trwające operacji we/wy są zakłócane, a urządzenie nie będzie miało przestoju. 

Przed zastosowaniem aktualizacji zaleca się, aby najpierw przełączyć woluminy lub udziały w tryb offline na hoście, a następnie na urządzeniu. Minimalizuje to ryzyko uszkodzenia danych.

> [!IMPORTANT]
> W przypadku korzystania z aktualizacji 0,1 lub GA wersji oprogramowania należy użyć metody poprawek za pomocą lokalnego interfejsu użytkownika sieci Web, aby zainstalować aktualizację 0,3. W przypadku korzystania z aktualizacji 0,2 zalecamy zainstalowanie aktualizacji za pośrednictwem klasycznego portalu Azure.
 

## <a name="use-the-local-web-ui"></a>Korzystanie z lokalnego interfejsu użytkownika sieci Web

W przypadku korzystania z lokalnego interfejsu użytkownika sieci Web należy wykonać dwa kroki:

* Pobierz aktualizację lub poprawkę
* Zainstaluj aktualizację lub poprawkę

### <a name="download-the-update-or-the-hotfix"></a>Pobierz aktualizację lub poprawkę

Wykonaj następujące kroki, aby pobrać aktualizację oprogramowania z Wykazu usługi Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Aby pobrać aktualizację lub poprawkę

1. Uruchom program Internet Explorer i przejdź do [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) .

2. Jeśli po raz pierwszy używasz Wykazu usługi Microsoft Update na danym komputerze, po wyświetleniu monitu o zainstalowanie dodatku Wykazu usługi Microsoft Update kliknij pozycję **Zainstaluj**.

3. W polu wyszukiwania w wykazie Microsoft Update wprowadź numer bazy wiedzy (KB) poprawki, którą chcesz pobrać. Wprowadź **3182061** dla aktualizacji 0,3, a następnie kliknij przycisk **Wyszukaj**.
   
    Zostanie wyświetlona lista poprawek, na przykład **StorSimple Virtual Array Update 0,3**.
   
    ![Przeszukiwanie wykazu](./media/storsimple-virtual-array-install-update/download1.png)

4. Kliknij pozycję **Dodaj**. Aktualizacja zostanie dodana do koszyka.

5. Kliknij pozycję **Wyświetl koszyk**.

6. Kliknij pozycję **Pobierz**. Określ lokalizację lokalną, do której mają trafiać pobrane pliki, albo **przejdź** do takiej lokalizacji. Aktualizacje zostaną pobrane do wskazanej lokalizacji i umieszczone w podfolderze o nazwie takiej samej jak aktualizacja. Folder można też skopiować do udziału sieciowego osiągalnego z urządzenia.

7. Otwórz skopiowany folder, który powinien zostać wyświetlony Microsoft Update autonomiczny plik pakietu `WindowsTH-KB3011067-x64` . Ten plik jest używany do instalowania aktualizacji lub poprawki.

### <a name="install-the-update-or-the-hotfix"></a>Zainstaluj aktualizację lub poprawkę

Przed zainstalowaniem aktualizacji lub poprawki upewnij się, że masz aktualizację lub poprawkę pobraną lokalnie na hoście lub dostępną za pośrednictwem udziału sieciowego. 

Ta metoda służy do instalowania aktualizacji na urządzeniu z oprogramowaniem w wersji GA lub Update 0,1. Wykonanie tej procedury trwa krócej niż 2 minuty. Wykonaj następujące kroki, aby zainstalować aktualizację lub poprawkę.

#### <a name="to-install-the-update-or-the-hotfix"></a>Aby zainstalować aktualizację lub poprawkę

1. W lokalnym interfejsie użytkownika sieci Web przejdź do pozycji **konserwacja**  >  **Aktualizowanie oprogramowania**.
   
    ![Zrzut ekranu przedstawia aktualizację oprogramowania wybraną z menu obsługa.](./media/storsimple-virtual-array-install-update/update1m.png)

2. W polu **ścieżka pliku aktualizacji** wprowadź nazwę pliku aktualizacji lub poprawki. Możesz również przejść do pliku instalacyjnego aktualizacji lub poprawek, jeśli znajduje się on w udziale sieciowym. Kliknij pozycję **Zastosuj**.
   
    ![Zrzut ekranu przedstawia pole tekstowe Aktualizuj ścieżkę pliku na stronie aktualizacji oprogramowania.](./media/storsimple-virtual-array-install-update/update2m.png)

3. Wyświetlane jest ostrzeżenie. Po zastosowaniu tej operacji urządzenie jest uruchamiane jednym węzłem, a następnie zostanie ponownie uruchomione i wystąpiło przestoje. Kliknij ikonę zaznaczania.
   
   ![Zrzut ekranu przedstawia ostrzeżenie okna dialogowego o przestoju.](./media/storsimple-virtual-array-install-update/update3m.png)

4. Rozpocznie się aktualizacja. Po pomyślnym zaktualizowaniu urządzenia zostanie ono ponownie uruchomione. Lokalny interfejs użytkownika nie jest dostępny w tym czasie trwania.
   
    ![Zrzut ekranu przedstawia komunikat o powodzeniu dla aktualizacji.](./media/storsimple-virtual-array-install-update/update5m.png)

5. Po ponownym uruchomieniu nastąpi przekierowanie do strony **logowania** . Aby sprawdzić, czy oprogramowanie urządzenia zostało zaktualizowane, w lokalnym interfejsie użytkownika sieci Web przejdź do pozycji **konserwacja**  >  **Aktualizowanie oprogramowania**. Wyświetlana wersja oprogramowania powinna być **10.0.0.0.0.10288.0** dla aktualizacji 0,3.
   
   > [!NOTE]
   > Firma Microsoft zgłasza wersje oprogramowania w nieco inny sposób w lokalnym interfejsie użytkownika sieci Web i Azure Portal. Na przykład lokalny interfejs użytkownika sieci Web raportuje **10.0.0.0.0.10288** oraz Azure Portal raporty **10.0.10288.0** dla tej samej wersji.
   
    ![Zrzut ekranu przedstawia stronę aktualizacji oprogramowania z bieżącą wersją oprogramowania.](./media/storsimple-virtual-array-install-update/update6m.png)

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

W przypadku uruchamiania aktualizacji 0,2 zalecamy zainstalowanie aktualizacji za pomocą Azure Portal. Procedura portalu wymaga od użytkownika skanowania, pobrania i zainstalowania aktualizacji. Wykonanie tej procedury zajmuje około 7 minut. Wykonaj następujące kroki, aby zainstalować aktualizację lub poprawkę.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal.md)]

Po zakończeniu instalacji (wskazywanym przez stan zadania o 100%) przejdź do usługi StorSimple Device Manager. Wybierz pozycję **urządzenia** , a następnie wybierz i kliknij urządzenie, które chcesz zaktualizować z listy urządzeń podłączonych do tej usługi. W bloku **Ustawienia** przejdź do sekcji **Zarządzanie** , a następnie wybierz pozycję **aktualizacje urządzeń**. Wyświetlana wersja oprogramowania powinna być **10.0.10288.0**.


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej [na temat administrowania wirtualną macierzą StorSimple](storsimple-ova-web-ui-admin.md).

