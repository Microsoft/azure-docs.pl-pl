---
title: Rozwiązywanie problemów z łącznością z prywatnym punktem końcowym platformy Azure
description: Wskazówki krok po kroku umożliwiające zdiagnozowanie łączności z prywatnym punktem końcowym
services: private-endpoint
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
ms.openlocfilehash: 90831c0e8d5ab73f65dc801319a357d59799cbc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97807556"
---
# <a name="troubleshoot-azure-private-endpoint-connectivity-problems"></a>Rozwiązywanie problemów z łącznością z prywatnym punktem końcowym platformy Azure

Ten artykuł zawiera wskazówki krok po kroku dotyczące sprawdzania poprawności i zdiagnozowania konfiguracji połączenia prywatnego punktu końcowego platformy Azure.

Prywatny punkt końcowy platformy Azure to interfejs sieciowy, który nawiązuje połączenie prywatnie i bezpiecznie z usługą linku prywatnego. To rozwiązanie pomaga w zabezpieczeniu obciążeń na platformie Azure, zapewniając prywatną łączność z zasobami usługi platformy Azure z sieci wirtualnej. To rozwiązanie efektywnie przenosi te usługi do sieci wirtualnej.

Oto scenariusze łączności, które są dostępne dla prywatnego punktu końcowego:

- Sieć wirtualna z tego samego regionu
- Regionalne sieci wirtualne równorzędne
- Globalnie równorzędne sieci wirtualne
- Klient lokalny przez sieć VPN lub obwody usługi Azure ExpressRoute

## <a name="diagnose-connectivity-problems"></a>Diagnozuj problemy z łącznością 

Zapoznaj się z tymi krokami, aby upewnić się, że wszystkie typowe konfiguracje są wymagane do rozwiązania problemów z łącznością z konfiguracją prywatnego punktu końcowego.

1. Przejrzyj konfigurację prywatnego punktu końcowego, przeglądając zasób.

    a. Przejdź do **prywatnego centrum linków**.

      ![Prywatne centrum linków](./media/private-endpoint-tsg/private-link-center.png)

    b. W okienku po lewej stronie wybierz pozycję **prywatne punkty końcowe**.
    
      ![Prywatne punkty końcowe](./media/private-endpoint-tsg/private-endpoints.png)

    c. Odfiltruj i wybierz prywatny punkt końcowy, który chcesz zdiagnozować.

    d. Przejrzyj informacje dotyczące sieci wirtualnej i usługi DNS.
     - Sprawdź, czy stan połączenia został **zatwierdzony**.
     - Upewnij się, że maszyna wirtualna ma łączność z siecią wirtualną, która obsługuje prywatne punkty końcowe.
     - Sprawdź, czy są przypisane informacje o nazwie FQDN (kopia) i prywatny adres IP.
    
       ![Konfiguracja sieci wirtualnej i systemu DNS](./media/private-endpoint-tsg/vnet-dns-configuration.png)
    
1. Użyj [Azure monitor](../azure-monitor/overview.md) , aby sprawdzić, czy dane przepływają.

    a. W polu zasób prywatnego punktu końcowego wybierz pozycję **Monitoruj**.
     - Wybierz **dane z lub z** **danych**. 
     - Sprawdź, czy podczas próby nawiązania połączenia z prywatnym punktem końcowym są przesyłane dane. Oczekiwano opóźnienia około 10 minut.
    
       ![Weryfikowanie telemetrii prywatnego punktu końcowego](./media/private-endpoint-tsg/private-endpoint-monitor.png)

1.  Użyj **rozwiązywania problemów z połączeniem maszyny wirtualnej** z poziomu usługi Azure Network Watcher.

    a. Wybierz maszynę wirtualną klienta.

    b. Wybierz pozycję **Rozwiązywanie problemów z połączeniami**, a następnie wybierz kartę **połączenia wychodzące** .
    
      ![Network Watcher — Testowanie połączeń wychodzących](./media/private-endpoint-tsg/network-watcher-outbound-connection.png)
    
    c. Wybierz pozycję **użyj Network Watcher, aby uzyskać szczegółowe śledzenie połączeń**.
    
      ![Network Watcher — Rozwiązywanie problemów z połączeniem](./media/private-endpoint-tsg/network-watcher-connection-troubleshoot.png)

    d. Wybierz pozycję **Testuj według nazwy FQDN**.
     - Wklej nazwę FQDN z prywatnego zasobu punktu końcowego.
     - Podaj port. Zazwyczaj należy używać 443 dla usługi Azure Storage lub Azure Cosmos DB i 1336 dla SQL.

    e. Wybierz pozycję **Testuj** i sprawdź poprawność wyników testu.
    
      ![Network Watcher — wyniki testu](./media/private-endpoint-tsg/network-watcher-test-results.png)
    
        
1. Rozpoznawanie nazw DNS z wyników testów musi mieć ten sam prywatny adres IP przypisany do prywatnego punktu końcowego.

    a. Jeśli ustawienia DNS są nieprawidłowe, wykonaj następujące kroki:
     - Jeśli używasz strefy prywatnej: 
       - Upewnij się, że sieć wirtualna klienta jest skojarzona z strefą prywatną.
       - Sprawdź, czy istnieje rekord prywatnej strefy DNS. Jeśli nie istnieje, utwórz ją.
     - Jeśli używasz niestandardowej usługi DNS:
       - Przejrzyj niestandardowe ustawienia DNS i sprawdź, czy konfiguracja DNS jest poprawna.
       Aby uzyskać wskazówki, zobacz [Omówienie prywatnego punktu końcowego: Konfiguracja DNS](./private-endpoint-overview.md#dns-configuration).

    b. Jeśli łączność kończy się niepowodzeniem ze względu na sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) lub trasy zdefiniowane przez użytkownika:
     - Przejrzyj reguły ruchu wychodzącego sieciowej grupy zabezpieczeń i Utwórz odpowiednie reguły ruchu wychodzącego, aby zezwolić na ruch.
    
       ![Reguły ruchu wychodzącego sieciowej grupy zabezpieczeń](./media/private-endpoint-tsg/nsg-outbound-rules.png)

1. Źródłowa maszyna wirtualna powinna mieć trasy do prywatnego adresu IP punktu końcowego jako InterfaceEndpoints w ramach tras sieciowych. 

    a. Jeśli nie widzisz trasy prywatnego punktu końcowego na źródłowej maszynie wirtualnej, sprawdź, czy 
     - Źródłowa maszyna wirtualna i prywatny punkt końcowy należą do tej samej sieci wirtualnej. Jeśli tak, należy skontaktować się z pomocą techniczną. 
     - Źródłowa maszyna wirtualna i prywatny punkt końcowy są częścią różnych sieci wirtualnych, a następnie sprawdzają połączenie IP między sieci wirtualnych. Jeśli istnieje połączenie IP i nadal nie możesz zobaczyć trasy, należy wziąć pod uwagę pomoc techniczną. 

1. Jeśli połączenie ma zweryfikowane wyniki, problem z łącznością może być związany z innymi aspektami, takimi jak wpisy tajne, tokeny i hasła w warstwie aplikacji.
   - W takim przypadku należy przejrzeć konfigurację zasobu link prywatny skojarzonego z prywatnym punktem końcowym. Aby uzyskać więcej informacji, zobacz [Przewodnik rozwiązywania problemów z prywatnym linkiem na platformie Azure](troubleshoot-private-link-connectivity.md)
   
1. Zawsze warto zawęzić ją przed podniesieniem biletu pomocy technicznej. 

    a. Jeśli źródło jest połączone z prywatnym punktem końcowym na platformie Azure i ma problemy, spróbuj nawiązać połączenie 
      - Na inną maszynę wirtualną z lokalnego i sprawdź, czy masz połączenie IP z Virtual Network z lokalnego. 
      - Z maszyny wirtualnej w Virtual Network do prywatnego punktu końcowego.
      
    b. Jeśli źródłem jest Azure, a prywatny punkt końcowy znajduje się w różnych Virtual Network, spróbuj nawiązać połączenie 
      - Do prywatnego punktu końcowego z innego źródła. W ten sposób można odizolować wszelkie problemy specyficzne dla maszyn wirtualnych. 
      - Do dowolnej maszyny wirtualnej, która jest częścią tego samego Virtual Network tego prywatnego punktu końcowego.  

1. Skontaktuj się z zespołem [pomocy technicznej systemu Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) , jeśli problem nadal nie zostanie rozwiązany i nadal istnieje problem z łącznością.

## <a name="next-steps"></a>Następne kroki

 * [Utwórz prywatny punkt końcowy w zaktualizowanej podsieci (Azure Portal)](./create-private-endpoint-portal.md)
 * [Przewodnik rozwiązywania problemów z prywatnym linkiem na platformie Azure](troubleshoot-private-link-connectivity.md)
