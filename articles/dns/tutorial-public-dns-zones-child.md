---
title: Tworzenie podrzędnych stref DNS platformy Azure
titleSuffix: Azure DNS
description: Samouczek dotyczący sposobu tworzenia podrzędnych stref DNS w Azure Portal.
author: jonbeck
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.topic: tutorial
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 7/16/2020
ms.author: jonbeck
ms.openlocfilehash: 3f35d39634470ccacffa4d35c272a82725e9001c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89088157"
---
# <a name="tutorial-creating-a-new-child-dns-zone"></a>Samouczek: Tworzenie nowej podrzędnej strefy DNS

Ten samouczek zawiera informacje na temat wykonywania następujących czynności: 

> [!div class="checklist"]
> * Logowanie do witryny Azure Portal.
> * Tworzenie podrzędnej strefy DNS za pośrednictwem nowej strefy DNS.
> * Tworzenie podrzędnej strefy DNS za pośrednictwem nadrzędnej strefy DNS.
> * Weryfikowanie delegowania NS dla nowej podrzędnej strefy DNS.



## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją.  Jeśli nie masz konta, możesz [utworzyć konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Istniejąca strefa nadrzędna Azure DNS.  

Na potrzeby tego samouczka będziemy używać contoso.com jako strefy nadrzędnej i subdomain.contoso.com jako nazwy domeny podrzędnej.  Zastąp *contoso.com* nazwą domeny nadrzędnej i *poddomeną* domeną podrzędną.  Jeśli nadrzędna strefa DNS nie została utworzona, zapoznaj się z instrukcjami [tworzenia strefy DNS przy użyciu Azure Portal](https://docs.microsoft.com/azure/dns/dns-getstarted-portal#create-a-dns-zone). 


## <a name="sign-in-to-azure-portal"></a>Logowanie do witryny Azure Portal

Zaloguj się w [witrynie Azure Portal](https://portal.azure.com/) przy użyciu danych konta Azure.
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto.

Istnieją dwa sposoby tworzenia podrzędnej strefy DNS.
1.  Za pomocą strony portalu "Tworzenie strefy DNS".
1.  Za pomocą strony konfiguracji nadrzędnej strefy DNS.


## <a name="create-child-dns-zone-via-create-dns-zone"></a>Tworzenie podrzędnej strefy DNS za pośrednictwem tworzenia strefy DNS

W tym kroku utworzymy nową podrzędną strefę DNS o nazwie **subdomain.contoso.com** i delegujesz ją do istniejącej nadrzędnej strefy DNS **contoso.com**. Strefa DNS zostanie utworzona przy użyciu kart na stronie **Tworzenie strefy DNS** .
1.  W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób**. Zostanie wyświetlone okno **Nowe**.
1.  Wybierz pozycję **Sieć**, a następnie wybierz pozycję **strefa DNS** , a następnie wybierz przycisk **Dodaj** .

1.  Na karcie **podstawowe** wpisz lub wybierz następujące wartości:
    * **Subskrypcja**: wybierz subskrypcję, w której ma zostać utworzona strefa.
    * **Grupa zasobów**: wprowadź istniejącą grupę zasobów lub Utwórz nową, wybierając pozycję **Utwórz nową** *, wprowadź wartość Grupa, a*następnie wybierz pozycję **OK**. Nazwa grupy zasobów musi być unikatowa w ramach subskrypcji platformy Azure.
    * Zaznacz to pole wyboru: **Ta strefa jest elementem podrzędnym istniejącej strefy już hostowanej w Azure DNS**
    * **Subskrypcja strefy nadrzędnej**: z tego menu rozwijanego Wyszukaj i/lub wybierz nazwę subskrypcji, w ramach której utworzono nadrzędną strefę DNS *contoso.com* .
    * **Strefa nadrzędna**: na pasku wyszukiwania wpisz *contoso.com* , aby załadować ją na liście rozwijanej. Po załadowaniu wybierz pozycję *contoso.com* z listy rozwijanej.
    * **Nazwa:** Dla tego przykładu wpisz *poddomenę* . Zwróć uwagę, że nazwa nadrzędnej strefy DNS *contoso.com* jest automatycznie dodawana jako sufiks do nazwy, gdy wybieramy strefę nadrzędną w powyższym kroku.

1. Wybierz pozycję **Dalej: Przeglądanie i tworzenie**.
1. Na karcie **Recenzja + tworzenie** Przejrzyj podsumowanie, popraw błędy walidacji, a następnie wybierz pozycję **Utwórz**.
Tworzenie strefy może potrwać kilka minut.

 
    :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-dns-zone-inline.png" alt-text="Zrzut ekranu przedstawiający stronę tworzenie strefy DNS." lightbox="./media/dns-delegate-domain-azure-dns/create-dns-zone-expanded.png":::

## <a name="create-child-dns-zone-via-parent-dns-zone-overview-page"></a>Tworzenie podrzędnej strefy DNS za pośrednictwem strony Przegląd nadrzędnej strefy DNS
Możesz również utworzyć nową podrzędną strefę DNS i delegować ją do nadrzędnej strefy DNS przy użyciu przycisku **strefy podrzędnej** ze strony Przegląd strefy nadrzędnej. Przy użyciu tego przycisku automatycznie wstępnie wypełniane są parametry nadrzędne dla strefy podrzędnej. 

1.  W Azure Portal w obszarze **wszystkie zasoby**Otwórz strefę DNS *contoso.com* w **grupie zasobów zasobu** . Możesz wprowadzić *contoso.com* w polu **Filtruj według nazwy** , aby łatwiej je znaleźć.
1.  Na stronie Przegląd strefy DNS wybierz przycisk **+ strefa podrzędna** .

      :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-inline.png" alt-text="Zrzut ekranu przedstawiający stronę tworzenie strefy DNS." border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-expanded.png":::

1.  Następnie zostanie otwarta strona tworzenie strefy DNS. Opcja strefy podrzędnej jest już zaznaczona, a subskrypcja strefy nadrzędnej i strefa nadrzędna zostały już wypełnione na tej stronie.
1.  Wpisz nazwę jako *element podrzędny* dla tego przykładu samouczka. Zwróć uwagę, że nazwa strefy nadrzędnej DNS contoso.com jest automatycznie dodawana jako prefiks do nazwy.
1.  Wybierz pozycję **Dalej: Tagi** , a następnie kliknij przycisk **Dalej: przegląd + Utwórz**.
1.  Na karcie **Recenzja + tworzenie** Przejrzyj podsumowanie, popraw błędy walidacji, a następnie wybierz pozycję **Utwórz**.

    :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-dns-zone-child-inline.png" alt-text="Zrzut ekranu przedstawiający stronę tworzenie strefy DNS." border="true"  lightbox="./media/dns-delegate-domain-azure-dns/create-dns-zone-child-expanded.png":::
## <a name="verify-child-dns-zone"></a>Weryfikuj podrzędną strefę DNS
Teraz masz utworzoną nową podrzędną strefę DNS *subdomain.contoso.com* . Aby sprawdzić, czy delegowanie poszło prawidłowo, sprawdź, czy rekordy serwer nazw (NS) dla strefy podrzędnej są w strefie nadrzędnej, zgodnie z poniższym opisem.  

**Pobieranie serwerów nazw podrzędnej strefy DNS:**

1.  W Azure Portal w obszarze **wszystkie zasoby**Otwórz strefę DNS *subdomain.contoso.com* w **grupie zasobów zasobu** . Możesz wprowadzić *subdomain.contoso.com* w polu **Filtruj według nazwy** , aby łatwiej je znaleźć.
1.  Pobierz serwery nazw ze strony Przegląd strefy DNS. W tym przykładzie do contoso.com strefy zostały przypisane serwery nazw *ns1-08.Azure-DNS.com, ns2-08.Azure-DNS.NET, NS3-08.Azure-DNS.org*i *NS4-08.Azure-DNS.info*:

      :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-inline.png" alt-text="Zrzut ekranu przedstawiający stronę tworzenie strefy DNS." border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-expanded.png":::
**Sprawdź rekord NS w strefie nadrzędnej DNS:**

Teraz w tym kroku przejdziemy do *contoso.com* nadrzędnej strefy DNS i sprawdź, czy został utworzony wpis zestawu rekordów NS dla stref podrzędnych serwery nazw.

1. W Azure Portal w obszarze **wszystkie zasoby**Otwórz strefę DNS contoso.com w **grupie zasobów zasobu** . Możesz wprowadzić contoso.com w polu **Filtruj według nazwy** , aby łatwiej je znaleźć.
1.  Na stronie Przegląd stref DNS *contoso.com* Sprawdź zestaw rekordów.
1.  Ten zestaw rekordów typu NS i nazwa poddomeny jest już utworzony w nadrzędnej strefie DNS. Sprawdź wartości dla tego zestawu rekordów, podobnie jak lista serwer nazw, która została pobrana z podrzędnej strefy DNS w powyższym kroku.

     :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-validate-inline.png" alt-text="Zrzut ekranu przedstawiający stronę tworzenie strefy DNS." border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-validate-expanded.png":::
## <a name="clean-up-resources"></a>Czyszczenie zasobów
Jeśli zasoby utworzone w tym samouczku nie są już potrzebne, usuń je, usuwając **grupę zasobów.** Otwórz grupę **zasobów zasobu** i wybierz pozycję **Usuń grupę zasobów**.



## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Scenariusze Azure DNS Private Zones](private-dns-scenarios.md)
