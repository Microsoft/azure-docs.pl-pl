---
title: 'VPN Gateway: klient VPN dla połączeń protokołu P2S OpenVPN: uwierzytelnianie usługi Azure AD'
description: Dowiedz się, jak skonfigurować klienta sieci VPN do łączenia się z siecią wirtualną przy użyciu sieci VPN typu punkt-lokacja i uwierzytelniania Azure Active Directory.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/15/2020
ms.author: alzam
ms.openlocfilehash: 09d39acc867124acfd5c3c38d5c26053218972e7
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92109106"
---
# <a name="azure-active-directory-authentication-configure-a-vpn-client-for-p2s-openvpn-protocol-connections"></a>Uwierzytelnianie Azure Active Directory: Konfigurowanie klienta VPN dla połączeń protokołu P2S OpenVPN

Ten artykuł pomaga skonfigurować klienta sieci VPN do łączenia się z siecią wirtualną przy użyciu sieci VPN typu punkt-lokacja i uwierzytelniania Azure Active Directory. Aby można było nawiązać połączenie i uwierzytelnić się za pomocą usługi Azure AD, należy najpierw skonfigurować dzierżawę usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Konfigurowanie dzierżawy usługi Azure AD](openvpn-azure-ad-tenant.md).

[!INCLUDE [Windows 10 and OpenVPN note](../../includes/vpn-gateway-openvpn-auth-include.md)]

## <a name="working-with-client-profiles"></a><a name="profile"></a>Praca z profilami klientów

Aby nawiązać połączenie, należy pobrać klienta sieci VPN platformy Azure i skonfigurować profil klienta sieci VPN na każdym komputerze, który chce nawiązać połączenie z siecią wirtualną. Można utworzyć profil klienta na komputerze, wyeksportować go, a następnie zaimportować do dodatkowych komputerów.

### <a name="to-download-the-azure-vpn-client"></a>Aby pobrać klienta sieci VPN platformy Azure

Użyj tego [linku](https://go.microsoft.com/fwlink/?linkid=2117554) , aby pobrać klienta sieci VPN platformy Azure. Upewnij się, że klient sieci VPN platformy Azure ma uprawnienie do uruchamiania w tle. Aby sprawdzić/włączyć uprawnienie, wykonaj poniższe czynności:

1. Przejdź do menu Start, a następnie wybierz pozycję Ustawienia > prywatność > aplikacje w tle.
2. W obszarze aplikacje w tle upewnij się, że **aplikacje są uruchamiane w tle** .
3. W obszarze Wybierz aplikacje, które mogą być uruchamiane w tle, Włącz ustawienia dla usługi Azure VPN Client na wartość **włączone**.

  ![zezwolenie](./media/openvpn-azure-ad-client/backgroundpermission.png)

### <a name="to-create-a-certificate-based-client-profile"></a><a name="cert"></a>Aby utworzyć profil klienta oparty na certyfikacie

Podczas pracy z profilem opartym na certyfikatach upewnij się, że odpowiednie certyfikaty są zainstalowane na komputerze klienckim. Aby uzyskać więcej informacji o certyfikatach, zobacz [Instalowanie certyfikatów klienta](point-to-site-how-to-vpn-client-install-azure-cert.md).

  ![cert](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="to-create-a-radius-client-profile"></a><a name="radius"></a>Aby utworzyć profil klienta usługi RADIUS

  ![promienie](./media/openvpn-azure-ad-client/create/create-radius1.jpg)
  
> [!NOTE]
> Wpis tajny serwera można wyeksportować w profilu klienta sieci VPN P2S.  Instrukcje dotyczące sposobu eksportowania profilu klienta można znaleźć [tutaj](about-vpn-profile-download.md).
>

### <a name="to-export-and-distribute-a-client-profile"></a><a name="export"></a>Aby wyeksportować i rozpowszechnić profil klienta

Gdy dysponujesz profilem roboczym i chcesz go rozesłać do innych użytkowników, możesz go wyeksportować, wykonując następujące czynności:

1. Zaznacz profil klienta sieci VPN, który chcesz wyeksportować, wybierz pozycję **...**, a następnie wybierz pozycję **Eksportuj**.

    ![Zrzut ekranu przedstawiający stronę "klient sieci VPN platformy Azure" z zaznaczonym wielokropkiem i wyróżnioną opcją "Eksportuj".](./media/openvpn-azure-ad-client/export/export1.jpg)

2. Wybierz lokalizację, w której chcesz zapisać ten profil, pozostaw nazwę pliku, a następnie wybierz pozycję **Zapisz** , aby zapisać plik XML.

    ![eksportowanie](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="to-import-a-client-profile"></a><a name="import"></a>Aby zaimportować profil klienta

1. Na stronie wybierz pozycję **Importuj**.

    ![Zrzut ekranu pokazujący wybrany przycisk "Dodaj" oraz akcję "Importuj" wyróżnioną w lewej dolnej części okna.](./media/openvpn-azure-ad-client/import/import1.jpg)

2. Przejdź do pliku XML profilu i wybierz go. Po wybraniu pliku wybierz pozycję **Otwórz**.

    ![Zrzut ekranu pokazujący wybrany profil x m l.](./media/openvpn-azure-ad-client/import/import2.jpg)

3. Określ nazwę profilu i wybierz pozycję **Zapisz**.

    ![Zrzut ekranu, na którym jest wyróżnione okno "nazwa połączenia" i wybrany przycisk "Zapisz".](./media/openvpn-azure-ad-client/import/import3.jpg)

4. Wybierz pozycję **Połącz** , aby nawiązać połączenie z siecią VPN.

    ![Zrzut ekranu pokazujący wybrany przycisk sieci VPN i "Połącz".](./media/openvpn-azure-ad-client/import/import4.jpg)

5. Po nawiązaniu połączenia ikona zmieni kolor na zielony i powiedzie **się.**

    ![import](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Aby usunąć profil klienta

1. Wybierz wielokropek obok profilu klienta, który chcesz usunąć. Następnie wybierz pozycję **Usuń**.

    ![Zrzut ekranu pokazujący, że wybrano opcję wielokropka i "Usuń".](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. Wybierz pozycję **Usuń** , aby usunąć.

    ![delete](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="create-a-connection"></a><a name="connection"></a>Tworzenie połączenia

1. Na stronie wybierz pozycję **+** , a następnie pozycję **+ Dodaj**.

    ![Zrzut ekranu pokazujący wybrany przycisk "Dodaj".](./media/openvpn-azure-ad-client/create/create1.jpg)

2. Wprowadź informacje o połączeniu. Jeśli nie masz pewności co do wartości, skontaktuj się z administratorem. Po wypełnieniu wartości wybierz pozycję **Zapisz**.

    ![Zrzut ekranu pokazujący wyróżnione właściwości połączenia sieci VPN i wybrany przycisk "Zapisz".](./media/openvpn-azure-ad-client/create/create2.jpg)

3. Wybierz pozycję **Połącz** , aby nawiązać połączenie z siecią VPN.

    ![Zrzut ekranu pokazujący wybrany przycisk "Połącz".](./media/openvpn-azure-ad-client/create/create3.jpg)

4. Wybierz odpowiednie poświadczenia, a następnie wybierz pozycję **Kontynuuj**.

    ![Zrzut ekranu przedstawiający przykładowe wyróżnione poświadczenia i wybrany przycisk "Kontynuuj".](./media/openvpn-azure-ad-client/create/create4.jpg)

5. Po pomyślnym nawiązaniu połączenia ikona zmieni kolor na zielony i powiedzie **się.**

    ![połączenie](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="to-connect-automatically"></a><a name="autoconnect"></a>Aby nawiązać połączenie automatycznie

Te kroki ułatwiają skonfigurowanie połączenia do automatycznego łączenia z usługą Always-On.

1. Na stronie głównej klienta sieci VPN wybierz pozycję **Ustawienia sieci VPN**.

    ![Zrzut ekranu strony głównej sieci VPN z wybraną opcją "Ustawienia sieci VPN".](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. W oknie dialogowym przełączanie aplikacji wybierz pozycję **tak** .

    ![Zrzut ekranu przedstawiający "czy chcesz przełączyć aplikacje?" okno dialogowe z wybranym przyciskiem "tak".](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. Upewnij się, że połączenie, które chcesz ustawić, nie jest już połączone, a następnie zaznacz profil i zaznacz pole wyboru **Połącz automatycznie** .

    ![Zrzut ekranu przedstawiający okno "Ustawienia" z zaznaczonym polem "Połącz automatycznie".](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. Wybierz pozycję **Połącz** , aby zainicjować połączenie sieci VPN.

    ![auto](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose-connection-issues"></a><a name="diagnose"></a>Diagnozuj problemy z połączeniem

1. Aby zdiagnozować problemy z połączeniem, można użyć narzędzia do **diagnozowania** . Wybierz **...** obok połączenia sieci VPN, które chcesz zdiagnozować, aby wyświetlić menu. Następnie wybierz pozycję **Diagnozuj**.

    ![Zrzut ekranu przedstawiający wielokropek i "Diagnozuj wybrane".](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. Na stronie **Właściwości połączenia** wybierz pozycję **Uruchom diagnostykę**.

    ![Zrzut ekranu przedstawiający stronę "właściwości połączenia" z wybraną opcją "Uruchom diagnostykę".](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. Zaloguj się przy użyciu swoich poświadczeń.

    ![Zrzut ekranu przedstawiający okno dialogowe "Logowanie do użytkownika" z wybraną opcją "konto służbowe".](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. Wyświetl wyniki diagnostyki.

    ![Sprawdź](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="faq"></a>Często zadawane pytania

### <a name="is-the-azure-vpn-client-supported-with-windows-fips-mode"></a>Czy klient sieci VPN platformy Azure jest obsługiwany z trybem FIPS systemu Windows?

Tak, przy użyciu poprawki [KB4577063](https://support.microsoft.com/help/4577063/windows-10-update-kb4577063) .

### <a name="how-do-i-add-dns-suffixes-to-the-vpn-client"></a>Jak mogę dodać sufiksy DNS do klienta sieci VPN?

Można zmodyfikować pobrany plik XML profilu i dodać ** \<dnssuffixes> \<dnssufix> \</dnssufix> \</dnssuffixes> ** Tagi

```
<azvpnprofile>
<clientconfig>

    <dnssuffixes>
          <dnssuffix>.mycorp.com</dnssuffix>
          <dnssuffix>.xyz.com</dnssuffix>
          <dnssuffix>.etc.net</dnssuffix>
    </dnssuffixes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="how-do-i-add-custom-dns-servers-to-the-vpn-client"></a>Jak mogę dodać do klienta sieci VPN niestandardowe serwery DNS?

Można zmodyfikować pobrany plik XML profilu i dodać ** \<dnsservers> \<dnsserver> \</dnsserver> \</dnsservers> ** Tagi

```
<azvpnprofile>
<clientconfig>

    <dnsservers>
        <dnsserver>x.x.x.x</dnsserver>
        <dnsserver>y.y.y.y</dnsserver>
    </dnsservers>
    
</clientconfig>
</azvpnprofile>
```

> [!NOTE]
> Klient usługi OpenVPN Azure AD korzysta z wpisów tabeli zasad rozpoznawania nazw DNS (NRPT), co oznacza, że serwery DNS nie będą wyświetlane w danych wyjściowych `ipconfig /all` . Aby potwierdzić używane ustawienia usługi DNS, zapoznaj się z tematem [Get-DnsClientNrptPolicy](https://docs.microsoft.com/powershell/module/dnsclient/get-dnsclientnrptpolicy?view=win10-ps&preserve-view=true) w programie PowerShell.
>

### <a name="how-do-i-add-custom-routes-to-the-vpn-client"></a>Jak mogę dodać trasy niestandardowe do klienta sieci VPN?

Można zmodyfikować pobrany plik XML profilu i dodać ** \<includeroutes> \<route> \<destination> \<mask> \</destination> \</mask> \</route> \</includeroutes> ** Tagi

```
<azvpnprofile>
<clientconfig>

    <includeroutes>
        <route>
            <destination>x.x.x.x</destination><mask>24</mask>
        </route>
    </includeroutes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="how-do-i-block-exclude-routes-from-the-vpn-client"></a>Jak mogę blokowe (wykluczanie) tras z klienta sieci VPN?

Można zmodyfikować pobrany plik XML profilu i dodać ** \<excluderoutes> \<route> \<destination> \<mask> \</destination> \</mask> \</route> \</excluderoutes> ** Tagi

```
<azvpnprofile>
<clientconfig>

    <excluderoutes>
        <route>
            <destination>x.x.x.x</destination><mask>24</mask>
        </route>
    </excluderoutes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="can-i-import-the-profile-from-a-command-line-prompt"></a>Czy mogę zaimportować profil z wiersza polecenia?

Możesz zaimportować profil z wiersza polecenia, umieszczając pobrany plik **azurevpnconfig.xml** w folderze **%USERPROFILE%\appdata\local\packages\ Microsoft.AzureVpn_8wekyb3d8bbwe \localstate** i uruchamiając następujące polecenie:

```
azurevpn -i azurevpnconfig.xml 
```
Aby wymusić również użycie przełącznika **-f**


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Tworzenie dzierżawy Azure Active Directory dla P2S Otwórz połączenia sieci VPN korzystające z uwierzytelniania usługi Azure AD](openvpn-azure-ad-tenant.md).
