---
title: Tworzenie połączenia punkt-lokacja z platformą Azure w usłudze Azure Virtual WAN | Microsoft Docs
description: Z tego samouczka dowiesz się, jak utworzyć połączenie punkt-lokacja VPN z platformą Azure w usłudze Azure Virtual WAN.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 04/16/2020
ms.author: alzam
ms.openlocfilehash: 11007bc39cb1112799c89afaf0ca670aa6760de6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81482128"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>Samouczek: Tworzenie połączenia sieci VPN użytkownika przy użyciu wirtualnej sieci WAN platformy Azure

W tym samouczku pokazano, w jaki sposób przy użyciu usługi Azure Virtual WAN utworzyć połączenie z zasobami na platformie Azure za pośrednictwem połączenia sieci VPN protokołu IPsec/IKE (IKEv2) lub OpenVPN. Ten typ połączenia wymaga skonfigurowania klienta na komputerze klienckim. Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz [Omówienie wirtualnej sieci WAN](virtual-wan-about.md)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie sieci WAN
> * Tworzenie koncentratora
> * Tworzenie konfiguracji P2S
> * Pobieranie profilu klienta VPN
> * Stosowanie konfiguracji P2S do koncentratora
> * Łączenie sieci wirtualnej z koncentratorem
> * Pobieranie i stosowanie konfiguracji klienta sieci VPN
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

1. Przejdź do strony wirtualna sieć WAN. W portalu kliknij pozycję **+Utwórz zasób**. W polu wyszukiwania wpisz **wirtualną sieć WAN** i wybierz pozycję Wprowadź.
2. Z wyników wybierz pozycję **wirtualna sieć WAN** . Na stronie wirtualna sieć WAN kliknij przycisk **Utwórz** , aby otworzyć stronę tworzenie sieci WAN.
3. Na stronie **Tworzenie sieci WAN** na karcie **podstawowe** wypełnij następujące pola:

   ![Wirtualna sieć WAN](./media/virtual-wan-point-to-site-portal/vwan.png)

   * **Subskrypcja** — wybierz subskrypcję, która ma być używana.
   * **Grupa zasobów** — Utwórz nową lub Użyj istniejącej.
   * **Lokalizacja grupy zasobów** — wybierz lokalizację zasobu z listy rozwijanej. Sieć WAN to zasób globalny i nie znajduje się w konkretnym regionie. Konieczne jest jednak wybranie regionu, aby łatwiej zarządzać utworzonym zasobem sieci WAN i go lokalizować.
   * **Nazwa** — wpisz nazwę, która ma zostać wywołana w sieci WAN.
   * **Typ:** Standardowa. W przypadku tworzenia podstawowej sieci WAN można utworzyć tylko podstawowe centrum. Centra podstawowe obsługują tylko połączenia sieci VPN typu lokacja-lokacja.
4. Po zakończeniu wypełniania pól wybierz pozycję **Przegląd + Utwórz**.
5. Po zakończeniu walidacji wybierz pozycję **Utwórz** , aby utworzyć wirtualną sieć WAN.

## <a name="create-an-empty-virtual-hub"></a><a name="hub"></a>Tworzenie pustego koncentratora wirtualnego

1. W obszarze wirtualnej sieci WAN wybierz pozycję centra i kliknij pozycję **+ nowe centrum** .

   ![nowa lokacja](media/virtual-wan-point-to-site-portal/hub1.jpg)
2. Na stronie Tworzenie wirtualnego centrum Wypełnij poniższe pola.

   **Region** — wybierz region, w którym chcesz wdrożyć koncentrator wirtualny.

   **Nazwa** — wprowadź nazwę, która ma być wywoływana z koncentratorem wirtualnym.

   **Prywatna przestrzeń adresowa centrum** — zakres adresów centrum w notacji CIDR.

   ![nowa lokacja](media/virtual-wan-point-to-site-portal/hub2.jpg)  
3. Kliknij przycisk **Przegląd + Utwórz**
4. Na stronie **Walidacja została przeniesiona** kliknij pozycję **Utwórz** .

## <a name="create-a-p2s-configuration"></a><a name="p2sconfig"></a>Tworzenie konfiguracji P2S

Konfiguracja P2S definiuje parametry służące do łączenia z klientami zdalnymi.

1. Przejdź do pozycji **Wszystkie zasoby**.
2. Kliknij utworzoną wirtualną sieć WAN.
3. Kliknij pozycję **+ Utwórz konfigurację sieci VPN użytkownika** w górnej części strony, aby otworzyć stronę **Konfiguracja sieci VPN tworzenia nowego użytkownika** .

   ![nowa lokacja](media/virtual-wan-point-to-site-portal/p2s1.jpg)
4. Na stronie **Utwórz nową konfigurację sieci VPN użytkownika** wypełnij następujące pola:

   **Nazwa konfiguracji** — jest to nazwa używana w celu odwoływania się do konfiguracji.

   **Typ tunelu** — protokół używany w przypadku tunelu.

   **Nazwa certyfikatu głównego** — opisowa nazwa certyfikatu.

   **Dane certyfikatu publicznego** -Base-64 zakodowane dane certyfikatu X. 509.
  
5. Kliknij przycisk **Utwórz**, aby utworzyć konfigurację.

## <a name="edit-hub-assignment"></a><a name="edit"></a>Edytowanie przypisania koncentratora

1. Przejdź do bloku **centra** w wirtualnej sieci WAN
2. Wybierz centrum, do którego chcesz skojarzyć konfigurację serwera sieci VPN, a następnie kliknij przycisk **...**

   ![nowa lokacja](media/virtual-wan-point-to-site-portal/p2s4.jpg)
3. Kliknij pozycję **Edytuj koncentrator wirtualny**.
4. Zaznacz pole wyboru **Uwzględnij bramę punkt-lokacja** i wybierz żądaną **jednostkę skalowania bramy** .

   ![nowa lokacja](media/virtual-wan-point-to-site-portal/p2s2.jpg)

W poniższej tabeli przedstawiono szczegółowe informacje o dostępnych **jednostkach skalowania**

| **Jednostka skalowania** | **Przepływność** | **Połączenia P2S** |
| --- | --- | --- |
| 1| 500 Mb/s | 500 |
| 2| 1 Gb/s | 500 |
| 3| 1,5 GB/s | 500 |
| 4| 2 Gb/s | 1000 |
| 5| 2,5 GB/s | 1000 |
| 6| 3 GB/s | 1000 |
| 7| 3,5 GB/s | 5000 |
| 8| 4 GB/s | 5000 |
| 9| 4,5 GB/s | 5000 |
| 10| 5 Gb/s | 5000 |
| 11| 5,5 GB/s | 5000 |
| 12| 6 GB/s | 5000 |
| 13| 6,5 GB/s | 10 000 |
| 14| 7 GB/s | 10 000 |
| 15| 7,5 GB/s | 10 000 |
| 16| 8 GB/s | 10 000 |
| 17| 8,5 GB/s | 10 000 |
| 18| 9 GB/s | 10 000 |
| 19| 9,5 GB/s | 10 000 |
| 20| 10 Gb/s | 10 000 |

5. Wprowadź **pulę adresów** , z której będą przypisywane adresy IP klientów sieci VPN.
6. Kliknij przycisk **Potwierdź**
7. Wykonanie operacji może potrwać do 30 minut.

## <a name="download-vpn-profile"></a><a name="download"></a>Pobieranie profilu sieci VPN

Użyj profilu sieci VPN, aby skonfigurować klientów.

1. Na stronie wirtualnej sieci WAN kliknij pozycję **konfiguracje sieci VPN użytkownika**.
2. W górnej części strony kliknij pozycję **Pobierz konfigurację sieci VPN użytkownika**.
3. Po zakończeniu tworzenia pliku możesz kliknąć link, aby go pobrać.
4. Użyj pliku profilu, aby skonfigurować klientów sieci VPN.

### <a name="configure-user-vpn-clients"></a>Konfigurowanie klientów VPN użytkowników
Użyj pobranego profilu, aby skonfigurować klientów zdalnego dostępu. Procedury dla każdego systemu operacyjnego są różne, postępuj zgodnie z poniższymi instrukcjami:

#### <a name="microsoft-windows"></a>Microsoft Windows
##### <a name="openvpn"></a>OpenVPN

1. Pobierz i zainstaluj klienta OpenVPN z oficjalnej witryny internetowej.
2. Pobierz profil sieci VPN dla bramy. Można to zrobić na karcie konfiguracje sieci VPN użytkownika w Azure Portal lub polecenie New-AzureRmVpnClientConfiguration w programie PowerShell.
3. Rozpakuj profil. Otwórz plik konfiguracji vpnconfig.ovpn z folderu OpenVPN w programie Notatnik.
4. W sekcji certyfikatu klienta P2S wprowadź klucz publiczny certyfikatu klienta P2S w formacie base64. W przypadku certyfikatu w formacie PEM można po prostu otworzyć plik cer i skopiować klucz w formacie base64 znajdujący się pomiędzy nagłówkami certyfikatów. Aby uzyskać instrukcje, zobacz [Eksportowanie certyfikatu w celu uzyskania zakodowanego klucza publicznego.](certificates-point-to-site.md)
5. W sekcji klucza prywatnego wprowadź klucz prywatny certyfikatu klienta P2S w formacie base64. Aby uzyskać instrukcje, zobacz [Jak wyodrębnić klucz prywatny.](howto-openvpn-clients.md#windows)
6. Nie zmieniaj innych pól. Użyj konfiguracji wprowadzonej w danych wejściowych klienta, aby nawiązać połączenie z siecią VPN.
7. Skopiuj plik vpnconfig.ovpn do folderu C:\Program Files\OpenVPN\config.
8. Kliknij prawym przyciskiem myszy ikonę OpenVPN na pasku zadań i kliknij przycisk Połącz.

##### <a name="ikev2"></a>IKEv2

1. Wybierz pliki konfiguracji klienta sieci VPN, które odpowiadają architekturze komputera z systemem Windows. W przypadku 64-bitowej architektury procesora wybierz pakiet instalatora „VpnClientSetupAmd64”. W przypadku 32-bitowej architektury procesora wybierz pakiet instalatora „VpnClientSetupX86”.
2. Kliknij dwukrotnie pakiet, aby go zainstalować. Jeśli zostanie wyświetlone okno podręczne SmartScreen, kliknij pozycję Więcej informacji, a następnie pozycję Uruchom mimo to.
3. Na komputerze klienckim przejdź do obszaru Ustawienia sieci i kliknij pozycję Sieć VPN. Połączenie z siecią VPN zawiera nazwę sieci wirtualnej, z którą jest nawiązywane połączenie.
4. Zanim spróbujesz nawiązać połączenie, sprawdź, czy zainstalowano certyfikat klienta na komputerze klienckim. Certyfikat klienta jest wymagany w celu uwierzytelniania podczas korzystania z natywnego typu certyfikatu uwierzytelniania platformy Azure. Więcej informacji o generowaniu certyfikatów znajduje się w temacie [Generate Certificates](certificates-point-to-site.md). Informacje o sposobie instalowania certyfikatu klienta znajdują się w temacie [Install a Client Certificate (Instalowanie certyfikatu klienta](../vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md)).

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Wyświetlanie wirtualnej sieci WAN

1. Przejdź do wirtualnej sieci WAN.
2. Na stronie Przegląd każdy punkt na mapie przedstawia koncentrator.
3. W sekcji dotyczącej koncentratorów i połączeń możesz wyświetlić stan koncentratora, lokację, region, stan połączenia sieci VPN oraz bajty przychodzące i wychodzące.


## <a name="clean-up-resources"></a><a name="cleanup"></a>Oczyszczanie zasobów

Gdy grupa zasobów i zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). Zastąp wartość „myResourceGroup” nazwą grupy zasobów, a następnie uruchom następujące polecenie programu PowerShell:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Virtual WAN, zobacz stronę [Omówienie usługi Virtual WAN](virtual-wan-about.md).
