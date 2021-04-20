---
title: 'Samouczek: tworzenie stref podrzędnego DNS platformy Azure'
titleSuffix: Azure DNS
description: Samouczek dotyczący tworzenia stref podrzędnego SYSTEMU DNS w Azure Portal.
author: jonbeck
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.topic: tutorial
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 04/19/2021
ms.author: jonbeck
ms.openlocfilehash: 283ff2786a0b63c6263c62a13e27cce92c2368dd
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107737392"
---
# <a name="tutorial-creating-a-new-child-dns-zone"></a>Samouczek: tworzenie nowej podrzędnej strefy DNS

Ten samouczek zawiera informacje na temat wykonywania następujących czynności: 

> [!div class="checklist"]
> * Logowanie się w witrynie Azure Portal.
> * Tworzenie podrzędnej strefy DNS za pośrednictwem nowej strefy DNS.
> * Tworzenie podrzędnej strefy DNS za pośrednictwem nadrzędnej strefy DNS.
> * Weryfikowanie delegowania NS dla nowej podrzędnej strefy DNS.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją.  Jeśli nie masz konta, możesz utworzyć je [bezpłatnie.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Istniejąca strefa Azure DNS nadrzędnej.  

W tym samouczku użyjemy contoso.com jako strefy nadrzędnej, a subdomain.contoso.com jako nazwy domeny podrzędnej.  Zastąp *contoso.com* swoją nazwą domeny nadrzędnej i *domeną podrzędną* domeną podrzędną.  Jeśli nie utworzono nadrzędnej strefy DNS, zobacz kroki tworzenia strefy [DNS](./dns-getstarted-portal.md#create-a-dns-zone)przy użyciu Azure Portal . 


## <a name="sign-in-to-azure-portal"></a>Logowanie do witryny Azure Portal

Zaloguj się w [witrynie Azure Portal](https://portal.azure.com/) przy użyciu danych konta Azure.
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto.

Istnieją dwa sposoby tworzenia podrzędnej strefy DNS.
1.  Za pośrednictwem strony portalu "Tworzenie strefy DNS".
1.  Za pośrednictwem strony konfiguracji nadrzędnej strefy DNS.

## <a name="create-child-dns-zone-via-create-dns-zone"></a>Tworzenie podrzędnej strefy DNS za pośrednictwem tworzenia strefy DNS

W tym kroku utworzymy nową podrzędną strefę DNS o nazwie **i subdomain.contoso.com** delegować ją do istniejącej nadrzędnej strefy DNS contoso.com **.** Strefę DNS utworzysz przy użyciu kart na **stronie Tworzenie strefy DNS.**
1.  W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób**. Zostanie wyświetlone okno **Nowe**.
1.  Wybierz **pozycję Sieć,** wybierz pozycję **Strefa DNS,** a następnie wybierz **przycisk** Dodaj.

1.  Na karcie **Podstawowe** wpisz lub wybierz następujące wartości:
    * **Subskrypcja:** wybierz subskrypcję, w ramach których chcesz utworzyć strefę.
    * **Grupa zasobów:** wprowadź istniejącą grupę zasobów lub utwórz nową, wybierając **pozycję Utwórz nową.** Wprowadź *nazwę MyResourceGroup* i wybierz przycisk **OK.** Nazwa grupy zasobów musi być unikatowa w ramach subskrypcji platformy Azure.
    * Zaznacz to pole wyboru: **Ta strefa jest elementem podrzędnym istniejącej strefy już hostowanej w Azure DNS**
    * **Subskrypcja strefy nadrzędnej:** z tej listy rozwijanej wyszukaj lub wybierz nazwę subskrypcji, w ramach której contoso.com *strefy* DNS.
    * **Strefa nadrzędna:** na pasku wyszukiwania wpisz *contoso.com,* aby załadować ją na liście rozwijanej. Po załadowaniu *wybierz contoso.com* z listy rozwijanej.
    * **Nazwa:** W *tym przykładzie samouczka* wpisz poddomenę. Zwróć uwagę, że nazwa nadrzędnej strefy DNS *contoso.com* jest automatycznie dodawana jako sufiks do nazwy po wybraniu strefy nadrzędnej w powyższym kroku.

1. Wybierz pozycję **Dalej: Przeglądanie i tworzenie**.
1. Na karcie **Przeglądanie + tworzenie** przejrzyj podsumowanie, popraw wszelkie błędy weryfikacji, a następnie wybierz pozycję **Utwórz**.
Tworzenie strefy może potrwać kilka minut.

    :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-dns-zone-inline.png" alt-text="Zrzut ekranu przedstawiający stronę tworzenia strefy DNS." lightbox="./media/dns-delegate-domain-azure-dns/create-dns-zone-expanded.png":::

## <a name="create-child-dns-zone-via-parent-dns-zone-overview-page"></a>Tworzenie podrzędnej strefy DNS za pośrednictwem strony przeglądu nadrzędnej strefy DNS
Można również utworzyć nową podrzędną strefę DNS i delegować ją do nadrzędnej strefy DNS przy użyciu przycisku Strefa podrzędna na stronie przeglądu strefy nadrzędnej.  Użycie tego przycisku powoduje automatyczne wstępne wypełnienie parametrów nadrzędnych dla strefy podrzędnej. 

1.  W Azure Portal w obszarze **Wszystkie zasoby** otwórz *strefę contoso.com* DNS w grupie zasobów **MyResourceGroup.** Możesz wprowadzić *contoso.com* w **polu Filtruj według** nazwy, aby łatwiej go znaleźć.
1.  Na stronie Przeglądu strefy DNS wybierz przycisk **+Strefa podrzędna.**

      :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-inline.png" alt-text="Zrzut ekranu przedstawiający przycisk strefy podrzędnej." border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-expanded.png":::

1.  Zostanie otwarta strona tworzenia strefy DNS. Opcja strefy podrzędnej jest już zaznaczona, a subskrypcja strefy nadrzędnej i strefa nadrzędna są wypełniane na tej stronie.
1.  W tym przykładzie *samouczka wpisz* nazwę jako podrzędną. Zauważ, że nazwa nadrzędnej strefy DNS jest contoso.com automatycznie dodawana jako prefiks do nazwy.
1.  Wybierz **pozycję Dalej: Tagi,** a następnie **pozycję Dalej: Przeglądanie + tworzenie.**
1.  Na karcie **Przeglądanie + tworzenie** przejrzyj podsumowanie, popraw wszelkie błędy weryfikacji, a następnie wybierz pozycję **Utwórz**.

    :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-dns-zone-child-inline.png" alt-text="Zrzut ekranu przedstawiający wybraną strefę podrzędną" border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-dns-zone-child-expanded.png":::

## <a name="verify-child-dns-zone"></a>Weryfikowanie podrzędnej strefy DNS
Teraz, gdy masz utworzoną nową podrzędną *strefę DNS subdomain.contoso.com* DNS. Aby sprawdzić, czy delegowanie zostało wykonane prawidłowo, należy sprawdzić rekordy nameserver(NS) dla strefy podrzędnej w strefie nadrzędnej, jak opisano poniżej.  

**Pobieranie serwerów nazw podrzędnej strefy DNS:**

1.  W witrynie Azure Portal obszarze **Wszystkie zasoby** otwórz subdomain.contoso.com *DNS* w grupie zasobów **MyResourceGroup.** Możesz wprowadzić *subdomain.contoso.com* w **polu Filtruj** według nazwy, aby łatwiej go znaleźć.
1.  Pobierz serwery nazw ze strony przeglądu strefy DNS. W tym przykładzie do strefy contoso.com serwery nazw *ns1-08.azure-dns.com, ns2-08.azure-dns.net, ns3-08.azure-dns.org* i *ns4-08.azure-dns.info:*

      :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-inline.png" alt-text="Zrzut ekranu przedstawiający serwery nazw strefy podrzędnej" border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-expanded.png":::
**Sprawdź rekord NS w nadrzędnej strefie DNS:**

Teraz w tym kroku przejdźmy  do nadrzędnej strefy DNS contoso.com i sprawdzimy, czy utworzono wpis zestawu rekordów NS dla serwerów nazw stref nadrzędnych.

1. W witrynie Azure Portal obszarze **Wszystkie** zasoby otwórz contoso.com DNS w grupie zasobów **MyResourceGroup.** Możesz wprowadzić contoso.com w **polu Filtruj według** nazwy, aby łatwiej go znaleźć.
1.  Na stronie *contoso.com* strefy DNS sprawdź zestawy rekordów.
1.  Zestaw rekordów typu NS i poddomeny nazw został już utworzony w nadrzędnej strefie DNS. Sprawdź wartości tego zestawu rekordów, które są podobne do listy nameserver pobranej z podrzędnej strefy DNS w powyższym kroku.

     :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-validate-inline.png" alt-text="Zrzut ekranu przedstawiający walidację serwerów nazw strefy podrzędnej" border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-validate-expanded.png":::
## <a name="clean-up-resources"></a>Czyszczenie zasobów
Jeśli nie potrzebujesz już zasobów utworzonych w tym samouczku, usuń je, usuwając grupę zasobów **MyResourceGroup.** Otwórz **grupę zasobów MyResourceGroup** i wybierz pozycję **Usuń grupę zasobów.**

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Scenariusze usługi Azure DNS Private Zones](private-dns-scenarios.md)
