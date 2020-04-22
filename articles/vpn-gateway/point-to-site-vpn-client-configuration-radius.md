---
title: 'Brama sieci VPN platformy Azure: tworzenie & instalowanie plików konfiguracyjnych klienta sieci VPN — połączenia P2S RADIUS'
description: Tworzenie plików konfiguracyjnych klienta sieci Vpn systemu Windows, Mac OS X i systemu Linux dla połączeń korzystających z uwierzytelniania RADIUS.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/10/2020
ms.author: cherylmc
ms.openlocfilehash: 69517d69a26364cf1cc950d7aaa849522decacf1
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732738"
---
# <a name="create-and-install-vpn-client-configuration-files-for-p2s-radius-authentication"></a>Tworzenie i instalowanie plików konfiguracyjnych klienta sieci VPN do uwierzytelniania P2S RADIUS

Aby połączyć się z siecią wirtualną za pośrednictwem punktu-lokacji (P2S), należy skonfigurować urządzenie klienckie, z którego nawiążesz połączenie. Połączenia sieci VPN P2S można tworzyć z urządzeń klienckich z systemami Windows, Mac OS X i Linux. 

Podczas korzystania z uwierzytelniania RADIUS istnieje wiele opcji uwierzytelniania: uwierzytelnianie nazwy użytkownika/hasła, uwierzytelnianie certyfikatów i inne typy uwierzytelniania. Konfiguracja klienta sieci VPN jest inna dla każdego typu uwierzytelniania. Aby skonfigurować klienta sieci VPN, należy użyć plików konfiguracyjnych klienta, które zawierają wymagane ustawienia. Ten artykuł ułatwia tworzenie i instalowanie konfiguracji klienta sieci VPN dla typu uwierzytelniania RADIUS, którego chcesz użyć.

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

Przepływ pracy konfiguracji uwierzytelniania P2S RADIUS jest następujący:

1. [Skonfiguruj bramę sieci VPN platformy Azure dla łączności P2S](point-to-site-how-to-radius-ps.md).
2. [Skonfiguruj serwer RADIUS do uwierzytelniania](point-to-site-how-to-radius-ps.md#radius). 
3. **Uzyskaj konfigurację klienta sieci VPN dla wybranej opcji uwierzytelniania i użyj jej do skonfigurowania klienta sieci VPN** (ten artykuł).
4. [Uzupełnij konfigurację P2S i połącz](point-to-site-how-to-radius-ps.md).

>[!IMPORTANT]
>Jeśli po wygenerowaniu profilu konfiguracji klienta sieci VPN wystąpią jakiekolwiek zmiany w konfiguracji sieci VPN, takie jak typ protokołu sieci VPN lub typ uwierzytelniania, należy wygenerować i zainstalować nową konfigurację klienta sieci VPN na urządzeniach użytkowników.
>
>

Aby użyć sekcji w tym artykule, najpierw zdecyduj, jakiego typu uwierzytelniania chcesz użyć: nazwa użytkownika/hasło, certyfikat lub inne typy uwierzytelniania. Każda sekcja zawiera kroki dla systemów Windows, Mac OS X i Linux (ograniczone kroki dostępne w tej chwili).


## <a name="usernamepassword-authentication"></a><a name="adeap"></a>Uwierzytelnianie nazwy użytkownika/hasła

Uwierzytelnianie nazwy użytkownika/hasła można skonfigurować tak, aby korzystało z usługi Active Directory lub nie korzystało z usługi Active Directory. W każdym scenariuszu upewnij się, że wszyscy użytkownicy łączący mają poświadczenia nazwy użytkownika/hasła, które mogą być uwierzytelnione za pośrednictwem usługi RADIUS.

Podczas konfigurowania uwierzytelniania nazwy użytkownika/hasła można utworzyć tylko konfigurację protokołu uwierzytelniania nazwy użytkownika/hasła EAP-MSCHAPv2. W poleceniach `-AuthenticationMethod` jest `EapMSChapv2`.

### <a name="1-generate-vpn-client-configuration-files"></a><a name="usernamefiles"></a>1. Generowanie plików konfiguracyjnych klienta SIECI VPN

Można wygenerować pliki konfiguracji klienta sieci VPN przy użyciu witryny Azure portal lub przy użyciu programu Azure PowerShell.

#### <a name="azure-portal"></a>Azure Portal

1. Przejdź do bramy sieci wirtualnej.
2. Kliknij **pozycję Konfiguracja punktu do lokacji**.
3. Kliknij **pozycję Pobierz klienta sieci VPN**.
4. Wybierz klienta i wypełnij wszelkie wymagane informacje.
5. Kliknij **przycisk Pobierz,** aby wygenerować plik zip.
6. Plik zip zostanie pobrany, zazwyczaj do folderu Pobrane.

#### <a name="azure-powershell"></a>Azure PowerShell

Generowanie plików konfiguracyjnych klienta sieci VPN do użycia z uwierzytelnianiem nazwy użytkownika/hasła. Pliki konfiguracyjne klienta sieci VPN można wygenerować za pomocą następującego polecenia:

```azurepowershell-interactive
New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapMSChapv2"
```
 
Uruchomienie polecenia zwraca łącze. Skopiuj i wklej link do przeglądarki internetowej, aby pobrać **VpnClientConfiguration.zip**. Rozpaj plik, aby wyświetlić następujące foldery: 
 
* **WindowsAmd64** i **WindowsX86**: Te foldery zawierają odpowiednio 64-bitowe i 32-bitowe pakiety instalacyjne systemu Windows. 
* **Ogólny:** Ten folder zawiera ogólne informacje używane do tworzenia własnej konfiguracji klienta sieci VPN. Nie potrzebujesz tego folderu dla konfiguracji uwierzytelniania nazwy użytkownika/hasła.
* **Mac**: Jeśli podczas tworzenia bramy sieci wirtualnej skonfigurowano usługę IKEv2, zostanie wyświetlony folder o nazwie **Mac** zawierający plik **mobileconfig.** Ten plik służy do konfigurowania klientów komputerów Mac.

Jeśli już utworzono pliki konfiguracyjne klienta, `Get-AzVpnClientConfiguration` można je pobrać za pomocą polecenia cmdlet. Jeśli jednak wybrano jakiekolwiek zmiany w konfiguracji sieci VPN P2S, takie jak typ protokołu sieci VPN lub typ uwierzytelniania, konfiguracja nie zostanie automatycznie zaktualizowana. Aby utworzyć `New-AzVpnClientConfiguration` nową konfigurację pobierania, należy uruchomić polecenie cmdlet.

Aby pobrać wcześniej wygenerowane pliki konfiguracji klienta, należy użyć następującego polecenia:

```azurepowershell-interactive
Get-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW"
```

### <a name="2-configure-vpn-clients"></a><a name="setupusername"></a>2. Konfigurowanie klientów sieci VPN

Można skonfigurować następujących klientów sieci VPN:

* [Windows](#adwincli)
* [Mac (OS X)](#admaccli)
* [Linux za pomocą strongSwan](#adlinuxcli)
 
#### <a name="windows-vpn-client-setup"></a><a name="adwincli"></a>Konfiguracja klienta sieci VPN systemu Windows

Tego samego pakietu konfiguracji klienta sieci VPN można używać na każdym komputerze klienckim systemu Windows, o ile wersja jest zgodna z architekturą klienta. Aby uzyskać listę obsługiwanych systemów operacyjnych klienckich, zobacz często zadawane [pytania](vpn-gateway-vpn-faq.md#P2S).

Aby skonfigurować macierzystego klienta sieci VPN systemu Windows, należy wykonać następujące czynności:

1. Wybierz pliki konfiguracji klienta sieci VPN, które odpowiadają architekturze komputera z systemem Windows. W przypadku architektury procesora 64-bitowego wybierz pakiet **instalatora VpnClientSetupAmd64.** W przypadku architektury procesora 32-bitowego wybierz pakiet **instalatora VpnClientSetupX86.** 
2. Aby zainstalować pakiet, kliknij go dwukrotnie. Jeśli widzisz okno podręczne SmartScreen, wybierz **pozycję Więcej informacji** > **Uruchom mimo to**.
3. Na komputerze klienckim przejdź do **pozycji Ustawienia sieciowe** i wybierz pozycję **VPN**. Połączenie z siecią VPN zawiera nazwę sieci wirtualnej, z którą jest nawiązywane połączenie. 

#### <a name="mac-os-x-vpn-client-setup"></a><a name="admaccli"></a>Konfiguracja klienta sieci VPN w systemie Mac (OS X)

1. Wybierz plik **VpnClientSetup mobileconfig** i wyślij go do każdego z użytkowników. Możesz użyć poczty e-mail lub innej metody.

2. Znajdź plik **mobileconfig** na komputerze Mac.

   ![Lokalizacja pliku mobileconfig](./media/point-to-site-vpn-client-configuration-radius/admobileconfigfile.png)

3. Krok opcjonalny — jeśli chcesz określić niestandardowy system DNS, dodaj następujące wiersze do pliku **mobileconfig:**

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
5. Wybierz **pozycję Kontynuuj,** aby zaufać nadawcy profilu i kontynuować instalację.

   ![Komunikat z potwierdzeniem](./media/point-to-site-vpn-client-configuration-radius/adcontinue.png)
6. Podczas instalacji profilu można określić nazwę użytkownika i hasło do uwierzytelniania sieci VPN. Wprowadzanie tych informacji nie jest obowiązkowe. Jeśli to zrobisz, informacje są zapisywane i automatycznie używane podczas inicjowania połączenia.Wybierz **pozycję Zainstaluj,** aby kontynuować.

   ![Pola nazwy użytkownika i hasła dla VPN](./media/point-to-site-vpn-client-configuration-radius/adsettings.png)
7. Wprowadź nazwę użytkownika i hasło dla uprawnień wymaganych do zainstalowania profilu na komputerze. Kliknij przycisk **OK**.

   ![Pola nazwy użytkownika i hasła do instalacji profilu](./media/point-to-site-vpn-client-configuration-radius/adusername.png)
8. Po zainstalowaniu profilu jest on widoczny w oknie dialogowym **Profile.** To okno dialogowe można również otworzyć później w **oknie preferencji systemowych**.

   ![Okno dialogowe "Profile"](./media/point-to-site-vpn-client-configuration-radius/adsystempref.png)
9. Aby uzyskać dostęp do połączenia sieci VPN, otwórz okno dialogowe **Sieć** w **oknie preferencji systemowych**.

   ![Ikony w preferencjach systemowych](./media/point-to-site-vpn-client-configuration-radius/adnetwork.png)
10. Połączenie VPN jest wyświetlane jako **IkeV2-VPN**. Nazwę można zmienić, aktualizując plik **mobileconfig.**

    ![Szczegóły dotyczące połączenia sieci VPN](./media/point-to-site-vpn-client-configuration-radius/adconnection.png)
11. Wybierz pozycję **Ustawienia uwierzytelniania**. Wybierz **pozycję Nazwa użytkownika** na liście i wprowadź poświadczenia. Jeśli poświadczenia zostały wprowadzone wcześniej, **nazwa użytkownika** jest automatycznie wybierana na liście, a nazwa użytkownika i hasło są wstępnie wypełniane. Wybierz **przycisk OK,** aby zapisać ustawienia.

    ![Ustawienia uwierzytelniania](./media/point-to-site-vpn-client-configuration-radius/adauthentication.png)
12. W oknie dialogowym **Sieć** wybierz pozycję **Zastosuj,** aby zapisać zmiany. Aby zainicjować połączenie, wybierz pozycję **Połącz**.

#### <a name="linux-vpn-client-setup-through-strongswan"></a><a name="adlinuxcli"></a>Konfiguracja klienta VPN w systemie Linux za pośrednictwem silnegoSwan

Poniższe instrukcje zostały utworzone przez strongSwan 5.5.1 na Ubuntu 17.0.4. Rzeczywiste ekrany mogą być różne, w zależności od wersji Linuksa i strongSwan.

1. Otwórz **terminal,** aby zainstalować **strongSwan** i jego Menedżera sieci, uruchamiając polecenie w przykładzie. Jeśli zostanie wyświetlony błąd związany `libcharon-extra-plugins`z , `strongswan-plugin-eap-mschapv2`zastąp go na .

   ```Terminal
   sudo apt-get install strongswan libcharon-extra-plugins moreutils iptables-persistent network-manager-strongswan
   ```
2. Wybierz ikonę **Menedżera sieci** (strzałka w górę/strzałka w dół) i wybierz pozycję **Edytuj połączenia**.

   ![Wybór "Edytuj połączenia" w Menedżerze sieci](./media/point-to-site-vpn-client-configuration-radius/EditConnection.png)
3. Wybierz przycisk **Dodaj,** aby utworzyć nowe połączenie.

   ![Przycisk "Dodaj" dla połączenia](./media/point-to-site-vpn-client-configuration-radius/AddConnection.png)
4. Z menu rozwijanego **wybierz opcję IPsec/IKEv2 (strongswan),** a następnie wybierz pozycję **Utwórz**. W tym kroku można zmienić nazwę połączenia.

   ![Wybór typu połączenia](./media/point-to-site-vpn-client-configuration-radius/AddIKEv2.png)
5. Otwórz plik **VpnSettings.xml** z folderu **Generic** pobranych plików konfiguracyjnych klienta. Znajdź tag `VpnServer` o nazwie i skopiuj nazwę, zaczynając od `azuregateway` i kończąc na `.cloudapp.net`.

   ![Zawartość pliku VpnSettings.xml](./media/point-to-site-vpn-client-configuration-radius/VpnSettings.png)
6. Wklej tę nazwę do pola **Adres** nowego połączenia sieci VPN w sekcji **Brama.** Następnie wybierz ikonę folderu na końcu pola **Certyfikat,** przejdź do folderu **Ogólne** i wybierz plik **VpnServerRoot.**
7. W sekcji **Klient** połączenia wybierz pozycję **EAP** do **uwierzytelniania**i wprowadź nazwę użytkownika i hasło. Aby zapisać te informacje, może być trzeba wybrać ikonę kłódki po prawej stronie. Następnie wybierz pozycję **Zapisz**.

   ![Edytowanie ustawień połączenia](./media/point-to-site-vpn-client-configuration-radius/editconnectionsettings.png)
8. Wybierz ikonę **Menedżera sieci** (strzałka w górę/strzałka w dół) i umieść wskaźnik myszy na **połączeniach sieci VPN**. Zostanie wyświetlenie utworzonego połączenia sieci VPN. Aby zainicjować połączenie, zaznacz je.

   ![Połączenie "VPN Radius" w Menedżerze sieci](./media/point-to-site-vpn-client-configuration-radius/ConnectRADIUS.png)

## <a name="certificate-authentication"></a><a name="certeap"></a>Uwierzytelnianie certyfikatu
 
Można utworzyć pliki konfiguracji klienta sieci VPN do uwierzytelniania certyfikatów RADIUS, które używają protokołu EAP-TLS. Zazwyczaj certyfikat wystawiony przez przedsiębiorstwo jest używany do uwierzytelniania użytkownika dla sieci VPN. Upewnij się, że wszyscy użytkownicy łączący mają zainstalowany certyfikat na swoich urządzeniach i że serwer RADIUS może zweryfikować certyfikat.

>[!NOTE]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

W poleceniach `-AuthenticationMethod` jest `EapTls`. Podczas uwierzytelniania certyfikatu klient sprawdza poprawność serwera RADIUS, sprawdzając jego certyfikat. `-RadiusRootCert`to plik cer zawierający certyfikat główny używany do sprawdzania poprawności serwera RADIUS.

Każde urządzenie klienta sieci VPN wymaga zainstalowanego certyfikatu klienta. Czasami urządzenie z systemem Windows ma wiele certyfikatów klienta. Podczas uwierzytelniania może to spowodować wyświetlenie okna dialogowego z listą wszystkich certyfikatów. Następnie użytkownik musi wybrać certyfikat do użycia. Poprawny certyfikat można odfiltrować, określając certyfikat główny, do który powinien fiknąć certyfikat klienta. 

`-ClientRootCert`to plik cer zawierający certyfikat główny. Jest to parametr opcjonalny. Jeśli urządzenie, z którego chcesz się połączyć, ma tylko jeden certyfikat klienta, nie trzeba określać tego parametru.

### <a name="1-generate-vpn-client-configuration-files"></a><a name="certfiles"></a>1. Generowanie plików konfiguracyjnych klienta SIECI VPN

Generowanie plików konfiguracyjnych klienta sieci VPN do użycia z uwierzytelnianiem certyfikatów. Pliki konfiguracyjne klienta sieci VPN można wygenerować za pomocą następującego polecenia:
 
```azurepowershell-interactive
New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls" -RadiusRootCert <full path name of .cer file containing the RADIUS root> -ClientRootCert <full path name of .cer file containing the client root> | fl
```

Uruchomienie polecenia zwraca łącze. Skopiuj i wklej link do przeglądarki internetowej, aby pobrać VpnClientConfiguration.zip. Rozpaj plik, aby wyświetlić następujące foldery:

* **WindowsAmd64** i **WindowsX86**: Te foldery zawierają odpowiednio 64-bitowe i 32-bitowe pakiety instalacyjne systemu Windows. 
* **GenericDevice:** Ten folder zawiera ogólne informacje, które są używane do tworzenia własnej konfiguracji klienta sieci VPN.

Jeśli już utworzono pliki konfiguracyjne klienta, `Get-AzVpnClientConfiguration` można je pobrać za pomocą polecenia cmdlet. Jeśli jednak wybrano jakiekolwiek zmiany w konfiguracji sieci VPN P2S, takie jak typ protokołu sieci VPN lub typ uwierzytelniania, konfiguracja nie zostanie automatycznie zaktualizowana. Aby utworzyć `New-AzVpnClientConfiguration` nową konfigurację pobierania, należy uruchomić polecenie cmdlet.

Aby pobrać wcześniej wygenerowane pliki konfiguracji klienta, należy użyć następującego polecenia:

```azurepowershell-interactive
Get-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" | fl
```
 
### <a name="2-configure-vpn-clients"></a><a name="setupusername"></a>2. Konfigurowanie klientów sieci VPN

Można skonfigurować następujących klientów sieci VPN:

* [Windows](#certwincli)
* [Mac (OS X)](#certmaccli)
* Linux (obsługiwane, nie ma jeszcze kroków artykułu)

#### <a name="windows-vpn-client-setup"></a><a name="certwincli"></a>Konfiguracja klienta sieci VPN systemu Windows

1. Wybierz pakiet konfiguracyjny i zainstaluj go na urządzeniu klienckim. W przypadku architektury procesora 64-bitowego wybierz pakiet **instalatora VpnClientSetupAmd64.** W przypadku architektury procesora 32-bitowego wybierz pakiet **instalatora VpnClientSetupX86.** Jeśli widzisz okno podręczne SmartScreen, wybierz **pozycję Więcej informacji** > **Uruchom mimo to**. Możesz także zapisać pakiet w celu zainstalowania go na innych komputerach klienckich.
2. Każdy klient wymaga certyfikatu klienta do uwierzytelniania. Zainstaluj certyfikat klienta. Aby uzyskać informacje o certyfikatach klientów, zobacz [Certyfikaty klientów dla punktu-lokacja](vpn-gateway-certificates-point-to-site.md). Aby zainstalować wygenerowany certyfikat, zobacz [Instalowanie certyfikatu na klientach systemu Windows](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. Na komputerze klienckim przejdź do **pozycji Ustawienia sieciowe** i wybierz pozycję **VPN**. Połączenie z siecią VPN zawiera nazwę sieci wirtualnej, z którą jest nawiązywane połączenie.

#### <a name="mac-os-x-vpn-client-setup"></a><a name="certmaccli"></a>Konfiguracja klienta sieci VPN w systemie Mac (OS X)

Należy utworzyć oddzielny profil dla każdego urządzenia Mac, który łączy się z siecią wirtualną platformy Azure. Dzieje się tak, ponieważ te urządzenia wymagają, aby certyfikat użytkownika do uwierzytelniania był określony w profilu. Folder **Ogólny** zawiera wszystkie informacje wymagane do utworzenia profilu:

* **VpnSettings.xml** zawiera ważne ustawienia, takie jak adres serwera i typ tunelu.
* **Plik VpnServerRoot.cer** zawiera certyfikat główny wymagany do sprawdzania poprawności bramy sieci VPN podczas konfigurowania połączenia P2S.
* **Plik RadiusServerRoot.cer** zawiera certyfikat główny wymagany do sprawdzania poprawności serwera RADIUS podczas uwierzytelniania.

Aby skonfigurować natywnego klienta sieci VPN na komputerze Mac do uwierzytelniania certyfikatów, należy wykonać następujące czynności:

1. Zaimportuj certyfikaty główne **VpnServerRoot** i **RadiusServerRoot** na komputerze Mac. Skopiuj każdy plik na komputer Mac, kliknij go dwukrotnie, a następnie wybierz pozycję **Dodaj**.

   ![Dodawanie certyfikatu VpnServerRoot](./media/point-to-site-vpn-client-configuration-radius/addcert.png)

   ![Dodawanie certyfikatu RadiusServerRoot](./media/point-to-site-vpn-client-configuration-radius/radiusrootcert.png)
2. Każdy klient wymaga certyfikatu klienta do uwierzytelniania. Zainstaluj certyfikat klienta na urządzeniu klienckim.
3. Otwórz okno dialogowe **Sieć** w obszarze **Preferencje sieciowe**. Wybierz, **+** aby utworzyć nowy profil połączenia klienta sieci VPN dla połączenia P2S z siecią wirtualną platformy Azure.

   Wartość **interfejsu** to **VPN**, a wartość type **sieci VPN** to **IKEv2**. Określ nazwę profilu w polu **Nazwa usługi,** a następnie wybierz pozycję **Utwórz,** aby utworzyć profil połączenia klienta sieci VPN.

   ![Informacje o interfejsie i nazwie usługi](./media/point-to-site-vpn-client-configuration-radius/network.png)
4. W **folderze Ogólny** z pliku **VpnSettings.xml** skopiuj wartość tagu **VpnServer.** Wklej tę wartość w polach **Adres serwera** i **Identyfikator zdalny** profilu. Pozostaw pole **Identyfikator lokalny** puste.

   ![Informacje o serwerze](./media/point-to-site-vpn-client-configuration-radius/servertag.png)
5. Wybierz **pozycję Ustawienia uwierzytelniania**i wybierz **pozycję Certyfikat**. 

   ![Ustawienia uwierzytelniania](./media/point-to-site-vpn-client-configuration-radius/certoption.png)
6. Kliknij **przycisk Wybierz,** aby wybrać certyfikat, którego chcesz użyć do uwierzytelniania.

   ![Wybieranie certyfikatu do uwierzytelniania](./media/point-to-site-vpn-client-configuration-radius/certificate.png)
7. **Wybierz tożsamość** wyświetla listę certyfikatów do wyboru. Wybierz odpowiedni certyfikat, a następnie wybierz pozycję **Kontynuuj**.

   ![Lista "Wybierz tożsamość"](./media/point-to-site-vpn-client-configuration-radius/identity.png)
8. W polu **Identyfikator lokalny** określ nazwę certyfikatu (z kroku 6). W tym przykładzie jest **ikev2Client.com**. Następnie wybierz przycisk **Zastosuj,** aby zapisać zmiany.

   ![Pole "Identyfikator lokalny"](./media/point-to-site-vpn-client-configuration-radius/applyconnect.png)
9. W oknie dialogowym **Sieć** wybierz pozycję **Zastosuj,** aby zapisać wszystkie zmiany. Następnie wybierz pozycję **Połącz,** aby uruchomić połączenie P2S z siecią wirtualną platformy Azure.

## <a name="working-with-other-authentication-types-or-protocols"></a><a name="otherauth"></a>Praca z innymi typami uwierzytelniania lub protokołami

Aby użyć innego typu uwierzytelniania (na przykład OTP) lub użyć innego protokołu uwierzytelniania (takiego jak PEAP-MSCHAPv2 zamiast EAP-MSCHAPv2), należy utworzyć własny profil konfiguracji klienta sieci VPN. Aby utworzyć profil, potrzebne są informacje, takie jak adres IP bramy sieci wirtualnej, typ tunelu i trasy podzielonego tunelu. Informacje te można uzyskać, wykonując następujące czynności:

1. Użyj `Get-AzVpnClientConfiguration` polecenia cmdlet, aby wygenerować konfigurację klienta sieci VPN dla protokołu EapMSChapv2.

2. Rozpakuj plik VpnClientConfiguration.zip i poszukaj folderu **GenericDevice.** Zignoruj foldery zawierające instalatory systemu Windows dla architektur 64-bitowych i 32-bitowych.
 
3. Folder **GenericDevice** zawiera plik XML o nazwie **VpnSettings**. Ten plik zawiera wszystkie wymagane informacje:

   * **VpnServer:** FQDN bramy sieci VPN platformy Azure. Jest to adres, z który klient łączy.
   * **VpnType:** Typ tunelu używany do łączenia.
   * **Trasy:** trasy, które należy skonfigurować w profilu, tak aby tylko ruch związany z siecią wirtualną platformy Azure jest wysyłany przez tunel P2S.
   
   Folder **GenericDevice** zawiera również plik cer o nazwie **VpnServerRoot**. Ten plik zawiera certyfikat główny wymagany do sprawdzania poprawności bramy sieci VPN platformy Azure podczas konfigurowania połączenia P2S. Zainstaluj certyfikat na wszystkich urządzeniach, które będą łączyć się z siecią wirtualną platformy Azure.

## <a name="next-steps"></a>Następne kroki

Wróć do artykułu, aby [ukończyć konfigurację P2S](point-to-site-how-to-radius-ps.md).

Aby uzyskać informacje dotyczące rozwiązywania problemów z usługami P2S, zobacz [Rozwiązywanie problemów z połączeniami typu punkt-lokacja](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)platformy Azure .
