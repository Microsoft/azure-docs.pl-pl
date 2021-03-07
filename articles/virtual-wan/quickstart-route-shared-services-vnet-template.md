---
title: 'Szybki Start: kierowanie do usług udostępnionych przy użyciu szablonu ARM'
titleSuffix: Azure Virtual WAN
description: W tym przewodniku szybki start przedstawiono sposób konfigurowania tras w celu uzyskania dostępu do udostępnionej sieci wirtualnej usługi z obciążeniem, które ma być używane przez każdą sieć wirtualną i gałąź do uzyskiwania dostępu przy użyciu szablonu Azure Resource Manager (szablon ARM).
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: quickstart
ms.date: 03/05/2021
ms.author: cherylmc
ms.custom: subject-armqs
ms.openlocfilehash: f325dd445e778bf03049d2c9e2e00fed7a427ccf
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102443956"
---
# <a name="quickstart-route-to-shared-services-vnets-using-an-arm-template"></a>Szybki Start: kierowanie do usług udostępnionych sieci wirtualnych przy użyciu szablonu ARM

W tym przewodniku szybki start opisano, jak używać szablonu Azure Resource Manager (szablon ARM) do konfigurowania tras w celu uzyskania dostępu do udostępnionej sieci wirtualnej usługi z obciążeniami, które mają być dostępne w każdej sieci wirtualnej i gałęzi (VPN/ER/P2S). Przykłady tych udostępnionych obciążeń mogą obejmować maszyny wirtualne z usługami takimi jak kontrolery domeny lub udziały plików albo usługi platformy Azure udostępniane wewnętrznie za pomocą [prywatnego punktu końcowego platformy Azure](../private-link/private-endpoint-overview.md).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f301-virtual-wan-with-route-tables%2fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dane certyfikatu klucza publicznego są wymagane dla tej konfiguracji. Przykładowe dane znajdują się w tym artykule. Jednak przykładowe dane są dostępne tylko w celu spełnienia wymagań szablonu, aby można było utworzyć bramę P2S. Po zakończeniu szablonu i wdrożeniu zasobów należy zaktualizować to pole przy użyciu własnych danych certyfikatu, aby konfiguracja mogła zostać zadziałała. Zobacz [Certyfikaty sieci VPN użytkowników](certificates-point-to-site.md#cer).

## <a name="review-the-template"></a><a name="review"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/301-virtual-wan-with-route-tables). Szablon tego artykułu jest zbyt długi, aby można go było wyświetlić w tym miejscu. Aby wyświetlić szablon, zobacz [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/301-virtual-wan-with-route-tables/azuredeploy.json).

W tym przewodniku szybki start utworzysz wdrożenie obejmujące wiele centrów wirtualnych sieci WAN platformy Azure, w tym wszystkie bramy i połączenia sieci wirtualnej. Lista parametrów wejściowych została przeprowadzona w minimalnym zakresie. Schemat adresowania IP można zmienić, modyfikując zmienne wewnątrz szablonu. Scenariusz jest dokładniej wyjaśniony w [scenariuszu: artykuł sieci wirtualnej usług udostępnionych](scenario-shared-services-vnet.md) .

:::image type="content" source="./media/routing-scenarios/shared-service-vnet/shared-services.png" alt-text="Architektura wdrożenia" lightbox="./media/routing-scenarios/shared-service-vnet/shared-services.png":::

Ten szablon służy do tworzenia w pełni funkcjonalnego środowiska sieci wirtualnej platformy Azure z następującymi zasobami:

* 2 różne centra w różnych regionach.
* 4 sieci wirtualne platformy Azure.
* 2 połączenia sieci wirtualnej dla każdego centrum VWan.
* 1 Brama sieci VPN typu punkt-lokacja (P2S) w każdym centrum.
* 1 Brama sieci VPN typu lokacja-lokacja (S2S) w każdym centrum.
* 1 Brama ExpressRoute w każdym centrum.
* Niestandardowe tabele tras RT_SHARED w każdym centrum.
* Etykieta LBL_RT_SHARED do grupowania RT_SHARED tabel tras.

W szablonie zdefiniowano wiele zasobów platformy Azure:

* [**Microsoft. Network/virtualwans**](/azure/templates/microsoft.network/virtualwans)
* [**Microsoft. Network/virtualhubs**](/azure/templates/microsoft.network/virtualhubs)
* [**Microsoft. Network/virtualnetworks**](/azure/templates/microsoft.network/virtualnetworks)
* [**Microsoft. Network/hubvirtualnetworkconnections**](/azure/templates/microsoft.network/virtualhubs/hubvirtualnetworkconnections)
* [**Microsoft. Network/hubroutetables**](/azure/templates/microsoft.network/virtualhubs/hubRouteTables)
* [**Microsoft. Network/p2svpngateways**](/azure/templates/microsoft.network/p2svpngateways)
* [**Microsoft. Network/vpngateways**](/azure/templates/microsoft.network/vpngateways)
* [**Microsoft. Network/expressroutegateways**](/azure/templates/microsoft.network/expressroutegateways)
* [**Microsoft. Network/vpnserverconfigurations**](/azure/templates/microsoft.network/vpnServerConfigurations)

>[!NOTE]
> Ten szablon usługi ARM nie tworzy zasobów po stronie klienta wymaganych do łączności hybrydowej. Po wdrożeniu szablonu nadal trzeba utworzyć i skonfigurować klientów sieci VPN P2S, gałęzi sieci VPN (lokacje lokalne) oraz podłączyć obwody usługi ExpressRoute.
>

Aby znaleźć więcej szablonów, zobacz [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a><a name="deploy"></a>Wdrożenie szablonu

Aby poprawnie wdrożyć ten szablon, musisz użyć przycisku do wdrożenia na platformie Azure i Azure Portal, a nie innych metod, z następujących powodów:

* Aby można było utworzyć konfigurację P2S, należy przekazać dane certyfikatu głównego. Pole danych nie akceptuje danych certyfikatu podczas korzystania z programu PowerShell lub interfejsu wiersza polecenia.
* Ten szablon nie działa prawidłowo przy użyciu Cloud Shell ze względu na przekazywanie danych certyfikatu.
* Ponadto możesz łatwo zmodyfikować szablon i parametry w portalu, aby uwzględnić zakresy adresów IP i inne wartości.

1. Kliknij pozycję **Wdrażanie na platformie Azure**.

   [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f301-virtual-wan-with-route-tables%2fazuredeploy.json)
1. Aby wyświetlić szablon, kliknij przycisk **Edytuj szablon**. Na tej stronie można dostosować niektóre wartości, takie jak przestrzeń adresowa lub nazwa niektórych zasobów. **Zapisz** , aby zapisać zmiany, lub **Odrzuć**.
1. Na stronie szablon wprowadź wartości. W przypadku tego szablonu wymagane są dane certyfikatu publicznego P2S. Jeśli używasz tego artykułu jako ćwiczenia, możesz użyć następujących danych z tego pliku. cer jako przykładowe dane dla obu centrów. Po uruchomieniu i wdrożeniu szablonu, aby użyć konfiguracji P2S, należy zastąpić te informacje [danymi certyfikatu](certificates-point-to-site.md#cer) klucza publicznego we własnym wdrożeniu.

   ```certificate-data
   MIIC5zCCAc+gAwIBAgIQGxd3Av1q6LJDZ71e3TzqcTANBgkqhkiG9w0BAQsFADAW
   MRQwEgYDVQQDDAtQMlNSb290Q2VydDAeFw0yMDExMDkyMjMxNTVaFw0yMTExMDky
   MjUxNTVaMBYxFDASBgNVBAMMC1AyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEF
   AAOCAQ8AMIIBCgKCAQEA33fFra/E0YmGuXLKmYcdvjsYpKwQmw8DjjDkbwhE9jcc
   Dp50e7F1P6Rxo1T6Hm3dIhEji+0QkP4Ie0XPpw0eW77+RWUiG9XJxGqtJ3Q4tyRy
   vBfsHORcqMlpV3VZOXIxrk+L/1sSm2xAc2QGuOqKaDNNoKmjrSGNVAeQHigxbTQg
   zCcyeuhFxHxAaxpW0bslK2hEZ9PhuAe22c2SHht6fOIDeXkadzqTFeV8wEZdltLr
   6Per0krxf7N2hFo5Cfz0KgWlvgdKLL7dUc9cjHo6b6BL2pNbLh8YofwHQOQbwt6H
   miAkEnx1EJ5N8AWuruUTByR2jcWyCnEAUSH41+nk4QIDAQABozEwLzAOBgNVHQ8B
   Af8EBAMCAgQwHQYDVR0OBBYEFJMgnJSYHH5AJ+9XB11usKRwjbjNMA0GCSqGSIb3
   DQEBCwUAA4IBAQBOy8Z5FBd/nvgDcjvAwNCw9h5RHzgtgQqDP0qUjEqeQv3ALeC+
   k/F2Tz0OWiPEzX5N+MMrf/jiYsL2exXuaPWCF5U9fu8bvs89GabHma8MGU3Qua2x
   Imvt0whWExQMjoyU8SNUi2S13fnRie9ZlSwNh8B/OIUUEtVhQsd4OfuZZFVH4xGp
   ibJMSMe5JBbZJC2tCdSdTLYfYJqrLkVuTjynXOjmz2JXfwnDNqEMdIMMjXzlNavR
   J8SNtAoptMOK5vAvlySg4LYtFyXkl0W0vLKIbbHf+2UszuSCijTUa3o/Y1FoYSfi
   eJH431YTnVLuwdd6fXkXFBrXDhjNsU866+hE
   ```

1. Po zakończeniu wprowadzania wartości wybierz pozycję **Przegląd + Utwórz**.
1. Na stronie **Recenzja i tworzenie** po zakończeniu walidacji wybierz pozycję **Utwórz**.
1. Ukończenie wdrożenia zajmie około 75 minut. Postęp można wyświetlić na stronie **Przegląd** szablonu. Jeśli zamkniesz Portal, wdrożenie będzie kontynuowane.

   :::image type="content" source="./media/quickstart-route-shared-services-template/template.png" alt-text="Przykład wdrożenia zakończone":::

## <a name="validate-the-deployment"></a><a name="validate"></a>Weryfikowanie wdrożenia

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W okienku po lewej stronie wybierz pozycję **grupy zasobów** .
1. Wybierz grupę zasobów utworzoną w poprzedniej sekcji. Na stronie **Przegląd** zobaczysz coś podobnego do tego przykładu: :::image type="content" source="./media/quickstart-route-shared-services-template/resources.png" alt-text="przykład zasobów" lightbox="./media/quickstart-route-shared-services-template/resources.png":::

1. Kliknij wirtualną sieć WAN, aby wyświetlić centra. Na stronie wirtualna sieć WAN kliknij każde centrum, aby wyświetlić połączenia i inne informacje o centrum.
   :::image type="content" source="./media/quickstart-route-shared-services-template/hub.png" alt-text="Przykład centrów" lightbox="./media/quickstart-route-shared-services-template/hub.png":::

## <a name="complete-the-hybrid-configuration"></a><a name="complete"></a>Ukończ konfigurację hybrydową

Szablon nie konfiguruje wszystkich ustawień wymaganych dla sieci hybrydowej. Należy wykonać następujące konfiguracje i ustawienia, w zależności od wymagań.

* [Konfigurowanie gałęzi sieci VPN — Lokacje lokalne](virtual-wan-site-to-site-portal.md#site)
* [Ukończ konfigurację sieci VPN P2S](virtual-wan-point-to-site-portal.md)
* [Połącz obwody usługi ExpressRoute](virtual-wan-expressroute-portal.md)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zasoby, które zostały utworzone, nie są już potrzebne, usuń je. Niektóre z zasobów wirtualnych sieci WAN muszą zostać usunięte w określonej kolejności z powodu zależności. Usuwanie może potrwać około 30 minut.

[!INCLUDE [Delete resources](../../includes/virtual-wan-resource-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Ukończ konfigurację sieci VPN P2S](virtual-wan-point-to-site-portal.md)

> [!div class="nextstepaction"]
> [Konfigurowanie gałęzi sieci VPN — Lokacje lokalne](virtual-wan-site-to-site-portal.md#site)

> [!div class="nextstepaction"]
> [Połącz obwody usługi ExpressRoute](virtual-wan-expressroute-portal.md)
