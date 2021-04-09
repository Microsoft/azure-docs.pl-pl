---
title: Włączanie prywatnego dostępu za pomocą prywatnego linku (wersja zapoznawcza) — Portal
titleSuffix: Azure Digital Twins
description: Zapoznaj się z tematem Włączanie prywatnego dostępu do rozwiązań usługi Azure Digital bliźniaczych reprezentacji za pomocą prywatnego linku przy użyciu Azure Portal.
author: baanders
ms.author: baanders
ms.date: 1/25/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 2545915edf9e39b63100a2bb16bd34fa6777675c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "100417835"
---
# <a name="enable-private-access-with-private-link-preview-azure-portal"></a>Włącz prywatny dostęp za pomocą prywatnego linku (wersja zapoznawcza): Azure Portal

[!INCLUDE [digital-twins-private-link-selector.md](../../includes/digital-twins-private-link-selector.md)]

W tym artykule opisano różne sposoby [włączania prywatnego linku do prywatnego punktu końcowego dla wystąpienia usługi Azure Digital bliźniaczych reprezentacji](concepts-security.md#private-network-access-with-azure-private-link-preview) (obecnie dostępne w wersji zapoznawczej). Skonfigurowanie prywatnego punktu końcowego dla swojego wystąpienia usługi Azure Digital bliźniaczych reprezentacji umożliwia zabezpieczenie wystąpienia usługi Azure Digital bliźniaczych reprezentacji i wyeliminowanie opinii publicznej, a także uniknięcie eksfiltracji danych z [usługi azure Virtual Network (VNET)](../virtual-network/virtual-networks-overview.md).

W tym artykule omówiono proces przy użyciu [**Azure Portal**](https://portal.azure.com).

Poniżej przedstawiono kroki omówione w tym artykule: 
1. Włącz link prywatny i skonfiguruj prywatny punkt końcowy dla wystąpienia usługi Azure Digital bliźniaczych reprezentacji.
1. Wyłącz lub Włącz flagi dostępu do sieci publicznej, aby ograniczyć dostęp API tylko do połączeń prywatnych.

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było skonfigurować prywatny punkt końcowy, będzie potrzebna [**usługa Azure Virtual Network (VNET)**](../virtual-network/virtual-networks-overview.md) , w której można wdrożyć punkt końcowy. Jeśli nie masz jeszcze sieci wirtualnej, możesz skorzystać z jednego z [przewodników szybki start](../virtual-network/quick-create-portal.md) dla systemu Virtual Network Azure, aby go skonfigurować.

## <a name="add-a-private-endpoint-for-an-azure-digital-twins-instance"></a>Dodawanie prywatnego punktu końcowego dla wystąpienia usługi Azure Digital bliźniaczych reprezentacji 

W przypadku korzystania z [Azure Portal](https://portal.azure.com)można wybrać opcję włączenia prywatnego linku z prywatnym punktem końcowym dla wystąpienia usługi Azure Digital bliźniaczych reprezentacji jako części początkowej konfiguracji wystąpienia lub włączyć ją później w wystąpieniu, które już istnieje. 

Każda z tych metod tworzenia będzie mieć te same opcje konfiguracji i ten sam wynik końcowy dla danego wystąpienia. W tej sekcji opisano, jak wykonać każdą z nich.

>[!TIP]
> Możesz również skonfigurować prywatny punkt końcowy łącza za pomocą usługi link prywatny, a nie za pomocą swojego wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Zapewnia to również te same opcje konfiguracji i ten sam wynik końcowy.
>
> Aby uzyskać więcej informacji na temat konfigurowania prywatnych zasobów łączy, zobacz dokumentację linku prywatnego dla [Azure Portal](../private-link/create-private-endpoint-portal.md), [interfejsu wiersza polecenia platformy Azure](../private-link/create-private-endpoint-cli.md), [ARM](../private-link/create-private-endpoint-template.md)lub [PowerShell](../private-link/create-private-endpoint-powershell.md).

### <a name="add-a-private-endpoint-during-instance-creation"></a>Dodaj prywatny punkt końcowy podczas tworzenia wystąpienia

W tej sekcji zostanie włączony prywatny link z prywatnym punktem końcowym w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji, które jest aktualnie tworzone. Ta sekcja koncentruje się na krokach sieci procesu tworzenia; pełny Przewodnik tworzenia nowego wystąpienia usługi Azure Digital bliźniaczych reprezentacji można znaleźć w temacie [*How to: Set a instance and Authentication*](how-to-set-up-instance-portal.md).

Opcje link prywatny znajdują się na karcie **Sieć** w instalatorze wystąpienia.

Na tej karcie można włączyć prywatne punkty końcowe, wybierając opcję **prywatny punkt końcowy** dla **metody łączności**.

Spowoduje to dodanie sekcji o nazwie **połączenia prywatnego punktu końcowego** , w której można skonfigurować szczegóły prywatnego punktu końcowego. Aby kontynuować, wybierz przycisk **+ Dodaj** .

:::image type="content" source="media/how-to-enable-private-link/create-instance-networking-1.png" alt-text="Zrzut ekranu przedstawiający Azure Portal wyświetlenia karty sieć okna dialogowego Tworzenie zasobu dla usługi Azure Digital bliźniaczych reprezentacji. Istnieje wyróżnienie wokół nazwy karty, opcji prywatnego punktu końcowego dla metody łączności oraz przycisku + Dodaj, aby utworzyć nowe połączenie prywatnego punktu końcowego." lightbox="media/how-to-enable-private-link/create-instance-networking-1.png":::

Spowoduje to otwarcie strony, aby wprowadzić szczegóły nowego prywatnego punktu końcowego.

:::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-full.png" alt-text="Zrzut ekranu przedstawiający Azure Portal na stronie Tworzenie prywatnego punktu końcowego. Zawiera pola opisane poniżej.":::

1. Wypełnij pola wyboru dla **subskrypcji** i **grupy zasobów**. Ustaw **lokalizację** na tę samą lokalizację, w której będziesz używać sieci wirtualnej. Wybierz **nazwę** punktu końcowego, a dla **docelowych zasobów podrzędnych** wybierz pozycję *interfejs API*.

1. Następnie wybierz **sieć wirtualną** i **podsieć** , której chcesz użyć do wdrożenia punktu końcowego.

1. Na koniec wybierz, czy **zintegrować z prywatną strefą DNS**. Możesz użyć wartości domyślnej **tak** lub, aby uzyskać pomoc dotyczącą tej opcji, możesz skorzystać z linku w portalu, aby [dowiedzieć się więcej na temat integracji prywatnej usługi DNS](../private-link/private-endpoint-overview.md#dns-configuration).

Po wypełnieniu opcji konfiguracji naciśnij przycisk **OK** , aby zakończyć.

Spowoduje to powrót do karty **Sieć** w instalatorze wystąpienia usługi Azure Digital bliźniaczych reprezentacji, gdzie nowy punkt końcowy powinien być widoczny w obszarze * * połączenia prywatnych punktów końcowych.

:::image type="content" source="media/how-to-enable-private-link/create-instance-networking-2.png" alt-text="Zrzut ekranu przedstawiający Azure Portal wyświetlenia karty sieć okna dialogowego Tworzenie zasobu dla usługi Azure Digital bliźniaczych reprezentacji. Istnieje wyróżnienie wokół nowego połączenia prywatnego punktu końcowego, a przyciski nawigacji (przegląd + Utwórz, poprzedni, dalej: Zaawansowane)." lightbox="media/how-to-enable-private-link/create-instance-networking-2.png":::

Następnie możesz użyć dolnych przycisków nawigacji, aby kontynuować instalację pozostałej części wystąpienia.

### <a name="add-a-private-endpoint-to-an-existing-instance"></a>Dodaj prywatny punkt końcowy do istniejącego wystąpienia

Ta sekcja umożliwia włączenie prywatnego linku z prywatnym punktem końcowym dla wystąpienia usługi Azure Digital bliźniaczych reprezentacji, które już istnieje.

1. Najpierw przejdź do [Azure Portal](https://portal.azure.com) w przeglądarce. Wywołaj swoje wystąpienie usługi Azure Digital bliźniaczych reprezentacji, wyszukując jego nazwę na pasku wyszukiwania portalu.

1. Wybierz pozycję **Sieć (wersja zapoznawcza)** w menu po lewej stronie.

1. Przejdź do karty **połączenia prywatne punktu końcowego** .

1. Wybierz pozycję **+ prywatny punkt końcowy** , aby otworzyć Instalatora **tworzenia prywatnego punktu końcowego** .

    :::image type="content" source="media/how-to-enable-private-link/add-endpoint-digital-twins.png" alt-text="Zrzut ekranu przedstawiający Azure Portal wyświetlania strony sieci (wersja zapoznawcza) dla wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Karta połączenia prywatnego punktu końcowego jest wyróżniona, a przycisk + prywatny punkt końcowy jest również wyróżniony." lightbox="media/how-to-enable-private-link/add-endpoint-digital-twins.png":::

1. Na karcie  **podstawy**   wprowadź lub wybierz **subskrypcję** i **grupę zasobów** projektu oraz **nazwę** i **region** punktu końcowego. Region musi być taki sam jak region używanej sieci wirtualnej.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-1.png" alt-text="Zrzut ekranu przedstawiający Azure Portal wyświetlenia pierwszej karty (podstawowe) okna dialogowego Tworzenie prywatnego punktu końcowego. Zawiera pola opisane powyżej.":::

    Po zakończeniu wybierz przycisk **Dalej: >zasobów** , aby przejść do następnej karty.

1. Na karcie **zasób** wprowadź lub wybierz następujące informacje: 
    * **Metoda połączenia**: wybierz pozycję **Połącz z zasobem platformy Azure w moim katalogu** , aby wyszukać wystąpienie usługi Azure Digital bliźniaczych reprezentacji.
    * **Subskrypcja**: wprowadź subskrypcję.
    * **Typ zasobu**: wybierz pozycję **Microsoft. DigitalTwins/digitalTwinsInstances**
    * **Zasób**: wybierz nazwę wystąpienia usługi Azure Digital bliźniaczych reprezentacji.
    * **Docelowy zasób podrzędny**: Wybierz **interfejs API**.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-2.png" alt-text="Zrzut ekranu przedstawiający Azure Portal, w którym jest wyświetlana druga karta (zasób) okna dialogowego Tworzenie prywatnego punktu końcowego. Zawiera pola opisane powyżej.":::

    Po zakończeniu wybierz przycisk **Dalej: >konfiguracji** , aby przejść do następnej karty.    

1. Na karcie **Konfiguracja** wprowadź lub wybierz następujące informacje:
    * **Sieć wirtualna**: Wybierz sieć wirtualną.
    * **Podsieć**: wybierz podsieć z sieci wirtualnej.
    * **Integracja z prywatną strefą DNS**: Wybierz, czy **zintegrować z prywatną strefą DNS**. Możesz użyć wartości domyślnej **tak** lub, aby uzyskać pomoc dotyczącą tej opcji, możesz skorzystać z linku w portalu, aby [dowiedzieć się więcej na temat integracji prywatnej usługi DNS](../private-link/private-endpoint-overview.md#dns-configuration).
    W przypadku wybrania **opcji tak** można pozostawić domyślne informacje o konfiguracji.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-3.png" alt-text="Zrzut ekranu przedstawiający Azure Portal wyświetlania trzeciej karty (Konfiguracja) okna dialogowego Tworzenie prywatnego punktu końcowego. Zawiera pola opisane powyżej.":::

    Po zakończeniu możesz wybrać przycisk **Recenzja + tworzenie** , aby zakończyć instalację. 

1. Na karcie **Recenzja i tworzenie** Przejrzyj wybrane opcje i wybierz przycisk  **Utwórz** . 

Po zakończeniu wdrażania punktu końcowego powinien on zostać wyświetlony w połączeniach prywatnych punktów końcowych dla wystąpienia usługi Azure Digital bliźniaczych reprezentacji.

>[!TIP]
> Punkt końcowy może być również wyświetlany z poziomu prywatnego centrum linków w Azure Portal.

## <a name="disable--enable-public-network-access-flags"></a>Wyłącz/Włącz flagi dostępu do sieci publicznej

Można skonfigurować wystąpienie usługi Azure Digital bliźniaczych reprezentacji, aby odmówić wszystkich połączeń publicznych i zezwolić tylko na połączenia za pomocą prywatnych punktów końcowych w celu zwiększenia bezpieczeństwa sieci. Ta akcja jest wykonywana z **flagą dostępu do sieci publicznej**. 

Te zasady umożliwiają ograniczenie dostępu do interfejsu API tylko do połączeń prywatnych. Gdy flaga dostępu do sieci publicznej jest ustawiona na *wyłączone*, wszystkie wywołania interfejsu API REST do płaszczyzny danych wystąpienia bliźniaczych reprezentacji cyfrowych platformy Azure z chmury publicznej zostaną zwrócone `403, Unauthorized` . Alternatywnie, jeśli zasady są ustawione na *wyłączone* , a żądanie jest nawiązywane za pomocą prywatnego punktu końcowego, wywołanie interfejsu API powiedzie się.

W tym artykule pokazano, jak zaktualizować wartość flagi sieci przy użyciu [Azure Portal](https://portal.azure.com). Aby uzyskać instrukcje, jak to zrobić za pomocą interfejsu wiersza polecenia platformy Azure lub narzędzia ARMClient, zobacz [wersja interfejsu wiersza](how-to-enable-private-link-cli.md) polecenia w tym artykule.

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Aby wyłączyć lub włączyć dostęp do sieci publicznej w [Azure Portal](https://portal.azure.com), Otwórz Portal i przejdź do wystąpienia usługi Azure Digital bliźniaczych reprezentacji.

1. Wybierz pozycję **Sieć (wersja zapoznawcza)** w menu po lewej stronie.

1. Na karcie **dostęp publiczny** ustaw opcję **Zezwalaj na dostęp do sieci publicznej na** **wyłączone** lub **wszystkie sieci**.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-enable-private-link/network-flag-portal.png" alt-text="Zrzut ekranu przedstawiający Azure Portal wyświetlania strony sieci (wersja zapoznawcza) dla wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Karta dostęp publiczny jest wyróżniona i opcja wyboru, czy zezwalać na dostęp do sieci publicznej również jest wyróżniony." lightbox="media/how-to-enable-private-link/network-flag-portal.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

    Wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat prywatnego linku dla platformy Azure: 
* [*Co to jest usługa Azure Private Link Service?*](../private-link/private-link-service-overview.md)