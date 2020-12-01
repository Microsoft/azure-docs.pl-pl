---
title: Zapora aplikacji sieci Web platformy Azure i Azure Policy
description: Zapora aplikacji sieci Web platformy Azure (WAF) w połączeniu z Azure Policy może pomóc wymusić standardy organizacyjne i ocenić zgodność na skalę zasobów WAF
author: tremansdoerfer
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: rimansdo
ms.openlocfilehash: fd474a32b4a517230a82615065d7815c04140045
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96432972"
---
# <a name="azure-web-application-firewall-and-azure-policy"></a>Zapora aplikacji sieci Web platformy Azure i Azure Policy

Zapora aplikacji sieci Web platformy Azure (WAF) w połączeniu z Azure Policy może pomóc wymusić standardy organizacyjne i ocenić zgodność na skalę zasobów WAF. Azure Policy to narzędzie ładu, które zapewnia Zagregowany widok służący do oszacowania ogólnego stanu środowiska, z możliwością przechodzenia do szczegółów poszczególnych zasobów i stopnia szczegółowości poszczególnych zasad. Usługa Azure Policy pomaga również zapewnić zgodność zasobów dzięki korygowaniu zbiorczemu dla istniejących zasobów i automatycznym korygowaniu nowych zasobów.

## <a name="azure-policy-for-web-application-firewall"></a>Azure Policy zapory aplikacji sieci Web

Istnieje kilka wbudowanych definicji Azure Policy do zarządzania zasobami WAF. Podział definicji zasad i ich funkcje są następujące:

1. **Zapora aplikacji sieci Web (WAF) powinna być włączona dla usługi Azure front-drzwi**: usługi frontonu platformy Azure są oceniane w przypadku, gdy istnieje WAF do tworzenia zasobów. Zasady mają trzy efekty: Inspekcja, odmowa i wyłączanie. Śledzenie inspekcji, gdy usługa frontonu platformy Azure nie ma WAF i umożliwia użytkownikom sprawdzenie, jakie usługi platformy Azure z przodu nie są zgodne. Odmów uniemożliwia tworzenie dowolnej usługi platformy Azure z systemem, jeśli WAF nie jest dołączona. Wyłączone wyłącza te zasady.

2. **Zapora aplikacji sieci Web (WAF) powinna być włączona dla Application Gateway**: usługi Application Gateway są oceniane w przypadku, gdy istnieje WAF do tworzenia zasobów. Zasady mają trzy efekty: Inspekcja, odmowa i wyłączanie. Śledzi śledzenie, gdy Application Gateway nie ma WAF i umożliwia użytkownikom sprawdzenie, jakie Application Gateway nie są zgodne. Odmów uniemożliwia tworzenie jakichkolwiek Application Gateway, jeśli nie dołączono elementu WAF. Wyłączone wyłącza te zasady.

3. **Zapora aplikacji sieci Web (WAF) powinna używać określonego trybu dla usługi platformy Azure front-drzwi**: zezwala na aktywne użycie trybu wykrywania lub zapobiegania na wszystkich zasadach zapory aplikacji sieci Web dla usługi platformy Azure. Zasady mają trzy efekty: Inspekcja, odmowa i wyłączanie. Śledzi śledzenie, gdy WAF nie pasuje do określonego trybu. Odmów uniemożliwia tworzenie WAF, jeśli nie jest w poprawnym trybie. Wyłączone wyłącza te zasady.

4. **Zapora aplikacji sieci Web (WAF) powinna używać określonego trybu dla Application Gateway**: zezwala na aktywne użycie trybu wykrywania lub zapobiegania na wszystkich zasadach zapory aplikacji sieci Web dla Application Gateway. Zasady mają trzy efekty: Inspekcja, odmowa i wyłączanie. Śledzi śledzenie, gdy WAF nie pasuje do określonego trybu. Odmów uniemożliwia tworzenie WAF, jeśli nie jest w poprawnym trybie. Wyłączone wyłącza te zasady.

## <a name="launch-an-azure-policy"></a>Uruchom Azure Policy

1.  Na stronie głównej platformy Azure wpisz zasady na pasku wyszukiwania, a następnie kliknij ikonę Azure Policy

2.  Na Azure Policy usługi w obszarze **Tworzenie** wybierz pozycję **przypisania**.

:::image type="content" source="../media/waf-azure-policy/policy-home.png" alt-text="Karta przypisania w Azure Policy":::

3.  Na stronie przypisania wybierz ikonę **Przypisz zasady** u góry.

:::image type="content" source="../media/waf-azure-policy/assign-policy.png" alt-text="Karta podstawy na stronie przypisywanie zasad":::

4.  Na karcie informacje na stronie przypisywanie zasad należy zaktualizować następujące pola:
    1.  **Zakres**: wybierz subskrypcje platformy Azure i grupy zasobów, na które ma mieć wpływ definicja zasad.
    2.  **Wykluczenia**: Wybierz wszystkie zasoby z zakresu, które mają zostać wykluczone z przypisania zasad.
    3.  **Definicja zasad**: Wybierz definicję zasad, która ma zostać zastosowana do zakresu z wykluczeniami. Wpisz "Zapora aplikacji sieci Web" na pasku wyszukiwania, aby wybrać odpowiednią zaporę aplikacji sieci Web Azure Policy.

:::image type="content" source="../media/waf-azure-policy/policy-listing.png" alt-text="Karta podstawy na stronie przypisywanie zasad":::

5.  Wybierz kartę **Parametry** i zaktualizuj parametry przypisywania zasad. Aby dowiedzieć się więcej o tym, jaki jest parametr, umieść kursor nad ikoną informacji obok nazwy parametru w celu uzyskania dalszych wyjaśnień.

6.  Wybierz pozycję **Przegląd + Utwórz** , aby zakończyć przypisanie zasad. Przydział zasad trwa około 15 minut, dopóki nie będzie aktywny dla nowych zasobów.
