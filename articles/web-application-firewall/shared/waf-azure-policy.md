---
title: Zapora aplikacji sieci Web platformy Azure i Azure Policy
description: Zapora aplikacji sieci Web platformy Azure (WAF) w połączeniu z Azure Policy może pomóc wymusić standardy organizacyjne i ocenić zgodność na skalę zasobów WAF
author: tremansdoerfer
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: conceptual
ms.date: 06/23/2020
ms.author: rimansdo
ms.openlocfilehash: 4c1fd53eb6ebf1f1aebdfba99b736e26bd6cff2b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85306963"
---
# <a name="azure-web-application-firewall-and-azure-policy"></a>Zapora aplikacji sieci Web platformy Azure i Azure Policy

Zapora aplikacji sieci Web platformy Azure (WAF) w połączeniu z Azure Policy może pomóc wymusić standardy organizacyjne i ocenić zgodność na skalę zasobów WAF. Azure Policy to narzędzie ładu, które zapewnia Zagregowany widok służący do oszacowania ogólnego stanu środowiska, z możliwością przechodzenia do szczegółów poszczególnych zasobów i stopnia szczegółowości poszczególnych zasad. Usługa Azure Policy pomaga również zapewnić zgodność zasobów dzięki korygowaniu zbiorczemu dla istniejących zasobów i automatycznym korygowaniu nowych zasobów.

## <a name="azure-policies-for-web-application-firewall"></a>Zasady platformy Azure dla zapory aplikacji sieci Web

Istnieje kilka wbudowanych zasad platformy Azure do zarządzania zasobami WAF. Podział zasad i ich funkcje są następujące:

1. **Zapora aplikacji sieci Web powinna być włączona dla usługi Azure front-drzwi lub Application Gateway**: usługi platformy Azure z przodu i bramy aplikacji są oceniane w przypadku, gdy istnieje WAF do tworzenia zasobów. Zasady mają trzy efekty: Inspekcja, odmowa i wyłączanie. Śledzenie inspekcji, gdy usługa lub Application Gateway platformy Azure nie ma WAF i umożliwia użytkownikom sprawdzenie, co usługa Azure Front-lub Application Gateway obecnie nie jest zgodna. Odmowa uniemożliwia utworzenie jakiejkolwiek usługi lub Application Gateway platformy Azure, jeśli WAF nie jest dołączony. Wyłączone wyłącza te zasady.

2. **Zapora aplikacji sieci Web powinna być trybem zestawu dla Application Gateway i usług frontonu platformy Azure**: Zapora aplikacji sieci Web jest oceniana w tym, w jakim trybie, jest to zapobieganie lub wykrywanie. Zasady zapewniają spójność trybu między zaporami aplikacji sieci Web. Zasady mają trzy efekty: Inspekcja, odmowa i wyłączanie. Śledzi śledzenie, gdy WAF nie pasuje do określonego trybu. Odmów uniemożliwia tworzenie WAF, jeśli nie jest w poprawnym trybie. Wyłączone wyłącza te zasady.


## <a name="launch-an-azure-policy"></a>Uruchom Azure Policy


1.  Na stronie głównej platformy Azure wpisz zasady na pasku wyszukiwania, a następnie kliknij ikonę Azure Policy

2.  W usłudze Azure Policy w obszarze **Tworzenie**wybierz pozycję **przypisania**.

![Zapora aplikacji sieci Web platformy Azure](../media/waf-azure-policy/policy-home.png)

3.  Na stronie przypisania wybierz ikonę **Przypisz zasady** u góry.

![Zapora aplikacji sieci Web platformy Azure](../media/waf-azure-policy/assign-policy.png)

4.  Na karcie informacje na stronie przypisywanie zasad należy zaktualizować następujące pola:
    1.  **Zakres**: wybierz subskrypcje platformy Azure i grupy zasobów, na które ma mieć wpływ Azure Policy.
    2.  **Wykluczenia**: Wybierz wszystkie zasoby z zakresu, które mają zostać wykluczone z zasad 
    3.  **Definicja zasad**: wybierz Azure Policy, które mają zostać zastosowane do zakresu z wykluczeniami. Wpisz "Zapora aplikacji sieci Web" na pasku wyszukiwania, aby wybrać odpowiednią zaporę aplikacji sieci Web Azure Policy.

![Zapora aplikacji sieci Web platformy Azure](../media/waf-azure-policy/policy-listings.png)


5.  Wybierz kartę **Parametry** i zaktualizuj parametry zasad. Aby dowiedzieć się więcej o tym, jaki jest parametr, umieść kursor nad ikoną informacji obok nazwy parametru w celu uzyskania dalszych wyjaśnień.

6.  Wybierz pozycję **Przegląd + Utwórz** , aby zakończyć swoje zasady platformy Azure. Zasady platformy Azure będą trwać około 15 minut, dopóki nie będzie aktywne dla nowych zasobów.
