---
title: Zainstaluj aktualizację 1,0 w macierzy wirtualnej StorSimple | Microsoft Docs
description: Opisuje sposób użycia interfejsu użytkownika sieci Web macierzy wirtualnej StorSimple do zastosowania aktualizacji 1,0 przy użyciu metody Azure Portal i Hot Fix.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: d05395f97aded0b57780f898e9cba6ddca491f95
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96005825"
---
# <a name="install-update-10-on-your-storsimple-virtual-array"></a>Zainstaluj aktualizację 1,0 w macierzy wirtualnej StorSimple

## <a name="overview"></a>Omówienie

W tym artykule opisano kroki wymagane do zainstalowania aktualizacji 1,0 w macierzy wirtualnej StorSimple za pomocą lokalnego interfejsu użytkownika sieci Web i za pośrednictwem Azure Portal.

Aby zapewnić aktualność macierzy wirtualnej StorSimple, należy zastosować aktualizacje lub poprawki oprogramowania. Przed zastosowaniem aktualizacji zaleca się, aby najpierw przełączyć woluminy lub udziały w tryb offline na hoście, a następnie na urządzeniu. Minimalizuje to ryzyko uszkodzenia danych. Gdy woluminy lub udziały są w trybie offline, należy również ręcznie wykonać kopię zapasową urządzenia.

> [!IMPORTANT]
>
> - Aktualizacja 1,0 jest zgodna z wersją oprogramowania **10.0.10296.0** na urządzeniu. Aby uzyskać informacje na temat Nowości w tej aktualizacji, przejdź do informacji o [wersji aktualizacji 1,0](storsimple-virtual-array-update-1-release-notes.md).
>
> - Należy pamiętać, że zainstalowanie aktualizacji lub poprawki powoduje ponowne uruchomienie urządzenia. W przypadku, gdy Macierz wirtualna StorSimple jest urządzeniem z jednym węzłem, wszystkie trwające operacji we/wy są zakłócane, a urządzenie nie będzie miało przestoju.
>
> - Aktualizacja Update 1 jest dostępna w Azure Portal tylko wtedy, gdy Macierz wirtualna korzysta z aktualizacji 0,6. W przypadku macierzy wirtualnych korzystających z wersji pre-Update 0,6 należy najpierw zainstalować aktualizację 0,6, a następnie zainstalować aktualizację Update 1.

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

W przypadku korzystania z aktualizacji 0,2 i nowszych zaleca się zainstalowanie aktualizacji za pomocą Azure Portal. Procedura portalu wymaga od użytkownika skanowania, pobrania i zainstalowania aktualizacji. W zależności od wersji oprogramowania, która jest uruchomiona w macierzy wirtualnej, zastosowanie aktualizacji za pomocą Azure Portal jest inne.

 - Jeśli w macierzy wirtualnej uruchomiono aktualizację 0,6, Azure Portal bezpośrednio zainstaluje aktualizację Update 1 (10.0.10296.0) na urządzeniu. Wykonanie tej procedury zajmuje około 7 minut.
 - Jeśli w macierzy wirtualnej uruchomiono wersję wcześniejszą niż aktualizacja 0,6, aktualizacja odbywa się na dwa etapy. Azure Portal najpierw zainstaluje aktualizację 0,6 (10.0.10293.0) na urządzeniu. Ponowny rozruch macierzy wirtualnej, a następnie Portal zainstaluje aktualizację Update 1 (10.0.10296.0) na urządzeniu. Wykonanie tej procedury zajmuje około 15 minut.


[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-1.md)]

Po zakończeniu instalacji przejdź do usługi StorSimple Device Manager. Wybierz pozycję **urządzenia** , a następnie wybierz i kliknij urządzenie, które właśnie zostało zaktualizowane. Przejdź do pozycji **Ustawienia, > zarządzanie aktualizacjami > urządzeń**. Wyświetlana wersja oprogramowania powinna być **10.0.10296.0**.

![Wersja oprogramowania po aktualizacji](./media/storsimple-virtual-array-install-update-1/azupdate17m1.png)

## <a name="use-the-local-web-ui"></a>Korzystanie z lokalnego interfejsu użytkownika sieci Web

W przypadku korzystania z lokalnego interfejsu użytkownika sieci Web należy wykonać dwa kroki:

* Pobierz aktualizację lub poprawkę
* Zainstaluj aktualizację lub poprawkę

> [!IMPORTANT] 
> **Kontynuuj tę aktualizację tylko wtedy, gdy korzystasz z aktualizacji 0,6 (10.0.10293.0). Jeśli używasz wcześniejszej wersji, najpierw [Zainstaluj aktualizację 0,6](storsimple-virtual-array-install-update-06.md) na urządzeniu, a następnie Zastosuj aktualizację Update 1.**

### <a name="download-the-update-or-the-hotfix"></a>Pobierz aktualizację lub poprawkę

Jeśli w macierzy wirtualnej działa aktualizacja 0,6, wykonaj następujące kroki, aby pobrać aktualizację Update 1 z katalogu Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Aby pobrać aktualizację lub poprawkę

1. Uruchom program Internet Explorer i przejdź do [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) .

2. Jeśli używasz wykazu Microsoft Update po raz pierwszy na tym komputerze, kliknij przycisk **Zainstaluj** po wyświetleniu monitu, aby zainstalować dodatek katalogu Microsoft Update.

3. W polu wyszukiwania w wykazie Microsoft Update wprowadź numer bazy wiedzy (KB) poprawki, którą chcesz pobrać. Wprowadź **4047203** dla aktualizacji 1,0, a następnie kliknij przycisk **Wyszukaj**.
   
    Zostanie wyświetlona lista poprawek, na przykład **StorSimple Virtual Array Update 1,0**.
   
    ![Przeszukiwanie wykazu](./media/storsimple-virtual-array-install-update-1/download1.png)

4. Kliknij pozycję **Pobierz**.

5. Pobierz dwa pliki do folderu. Możesz również skopiować folder do udziału sieciowego, który jest osiągalny z urządzenia.

6. Otwórz folder, w którym znajdują się pliki.

    ![Pliki w pakiecie](./media/storsimple-virtual-array-install-update-1/update01folder.png)

    Zobaczysz dwa pliki:
    -  Microsoft Update autonomiczny plik pakietu `WindowsTH-KB3011067-x64` . Ten plik jest używany do aktualizacji oprogramowania urządzenia.
    - Plik zawierający aktualizacje zbiorcze dla sierpnia `windows8.1-kb4034681-x64` . Aby uzyskać więcej informacji na temat tego, co jest zawarte w tym pakiecie zbiorczym, przejdź do [comiesięcznego zestawienia zabezpieczeń w sierpniu](https://support.microsoft.com/help/4034681/windows-8-1-windows-server-2012-r2-update-kb40346810).

### <a name="install-the-update-or-the-hotfix"></a>Zainstaluj aktualizację lub poprawkę

Przed zainstalowaniem aktualizacji lub poprawki upewnij się, że:

 - Aktualizacja lub poprawka jest pobierana lokalnie na hoście lub dostępna za pośrednictwem udziału sieciowego.
 - W macierzy wirtualnej działa aktualizacja 0,6 (10.0.10293.0). W przypadku korzystania z wersji wcześniejszej niż aktualizacja 0,6 należy najpierw [zainstalować aktualizację update 0,6](storsimple-virtual-array-install-update-06.md) , a następnie zainstalować aktualizację Update 1.

Wykonanie tej procedury zajmuje około 4 minuty. Wykonaj następujące kroki, aby zainstalować aktualizację lub poprawkę.

#### <a name="to-install-the-update-or-the-hotfix"></a>Aby zainstalować aktualizację lub poprawkę

1. W lokalnym interfejsie użytkownika sieci Web przejdź do pozycji **konserwacja**  >  **Aktualizowanie oprogramowania**. Zanotuj wersję oprogramowania, która jest uruchamiana. **Kontynuuj tę aktualizację tylko wtedy, gdy korzystasz z aktualizacji 0,6 (10.0.10293.0). Jeśli używasz wcześniejszej wersji, najpierw [Zainstaluj aktualizację 0,6](storsimple-virtual-array-install-update-06.md) na urządzeniu, a następnie Zastosuj aktualizację Update 1.**
   
    ![Zrzut ekranu przedstawia aktualizację oprogramowania wybraną z menu obsługa.](./media/storsimple-virtual-array-install-update-1/update1m.png)

2. W polu **ścieżka pliku aktualizacji** wprowadź nazwę pliku aktualizacji lub poprawki. Możesz również przejść do pliku instalacyjnego aktualizacji lub poprawek, jeśli znajduje się on w udziale sieciowym. Kliknij pozycję **Zastosuj**.
   
    ![Zrzut ekranu przedstawia bieżącą wersję oprogramowania i pole tekstowe Aktualizuj ścieżkę pliku na stronie aktualizacji oprogramowania.](./media/storsimple-virtual-array-install-update-1/update2m.png)

3. Wyświetlane jest ostrzeżenie. Po zastosowaniu aktualizacji Macierz wirtualna jest urządzeniem z jednym węzłem, a następnie następuje ponowne uruchomienie urządzenia i wystąpiła przestoje. Kliknij ikonę zaznaczania.
   
   ![Zrzut ekranu przedstawia ostrzeżenie okna dialogowego o przestoju.](./media/storsimple-virtual-array-install-update-1/update3m.png)

4. Rozpocznie się aktualizacja. Po pomyślnym zaktualizowaniu urządzenia zostanie ono ponownie uruchomione. Lokalny interfejs użytkownika nie jest dostępny w tym czasie trwania.
   
    ![Zrzut ekranu przedstawia komunikat o powodzeniu dla aktualizacji.](./media/storsimple-virtual-array-install-update-1/update5m.png)

5. Po ponownym uruchomieniu nastąpi przekierowanie do strony **logowania** . Aby sprawdzić, czy oprogramowanie urządzenia zostało zaktualizowane, w lokalnym interfejsie użytkownika sieci Web przejdź do pozycji **konserwacja**  >  **Aktualizowanie oprogramowania**. Wyświetlana wersja oprogramowania powinna być **10.0.0.0.0.10296** dla aktualizacji 1,0.
   
   > [!NOTE]
   > Firma Microsoft zgłasza wersje oprogramowania w nieco inny sposób w lokalnym interfejsie użytkownika sieci Web i Azure Portal. Na przykład lokalny interfejs użytkownika sieci Web raportuje **10.0.0.0.0.10296** oraz Azure Portal raporty **10.0.10296.0** dla tej samej wersji.
   
    ![Zrzut ekranu przedstawia stronę aktualizacji oprogramowania z bieżącą wersją oprogramowania.](./media/storsimple-virtual-array-install-update-1/update6m.png)

6. Powtórz kroki 2-4, aby zainstalować poprawkę zabezpieczeń systemu Windows przy użyciu pliku `windows8.1-kb4012213-x64` . Macierz wirtualna jest uruchamiana ponownie po instalacji i należy zalogować się do lokalnego interfejsu użytkownika sieci Web.

> [!NOTE]
> Jeśli aktualizacja Update 1 została zastosowana bezpośrednio do urządzenia z wersją wcześniejszą niż aktualizacja 0,6, nie ma żadnych aktualizacji. Aby uzyskać kolejne kroki, skontaktuj się z pomoc techniczna firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej [na temat administrowania wirtualną macierzą StorSimple](storsimple-ova-web-ui-admin.md).
