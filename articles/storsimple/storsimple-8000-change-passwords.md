---
title: Zmień hasła StorSimple | Microsoft Docs
description: Opisuje sposób używania usługi StorSimple Menedżer urządzeń do zmiany hasła Snapshot Manager i administratora urządzenia.
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
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: becbf7f89266fb0f56aaa053eb7cba9291da6896
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2020
ms.locfileid: "85514100"
---
# <a name="use-the-storsimple-device-manager-service-to-change-your-storsimple-passwords"></a>Zmienianie haseł StorSimple przy użyciu usługi StorSimple Menedżer urządzeń

## <a name="overview"></a>Omówienie
Opcja **Ustawienia urządzenia** Azure Portal zawiera wszystkie parametry urządzeń, które można ponownie skonfigurować na urządzeniu StorSimple zarządzanym przez usługę StorSimple Menedżer urządzeń. W tym samouczku wyjaśniono, jak można użyć opcji **zabezpieczenia** w obszarze **Ustawienia urządzenia** , aby zmienić hasło administratora urządzenia lub StorSimple Snapshot Manager.

## <a name="change-the-device-administrator-password"></a>Zmiana hasła administratora urządzenia
W przypadku korzystania z interfejsu programu Windows PowerShell w celu uzyskania dostępu do urządzenia StorSimple wymagane jest wprowadzenie hasła administratora urządzenia. Gdy pierwsze urządzenie StorSimple jest zarejestrowane w usłudze, domyślne hasło dla tego interfejsu to *Password1*. W celu zapewnienia bezpieczeństwa danych należy zmienić to hasło na końcu procesu rejestracji. Nie można wyjść z procesu rejestracji bez zmiany tego hasła. Aby uzyskać więcej informacji, zobacz [krok 3. Konfigurowanie i rejestrowanie urządzenia za pomocą program Windows PowerShell dla usługi StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

Hasło, które zostało najpierw ustawione za pośrednictwem interfejsu programu Windows PowerShell podczas rejestracji, można zmienić później za pomocą Azure Portal. Wykonaj następujące kroki, aby zmienić hasło administratora urządzenia.

#### <a name="to-change-the-device-administrator-password"></a>Aby zmienić hasło administratora urządzenia
1. Przejdź do usługi Menedżer urządzeń StorSimple i kliknij pozycję **Urządzenia**.

2. Na tabelarycznej liście urządzeń wybierz i kliknij urządzenie, którego hasło ma zostać zmienione.

    ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. W bloku **Ustawienia** przejdź do pozycji **Ustawienia urządzenia > zabezpieczenia**.

    ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. W bloku **Ustawienia zabezpieczeń** kliknij pozycję **hasło** , aby zmienić hasło administratora urządzenia.

    ![](./media/storsimple-8000-change-passwords/changepwd3.png)

5. W bloku **hasło** Podaj hasło administratora zawierające od 8 do 15 znaków. Hasło musi zawierać co najmniej 3 małe litery, wielkie litery, cyfry i znaki specjalne.

6. Potwierdź hasło.

    ![](./media/storsimple-8000-change-passwords/changepwd4.png)

7. Kliknij przycisk **Zapisz** i po wyświetleniu monitu o potwierdzenie kliknij przycisk **tak**.

    ![](./media/storsimple-8000-change-passwords/changepwd6.png)

Należy teraz zaktualizować hasło administratora urządzenia. Możesz użyć tego zmodyfikowanego hasła, aby uzyskać dostęp do interfejsu programu Windows PowerShell.

## <a name="set-the-storsimple-snapshot-manager-password"></a>Ustawianie hasła Snapshot Manager StorSimple
Oprogramowanie StorSimple Snapshot Manager jest zainstalowane na hoście z systemem Windows i umożliwia administratorom zarządzanie kopiami zapasowymi urządzenia StorSimple przez tworzenie migawek lokalnych i w chmurze.

Podczas konfigurowania urządzenia w programie StorSimple Snapshot Manager zostanie wyświetlony monit o podanie adresu IP i hasła urządzenia w celu uwierzytelnienia urządzenia magazynującego.

Możesz ustawić lub zmienić hasło dla StorSimple Snapshot Manager za pośrednictwem Azure Portal. Wykonaj następujące kroki, aby ustawić lub zmienić hasło Snapshot Manager StorSimple.

#### <a name="to-set-the-storsimple-snapshot-manager-password"></a>Aby ustawić StorSimple Snapshot Manager hasło
1. Przejdź do usługi Menedżer urządzeń StorSimple i kliknij pozycję **Urządzenia**.

2. Na tabelarycznej liście urządzeń wybierz i kliknij urządzenie, którego StorSimple Snapshot Manager hasło, które chcesz ustawić lub zmienić.

     ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. W bloku **Ustawienia** przejdź do pozycji **Ustawienia urządzenia > zabezpieczenia**.

     ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. W bloku **Ustawienia zabezpieczeń** kliknij pozycję **hasło** , aby ustawić lub zmienić hasło Snapshot Manager StorSimple.

     ![](./media/storsimple-8000-change-passwords/changepwd3.png) 

5. W bloku **hasło** wprowadź hasło zawierające 14 lub 15 znaków. Upewnij się, że hasło zawiera kombinację trzech lub więcej wielkich liter, małych liter, cyfr i znaków specjalnych.

6. Potwierdź hasło.

     ![](./media/storsimple-8000-change-passwords/changepwd5.png)

7. Kliknij przycisk **Zapisz** i po wyświetleniu monitu o potwierdzenie kliknij przycisk **tak**.

     ![](./media/storsimple-8000-change-passwords/changepwd6.png)

Należy teraz zaktualizować hasło Snapshot Manager StorSimple.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [zabezpieczeniach StorSimple](storsimple-8000-security.md).
* Dowiedz się więcej o [modyfikowaniu konfiguracji urządzenia](storsimple-8000-modify-device-config.md).
* Dowiedz się więcej o [korzystaniu z usługi StorSimple Menedżer urządzeń w celu administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

