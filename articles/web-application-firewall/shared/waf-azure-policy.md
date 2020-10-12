---
title: Zapora aplikacji sieci Web platformy Azure i Azure Policy
description: Zapora aplikacji sieci Web platformy Azure (WAF) w połączeniu z Azure Policy może pomóc wymusić standardy organizacyjne i ocenić zgodność na skalę zasobów WAF
author: tremansdoerfer
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: rimansdo
ms.openlocfilehash: 937f220980d602b755b6329da4d93df0e4b372ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86224064"
---
# <a name="azure-web-application-firewall-and-azure-policy"></a>Zapora aplikacji sieci Web platformy Azure i Azure Policy

Zapora aplikacji sieci Web platformy Azure (WAF) w połączeniu z Azure Policy może pomóc wymusić standardy organizacyjne i ocenić zgodność na skalę zasobów WAF. Azure Policy to narzędzie ładu, które zapewnia Zagregowany widok służący do oszacowania ogólnego stanu środowiska, z możliwością przechodzenia do szczegółów poszczególnych zasobów i stopnia szczegółowości poszczególnych zasad. Usługa Azure Policy pomaga również zapewnić zgodność zasobów dzięki korygowaniu zbiorczemu dla istniejących zasobów i automatycznym korygowaniu nowych zasobów.

## <a name="azure-policies-for-web-application-firewall"></a>Zasady platformy Azure dla zapory aplikacji sieci Web

Istnieje kilka wbudowanych zasad platformy Azure do zarządzania zasobami WAF. Podział zasad i ich funkcje są następujące:

1. **Zapora aplikacji sieci Web (WAF) powinna być włączona dla usługi Azure front-drzwi**: usługi frontonu platformy Azure są oceniane w przypadku, gdy istnieje WAF do tworzenia zasobów. Zasady mają trzy efekty: Inspekcja, odmowa i wyłączanie. Śledzenie inspekcji, gdy usługa frontonu platformy Azure nie ma WAF i umożliwia użytkownikom sprawdzenie, jakie usługi platformy Azure z przodu nie są zgodne. Odmów uniemożliwia tworzenie dowolnej usługi platformy Azure z systemem, jeśli WAF nie jest dołączona. Wyłączone wyłącza te zasady.

2. **Zapora aplikacji sieci Web (WAF) powinna być włączona dla Application Gateway**: usługi Application Gateway są oceniane w przypadku, gdy istnieje WAF do tworzenia zasobów. Zasady mają trzy efekty: Inspekcja, odmowa i wyłączanie. Śledzi śledzenie, gdy Application Gateway nie ma WAF i umożliwia użytkownikom sprawdzenie, jakie Application Gateway nie są zgodne. Odmów uniemożliwia tworzenie jakichkolwiek Application Gateway, jeśli nie dołączono elementu WAF. Wyłączone wyłącza te zasady.

3. **Zapora aplikacji sieci Web (WAF) powinna używać określonego trybu dla usługi platformy Azure front-drzwi**: zezwala na aktywne użycie trybu wykrywania lub zapobiegania na wszystkich zasadach zapory aplikacji sieci Web dla usługi platformy Azure. Zasady mają trzy efekty: Inspekcja, odmowa i wyłączanie. Śledzi śledzenie, gdy WAF nie pasuje do określonego trybu. Odmów uniemożliwia tworzenie WAF, jeśli nie jest w poprawnym trybie. Wyłączone wyłącza te zasady.

4. **Zapora aplikacji sieci Web (WAF) powinna używać określonego trybu dla Application Gateway**: zezwala na aktywne użycie trybu wykrywania lub zapobiegania na wszystkich zasadach zapory aplikacji sieci Web dla Application Gateway. Zasady mają trzy efekty: Inspekcja, odmowa i wyłączanie. Śledzi śledzenie, gdy WAF nie pasuje do określonego trybu. Odmów uniemożliwia tworzenie WAF, jeśli nie jest w poprawnym trybie. Wyłączone wyłącza te zasady.


## <a name="launch-an-azure-policy"></a>Uruchom Azure Policy


1.  Na stronie głównej platformy Azure wpisz zasady na pasku wyszukiwania, a następnie kliknij ikonę Azure Policy

2.  W usłudze Azure Policy w obszarze **Tworzenie**wybierz pozycję **przypisania**.

[!div class="mx-imgBorder"]
![Zapora aplikacji sieci Web platformy Azure](../media/waf-azure-policy/policy-home.png)

3.  Na stronie przypisania wybierz ikonę **Przypisz zasady** u góry.

[!div class="mx-imgBorder"]
![Zapora aplikacji sieci Web platformy Azure](../media/waf-azure-policy/assign-policy.png)

4.  Na karcie informacje na stronie przypisywanie zasad należy zaktualizować następujące pola:
    1.  **Zakres**: wybierz subskrypcje platformy Azure i grupy zasobów, na które ma mieć wpływ Azure Policy.
    2.  **Wykluczenia**: Wybierz wszystkie zasoby z zakresu, które mają zostać wykluczone z zasad 
    3.  **Definicja zasad**: wybierz Azure Policy, które mają zostać zastosowane do zakresu z wykluczeniami. Wpisz "Zapora aplikacji sieci Web" na pasku wyszukiwania, aby wybrać odpowiednią zaporę aplikacji sieci Web Azure Policy.

[!div class="mx-imgBorder"]
![Zapora aplikacji sieci Web platformy Azure](../media/waf-azure-policy/policy-listing.png)


5.  Wybierz kartę **Parametry** i zaktualizuj parametry zasad. Aby dowiedzieć się więcej o tym, jaki jest parametr, umieść kursor nad ikoną informacji obok nazwy parametru w celu uzyskania dalszych wyjaśnień.

6.  Wybierz pozycję **Przegląd + Utwórz** , aby zakończyć swoje zasady platformy Azure. Zasady platformy Azure będą trwać około 15 minut, dopóki nie będzie aktywne dla nowych zasobów.
