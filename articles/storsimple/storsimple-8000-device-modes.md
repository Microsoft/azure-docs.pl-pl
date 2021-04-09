---
title: Zmień tryb urządzenia StorSimple | Microsoft Docs
description: W tym artykule opisano tryby urządzeń StorSimple i wyjaśniono, w jaki sposób należy zmienić tryb urządzenia za pomocą program Windows PowerShell dla usługi StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: c7b0ea489c1d70ab86d677aad666ea6728fa76b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017087"
---
# <a name="change-the-device-mode-on-your-storsimple-device"></a>Zmienianie trybu urządzenia na urządzeniu StorSimple

Ten artykuł zawiera krótki opis różnych trybów, w których może działać urządzenie StorSimple. Urządzenie StorSimple może działać w trzech trybach: normalny, konserwacja i odzyskiwanie.

Po przeczytaniu tego artykułu uzyskasz następujące informacje:

* Co to są tryby urządzenia StorSimple
* Jak ustalić tryb, w którym znajduje się urządzenie StorSimple
* Jak zmienić z normalny na tryb konserwacji i na *odwrót*

Powyższe zadania zarządzania można wykonać tylko za pomocą interfejsu programu Windows PowerShell urządzenia StorSimple.

## <a name="about-storsimple-device-modes"></a>Informacje o trybach urządzenia StorSimple

Urządzenie StorSimple może działać w trybie normalnym, konserwacji lub odzyskiwania. Każdy z tych trybów został krótko opisany poniżej.

### <a name="normal-mode"></a>Tryb normalny

Jest to zdefiniowane jako normalny tryb operacyjny dla w pełni skonfigurowanego urządzenia StorSimple. Domyślnie urządzenie powinno działać w trybie normalnym.

### <a name="maintenance-mode"></a>Tryb konserwacji

Czasami może być konieczne umieszczenie urządzenia StorSimple w trybie konserwacji. Ten tryb pozwala na przeprowadzanie konserwacji na urządzeniu i Instalowanie nieprzerwanych aktualizacji, takich jak te powiązane z oprogramowaniem układowym dysku.

System można przełączyć do trybu konserwacji tylko za pośrednictwem program Windows PowerShell dla usługi StorSimple. Wszystkie żądania we/wy są wstrzymane w tym trybie. Usługi, takie jak nietrwały dostęp losowy (NVRAM) lub usługa klastrowania, również są zatrzymane. Oba kontrolery są ponownie uruchamiane po wprowadzeniu lub wyjściu z tego trybu. Po zamknięciu trybu konserwacji wszystkie usługi zostaną wznowione i powinny być w dobrej kondycji. Może to potrwać kilka minut.

> [!NOTE]
> **Tryb konserwacji jest obsługiwany tylko na prawidłowo działającym urządzeniu. Nie jest obsługiwana na urządzeniu, na którym jeden lub oba kontrolery nie działają.**


### <a name="recovery-mode"></a>Tryb odzyskiwania

Tryb odzyskiwania można opisać jako "tryb awaryjny systemu Windows z obsługą sieci". Tryb odzyskiwania angażuje zespół pomoc techniczna firmy Microsoft i umożliwia im przeprowadzanie diagnostyki w systemie. Głównym celem trybu odzyskiwania jest pobranie dzienników systemu.

Jeśli system przechodzi w tryb odzyskiwania, należy skontaktować się z pomoc techniczna firmy Microsoft w celu wykonania następnych kroków. Aby uzyskać więcej informacji, przejdź do [Pomoc techniczna firmy Microsoft kontaktu](storsimple-8000-contact-microsoft-support.md).

> [!NOTE]
> **Nie można umieścić urządzenia w trybie odzyskiwania. Jeśli urządzenie jest w nieprawidłowym stanie, tryb odzyskiwania próbuje uzyskać dostęp do urządzenia w stanie, w którym personel pomoc techniczna firmy Microsoft może go przeanalizować.**

## <a name="determine-storsimple-device-mode"></a>Określanie trybu urządzenia StorSimple

#### <a name="to-determine-the-current-device-mode"></a>Aby określić bieżący tryb urządzenia

1. Zaloguj się do konsoli szeregowej urządzenia, wykonując kroki opisane w temacie [Korzystanie z konsoli szeregowej urządzenia](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. Spójrz na transparent w menu konsoli szeregowej urządzenia. Ten komunikat jawnie wskazuje, czy urządzenie jest w trybie konserwacji, czy odzyskiwania. Jeśli komunikat nie zawiera określonych informacji odnoszących się do trybu systemowego, urządzenie jest w trybie normalnym.

## <a name="change-the-storsimple-device-mode"></a>Zmień tryb urządzenia StorSimple

Urządzenie StorSimple można umieścić w trybie konserwacji (z trybu normalnego), aby przeprowadzić konserwację lub zainstalować aktualizacje trybu konserwacji. Wykonaj poniższe procedury, aby wejść lub wyjść z trybu konserwacji.

> [!IMPORTANT]
> Przed przejściem do trybu konserwacji Sprawdź, czy oba kontrolery urządzeń są w dobrej kondycji, uzyskując dostęp do **ustawień urządzenia > kondycji sprzętu** urządzenia w Azure Portal. Jeśli jeden lub oba kontrolery nie są w dobrej kondycji, skontaktuj się z firmą pomoc techniczna firmy Microsoft w celu wykonania następnych kroków. Aby uzyskać więcej informacji, przejdź do [Pomoc techniczna firmy Microsoft kontaktu](storsimple-8000-contact-microsoft-support.md).
 

#### <a name="to-enter-maintenance-mode"></a>Aby przejść do trybu konserwacji

1. Zaloguj się do konsoli szeregowej urządzenia, wykonując kroki opisane w temacie [Korzystanie z konsoli szeregowej urządzenia](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. W menu konsoli szeregowej wybierz opcję 1, **Zaloguj się z pełnymi prawami dostępu**. Po wyświetleniu monitu podaj **hasło administratora urządzenia**. Domyślne hasło to: `Password1` .
3. W wierszu polecenia wpisz 
   
    `Enter-HcsMaintenanceMode`
4. Zostanie wyświetlony komunikat ostrzegawczy informujący o tym, że tryb konserwacji przerwie wszystkie żądania we/wy i nawiąże połączenie z Azure Portal i zostanie wyświetlony monit o potwierdzenie. Wpisz **Y** , aby przejść do trybu konserwacji.
5. Oba kontrolery zostaną uruchomione ponownie. Po ponownym uruchomieniu zostanie wyświetlony transparent konsoli szeregowej wskazujący, że urządzenie jest w trybie konserwacji. Poniżej pokazano przykładowe dane wyjściowe.

```
    ---------------------------------------------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Passive
    ---------------------------------------------------------------

    Controller0>Enter-HcsMaintenanceMode
    Checking device state...

    In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
    [Y] Yes [N] No (Default is "Y"): Y

    <BOTH CONTROLLERS RESTART>

    -----------------------MAINTENANCE MODE------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Passive
    ---------------------------------------------------------------

    Serial Console Menu
    [1] Log in with full access
    [2] Log into peer controller with full access
    [3] Connect with limited access
    [4] Change language
    Please enter your choice>

```

#### <a name="to-exit-maintenance-mode"></a>Aby wyjść z trybu konserwacji

1. Zaloguj się do konsoli szeregowej urządzenia. Sprawdź, czy na transparencie nie jest wyświetlany komunikat o tym, że urządzenie jest w trybie konserwacji.
2. W wierszu polecenia wpisz polecenie:
   
    `Exit-HcsMaintenanceMode`
3. Zostanie wyświetlony komunikat ostrzegawczy. Wpisz **Y** , aby wyjść z trybu konserwacji.
4. Oba kontrolery zostaną uruchomione ponownie. Po ponownym uruchomieniu, transparent konsoli szeregowej wskazuje, że urządzenie jest w trybie normalnym. Poniżej pokazano przykładowe dane wyjściowe.

```
    -----------------------MAINTENANCE MODE------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0
    ---------------------------------------------------------------

    Controller0>Exit-HcsMaintenanceMode
    Checking device state...

    Before exiting maintenance mode, ensure that any updates that are required on both controllers have been applied. Failure to install on each controller could result in data corruption. Exiting maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to exit maintenance mode?
    [Y] Yes [N] No (Default is "Y"): Y

    <BOTH CONTROLLERS RESTART>

    ---------------------------------------------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Active
    ---------------------------------------------------------------

    Serial Console Menu
    [1] Log in with full access
    [2] Log into peer controller with full access
    [3] Connect with limited access
    [4] Change language
    Please enter your choice>
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się [, jak zastosować aktualizacje trybu normalnego i konserwacji](storsimple-update-device.md) na urządzeniu StorSimple.

