---
title: Instalowanie aktualizacji w macierzy wirtualnej StorSimple | Microsoft Docs
description: Opisuje sposób użycia interfejsu użytkownika sieci Web macierzy wirtualnej StorSimple do zastosowania aktualizacji 0,4 przy użyciu metody Azure Portal i Hot Fix.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/07/2017
ms.author: alkohli
ms.openlocfilehash: 26890aaa7bc2d0ea70ff326e36a5767d1d468d5c
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977548"
---
# <a name="install-update-04-on-your-storsimple-virtual-array"></a>Zainstaluj aktualizację 0,4 w macierzy wirtualnej StorSimple

## <a name="overview"></a>Omówienie

W tym artykule opisano kroki wymagane do zainstalowania aktualizacji 0,4 w macierzy wirtualnej StorSimple za pomocą lokalnego interfejsu użytkownika sieci Web i za pośrednictwem Azure Portal. Aby zapewnić aktualność macierzy wirtualnej StorSimple, należy zastosować aktualizacje lub poprawki oprogramowania. 

Należy pamiętać, że zainstalowanie aktualizacji lub poprawki powoduje ponowne uruchomienie urządzenia. W przypadku, gdy Macierz wirtualna StorSimple jest urządzeniem z jednym węzłem, wszystkie trwające operacji we/wy są zakłócane, a urządzenie nie będzie miało przestoju. 

Przed zastosowaniem aktualizacji zaleca się, aby najpierw przełączyć woluminy lub udziały w tryb offline na hoście, a następnie na urządzeniu. Minimalizuje to ryzyko uszkodzenia danych.

> [!IMPORTANT]
> W przypadku korzystania z aktualizacji 0,1 lub GA wersji oprogramowania należy użyć metody poprawek za pomocą lokalnego interfejsu użytkownika sieci Web, aby zainstalować aktualizację 0,3. W przypadku korzystania z aktualizacji 0,2 lub nowszej zaleca się zainstalowanie aktualizacji za pomocą Azure Portal.
 

## <a name="use-the-local-web-ui"></a>Korzystanie z lokalnego interfejsu użytkownika sieci Web

W przypadku korzystania z lokalnego interfejsu użytkownika sieci Web należy wykonać dwa kroki:

* Pobierz aktualizację lub poprawkę
* Zainstaluj aktualizację lub poprawkę

### <a name="download-the-update-or-the-hotfix"></a>Pobierz aktualizację lub poprawkę

Wykonaj następujące kroki, aby pobrać aktualizację oprogramowania z Wykazu usługi Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Aby pobrać aktualizację lub poprawkę

1. Uruchom program Internet Explorer i przejdź do [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) .

2. Jeśli po raz pierwszy używasz Wykazu usługi Microsoft Update na danym komputerze, po wyświetleniu monitu o zainstalowanie dodatku Wykazu usługi Microsoft Update kliknij pozycję **Zainstaluj**.

3. W polu wyszukiwania w wykazie Microsoft Update wprowadź numer bazy wiedzy (KB) poprawki, którą chcesz pobrać. Wprowadź **3216577** dla aktualizacji 0,4, a następnie kliknij przycisk **Wyszukaj**.
   
    Zostanie wyświetlona lista poprawek, na przykład **StorSimple Virtual Array Update 0,4**.
   
    ![Przeszukiwanie wykazu](./media/storsimple-virtual-array-install-update-04/download1.png)

4. Kliknij pozycję **Dodaj**. Aktualizacja zostanie dodana do koszyka.

5. Kliknij pozycję **Wyświetl koszyk**.

6. Kliknij pozycję **Pobierz**. Określ lokalizację lokalną, do której mają trafiać pobrane pliki, albo **przejdź** do takiej lokalizacji. Aktualizacje zostaną pobrane do wskazanej lokalizacji i umieszczone w podfolderze o nazwie takiej samej jak aktualizacja. Folder można też skopiować do udziału sieciowego osiągalnego z urządzenia.

7. Otwórz skopiowany folder, który powinien zostać wyświetlony Microsoft Update autonomiczny plik pakietu `WindowsTH-KB3011067-x64` . Ten plik jest używany do instalowania aktualizacji lub poprawki.

### <a name="install-the-update-or-the-hotfix"></a>Zainstaluj aktualizację lub poprawkę

Przed zainstalowaniem aktualizacji lub poprawki upewnij się, że masz aktualizację lub poprawkę pobraną lokalnie na hoście lub dostępną za pośrednictwem udziału sieciowego. 

Ta metoda służy do instalowania aktualizacji na urządzeniu z oprogramowaniem w wersji GA lub Update 0,1. Wykonanie tej procedury trwa krócej niż 2 minuty. Wykonaj następujące kroki, aby zainstalować aktualizację lub poprawkę.

#### <a name="to-install-the-update-or-the-hotfix"></a>Aby zainstalować aktualizację lub poprawkę

1. W lokalnym interfejsie użytkownika sieci Web przejdź do pozycji **konserwacja**  >  **Aktualizowanie oprogramowania**.
   
    ![Aktualizacja urządzenia 1](./media/storsimple-virtual-array-install-update/update1m.png)

2. W polu **ścieżka pliku aktualizacji**wprowadź nazwę pliku aktualizacji lub poprawki. Możesz również przejść do pliku instalacyjnego aktualizacji lub poprawek, jeśli znajduje się on w udziale sieciowym. Kliknij pozycję **Zastosuj**.
   
    ![Aktualizacja urządzenia 2](./media/storsimple-virtual-array-install-update/update2m.png)

3. Wyświetlane jest ostrzeżenie. Po zastosowaniu tej operacji urządzenie jest uruchamiane jednym węzłem, a następnie zostanie ponownie uruchomione i wystąpiło przestoje. Kliknij ikonę zaznaczania.
   
   ![Aktualizacja urządzenia 3](./media/storsimple-virtual-array-install-update/update3m.png)

4. Rozpocznie się aktualizacja. Po pomyślnym zaktualizowaniu urządzenia zostanie ono ponownie uruchomione. Lokalny interfejs użytkownika nie jest dostępny w tym czasie trwania.
   
    ![Aktualizacja urządzenia 4](./media/storsimple-virtual-array-install-update/update5m.png)

5. Po ponownym uruchomieniu nastąpi przekierowanie do strony **logowania** . Aby sprawdzić, czy oprogramowanie urządzenia zostało zaktualizowane, w lokalnym interfejsie użytkownika sieci Web przejdź do pozycji **konserwacja**  >  **Aktualizowanie oprogramowania**. Wyświetlana wersja oprogramowania powinna być **10.0.0.0.0.10289.0** dla aktualizacji 0,4.
   
   > [!NOTE]
   > Firma Microsoft zgłasza wersje oprogramowania w nieco inny sposób w lokalnym interfejsie użytkownika sieci Web i Azure Portal. Na przykład lokalny interfejs użytkownika sieci Web raportuje **10.0.0.0.0.10289** oraz Azure Portal raporty **10.0.10289.0** dla tej samej wersji.
   
    ![Aktualizacja urządzenia 5](./media/storsimple-virtual-array-install-update/update6m.png)

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

W przypadku korzystania z aktualizacji 0,2 i nowszych zaleca się zainstalowanie aktualizacji za pomocą Azure Portal. Procedura portalu wymaga od użytkownika skanowania, pobrania i zainstalowania aktualizacji. Wykonanie tej procedury zajmuje około 7 minut. Wykonaj następujące kroki, aby zainstalować aktualizację lub poprawkę.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

Po zakończeniu instalacji (wskazywanym przez stan zadania o 100%) przejdź do usługi StorSimple Menedżer urządzeń. Wybierz pozycję **urządzenia** , a następnie wybierz i kliknij urządzenie, które chcesz zaktualizować z listy urządzeń podłączonych do tej usługi. W bloku **Ustawienia** przejdź do sekcji **Zarządzanie** , a następnie wybierz pozycję **aktualizacje urządzeń**. Wyświetlana wersja oprogramowania powinna być **10.0.10289.0**.


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej [na temat administrowania wirtualną macierzą StorSimple](storsimple-ova-web-ui-admin.md).

