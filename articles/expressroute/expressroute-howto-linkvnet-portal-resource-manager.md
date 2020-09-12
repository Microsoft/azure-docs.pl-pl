---
title: 'ExpressRoute: Połącz sieć wirtualną z obwodem: Azure Portal'
description: Dowiedz się, jak utworzyć połączenie w celu połączenia sieci wirtualnej z obwodem usługi Azure ExpressRoute przy użyciu Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 09/17/2019
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 56508503c199b1f822ce8f181689a236f3a0af18
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89395829"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>Nawiązywanie połączenia sieci wirtualnej z obwodem usługi ExpressRoute za pomocą witryny Portal
> [!div class="op_single_selector"]
> * [Witryna Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [Program PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-linkvnet-cli.md)
> * [Wideo — Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-linkvnet-classic.md)
> 

Ten artykuł pomaga utworzyć połączenie w celu połączenia sieci wirtualnej z obwodem usługi Azure ExpressRoute przy użyciu Azure Portal. Sieci wirtualne, które można połączyć ze obwodem usługi Azure ExpressRoute, mogą znajdować się w tej samej subskrypcji lub być częścią innej subskrypcji.

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Przed rozpoczęciem konfiguracji zapoznaj się z warunkami [wstępnymi](expressroute-prerequisites.md), [wymaganiami dotyczącymi routingu](expressroute-routing.md)i [przepływami pracy](expressroute-workflows.md) .

* Musisz mieć aktywny obwód usługi ExpressRoute.
  * Postępuj zgodnie z instrukcjami, aby [utworzyć obwód usługi ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) i mieć obwód włączony przez dostawcę połączenia.
  * Upewnij się, że masz skonfigurowaną prywatną komunikację równorzędną Azure dla obwodu. Zapoznaj się z instrukcjami dotyczącymi komunikacji równorzędnej i routingu w artykule [Tworzenie i modyfikowanie komunikacji równorzędnej dla obwodu usługi ExpressRoute](expressroute-howto-routing-portal-resource-manager.md) .
  * Upewnij się, że skonfigurowano prywatną komunikację równorzędną platformy Azure, a Komunikacja równorzędna protokołu BGP między siecią i firmą Microsoft jest w stanie umożliwiającym kompleksową łączność.
  * Upewnij się, że masz utworzoną sieć wirtualną i bramę sieci wirtualnej i została ona w pełni zainicjowana. Postępuj zgodnie z instrukcjami, aby [utworzyć bramę sieci wirtualnej dla usługi ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Brama sieci wirtualnej dla usługi ExpressRoute używa elementu Gatewaytype "ExpressRoute", a nie sieci VPN.

* Do obwodu standardowego ExpressRoute można połączyć maksymalnie 10 sieci wirtualnych. Wszystkie sieci wirtualne muszą znajdować się w tym samym regionie geopolitycznym w przypadku korzystania ze standardowego obwodu usługi ExpressRoute.

* Jedna sieć wirtualna może być połączona do maksymalnie czterech obwodów usługi ExpressRoute. Użyj poniższego procesu, aby utworzyć nowy obiekt połączenia dla każdego obwodu usługi ExpressRoute, z którym nawiązujesz połączenie. Obwody usługi ExpressRoute mogą znajdować się w tej samej subskrypcji, różnych subskrypcjach lub kombinacji obu tych elementów.

* Możesz połączyć sieć wirtualną poza regionem geopolitycznym obwodu ExpressRoute lub połączyć większą liczbę sieci wirtualnych z obwodem ExpressRoute, jeśli włączono dodatek ExpressRoute Premium. Zapoznaj się z [często zadawanymi pytaniami](expressroute-faqs.md) , aby uzyskać więcej informacji na temat dodatku Premium.

* [Film wideo można wyświetlić](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit) przed rozpoczęciem, aby lepiej zrozumieć kroki.

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>Łączenie sieci wirtualnej z obwodem — ta sama subskrypcja

> [!NOTE]
> Informacje o konfiguracji protokołu BGP nie będą wyświetlane, jeśli dostawca warstwy 3 skonfigurował komunikację równorzędną. Jeśli obwód jest w stanie aprowizacji, powinno być możliwe tworzenie połączeń.
>

### <a name="to-create-a-connection"></a>Aby utworzyć połączenie

1. Upewnij się, że pomyślnie skonfigurowano obwód usługi ExpressRoute i prywatną komunikację równorzędną Azure. Postępuj zgodnie z instrukcjami w temacie [Tworzenie obwodu usługi ExpressRoute](expressroute-howto-circuit-arm.md) i [Tworzenie i modyfikowanie komunikacji równorzędnej dla obwodu usługi ExpressRoute](expressroute-howto-routing-arm.md). Obwód ExpressRoute powinien wyglądać jak na poniższej ilustracji:

   [![Zrzut ekranu obwodu ExpressRoute](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png "Wyświetl obwód")](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1-exp.png#lightbox)
2. Teraz można rozpocząć Inicjowanie obsługi połączenia w celu połączenia bramy sieci wirtualnej z obwodem usługi ExpressRoute. Kliknij pozycję **połączenie**  >  **Dodaj** , aby otworzyć stronę **Dodawanie połączenia** , a następnie skonfiguruj wartości.

   [![Dodaj zrzut ekranu połączenia](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png "Dodaj zrzut ekranu połączenia")](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1-exp.png#lightbox)
3. Po pomyślnym skonfigurowaniu połączenia obiekt połączenia będzie wyświetlał informacje dotyczące połączenia.

   ![Zrzut ekranu obiektu połączenia](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)


## <a name="connect-a-vnet-to-a-circuit---different-subscription"></a>Łączenie sieci wirtualnej z inną subskrypcją obwodu

Obwód ExpressRoute można udostępnić w wielu subskrypcjach. Na poniższej ilustracji przedstawiono prosty schemat udostępniania współdziałania dla obwodów usługi ExpressRoute w ramach wielu subskrypcji.

![Łączność między subskrypcjami](./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png)

- Każda z mniejszych chmur w ramach dużej chmury służy do reprezentowania subskrypcji należących do różnych działów w organizacji.
- Każdy dział w organizacji może używać własnej subskrypcji do wdrażania swoich usług, ale mogą współdzielić jeden obwód usługi ExpressRoute, aby połączyć się z siecią lokalną.
- Pojedynczy dział (w tym przykładzie:) może być własnym obwodem ExpressRoute. Inne subskrypcje w organizacji mogą używać obwodu ExpressRoute i autoryzacji skojarzonych z obwodem, w tym subskrypcji połączonych z innymi dzierżawami Azure Active Directory i Umowa Enterprise rejestracji.

  > [!NOTE]
  > Naliczanie opłat za połączenia i przepustowość dla dedykowanego obwodu zostanie zastosowane do właściciela obwodu usługi ExpressRoute. Wszystkie sieci wirtualne mają tę samą przepustowość.
  >
  >

### <a name="administration---about-circuit-owners-and-circuit-users"></a>Administracja — informacje o właścicielach obwodu i użytkowników obwodu

"Właściciel obwodu" jest autoryzowanym użytkownikiem zaawansowanym zasobu obwodu ExpressRoute. Właściciel obwodu może tworzyć autoryzacje, które mogą być zrealizowane przez "użytkowników obwodów". Użytkownicy obwodu to właściciele bram sieci wirtualnej, którzy nie znajdują się w tej samej subskrypcji co obwód usługi ExpressRoute. Użytkownicy obwodów mogą wykorzystać autoryzacje (jedna autoryzacja na sieć wirtualną).

Właściciel obwodu ma uprawnienia do modyfikowania i odwoływania autoryzacji w dowolnym momencie. Odwoływanie autoryzacji powoduje usunięcie wszystkich połączeń linków z subskrypcji, której dostęp został odwołany.

### <a name="circuit-owner-operations"></a>Operacje właściciela obwodu

**Aby utworzyć autoryzację połączenia**

Właściciel obwodu tworzy autoryzację. Powoduje to utworzenie klucza autoryzacji, który może być używany przez użytkownika obwodu do łączenia ich bram sieci wirtualnych z obwodem ExpressRoute. Autoryzacja jest prawidłowa tylko dla jednego połączenia.

> [!NOTE]
> Każde połączenie wymaga oddzielnej autoryzacji.
>

1. Na stronie ExpressRoute kliknij pozycję **autoryzacje** , a następnie wpisz **nazwę** autoryzacji i kliknij przycisk **Zapisz**.

   ![Autoryzacje](./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png)
2. Po zapisaniu konfiguracji Skopiuj **Identyfikator zasobu** i **klucz autoryzacji**.

   ![Klucz autoryzacji](./media/expressroute-howto-linkvnet-portal-resource-manager/authkey.png)

**Aby usunąć autoryzację połączenia**

Połączenie można usunąć, wybierając ikonę **usuwania** na stronie połączenia.

### <a name="circuit-user-operations"></a>Operacje użytkownika obwodowego

Użytkownik obwodu potrzebuje identyfikatora zasobu i klucza autoryzacji od właściciela obwodu.

**Aby zrealizować autoryzację połączenia**

1. Kliknij przycisk **+ Nowy** .

   ![Kliknij pozycję Nowy.](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection1.png)
2. Wyszukaj ciąg **"Connection"** w portalu Marketplace, wybierz go, a następnie kliknij przycisk **Utwórz**.

   ![Wyszukaj połączenie](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection2.png)
3. Upewnij się, że **Typ połączenia** to "ExpressRoute".
4. Wypełnij szczegóły, a następnie kliknij przycisk **OK** na stronie podstawy.

   ![Strona Podstawy](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection3.png)
5. Na stronie **Ustawienia** wybierz **bramę sieci wirtualnej** i zaznacz pole wyboru Wykorzystaj **autoryzację** .
6. Wprowadź **klucz autoryzacji** i **Identyfikator URI obwodu równorzędnego** i nadaj nazwę połączenie. Kliknij pozycję **OK**. **URI obwodu równorzędnego** jest identyfikatorem zasobu obwodu ExpressRoute (który można znaleźć w okienku ustawienia właściwości obwodu ExpressRoute).

   ![Strona Ustawienia](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection4.png)
7. Przejrzyj informacje na stronie **Podsumowanie** i kliknij przycisk **OK**.

**Aby zwolnić autoryzację połączenia**

Można zwolnić autoryzację, usuwając połączenie łączące obwód usługi ExpressRoute z siecią wirtualną.

## <a name="delete-a-connection-to-unlink-a-vnet"></a>Usuwanie połączenia w celu odłączenia sieci wirtualnej

Możesz usunąć połączenie i odłączyć sieć wirtualną do obwodu usługi ExpressRoute, wybierając ikonę **usuwania** na stronie połączenia.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat ExpressRoute, zobacz [często zadawane pytania](expressroute-faqs.md)dotyczące usługi ExpressRoute.
