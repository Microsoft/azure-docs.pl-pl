---
title: Tworzenie połączenia punkt-lokacja z platformą Azure w usłudze Azure Virtual WAN | Microsoft Docs
description: Z tego samouczka dowiesz się, jak utworzyć połączenie punkt-lokacja VPN z platformą Azure w usłudze Azure Virtual WAN.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 06/29/2020
ms.author: alzam
ms.openlocfilehash: 9c93ad0357011008c45b2898260a655509b02dc2
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/30/2020
ms.locfileid: "85560691"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>Samouczek: Tworzenie połączenia sieci VPN użytkownika przy użyciu wirtualnej sieci WAN platformy Azure

W tym samouczku pokazano, w jaki sposób przy użyciu usługi Azure Virtual WAN utworzyć połączenie z zasobami na platformie Azure za pośrednictwem połączenia sieci VPN protokołu IPsec/IKE (IKEv2) lub OpenVPN. Ten typ połączenia wymaga skonfigurowania klienta na komputerze klienckim. Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz [Omówienie wirtualnej sieci WAN](virtual-wan-about.md)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie sieci WAN
> * Tworzenie konfiguracji P2S
> * Tworzenie koncentratora
> * Określ serwery DNS
> * Pobieranie profilu klienta VPN
> * Wyświetlanie wirtualnej sieci WAN

![Diagram usługi Virtual WAN](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem konfiguracji sprawdź, czy są spełnione następujące kryteria:

* Masz sieć wirtualną, z którą chcesz nawiązać połączenie. Sprawdź, czy żadna z podsieci sieci lokalnych nie nakłada się na sieci wirtualne, z którymi chcesz nawiązać połączenie. Aby utworzyć sieć wirtualną w Azure Portal, zobacz [Przewodnik Szybki Start](../virtual-network/quick-create-portal.md).

* Twoja sieć wirtualna nie ma żadnych bram sieci wirtualnej. Jeśli sieć wirtualna ma bramę (VPN lub ExpressRoute), należy usunąć wszystkie bramy. Ta konfiguracja wymaga, aby w zamian były połączone sieci wirtualne z bramą wirtualnego centrum sieci WAN.

* Uzyskaj zakres adresów IP w regionie koncentratora. Centrum jest siecią wirtualną, która jest tworzona i używana przez wirtualną sieć WAN. Zakres adresów określony dla centrum nie może pokrywać się z żadną z istniejących sieci wirtualnych, z którymi się łączysz. Nie może również pokrywać się z zakresami adresów, z którymi łączysz się lokalnie. Jeśli nie znasz zakresów adresów IP znajdujących się w konfiguracji sieci lokalnej, koordynuj się z osobą, która może podać te szczegóły.

* Jeśli nie masz subskrypcji platformy Azure, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Tworzenie wirtualnej sieci WAN

Przejdź w przeglądarce do witryny [Azure Portal](https://portal.azure.com) i zaloguj się przy użyciu konta platformy Azure.

1. Przejdź do strony wirtualna sieć WAN. W portalu wybierz pozycję **+ Utwórz zasób**. W polu wyszukiwania wpisz **wirtualną sieć WAN** i wybierz pozycję **wprowadź**.
1. Z wyników wybierz pozycję **wirtualna sieć WAN** . Na stronie wirtualna sieć WAN wybierz pozycję **Utwórz** , aby otworzyć stronę tworzenie sieci WAN.
1. Na stronie **Tworzenie sieci WAN** na karcie **podstawowe** wypełnij następujące pola:

   ![Virtual WAN](./media/virtual-wan-point-to-site-portal/vwan.png)

   * **Subskrypcja** — wybierz subskrypcję, która ma być używana.
   * **Grupa zasobów** — Utwórz nową lub Użyj istniejącej.
   * **Lokalizacja grupy zasobów** — wybierz lokalizację zasobu z listy rozwijanej. Sieć WAN to zasób globalny i nie znajduje się w konkretnym regionie. Konieczne jest jednak wybranie regionu, aby łatwiej zarządzać utworzonym zasobem sieci WAN i go lokalizować.
   * **Nazwa** — wpisz nazwę, która ma zostać wywołana w sieci WAN.
   * **Typ:** Standardowa. W przypadku tworzenia podstawowej sieci WAN można utworzyć tylko podstawowe centrum. Centra podstawowe obsługują tylko połączenia sieci VPN typu lokacja-lokacja.
1. Po zakończeniu wypełniania pól wybierz pozycję **Przegląd + Utwórz**.
1. Po zakończeniu walidacji wybierz pozycję **Utwórz** , aby utworzyć wirtualną sieć WAN.

## <a name="create-a-p2s-configuration"></a><a name="p2sconfig"></a>Tworzenie konfiguracji P2S

Konfiguracja P2S definiuje parametry służące do łączenia z klientami zdalnymi.

1. Przejdź do pozycji **Wszystkie zasoby**.
1. Wybierz utworzoną wirtualną sieć WAN.
1. Wybierz pozycję **+ Utwórz konfigurację sieci VPN użytkownika** w górnej części strony, aby otworzyć stronę **Konfiguracja sieci VPN tworzenia nowego użytkownika** .

   :::image type="content" source="media/virtual-wan-point-to-site-portal/p2s1.jpg" alt-text="Konfiguracje sieci VPN użytkownika":::

1. Na stronie **Utwórz nową konfigurację sieci VPN użytkownika** wypełnij następujące pola:

   * **Nazwa konfiguracji** — jest to nazwa używana w celu odwoływania się do konfiguracji.
   * **Typ tunelu** — protokół używany w przypadku tunelu.
   * **Nazwa certyfikatu głównego** — opisowa nazwa certyfikatu.
   * **Dane certyfikatu publicznego** -Base-64 zakodowane dane certyfikatu X. 509.
  
1. Wybierz pozycję **Utwórz** , aby utworzyć konfigurację.

## <a name="create-hub-with-point-to-site-gateway"></a><a name="hub"></a>Tworzenie centrum z bramą punkt-lokacja

1. W obszarze wirtualne sieci WAN wybierz pozycję centra i wybierz pozycję **+ nowe centrum**.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/hub1.jpg" alt-text="nowe centrum":::

1. Na stronie Tworzenie wirtualnego centrum Wypełnij poniższe pola.

   * **Region** — wybierz region, w którym chcesz wdrożyć koncentrator wirtualny.
   * **Nazwa** — wprowadź nazwę, która ma być wywoływana z koncentratorem wirtualnym.
   * **Prywatna przestrzeń adresowa centrum** — zakres adresów centrum w notacji CIDR.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/hub2.jpg" alt-text="Tworzenie koncentratora wirtualnego":::

1. Na karcie punkt-lokacja wykonaj następujące pola:

   * **Jednostki skalowania bramy** , które reprezentują zagregowaną pojemność bramy sieci VPN użytkownika.
   * **Wskaż konfigurację lokacji** , która została utworzona w poprzednim kroku.
   * **Pula adresów klienta** — dla użytkowników zdalnych.
   * **Niestandardowy adres IP serwera DNS**.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/hub-with-p2s.png" alt-text="centrum z punktem do lokacji":::

1. Wybierz pozycję **Przegląd + utwórz**.
1. Na stronie **Walidacja została przeniesiona** wybierz pozycję **Utwórz**.

## <a name="specify-dns-server"></a><a name="dns"></a>Określ serwer DNS

Wirtualne bramy sieci VPN użytkownika sieci WAN umożliwiają określanie maksymalnie 5 serwerów DNS. Można to skonfigurować w trakcie procesu tworzenia centrum lub zmodyfikować go w późniejszym czasie. Aby to zrobić, zlokalizuj koncentrator wirtualny. W obszarze **Sieć VPN użytkownika (wskaż lokację)** kliknij pozycję Konfiguruj i wprowadź adresy IP serwera DNS w polu tekstowym **niestandardowe serwery DNS** .

   :::image type="content" source="media/virtual-wan-point-to-site-portal/custom-dns.png" alt-text="niestandardowy serwer DNS" lightbox="media/virtual-wan-point-to-site-portal/custom-dns-expand.png":::

## <a name="download-vpn-profile"></a><a name="download"></a>Pobieranie profilu sieci VPN

Użyj profilu sieci VPN, aby skonfigurować klientów.

1. Na stronie wirtualnej sieci WAN wybierz pozycję **konfiguracje sieci VPN użytkownika**.
2. W górnej części strony wybierz pozycję **Pobierz konfigurację sieci VPN użytkownika**. Pobieranie konfiguracji na poziomie sieci WAN zapewnia wbudowany profil sieci VPN oparty na Traffic Manager. Aby uzyskać więcej informacji na temat profilów globalnych lub profilów opartych na centrum, zobacz te [Profile centrum](https://docs.microsoft.com/azure/virtual-wan/global-hub-profile).   Scenariusze trybu failover są uproszczone przy użyciu profilu globalnego.

   Jeśli z jakiegoś powodu centrum jest niedostępne, wbudowane zarządzanie ruchem udostępnianym przez usługę zapewnia łączność za pośrednictwem innego centrum z zasobami platformy Azure dla użytkowników typu punkt-lokacja. Można zawsze pobrać konfigurację sieci VPN specyficzną dla centrum, przechodząc do określonego centrum. W obszarze **Sieć VPN użytkownika (wskaż lokację)** Pobierz profil **sieci VPN użytkownika** koncentratora wirtualnego.

1. Po zakończeniu tworzenia pliku możesz wybrać łącze, aby je pobrać.
1. Użyj pliku profilu, aby skonfigurować klientów sieci VPN.

### <a name="configure-user-vpn-clients"></a>Konfigurowanie klientów VPN użytkowników

Użyj pobranego profilu, aby skonfigurować klientów zdalnego dostępu. Procedury dla każdego systemu operacyjnego są różne, postępuj zgodnie z poniższymi instrukcjami:

#### <a name="microsoft-windows"></a>Microsoft Windows
##### <a name="openvpn"></a>OpenVPN

1. Pobierz i zainstaluj klienta OpenVPN z oficjalnej witryny internetowej.
1. Pobierz profil sieci VPN dla bramy. Można to zrobić na karcie konfiguracje sieci VPN użytkownika w Azure Portal lub polecenie New-AzureRmVpnClientConfiguration w programie PowerShell.
1. Rozpakuj profil. Otwórz plik konfiguracji vpnconfig.ovpn z folderu OpenVPN w programie Notatnik.
1. W sekcji certyfikatu klienta P2S wprowadź klucz publiczny certyfikatu klienta P2S w formacie base64. W przypadku certyfikatu w formacie PEM można po prostu otworzyć plik cer i skopiować klucz w formacie base64 znajdujący się pomiędzy nagłówkami certyfikatów. Aby uzyskać instrukcje, zobacz [Eksportowanie certyfikatu w celu uzyskania zakodowanego klucza publicznego.](certificates-point-to-site.md)
1. W sekcji klucza prywatnego wprowadź klucz prywatny certyfikatu klienta P2S w formacie base64. Aby uzyskać instrukcje, zobacz [Jak wyodrębnić klucz prywatny.](howto-openvpn-clients.md#windows)
1. Nie zmieniaj innych pól. Użyj konfiguracji wprowadzonej w danych wejściowych klienta, aby nawiązać połączenie z siecią VPN.
1. Skopiuj plik vpnconfig.ovpn do folderu C:\Program Files\OpenVPN\config.
1. Kliknij prawym przyciskiem myszy ikonę OpenVPN na pasku zadań i wybierz pozycję **Połącz**.

##### <a name="ikev2"></a>IKEv2

1. Wybierz pliki konfiguracji klienta sieci VPN, które odpowiadają architekturze komputera z systemem Windows. W przypadku 64-bitowej architektury procesora wybierz pakiet instalatora „VpnClientSetupAmd64”. W przypadku 32-bitowej architektury procesora wybierz pakiet instalatora „VpnClientSetupX86”.
1. Kliknij dwukrotnie pakiet, aby go zainstalować. Jeśli zobaczysz okno podręczne SmartScreen, wybierz pozycję **więcej informacji**, a następnie **Uruchom mimo wszystko**.
1. Na komputerze klienckim przejdź do **ustawień sieci** i wybierz pozycję **Sieć VPN**. Połączenie z siecią VPN zawiera nazwę sieci wirtualnej, z którą jest nawiązywane połączenie.
1. Zanim spróbujesz nawiązać połączenie, sprawdź, czy zainstalowano certyfikat klienta na komputerze klienckim. Certyfikat klienta jest wymagany w celu uwierzytelniania podczas korzystania z natywnego typu certyfikatu uwierzytelniania platformy Azure. Więcej informacji o generowaniu certyfikatów znajduje się w temacie [Generate Certificates](certificates-point-to-site.md). Informacje o sposobie instalowania certyfikatu klienta znajdują się w temacie [Install a Client Certificate (Instalowanie certyfikatu klienta](../vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md)).

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Wyświetlanie wirtualnej sieci WAN

1. Przejdź do wirtualnej sieci WAN.
1. Na stronie **Przegląd** każdy punkt na mapie reprezentuje centrum.
1. W sekcji **centra i połączenia** można wyświetlić stan centrum, lokację, region, stan połączenia sieci VPN oraz liczbę bajtów do i wychodzące.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Oczyszczanie zasobów

Gdy grupa zasobów i zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). Zastąp wartość „myResourceGroup” nazwą grupy zasobów, a następnie uruchom następujące polecenie programu PowerShell:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Virtual WAN, zobacz stronę [Omówienie usługi Virtual WAN](virtual-wan-about.md).
