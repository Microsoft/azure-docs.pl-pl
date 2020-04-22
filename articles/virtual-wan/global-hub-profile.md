---
title: Pobieranie globalnych lub centralnych profili sieci VPN platformy Azure Virtual WAN | Dokumenty firmy Microsoft
description: Dowiedz się więcej o wirtualnej sieci WAN zautomatyzowanej skalowalnej łączności między gałęziami, dostępnych regionach i partnerach.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 4/20/2020
ms.author: alzam
ms.openlocfilehash: b63bb861f4df087f852bb1bf599d32100d063f7e
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733186"
---
# <a name="download-a-global-or-hub-based-profile-for-user-vpn-clients"></a>Pobieranie globalnego lub opartego na centrum profilu dla klientów sieci VPN użytkowników

Wirtualna sieć WAN platformy Azure oferuje dwa typy łączności dla użytkowników zdalnych: globalne i oparte na centrum. Skorzystaj z poniższych sekcji, aby dowiedzieć się więcej o profilu i pobrać go. 

> [!IMPORTANT]
> Uwierzytelnianie usługi RADIUS obsługuje tylko profil oparty na centrum.

## <a name="global-profile"></a>Profil globalny

Profil wskazuje moduł równoważenia obciążenia, który zawiera wszystkie aktywne centra sieci VPN użytkowników. Użytkownik jest kierowany do centrum, który znajduje się najbliżej lokalizacji geograficznej użytkownika. Ten typ łączności jest przydatny, gdy użytkownicy często podróżują do różnych lokalizacji. Aby pobrać profil **globalny:**

1. Przejdź do wirtualnej sieci WAN.
2. Kliknij **pozycję Konfiguracja sieci VPN użytkownika**.
3. Wyróżnij konfigurację, dla której chcesz pobrać profil.
4. Kliknij **pozycję Pobierz wirtualny profil sieci VPN użytkownika sieci WAN**.

   ![Profil globalny](./media/global-hub-profile/global1.png)

## <a name="hub-based-profile"></a>Profil oparty na koncentratorze

Profil wskazuje na pojedynczy koncentrator. Użytkownik może łączyć się tylko z określonym koncentratorem przy użyciu tego profilu. Aby pobrać profil oparty na **koncentratorze:**

1. Przejdź do wirtualnej sieci WAN.
2. Kliknij **pozycję Centrum** na stronie Przegląd.

    ![Profil piasty 1](./media/global-hub-profile/hub1.png)
3. Kliknij **pozycję User VPN (Point to site)**.
4. Kliknij **pozycję Pobierz profil sieci VPN użytkownika wirtualnego centrum**.

   ![Profil piasty 2](./media/global-hub-profile/hub2.png)
5. Sprawdź, **czy w pliku EAPTLS**jest
6. Kliknij **pozycję Generuj i pobierz profil**.

   ![Profil piasty 3](./media/global-hub-profile/download.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Virtual WAN, zobacz stronę [Omówienie usługi Virtual WAN](virtual-wan-about.md).
