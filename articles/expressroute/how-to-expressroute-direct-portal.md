---
title: 'Azure ExpressRoute: Konfigurowanie ExpressRoute Direct: Portal'
description: Ta strona ułatwia konfigurowanie usługi ExpressRoute Direct przy użyciu portalu.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 09/29/2020
ms.author: duau
ms.openlocfilehash: dac9df23209fa93b8060ad80d58fd66f7a356ecd
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2020
ms.locfileid: "91651483"
---
# <a name="create-expressroute-direct-using-the-azure-portal"></a>Utwórz ExpressRoute bezpośrednio przy użyciu Azure Portal

W tym artykule pokazano, jak utworzyć ExpressRoute bezpośrednio przy użyciu Azure Portal.
Usługa ExpressRoute Direct umożliwia bezpośrednie nawiązywanie połączenia z siecią globalną firmy Microsoft przy użyciu lokalizacji komunikacji równorzędnej strategicznie rozmieszczonych na całym świecie. Aby uzyskać więcej informacji, zobacz [About ExpressRoute Direct (Usługa ExpressRoute Direct)](expressroute-erdirect-about.md).

## <a name="before-you-begin"></a><a name="before"></a>Przed rozpoczęciem

Sprawdź, czy dostawca zasobów **Microsoft. Network** został zarejestrowany w Twojej subskrypcji. Rejestracja dostawcy zasobów umożliwia skonfigurowanie subskrypcji do pracy z dostawcą zasobów.

1. Uzyskaj dostęp do ustawień subskrypcji zgodnie z opisem w temacie [dostawcy zasobów platformy Azure i typy](../azure-resource-manager/management/resource-providers-and-types.md).
1. W ramach subskrypcji dla **dostawców zasobów**Sprawdź, czy dostawca **Microsoft. Network** wskazuje **zarejestrowany** stan. Jeśli dostawcy zasobów Microsoft. Network nie ma na liście zarejestrowanych dostawców, Dodaj go.

## <a name="create-expressroute-direct"></a><a name="create-erdir"></a>Utwórz ExpressRoute bezpośrednie

1. W menu [Azure Portal](https://portal.azure.com) lub na stronie **głównej** wybierz pozycję **Utwórz zasób**.

1. Na stronie **Nowy** w polu Wyszukaj w ***witrynie Marketplace*** wpisz **ExpressRoute Direct**, a następnie wybierz pozycję **Enter** , aby przejść do wyników wyszukiwania.

1. Z wyników wybierz pozycję **ExpressRoute Direct (bezpośrednie**).

1. Na stronie **ExpressRoute Direct** wybierz pozycję **Utwórz** , aby otworzyć stronę **Tworzenie ExpressRoute Direct** .

1. Zacznij od wypełnienia pól na stronie **podstawy** .

    :::image type="content" source="./media/how-to-expressroute-direct-portal/basics.png" alt-text="Strona Podstawy":::

    * **Subskrypcja**: subskrypcja platformy Azure, która ma zostać użyta do utworzenia nowej usługi ExpressRoute Direct. Zasoby ExpressRoute Direct i obwody usługi ExpressRoute muszą znajdować się w tej samej subskrypcji.
    * **Grupa zasobów**: Grupa zasobów platformy Azure, w której zostanie utworzony nowy zasób ExpressRoute Direct. Jeśli nie masz istniejącej grupy zasobów, możesz utworzyć nową.
    * **Region**: region publiczny platformy Azure, w którym zostanie utworzony zasób.
    * **Name**: Nazwa nowego zasobu ExpressRoute Direct.

1. Następnie wypełnij pola na stronie **Konfiguracja** .

    :::image type="content" source="./media/how-to-expressroute-direct-portal/configuration.png" alt-text="Strona Podstawy":::

    * **Lokalizacja komunikacji równorzędnej**: Lokalizacja komunikacji równorzędnej, w której zostanie nawiązane połączenie z zasobem Direct ExpressRoute. Aby uzyskać więcej informacji na temat lokalizacji komunikacji równorzędnej, przejrzyj [lokalizacje ExpressRoute](expressroute-locations-providers.md).
   * **Przepustowość**: przepustowość pary portów, która ma zostać zarezerwowana. Program ExpressRoute Direct obsługuje opcje przepustowości 10 GB i 100 GB. Jeśli żądana przepustowość nie jest dostępna w określonej lokalizacji komunikacji równorzędnej, [Otwórz żądanie obsługi w Azure Portal](https://aka.ms/azsupt).
   * **Hermetyzacja**: ExpressRoute Direct obsługuje hermetyzację QinQ i Dot1Q.
     * W przypadku wybrania QinQ każdy obwód ExpressRoute zostanie dynamicznie przypisany do znacznika S-i będzie unikatowy przez cały zasób ExpressRoute Direct.
     *  Każdy tag C w obwodzie musi być unikatowy w obwodzie, ale nie w ramach bezpośredniego ExpressRoute.
     * W przypadku wybrania hermetyzacji Dot1Q należy zarządzać unikatowością tagów języka C (VLAN) w całym zasobie ExpressRoute Direct.
     >[!IMPORTANT]
     >ExpressRoute Direct może być tylko jednym typem hermetyzacji. Nie można zmienić hermetyzacji po ExpressRoute bezpośredniej.
     >

1. Określ wszystkie Tagi zasobów, a następnie wybierz pozycję **Przegląd + Utwórz** , aby sprawdzić poprawność ustawień zasobów bezpośrednich ExpressRoute.

    :::image type="content" source="./media/how-to-expressroute-direct-portal/validate.png" alt-text="Strona Podstawy":::

1. Wybierz przycisk **Utwórz**. Zobaczysz komunikat informujący o tym, że wdrożenie jest w toku. Stan będzie wyświetlany na tej stronie podczas tworzenia zasobów. 

## <a name="generate-the-letter-of-authorization-loa"></a><a name="authorization"></a>Generuj list autoryzacji (DOWANIU)

W tej chwili generowanie listy autoryzacji jest niedostępne w portalu. Użyj **[Azure PowerShell](expressroute-howto-erdirect.md#authorization)** lub **[interfejsu wiersza polecenia platformy Azure](expressroute-howto-expressroute-direct-cli.md#authorization)** w celu uzyskania listy autoryzacji.

## <a name="change-admin-state-of-links"></a><a name="state"></a>Zmień stan administratora linków

Ten proces powinien służyć do przeprowadzenia testu warstwy 1, co gwarantuje, że każde połączenie krzyżowe jest prawidłowo poprawione na każdy router dla podstawowego i pomocniczego.

1. Na stronie **Przegląd** zasobów bezpośrednich ExpressRoute w sekcji **linki** wybierz pozycję **link1**.

    :::image type="content" source="./media/how-to-expressroute-direct-portal/link.png" alt-text="Strona Podstawy" lightbox="./media/how-to-expressroute-direct-portal/link-expand.png":::

1. Przełącz ustawienie **stanu administratora** na **włączone**, a następnie wybierz pozycję **Zapisz**.

    :::image type="content" source="./media/how-to-expressroute-direct-portal/state.png" alt-text="Strona Podstawy":::

    >[!IMPORTANT]
    >Rozliczanie rozpocznie się po włączeniu stanu administratora dla dowolnego linku.
    >

1. Powtórz ten sam proces dla **Link2**.

## <a name="create-a-circuit"></a><a name="circuit"></a>Tworzenie obwodu

Domyślnie można utworzyć 10 obwodów w ramach subskrypcji, w której znajduje się zasób ExpressRoute Direct. Tę liczbę można zwiększyć przez pomoc techniczną. Użytkownik jest odpowiedzialny za śledzenie zarówno alokowanej, jak i wykorzystywanej przepustowości. Przystosowana przepustowość to suma przepustowości wszystkich obwodów w zasobie ExpressRoute Direct. Wykorzystanie przepustowości jest fizycznym użyciem podstawowych interfejsów fizycznych.

* Dostępne są dodatkowe przepustowości obwodów, które mogą być używane w ExpressRoute bezpośrednio tylko w celu obsługi scenariuszy opisanych powyżej. Są to: 40 GB/s i 100 GB/s.

* SkuTier może być lokalna, standardowa lub Premium.

* SkuFamily musi być tylko MeteredData. Nieograniczona nie jest obsługiwana w przypadku ExpressRoute Direct.

Poniższe kroki ułatwiają tworzenie obwodu usługi ExpressRoute z przepływu pracy Direct ExpressRoute. Jeśli wolisz, możesz również utworzyć obwód przy użyciu przepływu pracy regularnego obwodu, chociaż nie ma żadnej możliwości korzystania z etapów przepływu pracy zwykłego obwodu dla tej konfiguracji. Zobacz [Tworzenie i modyfikowanie obwodu ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md).

1. W sekcji **Ustawienia** bezpośrednie ExpressRoute wybierz pozycję **obwody**, a następnie wybierz pozycję **+ Dodaj**. 

    :::image type="content" source="./media/how-to-expressroute-direct-portal/add.png" alt-text="Strona Podstawy" lightbox="./media/how-to-expressroute-direct-portal/add-expand.png":::

1. Skonfiguruj ustawienia na stronie **Konfiguracja** .

   :::image type="content" source="./media/how-to-expressroute-direct-portal/configuration2.png" alt-text="Strona Podstawy":::

1. Aby sprawdzić poprawność wartości przed **Review + Create** utworzeniem zasobu, określ wszelkie Tagi zasobów.

   :::image type="content" source="./media/how-to-expressroute-direct-portal/review.png" alt-text="Strona Podstawy":::

1. Wybierz przycisk **Utwórz**. Zobaczysz komunikat informujący o tym, że wdrożenie jest w toku. Stan będzie wyświetlany na tej stronie podczas tworzenia zasobów. 

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi ExpressRoute Direct, zobacz [Omówienie](expressroute-erdirect-about.md).
