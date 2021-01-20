---
title: Tworzenie sieci VPN między platformą Azure i AWS przy użyciu rozwiązań zarządzanych
description: Jak utworzyć połączenie sieci VPN między platformą Azure i AWS przy użyciu rozwiązań zarządzanych, a nie maszyn wirtualnych lub urządzeń.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 01/15/2021
ms.author: cherylmc
ms.openlocfilehash: c1bc263ca67a7d05dbb0d40bb07ba1ae43c2db5c
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98605643"
---
# <a name="create-a-vpn-connection-between-azure-and-aws-using-managed-solutions"></a>Utwórz połączenie sieci VPN między platformą Azure i AWS przy użyciu rozwiązań zarządzanych

Połączenie między platformą Azure i usługą AWS można nawiązać przy użyciu rozwiązań zarządzanych. Wcześniej było wymagane użycie urządzenia lub maszyny wirtualnej działającej jako obiekt odpowiadający. Teraz możesz połączyć wirtualną bramę prywatną AWS z platformą Azure VPN Gateway bezpośrednio bez konieczności zarządzania zasobami IaaS, takimi jak maszyny wirtualne. Ten artykuł pomaga utworzyć połączenie sieci VPN między platformą Azure i usługą AWS przy użyciu tylko rozwiązań zarządzanych.

:::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/diagram.png" alt-text="Diagram architektury":::

## <a name="configure-azure"></a>Konfigurowanie platformy Azure

### <a name="configure-a-virtual-network"></a>Konfigurowanie sieci wirtualnej

Skonfiguruj sieć wirtualną. Aby uzyskać instrukcje, zobacz [Virtual Network przewodnika Szybki Start](../virtual-network/quick-create-portal.md).

W tym artykule są używane następujące przykładowe wartości:

* **Grupa zasobów:** RG-Azure-AWS
* **Region:** Wschodnie stany USA
* **Nazwa sieci wirtualnej:** Sieć wirtualna — Azure
* **Przestrzeń adresów IPv4:** 172.10.0.0/16
* **Nazwa podsieci:** Subnet-01
* **Zakres adresów podsieci:** 172.10.1.0/24

### <a name="create-a-vpn-gateway"></a>Tworzenie bramy sieci VPN

Utwórz bramę sieci VPN dla sieci wirtualnej. Aby uzyskać instrukcje, zobacz [Samouczek: Tworzenie bramy sieci VPN i zarządzanie nią](tutorial-create-gateway-portal.md).

W tym artykule są używane następujące przykładowe wartości i ustawienia:

* **Typ bramy:** VPN
* **Typ sieci VPN:** oparta na trasach
* **Jednostka SKU:** VpnGw1
* **Generowanie:** Generacja 1
* **Sieć wirtualna:** Musi być siecią wirtualną, dla której chcesz utworzyć bramę.
* **Zakres adresów podsieci bramy:** 172.10.0.0/27
* **Publiczny adres IP:** Utwórz nowy
* **Nazwa publicznego adresu IP:** PIP-VPN-Azure-AWS
* **Włącz tryb aktywny-aktywny:** Wyłącza
* **Konfigurowanie protokołu BGP:** Wyłącza

Przykład:

:::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/summary.png" alt-text="Podsumowanie bramy sieci wirtualnej":::

## <a name="configure-aws"></a>Konfigurowanie AWS

1. Utwórz wirtualną chmurę prywatną (VPC).

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-vpc.png" alt-text="Utwórz informacje VPC":::

1. Utwórz podsieć wewnątrz VPC (Sieć wirtualna).

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-subnet-vpc.png" alt-text="Utwórz podsieć":::

1. Utwórz bramę klienta, która wskazuje publiczny adres IP usługi Azure VPN Gateway. **Brama klienta** to zasób AWS zawierający informacje o AWS urządzenia bramy klienta, które w tym przypadku jest VPN Gateway platformy Azure.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-customer-gw.png" alt-text="Utwórz bramę klienta":::

1. Utwórz wirtualną bramę prywatną.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-vpg.png" alt-text="Tworzenie wirtualnej bramy prywatnej":::

1. Dołącz wirtualną bramę prywatną do VPC.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attach-vpg.png" alt-text="Dołącz VPG do VPC":::

1. Wybierz VPC.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attaching-vpg.png" alt-text="Dołącz":::

1. Utwórz połączenie sieci VPN typu lokacja-lokacja.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-vpn-connection.png" alt-text="Utwórz połączenie sieci VPN":::

1. Ustaw opcję routingu na **statyczną** i wskaż prefiks podsieć Azure-01 **(172.10.1.0/24).**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/set-static-route.png" alt-text="Ustawianie trasy statycznej":::

1. Po wprowadzeniu opcji **Utwórz** połączenie.

1. Pobierz plik konfiguracji. Aby pobrać poprawną konfigurację, Zmień dostawcę, platformę i oprogramowanie na **Ogólne**, ponieważ platforma Azure nie jest prawidłową opcją.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/download-config.png" alt-text="Pobierz konfigurację":::

1. Plik konfiguracji zawiera klucz wstępny i publiczny adres IP dla każdego z dwóch tuneli IPsec utworzonych przez AWS.

   **Tunel 1**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-1.png" alt-text="Tunel 1":::

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-1-config.png" alt-text="Konfiguracja tunelu 1":::

   **Tunel 2**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-2.png" alt-text="Tunel 2":::

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-2-config.png" alt-text="Konfiguracja tunelu 2":::

1. Po utworzeniu tuneli zobaczysz coś podobnego do tego przykładu.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-connection-details.png" alt-text="Szczegóły połączenia sieci VPN AWS":::

## <a name="create-local-network-gateway"></a>Utwórz bramę sieci lokalnej

Na platformie Azure Brama sieci lokalnej jest zasobem platformy Azure, który zwykle reprezentuje lokalizację lokalną. Jest on wypełniany informacjami używanymi do nawiązywania połączenia z lokalnym urządzeniem sieci VPN. Jednak w tej konfiguracji Brama sieci lokalnej jest tworzona i wypełniana przy użyciu informacji o połączeniu z wirtualną bramą prywatną AWS. Aby uzyskać więcej informacji na temat bram sieci lokalnej platformy Azure, zobacz [Ustawienia usługi azure VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md#lng).

Utwórz bramę sieci lokalnej na platformie Azure. Aby uzyskać instrukcje, zobacz [Tworzenie bramy sieci lokalnej](tutorial-site-to-site-portal.md#LocalNetworkGateway).

Określ następujące wartości:

* **Nazwa:** W tym przykładzie używamy LNG-Azure-AWS.
* **Punkt końcowy:** Adres IP
* **Adres IP:** Publiczny adres IP z wirtualnej bramy prywatnej AWS i prefiks CIDR VPC. Publiczny adres IP można znaleźć w pliku konfiguracji, który został wcześniej pobrany.

AWS tworzy dwa tunele IPsec w celu zapewnienia wysokiej dostępności. Poniższy przykład pokazuje publiczny adres IP z #1 tunelu IPsec.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/local-network-gateway.png" alt-text="Brama sieci lokalnej":::

## <a name="create-vpn-connection"></a>Utwórz połączenie sieci VPN

W tej sekcji utworzysz połączenie sieci VPN między bramą sieci wirtualnej platformy Azure i bramą AWS.

1. Utwórz połączenie platformy Azure. Aby uzyskać instrukcje dotyczące tworzenia połączenia, zobacz [Tworzenie połączenia sieci VPN](tutorial-site-to-site-portal.md#CreateConnection).

   W poniższym przykładzie klucz współużytkowany został pobrany z pobranego wcześniej pliku konfiguracji. W tym przykładzie użyjemy wartości dla #1 tunelu IPsec utworzonych przez AWS i opisanych w pliku konfiguracji.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-connection.png" alt-text="Obiekt połączenia platformy Azure":::

1. Wyświetl połączenie. Po kilku minutach połączenie zostanie nawiązane.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/connection-established.png" alt-text="Połączenie robocze":::

1. Sprawdź, czy #1 tunelu IPsec **AWS.**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-connection-established.png" alt-text="Weryfikowanie tunelu AWS":::

1. Edytuj tabelę tras skojarzoną z VPC.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/edit-aws-route.png" alt-text="Edytowanie trasy":::

1. Dodaj trasę do podsieci platformy Azure. Ta trasa będzie przenoszona przez bramę VPC.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/save-aws-route.png" alt-text="Zapisywanie konfiguracji trasy":::

## <a name="configure-second-connection"></a>Skonfiguruj drugie połączenie

W tej sekcji utworzysz drugie połączenie w celu zapewnienia wysokiej dostępności.

1. Utwórz kolejną bramę sieci lokalnej, która wskazuje publiczny adres IP #2 tunelu IPsec na AWS. Jest to brama gotowości.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-lng-standby.png" alt-text="Tworzenie bramy sieci lokalnej":::

1. Utwórz drugie połączenie sieci VPN z platformy Azure do AWS.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-connection-standby.png" alt-text="Utwórz rezerwowe połączenie bramy sieci lokalnej":::

1. Wyświetlanie połączeń bramy sieci VPN platformy Azure.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-tunnels.png" alt-text="Stan połączenia platformy Azure":::

1. Wyświetlanie połączeń AWS.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-tunnels.png" alt-text="Stan połączenia usługi AWS":::

Połączenia są teraz nawiązane.

## <a name="test-connections"></a>Testuj połączenia

1. Dodaj bramę internetową do VPC na AWS. Brama internetowa jest połączeniem logicznym między siecią Amazon VPN a Internetem. Ten zasób umożliwia nawiązanie połączenia przez testową maszynę wirtualną z publicznego adresu IP AWS za pośrednictwem Internetu. Ten zasób nie jest wymagany w przypadku połączenia sieci VPN. Używamy go tylko do testowania.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-igw.png" alt-text="Utwórz bramę internetową":::

1. Wybierz pozycję **Dołącz do VPC**.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attach-igw.png" alt-text="Dołączanie bramy internetowej do VPC":::

1. Wybierz VPC i **Dołącz bramę internetową**.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attach-igw-2.png" alt-text="Dołącz bramę":::

1. Utwórz trasę, aby zezwolić na połączenia z **0.0.0.0/0** (Internet) przez bramę internetową.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/allow-internet-igw.png" alt-text="Konfigurowanie trasy za pomocą bramy":::

1. Na platformie Azure trasa jest tworzona automatycznie. Możesz sprawdzić trasę z maszyny wirtualnej platformy Azure, wybierając kolejno pozycje **vm > Networking > Network Interface > obowiązujące trasy**. Zobaczysz 2 trasy, 1 trasę dla każdego połączenia.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-effective-routes.png" alt-text="Sprawdź obowiązujące trasy":::

1. W przypadku maszyny wirtualnej z systemem Linux na platformie Azure środowisko jest podobne do poniższego przykładu.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-overview.png" alt-text="Omówienie platformy Azure na maszynie wirtualnej z systemem Linux":::

1. Na maszynie wirtualnej z systemem Linux w systemie AWS środowisko jest podobne do poniższego przykładu.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-overview.png" alt-text="AWS — Omówienie z maszyny wirtualnej z systemem Linux":::

1. Przetestuj łączność z poziomu maszyny wirtualnej platformy Azure.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-ping.png" alt-text="Test polecenia ping z platformy Azure":::

1. Przetestuj łączność z maszyny wirtualnej AWS.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-ping.png" alt-text="Test polecenia ping z AWS":::

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat obsługi AWS dla protokołu IKEv2, zobacz [artykuł AWS](https://aws.amazon.com/about-aws/whats-new/2019/02/aws-site-to-site-vpn-now-supports-ikev2/).
