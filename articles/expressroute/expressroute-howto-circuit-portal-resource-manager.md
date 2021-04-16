---
title: 'Szybki start: tworzenie i modyfikowanie obwodu za pomocą expressRoute — Azure Portal'
description: W tym przewodniku Szybki start dowiesz się, jak utworzyć, aprowizować, weryfikować, aktualizować, usuwać i coprowizować obwód usługi ExpressRoute przy użyciu Azure Portal.
services: expressroute
author: duongau
ms.author: duau
ms.date: 10/21/2020
ms.topic: quickstart
ms.service: expressroute
ms.custom:
- mode-portal
ms.openlocfilehash: f62b60a9c33cd1b813b8f3307ec82d4242be168b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534800"
---
# <a name="quickstart-create-and-modify-an-expressroute-circuit"></a>Szybki start: tworzenie i modyfikowanie obwodu expressRoute

W tym przewodniku Szybki start pokazano, jak utworzyć obwód usługi ExpressRoute przy użyciu Azure Portal i Azure Resource Manager wdrożenia. Możesz również sprawdzić stan, zaktualizować, usunąć lub coprowizować obwód.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Przed rozpoczęciem [konfiguracji przejrzyj wymagania wstępne](expressroute-prerequisites.md) [i](expressroute-workflows.md) przepływy pracy.
* Możesz [obejrzeć film wideo przed](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) rozpoczęciem, aby lepiej zrozumieć kroki.

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Tworzenie i aprowizować obwód usługi ExpressRoute

### <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Przejdź w przeglądarce do witryny [Azure Portal](https://portal.azure.com) i zaloguj się przy użyciu konta platformy Azure.

### <a name="create-a-new-expressroute-circuit"></a>Tworzenie nowego obwodu expressRoute

> [!IMPORTANT]
> Obwód usługi ExpressRoute jest rozliczany od momentu, gdy zostanie wystawiony klucz usługi. Upewnij się, że tę operację wykonujesz, gdy dostawca połączenia jest gotowy do aprowizowania obwodu.

Obwód expressRoute można utworzyć, wybierając opcję utworzenia nowego zasobu. 

1. W menu witryny Azure Portal wybierz pozycję **Utwórz zasób**. Wybierz **pozycję Sieć**  >  **ExpressRoute,** jak pokazano na poniższej ilustracji:

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/create-expressroute-circuit-menu.png" alt-text="Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)":::

2. Po wybraniu opcji **ExpressRoute** zobaczysz stronę **Tworzenie expressRoute.** Podaj **grupę zasobów,** **region**  **i nazwę** obwodu. Następnie wybierz **pozycję Dalej: konfiguracja >**.

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-create-basic.png" alt-text="Konfigurowanie grupy zasobów i regionu":::

3. Podczas wypełniania wartości na tej stronie upewnij się, że określono poprawną warstwę SKU (Lokalna, Standardowa lub Premium) oraz model rozliczeń pomiaru danych (nieograniczony lub mierzony).

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-create-configuration.png" alt-text="Konfigurowanie obwodu":::
    
    * **Typ portu** określa, czy łączysz się z dostawcą usług, czy bezpośrednio z siecią globalną firmy Microsoft w lokalizacji komunikacji równorzędnej.
    * **Tworzenie nowego obwodu** lub importowanie z wersji klasycznej określa, czy jest tworzony nowy obwód, czy migrujesz obwód klasyczny do Azure Resource Manager.
    * **Dostawca** to dostawca usług internetowych, od których będziesz żądać usługi.
    * **Lokalizacja komunikacji równorzędnej** to fizyczna lokalizacja komunikacji równorzędnej z firmą Microsoft.

    > [!IMPORTANT]
    > Lokalizacja komunikacji równorzędnej wskazuje lokalizację [fizyczną,](expressroute-locations.md) w której jest nasyłana komunikacja równorzędna z firmą Microsoft. Nie jest **to połączone** z właściwością "Location", która odnosi się do lokalizacji geograficznej, w której znajduje się dostawca zasobów sieciowych platformy Azure. Chociaż nie są one powiązane, dobrym rozwiązaniem jest wybranie dostawcy zasobów sieciowych geograficznie blisko lokalizacji komunikacji równorzędnej obwodu.

    * **SKU** określa, czy jest włączony dodatek ExpressRoute w wersji Premium, lokalny, expressRoute, czy premium. Możesz określić wartość **Lokalna,** aby uzyskać lokalną wersję SKU, **Standardowa,** aby uzyskać standardową wersję SKU lub **Premium** dla dodatku Premium. Możesz zmienić wersję SKU, aby włączyć dodatek Premium.
    > [!IMPORTANT]
    > Nie można zmienić wersji **standardowej/premium** na **lokalną.**
    
    * **Model rozliczeń** określa typ rozliczeń. Możesz określić wartość **Mierzone dla** taryfowego planu danych i wartość **Bez ograniczeń** dla nieograniczonego planu danych. Typ rozliczeń można zmienić z **Mierzone na** **Bez ograniczeń.**

    > [!IMPORTANT]
    > Nie można zmienić typu z **Nieograniczone na** **Mierzone.**

    * **Zezwalaj na operację klasyczną** umożliwi łączenie klasycznych sieci wirtualnych z obwodem.

### <a name="view-the-circuits-and-properties"></a>Wyświetlanie obwodów i właściwości

**Wyświetl wszystko obwodów**

Wszystkie utworzone obwody można wyświetlić, wybierając pozycję Wszystkie usługi > **sieciowe > Obwody usługi ExpressRoute** w menu po lewej stronie.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-menu.png" alt-text="Menu obwodu expressroute":::

W tym miejscu zostaną wyświetlone wszystkie obwody usługi ExpressRoute utworzone w ramach subskrypcji.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-list.png" alt-text="Lista obwodów expressroute":::

**Wyświetlanie właściwości**

Możesz wyświetlić właściwości obwodu, wybierając go. Na stronie **Przegląd** obwodu klucz usługi jest wyświetlany w polu klucz usługi. Zapoznaj się z kluczem usługi dla obwodu i podaj go dostawcy usług, aby ukończyć proces aprowacji. Klucz usługi jest specyficzny dla Obwodu.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-overview.png" alt-text="Wyświetl właściwości":::

### <a name="send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>Wyślij klucz usługi do dostawcy łączności w celu aprowizowania

Na tej stronie **stan dostawcy** zawiera bieżący stan aprowizowania po stronie dostawcy usług. **Stan obwodu** zawiera stan po stronie firmy Microsoft. Aby uzyskać więcej informacji o stanach aprowowania obwodu, zobacz [artykuł Przepływy](expressroute-workflows.md#expressroute-circuit-provisioning-states) pracy.

Podczas tworzenia nowego obwodu expressRoute obwód jest w następującym stanie:

Stan dostawcy: **Nie aprowizowane**<BR>
Stan obwodu: **Włączone**

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-overview-provisioning-state.png" alt-text="Rozpoczyna proces aprowacji":::

Obwód zmieni się na następujący, gdy dostawca połączenia aktualnie go dla Ciebie wł.

Stan dostawcy: **Aprowizowanie**<BR>
Stan obwodu: **Włączone**

Aby można było korzystać z obwodu expressRoute, musi on mieć następujący stan:

Stan dostawcy: **Aprowizowana**<BR>
Stan obwodu: **Włączone**

### <a name="periodically-check-the-status-and-the-state-of-the-circuit-key"></a>Okresowe sprawdzanie stanu i stanu klucza obwodu

Możesz wyświetlić właściwości obwodu, który Cię interesuje, wybierając go. Przed **kontynuowaniem sprawdź stan dostawcy** i upewnij się, że został **on** przeniesiony do aprowizowanych.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png" alt-text="Stan obwodu i dostawcy":::

### <a name="create-your-routing-configuration"></a>Tworzenie konfiguracji routingu

Aby uzyskać instrukcje krok po kroku, zapoznaj się z artykułem [ExpressRoute circuit routing configuration (Konfiguracja routingu](expressroute-howto-routing-portal-resource-manager.md) obwodu usługi ExpressRoute), aby utworzyć i zmodyfikować komunikacji równorzędne obwodów.

> [!IMPORTANT]
> Te instrukcje dotyczą tylko obwodów utworzonych za pomocą dostawców usług, którzy oferują usługi łączności warstwy 2. Jeśli używasz dostawcy usług, który oferuje usługi zarządzane w warstwie 3 (zazwyczaj sieć VPN ip, na przykład MPLS), dostawca połączenia konfiguruje routing i zarządza tym routingiem.

### <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>Łączenie sieci wirtualnej z obwodem usługi ExpressRoute

Następnie połącz sieć wirtualną z obwodem usługi ExpressRoute. Podczas pracy z modelem wdrażania usługi Resource Manager artykuł Linking virtual [networks to ExpressRoute circuits](expressroute-howto-linkvnet-arm.md) (Łączenie sieci wirtualnych z obwodami usługi ExpressRoute).

## <a name="getting-the-status-of-an-expressroute-circuit"></a><a name="status"></a>Pobieranie stanu obwodu expressRoute

Stan obwodu można wyświetlić, wybierając go i wyświetlając stronę Przegląd.

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Modyfikowanie obwodu expressRoute

Niektóre właściwości obwodu usługi ExpressRoute można modyfikować bez wpływu na łączność. Na stronie Konfiguracja można zmodyfikować przepustowość, sku, model rozliczeń i zezwolić na operacje **klasyczne.** Aby uzyskać informacje na temat limitów i ograniczeń, zobacz [ExpressRoute FAQ (ExpressRoute — często zadawane pytania).](expressroute-faqs.md)

Można wykonać następujące zadania bez przestoju:

* Włączanie lub wyłączanie dodatku usługi ExpressRoute Premium dla obwodu usługi ExpressRoute.

> [!IMPORTANT]
  > Zmiana wersji SKU z **Standardowa/Premium** **na Lokalna** nie jest obsługiwana.

* Zwiększ przepustowość obwodu usługi ExpressRoute, pod warunkiem, że na porcie jest dostępna pojemność.

  > [!IMPORTANT]
  > Nie jest obsługiwana klasyfikacja przepustowości obwodu.

* Zmień plan pomiarów z *Dane taryfowe na* Nieograniczone *dane.*

  > [!IMPORTANT]
  > Zmiana planu pomiaru z **Nieograniczone dane** na **Dane taryfowe** nie jest obsługiwana.

* Można włączyć i wyłączyć opcję *Zezwalaj na operacje klasyczne.*
  > [!IMPORTANT]
  > W przypadku niewystarczającej pojemności istniejącego portu może być trzeba ponownie utworzyć obwód usługi ExpressRoute. Nie można uaktualnić obwodu, jeśli w tej lokalizacji nie ma dostępnej dodatkowej pojemności.
  >
  > Chociaż można bezproblemowo uaktualnić przepustowość, nie można zmniejszyć przepustowości obwodu usługi ExpressRoute bez zakłóceń. Ograniczenie przepustowości wymaga coprowizowania obwodu expressRoute, a następnie ponownego aprowizowania nowego obwodu expressRoute.
  >
  > Wyłączenie operacji dodatku Premium może się nie powieść, jeśli używasz zasobów większych niż dozwolone dla obwodu standardowego.

Aby zmodyfikować obwód usługi ExpressRoute, wybierz pozycję **Konfiguracja**.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-configuration.png" alt-text="Modyfikowanie obwodu":::

## <a name="deprovisioning-an-expressroute-circuit"></a><a name="delete"></a>Coprowizowanie obwodu expressRoute

Jeśli stan aprowizowania dostawcy usług  obwodu  usługi ExpressRoute to Aprowizowanie lub Aprowizowanie, musisz współpracować z dostawcą usług, aby coprowizować obwód po jego stronie. Nadal rezerwujemy zasoby i naliczamy rachunek, dopóki dostawca usług nie zakończy aprowizowania obwodu i powiadomi nas.

> [!NOTE]
>* Przed cofzeniem *aprowizowania* należy odłączyć wszystkie sieci wirtualne od obwodu usługi ExpressRoute. Jeśli ta operacja nie powiedzie się, sprawdź, czy jakiekolwiek sieci wirtualne są połączone z obwodem.
>* Jeśli dostawca usług coprowizował obwód (stan aprowizowania dostawcy usług jest ustawiony na Nie **aprowizowany),** możesz usunąć obwód. Spowoduje to zatrzymanie naliczania opłat za obwód.


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Obwód expressRoute można usunąć, wybierając **ikonę** Usuń. Przed podjęciem czynności upewnij się, że dostawca ma *stan Nie aprowizowany.*

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-delete.png" alt-text="Usuwanie obwodu":::

## <a name="next-steps"></a>Następne kroki

Po utworzeniu obwodu przejdź do następnego kroku:

> [!div class="nextstepaction"]
> [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
