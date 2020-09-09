---
title: Samouczek — Tworzenie i modyfikowanie obwodu za pomocą ExpressRoute
description: W ramach tego samouczka nauczysz się tworzyć, inicjować, weryfikować, aktualizować, usuwać i cofać aprowizacji obwodu ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 09/01/2020
ms.author: duau
ms.openlocfilehash: e0ba14cd5db47c12435b2de35d0753b402c947ea
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/09/2020
ms.locfileid: "89566266"
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

W tym samouczku pokazano, jak utworzyć obwód usługi ExpressRoute przy użyciu Azure Portal i Azure Resource Manager model wdrażania. Możesz również sprawdzić stan, zaktualizować, usunąć lub anulować obsługę administracyjną obwodu.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)
> * Pobierz bieżący stan obwodu
> * Modyfikowanie obwodu
> * Cofanie aprowizacji i usuwanie obwodu

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

1. W menu witryny Azure Portal wybierz pozycję **Utwórz zasób**. Wybierz pozycję **Sieć**  >  **ExpressRoute**, jak pokazano na poniższej ilustracji:

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/create-expressroute-circuit-menu.png" alt-text="Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)":::

2. Po kliknięciu przycisku **ExpressRoute**zostanie wyświetlona strona **Tworzenie ExpressRoute** . Podaj **grupę zasobów**, **region**i  **nazwę** obwodu. Następnie kliknij przycisk **Dalej: Configuration >**.

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-create-basic.png" alt-text="Konfigurowanie grupy zasobów i regionu":::

3. Podczas wypełniania wartości na tej stronie upewnij się, że określono poprawną warstwę SKU (lokalna, standardowa lub Premium) i model rozliczeń pomiaru danych (nieograniczoną lub taryfową).

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-create-configuration.png" alt-text="Konfigurowanie obwodu":::
    
    * **Typ portu** określa, czy łączysz się z dostawcą usług lub bezpośrednio do sieci globalnej firmy Microsoft w lokalizacji komunikacji równorzędnej.
    * **Utwórz nowy lub Importuj z klasycznego** określa, czy jest tworzony nowy obwód, czy też w przypadku migrowania klasycznego obwodu do usługi ARM.
    * **Dostawca** jest usługą internetową, z której będziesz otrzymywać żądania usługi.
    * **Lokalizacja komunikacji równorzędnej** to lokalizacja fizyczna, w której Komunikacja równorzędna jest przeprowadzana z firmą Microsoft.

    > [!IMPORTANT]
    > Lokalizacja komunikacji równorzędnej wskazuje [lokalizację fizyczną](expressroute-locations.md) , w której jest przeprowadzana Komunikacja równorzędna z firmą Microsoft. **Nie** jest to połączenie z właściwością "Location", która odnosi się do lokalizacji geograficznej, w której znajduje się dostawca zasobów sieciowych platformy Azure. Chociaż nie są one powiązane, dobrym sposobem jest wybranie dostawcy zasobów sieciowych geograficznie blisko lokalizacji komunikacji równorzędnej obwodu.

    * **Jednostka SKU** określa, czy włączono dodatek ExpressRoute Local, ExpressRoute Standard lub ExpressRoute Premium. Możesz określić **lokalną** jednostkę SKU, **Standard** , aby uzyskać standardową jednostkę SKU lub **Premium** dla dodatku Premium. Należy pamiętać, że można zmienić jednostkę SKU, aby włączyć dodatek Premium.
    > [!IMPORTANT]
    > Nie można zmienić jednostki SKU z warstwy **standardowa/Premium** na **lokalną**.
    
    * **Model rozliczeń** określa typ rozliczeń. Możesz określić **taryfowy** plan taryfowy i **nieograniczone** dla nieograniczonego planu taryfowego. Należy pamiętać, że typ rozliczeń można zmienić z **taryfowe** na **nieograniczony**.

    > [!IMPORTANT]
    > Nie można zmienić typu z **nieograniczonego** na **taryfowy**.

    * Wartość **Zezwalaj na użycie klasycznej** sieci wirtualnej będzie zawierać link do obwodu.

### <a name="3-view-the-circuits-and-properties"></a>3. Wyświetl obwody i właściwości

**Wyświetl wszystkie obwody**

Można wyświetlić wszystkie utworzone przez Ciebie obwody, wybierając pozycję **wszystkie usługi > sieć > obwody ExpressRoute** w menu po lewej stronie.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-menu.png" alt-text="Menu obwodu ExpressRoute":::

Wszystkie obwody usługi ExpressRoute utworzone w ramach subskrypcji będą wyświetlane w tym miejscu.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-list.png" alt-text="Lista obwodów ExpressRoute":::

**Wyświetl właściwości**

Możesz wyświetlić właściwości obwodu, zaznaczając go. Na stronie **Omówienie** obwodu w polu klucz usługi pojawi się klucz usługi. Zapoznaj się z kluczem usługi dla obwodu i podaj jej dostawcę usług w celu ukończenia procesu aprowizacji. Klucz usługi jest specyficzny dla obwodu.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-overview.png" alt-text="Wyświetl właściwości":::

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. Wyślij klucz usługi do dostawcy połączenia w celu aprowizacji

Na tej stronie **stan dostawcy** zawiera informacje o bieżącym stanie aprowizacji po stronie Dostawca usług. **Stan obwodu** zapewnia stan po stronie firmy Microsoft. Aby uzyskać więcej informacji o Stanach aprowizacji obwodów, zobacz artykuł [przepływy pracy](expressroute-workflows.md#expressroute-circuit-provisioning-states) .

Podczas tworzenia nowego obwodu ExpressRoute obwód jest w następującym stanie:

Stan dostawcy: **nie zainicjowano obsługi administracyjnej**<BR>
Stan obwodu: **włączony**

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-overview-provisioning-state.png" alt-text="Inicjuj proces aprowizacji":::

Obwód zostanie zmieniony na następujący stan, gdy dostawca łączności jest w trakcie jego włączania:

Stan dostawcy: **Inicjowanie obsługi**<BR>
Stan obwodu: **włączony**

Aby móc korzystać z obwodu usługi ExpressRoute, musi on być w następującym stanie:

Stan dostawcy: **zainicjowano**<BR>
Stan obwodu: **włączony**

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. okresowe sprawdzanie stanu i stanu klucza obwodu

Możesz wyświetlić właściwości obwodu, który Cię interesuje, zaznaczając go. Sprawdź **stan dostawcy** i upewnij się, że został przeniesiony do programu z **obsługą administracyjną** przed kontynuowaniem.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png" alt-text="Stan obwodu i dostawcy":::

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

> [!IMPORTANT]
  > Zmiana jednostki SKU z warstwy **standardowa/Premium** na **lokalną** nie jest obsługiwana.

* Zwiększ przepustowość obwodu ExpressRoute, pod warunkiem że na porcie jest dostępna pojemność.

  > [!IMPORTANT]
  > Obniżenie przepustowości obwodu nie jest obsługiwane.

* Zmień plan pomiaru z *danych mierzonych* na *dane nieograniczone*.

  > [!IMPORTANT]
  > Zmiana planu pomiaru z **nieograniczonej ilości danych** na **dane taryfowe** nie jest obsługiwana.

* Można włączać i wyłączać *Zezwalanie na klasyczne operacje*.
  > [!IMPORTANT]
  > Może być konieczne ponowne utworzenie obwodu ExpressRoute, jeśli nie ma odpowiedniej pojemności na istniejącym porcie. Nie można uaktualnić obwodu, jeśli w tej lokalizacji nie ma dodatkowej pojemności.
  >
  > Chociaż można bezproblemowo uaktualnić przepustowość, nie można zmniejszyć przepustowości obwodu ExpressRoute bez zakłóceń. Obniżenie przepustowości wymaga anulowania aprowizacji obwodu ExpressRoute, a następnie ponownego aprowizacji nowego obwodu ExpressRoute.
  >
  > Wyłączenie operacji dodatku Premium może zakończyć się niepowodzeniem, jeśli używasz zasobów, które są większe niż dozwolone dla obwodu standardowego.

Aby zmodyfikować obwód ExpressRoute, kliknij pozycję **Konfiguracja**.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-configuration.png" alt-text="Modyfikuj obwód":::

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>Anulowanie aprowizacji i usuwanie obwodu usługi ExpressRoute

Jeśli stan aprowizacji dostawcy usługi obwodu ExpressRoute jest inicjowany lub **Zainicjowano obsługę administracyjną** , należy skontaktować się z dostawcą usług w celu **anulowania aprowizacji** obwodu po stronie. Nadal rezerwujemy zasoby i obciążamy Cię, dopóki dostawca usług nie ukończy anulowania aprowizacji obwodu i powiadamia nas.

> [!NOTE]
>* Przed cofnięciem aprowizacji należy odłączyć *wszystkie sieci wirtualne* od obwodu usługi ExpressRoute. Jeśli ta operacja nie powiedzie się, sprawdź, czy wszystkie sieci wirtualne są połączone z obwodem.
>* Jeśli dostawca usług anulował obsługę administracyjną obwodu (stan aprowizacji dostawcy usług jest ustawiony na **nieinicjowany**), można usunąć obwód. Spowoduje to zatrzymanie naliczania opłat za obwód.

Obwód ExpressRoute można usunąć, wybierając ikonę **Usuń** . 

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-delete.png" alt-text="Usuń obwód":::

## <a name="next-steps"></a>Następne kroki

Po utworzeniu obwodu wykonaj następujące kolejne czynności:

* [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Łączenie sieci wirtualnej z obwodem ExpressRoute](expressroute-howto-linkvnet-arm.md)
