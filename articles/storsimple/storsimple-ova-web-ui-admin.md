---
title: Administracja interfejsu użytkownika sieci Web StorSimple Virtual Array | Microsoft Docs
description: Opisuje sposób wykonywania podstawowych zadań administracyjnych urządzenia za pomocą interfejsu użytkownika sieci Web macierzy wirtualnej StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: ea65b4c7-a478-43e6-83df-1d9ea62916a6
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 12/1/2016
ms.author: alkohli
ms.openlocfilehash: 6bea9c9b7807ffb32b6071c968d3186965477009
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000933"
---
# <a name="use-the-web-ui-to-administer-your-storsimple-virtual-array"></a>Korzystanie z internetowego interfejsu użytkownika do administrowania wirtualną macierzą StorSimple
![przepływ procesu instalacji](./media/storsimple-ova-web-ui-admin/manage4.png)

## <a name="overview"></a>Omówienie
Samouczki w tym artykule dotyczą Microsoft Azure StorSimple macierzy wirtualnej (nazywanej również lokalnym urządzeniem wirtualnym StorSimple) z marca 2016 (wersja ogólna dostępności). W tym artykule opisano niektóre złożone przepływy pracy i zadania zarządzania, które można wykonać w macierzy wirtualnej StorSimple. Wirtualną macierzą StorSimple można zarządzać za pomocą interfejsu użytkownika usługi StorSimple Manager (nazywanego interfejsem użytkownika portalu) oraz lokalnego interfejsu użytkownika sieci Web urządzenia. Ten artykuł koncentruje się na zadaniach, które można wykonać za pomocą interfejsu użytkownika sieci Web.

W tym artykule zawarto następujące samouczki:

* Pobierz klucz szyfrowania danych usługi
* Rozwiązywanie problemów z błędami konfiguracji interfejsu użytkownika sieci Web
* Generuj pakiet dziennika
* Zamykanie lub ponowne uruchamianie urządzenia

## <a name="get-the-service-data-encryption-key"></a>Pobierz klucz szyfrowania danych usługi
Klucz szyfrowania danych usługi jest generowany po zarejestrowaniu pierwszego urządzenia w usłudze StorSimple Manager. Ten klucz jest następnie wymagany z kluczem rejestracji usługi w celu zarejestrowania dodatkowych urządzeń w usłudze StorSimple Manager.

Jeśli klucz szyfrowania danych usługi został zagubiony i trzeba go pobrać, wykonaj następujące kroki w lokalnym interfejsie użytkownika sieci Web urządzenia zarejestrowanego w usłudze.

#### <a name="to-get-the-service-data-encryption-key"></a>Aby uzyskać klucz szyfrowania danych usługi
1. Nawiąż połączenie z lokalnym interfejsem użytkownika sieci Web. Przejdź do pozycji **Konfiguracja**  >  **chmury ustawienia**.
2. W dolnej części strony kliknij pozycję **Pobierz klucz szyfrowania danych usługi**. Zostanie wyświetlony klucz. Skopiuj i Zapisz ten klucz.
   
    ![Pobierz klucz szyfrowania danych usługi 1](./media/storsimple-ova-web-ui-admin/image27.png)

## <a name="troubleshoot-web-ui-setup-errors"></a>Rozwiązywanie problemów z błędami konfiguracji interfejsu użytkownika sieci Web
W niektórych przypadkach podczas konfigurowania urządzenia za pomocą lokalnego interfejsu użytkownika sieci Web może wystąpić błąd. Aby zdiagnozować i rozwiązać te błędy, można uruchomić testy diagnostyczne.

#### <a name="to-run-the-diagnostic-tests"></a>Aby uruchomić testy diagnostyczne
1. W lokalnym interfejsie użytkownika sieci Web przejdź do **rozwiązywania problemów z**  >  **testami diagnostycznymi**.
   
    ![Uruchom diagnostykę 1](./media/storsimple-ova-web-ui-admin/image29.png)
2. W dolnej części strony kliknij pozycję **Uruchom testy diagnostyczne**. Spowoduje to zainicjowanie testów w celu zdiagnozowania ewentualnych problemów z ustawieniami sieci, urządzeń, serwera proxy sieci Web, czasu lub chmury. Zostanie wyświetlone powiadomienie, że na urządzeniu są uruchomione testy.
3. Po zakończeniu testów zostaną wyświetlone wyniki. Poniższy przykład pokazuje wyniki testów diagnostycznych. Należy zauważyć, że ustawienia serwera proxy sieci Web nie zostały skonfigurowane na tym urządzeniu i w związku z tym test serwera proxy sieci Web nie został uruchomiony. Wszystkie pozostałe testy ustawień sieci, serwera DNS i czasu zostały wykonane pomyślnie.
   
    ![Uruchom diagnostykę 2](./media/storsimple-ova-web-ui-admin/image30.png)

## <a name="generate-a-log-package"></a>Generuj pakiet dziennika
Pakiet dziennika składa się ze wszystkich odpowiednich dzienników, które mogą pomóc pomoc techniczna firmy Microsoft rozwiązywaniu problemów z urządzeniami. W tej wersji można generować pakiet dzienników za pośrednictwem lokalnego interfejsu użytkownika sieci Web.

#### <a name="to-generate-the-log-package"></a>Aby wygenerować pakiet dzienników
1. W lokalnym interfejsie użytkownika sieci Web przejdź do obszaru **Rozwiązywanie problemów** z  >  **dziennikami systemowymi**.
   
    ![Generuj pakiet dziennika 1](./media/storsimple-ova-web-ui-admin/image31.png)
2. W dolnej części strony kliknij pozycję **Utwórz pakiet dzienników**. Zostanie utworzony pakiet dzienników systemu. Zajmie to kilka minut.
   
    ![Generuj pakiet dziennika 2](./media/storsimple-ova-web-ui-admin/image32.png)
   
    Po pomyślnym utworzeniu pakietu otrzymasz powiadomienie, a strona zostanie zaktualizowana, aby wskazać godzinę i datę utworzenia pakietu.
   
    ![Generuj pakiet dziennika 3](./media/storsimple-ova-web-ui-admin/image33.png)
3. Kliknij pozycję **Pobierz pakiet dzienników**. Pakiet spakowany zostanie pobrany w systemie.
   
    ![Generuj pakiet dzienników 4](./media/storsimple-ova-web-ui-admin/image34.png)
4. Można rozpakować pobrany pakiet dzienników i wyświetlić systemowe pliki dziennika.

## <a name="shut-down-and-restart-your-device"></a>Zamykanie i ponowne uruchamianie urządzenia
Urządzenie wirtualne można zamknąć lub ponownie uruchomić przy użyciu lokalnego internetowego interfejsu użytkownika. Zaleca się, aby przed ponownym uruchomieniem przełączyć woluminy lub udziały w tryb offline na hoście, a następnie na urządzeniu. Spowoduje to zminimalizowanie wszelkich możliwości uszkodzenia danych. 

#### <a name="to-shut-down-your-virtual-device"></a>Aby zamknąć urządzenie wirtualne
1. W lokalnym interfejsie użytkownika sieci Web przejdź do pozycji ustawienia **trybu konserwacji**  >  **Power settings**.
2. W dolnej części strony kliknij przycisk **Zamknij**.
   
    ![Zamykanie urządzenia 1](./media/storsimple-ova-web-ui-admin/image36.png)
3. Zostanie wyświetlone ostrzeżenie informujące o tym, że zamknięcie urządzenia przerwie wszystkie operacje we/wy, które były w toku, co spowodowało przestoje. Kliknij ikonę znacznika wyboru, ![ikona znacznika wyboru](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![Ostrzeżenie o zamknięciu urządzenia](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Zostanie wyświetlone powiadomienie o zainicjowaniu zamknięcia.
   
    ![rozpoczęto zamykanie urządzenia](./media/storsimple-ova-web-ui-admin/image38.png)
   
    Urządzenie zostanie teraz zamknięte. Jeśli chcesz uruchomić urządzenie, musisz wykonać te czynności za pomocą Menedżera funkcji Hyper-V.

#### <a name="to-restart-your-virtual-device"></a>Aby ponownie uruchomić urządzenie wirtualne
1. W lokalnym interfejsie użytkownika sieci Web przejdź do pozycji ustawienia **trybu konserwacji**  >  **Power settings**.
2. W dolnej części strony kliknij pozycję **Uruchom ponownie**.
   
    ![ponowne uruchomienie urządzenia](./media/storsimple-ova-web-ui-admin/image36.png)
3. Zostanie wyświetlone ostrzeżenie z informacją, że ponowne uruchomienie urządzenia spowoduje przerwanie wszystkich systemów IOs, które były w toku, co spowodowało przestoje. Kliknij ikonę znacznika wyboru, ![ikona znacznika wyboru](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![Ostrzeżenie o ponownym uruchomieniu](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Zostanie wyświetlone powiadomienie o zainicjowaniu ponownego uruchomienia.
   
    ![zainicjowano ponowne uruchomienie](./media/storsimple-ova-web-ui-admin/image39.png)
   
    Gdy ponowne uruchomienie jest w toku, nastąpi utrata połączenia z interfejsem użytkownika. Ponowne uruchomienie można monitorować, odświeżając okresowo interfejs użytkownika. Alternatywnie można monitorować stan ponownego uruchomienia urządzenia za pomocą Menedżera funkcji Hyper-V.

## <a name="next-steps"></a>Następne kroki
Dowiedz się [, jak zarządzać urządzeniem za pomocą usługi StorSimple Manager](storsimple-virtual-array-manager-service-administration.md).

