---
title: 'Tworzenie & Instalowanie plików konfiguracji klienta sieci VPN P2S: uwierzytelnianie certyfikatu'
titleSuffix: Azure VPN Gateway
description: Tworzenie i Instalowanie plików konfiguracji klienta sieci VPN systemu Windows, Linux, Linux (klient strongswan) i macOS X na potrzeby uwierzytelniania certyfikatu P2S.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/11/2020
ms.author: cherylmc
ms.openlocfilehash: c7b186aa1a6f63b1bc3e9dbefa5001faac967762
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94556168"
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
* **Ogólne, który** zawiera ogólne informacje służące do tworzenia własnej konfiguracji klienta sieci VPN. Folder ogólny jest dostępny, jeśli na bramie skonfigurowano protokół IKEv2 lub SSTP + IKEv2. Jeśli skonfigurowano tylko protokół SSTP, folder ogólny nie jest obecny.

### <a name="generate-files-using-the-azure-portal"></a><a name="zipportal"></a>Generuj pliki przy użyciu Azure Portal

1. W Azure Portal przejdź do bramy sieci wirtualnej dla sieci wirtualnej, z którą chcesz nawiązać połączenie.
1. Na stronie Brama sieci wirtualnej wybierz opcję **Konfiguracja punktu do lokacji**.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/download-client.png" alt-text="Pobieranie klienta VPN":::
1. W górnej części strony Konfiguracja punktu do lokacji wybierz pozycję **Pobierz klienta sieci VPN**. Generowanie pakietu konfiguracji klienta może potrwać kilka minut.
1. Twoja przeglądarka wskazuje, że plik zip konfiguracji klienta jest dostępny. Nazwa taka sama nazywa się bramą. Rozpakuj plik, aby wyświetlić foldery.

### <a name="generate-files-using-powershell"></a><a name="zipps"></a>Generowanie plików przy użyciu programu PowerShell

1. Podczas generowania plików konfiguracji klienta sieci VPN wartość "-bioEapTls" jest równa "{0}". Wygeneruj pliki konfiguracji klienta VPN za pomocą następującego polecenia:

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```

1. Skopiuj adres URL do przeglądarki, aby pobrać plik zip, a następnie Rozpakuj plik, aby wyświetlić foldery.

## <a name="windows"></a><a name="installwin"></a>Windows

[!INCLUDE [Windows instructions](../../includes/vpn-gateway-p2s-client-configuration-windows.md)]

## <a name="mac-os-x"></a><a name="installmac"></a>Mac (OS X)

 Należy ręcznie skonfigurować natywnego klienta sieci VPN z protokołem IKEv2 na każdym komputerze Mac, który jest połączony z platformą Azure. Platforma Azure nie zapewnia pliku mobileconfig dla uwierzytelniania natywnego certyfikatu platformy Azure. **Ogólne** zawiera wszystkie informacje potrzebne do konfiguracji. Jeśli nie widzisz folderu Generic w elementach pobranych, prawdopodobnie nie wybrano protokołu IKEv2 jako typu tunelu. Należy pamiętać, że podstawowa jednostka SKU bramy sieci VPN nie obsługuje protokołu IKEv2. Po wybraniu protokołu IKEv2 należy ponownie wygenerować plik zip, aby pobrać folder Generic.<br>Folder Generic zawiera następujące pliki:

* **VpnSettings.xml**, który zawiera ważne ustawienia, takie jak adres serwera i typ tunelu. 
* **VpnServerRoot. cer**, który zawiera certyfikat główny wymagany do zweryfikowania VPN Gateway platformy Azure podczas instalacji połączenia P2S.

Wykonaj następujące kroki, aby skonfigurować natywnego klienta sieci VPN na komputerze Mac na potrzeby uwierzytelniania certyfikatów. Musisz wykonać te czynności na każdym komputerze Mac, który będzie łączyć się z platformą Azure:

1. Zaimportuj certyfikat główny **VpnServerRoot** na komputer Mac. Można to zrobić, kopiując plik do komputera Mac i klikając go dwukrotnie. Wybierz pozycję **Dodaj** , aby zaimportować.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/add-certificate.png" alt-text="Zrzut ekranu przedstawiający stronę certyfikaty":::
  
    >[!NOTE]
    >Dwukrotne kliknięcie certyfikatu może nie wyświetlać okna dialogowego **Dodawanie** , ale certyfikat jest instalowany w prawidłowym magazynie. Certyfikat można sprawdzić w pęku kluczy logowania w kategorii certyfikaty.
    >
  
1. Sprawdź, czy zainstalowano certyfikat klienta, który został wystawiony przez certyfikat główny przekazany do platformy Azure po skonfigurowaniu ustawień P2S. Różni się to od VPNServerRoot zainstalowanego w poprzednim kroku. Certyfikat klienta jest używany do uwierzytelniania i jest wymagany. Więcej informacji o generowaniu certyfikatów znajduje się w temacie [Generate Certificates](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Informacje o sposobie instalowania certyfikatu klienta znajdują się w temacie [Install a Client Certificate (Instalowanie certyfikatu klienta](point-to-site-how-to-vpn-client-install-azure-cert.md)).
1. Otwórz okno dialogowe **Sieć** w obszarze **Preferencje sieci** i wybierz pozycję **"+"** , aby utworzyć nowy profil połączenia klienta sieci VPN dla połączenia P2S z siecią wirtualną platformy Azure.

   Wartość **interfejsu** to "VPN", a wartość **typu sieci VPN** to "IKEv2". Określ nazwę profilu w polu **nazwa usługi** , a następnie wybierz pozycję **Utwórz** , aby utworzyć profil połączenia klienta sieci VPN.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/network.png" alt-text="Zrzut ekranu przedstawia okno sieci z opcją wyboru interfejsu, wybierz opcję Typ sieci VPN i wprowadź nazwę usługi":::
1. W folderze **ogólnym** , w pliku **VpnSettings.xml** skopiuj wartość tagu **VpnServer** . Wklej tę wartość do pól **adres serwera** i **Identyfikator zdalny** profilu.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/server.png" alt-text="Zrzut ekranu przedstawia informacje o serwerze.":::
1. Wybierz pozycję **Ustawienia uwierzytelniania** i wybierz pozycję **certyfikat**. W przypadku **Catalina** wybierz opcję **Brak**, a następnie pozycję **certyfikat**.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/authentication-settings.png" alt-text="Zrzut ekranu przedstawia ustawienia uwierzytelniania.":::

   W przypadku Catalina wybierz **Brak** , a następnie **certyfikat**. **Wybierz** prawidłowy certyfikat:
   
   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/catalina.png" alt-text="Zrzut ekranu przedstawia okno sieć z opcją brak wybraną dla ustawień uwierzytelniania i wybranego certyfikatu.":::

1. Kliknij przycisk **Wybierz...** Aby wybrać certyfikat klienta, który ma być używany do uwierzytelniania. Jest to certyfikat zainstalowany w kroku 2.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png" alt-text="Zrzut ekranu przedstawia okno sieci z ustawieniami uwierzytelniania, w którym można wybrać certyfikat.":::
1. **Wybierz tożsamość** wyświetla listę certyfikatów do wyboru. Wybierz odpowiedni certyfikat, a następnie wybierz pozycję **Kontynuuj**.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/identity.png" alt-text="Zrzut ekranu przedstawia okno dialogowe Wybieranie tożsamości, w którym można wybrać odpowiedni certyfikat.":::

1. W polu **Identyfikator lokalny** Określ nazwę certyfikatu (z kroku 6). W tym przykładzie jest to `ikev2Client.com`. Następnie wybierz pozycję **Zastosuj** , aby zapisać zmiany.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/apply-connect.png" alt-text="Zrzut ekranu przedstawia zastosowanie.":::
1. W oknie dialogowym **Sieć** wybierz pozycję **Zastosuj** , aby zapisać wszystkie zmiany. Następnie wybierz pozycję **Połącz** , aby rozpocząć połączenie P2S z siecią wirtualną platformy Azure.

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
1. Wybierz pozycję **Ustawienia**, a następnie pozycję **Sieć**. Wybierz **+** przycisk, aby utworzyć nowe połączenie.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/edit-connections.png" alt-text="Zrzut ekranu przedstawia stronę połączenia sieciowe.":::

1. Wybierz pozycję **IPSec/IKEv2 (klient strongswan)** z menu, a następnie kliknij dwukrotnie.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/add-connection.png" alt-text="Zrzut ekranu przedstawia stronę Dodawanie sieci VPN.":::

1. Na stronie **Dodawanie sieci VPN** Dodaj nazwę połączenia sieci VPN.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/choose-type.png" alt-text="Zrzut ekranu przedstawia wybierz typ połączenia.":::
1. Otwórz plik **VpnSettings.xml** z folderu **ogólnego** zawartego w pobranych plikach konfiguracji klienta. Znajdź tag o nazwie **VpnServer** i skopiuj nazwę, zaczynając od "azuregateway" i kończąc na ". cloudapp.NET".

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/vpn-server.png" alt-text="Zrzut ekranu przedstawia dane kopiowania.":::
1. Wklej nazwę w polu **adres** nowego połączenia sieci VPN w sekcji **Gateway** . Następnie wybierz ikonę folderu na końcu pola **certyfikat** , przejdź do folderu **ogólnego** , a następnie wybierz plik **VpnServerRoot** .
1. W sekcji **Klient** połączenia w obszarze **uwierzytelnianie** wybierz pozycję **certyfikat/klucz prywatny**. W polu **certyfikat** i **klucz prywatny** wybierz certyfikat i klucz prywatny, który został utworzony wcześniej. W obszarze **Opcje** wybierz pozycję **Żądaj wewnętrznego adresu IP**. Następnie wybierz pozycję **Dodaj**.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/ip-request.png" alt-text="Zrzut ekranu przedstawia żądanie wewnętrznego adresu IP.":::

1. **Włącz połączenie.**

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/turn-on.png" alt-text="Zrzut ekranu przedstawia kopię.":::

## <a name="linux-strongswan-cli"></a><a name="linuxinstallcli"></a>Linux (klient strongswan CLI)

### <a name="install-strongswan"></a>Zainstaluj klient strongswan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a>Generowanie certyfikatów klienta

Jeśli certyfikaty nie zostały jeszcze wygenerowane, wykonaj następujące czynności:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a>Instalowanie i konfigurowanie

1. Pobierz pakiet klienta z Azure Portal.
1. Wyodrębnij plik.
1. W folderze **ogólnym** Skopiuj lub Przenieś **VpnServerRoot. cer** do programu **/etc/IPSec.d/cacerts**.
1. Skopiuj lub Przenieś **klienta CP. p12** do **/etc/IPSec.d/Private/**. Ten plik jest certyfikatem klienta dla bramy sieci VPN.
1. Otwórz plik **VpnSettings.xml** i skopiuj `<VpnServer>` wartość. Ta wartość zostanie użyta w następnym kroku.
1. Dostosuj wartości w poniższym przykładzie, a następnie Dodaj przykład do konfiguracji **/etc/IPSec.conf** .
  
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
1. Dodaj następujące wartości do **/etc/IPSec.Secrets**.

   ```
   : P12 client.p12 'password' # key filename inside /etc/ipsec.d/private directory
   ```

1. Uruchom następujące polecenia:

   ```
   # ipsec restart
   # ipsec up azure
   ```

## <a name="next-steps"></a>Następne kroki

Wróć do oryginalnego artykułu, z którego pracujesz, a następnie ukończ konfigurację usługi P2S.

* [Kroki konfiguracji programu PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md).
* [Azure Portal kroki konfiguracji](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
