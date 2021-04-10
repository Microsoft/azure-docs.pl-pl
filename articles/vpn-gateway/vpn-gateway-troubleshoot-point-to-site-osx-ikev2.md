---
title: 'VPN Gateway platformy Azure: Rozwiązywanie problemów z połączeniami punkt-lokacja: Mac OS X klientów'
description: Dowiedz się, jak rozwiązywać problemy z połączeniem punkt-lokacja z Mac OS X przy użyciu natywnego klienta sieci VPN i protokołu IKEv2.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 02/10/2021
ms.author: alzam
ms.openlocfilehash: 4c8657f8a40084a726280efe471c0791931294f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100366640"
---
# <a name="troubleshoot-point-to-site-vpn-connections-from-mac-os-x-vpn-clients"></a>Rozwiązywanie problemów z połączeniami sieci VPN typu punkt-lokacja z Mac OS X klientów sieci VPN

Ten artykuł pomaga rozwiązywać problemy z połączeniem typu punkt-lokacja z Mac OS X przy użyciu natywnego klienta sieci VPN i protokołu IKEv2. Klient sieci VPN w systemie Mac dla protokołu IKEv2 jest bardzo podstawowy i nie pozwala na wiele dostosowań. Należy sprawdzić tylko cztery ustawienia:

* Adres serwera
* Identyfikator zdalny
* Identyfikator lokalny
* Ustawienia uwierzytelniania
* Wersja systemu operacyjnego (10,11 lub nowsza)


## <a name="troubleshoot-certificate-based-authentication"></a><a name="VPNClient"></a> Rozwiązywanie problemów z uwierzytelnianiem opartym na certyfikatach
1. Sprawdź ustawienia klienta sieci VPN. Przejdź do **Ustawienia sieci** , naciskając klawisze Command + Shift, a następnie wpisz "VPN", aby sprawdzić ustawienia klienta sieci VPN. Z listy kliknij wpis sieci VPN, który należy zbadać.

   ![Uwierzytelnianie oparte na certyfikacie IKEv2](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2cert1.jpg)
2. Sprawdź, czy **adres serwera** jest pełną nazwą FQDN i zawiera cloudapp.NET.
3. **Identyfikator zdalny** powinien być taki sam jak adres serwera (FQDN bramy).
4. **Lokalny identyfikator** powinien być taki sam jak **podmiot** certyfikatu klienta.
5. Kliknij pozycję **Ustawienia uwierzytelniania** , aby otworzyć stronę Ustawienia uwierzytelniania.

   ![Zrzut ekranu przedstawia okno dialogowe Ustawienia uwierzytelniania z wybranym certyfikatem.](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth2.jpg)
6. Upewnij się, że wybrano **certyfikat** z listy rozwijanej.
7. Kliknij przycisk **Wybierz** i sprawdź, czy wybrano prawidłowy certyfikat. Kliknij przycisk **OK** , aby zapisać zmiany.

## <a name="troubleshoot-username-and-password-authentication"></a><a name="ikev2"></a>Rozwiązywanie problemów z uwierzytelnianiem nazwy użytkownika i hasła

1. Sprawdź ustawienia klienta sieci VPN. Przejdź do **Ustawienia sieci** , naciskając klawisze Command + Shift, a następnie wpisz "VPN", aby sprawdzić ustawienia klienta sieci VPN. Z listy kliknij wpis sieci VPN, który należy zbadać.

   ![Hasło użytkownika protokołu IKEv2](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2user3.jpg)
2. Sprawdź, czy **adres serwera** jest pełną nazwą FQDN i zawiera cloudapp.NET.
3. **Identyfikator zdalny** powinien być taki sam jak adres serwera (FQDN bramy).
4. **Identyfikator lokalny** może być pusty.
5. Kliknij przycisk **Ustawienia uwierzytelniania** i sprawdź, czy wybrano pozycję "username" z listy rozwijanej.

   ![Zrzut ekranu przedstawia okno dialogowe Ustawienia uwierzytelniania z wybraną nazwą użytkownika.](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth4.png)
6. Sprawdź, czy wprowadzono poprawne poświadczenia.

## <a name="additional-steps"></a><a name="additional"></a>Dodatkowe kroki

Jeśli spróbujesz wykonać poprzednie kroki i wszystko jest prawidłowo skonfigurowane, Pobierz program [Wireshark](https://www.wireshark.org/#download) i Przechwyć pakiet.

1. Odfiltruj protokół *ISAKMP* i sprawdź, czy **IKE_SA** pakiety. Powinien być w stanie zapoznać się ze szczegółami oferty SKOJARZENIA zabezpieczeń w obszarze **ładunek: zabezpieczenia**. 
2. Sprawdź, czy klient i serwer mają wspólny zestaw.

   ![odebran](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/packet5.jpg) 
  
3. Jeśli w śladach sieci nie ma odpowiedzi serwera, sprawdź, czy na stronie Konfiguracja bramy platformy Azure w witrynie sieci Web Azure Portal włączono protokół IKEv2.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać dodatkową pomoc, zobacz [Pomoc techniczna firmy Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
