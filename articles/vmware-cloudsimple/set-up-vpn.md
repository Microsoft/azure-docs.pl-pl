---
title: Rozwiązanie VMware firmy Azure przez CloudSimple — Konfigurowanie sieci VPN między lokalną i chmurą prywatną
description: Opisuje sposób konfigurowania połączenia sieci VPN typu lokacja-lokacja lub punkt-lokacja między siecią lokalną i chmurą prywatną CloudSimple
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b7a119b538ee07b961011bfa98d748f92cc76f0e
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899222"
---
# <a name="configure-a-vpn-connection-to-your-cloudsimple-private-cloud"></a>Skonfiguruj połączenie sieci VPN z chmurą prywatną CloudSimple

Bramy sieci VPN umożliwiają łączenie się z siecią CloudSimple z sieci lokalnej i zdalnie z komputera klienckiego.  W tym artykule znajdują się informacje dotyczące konfigurowania bram sieci VPN z portalu CloudSimple.  Połączenie sieci VPN między siecią lokalną a siecią CloudSimple zapewnia dostęp do programu vCenter i obciążeń w chmurze prywatnej. CloudSimple obsługuje zarówno bramę sieci VPN typu punkt-lokacja, jak i bramy sieci VPN typu lokacja-lokacja.

## <a name="vpn-gateway-types"></a>Typy bram sieci VPN

* Połączenie **sieci VPN typu punkt-lokacja** jest najprostszym sposobem nawiązywania połączenia z chmurą prywatną z komputera. Aby zdalnie nawiązać połączenie z chmurą prywatną, Użyj połączenia sieci VPN typu punkt-lokacja.
* Połączenie **sieci VPN typu lokacja-lokacja** umożliwia skonfigurowanie obciążeń chmur prywatnych w celu uzyskania dostępu do usług lokalnych. Możesz również użyć Active Directory lokalnego jako źródła tożsamości do uwierzytelniania w chmurze prywatnej programu vCenter.  Obecnie jest obsługiwany **oparty na zasadach typ sieci VPN** .

W regionie można utworzyć jedną bramę sieci VPN typu lokacja-lokacja i jedną bramę sieci VPN typu punkt-lokacja.

## <a name="point-to-site-vpn"></a>Sieć VPN typu punkt-lokacja

Aby utworzyć bramę sieci VPN typu punkt-lokacja, zobacz [Tworzenie bramy sieci VPN typu punkt-lokacja](vpn-gateway.md#create-point-to-site-vpn-gateway).

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>Nawiązywanie połączenia z usługą CloudSimple przy użyciu sieci VPN typu punkt-lokacja

Klient sieci VPN jest wymagany do nawiązywania połączenia z usługą CloudSimple z komputera.  Pobierz [klienta OpenVPN](https://openvpn.net/community-downloads/) dla systemu Windows lub [lepkości](https://www.sparklabs.com/viscosity/download/) dla macOS i OS X.

1. Uruchom Portal CloudSimple i wybierz pozycję **Sieć**.
2. Wybierz **VPN Gateway**.
3. Na liście bram sieci VPN kliknij pozycję Brama sieci VPN typu punkt-lokacja.
4. Wybierz pozycję **Użytkownicy**.
5. Kliknij pozycję **Pobierz moją konfigurację sieci VPN**

    ![Pobieranie konfiguracji sieci VPN](media/download-p2s-vpn-configuration.png)

6. Importowanie konfiguracji na kliencie sieci VPN

    * Instrukcje dotyczące [importowania konfiguracji w kliencie systemu Windows](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Instrukcje dotyczące [importowania konfiguracji w systemie macOS lub OS X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Nawiązywanie połączenia z bramą sieci VPN CloudSimple.

W poniższym przykładzie pokazano Importowanie połączenia za pomocą **klienta lepkości**.

#### <a name="import-connection-on-viscosity-client"></a>Importuj połączenie na kliencie z lepką

1. Wyodrębnij zawartość konfiguracji sieci VPN z pobranego pliku zip.

2. Otwórz na komputerze.

3. Kliknij **+** ikonę i wybierz pozycję **Importuj połączenie**  >  **z pliku**.

    ![Importuj konfigurację sieci VPN z pliku](media/import-p2s-vpn-config.png)

4. Wybierz plik konfiguracji OpenVPN (. ovpn) dla protokołu, którego chcesz użyć, a następnie kliknij przycisk **Otwórz**.

    ![Zrzut ekranu, który podświetla pliki konfiguracyjne OpenVPN, które można wybrać.](media/import-p2s-vpn-config-choose-ovpn.png)

Połączenie zostanie wyświetlone w menu lepkość.

#### <a name="connect-to-the-vpn"></a>Połącz się z siecią VPN

Aby nawiązać połączenie z siecią VPN za pomocą OpenVPNego, wybierz połączenie z menu. Ikona menu jest aktualizowana w celu wskazania, że połączenie zostało nawiązane.

![Zrzut ekranu przedstawiający stan łączności sieci VPN CloudSimple.](media/vis03.png)

### <a name="connecting-to-multiple-private-clouds"></a>Łączenie z wieloma chmurami prywatnymi

Połączenie sieci VPN typu punkt-lokacja rozwiązuje nazwy DNS pierwszej utworzonej chmury prywatnej. Aby uzyskać dostęp do innych chmur prywatnych, należy zaktualizować serwer DNS na kliencie sieci VPN.

1. Uruchom [Portal CloudSimple](access-cloudsimple-portal.md).

2. Przejdź do **zasobów**  >  **chmury prywatne** i wybierz chmurę prywatną, z którą chcesz nawiązać połączenie.

3. Na stronie **Podsumowanie** chmury prywatnej Skopiuj adres IP serwera DNS w chmurze prywatnej w obszarze **podstawowe informacje**.

    ![Serwery DNS w chmurze prywatnej](media/private-cloud-dns-server.png)

4. Kliknij prawym przyciskiem myszy ikonę lepkość na pasku systemowym komputera i wybierz pozycję **Preferencje**.

    ![VPN](media/vis00.png)

5. Wybierz połączenie sieci VPN CloudSimple.

    ![Połączenie sieci VPN](media/viscosity-client.png)

6. Kliknij przycisk **Edytuj** , aby zmienić właściwości połączenia.

    ![Edytowanie połączenia sieci VPN](media/viscosity-edit-connection.png)

7. Kliknij kartę **Sieć** , a następnie wprowadź adresy IP serwerów DNS w chmurze prywatnej oddzielone przecinkami lub spacjami i domeną jako ```cloudsimple.io``` .  Wybierz opcję **Ignoruj ustawienia DNS wysyłane przez serwer sieci VPN**.

    ![Sieci VPN](media/viscosity-edit-connection-networking.png)

> [!IMPORTANT]
> Aby nawiązać połączenie z pierwszą chmurą prywatną, usuń te ustawienia i Połącz się z serwerem sieci VPN.

## <a name="site-to-site-vpn"></a>Sieć VPN typu lokacja-lokacja

Aby utworzyć bramę sieci VPN typu lokacja-lokacja, zobacz [Tworzenie bramy sieci VPN typu lokacja-lokacja](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway).  Połączenia sieci VPN typu lokacja-lokacja z sieci lokalnej do chmury prywatnej zapewniają te korzyści.  

* Dostępność w chmurze prywatnej vCenter z dowolnej stacji roboczej w sieci lokalnej
* Używanie Active Directory lokalnego jako źródła tożsamości programu vCenter
* Wygodny transfer szablonów maszyn wirtualnych, obrazów ISO i innych plików z zasobów lokalnych do programu vCenter w chmurze prywatnej
* Dostępność obciążeń działających w chmurze prywatnej z sieci lokalnej

Aby skonfigurować lokalną bramę sieci VPN w trybie wysokiej dostępności, zobacz [Konfigurowanie połączenia sieci VPN o wysokiej dostępności](high-availability-vpn-connection.md).

> [!IMPORTANT]
>    1. Na urządzeniu sieci VPN ustaw wartość opcji ograniczania TCP = 1200. Lub jeśli urządzenia sieci VPN nie obsługują funkcji ograniczania, można zamiast tego ustawić jednostkę MTU w interfejsie tunelu na 1240 bajtów.
> 2. Po skonfigurowaniu sieci VPN typu lokacja-lokacja Prześlij dalej żądania DNS dla *. cloudsimple.io do serwerów DNS w chmurze prywatnej.  Postępuj zgodnie z instrukcjami w [lokalnej konfiguracji DNS](on-premises-dns-setup.md).
