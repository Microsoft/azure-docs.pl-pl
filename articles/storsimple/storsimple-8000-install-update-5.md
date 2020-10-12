---
title: Zainstaluj aktualizację Update 5 na urządzeniu z serii StorSimple 8000 | Microsoft Docs
description: Wyjaśniono, jak zainstalować aktualizację StorSimple 8000 serii 5 na urządzeniu z serii StorSimple 8000.
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
ms.date: 11/13/2017
ms.author: alkohli
ms.openlocfilehash: bbac6eade634ffcfdc47ae3d22b32e0bd429b7c6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85513184"
---
# <a name="install-update-5-on-your-storsimple-device"></a>Zainstaluj aktualizację Update 5 na urządzeniu StorSimple

## <a name="overview"></a>Omówienie

W tym samouczku wyjaśniono, jak zainstalować aktualizację Update 5 na urządzeniu StorSimple z uruchomioną wcześniejszą wersją oprogramowania za pośrednictwem Azure Portal i przy użyciu metody Hotfix. Metoda Hotfix jest używana podczas próby zainstalowania aktualizacji Update 5 na urządzeniu z uruchomioną wersją pre-Update 3. Metoda Hotfix jest również używana, gdy Brama jest skonfigurowana w interfejsie sieciowym innym niż dane 0 urządzenia StorSimple, a użytkownik próbuje przeprowadzić aktualizację z wersji wstępnej Update 1.

Aktualizacja Update 5 obejmuje oprogramowanie Device, Storport i Spaceport, aktualizacje zabezpieczeń systemu operacyjnego oraz aktualizacje systemu operacyjnego oraz aktualizacje oprogramowania układowego dysku.  Aktualizacje oprogramowania, Spaceport, Storport, Security i innych systemów operacyjnych są niezakłócone. Niezakłócone lub regularne aktualizacje można zastosować za pośrednictwem Azure Portal lub przy użyciu metody Hotfix. Aktualizacje oprogramowania układowego dysku są aktualizacjami przerywanymi i są stosowane, gdy urządzenie jest w trybie konserwacji za pomocą metody hotfix przy użyciu interfejsu programu Windows PowerShell na urządzeniu.

> [!IMPORTANT]
> * Aktualizacja Update 5 to wymagana aktualizacja, która powinna zostać zainstalowana natychmiast. Aby uzyskać więcej informacji, zobacz [Informacje o wersji Update 5](storsimple-update5-release-notes.md).
> * Przed zainstalowaniem zestaw ręcznych i automatycznych testów jest przeprowadzany przed rozpoczęciem instalacji w celu określenia kondycji urządzenia pod kątem stanu sprzętu i połączenia sieciowego. Te wstępne sprawdzenia są wykonywane tylko w przypadku zastosowania aktualizacji z Azure Portal.
> * Zdecydowanie zalecamy, aby podczas aktualizowania urządzenia z wersjami wcześniejszymi niż Update 3 zainstalować aktualizacje przy użyciu metody Hotfix. Jeśli napotkasz jakiekolwiek problemy, [zarejestruj bilet pomocy technicznej](storsimple-8000-contact-microsoft-support.md).
> * Zalecamy zainstalowanie oprogramowania i innych zwykłych aktualizacji za pośrednictwem Azure Portal. W przypadku niepowodzenia sprawdzania bramy przed aktualizacją w portalu należy przejść do interfejsu programu Windows PowerShell urządzenia (aby zainstalować aktualizacje). W zależności od używanej wersji programu aktualizacje mogą zostać zainstalowane dopiero po 4 godzinach (lub większych). Aktualizacje trybu konserwacji należy zainstalować za pośrednictwem interfejsu programu Windows PowerShell urządzenia. Gdy aktualizacje trybu konserwacji są aktualizacjami negatywnymi, są one w niezmienionej postaci.
> * W przypadku uruchamiania opcjonalnego Snapshot Manager StorSimple upewnij się, że uaktualniono wersję Snapshot Manager do Update 5 przed zaktualizowaniem urządzenia.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-5-via-the-azure-portal"></a>Zainstaluj aktualizację Update 5 za pomocą Azure Portal
Wykonaj następujące kroki, aby zaktualizować urządzenie do [aktualizacji Update 5](storsimple-update5-release-notes.md).

> [!NOTE]
> Firma Microsoft pobiera dodatkowe informacje diagnostyczne z urządzenia. W związku z tym, gdy nasz zespół operacyjny zidentyfikuje urządzenia, na których występują problemy, lepiej jest zbierać informacje z urządzenia i diagnozować problemy.

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update5-via-portal.md)]

Sprawdź, czy na urządzeniu jest uruchomiona **StorSimple 8000 Series Update 5 (6.3.9600.17845)**. **Data ostatniej aktualizacji** powinna zostać zmodyfikowana.

Zobaczysz teraz, że aktualizacje trybu konserwacji są dostępne (ten komunikat może być nadal wyświetlany przez maksymalnie 24 godziny po zainstalowaniu aktualizacji). Kroki instalacji aktualizacji trybu konserwacji opisano szczegółowo w następnej sekcji.

[!INCLUDE [storsimple-8000-install-maintenance-mode-updates](../../includes/storsimple-8000-install-maintenance-mode-updates.md)]

## <a name="install-update-5-as-a-hotfix"></a>Zainstaluj aktualizację Update 5 jako poprawkę

Wersje oprogramowania, które można uaktualnić przy użyciu metody Hotfix, są następujące:

* Aktualizacja 0,1, 0,2, 0,3
* Update 1, 1,1, 1,2
* Update 2, 2,1, 2,2
* Update 3, 3,1
* Update 4

> [!NOTE] 
> Zalecaną metodą instalowania aktualizacji Update 5 jest za pośrednictwem Azure Portal podczas próby aktualizacji z wersji Update 3 lub nowszej. W przypadku aktualizowania urządzenia z wersjami wcześniejszymi niż aktualizacja 3 Użyj tej procedury. Tej procedury można również użyć w przypadku niepowodzenia sprawdzania bramy podczas próby zainstalowania aktualizacji za pomocą Azure Portal. Sprawdzenie nie powiedzie się, jeśli masz bramę przypisaną do interfejsu sieciowego innego niż DATA 0, a na urządzeniu jest uruchomiona wersja oprogramowania wcześniejsza niż Update 1.

Metoda poprawek obejmuje następujące trzy kroki:

1. Pobierz poprawki z katalogu Microsoft Update.
2. Zainstaluj i Sprawdź poprawki w trybie regularnym.
3. Zainstaluj i sprawdź poprawkę trybu konserwacji.

#### <a name="download-updates-for-your-device"></a>Pobieranie aktualizacji dla urządzenia

Należy pobrać i zainstalować następujące poprawki we wskazanej kolejności i w sugerowanych folderach:

| Zamówienie | KB | Opis | Typ aktualizacji | Godzina instalacji |Zainstaluj w folderze|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4037264 |Aktualizacja oprogramowania<br> Pobierz zarówno _HcsSoftwareUpdate.exe_ , jak i _CisMSDAgent.exe_ |Zwykły <br></br>Bez zakłóceń |~ 25 minut |FirstOrderUpdate|

W przypadku aktualizacji z poziomu urządzenia z aktualizacją Update 4 należy zainstalować aktualizacje zbiorcze systemu operacyjnego tylko w ramach aktualizacji drugiej kolejności.

| Zamówienie | KB | Opis | Typ aktualizacji | Godzina instalacji |Zainstaluj w folderze|
| --- | --- | --- | --- | --- | --- |
| 2a. |KB4025336 |Pakiet aktualizacji zbiorczych systemu operacyjnego <br> Pobierz wersję systemu Windows Server 2012 R2 |Zwykły <br></br>Bez zakłóceń |- |SecondOrderUpdate|

W przypadku instalowania programu z poziomu urządzenia z aktualizacją Update 3 lub starszej należy zainstalować następujące elementy oprócz aktualizacji zbiorczych.

| Zamówienie | KB | Opis | Typ aktualizacji | Godzina instalacji |Zainstaluj w folderze|
| --- | --- | --- | --- | --- | --- |
| 2b. |KB4011841 <br> KB4011842 |Aktualizacje sterowników i oprogramowania układowego LSI <br> Aktualizacja oprogramowania układowego USM (wersja 3,38) |Zwykły <br></br>Bez zakłóceń |~ 3 godz. <br> (obejmuje 2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2C. |KB3139398 <br> KB3142030 <br> KB3108381 <br> KB3153704 <br> KB3174644 <br> KB3139914   |Pakiet aktualizacji zabezpieczeń systemu operacyjnego <br> Pobierz wersję systemu Windows Server 2012 R2 |Zwykły <br></br>Bez zakłóceń |- |SecondOrderUpdate|
| 2D. |KB3146621 <br> KB3103616 <br> KB3121261 <br> KB3123538 |Pakiet aktualizacji systemu operacyjnego <br> Pobierz wersję systemu Windows Server 2012 R2 |Zwykły <br></br>Bez zakłóceń |- |SecondOrderUpdate|


Może być również konieczne zainstalowanie aktualizacji oprogramowania układowego dysku na wszystkich aktualizacjach przedstawionych w poprzednich tabelach. Aby sprawdzić, czy są wymagane aktualizacje oprogramowania układowego dysku, należy uruchomić `Get-HcsFirmwareVersion` polecenie cmdlet. Jeśli są używane te wersje oprogramowania układowego:,,,,,,, `XMGJ` `XGEG` `KZ50` `F6C2` `VR08` `N003` `0107` nie trzeba instalować tych aktualizacji.

| Zamówienie | KB | Opis | Typ aktualizacji | Godzina instalacji | Zainstaluj w folderze|
| --- | --- | --- | --- | --- | --- |
| 3. |KB4037263 |Oprogramowanie układowe dysku |Konserwacja <br></br>Szkodliwe |~ 30 minut | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * W przypadku aktualizacji z aktualizacji Update 4 łączny czas instalacji jest zbliżony do 4 godzin.
> * Przed użyciem tej procedury w celu zastosowania aktualizacji upewnij się, że oba kontrolery urządzeń są w trybie online, a wszystkie składniki sprzętowe są w dobrej kondycji.

Wykonaj następujące kroki, aby pobrać i zainstalować poprawki.

[!INCLUDE [storsimple-install-update5-hotfix](../../includes/storsimple-install-update5-hotfix.md)]

[!INCLUDE [storsimple-8000-install-troubleshooting](../../includes/storsimple-8000-install-troubleshooting.md)]

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [wersji Update 5](storsimple-update5-release-notes.md).

