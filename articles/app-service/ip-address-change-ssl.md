---
title: Przygotowanie do zmiany adresu IP protokołu SSL
description: Jeśli twój adres IP SSL zostanie zmieniony, dowiedz się, co zrobić, aby aplikacja nadal działała po zmianie.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: dcfe11bcab25f6267a557de5faf7befab467bc29
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535727"
---
# <a name="how-to-prepare-for-an-ssl-ip-address-change"></a>Jak przygotować się do zmiany adresu IP SSL

Jeśli otrzymałeś powiadomienie, że zmienia się adres IP protokołu SSL aplikacji usługi Azure App Service, postępuj zgodnie z instrukcjami w tym artykule, aby zwolnić istniejący adres IP protokołu SSL i przypisać nowy.

## <a name="release-ssl-ip-addresses-and-assign-new-ones"></a>Zwalnianie adresów IP SSL i przypisywanie nowych

1.  Otwórz witrynę [Azure Portal](https://portal.azure.com).

2.  W menu nawigacji po lewej stronie wybierz pozycję **Usługi aplikacji**.

3.  Wybierz aplikację usługi App Service z listy.

4.  W nagłówku **Ustawienia** kliknij pozycję **Ustawienia SSL** w lewej nawigacji.

1. W sekcji powiązania TLS/SSL wybierz rekord nazwy hosta. W otwieranym edytorze wybierz polecenie **SNI SSL** w menu rozwijanym **Typ SSL** i kliknij przycisk **Dodaj powiązanie**. Po wyświetleniu komunikatu o powodzenie operacji, istniejący adres IP został zwolniony.

6.  W sekcji **powiązania SSL** ponownie wybierz ten sam rekord nazwy hosta z certyfikatem. W otwieranym edytorze tym razem wybierz pozycję **SSL opartą na protoke ip** w menu rozwijanym **Typ SSL** i kliknij przycisk **Dodaj powiązanie**. Po wyświetleniu komunikatu o powodzenie operacji, zakupiono nowy adres IP.

7.  Jeśli rekord A (rekord DNS skierowany bezpośrednio do adresu IP) jest skonfigurowany w portalu rejestracji domeny (zewnętrznego dostawcy DNS lub usłudze Azure DNS), zastąp istniejący adres IP nowo wygenerowanym adresem. Nowy adres IP można znaleźć, postępując zgodnie z instrukcjami w następnej sekcji.

## <a name="find-the-new-ssl-ip-address-in-the-azure-portal"></a>Znajdź nowy adres IP SSL w witrynie Azure Portal

1.  Zaczekaj kilka minut, a następnie otwórz [witrynę Azure portal](https://portal.azure.com).

2.  W menu nawigacji po lewej stronie wybierz pozycję **Usługi aplikacji**.

3.  Wybierz aplikację usługi App Service z listy.

4.  W nagłówku **Ustawienia** kliknij pozycję **Właściwości** w lewej nawigacji i znajdź sekcję z etykietą **Wirtualny adres IP**.

5. Skopiuj adres IP i skonfiguruj ponownie rekord domeny lub mechanizm IP.

## <a name="next-steps"></a>Następne kroki

W tym artykule wyjaśniono, jak przygotować się do zmiany adresu IP, który został zainicjowany przez platformę Azure. Aby uzyskać więcej informacji na temat adresów IP w usłudze Azure App Service, zobacz [Przychodzące i wychodzące adresy IP w usłudze Azure App Service](overview-inbound-outbound-ips.md).
