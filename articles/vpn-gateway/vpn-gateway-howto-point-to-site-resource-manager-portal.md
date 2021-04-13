---
title: 'Nawiązywanie połączenia z siecią wirtualną przy użyciu sieci VPN typu P2S & uwierzytelnianie certyfikatu: portal'
titleSuffix: Azure VPN Gateway
description: Bezpieczne łączenie klientów z systemami Windows, macOS i Linux z siecią wirtualną platformy Azure przy użyciu połączeń typu P2S i certyfikatów z podpisem własnym lub wystawionych przez urząd certyfikacji. W tym artykule jest używana witryna Azure Portal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/10/2021
ms.author: cherylmc
ms.openlocfilehash: b515ed37b5d2c71843cb138240bd2fa77fe2fd8d
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365556"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-native-azure-certificate-authentication-azure-portal"></a>Konfigurowanie połączenia sieci VPN typu punkt-lokacja z siecią wirtualną przy użyciu natywnego uwierzytelniania certyfikatu platformy Azure: Azure Portal

Ten artykuł ułatwia bezpieczne łączenie poszczególnych klientów z systemem Windows, Linux lub macOS z siecią wirtualną platformy Azure. Połączenia sieci VPN typu punkt-lokacja przydają się w przypadku, gdy celem użytkownika jest połączenie się z siecią wirtualną z lokalizacji zdalnej, podczas pracy zdalnej z domu lub konferencji. Możesz również użyć połączenia typu punkt-lokacja zamiast połączenia sieci VPN typu lokacja-lokacja w przypadku niewielkiej liczby klientów, którzy muszą się łączyć z siecią wirtualną. Połączenia typu punkt-lokacja nie wymagają urządzenia sieci VPN ani publicznego adresu IP. Połączenie typu punkt-lokacja tworzy połączenie sieci VPN nawiązywane za pośrednictwem protokołu SSTP (Secure Socket Tunneling Protocol) lub IKEv2. Aby uzyskać więcej informacji na temat połączeń sieci VPN typu punkt-lokacja, zobacz [About Point-to-Site VPN (Informacje o sieci VPN typu punkt-lokacja)](point-to-site-about.md).

:::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-diagram.png" alt-text="Łączenie z komputera do sieci wirtualnej platformy Azure — diagram połączenia punkt-lokacja":::

Aby uzyskać więcej informacji na temat sieci VPN typu punkt-lokacja, zobacz About point-to-site VPN (Informacje o sieci [VPN typu punkt-lokacja).](point-to-site-about.md) Aby utworzyć tę konfigurację przy użyciu Azure PowerShell, zobacz [Konfigurowanie sieci VPN typu punkt-lokacja](vpn-gateway-howto-point-to-site-rm-ps.md)przy użyciu Azure PowerShell .

[!INCLUDE [P2S basic architecture](../../includes/vpn-gateway-p2s-architecture.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Sprawdź, czy masz subskrypcję platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial).

### <a name="example-values"></a><a name="example"></a>Przykładowe wartości

Następujących wartości możesz użyć do tworzenia środowiska testowego lub odwoływać się do tych wartości, aby lepiej zrozumieć przykłady w niniejszym artykule:

* **Nazwa sieci wirtualnej:** VNet1
* **Przestrzeń adresowa:** 10.1.0.0/16<br>W tym przykładzie zostanie wykorzystana tylko jedna przestrzeń adresowa. Istnieje możliwość użycia więcej niż jednej przestrzeni adresowej dla sieci wirtualnej.
* **Nazwa podsieci:** Frontend
* **Zakres adresów podsieci:** 10.1.0.0/24
* **Subskrypcja:** jeśli masz więcej niż jedną subskrypcję, sprawdź, czy korzystasz z właściwej.
* **Grupa zasobów:** TestRG1
* **Lokalizacja:** Wschodnie usa
* **GatewaySubnet:** 10.1.255.0/27<br>
* **Nazwa bramy sieci wirtualnej:** VNet1GW
* **Typ bramy:** VPN
* **Typ sieci VPN:** oparta na trasach
* **Publiczny adres IP:** VNet1GWpip
* **Typ połączenia:** Punkt-lokacja
* **Pula adresów klienta:** 172.16.201.0/24<br>Klienci sieci VPN połączeni z siecią wirtualną, którzy korzystają z tego połączenia punkt-lokacja, otrzymują adresy IP z puli adresów klientów.

## <a name="virtual-network"></a><a name="createvnet"></a>Sieć wirtualna

W tej sekcji utworzysz sieć wirtualną.

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="virtual-network-gateway"></a><a name="creategw"></a>Brama sieci wirtualnej

W tym kroku zostaje utworzona brama dla sieci wirtualnej użytkownika. Tworzenie bramy często może trwać 45 minut lub dłużej, w zależności od wybranej jednostki SKU bramy.

>[!NOTE]
>Podstawowa brama SKU nie obsługuje uwierzytelniania protokołu IKEv2 ani RADIUS. Jeśli planujesz, aby klienci mac połączyli się z siecią wirtualną, nie używaj podstawowej wersji SKU.
>

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="generate-certificates"></a><a name="generatecert"></a>Generowanie certyfikatów klienta

Certyfikaty są używane przez platformę Azure do uwierzytelniania klientów łączących się z siecią wirtualną za pośrednictwem połączenia sieci VPN typu punkt-lokacja. Po uzyskaniu certyfikatu głównego należy [przekazać](#uploadfile) informacje o kluczu publicznym do platformy Azure. Certyfikat główny jest następnie uznawany przez platformę Azure za zaufany dla połączeń typu punkt-lokacja z siecią wirtualną. Ponadto certyfikaty klienta są generowane na podstawie zaufanego certyfikatu głównego, a następnie instalowane na każdym komputerze klienckim. Certyfikat klienta jest używany do uwierzytelniania klienta, gdy inicjuje on połączenie z siecią wirtualną.

### <a name="generate-a-root-certificate"></a><a name="getcer"></a>Generowanie certyfikatu głównego

[!INCLUDE [root-certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generate-client-certificates"></a><a name="generateclientcert"></a>Generowanie certyfikatów klienta

[!INCLUDE [generate-client-cert](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="client-address-pool"></a><a name="addresspool"></a>Pula adresów klienta

Pula adresów klienta to określony przez Ciebie zakres prywatnych adresów IP. Klienci łączący się dynamicznie przez połączenie VPN typu punkt-lokacja otrzymują adres IP z tego zakresu. Używaj zakresu prywatnych adresów IP nienakładającego się na lokalizację lokalną, z której się łączysz, ani na sieć wirtualną, z którą chcesz się łączyć. Jeśli skonfigurujesz wiele protokołów, a protokół SSTP jest jednym z protokołów, skonfigurowana pula adresów zostanie podzielona między skonfigurowane protokoły w równym stopniu.

1. Po utworzeniu bramy sieci wirtualnej przejdź do sekcji **Ustawienia** na stronie bramy sieci wirtualnej. W **ustawieniach** wybierz **pozycję Konfiguracja punkt-lokacja.** Wybierz **pozycję Konfiguruj teraz,** aby otworzyć stronę konfiguracji.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png" alt-text="Strona konfiguracji punkt-lokacja" lightbox="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png":::
1. Na stronie **Konfiguracja punkt-lokacja** w  polu Pula adresów dodaj zakres prywatnych adresów IP, którego chcesz użyć. Klienci sieci VPN dynamicznie otrzymują adres IP z określonego zakresu. Minimalna maska podsieci jest 29-bitowa dla konfiguracji aktywne/pasywne i 28-bitowa dla konfiguracji aktywne/aktywne.
1. Przejdź do następnej sekcji, aby skonfigurować typy uwierzytelniania i tuneli.

## <a name="authentication-and-tunnel-types"></a><a name="type"></a>Typy uwierzytelniania i tuneli

W tej sekcji skonfigurujesz typ uwierzytelniania i typ tunelu. Jeśli na **stronie Konfiguracja punkt-lokacja** nie widzisz  opcji Typ tunelu lub Typ uwierzytelniania, brama używa podstawowej wersji SKU.  Podstawowa jednostka SKU nie obsługuje uwierzytelniania za pomocą protokołu IKEv2 ani usługi RADIUS. Jeśli chcesz użyć tych ustawień, musisz usunąć i ponownie utworzyć bramę przy użyciu innej usługi SKU bramy.

### <a name="tunnel-type"></a><a name="tunneltype"></a>Typ tunelu

Na **stronie Konfiguracja punkt-lokacja** wybierz typ tunelu. Opcje tunelowania to OpenVPN, SSTP i IKEv2.

* Klient strongSwan w systemach Android i Linux oraz natywny klient sieci VPN IKEv2 w systemach iOS i OSX będą używać do łączenia się tylko tuneli IKEv2.
* Klienci systemu Windows najpierw spróbują użyć protokołu IKEv2, a jeśli nie nawiąą one połączenia, wrócą do protokołu SSTP.
* Możesz użyć klienta OpenVPN, aby nawiązać połączenie z typem tunelu OpenVPN.

### <a name="authentication-type"></a><a name="authenticationtype"></a>Typ uwierzytelniania

W **przypadku typu uwierzytelniania** wybierz **pozycję Certyfikat platformy Azure.**


## <a name="root-certificate-data"></a><a name="uploadfile"></a>Dane certyfikatu głównego

W tej sekcji przekażemy dane publicznego certyfikatu głównego na platformę Azure. Po przekazaniu danych certyfikatu publicznego platforma Azure może używać go do uwierzytelniania klientów, którzy mają zainstalowany certyfikat klienta wygenerowany na podstawie zaufanego certyfikatu głównego.

1. Certyfikaty są dodawane na stronie **Konfiguracja punktu do lokacji** w sekcji **Certyfikat główny**.
1. Upewnij się, że certyfikat główny został wyeksportowany jako plik X.509 (cer) zaszyfrowany z użyciem algorytmu Base-64. Certyfikat należy wyeksportować w takim formacie, aby możliwe było jego otwarcie za pomocą edytora tekstów.
1. Otwórz certyfikat za pomocą edytora tekstów, takiego jak Notatnik. Podczas kopiowania danych dotyczących certyfikatu upewnij się, że kopiujesz tekst jako jeden ciągły wiersz bez znaków powrotu karetki i wysuwu wiersza. Może zajść potrzeba zmodyfikowania widoku w edytorze tekstu na potrzeby pokazywania symboli lub pokazywania wszystkich znaków, aby wyświetlić znaki powrotu karetki i wysuwu wiersza. Skopiuj tylko następującą sekcję jako jeden ciągły wiersz:

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/notepadroot.png" alt-text="Dane dotyczące certyfikatu" border="false":::
1. Wklej dane dotyczące certyfikatu w polu **Dane certyfikatu publicznego**. **Nadaj** certyfikatowi nazwę, a następnie wybierz **pozycję Zapisz.** Możesz dodać maksymalnie 20 zaufanych certyfikatów głównych.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/uploaded.png" alt-text="Wklejanie danych certyfikatu" border="false":::
1. Wybierz **pozycję** Zapisz w górnej części strony, aby zapisać wszystkie ustawienia konfiguracji.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/save.png" alt-text="Zapisywanie konfiguracji" border="false":::

## <a name="client-certificate"></a><a name="installclientcert"></a>Certyfikat klienta

Jeśli chcesz utworzyć połączenie punkt-lokacja z komputerem klienckim innym niż użyty do wygenerowania certyfikatów klienta, należy zainstalować certyfikat klienta. Podczas instalowania certyfikatu klienta potrzebne jest hasło, które zostało utworzone w trakcie eksportowania certyfikatu klienta.

Upewnij się, że certyfikat klienta został wyeksportowany jako plik pfx wraz z całym łańcuchem certyfikatów (jest to ustawienie domyślne). W przeciwnym razie informacje o certyfikacie głównym nie będą dostępne na komputerze klienckim i klient nie będzie mógł się poprawnie uwierzytelnić.

Aby zapoznać się z krokami instalacji, zobacz [Install a client certificate](point-to-site-how-to-vpn-client-install-azure-cert.md) (Instalowanie certyfikatu klienta).

## <a name="vpn-client-configuration-package"></a><a name="clientconfig"></a>Pakiet konfiguracji klienta sieci VPN

Klienci sieci VPN muszą być skonfigurowani przy użyciu ustawień konfiguracji klienta. Pakiet konfiguracji klienta sieci VPN zawiera pliki z ustawieniami konfigurowania klientów sieci VPN w celu nawiązania połączenia z siecią wirtualną za pośrednictwem połączenia P2S.

Aby uzyskać instrukcje dotyczące generowania i instalowania plików konfiguracji klienta sieci VPN, zobacz Create [and install VPN client configuration files for native Azure certificate authentication P2S configurations](point-to-site-vpn-client-configuration-azure-cert.md)(Tworzenie i instalowanie plików konfiguracji klienta sieci VPN dla konfiguracji P2S natywnego uwierzytelniania certyfikatów platformy Azure).

## <a name="connect-to-azure"></a><a name="connect"></a>Nawiązywanie połączenia z usługą Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>Aby połączyć się z klienta sieci VPN w systemie Windows

[!INCLUDE [Connect from a Windows client](../../includes/vpn-gateway-p2s-connect-windows-client.md)]

[!INCLUDE [verifies client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="to-connect-from-a-mac-vpn-client"></a>Aby połączyć się z klienta sieci VPN na komputerze Mac

W oknie dialogowym Sieć znajdź profil klienta, którego chcesz użyć, określ ustawienia w oknie [dialogowymVpnSettings.xml](point-to-site-vpn-client-configuration-azure-cert.md#installmac), a następnie wybierz pozycję **Połącz.**

Aby uzyskać szczegółowe instrukcje, zobacz Instalowanie [— komputery Mac (OS X).](./point-to-site-vpn-client-configuration-azure-cert.md#installmac) Jeśli masz problemy z nawiązaniem połączenia, sprawdź, czy brama sieci wirtualnej nie używa podstawowej wersji SKU. Podstawowa sku nie jest obsługiwana w przypadku klientów Mac.

:::image type="content" source="./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png" alt-text="Połączenie klienta sieci VPN dla komputerów Mac" border="false":::

## <a name="to-verify-your-connection"></a><a name="verify"></a>Aby zweryfikować połączenie

Te instrukcje dotyczą klientów w systemie Windows.

1. Aby sprawdzić, czy połączenie sieci VPN jest aktywne, otwórz wiersz polecenia z podwyższonym poziomem uprawnień, a następnie uruchom polecenie *ipconfig/all*.
2. Przejrzyj wyniki. Zwróć uwagę na fakt, że otrzymany adres IP należy do określonej podczas konfiguracji połączenia punkt-lokacja puli adresów klienta sieci VPN. Wyniki są podobne, jak w następującym przykładzie:

   ```
   PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.3(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
   ```

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Nawiązywanie połączenia z maszyną wirtualną

Te instrukcje dotyczą klientów w systemie Windows.

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

* Sprawdź, czy pakiet konfiguracji klienta sieci VPN został wygenerowany po określeniu adresów IP serwera DNS dla sieci wirtualnej. Jeśli adresy IP serwera DNS zostały zaktualizowane, wygeneruj i zainstaluj nowy pakiet konfiguracji klienta sieci VPN.

* Użyj narzędzia „ipconfig”, aby sprawdzić adres IPv4 przypisany do karty Ethernet na komputerze, z którego jest nawiązywane połączenie. Jeśli adres IP znajduje się w zakresie adresów sieci wirtualnej, z którą jest nawiązywane połączenie, lub w zakresie adresów puli VPNClientAddressPool, jest to określane jako nakładająca się przestrzeń adresowa. Kiedy przestrzeń adresowa nakłada się w ten sposób, ruch sieciowy nie dociera do platformy Azure, tylko pozostaje w sieci lokalnej.

## <a name="to-add-or-remove-trusted-root-certificates"></a><a name="add"></a>Dodawanie lub usuwanie zaufanych certyfikatów głównych

Zaufane certyfikaty główne można dodawać do platformy Azure lub z niej usuwać. Po usunięciu certyfikatu głównego klienci, którzy mają certyfikat wygenerowany na podstawie tego certyfikatu głównego, nie będą w stanie się uwierzytelnić i w związku z tym nie będą mogli nawiązywać połączeń. Jeśli chcesz, aby klienci mogli uwierzytelniać się i nawiązywać połączenia, musisz zainstalować nowy certyfikat klienta wygenerowany na podstawie certyfikatu głównego, który jest traktowany jako zaufany przez platformę Azure (przekazany na tę platformę).

### <a name="to-add-a-trusted-root-certificate"></a>Aby dodać zaufany certyfikat główny

Na platformie Azure można dodać maksymalnie 20 plików cer zaufanego certyfikatu głównego. Aby uzyskać instrukcje, zobacz sekcję [Przekazywanie zaufanego certyfikatu głównego](#uploadfile) w tym artykule.

### <a name="to-remove-a-trusted-root-certificate"></a>Usuwanie zaufanego certyfikatu głównego

1. Aby usunąć zaufany certyfikat główny, przejdź do strony **Konfiguracja punktu do lokacji** dla bramy Twojej sieci wirtualnej.
1. W sekcji **Certyfikat główny** na stronie znajdź certyfikat, który chcesz usunąć.
1. Wybierz wielokropek obok certyfikatu, a następnie wybierz pozycję "Usuń".

## <a name="to-revoke-a-client-certificate"></a><a name="revokeclient"></a>Aby odwołać certyfikat klienta

Certyfikaty klienta można odwołać. Lista odwołania certyfikatów umożliwia dokonanie selektywnej odmowy połączenia punkt-lokacja w oparciu o indywidualne certyfikaty klienta. Różni się to od usuwania zaufanego certyfikatu głównego. Jeśli usuniesz plik cer zaufanego certyfikatu głównego z platformy Azure, spowoduje to odwołanie dostępu dla wszystkich certyfikatów klienta wygenerowanych lub podpisanych przez odwołany certyfikat główny. Odwołanie certyfikatu klienta zamiast certyfikatu głównego pozwala dalej używać innych certyfikatów wygenerowanych na podstawie certyfikatu głównego do uwierzytelniania.

Częstą praktyką jest użycie certyfikatu głównego do zarządzania dostępem na poziomach zespołu lub organizacji przy jednoczesnym korzystaniu z odwołanych certyfikatów klienta dla bardziej precyzyjnej kontroli dostępu poszczególnych użytkowników.

### <a name="revoke-a-client-certificate"></a>Odwołanie certyfikatu klienta

Certyfikat klienta można odwołać przez dodanie odcisku palca do listy odwołania.

1. Pobierz odcisk palca certyfikatu klienta. Aby uzyskać więcej informacji, zobacz [Jak pobrać odcisk palca certyfikatu](/dotnet/framework/wcf/feature-details/how-to-retrieve-the-thumbprint-of-a-certificate).
1. Skopiuj informacje do edytora tekstu i usuń wszelkie spacje, tak aby powstał ciąg bez odstępów.
1. Przejdź do strony **Konfiguracja punktu do lokacji** bramy sieci wirtualnej. Korzystając z tej samej strony, [przekazano zaufany certyfikat główny](#uploadfile).
1. W sekcji **Odwołane certyfikaty** wprowadź przyjazną nazwę certyfikatu (nie musi to być nazwa pospolita certyfikatu).
1. Skopiuj i wklej ciąg odcisku palca do pola **Odcisk palca**.
1. Odcisk palca zostanie zweryfikowany i automatycznie dodany do listy odwołania. Na ekranie zobaczysz komunikat informujący o aktualizowaniu listy. 
1. Po zakończeniu aktualizowania nie będzie można już używać certyfikatu do nawiązywania połączeń. Klienci, którzy spróbują połączyć się za pomocą tego certyfikatu, zobaczą komunikat informujący o tym, że certyfikat nie jest już ważny.

## <a name="point-to-site-faq"></a><a name="faq"></a>Często zadawane pytania dotyczące punkt-lokacja

Ta sekcja zawiera często zadawane pytania dotyczące konfiguracji punkt-lokacja. Możesz również wyświetlić często zadawane [pytania dotyczące VPN Gateway,](vpn-gateway-vpn-faq.md) aby uzyskać dodatkowe informacje VPN Gateway.

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="next-steps"></a>Następne kroki
Po zakończeniu procesu nawiązywania połączenia można dodać do sieci wirtualnych maszyny wirtualne. Aby uzyskać więcej informacji, zobacz [Virtual Machines](../index.yml) (Maszyny wirtualne). Aby dowiedzieć się więcej o sieci i maszynach wirtualnych, zobacz [Azure and Linux VM network overview](../virtual-machines/network-overview.md) (Omówienie sieci maszyny wirtualnej z systemem Linux i platformy Azure).

Aby uzyskać informacje dotyczące rozwiązywania problemów z połączeniem typu punkt-lokacja, zobacz [Troubleshooting Azure point-to-site connections (Rozwiązywanie problemów z połączeniami typu punkt-lokacja na platformie Azure)](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
