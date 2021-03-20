---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 55fa01d100c60c6411774373428ff4bbd9a56822
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "80986696"
---
## <a name="windows-clients"></a><a name="windows"></a>Klienci z systemem Windows

1. Pobierz i zainstaluj klienta programu OpenVPN (w wersji 2,4 lub nowszej) z oficjalnej [witryny sieci Web OpenVPN](https://openvpn.net/index.php/open-source/downloads.html).
2. Pobierz profil sieci VPN dla bramy. Można to zrobić na karcie Konfiguracja punktu do lokacji w Azure Portal lub polecenie "New-AzVpnClientConfiguration" w programie PowerShell.
3. Rozpakuj profil. Następnie otwórz plik konfiguracyjny *vpnconfig. ovpn* z folderu OpenVPN za pomocą Notatnika.
4. Wyeksportuj certyfikat klienta punkt-lokacja utworzony i przekazany do konfiguracji P2S na bramie. Skorzystaj z poniższych linków artykułu:

   * Instrukcje [VPN Gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport)
   
   * Instrukcje dotyczące [wirtualnej sieci WAN](../articles/virtual-wan/certificates-point-to-site.md#clientexport)
5. Wyodrębnij klucz prywatny i odcisk palca Base64 z pliku *PFX*. Istnieje wiele sposobów, aby to zrobić. Korzystanie z OpenSSL na maszynie jest jednym ze sposobów. Plik *profileinfo.txt* zawiera klucz prywatny i odcisk palca urzędu certyfikacji oraz certyfikat klienta. Upewnij się, że używasz odcisku palca certyfikatu klienta.

   ```
   openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
   ```
6. Otwórz *profileinfo.txt* w Notatniku. Aby uzyskać odcisk palca certyfikatu klienta (elementu podrzędnego), zaznacz tekst (w tym i między) "-----BEGIN CERTIFICATE-----" i "----------certyfikatu" dla certyfikatu podrzędnego i skopiuj go. Certyfikat podrzędny można zidentyfikować, przeglądając temat subject =/line.
7. Przejdź do pliku *vpnconfig. ovpn* , który został otwarty w Notatniku, z kroku 3. Znajdź sekcję pokazaną poniżej i Zastąp wszystko "CERT" i "/CERT".

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Otwórz *profileinfo.txt* w Notatniku. Aby uzyskać klucz prywatny, zaznacz tekst (w tym i między) "-----Rozpocznij-----klucza prywatnego" i "-----Zakończ klucz prywatny-----" i skopiuj go.
9. Wróć do pliku vpnconfig. ovpn w Notatniku i znajdź tę sekcję. Wklej klucz prywatny, zastępując wszystkie elementy od i "Key" i "/Key".

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```
10. Nie zmieniaj innych pól. Użyj konfiguracji wprowadzonej w danych wejściowych klienta, aby nawiązać połączenie z siecią VPN.
11. Skopiuj plik vpnconfig.ovpn do folderu C:\Program Files\OpenVPN\config.
12. Kliknij prawym przyciskiem myszy ikonę OpenVPN na pasku zadań i kliknij przycisk Połącz.

## <a name="mac-clients"></a><a name="mac"></a>Klienci na komputery Mac

1. Pobierz i zainstaluj klienta OpenVPN, taki jak [TunnelBlick](https://tunnelblick.net/downloads.html). 
2. Pobierz profil sieci VPN dla bramy. Można to zrobić na karcie Konfiguracja punktu do lokacji w Azure Portal lub przy użyciu polecenia "New-AzVpnClientConfiguration" w programie PowerShell.
3. Rozpakuj profil. Otwórz plik konfiguracyjny vpnconfig. ovpn z folderu OpenVPN w edytorze tekstów.
4. W sekcji certyfikatu klienta P2S wprowadź klucz publiczny certyfikatu klienta P2S w formacie base64. W przypadku certyfikatu w formacie PEM można po prostu otworzyć plik cer i skopiować klucz w formacie base64 znajdujący się pomiędzy nagłówkami certyfikatów. Skorzystaj z poniższych linków, aby uzyskać informacje na temat eksportowania certyfikatu w celu pobrania zakodowanego klucza publicznego:

   * Instrukcje [VPN Gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#cer) 
   
   * Instrukcje dotyczące [wirtualnej sieci WAN](../articles/virtual-wan/certificates-point-to-site.md#cer)
5. W sekcji klucza prywatnego wprowadź klucz prywatny certyfikatu klienta P2S w formacie base64. Zapoznaj się z tematem [Eksportowanie klucza prywatnego](https://openvpn.net/community-resources/how-to/#pki) w witrynie OpenVPN, aby uzyskać informacje na temat sposobu wyodrębniania klucza prywatnego.
6. Nie zmieniaj innych pól. Użyj konfiguracji wprowadzonej w danych wejściowych klienta, aby nawiązać połączenie z siecią VPN.
7. Kliknij dwukrotnie plik profilu, aby utworzyć profil w Tunnelblick.
8. Uruchom Tunnelblick z folderu aplikacji.
9. Kliknij ikonę Tunnelblick na pasku zadań i wybierz pozycję Połącz.

> [!IMPORTANT]
>W przypadku protokołu OpenVPN obsługiwane są tylko systemy iOS 11,0 i nowsze oraz MacOS 10,13 i nowsze.
>
## <a name="ios-clients"></a><a name="iOS"></a>Klienci z systemem iOS

1. Zainstaluj klienta OpenVPN (w wersji 2,4 lub nowszej) ze sklepu App Store.
2. Pobierz profil sieci VPN dla bramy. Można to zrobić na karcie Konfiguracja punktu do lokacji w Azure Portal lub przy użyciu polecenia "New-AzVpnClientConfiguration" w programie PowerShell.
3. Rozpakuj profil. Otwórz plik konfiguracyjny vpnconfig. ovpn z folderu OpenVPN w edytorze tekstów.
4. W sekcji certyfikatu klienta P2S wprowadź klucz publiczny certyfikatu klienta P2S w formacie base64. W przypadku certyfikatu w formacie PEM można po prostu otworzyć plik cer i skopiować klucz w formacie base64 znajdujący się pomiędzy nagłówkami certyfikatów. Skorzystaj z poniższych linków, aby uzyskać informacje na temat eksportowania certyfikatu w celu pobrania zakodowanego klucza publicznego:

   * Instrukcje [VPN Gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#cer) 
   
   * Instrukcje dotyczące [wirtualnej sieci WAN](../articles/virtual-wan/certificates-point-to-site.md#cer)
5. W sekcji klucza prywatnego wprowadź klucz prywatny certyfikatu klienta P2S w formacie base64. Aby uzyskać informacje na temat sposobu wyodrębniania klucza prywatnego, zobacz [Eksportowanie klucza prywatnego](https://openvpn.net/community-resources/how-to/#pki) w witrynie OpenVPN.
6. Nie zmieniaj innych pól.
7. Wyślij pocztą e-mail plik profilu (. ovpn) na konto e-mail, które jest skonfigurowane w aplikacji poczty na telefonie iPhone. 
8. Otwórz wiadomość e-mail w aplikacji poczty na telefonie iPhone i naciśnij załączony plik

    ![Otwórz wiadomość e-mail](./media/vpn-gateway-vwan-config-openvpn-clients/ios2.png)

9. Naciśnij przycisk **więcej** , jeśli nie widzisz opcji **Kopiuj do OpenVPN**

    ![Więcej](./media/vpn-gateway-vwan-config-openvpn-clients/ios3.png)

10. Naciśnij pozycję **Kopiuj do OpenVPN** 

    ![Kopiuj do OpenVPN](./media/vpn-gateway-vwan-config-openvpn-clients/ios4.png)

11. Naciśnij pozycję **Dodaj** na stronie **Importuj profil**

    ![Dodaj](./media/vpn-gateway-vwan-config-openvpn-clients/ios5.png)

12. Naciśnij pozycję **Dodaj** na stronie **zaimportowany profil**

    ![Naciśnij pozycję Dodaj](./media/vpn-gateway-vwan-config-openvpn-clients/ios6.png)

13. Uruchom aplikację OpenVPN i przesuń przełącznik na stronę **profilu** w prawo, aby nawiązać połączenie

    ![Connect](./media/vpn-gateway-vwan-config-openvpn-clients/ios8.png)


## <a name="linux-clients"></a><a name="linux"></a>Klienci z systemem Linux

1. Otwórz nową sesję terminala. Możesz otworzyć nową sesję, naciskając klawisze "Ctrl + Alt + t" w tym samym czasie.
2. Wprowadź następujące polecenie, aby zainstalować wymagane składniki:

   ```
   sudo apt-get install openvpn
   sudo apt-get -y install network-manager-openvpn
   sudo service network-manager restart
   ```
3. Pobierz profil sieci VPN dla bramy. Można to zrobić na karcie Konfiguracja punktu do lokacji w Azure Portal.
4. Wyeksportuj certyfikat klienta P2S utworzony i przekazany do konfiguracji P2S na bramie. Skorzystaj z poniższych linków artykułu:

   * Instrukcje [VPN Gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport) 
   
   * Instrukcje dotyczące [wirtualnej sieci WAN](../articles/virtual-wan/certificates-point-to-site.md#clientexport)
5. Wyodrębnij klucz prywatny i odcisk palca Base64 z pliku PFX. Istnieje wiele sposobów, aby to zrobić. Korzystanie z OpenSSL na komputerze jest jednym ze sposobów.

    ```
    openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
    ```
   Plik *profileinfo.txt* będzie zawierać klucz prywatny i odcisk palca urzędu certyfikacji oraz certyfikat klienta. Upewnij się, że używasz odcisku palca certyfikatu klienta.

6. Otwórz *profileinfo.txt* w edytorze tekstu. Aby uzyskać odcisk palca certyfikatu klienta (elementu podrzędnego), zaznacz tekst obejmujący między innymi "-----BEGIN CERTIFICATE-----" i "----------certyfikatu" dla certyfikatu podrzędnego i skopiuj go. Certyfikat podrzędny można zidentyfikować, przeglądając temat subject =/line.

7. Otwórz plik *vpnconfig. ovpn* i Znajdź sekcję pokazaną poniżej. Zastąp wszystko "certyfikatem" i "/CERT".

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Otwórz profileinfo.txt w edytorze tekstu. Aby uzyskać klucz prywatny, zaznacz tekst, w tym między innymi "-----Rozpocznij klucz prywatny-----" i "-----Zakończ klucz prywatny-----" i skopiuj go.

9. Otwórz plik vpnconfig. ovpn w edytorze tekstów i znajdź tę sekcję. Wklej klucz prywatny, zastępując wszystkie elementy od i "Key" i "/Key".

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```

10. Nie zmieniaj innych pól. Użyj konfiguracji wprowadzonej w danych wejściowych klienta, aby nawiązać połączenie z siecią VPN.
11. Aby nawiązać połączenie przy użyciu wiersza polecenia, wpisz następujące polecenie:
  
    ```
    sudo openvpn --config <name and path of your VPN profile file>&
    ```
12. Aby nawiązać połączenie przy użyciu graficznego interfejsu użytkownika, przejdź do pozycji Ustawienia systemu.
13. Kliknij **+** , aby dodać nowe połączenie sieci VPN.
14. W obszarze **Dodaj sieć VPN** wybierz pozycję **Importuj z pliku...**
15. Przejdź do pliku profilu, a następnie kliknij dwukrotnie lub wybierz pozycję **Otwórz**.
16. Kliknij przycisk **Dodaj** w oknie **Dodawanie sieci VPN** .
  
    ![Importuj z pliku](./media/vpn-gateway-vwan-config-openvpn-clients/import.png)
17. Możesz nawiązać połączenie, przełączając sieć VPN **na** stronie **ustawień sieci** lub ikonę sieci na pasku zadań.
