---
title: Pobieranie skryptów konfiguracji urządzenia sieci VPN dla połączeń sieci VPN S2S
description: W tym artykule omówiono pobieranie skryptów konfiguracji urządzeń sieci VPN dla połączeń sieci VPN S2S z bramami sieci VPN platformy Azure przy użyciu Azure Resource Manager.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: d2593c656e17310c5afb4a897b94cc8c12a618f0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98879498"
---
# <a name="download-vpn-device-configuration-scripts-for-s2s-vpn-connections"></a>Pobieranie skryptów konfiguracji urządzenia sieci VPN dla połączeń sieci VPN S2S

W tym artykule omówiono pobieranie skryptów konfiguracji urządzeń sieci VPN dla połączeń sieci VPN S2S z bramami sieci VPN platformy Azure przy użyciu Azure Resource Manager. Na poniższym diagramie przedstawiono przepływ pracy wysokiego poziomu.

![Pobierz — skrypt](./media/vpn-gateway-download-vpndevicescript/downloaddevicescript.png)

Następujące urządzenia mają dostępne skrypty:

[!INCLUDE [scripts](../../includes/vpn-gateway-device-configuration-scripts.md)]

## <a name="about-vpn-device-configuration-scripts"></a><a name="about"></a>Informacje o skryptach konfiguracji urządzeń sieci VPN

Połączenie sieci VPN między lokalizacjami składa się z bramy sieci VPN platformy Azure, lokalnego urządzenia sieci VPN oraz tunelu VPN S2S IPsec łączącego dwa. Typowy przepływ pracy obejmuje następujące kroki:

1. Tworzenie i Konfigurowanie usługi Azure VPN Gateway (Brama sieci wirtualnej)
2. Utwórz i skonfiguruj bramę sieci lokalnej platformy Azure, która reprezentuje sieć lokalną i urządzenie sieci VPN
3. Tworzenie i Konfigurowanie połączenia sieci VPN platformy Azure między bramą sieci VPN platformy Azure i bramą sieć lokalna
4. Skonfiguruj lokalne urządzenie sieci VPN reprezentowane przez bramę sieci lokalnej w celu ustalenia rzeczywistego tunelu sieci VPN S2S przy użyciu bramy sieci VPN platformy Azure

Kroki od 1 do 3 można wykonać przy użyciu witryny Azure [Portal](./tutorial-site-to-site-portal.md), [programu PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)lub [interfejsu wiersza polecenia](vpn-gateway-howto-site-to-site-resource-manager-cli.md). Ostatni krok polega na konfigurowaniu lokalnych urządzeń sieci VPN poza platformą Azure. Ta funkcja umożliwia pobranie skryptu konfiguracji dla urządzenia sieci VPN z odpowiednimi wartościami bramy sieci VPN platformy Azure, sieci wirtualnej i lokalnych prefiksów adresów sieciowych oraz właściwości połączenia sieci VPN itp. Możesz użyć skryptu jako punktu wyjścia lub zastosować skrypt bezpośrednio do lokalnych urządzeń sieci VPN za pośrednictwem konsoli Konfiguracja.

> [!IMPORTANT]
> * Składnia dla każdego skryptu konfiguracji urządzenia sieci VPN jest różna i w dużym stopniu zależy od modeli i wersji oprogramowania układowego. Zwróć szczególną uwagę na model urządzenia i informacje o wersji dotyczące dostępnych szablonów.
> * Niektóre wartości parametrów muszą być unikatowe na urządzeniu i nie można ich określić bez uzyskiwania dostępu do urządzenia. Skrypty konfiguracyjne generowane przez platformę Azure wstępnie wypełniają te wartości, ale należy upewnić się, że podane wartości są prawidłowe na urządzeniu. Przykłady:
>    * Numery interfejsów
>    * Numery list kontroli dostępu
>    * Nazwy lub numery zasad itd.
> * Poszukaj słowa kluczowego "**replace**" osadzonego w skrypcie, aby znaleźć parametry, które należy zweryfikować przed zastosowaniem skryptu.
> * Niektóre szablony zawierają sekcję "**czyszczenie**", którą można zastosować, aby usunąć konfiguracje. Sekcje oczyszczania są domyślnie oznaczone jako komentarze.

## <a name="download-the-configuration-script-from-azure-portal"></a>Pobierz skrypt konfiguracji z Azure Portal

Utwórz bramę sieci VPN platformy Azure, bramę sieć lokalną i zasób połączenia łączący te dwa. Poniższa Strona przeprowadzi Cię przez poszczególne kroki:

* [Tworzenie połączenia typu lokacja-lokacja w witrynie Azure Portal](./tutorial-site-to-site-portal.md)

Po utworzeniu zasobu połączenia postępuj zgodnie z poniższymi instrukcjami, aby pobrać skrypty konfiguracji urządzenia sieci VPN:

1. W przeglądarce przejdź do [Azure Portal](https://portal.azure.com) i, jeśli to konieczne, zaloguj się przy użyciu konta platformy Azure
2. Przejdź do utworzonego zasobu połączenia. Listę wszystkich zasobów połączeń można znaleźć, klikając pozycję "wszystkie usługi", a następnie "sieci" i "połączenia".

    ![Lista połączeń](./media/vpn-gateway-download-vpndevicescript/connectionlist.png)

3. Kliknij połączenie, które chcesz skonfigurować.

    ![połączenie — Omówienie](./media/vpn-gateway-download-vpndevicescript/connectionoverview.png)

4. Kliknij link "Pobierz konfigurację" jako wyróżniony czerwono na stronie Przegląd połączenia. spowoduje to otwarcie strony "Pobieranie konfiguracji".

    ![Pobieranie-skrypt-1](./media/vpn-gateway-download-vpndevicescript/downloadscript-1.png)

5. Wybierz rodzinę modelu i wersję oprogramowania układowego dla urządzenia sieci VPN, a następnie kliknij przycisk "Pobierz konfigurację".

    ![download66-Script-2](./media/vpn-gateway-download-vpndevicescript/downloadscript-2.PNG)

6. Zostanie wyświetlony monit o zapisanie pobranego skryptu (plik tekstowy) z przeglądarki.
7. Po pobraniu skryptu konfiguracji Otwórz go za pomocą edytora tekstów i wyszukaj słowo kluczowe "REPLACE", aby zidentyfikować i sprawdzić parametry, które mogą być zastąpione.

    ![Edycja — skrypt](./media/vpn-gateway-download-vpndevicescript/editscript.png)

## <a name="download-the-configuration-script-using-azure-powershell"></a>Pobierz skrypt konfiguracji przy użyciu Azure PowerShell



Możesz również pobrać skrypt konfiguracji przy użyciu Azure PowerShell, jak pokazano w następującym przykładzie:

```azurepowershell-interactive
$RG          = "TestRG1"
$GWName      = "VNet1GW"
$Connection  = "VNet1toSite1"

# List the available VPN device models and versions
Get-AzVirtualNetworkGatewaySupportedVpnDevice -Name $GWName -ResourceGroupName $RG

# Download the configuration script for the connection
Get-AzVirtualNetworkGatewayConnectionVpnDeviceConfigScript -Name $Connection -ResourceGroupName $RG -DeviceVendor Juniper -DeviceFamily Juniper_SRX_GA -FirmwareVersion Juniper_SRX_12.x_GA
```

## <a name="apply-the-configuration-script-to-your-vpn-device"></a>Zastosuj skrypt konfiguracji do urządzenia sieci VPN

Po pobraniu i sprawdzeniu poprawności skryptu konfiguracji następnym krokiem jest zastosowanie skryptu do urządzenia sieci VPN. Rzeczywista procedura różni się w zależności od modelu i modeli urządzeń sieci VPN. Zapoznaj się z instrukcjami dotyczącymi operacji lub stron instrukcji dla urządzeń sieci VPN.

## <a name="next-steps"></a>Następne kroki

Kontynuuj Konfigurowanie [połączenia lokacja-lokacja](./tutorial-site-to-site-portal.md).