---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: fa17223789779390dabc6c88df02824882882128
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106073452"
---
Po wygenerowaniu certyfikatu klienta jest on automatycznie instalowany na komputerze użytym do jego wygenerowania. Jeśli chcesz zainstalować certyfikat klienta na innym komputerze klienckim, musisz wyeksportować wygenerowany certyfikat klienta.

1. Aby wyeksportować certyfikat klienta, otwórz okno **Zarządzaj certyfikatami użytkowników**. Wygenerowane certyfikaty klienta są domyślnie zlokalizowane w temacie "Certificates-Current User\Personal\Certificates". Kliknij prawym przyciskiem myszy certyfikat klienta, który chcesz wyeksportować, kliknij pozycję **wszystkie zadania**, a następnie kliknij pozycję **Eksportuj** , aby otworzyć **Kreatora eksportu certyfikatów**.

   ![Zrzut ekranu przedstawia okno certyfikaty dla bieżącego użytkownika z wybranymi certyfikatami i eksportowanie wybrane z wszystkich zadań.](./media/vpn-gateway-certificates-export-client-cert-include/export.png)
2. W Kreatorze eksportu certyfikatów kliknij przycisk **dalej** , aby kontynuować.

   ![Zrzut ekranu przedstawia komunikat powitalny Kreatora eksportu certyfikatów.](./media/vpn-gateway-certificates-export-client-cert-include/next.png)
3. Wybierz opcję **tak, eksportuj klucz prywatny**, a następnie kliknij przycisk **dalej**.

   ![Eksportuj klucz prywatny](./media/vpn-gateway-certificates-export-client-cert-include/privatekeyexport.png)
4. Na stronie **Format pliku eksportu** pozostaw wybrane wartości domyślne. Upewnij się, że jest zaznaczona pozycja **Jeśli jest to możliwe, dołącz wszystkie certyfikaty do ścieżki certyfikacji**. To ustawienie dodatkowo eksportuje informacje o certyfikacie głównym, które są wymagane do pomyślnego uwierzytelnienia klienta. Bez tego uwierzytelnianie klienta nie powiedzie się, ponieważ klient nie ma zaufanego certyfikatu głównego. Następnie kliknij przycisk **Dalej**.

   ![Format pliku eksportu](./media/vpn-gateway-certificates-export-client-cert-include/includeallcerts.png)
5. Na stronie **Zabezpieczenia** należy włączyć ochronę klucza prywatnego. Jeśli wybierzesz opcję użycia hasła, zapisz lub zapamiętaj hasło ustawione dla tego certyfikatu. Następnie kliknij przycisk **Dalej**.

   ![Zrzut ekranu przedstawia stronę zabezpieczeń Kreator eksportu certyfikatów z wprowadzonym hasłem i potwierdzeniem i następnym wyróżnionym.](./media/vpn-gateway-certificates-export-client-cert-include/security.png)
6. W obszarze **Eksport pliku** wybierz pozycję **Przeglądaj**, aby przejść do lokalizacji, do której chcesz wyeksportować certyfikat. Do pola **Nazwa pliku** wprowadź nazwę pliku certyfikatu. Następnie kliknij przycisk **Dalej**.

   ![plik do wyeksportowania](./media/vpn-gateway-certificates-export-client-cert-include/filetoexport.png)
7. Kliknij przycisk **Zakończ**, aby wyeksportować certyfikat.

   ![Zrzut ekranu pokazuje Kreatora eksportu certyfikatów z wprowadzonymi ustawieniami.](./media/vpn-gateway-certificates-export-client-cert-include/finish.png)