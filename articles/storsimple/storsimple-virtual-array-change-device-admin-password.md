---
title: Zmień hasło administratora urządzenia macierzy wirtualnej StorSimple | Microsoft Docs
description: Opisuje sposób używania interfejsu użytkownika sieci Web macierzy wirtualnej Azure Portal lub StorSimple w celu zmiany hasła administratora urządzenia.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 11490814-d9fd-4dc7-9c3b-55dd2c23eaf1
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8bc8846d546faec194617ccb753cdbd105e16bf9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85513615"
---
# <a name="change-the-storsimple-virtual-array-device-administrator-password-via-storsimple-device-manager"></a>Zmień hasło administratora urządzenia macierzy wirtualnej StorSimple za pośrednictwem StorSimple Device Manager

## <a name="overview"></a>Omówienie

W przypadku korzystania z interfejsu programu Windows PowerShell w celu uzyskania dostępu do macierzy wirtualnej StorSimple wymagane jest wprowadzenie hasła administratora urządzenia. Gdy urządzenie StorSimple jest po raz pierwszy inicjowane i uruchomione, domyślne hasło to *Password1*. W celu zapewnienia bezpieczeństwa danych hasło domyślne wygasa po pierwszym zalogowaniu i użytkownik musi zmienić to hasło.

Możesz również użyć lokalnego interfejsu użytkownika sieci Web lub Azure Portal, aby zmienić hasło administratora urządzenia w dowolnym momencie po wdrożeniu urządzenia w środowisku produkcyjnym. Każda z tych procedur została opisana w tym artykule.

 ![blok urządzeń](./media/storsimple-virtual-array-change-device-admin-password/ova-devices-blade.png)

## <a name="use-the-azure-portal-to-change-the-password"></a>Użyj Azure Portal, aby zmienić hasło

Wykonaj następujące kroki, aby zmienić hasło administratora urządzenia za pomocą Azure Portal.

#### <a name="to-change-the-device-administrator-password-via-the-azure-portal"></a>Aby zmienić hasło administratora urządzenia za pomocą Azure Portal

1. Na stronie miejsce docelowe usługi wybierz swoją usługę, kliknij dwukrotnie nazwę usługi, a następnie w sekcji **Zarządzanie** kliknij pozycję **urządzenia**. Spowoduje to otwarcie bloku **urządzenia** , który zawiera listę wszystkich urządzeń wirtualnej macierzy StorSimple.

2. W bloku **urządzenia** kliknij dwukrotnie urządzenie wymagające zmiany hasła.

3. W bloku **Ustawienia** urządzenia kliknij pozycję **zabezpieczenia**.

4. W bloku **Ustawienia zabezpieczeń** wykonaj następujące czynności:
   
   1. Przewiń w dół do sekcji **hasło administratora urządzenia** . Podaj hasło administratora zawierające od 8 do 15 znaków.
   2. Potwierdź hasło.
   3. Kliknij pozycję **Zapisz** w górnej części bloku.

Hasło administratora urządzenia zostało zaktualizowane. Możesz użyć tego zmodyfikowanego hasła, aby uzyskać dostęp do urządzenia lokalnie.

![Blok ustawień zabezpieczeń](./media/storsimple-virtual-array-change-device-admin-password/ova-change-device-pwd.png)

## <a name="use-the-local-web-ui-to-change-the-password"></a>Zmienianie hasła przy użyciu lokalnego interfejsu użytkownika sieci Web

Wykonaj następujące kroki, aby zmienić hasło administratora urządzenia za pomocą lokalnego interfejsu użytkownika sieci Web.

#### <a name="to-change-the-device-administrator-password-via-the-local-web-ui"></a>Aby zmienić hasło administratora urządzenia za pośrednictwem lokalnego interfejsu użytkownika sieci Web

1. W lokalnym interfejsie użytkownika sieci Web kliknij pozycję  >  **zmiana hasła** konserwacji dla Twojego urządzenia.
   
    ![Zmień password1](./media/storsimple-virtual-array-change-device-admin-password/image40.png)
2. Wprowadź **bieżące hasło**.
3. Podaj **nowe hasło**. Hasło musi mieć długość co najmniej 8 znaków. Musi zawierać 3 z 4 z następujących elementów: wielkie litery, małe litery, cyfry i znaki specjalne.
   
    Należy pamiętać, że hasło nie może być takie samo jak w przypadku ostatnich 24 haseł.
4. Wprowadź ponownie hasło w celu potwierdzenia.
   
    ![Zmień password2](./media/storsimple-virtual-array-change-device-admin-password/image41.png)
5. W dolnej części strony kliknij pozycję **Zastosuj**. Nowe hasło jest teraz stosowane. Jeśli zmiana hasła nie powiedzie się, zostanie wyświetlony następujący błąd:
   
    ![Błąd hasła](./media/storsimple-virtual-array-change-device-admin-password/image42.png)
   
    Po pomyślnym zaktualizowaniu hasła zostanie wyświetlone powiadomienie. Następnie można użyć tego zmodyfikowanego hasła do lokalnego dostępu do urządzenia.


## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak [administrować wirtualną macierzą StorSimple](storsimple-ova-web-ui-admin.md).

