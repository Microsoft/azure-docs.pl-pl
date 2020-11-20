---
title: Modyfikowanie ustawień DATA 0 na urządzeniu z serii StorSimple 8000 | Microsoft Docs
description: Dowiedz się, jak za pomocą program Windows PowerShell dla usługi StorSimple zmienić konfigurację interfejsu sieciowego danych 0 na urządzeniu StorSimple.
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
ms.date: 03/27/2017
ms.author: alkohli
ms.openlocfilehash: 85d7114f419266124d0d23368b24700af025758a
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94961043"
---
# <a name="modify-the-data-0-network-interface-settings-on-your-storsimple-8000-series-device"></a>Modyfikowanie ustawień interfejsu sieciowego DATA 0 na urządzeniu z serii StorSimple 8000

## <a name="overview"></a>Omówienie

Urządzenie Microsoft Azure StorSimple ma sześć interfejsów sieciowych — od danych 0 do danych 5. Interfejs DATA 0 jest zawsze konfigurowany za pomocą interfejsu programu Windows PowerShell lub konsoli szeregowej i jest automatycznie włączany w chmurze. Należy pamiętać, że nie można skonfigurować interfejsu sieciowego danych 0 za pomocą Azure Portal.

Interfejs DATA 0 jest najpierw konfigurowany za pomocą Kreatora instalacji podczas początkowego wdrażania urządzenia StorSimple. Gdy urządzenie jest w trybie operacyjnym, może być konieczne ponowne skonfigurowanie ustawień DATA 0. Ten samouczek zawiera dwie metody modyfikowania ustawień sieci DATA 0, zarówno przez program Windows PowerShell dla usługi StorSimple.

Po przeczytaniu tego samouczka będziesz mieć możliwość:

* Modyfikowanie ustawień sieci DATA 0 za pomocą Kreatora instalacji
* Modyfikowanie ustawień sieci DATA 0 za pomocą `Set-HcsNetInterface` polecenia cmdlet

## <a name="modify-data-0-network-settings-through-setup-wizard"></a>Modyfikowanie ustawień sieci DATA 0 za poorednictwem Kreatora instalacji
Ustawienia sieciowe danych 0 można skonfigurować ponownie, łącząc się z interfejsem programu Windows PowerShell urządzenia StorSimple i uruchamiając sesję Kreatora instalacji. Wykonaj następujące kroki, aby zmodyfikować ustawienia DATA 0:

#### <a name="to-modify-data-0-network-settings-through-setup-wizard"></a>Aby zmodyfikować ustawienia sieci DATA 0 za poorednictwem Kreatora instalacji
1. W menu konsoli szeregowej wybierz opcję 1, **Zaloguj się z pełnymi prawami dostępu**. Po wyświetleniu monitu podaj **hasło administratora urządzenia**. Domyślne hasło to `Password1` .
2. W wierszu polecenia wpisz polecenie:
   
    `Invoke-HcsSetupWizard`
3. Zostanie wyświetlony Kreator instalacji, który pomoże skonfigurować interfejs DATA 0 urządzenia. Podaj nowe wartości dla adresu IP, bramy i maski sieci.

> [!NOTE]
> Adresy IP stałych kontrolerów należy ponownie skonfigurować za pomocą bloku **Ustawienia sieci** urządzenia StorSimple w Azure Portal. Aby uzyskać więcej informacji, przejdź do pozycji [Modyfikuj interfejsy sieciowe](storsimple-8000-modify-device-config.md#modify-network-interfaces).

## <a name="modify-data-0-network-settings-through-set-hcsnetinterface-cmdlet"></a>Modyfikowanie ustawień sieci DATA 0 za poorednictwem polecenia cmdlet Set-HcsNetInterface
Alternatywny sposób zmiany konfiguracji interfejsu sieciowego danych 0 polega na użyciu `Set-HcsNetInterface` polecenia cmdlet. Polecenie cmdlet jest wykonywane z interfejsu programu Windows PowerShell urządzenia StorSimple. Korzystając z tej procedury, można również skonfigurować stałe adresy IP kontrolera. Wykonaj następujące kroki, aby zmodyfikować ustawienia DATA 0: 

#### <a name="to-modify-data-0-network-settings-through-the-set-hcsnetinterface-cmdlet"></a>Aby zmodyfikować ustawienia sieci DATA 0 za pomocą polecenia cmdlet Set-HcsNetInterface
1. W menu konsoli szeregowej wybierz opcję 1, **Zaloguj się z pełnymi prawami dostępu**. Po wyświetleniu monitu podaj hasło administratora urządzenia. Domyślne hasło to `Password1` .
2. W wierszu polecenia wpisz polecenie:
   
    `Set-HCSNetInterface -InterfaceAlias Data0 -IPv4Address <> -IPv4Netmask <> -IPv4Gateway <> -Controller0IPv4Address <> -Controller1IPv4Address <> -IsiScsiEnabled 1 -IsCloudEnabled 1`
   
    W nawiasach ostrych wpisz następujące wartości dla danych 0:
   
   * Adres IPv4
   * Brama IPv4
   * Maska podsieci IPv4
   * Stały adres IPv4 dla kontrolera 0
   * Stały adres IPv4 dla kontrolera 1
     
     Aby uzyskać więcej informacji na temat korzystania z tego polecenia cmdlet, przejdź do [program Windows PowerShell dla usługi StorSimple dokumentacja poleceń cmdlet](/previous-versions/windows/powershell-scripting/dn688161(v=wps.630)).

## <a name="next-steps"></a>Następne kroki
* Aby skonfigurować interfejsy sieciowe inne niż DATA 0, można użyć [ustawień Konfiguruj ustawienia sieci w Azure Portal](storsimple-8000-modify-device-config.md). 
* W przypadku napotkania problemów podczas konfigurowania interfejsów sieciowych zapoznaj się z tematem [Rozwiązywanie problemów z wdrażaniem](./storsimple-8000-troubleshoot-deployment.md).