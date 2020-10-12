---
title: 'Tworzenie & Instalowanie plików konfiguracji klienta sieci VPN P2S: uwierzytelnianie certyfikatu'
titleSuffix: Azure VPN Gateway
description: Tworzenie i Instalowanie plików konfiguracji klienta sieci VPN systemu Windows, Linux, Linux (klient strongswan) i macOS X na potrzeby uwierzytelniania certyfikatu P2S.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 3a7a9711f04a4b54459d57a05ae65b4eedf1d6b6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90986651"
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-p2s-configurations"></a>Tworzenie i instalowanie plików konfiguracji klienta sieci VPN dla konfiguracji połączeń punkt-lokacja z natywnym uwierzytelnianiem certyfikatów platformy Azure

Pliki konfiguracji klienta sieci VPN są zawarte w pliku zip. Pliki konfiguracji zapewniają ustawienia wymagane dla natywnych klientów systemu Windows, Mac IKEv2 lub Linux do łączenia się z siecią wirtualną za pośrednictwem połączeń typu punkt-lokacja, które korzystają z natywnego uwierzytelniania certyfikatu platformy Azure.

Pliki konfiguracji klienta są specyficzne dla konfiguracji sieci VPN dla sieci wirtualnej. W przypadku zmiany konfiguracji sieci VPN typu punkt-lokacja po wygenerowaniu plików konfiguracji klienta sieci VPN, takich jak typ protokołu sieci VPN lub typ uwierzytelniania, należy pamiętać o wygenerowaniu nowych plików konfiguracji klienta sieci VPN dla urządzeń użytkowników. 

* Aby uzyskać więcej informacji na temat połączeń punkt-lokacja, zobacz [About Point-to-Site VPN](point-to-site-about.md) (Informacje o sieci VPN typu punkt-lokacja).
* Instrukcje OpenVPN można znaleźć w temacie [Configure OpenVPN for P2S](vpn-gateway-howto-openvpn.md) i [Configure OpenVPN clients](vpn-gateway-howto-openvpn-clients.md).

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="generate-vpn-client-configuration-files"></a><a name="generate"></a>Generuj pliki konfiguracji klienta sieci VPN

Przed rozpoczęciem upewnij się, że na urządzeniu użytkownika jest zainstalowany prawidłowy certyfikat dla wszystkich użytkowników nawiązujących połączenie. Aby uzyskać więcej informacji na temat instalowania certyfikatu klienta, zobacz [Instalowanie certyfikatu klienta](point-to-site-how-to-vpn-client-install-azure-cert.md).

Można generować pliki konfiguracji klienta przy użyciu programu PowerShell lub za pomocą Azure Portal. Każda metoda zwraca ten sam plik zip. Rozpakuj plik, aby wyświetlić następujące foldery:

  * **WindowsAmd64** i **WindowsX86**, które zawierają odpowiednio pakiety Instalatora systemu Windows 32-bit i 64-bitowego. Pakiet Instalatora **WindowsAmd64** jest przeznaczony dla wszystkich obsługiwanych 64-bitowych klientów z systemem Windows, a nie tylko AMD.
  * **Ogólne, który**zawiera ogólne informacje służące do tworzenia własnej konfiguracji klienta sieci VPN. Folder ogólny jest dostępny, jeśli na bramie skonfigurowano protokół IKEv2 lub SSTP + IKEv2. Jeśli skonfigurowano tylko protokół SSTP, folder ogólny nie jest obecny.

### <a name="generate-files-using-the-azure-portal"></a><a name="zipportal"></a>Generuj pliki przy użyciu Azure Portal

1. W Azure Portal przejdź do bramy sieci wirtualnej dla sieci wirtualnej, z którą chcesz nawiązać połączenie.
2. Na stronie Brama sieci wirtualnej kliknij pozycję **Konfiguracja punktu do lokacji**.

   ![Pobierz Portal klienta](./media/point-to-site-vpn-client-configuration-azure-cert/client-configuration-portal.png)
3. W górnej części strony Konfiguracja punktu do lokacji kliknij pozycję **Pobierz klienta sieci VPN**. Generowanie pakietu konfiguracji klienta może potrwać kilka minut.
4. Twoja przeglądarka wskazuje, że plik zip konfiguracji klienta jest dostępny. Nazwa taka sama nazywa się bramą. Rozpakuj plik, aby wyświetlić foldery.

### <a name="generate-files-using-powershell"></a><a name="zipps"></a>Generowanie plików przy użyciu programu PowerShell


1. Podczas generowania plików konfiguracji klienta sieci VPN wartość "-bioEapTls" jest równa "{0}". Wygeneruj pliki konfiguracji klienta VPN za pomocą następującego polecenia:

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```
2. Skopiuj adres URL do przeglądarki, aby pobrać plik zip, a następnie Rozpakuj plik, aby wyświetlić foldery.

## <a name="windows"></a><a name="installwin"></a>Windows

Tego samego pakietu konfiguracyjnego klienta sieci VPN można użyć na każdym komputerze klienckim z systemem Windows, o ile wersja jest zgodna z architekturą klienta. Listę obsługiwanych systemów operacyjnych klienta znajduje się w sekcji "punkt-lokacja [" VPN Gateway często zadawane pytania](vpn-gateway-vpn-faq.md#P2S).

>[!NOTE]
>Użytkownik musi mieć uprawnienia administratora na komputerze klienckim z systemem Windows, z którego chcesz się połączyć.
>
>

Wykonaj następujące kroki, aby skonfigurować natywnego klienta sieci VPN systemu Windows na potrzeby uwierzytelniania certyfikatu:

1. Wybierz pliki konfiguracji klienta sieci VPN, które odpowiadają architekturze komputera z systemem Windows. W przypadku 64-bitowej architektury procesora wybierz pakiet instalatora „VpnClientSetupAmd64”. W przypadku 32-bitowej architektury procesora wybierz pakiet instalatora „VpnClientSetupX86”. 
2. Kliknij dwukrotnie pakiet, aby go zainstalować. Jeśli zobaczysz okno podręczne SmartScreen, kliknij pozycję **więcej informacji**, a następnie **Uruchom mimo wszystko**.
3. Na komputerze klienckim przejdź do obszaru **Ustawienia sieci** i kliknij pozycję **Sieć VPN**. Połączenie z siecią VPN zawiera nazwę sieci wirtualnej, z którą jest nawiązywane połączenie. 
4. Zanim spróbujesz nawiązać połączenie, sprawdź, czy zainstalowano certyfikat klienta na komputerze klienckim. Certyfikat klienta jest wymagany w celu uwierzytelniania podczas korzystania z natywnego typu certyfikatu uwierzytelniania platformy Azure. Więcej informacji o generowaniu certyfikatów znajduje się w temacie [Generate Certificates](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Informacje o sposobie instalowania certyfikatu klienta znajdują się w temacie [Install a Client Certificate (Instalowanie certyfikatu klienta](point-to-site-how-to-vpn-client-install-azure-cert.md)).

## <a name="mac-os-x"></a><a name="installmac"></a>Mac (OS X)

 Należy ręcznie skonfigurować natywnego klienta sieci VPN z protokołem IKEv2 na każdym komputerze Mac, który jest połączony z platformą Azure. Platforma Azure nie zapewnia pliku mobileconfig dla uwierzytelniania natywnego certyfikatu platformy Azure. **Ogólne** zawiera wszystkie informacje potrzebne do konfiguracji. Jeśli nie widzisz folderu Generic w elementach pobranych, prawdopodobnie nie wybrano protokołu IKEv2 jako typu tunelu. Należy pamiętać, że podstawowa jednostka SKU bramy sieci VPN nie obsługuje protokołu IKEv2. Po wybraniu protokołu IKEv2 należy ponownie wygenerować plik zip, aby pobrać folder Generic.<br>Folder Generic zawiera następujące pliki:

* **VpnSettings.xml**, który zawiera ważne ustawienia, takie jak adres serwera i typ tunelu. 
* **VpnServerRoot. cer**, który zawiera certyfikat główny wymagany do zweryfikowania VPN Gateway platformy Azure podczas instalacji połączenia P2S.

Wykonaj następujące kroki, aby skonfigurować natywnego klienta sieci VPN na komputerze Mac na potrzeby uwierzytelniania certyfikatów. Musisz wykonać te czynności na każdym komputerze Mac, który będzie łączyć się z platformą Azure:

1. Zaimportuj certyfikat główny **VpnServerRoot** na komputer Mac. Można to zrobić, kopiując plik do komputera Mac i klikając go dwukrotnie. Kliknij przycisk **Dodaj** , aby zaimportować.

   ![Dodaj certyfikat](./media/point-to-site-vpn-client-configuration-azure-cert/addcert.png)
  
    >[!NOTE]
    >Dwukrotne kliknięcie certyfikatu może nie wyświetlać okna dialogowego **Dodawanie** , ale certyfikat jest instalowany w prawidłowym magazynie. Certyfikat można sprawdzić w pęku kluczy logowania w kategorii certyfikaty.
    >
  
2. Sprawdź, czy zainstalowano certyfikat klienta, który został wystawiony przez certyfikat główny przekazany do platformy Azure po skonfigurowaniu ustawień P2S. Różni się to od VPNServerRoot zainstalowanego w poprzednim kroku. Certyfikat klienta jest używany do uwierzytelniania i jest wymagany. Więcej informacji o generowaniu certyfikatów znajduje się w temacie [Generate Certificates](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Informacje o sposobie instalowania certyfikatu klienta znajdują się w temacie [Install a Client Certificate (Instalowanie certyfikatu klienta](point-to-site-how-to-vpn-client-install-azure-cert.md)).
3. Otwórz okno dialogowe **Sieć** w obszarze **Preferencje sieciowe** i kliknij przycisk **"+"** , aby utworzyć nowy profil połączenia klienta VPN dla połączenia P2S z siecią wirtualną platformy Azure.

   Wartość **interfejsu** to "VPN", a wartość **typu sieci VPN** to "IKEv2". Określ nazwę profilu w polu **nazwa usługi** , a następnie kliknij przycisk **Utwórz** , aby utworzyć profil połączenia klienta sieci VPN.

   ![Zrzut ekranu przedstawia okno sieci z opcją wyboru interfejsu, wybierz opcję Typ sieci VPN i wprowadź nazwę usługi.](./media/point-to-site-vpn-client-configuration-azure-cert/network.png)
4. W folderze **ogólnym** , w pliku **VpnSettings.xml** skopiuj wartość tagu **VpnServer** . Wklej tę wartość do pól **adres serwera** i **Identyfikator zdalny** profilu.

   ![Informacje o serwerze](./media/point-to-site-vpn-client-configuration-azure-cert/server.png)
5. Kliknij pozycję **Ustawienia uwierzytelniania** i wybierz pozycję **certyfikat**.W przypadku usługi **Catalina**kliknij **Brak** , a następnie pozycję **certyfikat**

   ![ustawienia uwierzytelniania](./media/point-to-site-vpn-client-configuration-azure-cert/authsettings.png)

   * W przypadku Catalina wybierz **Brak** , a następnie **certyfikat**. **Wybierz** prawidłowy certyfikat:
   
   ![Zrzut ekranu przedstawia okno sieć z opcją brak wybraną dla ustawień uwierzytelniania i wybranego certyfikatu.](./media/point-to-site-vpn-client-configuration-azure-cert/catalina.png)

6. Kliknij przycisk **Wybierz...** Aby wybrać certyfikat klienta, który ma być używany do uwierzytelniania. Jest to certyfikat zainstalowany w kroku 2.

   ![Zrzut ekranu przedstawia okno sieci z ustawieniami uwierzytelniania, w którym można wybrać certyfikat.](./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png)
7. **Wybierz tożsamość** wyświetla listę certyfikatów do wyboru. Wybierz odpowiedni certyfikat, a następnie kliknij przycisk **Kontynuuj**.

   ![Zrzut ekranu przedstawia okno dialogowe Wybieranie tożsamości, w którym można wybrać odpowiedni certyfikat.](./media/point-to-site-vpn-client-configuration-azure-cert/identity.png)
8. W polu **Identyfikator lokalny** Określ nazwę certyfikatu (z kroku 6). W tym przykładzie jest to "ikev2Client.com". Następnie kliknij przycisk **Zastosuj** , aby zapisać zmiany.

   ![apply](./media/point-to-site-vpn-client-configuration-azure-cert/applyconnect.png)
9. W oknie dialogowym **Sieć** kliknij pozycję **Zastosuj** , aby zapisać wszystkie zmiany. Następnie kliknij przycisk **Połącz** , aby rozpocząć połączenie P2S z siecią wirtualną platformy Azure.

## <a name="linux-strongswan-gui"></a><a name="linuxgui"></a>Linux (klient strongswan GUI)

### <a name="install-strongswan"></a><a name="installstrongswan"></a>Zainstaluj klient strongswan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a><a name="genlinuxcerts"></a>Generowanie certyfikatów klienta

Jeśli certyfikaty nie zostały jeszcze wygenerowane, wykonaj następujące czynności:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a><a name="install"></a>Instalowanie i konfigurowanie

Następujące instrukcje zostały utworzone w witrynie Ubuntu 18.0.4. Ubuntu 16.0.10 nie obsługuje graficznego interfejsu użytkownika klient strongswan. Jeśli chcesz użyć Ubuntu 16.0.10, należy użyć [wiersza polecenia](#linuxinstallcli). Poniższe przykłady mogą nie odpowiadać ekranom widocznym w zależności od wersji systemu Linux i klient strongswan.

1. Otwórz **Terminal** , aby zainstalować **Klient strongswan** i jego Menedżera sieci, uruchamiając polecenie w przykładzie.

   ```
   sudo apt install network-manager-strongswan
   ```
2. Wybierz pozycję **Ustawienia**, a następnie pozycję **Sieć**.

   ![Edytuj połączenia](./media/point-to-site-vpn-client-configuration-azure-cert/editconnections.png)
3. Kliknij **+** przycisk, aby utworzyć nowe połączenie.

   ![Dodawanie połączenia](./media/point-to-site-vpn-client-configuration-azure-cert/addconnection.png)
4. Wybierz pozycję **IPSec/IKEv2 (klient strongswan)** z menu, a następnie kliknij dwukrotnie. W tym kroku można nazwać połączenie.

   ![Wybierz typ połączenia](./media/point-to-site-vpn-client-configuration-azure-cert/choosetype.png)
5. Otwórz plik **VpnSettings.xml** z folderu **ogólnego** zawartego w pobranych plikach konfiguracji klienta. Znajdź tag o nazwie **VpnServer** i skopiuj nazwę, zaczynając od "azuregateway" i kończąc na ". cloudapp.NET".

   ![Nazwa kopii](./media/point-to-site-vpn-client-configuration-azure-cert/vpnserver.png)
6. Wklej tę nazwę w polu **adres** nowego połączenia sieci VPN w sekcji **Gateway** . Następnie wybierz ikonę folderu na końcu pola **certyfikat** , przejdź do folderu **ogólnego** , a następnie wybierz plik **VpnServerRoot** .
7. W sekcji **Klient** połączenia w obszarze **uwierzytelnianie**wybierz pozycję **certyfikat/klucz prywatny**. W polu **certyfikat** i **klucz prywatny**wybierz certyfikat i klucz prywatny, który został utworzony wcześniej. W obszarze **Opcje**wybierz pozycję **Żądaj wewnętrznego adresu IP**. Następnie kliknij przycisk **Add** (Dodaj).

   ![Żądaj wewnętrznego adresu IP](./media/point-to-site-vpn-client-configuration-azure-cert/turnon.png)
8. **Włącz połączenie.**

## <a name="linux-strongswan-cli"></a><a name="linuxinstallcli"></a>Linux (klient strongswan CLI)

### <a name="install-strongswan"></a>Zainstaluj klient strongswan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a>Generowanie certyfikatów klienta

Jeśli certyfikaty nie zostały jeszcze wygenerowane, wykonaj następujące czynności:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a>Instalowanie i konfigurowanie

1. Pobierz pakiet klienta z Azure Portal.
2. Wyodrębnij plik.
3. W folderze **ogólnym** Skopiuj lub Przenieś VpnServerRoot. cer do/etc/IPSec.d/cacerts.
4. Kopiowanie lub przenoszenie klienta CP. p12 do/etc/IPSec.d/Private/. Ten plik jest certyfikatem klienta dla VPN Gateway platformy Azure.
5. Otwórz plik VpnSettings.xml i skopiuj `<VpnServer>` wartość. Ta wartość zostanie użyta w następnym kroku.
6. Dostosuj wartości w poniższym przykładzie, a następnie Dodaj przykład do konfiguracji/etc/IPSec.conf.
  
   ```
   conn azure
         keyexchange=ikev2
         type=tunnel
         leftfirewall=yes
         left=%any
         leftauth=eap-tls
         leftid=%client # use the DNS alternative name prefixed with the %
         right= Enter the VPN Server value here# Azure VPN gateway address
         rightid=% # Enter the VPN Server value here# Azure VPN gateway FQDN with %
         rightsubnet=0.0.0.0/0
         leftsourceip=%config
         auto=add
   ```
6. Dodaj następujący do */etc/IPSec.Secrets*.

   ```
   : P12 client.p12 'password' # key filename inside /etc/ipsec.d/private directory
   ```

7. Uruchom następujące polecenia:

   ```
   # ipsec restart
   # ipsec up azure
   ```

## <a name="next-steps"></a>Następne kroki

Wróć do artykułu, aby [zakończyć konfigurację programu P2S](vpn-gateway-howto-point-to-site-rm-ps.md).

Aby rozwiązać problemy z połączeniami P2S, zobacz następujące artykuły:

  * [Rozwiązywanie problemów z połączeniami "punkt-lokacja" platformy Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
  * [Rozwiązywanie problemów z połączeniami sieci VPN od klientów sieci VPN macOS X](vpn-gateway-troubleshoot-point-to-site-osx-ikev2.md)
