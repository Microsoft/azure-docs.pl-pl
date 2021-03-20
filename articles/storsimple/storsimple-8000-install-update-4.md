---
title: Zainstaluj aktualizację Update 4 na urządzeniu z serii StorSimple 8000 | Microsoft Docs
description: Wyjaśnia, w jaki sposób zainstalować StorSimple 8000 Series Update 4 na urządzeniu z serii StorSimple 8000.
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
ms.date: 08/02/2017
ms.author: alkohli
ms.openlocfilehash: ed6f9d58c5c54c88acf8e3a0e7fda7d2d65b8637
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85514312"
---
# <a name="install-update-4-on-your-storsimple-device"></a>Zainstaluj aktualizację Update 4 na urządzeniu StorSimple

## <a name="overview"></a>Omówienie

W tym samouczku wyjaśniono, jak zainstalować aktualizację Update 4 na urządzeniu StorSimple z uruchomioną wcześniejszą wersją oprogramowania za pośrednictwem Azure Portal i przy użyciu metody Hotfix. Metoda Hotfix jest używana, gdy Brama jest skonfigurowana w interfejsie sieciowym innym niż dane 0 urządzenia StorSimple, a użytkownik próbuje przeprowadzić aktualizację z wersji wstępnej Update 1.

Aktualizacja Update 4 obejmuje oprogramowanie urządzenia, oprogramowanie układowe USM, sterownik LSI i oprogramowanie układowe, aplikacje Storport i Spaceport, aktualizacje zabezpieczeń systemu operacyjnego oraz hosta innych aktualizacji systemu operacyjnego.  Aktualizacje oprogramowania urządzenia, oprogramowania układowego USM, Spaceport, Storport i innych systemów operacyjnych są niezakłócane. Niezakłócone lub regularne aktualizacje można zastosować za pośrednictwem Azure Portal lub przy użyciu metody Hotfix. Aktualizacje oprogramowania układowego dysku są aktualizacjami przerywanymi i można je stosować tylko za pomocą metody hotfix przy użyciu interfejsu programu Windows PowerShell urządzenia.

> [!IMPORTANT]
> * Przed zainstalowaniem zestaw ręcznych i automatycznych testów jest przeprowadzany przed rozpoczęciem instalacji w celu określenia kondycji urządzenia pod kątem stanu sprzętu i połączenia sieciowego. Te wstępne sprawdzenia są wykonywane tylko w przypadku zastosowania aktualizacji z Azure Portal.
> * Zalecamy zainstalowanie oprogramowania i innych zwykłych aktualizacji za pośrednictwem Azure Portal. W przypadku niepowodzenia sprawdzania bramy przed aktualizacją w portalu należy przejść do interfejsu programu Windows PowerShell urządzenia (aby zainstalować aktualizacje). W zależności od używanej wersji programu aktualizacje mogą zostać zainstalowane dopiero po 4 godzinach (lub większych). Aktualizacje trybu konserwacji należy również zainstalować za pośrednictwem interfejsu programu Windows PowerShell urządzenia. Gdy aktualizacje trybu konserwacji są aktualizacjami negatywnymi, spowoduje to zmniejszenie czasu dla urządzenia.
> * W przypadku uruchamiania opcjonalnego Snapshot Manager StorSimple upewnij się, że uaktualniono wersję Snapshot Manager do Update 4 przed zaktualizowaniem urządzenia.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-4-via-the-azure-portal"></a>Zainstaluj aktualizację Update 4 za pomocą Azure Portal
Wykonaj następujące kroki, aby zaktualizować urządzenie do [aktualizacji Update 4](storsimple-update4-release-notes.md).

> [!NOTE]
> Firma Microsoft pobiera dodatkowe informacje diagnostyczne z urządzenia. W związku z tym, gdy nasz zespół operacyjny zidentyfikuje urządzenia, na których występują problemy, lepiej jest zbierać informacje z urządzenia i diagnozować problemy. 

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update4-via-portal.md)]

Sprawdź, czy na urządzeniu jest uruchomiona **StorSimple 8000 Series Update 4 (6.3.9600.17820)**. Należy również zmodyfikować **datę ostatniej aktualizacji** .

* Zobaczysz teraz, że aktualizacje trybu konserwacji są dostępne (ten komunikat może być nadal wyświetlany przez maksymalnie 24 godziny po zainstalowaniu aktualizacji). Aktualizacje trybu konserwacji to zakłócenia aktualizacji, które powodują przestoje urządzenia i mogą być stosowane tylko za pośrednictwem interfejsu programu Windows PowerShell urządzenia.

* Pobierz aktualizacje trybu konserwacji, wykonując kroki opisane w sekcji, [Aby pobrać poprawki](#to-download-hotfixes) w celu wyszukania i pobrania KB4011837, które instaluje aktualizacje oprogramowania układowego dysku (inne aktualizacje powinny już być zainstalowane teraz). Wykonaj kroki opisane w temacie [Instalowanie i weryfikowanie poprawek trybu konserwacji](#to-install-and-verify-maintenance-mode-hotfixes) , aby zainstalować aktualizacje trybu konserwacji.

## <a name="install-update-4-as-a-hotfix"></a>Zainstaluj aktualizację Update 4 jako poprawkę
Zalecana metoda instalacji aktualizacji Update 4 odbywa się za pośrednictwem Azure Portal.

Tej procedury należy użyć w przypadku niepowodzenia sprawdzania bramy podczas próby zainstalowania aktualizacji za pomocą Azure Portal. Sprawdzenie nie powiodło się, ponieważ Brama jest przypisana do interfejsu sieciowego innego niż DATA 0, a na urządzeniu jest uruchomiona wersja oprogramowania przed aktualizacją 1.

Wersje oprogramowania, które można uaktualnić przy użyciu metody Hotfix, są następujące:

* Aktualizacja 0,1, 0,2, 0,3
* Update 1, 1,1, 1,2
* Update 2, 2,1, 2,2
* Update 3, 3,1


Metoda poprawek obejmuje następujące trzy kroki:

1. Pobierz poprawki z katalogu Microsoft Update.
2. Zainstaluj i Sprawdź poprawki w trybie regularnym.
3. Zainstaluj i sprawdź poprawkę trybu konserwacji.

#### <a name="download-updates-for-your-device"></a>Pobieranie aktualizacji dla urządzenia

Należy pobrać i zainstalować następujące poprawki we wskazanej kolejności i w sugerowanych folderach:

| Zamówienie | KB | Opis | Typ aktualizacji | Godzina instalacji |Zainstaluj w folderze|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4011839 |Aktualizacja oprogramowania |Zwykły <br></br>Bez zakłóceń |~ 25 minut |FirstOrderUpdate|
| 2a. |KB4011841 <br> KB4011842 |Aktualizacje sterowników i oprogramowania układowego LSI <br> Aktualizacja oprogramowania układowego USM (wersja 3,38) |Zwykły <br></br>Bez zakłóceń |~ 3 godz. <br> (obejmuje 2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2b. |KB3139398, KB3108381 <br> KB3205400, KB3142030 <br> KB3197873, KB3197873 <br> KB3192392, KB3153704 <br> KB3174644, KB3139914  |Pakiet aktualizacji zabezpieczeń systemu operacyjnego <br> Pobierz system Windows Server 2012 R2 |Zwykły <br></br>Bez zakłóceń |- |SecondOrderUpdate|
| 2C. |KB3210083, KB3103616 <br> KB3146621, KB3121261 <br> KB3123538 |Pakiet aktualizacji systemu operacyjnego <br> Pobierz system Windows Server 2012 R2 |Zwykły <br></br>Bez zakłóceń |- |SecondOrderUpdate|

Może być również konieczne zainstalowanie aktualizacji oprogramowania układowego dysku na wszystkich aktualizacjach przedstawionych w poprzednich tabelach. Aby sprawdzić, czy są wymagane aktualizacje oprogramowania układowego dysku, należy uruchomić `Get-HcsFirmwareVersion` polecenie cmdlet. Jeśli są używane te wersje oprogramowania układowego:,,,,,,, `XMGJ` `XGEG` `KZ50` `F6C2` `VR08` `N002` `0106` nie trzeba instalować tych aktualizacji.

| Zamówienie | KB | Opis | Typ aktualizacji | Godzina instalacji | Zainstaluj w folderze|
| --- | --- | --- | --- | --- | --- |
| 3. |KB3121899 |Oprogramowanie układowe dysku |Konserwacja <br></br>Szkodliwe |~ 30 minut | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * Tę procedurę należy wykonać tylko raz, aby zastosować aktualizację Update 4. Możesz użyć Azure Portal, aby zastosować kolejne aktualizacje.
> * W przypadku aktualizacji z aktualizacji Update 3 lub 3,1 łączny czas instalacji jest zbliżony do 4 godzin.
> * Przed użyciem tej procedury w celu zastosowania aktualizacji upewnij się, że oba kontrolery urządzeń są w trybie online, a wszystkie składniki sprzętowe są w dobrej kondycji.

Wykonaj następujące kroki, aby pobrać i zainstalować poprawki.

[!INCLUDE [storsimple-install-update4-hotfix](../../includes/storsimple-install-update4-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [wersji Update 4](storsimple-update4-release-notes.md).

