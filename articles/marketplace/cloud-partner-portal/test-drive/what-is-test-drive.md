---
title: Co to jest wersja testowa? | Portal Azure Marketplace
description: Wyjaśnienie funkcji dysku testowego Marketplace
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: bdfadf359195464c4024b28c5e597c571305481a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80278233"
---
<a name="what-is-test-drive"></a>Co to jest wersja testowa?
===================

Wersja testowa to doskonały sposób na pokazanie oferty dla potencjalnych klientów, dając im możliwość \'wypróbowania przed zakupieniem\', co spowodowało zwiększenie konwersji i generowanie wysoce wykwalifikowanych klientów.

Po podania informacji kontaktowych klienci mogą uzyskać dostęp do wstępnie skompilowanego środowiska testowego: praktycznej, samodzielnej wersji próbnej funkcji produktu\'i korzyści, jakie można przedstawić w rzeczywistym scenariuszu implementacji.

Na platformie testowej można zwiększyć żywotność produktu i generować wysoko kwalifikujące się potencjalni klienci w procesie.

<a name="how-does-a-test-drive-work"></a>Jak działa Stacja testowa?
---------------------------

Potencjalny klient odnajduje aplikację w portalu Marketplace, loguje się i wyraża zgodę na warunki użytkowania. W tym momencie klient otrzymuje wstępnie skonfigurowane środowisko w celu wypróbowania przez określoną liczbę godzin, a użytkownik otrzymuje wysoce wykwalifikowany klient.

![Krok 1. Wyświetlana oferta witryny Marketplace](./media/what-is-test-drive/step1.png)

![Krok 2. Ekran logowania oferty witryny Marketplace](./media/what-is-test-drive/step1andahalf.png)

![Krok 3. Ekran umowy wydawcy z ofertą Marketplace](./media/what-is-test-drive/step2.png)

![Krok 4. Ekran konfiguracji dysku testowego](./media/what-is-test-drive/step3.png)

Poniżej przedstawiono przykład sposobu, w jaki wygląda oferta, gdy potrzebny jest czas na wdrożenie:

![Oferta portalu Marketplace jest wdrażana](./media/what-is-test-drive/step4.png)

![Ekran gotowy do testowania witryny Marketplace](./media/what-is-test-drive/step5.png)

![Ekran kończenia pracy z platformą Marketplace](./media/what-is-test-drive/step6.png)

Niezależnie od tego, jak złożona jest aplikacja, twój dysk testowy firmy Microsoft pomoże Ci w przeniesieniu produktu do klienta. Dzisiaj oferujemy trzy różne typy dysków testowych na podstawie typu produktu, scenariusza i portalu Marketplace, w którym się znajdują.

- **[Azure Resource Manager](./azure-resource-manager-test-drive.md)**: Azure Resource Manager Test jest szablonem wdrożenia zawierającym wszystkie zasoby platformy Azure, które składają się na rozwiązanie skompilowane przez wydawcę. Produkty, które pasują do tego scenariusza, korzystają z tylko zasobów platformy Azure.
- **[Aplikacja logiki](./logic-app-test-drive.md)**: dysk testowy aplikacji logiki to szablon wdrożenia, który ma obejmować wszystkie złożone architektury rozwiązań. Wszystkie aplikacje Dynamics lub produkty niestandardowe powinny używać tego typu dysku testowego.
- **Power BI**: Power BI dysk testowy jest po prostu osadzonym łączem do niestandardowego skompilowanego pulpitu nawigacyjnego. Każdy produkt, który chce tylko przedstawić interaktywną wizualizację Power BI, powinien używać tego typu dysku testowego.
    Wszystko, co musisz przekazać, to osadzony adres URL Power BI.

<a name="what-goes-on-in-the-background"></a>Co się stało w tle?
-------------------------------

Usługa dysk testowy jest wbudowana w taki sposób, aby obsługiwała klientów, bez konieczności ręcznego podejmowania jakichkolwiek działań. Jako Wydawca, Twoje zadanie służy do zarządzania ustawieniami dysków testowych z portalu theCloud partner i konfigurowania ich, a następnie to ustawienie będzie bezpośrednio dostępne dla klientów.

Oznacza to, że po skonfigurowaniu dla danego dysku testowego, każdy dysk testowy zostanie wystąpieniem zarządzanym, które zostanie wdrożone na żądanie dla klienta żądającego go. Po przypisaniu wystąpienia dysku testowego dysk testowy jest dostępny do użycia przez określoną ilość czasu, a następnie jest usuwany w celu utworzenia pokoju dla innego klienta.

<a name="next-steps"></a>Następne kroki
----------

Teraz, gdy już wiesz, co to jest wersja testowa, przejdź do wybranego typu dysku testowego, który ma zostać opublikowany, aby dowiedzieć się więcej na temat wymaganych pól.

- **[Azure Resource Manager](./azure-resource-manager-test-drive.md)**
- **[Aplikacja logiki](./logic-app-test-drive.md)**

Jeśli masz więcej pytań, szukasz porad dotyczących rozwiązywania problemów lub chcesz, aby test został pomyślnie przeszedł, przejdź do [sekcji często zadawane pytania, rozwiązywanie problemów, & najlepsze rozwiązania](./marketing-and-best-practices.md).
