---
title: 'Szybki Start: Tworzenie i modyfikowanie obwodu za pomocą ExpressRoute Azure Portal'
description: W tym przewodniku szybki start dowiesz się, jak tworzyć, inicjować, sprawdzać, aktualizować, usuwać i anulować obsługę obwodu usługi ExpressRoute przy użyciu Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: duau
ms.openlocfilehash: dbb28726d1c2eaf887ced818284d4b1e0298f8db
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91767807"
---
# <a name="quickstart-create-and-modify-an-expressroute-circuit"></a>Szybki Start: Tworzenie i modyfikowanie obwodu ExpressRoute

Ten przewodnik Szybki Start przedstawia sposób tworzenia obwodu usługi ExpressRoute przy użyciu Azure Portal i Azure Resource Manager modelu wdrażania. Możesz również sprawdzić stan, zaktualizować, usunąć lub anulować obsługę administracyjną obwodu.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Przed rozpoczęciem konfiguracji Przejrzyj [wymagania wstępne](expressroute-prerequisites.md) i [przepływy pracy](expressroute-workflows.md) .
* [Film wideo można wyświetlić](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) przed rozpoczęciem, aby lepiej zrozumieć kroki.

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Tworzenie i Inicjowanie obsługi obwodu ExpressRoute

### <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Przejdź w przeglądarce do witryny [Azure Portal](https://portal.azure.com) i zaloguj się przy użyciu konta platformy Azure.

### <a name="create-a-new-expressroute-circuit"></a>Tworzenie nowego obwodu ExpressRoute

> [!IMPORTANT]
> Obwód usługi ExpressRoute jest rozliczany od momentu, gdy zostanie wystawiony klucz usług. Upewnij się, że ta operacja jest wykonywana, gdy dostawca łączności jest gotowy do aprowizacji obwodu.

Obwód ExpressRoute można utworzyć, wybierając opcję tworzenia nowego zasobu. 

1. W menu witryny Azure Portal wybierz pozycję **Utwórz zasób**. Wybierz pozycję **Sieć**  >  **ExpressRoute**, jak pokazano na poniższej ilustracji:

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/create-expressroute-circuit-menu.png" alt-text="Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)":::

2. Po wybraniu **ExpressRoute**zostanie wyświetlona strona **Tworzenie ExpressRoute** . Podaj **grupę zasobów**, **region**i  **nazwę** obwodu. Następnie wybierz pozycję **Next (dalej): Configuration >**.

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-create-basic.png" alt-text="Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)":::

3. Podczas wypełniania wartości na tej stronie upewnij się, że określono poprawną warstwę SKU (lokalna, standardowa lub Premium) i model rozliczeń pomiaru danych (nieograniczoną lub taryfową).

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-create-configuration.png" alt-text="Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)":::
    
    * **Typ portu** określa, czy nawiązujesz połączenie z dostawcą usług lub bezpośrednio w sieci globalnej firmy Microsoft w lokalizacji komunikacji równorzędnej.
    * **Utwórz nowy lub Importuj z klasycznego** określa, czy jest tworzony nowy obwód, czy też w przypadku migrowania klasycznego obwodu do Azure Resource Manager.
    * **Dostawca** jest usługodawcą internetowym, z którego będziesz żądać usługi.
    * **Lokalizacja komunikacji równorzędnej** to fizyczna lokalizacja, w której użytkownik nawiązuje komunikację równorzędną z firmą Microsoft.

    > [!IMPORTANT]
    > Lokalizacja komunikacji równorzędnej wskazuje [lokalizację fizyczną](expressroute-locations.md) , w której jest przeprowadzana Komunikacja równorzędna z firmą Microsoft. **Nie** jest to połączenie z właściwością "Location", która odnosi się do lokalizacji geograficznej, w której znajduje się dostawca zasobów sieciowych platformy Azure. Chociaż nie są one powiązane, dobrym sposobem jest wybranie dostawcy zasobów sieciowych geograficznie blisko lokalizacji komunikacji równorzędnej obwodu.

    * **Jednostka SKU** określa, czy włączono dodatek ExpressRoute Local, ExpressRoute Standard lub ExpressRoute Premium. Możesz określić **lokalną** jednostkę SKU, **Standard** , aby uzyskać standardową jednostkę SKU lub **Premium** dla dodatku Premium. Można zmienić jednostkę SKU, aby włączyć dodatek Premium.
    > [!IMPORTANT]
    > Nie można zmienić jednostki SKU z warstwy **standardowa/Premium** na **lokalną**.
    
    * **Model rozliczeń** określa typ rozliczeń. Możesz określić **taryfowy** plan taryfowy i **nieograniczone** dla nieograniczonego planu taryfowego. Typ rozliczeń można zmienić z **taryfowe** na **nieograniczony**.

    > [!IMPORTANT]
    > Nie można zmienić typu z **nieograniczonego** na **taryfowy**.

    * Wartość **Zezwalaj na użycie klasycznej** sieci wirtualnej będzie zawierać link do obwodu.

### <a name="view-the-circuits-and-properties"></a>Wyświetlanie obwodów i właściwości

**Wyświetl wszystkie obwody**

Można wyświetlić wszystkie utworzone przez Ciebie obwody, wybierając pozycję **wszystkie usługi > sieć > obwody ExpressRoute** w menu po lewej stronie.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-menu.png" alt-text="Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)":::

W tym miejscu będą wyświetlane wszystkie obwody usługi ExpressRoute utworzone w ramach subskrypcji.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-list.png" alt-text="Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)":::

**Wyświetl właściwości**

Możesz wyświetlić właściwości obwodu, zaznaczając go. Na stronie **Omówienie** obwodu w polu klucz usługi pojawi się klucz usługi. Zapoznaj się z kluczem usługi dla obwodu i podaj jej dostawcę usług w celu ukończenia procesu aprowizacji. Klucz usługi jest specyficzny dla obwodu.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-overview.png" alt-text="Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)":::

### <a name="send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>Wyślij klucz usługi do dostawcy połączenia w celu aprowizacji

Na tej stronie **stan dostawcy** zapewnia bieżący stan aprowizacji po stronie Dostawca usług. **Stan obwodu** zapewnia stan po stronie firmy Microsoft. Aby uzyskać więcej informacji o Stanach aprowizacji obwodów, zobacz artykuł [przepływy pracy](expressroute-workflows.md#expressroute-circuit-provisioning-states) .

Podczas tworzenia nowego obwodu ExpressRoute obwód jest w następującym stanie:

Stan dostawcy: **nie zainicjowano obsługi administracyjnej**<BR>
Stan obwodu: **włączony**

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-overview-provisioning-state.png" alt-text="Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)":::

Obwód zostanie zmieniony na następujący stan, gdy dostawca łączności aktualnie go włączy:

Stan dostawcy: **Inicjowanie obsługi**<BR>
Stan obwodu: **włączony**

Aby użyć obwodu ExpressRoute, musi on być w następującym stanie:

Stan dostawcy: **zainicjowano**<BR>
Stan obwodu: **włączony**

### <a name="periodically-check-the-status-and-the-state-of-the-circuit-key"></a>Okresowe sprawdzanie stanu i stanu klucza obwodu

Możesz wyświetlić właściwości obwodu, który Cię interesuje, zaznaczając go. Sprawdź **stan dostawcy** i upewnij się, że został przeniesiony do programu z **obsługą administracyjną** przed kontynuowaniem.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png" alt-text="Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)":::

### <a name="create-your-routing-configuration"></a>Utwórz konfigurację routingu

Aby uzyskać instrukcje krok po kroku, zapoznaj się z artykułem [Konfiguracja routingu obwodów usługi ExpressRoute](expressroute-howto-routing-portal-resource-manager.md) w celu utworzenia i zmodyfikowania komunikacji równorzędnej obwodu.

> [!IMPORTANT]
> Te instrukcje dotyczą tylko obwodów, które są tworzone za pomocą dostawców usług oferujących usługi łączności warstwy 2. Jeśli używasz dostawcy usług oferującego zarządzane usługi warstwy 3 (zazwyczaj sieć VPN sieci IP, na przykład MPLS), dostawca łączności konfiguruje i zarządza routingiem.

### <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>Łączenie sieci wirtualnej z obwodem usługi ExpressRoute

Następnie połącz sieć wirtualną z obwodem ExpressRoute. Podczas pracy z modelem wdrażania Menedżer zasobów należy użyć artykułu [łączenie sieci wirtualnych do obwodów usługi ExpressRoute](expressroute-howto-linkvnet-arm.md) .

## <a name="getting-the-status-of-an-expressroute-circuit"></a><a name="status"></a>Pobieranie stanu obwodu ExpressRoute

Stan obwodu można wyświetlić, zaznaczając go i wyświetlając stronę przegląd.

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Modyfikowanie obwodu ExpressRoute

Niektóre właściwości obwodu usługi ExpressRoute można modyfikować bez wpływu na łączność. Na stronie **Konfiguracja** można modyfikować przepustowość, jednostkę SKU, model rozliczeń i zezwalać na działania klasyczne. Aby uzyskać informacje o limitach i ograniczeniach, zobacz [często zadawane pytania](expressroute-faqs.md)dotyczące usługi ExpressRoute.

W przypadku braku przestojów można wykonać następujące zadania:

* Włącz lub Wyłącz dodatek ExpressRoute Premium dla obwodu usługi ExpressRoute.

> [!IMPORTANT]
  > Zmiana jednostki SKU z warstwy **standardowa/Premium** na **lokalną** nie jest obsługiwana.

* Zwiększ przepustowość obwodu ExpressRoute, pod warunkiem, że na porcie jest dostępna pojemność.

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

Aby zmodyfikować obwód ExpressRoute, wybierz pozycję **Konfiguracja**.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-configuration.png" alt-text="Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)":::

## <a name="deprovisioning-an-expressroute-circuit"></a><a name="delete"></a>Anulowanie aprowizacji obwodu ExpressRoute

Jeśli stan aprowizacji dostawcy usługi obwodu ExpressRoute jest inicjowany lub **Zainicjowano obsługę administracyjną** , należy skontaktować się z dostawcą usług w celu **anulowania aprowizacji** obwodu po stronie. Nadal rezerwujemy zasoby i obciążamy Cię, dopóki dostawca usług nie ukończy anulowania aprowizacji obwodu i powiadamia nas.

> [!NOTE]
>* Przed cofnięciem aprowizacji należy odłączyć *wszystkie sieci wirtualne* od obwodu usługi ExpressRoute. Jeśli ta operacja nie powiedzie się, sprawdź, czy wszystkie sieci wirtualne są połączone z obwodem.
>* Jeśli dostawca usług anulował obsługę administracyjną obwodu (stan aprowizacji dostawcy usług jest ustawiony na **nieinicjowany**), można usunąć obwód. Spowoduje to zatrzymanie naliczania opłat za obwód.


## <a name="clean-up-resources"></a><a name="cleanup"></a>Czyszczenie zasobów

Obwód ExpressRoute można usunąć, wybierając ikonę **Usuń** . 

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-delete.png" alt-text="Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)":::

## <a name="next-steps"></a>Następne kroki

Po utworzeniu obwodu przejdź do następnego kroku:

> [!div class="nextstepaction"]
> [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
