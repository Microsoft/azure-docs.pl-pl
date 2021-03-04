---
title: 'Szybki Start: Tworzenie i Konfigurowanie serwera tras przy użyciu Azure Portal'
description: W tym przewodniku szybki start dowiesz się, jak utworzyć i skonfigurować serwer tras przy użyciu Azure Portal.
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.date: 03/03/2021
ms.author: duau
ms.openlocfilehash: ef48f7623aee1256ad1f889d2e70b5a2fdb8e6bf
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102108879"
---
# <a name="quickstart-create-and-configure-route-server-using-the-azure-portal"></a>Szybki Start: Tworzenie i Konfigurowanie serwera tras przy użyciu Azure Portal

W tym artykule opisano sposób konfigurowania usługi Azure Route Server do komunikacji równorzędnej za pomocą sieciowego urządzenia wirtualnego (urządzenie WUS) w sieci wirtualnej przy użyciu Azure Portal. Usługa Azure Route Server będzie uczyć się tras z urządzenie WUS i programować je na maszynach wirtualnych w sieci wirtualnej. Serwer usługi Azure Route również anonsuje trasy sieci wirtualnej do urządzenie WUS. Aby uzyskać więcej informacji, przeczytaj temat [serwer usługi Azure Route](overview.md).

> [!IMPORTANT]
> Usługa Azure Route Server (wersja zapoznawcza) jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Przejrzyj [limity usługi dla serwera usługi Azure Route](route-server-faq.md#limitations).

## <a name="create-a-route-server"></a>Tworzenie serwera tras

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Zaloguj się do konta platformy Azure i wybierz subskrypcję.

Przejdź w przeglądarce do witryny [Azure Portal](https://portal.azure.com) i zaloguj się przy użyciu konta platformy Azure.

### <a name="create-a-route-server"></a>Tworzenie serwera tras

1. Przejdź do witryny https://aka.ms/routeserver.

1. Wybierz pozycję **+ Utwórz nowy serwer tras**.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/route-server-landing-page.png" alt-text="Zrzut ekranu strony docelowej serwera tras."::: 

1. Na stronie **Tworzenie serwera tras** wprowadź lub wybierz wymagane informacje.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/create-route-server-page.png" alt-text="Zrzut ekranu przedstawiający stronę Tworzenie serwera tras.":::     

    | Ustawienia | Wartość |
    |----------|-------|
    | Subskrypcja | Wybierz subskrypcję platformy Azure, której chcesz użyć do wdrożenia serwera tras. |
    | Grupa zasobów | Wybierz grupę zasobów, w której ma zostać utworzony serwer tras. Jeśli nie masz istniejącej grupy zasobów, możesz utworzyć nową. |
    | Nazwa | Wprowadź nazwę serwera tras. |
    | Region (Region) | Wybierz region, w którym zostanie utworzony serwer tras. Wybierz ten sam region, który został wcześniej utworzony przez utworzoną sieć wirtualną w celu wyświetlenia sieci wirtualnej na liście rozwijanej. |
    | Virtual Network | Wybierz sieć wirtualną, w której zostanie utworzony serwer tras. Można utworzyć nową sieć wirtualną lub użyć istniejącej sieci wirtualnej. Jeśli używasz istniejącej sieci wirtualnej, upewnij się, że istniejąca sieć wirtualna ma wystarczającą ilość miejsca dla co najmniej %27 podsieci w celu uwzględnienia wymagania podsieci serwera tras. Jeśli nie widzisz sieci wirtualnej z listy rozwijanej, upewnij się, że wybrano poprawną grupę zasobów lub region. |
    | Podsieć | Po utworzeniu lub wybraniu sieci wirtualnej zostanie wyświetlone pole podsieć. Ta podsieć jest przeznaczona tylko do kierowania serwer. Wybierz pozycję **Zarządzaj konfiguracją podsieci** i Utwórz podsieć serwera tras platformy Azure. Wybierz pozycję **+ podsieć** i Utwórz podsieć, korzystając z następujących wskazówek:</br><br>-Podsieć musi mieć nazwę *RouteServerSubnet*.</br><br>-Podsieć musi mieć wartość co najmniej/27 lub większą.</br> |

1. Wybierz pozycję **Przegląd + Utwórz**, Przejrzyj podsumowanie, a następnie wybierz pozycję **Utwórz**. 

    > [!NOTE]
    > Wdrożenie serwera tras zajmie około 20 minut.

## <a name="set-up-peering-with-nva"></a>Konfigurowanie komunikacji równorzędnej za pomocą urządzenie WUS

Sekcja pomoże Ci skonfigurować komunikację równorzędną BGP z urządzenie WUS.

1. Przejdź do pozycji [serwer tras](https://aka.ms/routeserver) w Azure Portal i wybierz serwer tras, który chcesz skonfigurować.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/select-route-server.png" alt-text="Zrzut ekranu listy serwerów tras."::: 

1. Wybierz pozycję **elementy równorzędne** w obszarze *Ustawienia* w lewym panelu nawigacyjnym. Następnie wybierz pozycję **+ Dodaj** , aby dodać nowy element równorzędny.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/peers-landing-page.png" alt-text="Zrzut ekranu strony docelowej elementów równorzędnych."::: 

1. Wprowadź następujące informacje dotyczące elementu równorzędnego urządzenie WUS.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/add-peer-page.png" alt-text="Zrzut ekranu przedstawiający stronę Dodawanie elementu równorzędnego.":::

    | Ustawienia | Wartość |
    |----------|-------|
    | Nazwa | Nadaj nazwę komunikacji równorzędnej między serwerem tras i urządzenie WUS. |
    | Właściwość |  Wprowadź numer systemu autonomicznego (ASN) urządzenie WUS. |
    | Adres IPv4 | Wprowadź adres IP urządzenie WUS, z którym będzie się komunikować serwer tras, aby ustanowić protokół BGP. |

1. Wybierz pozycję **Dodaj** , aby dodać ten element równorzędny.

## <a name="complete-the-configuration-on-the-nva"></a>Ukończ konfigurację na urządzenie WUS

Aby ukończyć konfigurację usługi urządzenie WUS w celu ustanowienia sesji BGP, musisz dysponować równorzędnymi adresami IP i numerem ASN serwera usługi Azure Route. Te informacje można uzyskać na stronie Przegląd serwera tras.

:::image type="content" source="./media/quickstart-configure-route-server-portal/route-server-overview.png" alt-text="Zrzut ekranu przedstawiający stronę przegląd serwera tras.":::

## <a name="configure-route-exchange"></a>Konfigurowanie wymiany tras

Jeśli masz bramę usługi ExpressRoute i/lub bramę sieci VPN i chcesz, aby mogli oni wymieniać trasy z serwerem tras, możesz włączyć wymianę tras.

1. Przejdź do pozycji [serwer tras](https://aka.ms/routeserver) w Azure Portal i wybierz serwer tras, który chcesz skonfigurować.

1. Wybierz pozycję **Konfiguracja** w obszarze *Ustawienia* w lewym panelu nawigacyjnym.

1. Wybierz opcję **Włącz** dla ustawienia **odgałęzienie do gałęzi** , a następnie wybierz pozycję **Zapisz**.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/enable-route-exchange.png" alt-text="Zrzut ekranu przedstawiający sposób włączania wymiany tras.":::

## <a name="next-steps"></a>Następne kroki

Po utworzeniu serwera usługi Azure Route należy dalej dowiedzieć się, jak usługa Azure Route Server współdziała z ExpressRoute i bramami sieci VPN: 

> [!div class="nextstepaction"]
> [Azure ExpressRoute i obsługa sieci VPN platformy Azure](expressroute-vpn-support.md)
