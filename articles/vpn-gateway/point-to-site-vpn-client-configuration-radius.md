---
title: 'Azure VPN Gateway: Tworzenie & Instalowanie plików konfiguracji klienta sieci VPN — P2S połączeń usługi RADIUS'
description: Tworzenie plików konfiguracji klienta sieci VPN systemu Windows, OS X i Linux dla połączeń korzystających z uwierzytelniania usługi RADIUS.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: e6d811e19bb19c8c8bf96764cfcca2b1294f4a85
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91440057"
---
# <a name="create-and-install-vpn-client-configuration-files-for-p2s-radius-authentication"></a>Tworzenie i Instalowanie plików konfiguracji klienta sieci VPN na potrzeby uwierzytelniania P2S RADIUS

Aby nawiązać połączenie z siecią wirtualną przy użyciu połączenia typu punkt-lokacja (P2S), należy skonfigurować urządzenie klienckie, z którego będziesz się łączyć. Połączenia sieci VPN P2S można tworzyć z urządzeń klienckich z systemami Windows, OS X i Linux. 

W przypadku korzystania z uwierzytelniania usługi RADIUS istnieje wiele opcji uwierzytelniania: uwierzytelnianie nazwy użytkownika/hasła, uwierzytelnianie certyfikatów i inne typy uwierzytelniania. Konfiguracja klienta sieci VPN różni się w zależności od typu uwierzytelniania. Aby skonfigurować klienta sieci VPN, należy użyć plików konfiguracji klienta, które zawierają wymagane ustawienia. Ten artykuł ułatwia utworzenie i zainstalowanie konfiguracji klienta sieci VPN dla typu uwierzytelniania usługi RADIUS, który ma być używany.

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

Przepływ konfiguracji dla uwierzytelniania usługi P2S RADIUS jest następujący:

1. [Skonfiguruj bramę sieci VPN platformy Azure pod kątem łączności z usługą P2S](point-to-site-how-to-radius-ps.md).
2. [Skonfiguruj serwer RADIUS na potrzeby uwierzytelniania](point-to-site-how-to-radius-ps.md#radius). 
3. **Uzyskaj konfigurację klienta sieci VPN dla wybranej opcji uwierzytelniania i użyj jej do skonfigurowania klienta sieci VPN** (ten artykuł).
4. [Ukończ konfigurację programu P2S i Połącz się z nią](point-to-site-how-to-radius-ps.md).

>[!IMPORTANT]
>W przypadku zmiany konfiguracji sieci VPN typu punkt-lokacja po wygenerowaniu profilu konfiguracji klienta sieci VPN, takiego jak typ protokołu sieci VPN lub typ uwierzytelniania, należy wygenerować i zainstalować nową konfigurację klienta sieci VPN na urządzeniach użytkowników.
>
>

Aby skorzystać z sekcji w tym artykule, najpierw należy wybrać typ uwierzytelniania, który ma być używany: Nazwa użytkownika/hasło, certyfikat lub inne typy uwierzytelniania. Każda sekcja zawiera kroki dla systemów Windows, OS X i Linux (w tym momencie dostępne są ograniczone kroki).


## <a name="usernamepassword-authentication"></a><a name="adeap"></a>Uwierzytelnianie nazwy użytkownika/hasła

Uwierzytelnianie przy użyciu nazwy użytkownika i hasła można skonfigurować w taki sposób, aby używać Active Directory lub nie Active Directory. W każdym scenariuszu upewnij się, że wszyscy użytkownicy nawiązujący połączenia mają poświadczenia nazwy użytkownika/hasła, które mogą być uwierzytelniane za pomocą usługi RADIUS.

Podczas konfigurowania uwierzytelniania nazwy użytkownika/hasła można utworzyć tylko konfigurację dla EAP-MSCHAPv2 protokołu uwierzytelniania przy użyciu nazwy użytkownika/hasła. W poleceniach `-AuthenticationMethod` jest `EapMSChapv2` .

### <a name="1-generate-vpn-client-configuration-files"></a><a name="usernamefiles"></a> 1. Generuj pliki konfiguracji klienta sieci VPN

Pliki konfiguracji klienta sieci VPN można generować przy użyciu Azure Portal lub Azure PowerShell.

#### <a name="azure-portal"></a>Azure Portal

1. Przejdź do bramy sieci wirtualnej.
2. Kliknij pozycję **Konfiguracja punktu do lokacji**.
3. Kliknij pozycję **Pobierz klienta sieci VPN**.
4. Wybierz klienta i wprowadź wymagane informacje.
5. Kliknij przycisk **Pobierz** , aby wygenerować plik. zip.
6. Plik. zip zostanie pobrany, zazwyczaj do folderu downloads.

#### <a name="azure-powershell"></a>Azure PowerShell

Generuj pliki konfiguracji klienta sieci VPN do użycia z uwierzytelnianiem przy użyciu nazwy użytkownika/hasła. Pliki konfiguracji klienta sieci VPN można generować przy użyciu następującego polecenia:

```azurepowershell-interactive
New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapMSChapv2"
```
 
Uruchomienie polecenia zwraca link. Skopiuj i wklej link do przeglądarki sieci Web, aby pobrać **VpnClientConfiguration.zip**. Rozpakuj plik, aby wyświetlić następujące foldery: 
 
* **WindowsAmd64** i **WindowsX86**: te foldery zawierają odpowiednio pakiety Instalatora Windows 64-bit i 32-bitowe. 
* **Ogólne**: ten folder zawiera informacje ogólne, których można użyć do utworzenia własnej konfiguracji klienta sieci VPN. Ten folder nie jest potrzebny do konfiguracji uwierzytelniania nazwy użytkownika/hasła.
* **Mac**: w przypadku skonfigurowania protokołu IKEv2 podczas tworzenia bramy sieci wirtualnej zostanie wyświetlony folder o nazwie **Mac** zawierający plik **MOBILECONFIG** . Ten plik jest używany do konfigurowania klientów na komputery Mac.

Jeśli pliki konfiguracji klienta zostały już utworzone, można je pobrać przy użyciu `Get-AzVpnClientConfiguration` polecenia cmdlet. Jeśli jednak wprowadzisz jakiekolwiek zmiany konfiguracji sieci VPN P2S, takie jak typ protokołu sieci VPN lub typ uwierzytelniania, konfiguracja nie zostanie automatycznie zaktualizowana. `New-AzVpnClientConfiguration`Aby utworzyć nową konfigurację, należy uruchomić polecenie cmdlet.

Aby pobrać wcześniej wygenerowane pliki konfiguracji klienta, użyj następującego polecenia:

```azurepowershell-interactive
Get-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW"
```

### <a name="2-configure-vpn-clients"></a><a name="setupusername"></a> 2. Konfigurowanie klientów sieci VPN

Można skonfigurować następujących klientów sieci VPN:

* [Windows](#adwincli)
* [Mac (OS X)](#admaccli)
* [Linux przy użyciu klient strongswan](#adlinuxcli)
 
#### <a name="windows-vpn-client-setup"></a><a name="adwincli"></a>Konfiguracja klienta sieci VPN systemu Windows

Tego samego pakietu konfiguracyjnego klienta sieci VPN można użyć na każdym komputerze klienckim z systemem Windows, o ile wersja jest zgodna z architekturą klienta. Aby uzyskać listę obsługiwanych systemów operacyjnych klienta, zobacz [często zadawane pytania](vpn-gateway-vpn-faq.md#P2S).

Wykonaj następujące kroki, aby skonfigurować natywnego klienta sieci VPN systemu Windows na potrzeby uwierzytelniania certyfikatu:

1. Wybierz pliki konfiguracji klienta sieci VPN, które odpowiadają architekturze komputera z systemem Windows. W przypadku 64-bitowej architektury procesora wybierz pakiet Instalatora **VpnClientSetupAmd64** . W przypadku 32-bitowej architektury procesora wybierz pakiet Instalatora **VpnClientSetupX86** . 
2. Aby zainstalować pakiet, kliknij go dwukrotnie. Jeśli zobaczysz okno podręczne SmartScreen, wybierz opcję **więcej informacji**  >  **Uruchom mimo wszystko**.
3. Na komputerze klienckim przejdź do **ustawień sieci** i wybierz pozycję **Sieć VPN**. Połączenie z siecią VPN zawiera nazwę sieci wirtualnej, z którą jest nawiązywane połączenie. 

#### <a name="mac-os-x-vpn-client-setup"></a><a name="admaccli"></a>Konfiguracja klienta sieci VPN dla systemu Mac (OS X)

1. Wybierz plik **VpnClientSetup MOBILECONFIG** i wyślij go do każdego z użytkowników. Możesz użyć poczty e-mail lub innej metody.

2. Zlokalizuj plik **MOBILECONFIG** na komputerze Mac.

   ![Lokalizacja pliku MOBILECONFIG](./media/point-to-site-vpn-client-configuration-radius/admobileconfigfile.png)

3. Opcjonalny krok — Jeśli chcesz określić niestandardowy serwer DNS, Dodaj następujące wiersze do pliku **MOBILECONFIG** :

   ```xml
    <key>DNS</key>
    <dict>
      <key>ServerAddresses</key>
        <array>
            <string>10.0.0.132</string>
        </array>
      <key>SupplementalMatchDomains</key>
        <array>
            <string>TestDomain.com</string>
        </array>
    </dict> 
   ```
4. Kliknij dwukrotnie profil, aby go zainstalować, a następnie wybierz pozycję **Kontynuuj**. Nazwa profilu jest taka sama jak nazwa sieci wirtualnej.

   ![Komunikat instalacji](./media/point-to-site-vpn-client-configuration-radius/adinstall.png)
5. Wybierz pozycję **Kontynuuj** , aby zaufać nadawcy profilu i kontynuować instalację.

   ![Komunikat z potwierdzeniem](./media/point-to-site-vpn-client-configuration-radius/adcontinue.png)
6. Podczas instalacji profilu można określić nazwę użytkownika i hasło do uwierzytelniania sieci VPN. Wprowadzanie tych informacji nie jest obowiązkowe. W takim przypadku informacje są zapisywane i automatycznie używane po zainicjowaniu połączenia. Wybierz pozycję **Zainstaluj** , aby wykonać operację.

   ![Pola nazwy użytkownika i hasła dla sieci VPN](./media/point-to-site-vpn-client-configuration-radius/adsettings.png)
7. Wprowadź nazwę użytkownika i hasło dla uprawnień wymaganych do zainstalowania profilu na komputerze. Wybierz przycisk **OK**.

   ![Pola Nazwa użytkownika i hasło dla instalacji profilu](./media/point-to-site-vpn-client-configuration-radius/adusername.png)
8. Po zainstalowaniu profilu jest on widoczny w oknie dialogowym **Profile** . Możesz również otworzyć to okno dialogowe później z poziomu **preferencji systemowych**.

   ![Okno dialogowe "Profile"](./media/point-to-site-vpn-client-configuration-radius/adsystempref.png)
9. Aby uzyskać dostęp do połączenia sieci VPN, Otwórz okno dialogowe **Sieć** z **preferencji systemu**.

   ![Ikony w Preferencjach systemowych](./media/point-to-site-vpn-client-configuration-radius/adnetwork.png)
10. Połączenie sieci VPN jest wyświetlane jako **IkeV2-VPN**. Nazwę można zmienić, aktualizując plik **MOBILECONFIG** .

    ![Szczegóły połączenia sieci VPN](./media/point-to-site-vpn-client-configuration-radius/adconnection.png)
11. Wybierz pozycję **Ustawienia uwierzytelniania**. Na liście wybierz pozycję **username** i wprowadź swoje poświadczenia. Jeśli poświadczenia zostały wprowadzone wcześniej, na liście zostanie automatycznie wybrana **Nazwa użytkownika** , a nazwa użytkownika i hasło zostaną wstępnie wypełnione. Wybierz **przycisk OK** , aby zapisać ustawienia.

    ![Zrzut ekranu przedstawiający listę rozwijaną "ustawienia uwierzytelniania" z wybraną opcją "username".](./media/point-to-site-vpn-client-configuration-radius/adauthentication.png)
12. W oknie dialogowym **Sieć** wybierz pozycję **Zastosuj** , aby zapisać zmiany. Aby zainicjować połączenie, wybierz pozycję **Połącz**.

#### <a name="linux-vpn-client-setup-through-strongswan"></a><a name="adlinuxcli"></a>Konfiguracja klienta sieci VPN z systemem Linux za pośrednictwem klient strongswan

Następujące instrukcje zostały utworzone za pomocą klient strongswan 5.5.1 na Ubuntu 17.0.4. Rzeczywiste ekrany mogą się różnić w zależności od wersji systemu Linux i klient strongswan.

1. Otwórz **Terminal** , aby zainstalować **Klient strongswan** i jego Menedżera sieci, uruchamiając polecenie w przykładzie. Jeśli zostanie wyświetlony komunikat o błędzie związanym z programem `libcharon-extra-plugins` , zastąp go wyrażeniem `strongswan-plugin-eap-mschapv2` .

   ```Terminal
   sudo apt-get install strongswan libcharon-extra-plugins moreutils iptables-persistent network-manager-strongswan
   ```
2. Wybierz ikonę **Menedżera sieci** (Strzałka w górę/Strzałka w dół), a następnie wybierz pozycję **Edytuj połączenia**.

   ![Wybór opcji "Edytuj połączenia" w Menedżerze sieci](./media/point-to-site-vpn-client-configuration-radius/EditConnection.png)
3. Wybierz przycisk **Dodaj** , aby utworzyć nowe połączenie.

   ![Przycisk "Dodaj" dla połączenia](./media/point-to-site-vpn-client-configuration-radius/AddConnection.png)
4. Wybierz pozycję **IPSec/IKEv2 (klient strongswan)** z menu rozwijanego, a następnie wybierz pozycję **Utwórz**. W tym kroku można zmienić nazwę połączenia.

   ![Wybieranie typu połączenia](./media/point-to-site-vpn-client-configuration-radius/AddIKEv2.png)
5. Otwórz plik **VpnSettings.xml** z folderu **ogólnego** pobranych plików konfiguracji klienta. Znajdź tag o nazwie `VpnServer` i skopiuj jego nazwę, zaczynając od `azuregateway` i kończąc na `.cloudapp.net` .

   ![Zawartość pliku VpnSettings.xml](./media/point-to-site-vpn-client-configuration-radius/VpnSettings.png)
6. Wklej tę nazwę w polu **adres** nowego połączenia sieci VPN w sekcji **Gateway** . Następnie wybierz ikonę folderu na końcu pola **certyfikat** , przejdź do folderu **ogólnego** , a następnie wybierz plik **VpnServerRoot** .
7. W sekcji **Klient** połączenia wybierz pozycję **EAP** na potrzeby **uwierzytelniania**, a następnie wprowadź nazwę użytkownika i hasło. Może być konieczne wybranie ikony kłódki po prawej stronie, aby zapisać te informacje. Następnie wybierz pozycję **Zapisz**.

   ![Edytowanie ustawień połączenia](./media/point-to-site-vpn-client-configuration-radius/editconnectionsettings.png)
8. Wybierz ikonę **Menedżera sieci** (Strzałka w górę/Strzałka w dół) i umieść kursor nad **połączeniami sieci VPN**. Zobaczysz utworzone połączenie sieci VPN. Aby zainicjować połączenie, wybierz je.

   ![Połączenie "promień sieci VPN" w Menedżerze sieci](./media/point-to-site-vpn-client-configuration-radius/ConnectRADIUS.png)

## <a name="certificate-authentication"></a><a name="certeap"></a>Uwierzytelnianie certyfikatu
 
Można utworzyć pliki konfiguracji klienta sieci VPN dla uwierzytelniania certyfikatu usługi RADIUS, który używa protokołu EAP-TLS. Zazwyczaj certyfikat wystawiony przez przedsiębiorstwo jest używany do uwierzytelniania użytkownika w sieci VPN. Upewnij się, że wszyscy użytkownicy nawiązujący połączenia mają zainstalowany certyfikat na swoich urządzeniach i że serwer RADIUS może sprawdzić poprawność certyfikatu.

>[!NOTE]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

W poleceniach `-AuthenticationMethod` jest `EapTls` . Podczas uwierzytelniania przy użyciu certyfikatu klient sprawdza poprawność serwera RADIUS, sprawdzając jego certyfikat. `-RadiusRootCert` to plik. cer zawierający certyfikat główny używany do sprawdzania poprawności serwera RADIUS.

Każde urządzenie klienckie sieci VPN wymaga zainstalowanego certyfikatu klienta. Czasami urządzenie z systemem Windows ma wiele certyfikatów klienta. Podczas uwierzytelniania może to spowodować wyskakujące okno dialogowe, które wyświetla listę wszystkich certyfikatów. Użytkownik musi następnie wybrać certyfikat do użycia. Prawidłowy certyfikat można odfiltrować, określając certyfikat główny, do którego ma być powiązany certyfikat klienta. 

`-ClientRootCert` to plik. cer zawierający certyfikat główny. Jest to opcjonalny parametr. Jeśli urządzenie, z którego chcesz nawiązać połączenie, ma tylko jeden certyfikat klienta, nie trzeba określać tego parametru.

### <a name="1-generate-vpn-client-configuration-files"></a><a name="certfiles"></a>1. Generuj pliki konfiguracji klienta sieci VPN

Generuj pliki konfiguracji klienta sieci VPN do użycia z uwierzytelnianiem certyfikatów. Pliki konfiguracji klienta sieci VPN można generować przy użyciu następującego polecenia:
 
```azurepowershell-interactive
New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls" -RadiusRootCert <full path name of .cer file containing the RADIUS root> -ClientRootCert <full path name of .cer file containing the client root> | fl
```

Uruchomienie polecenia zwraca link. Skopiuj i wklej link do przeglądarki sieci Web, aby pobrać VpnClientConfiguration.zip. Rozpakuj plik, aby wyświetlić następujące foldery:

* **WindowsAmd64** i **WindowsX86**: te foldery zawierają odpowiednio pakiety Instalatora Windows 64-bit i 32-bitowe. 
* **GenericDevice**: ten folder zawiera ogólne informacje, które są używane do tworzenia własnej konfiguracji klienta sieci VPN.

Jeśli pliki konfiguracji klienta zostały już utworzone, można je pobrać przy użyciu `Get-AzVpnClientConfiguration` polecenia cmdlet. Jeśli jednak wprowadzisz jakiekolwiek zmiany konfiguracji sieci VPN P2S, takie jak typ protokołu sieci VPN lub typ uwierzytelniania, konfiguracja nie zostanie automatycznie zaktualizowana. `New-AzVpnClientConfiguration`Aby utworzyć nową konfigurację, należy uruchomić polecenie cmdlet.

Aby pobrać wcześniej wygenerowane pliki konfiguracji klienta, użyj następującego polecenia:

```azurepowershell-interactive
Get-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" | fl
```
 
### <a name="2-configure-vpn-clients"></a><a name="setupusername"></a> 2. Konfigurowanie klientów sieci VPN

Można skonfigurować następujących klientów sieci VPN:

* [Windows](#certwincli)
* [Mac (OS X)](#certmaccli)
* Linux (obsługiwane, nie ma jeszcze kroków artykułu)

#### <a name="windows-vpn-client-setup"></a><a name="certwincli"></a>Konfiguracja klienta sieci VPN systemu Windows

1. Wybierz pakiet konfiguracyjny i zainstaluj go na urządzeniu klienckim. W przypadku 64-bitowej architektury procesora wybierz pakiet Instalatora **VpnClientSetupAmd64** . W przypadku 32-bitowej architektury procesora wybierz pakiet Instalatora **VpnClientSetupX86** . Jeśli zobaczysz okno podręczne SmartScreen, wybierz opcję **więcej informacji**  >  **Uruchom mimo wszystko**. Możesz także zapisać pakiet w celu zainstalowania go na innych komputerach klienckich.
2. Każdy klient wymaga certyfikatu klienta w celu uwierzytelnienia. Zainstaluj certyfikat klienta. Informacje o certyfikatach klientów znajdują się w temacie [Certyfikaty klienta dla punktu do lokacji](vpn-gateway-certificates-point-to-site.md). Aby zainstalować wygenerowany certyfikat, zobacz [Instalowanie certyfikatu na klientach z systemem Windows](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. Na komputerze klienckim przejdź do **ustawień sieci** i wybierz pozycję **Sieć VPN**. Połączenie z siecią VPN zawiera nazwę sieci wirtualnej, z którą jest nawiązywane połączenie.

#### <a name="mac-os-x-vpn-client-setup"></a><a name="certmaccli"></a>Konfiguracja klienta sieci VPN dla systemu Mac (OS X)

Należy utworzyć oddzielny profil dla każdego urządzenia Mac, które nawiązuje połączenie z siecią wirtualną platformy Azure. Wynika to z faktu, że te urządzenia wymagają określenia certyfikatu użytkownika w celu uwierzytelnienia w profilu. Folder **ogólny** zawiera wszystkie informacje wymagane do utworzenia profilu:

* **VpnSettings.xml** zawiera ważne ustawienia, takie jak adres serwera i typ tunelu.
* **VpnServerRoot. cer** zawiera certyfikat główny wymagany do zweryfikowania bramy sieci VPN podczas konfiguracji połączenia usługi P2S.
* **RadiusServerRoot. cer** zawiera certyfikat główny wymagany do zweryfikowania serwera RADIUS podczas uwierzytelniania.

Wykonaj następujące kroki, aby skonfigurować natywnego klienta sieci VPN na komputerze Mac na potrzeby uwierzytelniania certyfikatu:

1. Zaimportuj certyfikaty główne **VpnServerRoot** i **RadiusServerRoot** na komputer Mac. Skopiuj każdy plik na komputer Mac, kliknij go dwukrotnie, a następnie wybierz pozycję **Dodaj**.

   ![Dodawanie certyfikatu VpnServerRoot](./media/point-to-site-vpn-client-configuration-radius/addcert.png)

   ![Dodawanie certyfikatu RadiusServerRoot](./media/point-to-site-vpn-client-configuration-radius/radiusrootcert.png)
2. Każdy klient wymaga certyfikatu klienta w celu uwierzytelnienia. Zainstaluj certyfikat klienta na urządzeniu klienckim.
3. Otwórz okno dialogowe **Sieć** w obszarze **Preferencje sieciowe**. Wybierz **+** , aby utworzyć nowy profil połączenia klienta sieci VPN dla połączenia P2S z siecią wirtualną platformy Azure.

   Wartość **interfejsu** to **VPN**, a wartość **typu sieci VPN** to **IKEv2**. Określ nazwę profilu w polu **nazwa usługi** , a następnie wybierz pozycję **Utwórz** , aby utworzyć profil połączenia klienta sieci VPN.

   ![Informacje o nazwie interfejsu i usługi](./media/point-to-site-vpn-client-configuration-radius/network.png)
4. W folderze **ogólnym** , w pliku **VpnSettings.xml** skopiuj wartość tagu **VpnServer** . Wklej tę wartość w polach **adres serwera** i **Identyfikator zdalny** profilu. Pozostaw pole **Identyfikator lokalny** puste.

   ![Informacje o serwerze](./media/point-to-site-vpn-client-configuration-radius/servertag.png)
5. Wybierz pozycję **Ustawienia uwierzytelniania** i wybierz pozycję **certyfikat**. 

   ![Ustawienia uwierzytelniania](./media/point-to-site-vpn-client-configuration-radius/certoption.png)
6. Kliknij pozycję **Wybierz** , aby wybrać certyfikat, który ma być używany do uwierzytelniania.

   ![Wybieranie certyfikatu do uwierzytelnienia](./media/point-to-site-vpn-client-configuration-radius/certificate.png)
7. **Wybierz tożsamość** wyświetla listę certyfikatów do wyboru. Wybierz odpowiedni certyfikat, a następnie wybierz pozycję **Kontynuuj**.

   ![Lista "Wybierz tożsamość"](./media/point-to-site-vpn-client-configuration-radius/identity.png)
8. W polu **Identyfikator lokalny** Określ nazwę certyfikatu (z kroku 6). W tym przykładzie jest to **ikev2Client.com**. Następnie wybierz przycisk **Zastosuj** , aby zapisać zmiany.

   ![Pole "Identyfikator lokalny"](./media/point-to-site-vpn-client-configuration-radius/applyconnect.png)
9. W oknie dialogowym **Sieć** wybierz pozycję **Zastosuj** , aby zapisać wszystkie zmiany. Następnie wybierz pozycję **Połącz** , aby rozpocząć połączenie P2S z siecią wirtualną platformy Azure.

## <a name="working-with-other-authentication-types-or-protocols"></a><a name="otherauth"></a>Praca z innymi typami uwierzytelniania lub protokołami

Aby użyć innego typu uwierzytelniania (na przykład OTP) lub użyć innego protokołu uwierzytelniania (takiego jak PEAP-MSCHAPv2 zamiast protokołu EAP-MSCHAPv2), należy utworzyć własny profil konfiguracji klienta sieci VPN. Aby utworzyć profil, potrzebne są informacje, takie jak adres IP bramy sieci wirtualnej, typ tunelu i trasy podzielonego tunelu. Te informacje można uzyskać, wykonując następujące czynności:

1. Użyj `Get-AzVpnClientConfiguration` polecenia cmdlet, aby wygenerować konfigurację klienta sieci VPN dla EapMSChapv2.

2. Rozpakuj plik VpnClientConfiguration.zip i Wyszukaj folder **GenericDevice** . Ignoruj foldery zawierające Instalatorzy systemu Windows dla architektury 64-bitowe i 32-bitowe.
 
3. Folder **GenericDevice** zawiera plik XML o nazwie **VpnSettings**. Ten plik zawiera wszystkie wymagane informacje:

   * **VpnServer**: nazwa FQDN bramy sieci VPN platformy Azure. Jest to adres, z którym łączy się klient.
   * **VpnType**: typ tunelu, który jest używany do nawiązywania połączenia.
   * **Trasy**: trasy, które należy skonfigurować w profilu, tak aby tylko ruch, który jest powiązany z siecią wirtualną platformy Azure, był wysyłany za pośrednictwem tunelu P2S.
   
   Folder **GenericDevice** zawiera również plik CER o nazwie **VpnServerRoot**. Ten plik zawiera certyfikat główny wymagany do zweryfikowania bramy sieci VPN platformy Azure podczas instalacji połączenia P2S. Zainstaluj certyfikat na wszystkich urządzeniach, które będą łączyć się z siecią wirtualną platformy Azure.

## <a name="next-steps"></a>Następne kroki

Wróć do artykułu, aby [zakończyć konfigurację programu P2S](point-to-site-how-to-radius-ps.md).

Informacje dotyczące rozwiązywania problemów z P2S można znaleźć w temacie [Rozwiązywanie problemów z połączeniami punkt-lokacja platformy Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
