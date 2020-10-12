---
title: 'Łączenie komputera z siecią wirtualną przy użyciu połączenia typu punkt-lokacja i uwierzytelniania certyfikatu: klasyczna witryna Azure Portal | Microsoft Docs'
description: Utwórz klasyczne połączenie bramy sieci VPN typu punkt-lokacja przy użyciu witryny Azure Portal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 01/09/2020
ms.author: cherylmc
ms.openlocfilehash: f68631771b8f86d995108112b1243ab38bf826bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84984788"
---
# <a name="configure-a-point-to-site-connection-by-using-certificate-authentication-classic"></a>Konfigurowanie połączenia typu punkt-lokacja przy użyciu uwierzytelniania certyfikatu (wersja klasyczna)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

W tym artykule pokazano, jak utworzyć sieć wirtualną z połączeniem typu punkt-lokacja. Ta sieć wirtualna zostanie utworzona za pomocą klasycznego modelu wdrażania w witrynie Azure Portal. Ta konfiguracja korzysta z certyfikatów z podpisem własnym lub wystawionych przez urząd certyfikacji do uwierzytelniania klienta nawiązującego połączenie. Tę konfigurację możesz również utworzyć przy użyciu innego narzędzia lub modelu wdrażania, korzystając z opcji opisanych w następujących artykułach:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Program PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Portal Azure (klasyczny)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>

Będziemy używać bramy sieci VPN typu punkt-lokacja (P2S, Point-to-Site), która pozwala utworzyć bezpieczne połączenie z siecią wirtualną z poziomu komputera klienckiego. Połączenia sieci VPN typu punkt-lokacja przydają się, gdy użytkownik chce połączyć się z siecią wirtualną z lokalizacji zdalnej. Połączenie sieci VPN typu punkt-lokacja jest lepszym rozwiązaniem niż połączenie sieci VPN typu lokacja-lokacja w przypadku niewielkiej liczby klientów, którzy muszą się łączyć z siecią wirtualną. Połączenie sieci VPN typu punkt-lokacja jest nawiązywane przez zainicjowanie go z komputera klienckiego.

> [!IMPORTANT]
> Klasyczny model wdrożenia obsługuje tylko klientów sieci VPN systemu Windows i używa protokołu Secure Socket Tunneling Protocol (SSTP) będącego protokołem sieci VPN opartym na protokole SSL. Aby zapewnić obsługę innych klientów niż sieć VPN systemu Windows, sieć wirtualna musi zostać utworzona przy użyciu modelu wdrażania usługi Resource Manager. Model wdrażania usługi Resource Manager obsługuje, oprócz protokołu SSTP, protokół IKEv2 sieci VPN. Aby uzyskać więcej informacji, zobacz [About P2S connections (Informacje o połączeniach punkt-lokacja)](point-to-site-about.md).
>
>

![Diagram: połączenie typu punkt-lokacja](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)

## <a name="prerequisites"></a>Wymagania wstępne

Połączenia typu punkt-lokacja z uwierzytelnianiem certyfikatu mają następujące wymagania wstępne:

* Brama dynamicznej sieci VPN.
* Klucz publiczny (plik cer) dla certyfikatu głównego, przekazany na platformę Azure. Ten klucz jest uznawany za certyfikat zaufany i jest używany do uwierzytelniania.
* Certyfikat klienta wygenerowany na podstawie certyfikatu głównego i zainstalowany na każdym komputerze klienckim, który będzie nawiązywać połączenie. Ten certyfikat jest używany do uwierzytelniania klientów.
* Pakiet konfiguracji klienta sieci VPN musi zostać wygenerowany i zainstalowany na każdym komputerze klienckim, który nawiązuje połączenie. Pakiet konfiguracji klienta konfiguruje natywnego klienta sieci VPN, który znajduje się już w systemie operacyjnym, ustawiając w nim informacje niezbędne do łączenia się z siecią wirtualną.

Połączenia typu punkt-lokacja nie wymagają urządzenia sieci VPN ani lokalnego publicznego adresu IP. Połączenie sieci VPN jest nawiązywane za pośrednictwem protokołu SSTP (Secure Socket Tunneling Protocol). Po stronie serwera obsługiwany jest protokół SSTP w wersji 1.0, 1.1 i 1.2. Klient decyduje o wyborze wersji do użycia. W przypadku systemu Windows 8.1 i nowszych protokół SSTP domyślnie używa wersji 1.2. 

Aby uzyskać więcej informacji na temat połączeń punkt-lokacja, zobacz [Często zadawane pytania dotyczące połączeń punkt-lokacja](#point-to-site-faq).

### <a name="example-settings"></a>Przykładowe ustawienia

Następujących wartości możesz użyć do tworzenia środowiska testowego lub odwoływać się do tych wartości, aby lepiej zrozumieć przykłady w tym artykule:

- **Ustawienia tworzenia sieci wirtualnej (model klasyczny)**
   - **Nazwa**: wprowadź *VNet1*.
   - **Przestrzeń adresowa**: wprowadź *192.168.0.0/16*. W tym przykładzie zostanie wykorzystana tylko jedna przestrzeń adresowa. Istnieje możliwość użycia więcej niż jednej przestrzeni adresowej dla sieci wirtualnej, jak pokazano na diagramie.
   - **Nazwa podsieci**: wprowadź *fronton*.
   - **Zakres adresów podsieci**: wprowadź *192.168.1.0/24*.
   - **Subskrypcja**: wybierz subskrypcję z listy dostępnych subskrypcji.
   - **Grupa zasobów**: wprowadź *TestRG*. Wybierz pozycję **Utwórz nową**, jeśli grupa zasobów nie istnieje.
   - **Lokalizacja**: wybierz pozycję **Wschodnie stany USA** z listy.

  - **Ustawienia połączenia sieci VPN**
    - **Typ połączenia**: wybierz pozycję **punkt-lokacja**.
    - **Przestrzeń adresowa klienta**: wprowadź *172.16.201.0/24*. Klienci sieci VPN połączeni z siecią wirtualną, którzy korzystają z tego połączenia punkt-lokacja, otrzymują adresy IP z określonej puli.

- **Ustawienia podsieci konfiguracji bramy**
   - **Nazwa**: wypełniane *autoGatewaySubnet*.
   - **Zakres adresów**: wprowadź *192.168.200.0/24*. 

- **Ustawienia konfiguracji bramy**:
   - **Rozmiar**: Wybierz jednostkę SKU bramy, która ma być używana.
   - **Typ routingu**: wybierz pozycję **dynamiczny**.

## <a name="create-a-virtual-network-and-a-vpn-gateway"></a>Tworzenie sieci wirtualnej i bramy VPN Gateway

Przed rozpoczęciem sprawdź, czy masz subskrypcję platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial).

### <a name="part-1-create-a-virtual-network"></a>Część 1. Tworzenie sieci wirtualnej

Jeśli nie masz jeszcze sieci wirtualnej, utwórz ją. Zamieszczone zrzuty ekranu są przykładowe. Przedstawione na nich wartości należy zastąpić własnymi. Aby utworzyć sieć wirtualną przy użyciu witryny Azure Portal, wykonaj poniższe kroki:

1. W menu witryny [Azure Portal](https://portal.azure.com) lub na **stronie głównej** wybierz pozycję **Utwórz zasób**. Zostanie otwarta strona **Nowy**.

2. W polu **Wyszukaj w witrynie Marketplace** wpisz *sieć wirtualna* i wybierz pozycję **Sieć wirtualna** na liście wyników. Zostanie otwarta strona **Sieć wirtualna**.

3. Na liście **Wybierz model wdrożenia** wybierz pozycję **Klasyczny**, a następnie wybierz pozycję **Utwórz**. Spowoduje to otwarcie strony **Tworzenie sieci wirtualnej**.

4. Na stronie **Tworzenie sieci wirtualnej** skonfiguruj ustawienia sieci wirtualnej. Na tej stronie dodaj pierwszą przestrzeń adresową i zakres adresów pojedynczej podsieci. Po zakończeniu tworzenia sieci wirtualnej możesz wrócić i dodać dodatkowe podsieci oraz przestrzenie adresowe.

   ![Strona Tworzenie sieci wirtualnej](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png)

5. Na liście rozwijanej wybierz **subskrypcję**, której chcesz użyć.

6. Wybierz istniejącą **grupę zasobów**. Możesz też utworzyć nową grupę zasobów, wybierając pozycję **Utwórz nową** i wprowadzając nazwę. Jeśli tworzysz nową grupę zasobów, nadaj jej nazwę odpowiadającą wartościom planowanej konfiguracji. Aby uzyskać więcej informacji na temat grup zasobów, zobacz [Omówienie usługi Azure Resource Manager](../azure-resource-manager/management/overview.md#resource-groups).

7. Wybierz **lokalizację** sieci wirtualnej. To ustawienie określa lokalizację geograficzną zasobów wdrażanych w tej sieci wirtualnej.

8. Wybierz pozycję **Utwórz**, aby utworzyć sieć wirtualną. Na stronie **Powiadomienia** pojawi się komunikat **Wdrażanie jest w toku**.

8. Po utworzeniu sieci wirtualnej treść komunikatu na stronie **Powiadomienia** zmieni się na **Wdrożenie powiodło się**. Jeśli chcesz, aby Twoją sieć wirtualną można było łatwo znaleźć na pulpicie nawigacyjnym, wybierz pozycję **Przypnij do pulpitu nawigacyjnego**. 

10. Dodaj serwer DNS (opcjonalnie). Po utworzeniu sieci wirtualnej możesz dodać adres IP serwera DNS na potrzeby rozpoznawania nazw. Określony adres IP serwera DNS powinien rozpoznawać nazwy zasobów w sieci wirtualnej.

    Aby dodać serwer DNS, wybierz pozycję **Serwery DNS** na stronie sieci wirtualnej. Wprowadź adres IP serwera DNS, którego chcesz użyć, a następnie wybierz pozycję **Zapisz**.

### <a name="part-2-create-a-gateway-subnet-and-a-dynamic-routing-gateway"></a>Część 2: Tworzenie podsieci bramy i bramy routingu dynamicznego

W tym kroku tworzona jest podsieć bramy i brama o dynamicznym routingu. W klasycznym modelu wdrażania w witrynie Azure Portal tworzenie bramy i podsieci bramy można wykonać na tych samych stronach konfiguracji. Podsieć bramy jest używana tylko na potrzeby usług bramy. Nigdy nie należy wdrażać żadnych elementów, takich jak maszyny wirtualne lub inne usługi, bezpośrednio w podsieci bramy.

1. W Azure Portal przejdź do sieci wirtualnej, dla której chcesz utworzyć bramę.

2. Na stronie sieci wirtualnej wybierz pozycję **Przegląd**, a następnie w sekcji **Połączenia VPN** wybierz pozycję **Brama**.

   ![Wybieranie opcji umożliwiających utworzenie bramy](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png)
3. Na stronie **Nowe połączenie VPN**wybierz pozycję **Punkt-lokacja**.

   ![Połączenie typu punkt-lokacja](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png)
4. W obszarze **Przestrzeń adresowa klienta** dodaj zakres adresów IP, z którego klienci sieci VPN otrzymują adres IP podczas nawiązywania połączenia. Używaj zakresu prywatnych adresów IP nienakładającego się na lokalizację lokalną, z której się łączysz, ani na sieć wirtualną, z którą chcesz się łączyć. Automatycznie wypełniony zakres możesz zastąpić zakresem prywatnych adresów IP, z którego chcesz korzystać. Ten przykład przedstawia automatycznie wypełniony zakres. 

   ![Przestrzeń adresowa klienta](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png)
5. Wybierz pozycję **Utwórz bramę natychmiast**, a następnie pozycję **Opcjonalna konfiguracja bramy**, aby otworzyć stronę **Konfiguracja bramy**.

   ![Wybieranie opcjonalnej konfiguracji bramy](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png)

6. Na stronie **Konfiguracja bramy** wybierz pozycję **Podsieć**, aby dodać podsieć bramy. Można utworzyć małą podsieć bramy — taką jak /29. Jednak zalecamy wybranie opcji przynajmniej /28 lub /27 i utworzenie większej podsieci, która zawiera więcej adresów. Zapewni to wystarczająco dużo adresów, aby możliwe były dodatkowe konfiguracje, które mogą być potrzebne w przyszłości. Podczas pracy z podsieciami bramy należy unikać kojarzenia sieciowej grupy zabezpieczeń (NSG, Network Security Group) z podsiecią bramy. Skojarzenie sieciowej grupy zabezpieczeń z tą podsiecią może spowodować, że brama sieci VPN przestanie działać zgodnie z oczekiwaniami. Wybierz przycisk **OK**, aby zapisać to ustawienie.

   ![Dodawanie podsieci GatewaySubnet](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png)
7. Wybierz **rozmiar** bramy. Rozmiar to jednostka SKU bramy sieci wirtualnej. W witrynie Azure Portal domyślną jednostką SKU jest **Domyślna**. Więcej informacji o jednostkach SKU bramy zawiera artykuł [Informacje o ustawieniach bramy VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

   ![Rozmiar bramy](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)
8. Wybierz **Typ routingu** dla bramy. Konfiguracje P2S wymagają typu routingu **Dynamiczny**. Wybierz przycisk **OK** po zakończeniu konfigurowania tej strony.

   ![Konfigurowanie typu routingu](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png)

9. Na stronie **Nowe połączenie VPN** wybierz przycisk **OK** u dołu strony, aby rozpocząć tworzenie bramy sieci wirtualnej. Tworzenie bramy sieci VPN może zająć do 45 minut, zależnie od wybranej jednostki SKU bramy.
 
## <a name="create-certificates"></a><a name="generatecerts"></a>Tworzenie certyfikatów

Na platformie Azure certyfikaty są używane do uwierzytelniania klientów sieci VPN w obrębie sieci VPN typu punkt-lokacja. Informacje o kluczu publicznym certyfikatu głównego należy przekazać na platformę Azure. Klucz publiczny jest wtedy uważany za *zaufany*. Certyfikaty klienta muszą zostać wygenerowane na podstawie zaufanego certyfikatu głównego, a następnie zainstalowane na każdym komputerze klienckim w magazynie certyfikatów Certificates-Current User\Personal\Certificates. Certyfikat jest używany do uwierzytelniania klienta, gdy inicjuje on połączenie z siecią wirtualną. 

Jeśli używasz certyfikatów z podpisem własnym, musisz je utworzyć przy użyciu określonych parametrów. Certyfikat z podpisem własnym możesz utworzyć przy użyciu polecenia [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) lub instrukcji dotyczących [środowiska PowerShell i systemu Windows 10](vpn-gateway-certificates-point-to-site.md). Ważne jest, aby wykonać kroki opisane w tych instrukcjach w przypadku używania certyfikatów głównych z podpisem własnym i generowania certyfikatów klienta na podstawie certyfikatu głównego z podpisem własnym. W przeciwnym razie utworzone przez Ciebie certyfikaty nie będą zgodne z połączeniami typu punkt-lokacja i zostanie wyświetlony błąd połączenia.

### <a name="acquire-the-public-key-cer-for-the-root-certificate"></a>Uzyskiwanie klucza publicznego (pliku cer) dla certyfikatu głównego

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generate-a-client-certificate"></a>Generowanie certyfikatu klienta

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload-the-root-certificate-cer-file"></a>Przekazywanie pliku cer certyfikatu głównego

Po utworzeniu bramy przekaż plik cer (który zawiera informacje o kluczu publicznym) dla zaufanego certyfikatu głównego na serwer platformy Azure. Nie przekazuj klucza prywatnego dla certyfikatu głównego. Po przekazaniu certyfikatu platforma Azure będzie używać go do uwierzytelniania klientów, którzy mają zainstalowany certyfikat klienta wygenerowany na podstawie zaufanego certyfikatu głównego. W razie potrzeby dodatkowe pliki zaufanego certyfikatu głównego możesz przekazać później — maksymalnie może ich być 20.  

1. W sekcji **Połączenia sieci VPN** na stronie danej sieci wirtualnej wybierz grafikę klientów, aby otworzyć stronę **Połączenie sieci VPN typu punkt-lokacja**.

   ![Klienci](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)

2. Na stronie **Połączenie sieci VPN typu punkt-lokacja** wybierz przycisk **Zarządzanie certyfikatami**, aby otworzyć stronę **Certyfikaty**.

   ![Strona Certyfikaty](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)

1. Na stronie **Certyfikaty** wybierz pozycję **Przekaż**, aby otworzyć stronę **Przekaż certyfikat**.

    ![Strona przekazywania certyfikatów](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png)

4. Wybierz grafikę folderu, aby przejść do pliku cer. Wybierz plik, a następnie wybierz przycisk **OK**. Przekazany certyfikat pojawi się na stronie **Certyfikaty**.

   ![Przekazywanie certyfikatu](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png)


## <a name="configure-the-client"></a>Konfigurowanie klienta

Aby nawiązać połączenie z siecią wirtualną przy użyciu połączenia sieci VPN typu punkt-lokacja, na każdym kliencie trzeba zainstalować pakiet do konfiguracji natywnego klienta sieci VPN systemu Windows. Pakiet konfiguracji powoduje skonfigurowanie natywnego klienta sieci VPN systemu Windows za pomocą ustawień koniecznych do łączenia się z siecią wirtualną.

Tego samego pakietu konfiguracji klienta VPN można użyć na każdym komputerze klienckim, o ile wersja jest zgodna z architekturą dla klienta. Lista obsługiwanych systemów operacyjnych klientów znajduje się w sekcji [Często zadawane pytania dotyczące połączeń typu punkt-lokacja](#point-to-site-faq).

### <a name="generate-and-install-a-vpn-client-configuration-package"></a>Generowanie i instalowanie pakietu konfiguracji klienta sieci VPN

1. W witrynie Azure Portal na stronie **Przegląd** sieci wirtualnej wybierz grafikę klienta w obszarze **Połączenia VPN**, aby otworzyć stronę **Połączenie sieci VPN typu punkt-lokacja**.

2. Na stronie **Połączenie sieci VPN typu punkt-lokacja** wybierz pakiet pobierania, który odpowiada systemowi operacyjnemu klienta, w którym zostanie zainstalowany:

   * Dla klientów 64-bitowych wybierz pakiet **Klient sieci VPN (64-bitowy)**.
   * Dla klientów 32-bitowych wybierz pakiet **Klient sieci VPN (32-bitowy)**.

   ![Pobieranie pakietu konfiguracyjnego klienta VPN](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png)

3. Gdy pakiet zostanie wygenerowany, pobierz go, a następnie zainstaluj na komputerze klienckim. Jeśli zostanie wyświetlone okno podręczne SmartScreen, wybierz pozycję **Więcej informacji**, a następnie pozycję **Uruchom mimo to**. Możesz także zapisać pakiet w celu zainstalowania go na innych komputerach klienckich.

### <a name="install-a-client-certificate"></a>Instalowanie certyfikatu klienta

Aby utworzyć połączenie punkt-lokacja z komputerem klienckim innym niż użyty do wygenerowania certyfikatów klienta, należy zainstalować certyfikat klienta. Podczas instalowania certyfikatu klienta potrzebne jest hasło, które zostało utworzone w trakcie eksportowania certyfikatu klienta. Zazwyczaj można zainstalować certyfikat, klikając go dwukrotnie. Aby uzyskać więcej informacji, zobacz [Install an exported client certificate](vpn-gateway-certificates-point-to-site.md#install) (Instalowanie wyeksportowanego certyfikatu klienta).


## <a name="connect-to-your-vnet"></a>Nawiązywanie połączenia z siecią wirtualną

>[!NOTE]
>Musisz mieć uprawnienia administratora na komputerze klienckim, z którym nawiązujesz połączenie.
>
>

1. Aby nawiązać połączenie z siecią wirtualną, na komputerze klienckim przejdź do pozycji **połączenia sieci VPN** w Azure Portal i Znajdź utworzone połączenie sieci VPN. Połączenie sieci VPN ma taką samą nazwę jak sieć wirtualna. Wybierz pozycję **Połącz**. Jeśli pojawi się komunikat podręczny dotyczący certyfikatu, wybierz pozycję **Kontynuuj**, aby użyć podwyższonego poziomu uprawnień.

2. Na stronie stanu **Połączenie** wybierz przycisk **Połącz**, aby rozpocząć połączenie. Jeśli widzisz ekran **Wybierz certyfikat**, sprawdź, czy wyświetlany certyfikat klienta to ten, który ma zostać użyty do nawiązania połączenia. Jeśli nie, wybierz odpowiedni certyfikat z listy rozwijanej, a następnie wybierz przycisk **OK**.

3. Jeśli połączenie zostanie nawiązane, zobaczysz powiadomienie **Połączono**.


### <a name="troubleshooting-p2s-connections"></a>Rozwiązywanie problemów dotyczących połączeń typu punkt-lokacja

[!INCLUDE [verify-client-certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

## <a name="verify-the-vpn-connection"></a>Sprawdzenie połączenia sieci VPN

1. Sprawdź, czy połączenie sieci VPN jest aktywne. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień na komputerze klienckim, a następnie uruchom polecenie **ipconfig/all**.
2. Przejrzyj wyniki. Zwróć uwagę na fakt, że otrzymany adres IP jest jednym z adresów z zakresu dla połączenia typu punkt-lokacja określonego podczas tworzenia sieci wirtualnej. Wyniki powinny być podobne do poniższego przykładu:

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

## <a name="connect-to-a-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-classic-include.md)]

## <a name="add-or-remove-trusted-root-certificates"></a>Dodawanie lub usuwanie zaufanych certyfikatów głównych

Zaufane certyfikaty główne można dodawać do platformy Azure lub z niej usuwać. Po usunięciu certyfikatu głównego klienci, którzy mają certyfikat wygenerowany na podstawie tego certyfikatu głównego, nie będą w stanie się uwierzytelnić i w związku z tym nie będą mogli nawiązywać połączeń. Jeśli chcesz, aby klienci mogli uwierzytelniać się i nawiązywać połączenia, musisz zainstalować nowy certyfikat klienta wygenerowany na podstawie certyfikatu głównego, który jest traktowany jako zaufany przez platformę Azure.

### <a name="to-add-a-trusted-root-certificate"></a>Aby dodać zaufany certyfikat główny

Na platformie Azure można dodać maksymalnie 20 plików cer zaufanego certyfikatu głównego. Aby uzyskać instrukcje, zobacz Przekazywanie pliku cer certyfikatu głównego.

### <a name="to-remove-a-trusted-root-certificate"></a>Usuwanie zaufanego certyfikatu głównego

1. W sekcji **Połączenia sieci VPN** na stronie danej sieci wirtualnej wybierz grafikę klientów, aby otworzyć stronę **Połączenie sieci VPN typu punkt-lokacja**.

   ![Klienci](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)

2. Na stronie **Połączenie sieci VPN typu punkt-lokacja** wybierz przycisk **Zarządzanie certyfikatami**, aby otworzyć stronę **Certyfikaty**.

   ![Strona Certyfikaty](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)

3. Na stronie **Certyfikaty** wybierz wielokropek obok certyfikatu, który chcesz usunąć, a następnie wybierz pozycję **Usuń**.

   ![Usuwanie certyfikatu głównego](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deleteroot.png)

## <a name="revoke-a-client-certificate"></a>Odwołanie certyfikatu klienta

W razie potrzeby możesz odwołać certyfikat klienta. Lista odwołania certyfikatów umożliwia dokonanie selektywnej odmowy połączenia punkt-lokacja w oparciu o indywidualne certyfikaty klienta. Różni się to od usuwania zaufanego certyfikatu głównego. Jeśli usuniesz plik cer zaufanego certyfikatu głównego z platformy Azure, spowoduje to odwołanie dostępu dla wszystkich certyfikatów klienta wygenerowanych lub podpisanych przez odwołany certyfikat główny. Odwołanie certyfikatu klienta zamiast certyfikatu głównego pozwala dalej używać innych certyfikatów wygenerowanych na podstawie certyfikatu głównego do uwierzytelniania połączeń punkt-lokacja.

Częstą praktyką jest użycie certyfikatu głównego do zarządzania dostępem na poziomach zespołu lub organizacji przy jednoczesnym korzystaniu z odwołanych certyfikatów klienta dla bardziej precyzyjnej kontroli dostępu poszczególnych użytkowników.

### <a name="to-revoke-a-client-certificate"></a>Aby odwołać certyfikat klienta

Certyfikat klienta można odwołać przez dodanie odcisku palca do listy odwołania.

1. Pobierz odcisk palca certyfikatu klienta. Aby uzyskać więcej informacji, zobacz [How to: Pobieranie odcisku palca certyfikatu](https://msdn.microsoft.com/library/ms734695.aspx).
2. Skopiuj informacje do edytora tekstu i usuń wszelkie spacje, tak aby powstał ciąg bez odstępów.
3. Przejdź do klasycznej sieci wirtualnej. Wybierz stronę **Połączenie sieci VPN typu punkt-lokacja**, a następnie wybierz przycisk **Zarządzanie certyfikatami**, aby otworzyć stronę **Certyfikaty**.
4. Wybierz pozycję **Lista odwołania**, aby otworzyć stronę **Lista odwołania**. 
5. Wybierz pozycję **Dodaj certyfikat**, aby otworzyć stronę **Dodawanie certyfikatu do listy odwołania**.
6. W obszarze **Odcisk palca** wklej odcisk palca certyfikatu jako jeden ciągły wiersz tekstu bez odstępów. Wybierz przycisk **OK**, aby zakończyć.

Po zakończeniu aktualizowania nie będzie można już używać certyfikatu do nawiązywania połączeń. Klienci, którzy spróbują połączyć się za pomocą tego certyfikatu, zobaczą komunikat informujący o tym, że certyfikat nie jest już ważny.

## <a name="point-to-site-faq"></a>Często zadawane pytania dotyczące połączeń punkt-lokacja

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-point-to-site-classic-include.md)]

## <a name="next-steps"></a>Następne kroki

- Po zakończeniu procesu nawiązywania połączenia można dodać maszyny wirtualne do sieci wirtualnych. Aby uzyskać więcej informacji, zobacz [Virtual Machines](https://docs.microsoft.com/azure/) (Maszyny wirtualne). 

- Aby dowiedzieć się więcej o sieci i maszynach wirtualnych z systemem Linux, zobacz [Azure and Linux VM network overview](../virtual-machines/linux/network-overview.md) (Omówienie sieci maszyny wirtualnej z systemem Linux i platformy Azure).

- Aby uzyskać informacje dotyczące rozwiązywania problemów z połączeniem typu punkt-lokacja, zobacz [Troubleshoot Azure point-to-site connections (Rozwiązywanie problemów z połączeniami typu punkt-lokacja na platformie Azure)](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
