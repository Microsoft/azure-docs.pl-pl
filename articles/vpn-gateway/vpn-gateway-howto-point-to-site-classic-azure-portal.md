---
title: 'Łączenie komputera z siecią wirtualną przy użyciu P2S: uwierzytelnianie certyfikatu: Azure Portal klasyczny'
titleSuffix: Azure VPN Gateway
description: Utwórz klasyczne połączenie punkt-lokacja VPN Gateway przy użyciu Azure Portal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/08/2020
ms.author: cherylmc
ms.openlocfilehash: 42b0945de55775f55f20cefdeb547cb5d6492c06
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94657078"
---
# <a name="configure-a-point-to-site-connection-by-using-certificate-authentication-classic"></a>Konfigurowanie połączenia typu punkt-lokacja przy użyciu uwierzytelniania certyfikatu (wersja klasyczna)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

W tym artykule pokazano, jak utworzyć sieć wirtualną z połączeniem typu punkt-lokacja. Tę sieć wirtualną można utworzyć za pomocą klasycznego modelu wdrażania przy użyciu Azure Portal. Ta konfiguracja korzysta z certyfikatów z podpisem własnym lub wystawionych przez urząd certyfikacji do uwierzytelniania klienta nawiązującego połączenie. Tę konfigurację możesz również utworzyć przy użyciu innego narzędzia lub modelu wdrażania, korzystając z opcji opisanych w następujących artykułach:

> [!div class="op_single_selector"]
> * [Witryna Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Program PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Portal Azure (klasyczny)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>

Będziemy używać bramy sieci VPN typu punkt-lokacja (P2S, Point-to-Site), która pozwala utworzyć bezpieczne połączenie z siecią wirtualną z poziomu komputera klienckiego. Połączenia sieci VPN typu punkt-lokacja przydają się, gdy użytkownik chce połączyć się z siecią wirtualną z lokalizacji zdalnej. Połączenie sieci VPN typu punkt-lokacja jest lepszym rozwiązaniem niż połączenie sieci VPN typu lokacja-lokacja w przypadku niewielkiej liczby klientów, którzy muszą się łączyć z siecią wirtualną. Połączenie sieci VPN typu punkt-lokacja jest nawiązywane przez zainicjowanie go z komputera klienckiego.

> [!IMPORTANT]
> Klasyczny model wdrożenia obsługuje tylko klientów sieci VPN systemu Windows i używa protokołu Secure Socket Tunneling Protocol (SSTP) będącego protokołem sieci VPN opartym na protokole SSL. Aby zapewnić obsługę innych klientów niż sieć VPN systemu Windows, sieć wirtualna musi zostać utworzona przy użyciu modelu wdrażania usługi Resource Manager. Model wdrażania usługi Resource Manager obsługuje, oprócz protokołu SSTP, protokół IKEv2 sieci VPN. Aby uzyskać więcej informacji, zobacz [About P2S connections (Informacje o połączeniach punkt-lokacja)](point-to-site-about.md).
>
>

![Diagram: połączenie typu punkt-lokacja](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)

## <a name="settings-and-requirements"></a>Ustawienia i wymagania

### <a name="requirements"></a>Wymagania

Połączenia z uwierzytelnianiem certyfikatu punkt-lokacja wymagają następujących elementów. W tym artykule opisano kroki, które ułatwią ich tworzenie.

* Brama dynamicznej sieci VPN.
* Klucz publiczny (plik cer) dla certyfikatu głównego, przekazany na platformę Azure. Ten klucz jest uznawany za certyfikat zaufany i jest używany do uwierzytelniania.
* Certyfikat klienta wygenerowany na podstawie certyfikatu głównego i zainstalowany na każdym komputerze klienckim, który będzie nawiązywać połączenie. Ten certyfikat jest używany do uwierzytelniania klientów.
* Pakiet konfiguracji klienta sieci VPN musi zostać wygenerowany i zainstalowany na każdym komputerze klienckim, który nawiązuje połączenie. Pakiet konfiguracji klienta konfiguruje natywnego klienta sieci VPN, który znajduje się już w systemie operacyjnym, ustawiając w nim informacje niezbędne do łączenia się z siecią wirtualną.

Połączenia typu punkt-lokacja nie wymagają urządzenia sieci VPN ani lokalnego publicznego adresu IP. Połączenie sieci VPN jest nawiązywane za pośrednictwem protokołu SSTP (Secure Socket Tunneling Protocol). Po stronie serwera obsługiwany jest protokół SSTP w wersji 1.0, 1.1 i 1.2. Klient decyduje o wyborze wersji do użycia. W przypadku systemu Windows 8.1 i nowszych protokół SSTP domyślnie używa wersji 1.2.

Aby uzyskać więcej informacji, zobacz [Informacje o połączeniach punkt-lokacja](point-to-site-about.md) i [często zadawanych pytaniach](#faq).

### <a name="example-settings"></a>Przykładowe ustawienia

Następujących wartości możesz użyć do tworzenia środowiska testowego lub odwoływać się do tych wartości, aby lepiej zrozumieć przykłady w tym artykule:

* **Grupa zasobów:** TestRG
* **Nazwa sieci wirtualnej:** VNet1
* **Przestrzeń adresowa:** 192.168.0.0/16 <br>W tym przykładzie zostanie wykorzystana tylko jedna przestrzeń adresowa. Istnieje możliwość użycia więcej niż jednej przestrzeni adresowej dla sieci wirtualnej.
* **Nazwa podsieci:** Frontonu
* **Zakres adresów podsieci:** 192.168.1.0/24
* **GatewaySubnet:** 10.11.255.0/27
* **Region:** (US) Wschodnie stany USA
* **Przestrzeń adresowa klienta:** 172.16.201.0/24 <br> Klienci sieci VPN połączeni z siecią wirtualną, którzy korzystają z tego połączenia punkt-lokacja, otrzymują adresy IP z określonej puli.
* **Typ połączenia**: wybierz pozycję **punkt-lokacja**.
* **GatewaySubnet zakres adresów (blok CIDR):** 192.168.200.0/24

Przed rozpoczęciem sprawdź, czy masz subskrypcję platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial).

## <a name="create-a-virtual-network"></a><a name="vnet"></a>Tworzenie sieci wirtualnej

Jeśli masz już sieć wirtualną, sprawdź, czy ustawienia są zgodne z projektem bramy sieci VPN. Zwróć szczególną uwagę na wszelkie podsieci, które mogą pokrywać się z innymi sieciami.

[!INCLUDE [basic classic vnet](../../includes/vpn-gateway-vnet-classic.md)]

[!INCLUDE [basic classic DNS](../../includes/vpn-gateway-dns-classic.md)]

## <a name="create-a-vpn-gateway"></a><a name="gateway"></a>Tworzenie bramy sieci VPN

1. Przejdź do utworzonej sieci wirtualnej.
1. Na stronie Sieć wirtualna w obszarze Ustawienia wybierz pozycję **brama**. Na stronie **brama** można wyświetlić bramę sieci wirtualnej. Ta sieć wirtualna nie ma jeszcze bramy. Kliknij **tutaj, aby dodać połączenie i bramę**.
1. Na stronie **Konfigurowanie połączenia sieci VPN i bramy** wybierz następujące ustawienia:

   * Typ połączenia: punkt-lokacja
   * Przestrzeń adresowa klienta: Dodaj zakres adresów IP, z którego klienci sieci VPN otrzymują adres IP podczas nawiązywania połączenia. Używaj zakresu prywatnych adresów IP nienakładającego się na lokalizację lokalną, z której się łączysz, ani na sieć wirtualną, z którą chcesz się łączyć.
1. Pozostaw pole wyboru dla opcji **nie konfiguruj bramy w tym momencie bez** wyboru. Utworzymy bramę.
1. W dolnej części strony wybierz pozycję **Dalej: brama >**.
1. Na karcie **brama** wybierz następujące wartości:

   * **Rozmiar:** Rozmiar to jednostka SKU bramy dla bramy sieci wirtualnej. W witrynie Azure Portal domyślną jednostką SKU jest **Domyślna**. Więcej informacji o jednostkach SKU bramy zawiera artykuł [Informacje o ustawieniach bramy VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).
   * **Typ routingu:** Należy wybrać opcję **dynamiczny** dla konfiguracji typu punkt-lokacja. Routing statyczny nie będzie działał.
   * **Podsieć bramy:** To pole jest już wypełniane. Nie można zmienić nazwy. Jeśli spróbujesz zmienić nazwę przy użyciu programu PowerShell lub innych metod, Brama nie będzie działała prawidłowo.
   * **Zakres adresów (blok CIDR):** Chociaż istnieje możliwość utworzenia podsieci bramy jako małej jako/29, zalecamy utworzenie większej podsieci zawierającej więcej adresów, wybierając co najmniej/28 lub/27. Zapewni to wystarczająco dużo adresów, aby możliwe były dodatkowe konfiguracje, które mogą być potrzebne w przyszłości. Podczas pracy z podsieciami bramy należy unikać kojarzenia sieciowej grupy zabezpieczeń (NSG, Network Security Group) z podsiecią bramy. Skojarzenie sieciowej grupy zabezpieczeń z tą podsiecią może spowodować, że brama sieci VPN przestanie działać zgodnie z oczekiwaniami.
1. Wybierz pozycję **Przegląd + Utwórz** , aby sprawdzić poprawność ustawień.
1. Po zakończeniu walidacji wybierz pozycję **Utwórz**. Tworzenie bramy sieci VPN może zająć do 45 minut, zależnie od wybranej jednostki SKU bramy.

## <a name="create-certificates"></a><a name="generatecerts"></a>Tworzenie certyfikatów

Na platformie Azure certyfikaty są używane do uwierzytelniania klientów sieci VPN w obrębie sieci VPN typu punkt-lokacja. Informacje o kluczu publicznym certyfikatu głównego należy przekazać na platformę Azure. Klucz publiczny jest wtedy uważany za *zaufany*. Certyfikaty klienta muszą zostać wygenerowane na podstawie zaufanego certyfikatu głównego, a następnie zainstalowane na każdym komputerze klienckim w magazynie certyfikatów Certificates-Current User\Personal\Certificates. Certyfikat jest używany do uwierzytelniania klienta, gdy inicjuje on połączenie z siecią wirtualną. 

Jeśli używasz certyfikatów z podpisem własnym, musisz je utworzyć przy użyciu określonych parametrów. Certyfikat z podpisem własnym możesz utworzyć przy użyciu polecenia [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) lub instrukcji dotyczących [środowiska PowerShell i systemu Windows 10](vpn-gateway-certificates-point-to-site.md). Ważne jest, aby wykonać kroki opisane w tych instrukcjach w przypadku używania certyfikatów głównych z podpisem własnym i generowania certyfikatów klienta na podstawie certyfikatu głównego z podpisem własnym. W przeciwnym razie utworzone przez Ciebie certyfikaty nie będą zgodne z połączeniami typu punkt-lokacja i zostanie wyświetlony błąd połączenia.

### <a name="acquire-the-public-key-cer-for-the-root-certificate"></a>Uzyskiwanie klucza publicznego (pliku cer) dla certyfikatu głównego

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generate-a-client-certificate"></a>Generowanie certyfikatu klienta

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload-the-root-certificate-cer-file"></a>Przekazywanie pliku cer certyfikatu głównego

Po utworzeniu bramy przekaż plik cer (który zawiera informacje o kluczu publicznym) dla zaufanego certyfikatu głównego na serwer platformy Azure. Nie przekazuj klucza prywatnego dla certyfikatu głównego. Po przekazaniu certyfikatu platforma Azure będzie używać go do uwierzytelniania klientów, którzy mają zainstalowany certyfikat klienta wygenerowany na podstawie zaufanego certyfikatu głównego. W razie potrzeby dodatkowe pliki zaufanego certyfikatu głównego możesz przekazać później — maksymalnie może ich być 20.

1. Przejdź do utworzonej sieci wirtualnej.
1. W obszarze **Ustawienia** wybierz pozycję **połączenia punkt-lokacja**.
1. Wybierz pozycję **Zarządzaj certyfikatem**.
1. Wybierz pozycję **Przekaż**.
1. W okienku **Przekaż certyfikat** wybierz ikonę folderu i przejdź do certyfikatu, który chcesz przekazać.
1. Wybierz pozycję **Przekaż**.
1. Po pomyślnym przekazaniu certyfikatu można go wyświetlić na stronie Zarządzanie certyfikatem. Może być konieczne wybranie opcji **Odśwież** , aby wyświetlić przekazany certyfikat.

## <a name="configure-the-client"></a>Konfigurowanie klienta

Aby nawiązać połączenie z siecią wirtualną przy użyciu połączenia sieci VPN typu punkt-lokacja, na każdym kliencie trzeba zainstalować pakiet do konfiguracji natywnego klienta sieci VPN systemu Windows. Pakiet konfiguracji powoduje skonfigurowanie natywnego klienta sieci VPN systemu Windows za pomocą ustawień koniecznych do łączenia się z siecią wirtualną.

Tego samego pakietu konfiguracji klienta VPN można użyć na każdym komputerze klienckim, o ile wersja jest zgodna z architekturą dla klienta. Listę obsługiwanych systemów operacyjnych klienta znajduje się w temacie [Informacje o połączeniach typu punkt-lokacja](point-to-site-about.md) i [często zadawanych pytaniach](#faq).

### <a name="generate-and-install-a-vpn-client-configuration-package"></a>Generowanie i instalowanie pakietu konfiguracji klienta sieci VPN

1. Przejdź do ustawień **połączeń punkt-lokacja** dla sieci wirtualnej.
1. W górnej części strony wybierz pakiet pobierania, który odpowiada systemowi operacyjnemu klienta, na którym zostanie zainstalowany:

   * W przypadku klientów 64-bitowych wybierz pozycję **klient VPN (64-bitowy)**.
   * W przypadku klientów 32-bitowych wybierz pozycję **klient VPN (32-bitowy)**.

1. Platforma Azure generuje pakiet z określonymi ustawieniami wymaganymi przez klienta. Za każdym razem, gdy wprowadzasz zmiany w sieci wirtualnej lub bramie, musisz pobrać nowy pakiet konfiguracji klienta i zainstalować go na komputerach klienckich.
1. Po wygenerowaniu pakietu wybierz pozycję **Pobierz**.
1. Zainstaluj pakiet konfiguracji klienta na komputerze klienckim. Jeśli podczas instalacji zostanie wyświetlony podręczny komunikat filtru SmartScreen z informacją o ochronie systemu Windows komputera, wybierz pozycję **więcej informacji**, a następnie wybierz pozycję **Uruchom mimo to**. Możesz także zapisać pakiet w celu zainstalowania go na innych komputerach klienckich.

### <a name="install-a-client-certificate"></a>Instalowanie certyfikatu klienta

W tym ćwiczeniu podczas generowania certyfikatu klienta został on automatycznie zainstalowany na komputerze. Aby utworzyć połączenie P2S z innego komputera klienckiego niż używany do generowania certyfikatów klienta, należy zainstalować na tym komputerze wygenerowany certyfikat klienta.

Podczas instalowania certyfikatu klienta potrzebne jest hasło, które zostało utworzone w trakcie eksportowania certyfikatu klienta. Zazwyczaj można zainstalować certyfikat, klikając go dwukrotnie. Aby uzyskać więcej informacji, zobacz [Install an exported client certificate](vpn-gateway-certificates-point-to-site.md#install) (Instalowanie wyeksportowanego certyfikatu klienta).

## <a name="connect-to-your-vnet"></a>Nawiązywanie połączenia z siecią wirtualną

>[!NOTE]
>Musisz mieć uprawnienia administratora na komputerze klienckim, z którym nawiązujesz połączenie.
>

1. Na komputerze klienckim przejdź do ustawień sieci VPN.
1. Wybierz utworzoną sieć VPN. Jeśli użyto przykładowych ustawień, połączenie zostanie oznaczone jako **Grupa TestRG VNet1**.
1. Wybierz pozycję **Połącz**.
1. W polu Virtual Network systemu Windows Azure wybierz pozycję **Połącz**. Jeśli zostanie wyświetlony komunikat podręczny o certyfikacie, wybierz pozycję **Kontynuuj** , aby użyć podwyższonych uprawnień i **tak** , aby akceptować zmiany w konfiguracji.
1. Po pomyślnym nawiązaniu połączenia zobaczysz **połączone** powiadomienie.

[!INCLUDE [verify-client-certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

## <a name="verify-the-vpn-connection"></a>Sprawdzenie połączenia sieci VPN

1. Sprawdź, czy połączenie sieci VPN jest aktywne. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień na komputerze klienckim, a następnie uruchom polecenie **ipconfig/all**.
1. Przejrzyj wyniki. Zwróć uwagę na fakt, że otrzymany adres IP jest jednym z adresów z zakresu dla połączenia typu punkt-lokacja określonego podczas tworzenia sieci wirtualnej. Wyniki powinny być podobne do poniższego przykładu:

   ```
    PPP adapter VNet1:
        Connection-specific DNS Suffix .:
        Description.....................: VNet1
        Physical Address................:
        DHCP Enabled....................: No
        Autoconfiguration Enabled.......: Yes
        IPv4 Address....................: 192.168.130.2(Preferred)
        Subnet Mask.....................: 255.255.255.255
        Default Gateway.................:
        NetBIOS over Tcpip..............: Enabled
   ```

## <a name="to-connect-to-a-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-classic-include.md)]

## <a name="to-add-or-remove-trusted-root-certificates"></a>Dodawanie lub usuwanie zaufanych certyfikatów głównych

Zaufane certyfikaty główne można dodawać do platformy Azure lub z niej usuwać. Po usunięciu certyfikatu głównego klienci, którzy mają certyfikat wygenerowany na podstawie tego certyfikatu głównego, nie będą w stanie się uwierzytelnić i w związku z tym nie będą mogli nawiązywać połączeń. Jeśli chcesz, aby klienci mogli uwierzytelniać się i nawiązywać połączenia, musisz zainstalować nowy certyfikat klienta wygenerowany na podstawie certyfikatu głównego, który jest traktowany jako zaufany przez platformę Azure.

### <a name="add-a-trusted-root-certificate"></a>Dodawanie zaufanego certyfikatu głównego

Do platformy Azure można dodać maksymalnie 20 plików CER zaufanych certyfikatów głównych przy użyciu tego samego procesu, który został użyty w celu dodania pierwszego zaufanego certyfikatu głównego.

### <a name="remove-a-trusted-root-certificate"></a>Usuń zaufany certyfikat główny

1. W sekcji **połączenia typu punkt-lokacja** sieci wirtualnej wybierz pozycję **Zarządzaj certyfikatem**.
1. Wybierz wielokropek obok certyfikatu, który chcesz usunąć, a następnie wybierz pozycję **Usuń**.

## <a name="to-revoke-a-client-certificate"></a>Aby odwołać certyfikat klienta

W razie potrzeby możesz odwołać certyfikat klienta. Lista odwołania certyfikatów umożliwia dokonanie selektywnej odmowy połączenia punkt-lokacja w oparciu o indywidualne certyfikaty klienta. Różni się to od usuwania zaufanego certyfikatu głównego. Jeśli usuniesz plik cer zaufanego certyfikatu głównego z platformy Azure, spowoduje to odwołanie dostępu dla wszystkich certyfikatów klienta wygenerowanych lub podpisanych przez odwołany certyfikat główny. Odwołanie certyfikatu klienta zamiast certyfikatu głównego pozwala dalej używać innych certyfikatów wygenerowanych na podstawie certyfikatu głównego do uwierzytelniania połączeń punkt-lokacja.

Częstą praktyką jest użycie certyfikatu głównego do zarządzania dostępem na poziomach zespołu lub organizacji przy jednoczesnym korzystaniu z odwołanych certyfikatów klienta dla bardziej precyzyjnej kontroli dostępu poszczególnych użytkowników.

Certyfikat klienta można odwołać przez dodanie odcisku palca do listy odwołania.

1. Pobierz odcisk palca certyfikatu klienta. Aby uzyskać więcej informacji, zobacz [How to: Pobieranie odcisku palca certyfikatu](/dotnet/framework/wcf/feature-details/how-to-retrieve-the-thumbprint-of-a-certificate).
1. Skopiuj informacje do edytora tekstu i usuń wszelkie spacje, tak aby powstał ciąg bez odstępów.
1. Przejdź do **połączenia sieci VPN typu punkt-lokacja**, a następnie wybierz pozycję **Zarządzaj certyfikatem**.
1. Wybierz pozycję **Lista odwołania**, aby otworzyć stronę **Lista odwołania**.
1. W obszarze **Odcisk palca** wklej odcisk palca certyfikatu jako jeden ciągły wiersz tekstu bez odstępów.
1. Wybierz pozycję **+ Dodaj do listy** , aby dodać odcisk palca do listy odwołania certyfikatów (CRL).

Po zakończeniu aktualizowania nie będzie można już używać certyfikatu do nawiązywania połączeń. Klienci, którzy spróbują połączyć się za pomocą tego certyfikatu, zobaczą komunikat informujący o tym, że certyfikat nie jest już ważny.

## <a name="faq"></a><a name="faq"></a>Często zadawane pytania

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-point-to-site-classic-include.md)]

## <a name="next-steps"></a>Następne kroki

* Po zakończeniu procesu nawiązywania połączenia można dodać maszyny wirtualne do sieci wirtualnych. Aby uzyskać więcej informacji, zobacz [Virtual Machines](../index.yml) (Maszyny wirtualne).

* Aby dowiedzieć się więcej o sieci i maszynach wirtualnych z systemem Linux, zobacz [Azure and Linux VM network overview](../virtual-machines/network-overview.md) (Omówienie sieci maszyny wirtualnej z systemem Linux i platformy Azure).

* Aby uzyskać informacje dotyczące rozwiązywania problemów z połączeniem typu punkt-lokacja, zobacz [Troubleshoot Azure point-to-site connections (Rozwiązywanie problemów z połączeniami typu punkt-lokacja na platformie Azure)](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).