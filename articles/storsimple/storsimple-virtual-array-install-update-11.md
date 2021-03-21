---
title: Zainstaluj aktualizację 1,1 w macierzy wirtualnej StorSimple | Microsoft Docs
description: Opisuje sposób stosowania aktualizacji przy użyciu Azure Portal i lokalnego interfejsu użytkownika sieci Web dla macierzy wirtualnej StorSimple
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/18/2018
ms.author: alkohli
ms.openlocfilehash: 709794dcccf9b100cbb11381ac34cfb86238b22b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94533995"
---
# <a name="install-update-11-on-your-storsimple-virtual-array"></a>Zainstaluj aktualizację 1,1 w macierzy wirtualnej StorSimple

## <a name="overview"></a>Omówienie

W tym artykule opisano kroki wymagane do zainstalowania aktualizacji 1,1 w macierzy wirtualnej StorSimple za pomocą lokalnego interfejsu użytkownika sieci Web i za pośrednictwem Azure Portal.

Aby zapewnić aktualność macierzy wirtualnej StorSimple, należy zastosować aktualizacje lub poprawki oprogramowania. Przed zastosowaniem aktualizacji zaleca się, aby najpierw przełączyć woluminy lub udziały w tryb offline na hoście, a następnie na urządzeniu. Minimalizuje to ryzyko uszkodzenia danych. Gdy woluminy lub udziały są w trybie offline, należy również ręcznie wykonać kopię zapasową urządzenia.

> [!IMPORTANT]
> - Aktualizacja 1,1 jest zgodna z wersją oprogramowania **10.0.10307.0** na urządzeniu. Aby uzyskać informacje na temat Nowości w tej aktualizacji, przejdź do informacji o [wersji aktualizacji 1,1](storsimple-virtual-array-update-11-release-notes.md).
>
> - Należy pamiętać, że zainstalowanie aktualizacji lub poprawki powoduje ponowne uruchomienie urządzenia. W przypadku, gdy Macierz wirtualna StorSimple jest urządzeniem z jednym węzłem, wszystkie trwające operacji we/wy są zakłócane, a urządzenie nie będzie miało przestoju.
>
> - Aktualizacja 1,1 jest dostępna w Azure Portal tylko wtedy, gdy Macierz wirtualna ma uruchomioną aktualizację Update 1. W przypadku macierzy wirtualnych z aktualizacją 0,6 wersji należy najpierw zainstalować aktualizację 1,0, a następnie zastosować aktualizację 1,1.

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

W przypadku korzystania z aktualizacji 0,2 i nowszych zaleca się zainstalowanie aktualizacji za pomocą Azure Portal. Procedura portalu wymaga od użytkownika skanowania, pobrania i zainstalowania aktualizacji. W zależności od wersji oprogramowania, która jest uruchomiona w macierzy wirtualnej, zastosowanie aktualizacji za pomocą Azure Portal jest inne.

 - Jeśli w macierzy wirtualnej uruchomiono aktualizację Update 1, Azure Portal bezpośrednio zainstaluje aktualizację 1,1 (10.0.10307.0) na urządzeniu. Wykonanie tej procedury zajmuje około 10 minut.
 - Jeśli w macierzy wirtualnej jest uruchomiona Aktualizacja 0,6, aktualizacja odbywa się na dwa etapy. Azure Portal najpierw zainstaluje aktualizację 1,0 (10.0.10296.0) na urządzeniu. Ponowne uruchomienie macierzy wirtualnej i portalu spowoduje zainstalowanie na urządzeniu aktualizacji 1,1 (10.0.10307.0). Wykonanie tej procedury zajmuje około 15 minut.


[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-11.md)]

Po zakończeniu instalacji przejdź do usługi StorSimple Device Manager. Wybierz pozycję **urządzenia** , a następnie wybierz i kliknij urządzenie, które właśnie zostało zaktualizowane. Przejdź do pozycji **Ustawienia, > zarządzanie aktualizacjami > urządzeń**. Wyświetlana wersja oprogramowania powinna być **10.0.10307.0**.

![Wersja oprogramowania po aktualizacji](./media/storsimple-virtual-array-install-update-11/azupdate17m2.png)

## <a name="use-the-local-web-ui"></a>Korzystanie z lokalnego interfejsu użytkownika sieci Web

W przypadku korzystania z lokalnego interfejsu użytkownika sieci Web należy wykonać dwa kroki:

* Pobierz aktualizację lub poprawkę
* Zainstaluj aktualizację lub poprawkę

> [!IMPORTANT] 
> **Kontynuuj tę aktualizację tylko wtedy, gdy korzystasz z aktualizacji Update 1 (10.0.10296.0). Jeśli używasz aktualizacji 0,6, najpierw [Zainstaluj aktualizację Update 1](storsimple-virtual-array-install-update-1.md) na urządzeniu, a następnie zastosuj aktualizację 1,1.**

### <a name="download-the-update-or-the-hotfix"></a>Pobierz aktualizację lub poprawkę

Wykonaj następujące kroki, aby pobrać aktualizację 1,1 z wykazu Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Aby pobrać aktualizację lub poprawkę

1. Uruchom program Internet Explorer i przejdź do [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) .

2. Jeśli używasz wykazu Microsoft Update po raz pierwszy na tym komputerze, kliknij przycisk **Zainstaluj** po wyświetleniu monitu, aby zainstalować dodatek katalogu Microsoft Update.

3. W polu wyszukiwania w wykazie Microsoft Update wprowadź numer bazy wiedzy (KB) poprawki, którą chcesz pobrać. Wprowadź **4337628** dla aktualizacji 1,1, a następnie kliknij przycisk **Wyszukaj**.
   
    Zostanie wyświetlona lista poprawek, na przykład **StorSimple Virtual Array Update 1,1**.
   
    ![Przeszukiwanie wykazu](./media/storsimple-virtual-array-install-update-11/download1.png)

4. Kliknij pozycję **Pobierz**.

5. Pobierz dwa pliki do folderu. Możesz również skopiować folder do udziału sieciowego, który jest osiągalny z urządzenia.

6. Otwórz folder, w którym znajdują się pliki.

    ![Pliki w pakiecie](./media/storsimple-virtual-array-install-update-11/update01folder.png)

    Zobaczysz dwa pliki:
    -  Microsoft Update autonomiczny plik pakietu `WindowsTH-KB3011067-x64` . Ten plik jest używany do aktualizacji oprogramowania urządzenia.
    - Plik zawierający aktualizacje zbiorcze dla czerwca `Windows8.1-KB4284815-x64` . Aby uzyskać więcej informacji na temat tego, co jest zawarte w tym pakiecie zbiorczym, przejdź do [miesięcznego zestawienia zabezpieczeń](https://support.microsoft.com/help/4284815/windows-81-update-kb4284815).

### <a name="install-the-update-or-the-hotfix"></a>Zainstaluj aktualizację lub poprawkę

Przed zainstalowaniem aktualizacji lub poprawki upewnij się, że:

 - Aktualizacja lub poprawka jest pobierana lokalnie na hoście lub dostępna za pośrednictwem udziału sieciowego.
 - W macierzy wirtualnej uruchomiono aktualizację Update 1 (10.0.10296.0). W przypadku korzystania z aktualizacji 0,6 najpierw należy [zainstalować aktualizację Update 1](storsimple-virtual-array-install-update-1.md) , a następnie zainstalować aktualizację 1,1.

Wykonanie tej procedury zajmuje około 4 minuty. Wykonaj następujące kroki, aby zainstalować aktualizację lub poprawkę.

#### <a name="to-install-the-update-or-the-hotfix"></a>Aby zainstalować aktualizację lub poprawkę

1. W lokalnym interfejsie użytkownika sieci Web przejdź do pozycji **konserwacja**  >  **Aktualizowanie oprogramowania**. Zanotuj wersję oprogramowania, która jest uruchamiana. **Kontynuuj tę aktualizację tylko wtedy, gdy korzystasz z aktualizacji Update 1 (10.0.10296.0). Jeśli używasz aktualizacji 0,6, najpierw [Zainstaluj aktualizację Update 1](storsimple-virtual-array-install-update-1.md) na urządzeniu, a następnie zastosuj aktualizację 1,1.**
   
    ![Zrzut ekranu przedstawia aktualizację oprogramowania wybraną z menu obsługa.](./media/storsimple-virtual-array-install-update-11/update1m.png)

2. W polu **ścieżka pliku aktualizacji** wprowadź nazwę pliku aktualizacji lub poprawki. Możesz również przejść do pliku instalacyjnego aktualizacji lub poprawek, jeśli znajduje się on w udziale sieciowym. Kliknij pozycję **Zastosuj**.
   
    ![Zrzut ekranu przedstawia pole tekstowe Aktualizuj ścieżkę pliku na stronie aktualizacji oprogramowania.](./media/storsimple-virtual-array-install-update-11/update2m.png)

3. Wyświetlane jest ostrzeżenie. Po zastosowaniu aktualizacji Macierz wirtualna jest urządzeniem z jednym węzłem, a następnie następuje ponowne uruchomienie urządzenia i wystąpiła przestoje. Kliknij ikonę zaznaczania.
   
   ![Zrzut ekranu przedstawia ostrzeżenie okna dialogowego o przestoju.](./media/storsimple-virtual-array-install-update-11/update3m.png)

4. Rozpocznie się aktualizacja. Po pomyślnym zaktualizowaniu urządzenia zostanie ono ponownie uruchomione. Lokalny interfejs użytkownika nie jest dostępny w tym czasie trwania.
   
    ![Zrzut ekranu przedstawia komunikat o powodzeniu dla aktualizacji.](./media/storsimple-virtual-array-install-update-11/update5m.png)

5. Po ponownym uruchomieniu nastąpi przekierowanie do strony **logowania** . Aby sprawdzić, czy oprogramowanie urządzenia zostało zaktualizowane, w lokalnym interfejsie użytkownika sieci Web przejdź do pozycji **konserwacja**  >  **Aktualizowanie oprogramowania**. Wyświetlana wersja oprogramowania powinna być **10.0.0.0.0.10307** dla aktualizacji 1,1.
   
   > [!NOTE]
   > Firma Microsoft zgłasza wersje oprogramowania w nieco inny sposób w lokalnym interfejsie użytkownika sieci Web i Azure Portal. Na przykład lokalny interfejs użytkownika sieci Web raportuje **10.0.0.0.0.10307** oraz Azure Portal raporty **10.0.10307.0** dla tej samej wersji.
   
    ![Zrzut ekranu przedstawia stronę aktualizacji oprogramowania z bieżącą wersją oprogramowania.](./media/storsimple-virtual-array-install-update-11/update6m.png)

6. Powtórz kroki 2-4, aby zainstalować poprawkę zabezpieczeń systemu Windows przy użyciu pliku `Windows8.1-KB4284815-x64` . Macierz wirtualna jest uruchamiana ponownie po instalacji i należy zalogować się do lokalnego interfejsu użytkownika sieci Web.


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej [na temat administrowania wirtualną macierzą StorSimple](storsimple-ova-web-ui-admin.md).
