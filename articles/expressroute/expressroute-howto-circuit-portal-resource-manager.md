---
title: Samouczek — Tworzenie i modyfikowanie obwodu za pomocą ExpressRoute
description: W tym samouczku dowiesz się, jak tworzyć, inicjować, sprawdzać, aktualizować, usuwać i cofać obsługę administracyjną obwodu ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/20/2018
ms.author: cherylmc
ms.openlocfilehash: 686ac8013879eff8adc4476d56119bbb4a169900
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/25/2020
ms.locfileid: "74813149"
---
# <a name="tutorial-create-and-modify-an-expressroute-circuit"></a>Samouczek: Tworzenie i modyfikowanie obwodu ExpressRoute

> [!div class="op_single_selector"]
> * [Witryna Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [Program PowerShell](expressroute-howto-circuit-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-circuit-cli.md)
> * [Szablon usługi Azure Resource Manager](expressroute-howto-circuit-resource-manager-template.md)
> * [Wideo — Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-circuit-classic.md)
>

Ten artykuł pomaga utworzyć obwód usługi ExpressRoute przy użyciu Azure Portal i Azure Resource Manager model wdrażania. Możesz również sprawdzić stan, zaktualizować, usunąć lub anulować obsługę administracyjną obwodu.

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Przed rozpoczęciem konfiguracji Przejrzyj [wymagania wstępne](expressroute-prerequisites.md) i [przepływy pracy](expressroute-workflows.md) .
* Upewnij się, że masz dostęp do [Azure Portal](https://portal.azure.com).
* Upewnij się, że masz uprawnienia do tworzenia nowych zasobów sieciowych. Jeśli nie masz odpowiednich uprawnień, skontaktuj się z administratorem konta.
* [Film wideo można wyświetlić](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) przed rozpoczęciem, aby lepiej zrozumieć te czynności.

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Tworzenie i Inicjowanie obsługi obwodu ExpressRoute

### <a name="1-sign-in-to-the-azure-portal"></a>1. Zaloguj się do Azure Portal

Przejdź w przeglądarce do witryny [Azure Portal](https://portal.azure.com) i zaloguj się przy użyciu konta platformy Azure.

### <a name="2-create-a-new-expressroute-circuit"></a>2. Utwórz nowy obwód ExpressRoute

> [!IMPORTANT]
> Obwód usługi ExpressRoute jest rozliczany od momentu, gdy zostanie wystawiony klucz usług. Upewnij się, że ta operacja jest wykonywana, gdy dostawca łączności jest gotowy do aprowizacji obwodu.

Obwód ExpressRoute można utworzyć, wybierając opcję tworzenia nowego zasobu. 

1. W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób**. Wybierz pozycję **Sieć**  >  **ExpressRoute**, jak pokazano na poniższej ilustracji:

   ![Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)](./media/expressroute-howto-circuit-portal-resource-manager/create-an-expressroute-circuit.png)

2. Po kliknięciu przycisku **ExpressRoute**zostanie wyświetlona strona **Tworzenie obwodu ExpressRoute** . Podczas wypełniania wartości na tej stronie upewnij się, że określono poprawną warstwę SKU (standardową lub Premium) i model rozliczeń pomiaru danych (nieograniczoną lub taryfową).

   ![Konfigurowanie warstwy jednostki SKU i pomiaru danych](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit.png)

   * **Warstwa** określa, czy włączono dodatek ExpressRoute Standard lub ExpressRoute Premium. Możesz określić **Standard** , aby uzyskać standardową jednostkę SKU lub **Premium** dla dodatku Premium.
   * **Pomiar danych** określa typ rozliczeń. Możesz określić **taryfowy** plan taryfowy i **nieograniczone** dla nieograniczonego planu taryfowego. Należy pamiętać, że typ rozliczeń można zmienić z **taryfowe** na **nieograniczony**.

     > [!IMPORTANT]
     > Nie można zmienić typu z **nieograniczonego** na **taryfowy**.

   * **Lokalizacja komunikacji równorzędnej** to lokalizacja fizyczna, w której Komunikacja równorzędna jest przeprowadzana z firmą Microsoft.

     > [!IMPORTANT]
     > Lokalizacja komunikacji równorzędnej wskazuje [lokalizację fizyczną](expressroute-locations.md) , w której jest przeprowadzana Komunikacja równorzędna z firmą Microsoft. **Nie** jest to połączenie z właściwością "Location", która odnosi się do lokalizacji geograficznej, w której znajduje się dostawca zasobów sieciowych platformy Azure. Chociaż nie są one powiązane, dobrym sposobem jest wybranie dostawcy zasobów sieciowych geograficznie blisko lokalizacji komunikacji równorzędnej obwodu.

### <a name="3-view-the-circuits-and-properties"></a>3. Wyświetl obwody i właściwości

**Wyświetl wszystkie obwody**

Wszystkie utworzone obwody można wyświetlić, wybierając pozycję **wszystkie zasoby** w menu po lewej stronie.

![Wyświetl obwody](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

**Wyświetl właściwości**

Możesz wyświetlić właściwości obwodu, zaznaczając go. Na stronie **Omówienie** obwodu w polu klucz usługi pojawi się klucz usługi. Należy skopiować klucz usługi dla obwodu i przekazać go do dostawcy usług w celu ukończenia procesu aprowizacji. Klucz usługi obwodu jest specyficzny dla obwodu.

![Wyświetl właściwości](./media/expressroute-howto-circuit-portal-resource-manager/servicekey1.png)

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. Wyślij klucz usługi do dostawcy połączenia w celu aprowizacji

Na tej stronie **stan dostawcy** zawiera informacje o bieżącym stanie aprowizacji po stronie Dostawca usług. **Stan obwodu** zapewnia stan po stronie firmy Microsoft. Aby uzyskać więcej informacji o Stanach aprowizacji obwodów, zobacz artykuł [przepływy pracy](expressroute-workflows.md#expressroute-circuit-provisioning-states) .

Podczas tworzenia nowego obwodu ExpressRoute obwód jest w następującym stanie:

Stan dostawcy: nie zainicjowano obsługi administracyjnej<BR>
Stan obwodu: włączony

![Inicjuj proces aprowizacji](./media/expressroute-howto-circuit-portal-resource-manager/status.png)

Obwód zostanie zmieniony na następujący stan, gdy dostawca łączności jest w trakcie jego włączania:

Stan dostawcy: Inicjowanie obsługi<BR>
Stan obwodu: włączony

Aby móc korzystać z obwodu usługi ExpressRoute, musi on być w następującym stanie:

Stan dostawcy: zainicjowano<BR>
Stan obwodu: włączony

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. okresowe sprawdzanie stanu i stanu klucza obwodu

Możesz wyświetlić właściwości obwodu, który Cię interesuje, zaznaczając go. Sprawdź **stan dostawcy** i upewnij się, że został przeniesiony do programu z **obsługą administracyjną** przed kontynuowaniem.

![Stan obwodu i dostawcy](./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png)

### <a name="6-create-your-routing-configuration"></a>6. Utwórz konfigurację routingu

Aby uzyskać instrukcje krok po kroku, zapoznaj się z artykułem [Konfiguracja routingu obwodów usługi ExpressRoute](expressroute-howto-routing-portal-resource-manager.md) w celu utworzenia i zmodyfikowania komunikacji równorzędnej obwodu.

> [!IMPORTANT]
> Te instrukcje dotyczą tylko obwodów, które są tworzone za pomocą dostawców usług oferujących usługi łączności warstwy 2. Jeśli używasz dostawcy usług oferującego zarządzane usługi warstwy 3 (zazwyczaj sieć VPN sieci IP, na przykład MPLS), dostawca łączności konfiguruje i zarządza routingiem.

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7. Łączenie sieci wirtualnej z obwodem ExpressRoute

Następnie połącz sieć wirtualną z obwodem ExpressRoute. Podczas pracy z modelem wdrażania Menedżer zasobów należy użyć artykułu [łączenie sieci wirtualnych do obwodów usługi ExpressRoute](expressroute-howto-linkvnet-arm.md) .

## <a name="getting-the-status-of-an-expressroute-circuit"></a><a name="status"></a>Pobieranie stanu obwodu ExpressRoute

Stan obwodu można wyświetlić, zaznaczając go i wyświetlając stronę przegląd.

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Modyfikowanie obwodu ExpressRoute

Niektóre właściwości obwodu usługi ExpressRoute można modyfikować bez wpływu na łączność. Na stronie **Konfiguracja** można modyfikować przepustowość, jednostkę SKU, model rozliczeń i zezwalać na działania klasyczne. Aby uzyskać informacje o limitach i ograniczeniach, zobacz [często zadawane pytania](expressroute-faqs.md)dotyczące usługi ExpressRoute.

W przypadku braku przestojów można wykonać następujące zadania:

* Włącz lub Wyłącz dodatek ExpressRoute Premium dla obwodu usługi ExpressRoute.
* Zwiększ przepustowość obwodu ExpressRoute, pod warunkiem że na porcie jest dostępna pojemność.

  > [!IMPORTANT]
  > Obniżenie przepustowości obwodu nie jest obsługiwane.

* Zmień plan pomiaru z *danych mierzonych* na *dane nieograniczone*.

  > [!IMPORTANT]
  > Zmiana planu pomiaru z nieograniczonej ilości danych na dane taryfowe nie jest obsługiwana.

* Można włączać i wyłączać *Zezwalanie na klasyczne operacje*.
  > [!IMPORTANT]
  > Może być konieczne ponowne utworzenie obwodu ExpressRoute, jeśli nie ma odpowiedniej pojemności na istniejącym porcie. Nie można uaktualnić obwodu, jeśli w tej lokalizacji nie ma dodatkowej pojemności.
  >
  > Chociaż można bezproblemowo uaktualnić przepustowość, nie można zmniejszyć przepustowości obwodu ExpressRoute bez zakłóceń. Obniżenie przepustowości wymaga anulowania aprowizacji obwodu ExpressRoute, a następnie ponownego aprowizacji nowego obwodu ExpressRoute.
  >
  > Wyłączenie operacji dodatku Premium może zakończyć się niepowodzeniem, jeśli używasz zasobów, które są większe niż dozwolone dla obwodu standardowego.

Aby zmodyfikować obwód ExpressRoute, kliknij pozycję **Konfiguracja**.

![Modyfikuj obwód](./media/expressroute-howto-circuit-portal-resource-manager/modify-circuit-configuration.png)

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>Anulowanie aprowizacji i usuwanie obwodu usługi ExpressRoute

Obwód ExpressRoute można usunąć, wybierając ikonę **Usuń** . Zanotuj następujące informacje:

* Musisz odłączyć wszystkie sieci wirtualne od obwodu usługi ExpressRoute. Jeśli ta operacja nie powiedzie się, sprawdź, czy wszystkie sieci wirtualne są połączone z obwodem.
* Jeśli stan aprowizacji dostawcy usługi obwodu ExpressRoute jest inicjowany lub **Zainicjowano obsługę administracyjną** , należy skontaktować się z dostawcą usług w celu **anulowania aprowizacji** obwodu po stronie. Nadal rezerwujemy zasoby i obciążamy Cię, dopóki dostawca usług nie ukończy anulowania aprowizacji obwodu i powiadamia nas.
* Jeśli dostawca usług anulował obsługę administracyjną obwodu (stan aprowizacji dostawcy usług jest ustawiony na **nieinicjowany**), można usunąć obwód. Spowoduje to zatrzymanie naliczania opłat za obwód.

## <a name="next-steps"></a>Następne kroki

Po utworzeniu obwodu wykonaj następujące kolejne czynności:

* [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Łączenie sieci wirtualnej z obwodem ExpressRoute](expressroute-howto-linkvnet-arm.md)
