---
title: Rozwiązywanie problemów z łącznością usługi Azure Private Link
description: Wskazówki krok po kroku dotyczące diagnozowania łączności z prywatnym linkiem
services: private-link
documentationcenter: na
author: rdhillon
manager: narayan
editor: ''
ms.service: private-link
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: rdhillon
ms.openlocfilehash: 45a7a146dd929408b50a0045fe2598726ee05505
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95544313"
---
# <a name="troubleshoot-azure-private-link-connectivity-problems"></a>Rozwiązywanie problemów z łącznością usługi Azure Private Link

Ten artykuł zawiera wskazówki krok po kroku umożliwiające sprawdzenie i zdiagnozowanie łączności z konfiguracją prywatnego połączenia platformy Azure.

Za pomocą linku prywatnego platformy Azure możesz uzyskiwać dostęp do usług platformy Azure jako usługi (PaaS), takich jak Azure Storage, Azure Cosmos DB i Azure SQL Database, oraz hostowanych klientów platformy Azure lub usług partnerskich za pośrednictwem prywatnego punktu końcowego w sieci wirtualnej. Ruch między siecią wirtualną a usługą odbywa się za pośrednictwem sieci szkieletowej firmy Microsoft, która eliminuje ekspozycję z publicznego Internetu. Możesz również utworzyć własną prywatną usługę linku w sieci wirtualnej i dostarczyć ją prywatnie do klientów.

Możesz włączyć usługę działającą za warstwą standardowa Azure Load Balancer, aby uzyskać dostęp do linków prywatnych. Konsumenci usługi mogą utworzyć prywatny punkt końcowy wewnątrz sieci wirtualnej i zmapować ją na tę usługę, aby uzyskać do niej dostęp prywatnie.

Oto scenariusze łączności, które są dostępne za pomocą linku prywatnego:

- Sieć wirtualna z tego samego regionu
- Regionalne sieci wirtualne równorzędne
- Globalnie równorzędne sieci wirtualne
- Klient lokalny przez sieć VPN lub obwody usługi Azure ExpressRoute

## <a name="deployment-troubleshooting"></a>Rozwiązywanie problemów z wdrażaniem

Zapoznaj się z informacjami na temat [wyłączania zasad sieciowych w usłudze linku prywatnego](./disable-private-link-service-network-policy.md) w celu rozwiązywania problemów, gdy nie możesz wybrać źródłowego adresu IP z wybranej podsieci do usługi link prywatny.

Upewnij się, że ustawienie **privateLinkServiceNetworkPolicies** jest wyłączone dla podsieci, z której jest wybierany źródłowy adres IP.

## <a name="diagnose-connectivity-problems"></a>Diagnozuj problemy z łącznością

Jeśli występują problemy z łącznością z konfiguracją linku prywatnego, zapoznaj się z tymi krokami, aby upewnić się, że wszystkie typowe konfiguracje są zgodnie z oczekiwaniami.

1. Przejrzyj konfigurację linku prywatnego, przeglądając zasób.

    a. Przejdź do **prywatnego centrum linków**.

      ![Prywatne centrum linków](./media/private-link-tsg/private-link-center.png)

    b. W okienku po lewej stronie wybierz pozycję **usługi połączeń prywatnych**.

      ![Usługi linków prywatnych](./media/private-link-tsg/private-link-service.png)

    c. Odfiltruj i wybierz usługę łącza prywatnego, którą chcesz zdiagnozować.

    d. Przejrzyj połączenia prywatnych punktów końcowych.
     - Upewnij się, że prywatny punkt końcowy, z którego próbujesz nawiązać połączenie, jest wymieniony w **zatwierdzonym** stanie połączenia.
     - Jeśli stan jest **oczekujący**, wybierz go i zatwierdź.

       ![Połączenia prywatnego punktu końcowego](./media/private-link-tsg/pls-private-endpoint-connections.png)

     - Przejdź do prywatnego punktu końcowego, z którego nawiązujesz połączenie, wybierając nazwę. Upewnij się, że stan połączenia jest wyświetlany jako **zatwierdzone**.

       ![Omówienie połączenia z prywatnym punktem końcowym](./media/private-link-tsg/pls-private-endpoint-overview.png)

     - Po zatwierdzeniu obu stron spróbuj ponownie nawiązać połączenie.

    e. Zapoznaj się z **aliasem** na karcie **Przegląd** i **Identyfikator zasobu** na karcie **Właściwości** .
     - Upewnij się, że informacje o **aliasie** i **identyfikatorze zasobu** są zgodne z **aliasem** i **identyfikatorem zasobu** używanym do tworzenia prywatnego punktu końcowego dla tej usługi.

       ![Weryfikuj informacje o aliasie](./media/private-link-tsg/pls-overview-pane-alias.png)

       ![Sprawdź informacje o IDENTYFIKATORze zasobu](./media/private-link-tsg/pls-properties-pane-resourceid.png)

    f. Przejrzyj informacje o **widoczności** na karcie **Przegląd** .
     - Upewnij się, że Twoja subskrypcja mieści się w zakresie **widoczności** .

       ![Weryfikuj informacje o widoczności](./media/private-link-tsg/pls-overview-pane-visibility.png)

    przykład Przejrzyj informacje dotyczące usługi **równoważenia obciążenia** na karcie **Przegląd** .
     - Możesz przejść do modułu równoważenia obciążenia, wybierając link modułu równoważenia obciążenia.

       ![Weryfikuj informacje modułu równoważenia obciążenia](./media/private-link-tsg/pls-overview-pane-ilb.png)

     - Upewnij się, że ustawienia usługi równoważenia obciążenia zostały skonfigurowane zgodnie z oczekiwaniami.
       - Przejrzyj **konfigurację adresu IP frontonu**.
       - Przejrzyj **Pule zaplecza**.
       - Przejrzyj **reguły równoważenia obciążenia**.

       ![Weryfikuj właściwości usługi równoważenia obciążenia](./media/private-link-tsg/pls-ilb-properties.png)

     - Upewnij się, że moduł równoważenia obciążenia działa zgodnie z poprzednimi ustawieniami.
       - Wybierz maszynę wirtualną w dowolnej podsieci innej niż podsieć, w której jest dostępna Pula zaplecza modułu równoważenia obciążenia.
       - Spróbuj uzyskać dostęp do frontonu modułu równoważenia obciążenia z poprzedniej maszyny wirtualnej.
       - Jeśli połączenie przyniesie je do puli zaplecza zgodnie z regułami równoważenia obciążenia, moduł równoważenia obciążenia działa.
       - Możesz również sprawdzić metrykę modułu równoważenia obciążenia za pomocą Azure Monitor, aby sprawdzić, czy dane przepływają przez moduł równoważenia obciążenia.

1. Użyj [Azure monitor](../azure-monitor/overview.md) , aby sprawdzić, czy dane przepływają.

    a. W obszarze zasób usługi link prywatny wybierz pozycję **metryki**.
     - Wybierz **bajty z lub w** **bajtach**.
     - Sprawdź, czy dane przepływają podczas próby nawiązania połączenia z usługą łącza prywatnego. Oczekiwano opóźnienia około 10 minut.

       ![Weryfikowanie metryk usługi linku prywatnego](./media/private-link-tsg/pls-metrics.png)

1. Skontaktuj się z zespołem [pomocy technicznej systemu Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) , jeśli problem nadal nie zostanie rozwiązany i nadal istnieje problem z łącznością.

## <a name="next-steps"></a>Następne kroki

 * [Tworzenie usługi linku prywatnego (CLI)](./create-private-link-service-cli.md)
 * [Przewodnik rozwiązywania problemów z prywatnym punktem końcowym platformy Azure](troubleshoot-private-endpoint-connectivity.md)