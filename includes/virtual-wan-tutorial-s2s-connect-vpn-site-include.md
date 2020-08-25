---
title: dołączanie pliku
description: dołączanie pliku
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8b338f25e9771f5947fd494cfb00d0f6cb9ef67a
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2020
ms.locfileid: "75450834"
---
1. Wybierz pozycję **Połącz Lokacje sieci VPN** , aby otworzyć stronę **łączenie witryn** .

    ![łączone](./media/virtual-wan-tutorial-connect-vpn-site-include/connect.png "nawiązywania połączenia")

   Wypełnij następujące pola:

   * Wprowadź klucz wstępny. Jeśli klucz nie zostanie wprowadzony, platforma Azure automatycznie wygeneruje je automatycznie.
   * Wybierz protokół i ustawienia protokołu IPsec. Zapoznaj się z tematem [domyślne/niestandardowe szczegóły protokołu IPSec] (https://docs.microsoft.com/azure/virtual-wan/virtual-wan-ipsec)
   * Wybierz odpowiednią opcję **propagacji trasy domyślnej**. Opcja **Włącz** umożliwia koncentratorowi wirtualnemu propagowanie rozkierowanej trasy domyślnej do tego połączenia. Ta flaga włącza propagację trasy domyślnej do połączenia tylko wtedy, gdy trasa domyślna jest już wykorzystana przez koncentrator wirtualnych sieci WAN w wyniku wdrożenia zapory w koncentratorze lub jeśli w innej połączonej lokacji włączono tunelowanie wymuszone. Trasa domyślna nie pochodzi z wirtualnego koncentratora sieci WAN.

2. Wybierz pozycję **Połącz**.
3. W ciągu kilku minut lokacja będzie zawierać stan połączenia i łączności.

   ![Stany](./media/virtual-wan-tutorial-connect-vpn-site-include/status.png "status")

   **Stan połączenia:** Jest to stan zasobu platformy Azure służący do nawiązywania połączenia, które łączy lokację sieci VPN z bramą sieci VPN centrum platformy Azure. Po pomyślnym zakończeniu tej operacji płaszczyzny kontroli usługa Azure VPN Gateway i lokalne urządzenie sieci VPN przejdzie do ustanowienia łączności.

   **Stan łączności:** Jest to rzeczywista wartość stanu łączności (ścieżki danych) między bramą sieci VPN platformy Azure w lokacji centrum i sieci VPN. Może on zawierać dowolne z następujących stanów:

    * **Nieznany**: ten stan jest zwykle widziany, jeśli systemy zaplecza pracują w celu przejścia do innego stanu.
    * **Łączenie**: usługa Azure VPN Gateway próbuje skontaktować się z rzeczywistą lokalną LOKACJĄ sieci VPN.
    * **Połączono**: połączenie między bramą sieci VPN platformy Azure i lokalną LOKACJĄ sieci VPN.
    * **Rozłączono**: ten stan jest widoczny, jeśli z jakiegoś powodu (lokalnie lub na platformie Azure) połączenie zostało rozłączone.
4. W centralnej witrynie sieci VPN można dodatkowo wykonać następujące czynności: 

   * Edytuj lub Usuń połączenie sieci VPN.
   * Usuń lokację w Azure Portal.
   * Pobierz konfigurację specyficzną dla gałęzi, aby uzyskać szczegółowe informacje na temat platformy Azure za pomocą menu kontekstowego (...) znajdującego się obok lokacji. Jeśli chcesz pobrać konfigurację dla wszystkich połączonych lokacji w centrum, wybierz pozycję **Pobierz konfigurację sieci VPN** w górnym menu.
