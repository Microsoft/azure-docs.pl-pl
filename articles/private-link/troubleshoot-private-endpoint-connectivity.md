---
title: Rozwiązywanie problemów z łącznością z prywatnym punktem końcowym platformy Azure
description: Wskazówki krok po kroku dotyczące diagnozowania łączności z prywatnym punktem końcowym
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
ms.openlocfilehash: 2a4f86d9fae7b78a57cf8da7ab42d2d4a4cd7be5
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835402"
---
# <a name="troubleshoot-azure-private-endpoint-connectivity-problems"></a>Rozwiązywanie problemów z łącznością z prywatnym punktem końcowym platformy Azure

Ten artykuł zawiera szczegółowe wskazówki dotyczące weryfikowania i diagnozowania konfiguracji łączności prywatnego punktu końcowego platformy Azure.

Prywatny punkt końcowy platformy Azure to interfejs sieciowy, który łączy Cię prywatnie i bezpiecznie z usługą łącza prywatnego. To rozwiązanie pomaga zabezpieczyć obciążenia na platformie Azure, zapewniając prywatną łączność z zasobami usługi platformy Azure z sieci wirtualnej. To rozwiązanie skutecznie wprowadza te usługi do sieci wirtualnej.

Oto scenariusze łączności dostępne z prywatnym punktem końcowym:

- Sieć wirtualna z tego samego regionu
- Regionalnie równorzędne sieci wirtualne
- Globalnie równorzędne sieci wirtualne
- Lokalnego klienta za pośrednictwem sieci VPN lub Azure ExpressRoute obwodów

## <a name="diagnose-connectivity-problems"></a>Diagnozowanie problemów z łącznością 

Przejrzyj te kroki, aby upewnić się, że wszystkie typowe konfiguracje są zgodnie z oczekiwaniami, aby rozwiązać problemy z łącznością z konfiguracją prywatnego punktu końcowego.

1. Przejrzyj konfigurację prywatnego punktu końcowego, przeglądając zasób.

    a. Przejdź do **Centrum usługi Private Link**.

      ![Centrum usługi Private Link](./media/private-endpoint-tsg/private-link-center.png)

    b. W okienku po lewej stronie wybierz **pozycję Prywatne punkty końcowe.**
    
      ![Prywatne punkty końcowe](./media/private-endpoint-tsg/private-endpoints.png)

    c. Filtruj i wybierz prywatny punkt końcowy, który chcesz zdiagnozować.

    d. Przejrzyj informacje o sieci wirtualnej i systemie DNS.
     - Sprawdź, czy stan połączenia to **Zatwierdzone.**
     - Upewnij się, że maszyna wirtualna ma łączność z siecią wirtualną, która hostuje prywatne punkty końcowe.
     - Sprawdź, czy są przypisane informacje O FQDN (kopia) i prywatny adres IP.
    
       ![Konfiguracja sieci wirtualnej i systemu DNS](./media/private-endpoint-tsg/vnet-dns-configuration.png)
    
1. Użyj [Azure Monitor,](../azure-monitor/overview.md) aby sprawdzić, czy dane przepływają.

    a. W zasobie prywatnego punktu końcowego wybierz pozycję **Monitoruj**.
     - Wybierz **pozycję Bajty w lub** **Bajty wychodzące**. 
     - Sprawdź, czy dane przepływa podczas próby nawiązania połączenia z prywatnym punktem końcowym. Spodziewaj się opóźnienia około 10 minut.
    
       ![Weryfikowanie telemetrii prywatnego punktu końcowego](./media/private-endpoint-tsg/private-endpoint-monitor.png)

1.  Rozwiązywanie problemów **z połączeniem maszyny wirtualnej** z usługi Azure Network Watcher.

    a. Wybierz maszynę wirtualną klienta.

    b. Wybierz **pozycję Rozwiązywanie problemów** z połączeniami, a następnie wybierz **kartę Połączenia wychodzące.**
    
      ![Network Watcher — testowanie połączeń wychodzących](./media/private-endpoint-tsg/network-watcher-outbound-connection.png)
    
    c. Wybierz **pozycję Użyj Network Watcher, aby uzyskać szczegółowe śledzenie połączeń.**
    
      ![Network Watcher — rozwiązywanie problemów z połączeniem](./media/private-endpoint-tsg/network-watcher-connection-troubleshoot.png)

    d. Wybierz **pozycję Testuj według FQDN.**
     - Wklej domenę FQDN z zasobu prywatnego punktu końcowego.
     - Podaj port. Zazwyczaj należy użyć 443 dla usługi Azure Storage lub Azure Cosmos DB i 1336 dla języka SQL.

    e. Wybierz **pozycję Testuj** i zweryfikuj wyniki testu.
    
      ![Network Watcher — wyniki testu](./media/private-endpoint-tsg/network-watcher-test-results.png)
    
        
1. Rozpoznawanie nazw DNS z wyników testu musi mieć ten sam prywatny adres IP przypisany do prywatnego punktu końcowego.

    a. Jeśli ustawienia DNS są nieprawidłowe, wykonaj następujące kroki:
     - Jeśli używasz strefy prywatnej: 
       - Upewnij się, że sieć wirtualna maszyny wirtualnej klienta jest skojarzona ze strefą prywatną.
       - Sprawdź, czy istnieje prywatny rekord strefy DNS. Jeśli nie istnieje, utwórz go.
     - Jeśli używasz niestandardowego systemu DNS:
       - Przejrzyj niestandardowe ustawienia DNS i sprawdź, czy konfiguracja dns jest poprawna.
       Aby uzyskać wskazówki, zobacz [Omówienie prywatnego punktu końcowego: konfiguracja DNS.](./private-endpoint-overview.md#dns-configuration)

    b. W przypadku awarii łączności z powodu sieciowych grup zabezpieczeń lub tras zdefiniowanych przez użytkownika:
     - Przejrzyj reguły ruchu wychodzącego sieciowej grupy danych i utwórz odpowiednie reguły ruchu wychodzącego, aby zezwolić na ruch.
    
       ![Reguły ruchu wychodzącego sieciowej sieciowej sieciowej sieci](./media/private-endpoint-tsg/nsg-outbound-rules.png)

1. Źródłową maszynę wirtualną powinna mieć trasę do następnego przeskoku ip prywatnego punktu końcowego jako InterfaceEndpoints w efektywnych trasach karty sieciowej. 

    a. Jeśli nie widzisz trasy prywatnego punktu końcowego na źródłowej maszynie wirtualnej, sprawdź, czy 
     - Źródłową maszynę wirtualną i prywatny punkt końcowy należy do tej samej sieci wirtualnej. Jeśli tak, należy angażować pomoc techniczną. 
     - Źródłową maszynę wirtualną i prywatny punkt końcowy są częścią różnych sieci wirtualnych, a następnie sprawdź łączność IP między sieciami wirtualnymi. Jeśli masz łączność IP i nadal nie widzisz trasy, zaangażuj się w pomoc techniczną. 

1. Jeśli połączenie zweryfikowało wyniki, problem z łącznością może być związany z innymi aspektami, np. wpisami tajnymi, tokenami i hasłami w warstwie aplikacji.
   - W takim przypadku przejrzyj konfigurację zasobu łącza prywatnego skojarzonego z prywatnym punktem końcowym. Aby uzyskać więcej informacji, zobacz [przewodnik rozwiązywania Azure Private Link problemów](troubleshoot-private-link-connectivity.md)
   
1. Zawsze warto zawęzić zakres przed zgłoszeniem biletu pomocy technicznej. 

    a. Jeśli źródłem jest lokalne połączenie z prywatnym punktem końcowym na platformie Azure, które mają problemy, spróbuj nawiązać połączenie 
      - Na inną maszynę wirtualną ze środowiska lokalnego i sprawdź, czy masz łączność IP z maszyną wirtualną Virtual Network ze środowiska lokalnego. 
      - Z maszyny wirtualnej w Virtual Network do prywatnego punktu końcowego.
      
    b. Jeśli źródło to Azure, a prywatny punkt końcowy znajduje się w innej Virtual Network, spróbuj nawiązać połączenie 
      - Do prywatnego punktu końcowego z innego źródła. W ten sposób można odizolować wszelkie problemy specyficzne dla maszyny wirtualnej. 
      - Do dowolnej maszyny wirtualnej, która jest częścią tej samej Virtual Network co prywatny punkt końcowy.  

1. Skontaktuj się [z zespołem pomoc techniczna platformy Azure,](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) jeśli problem nadal nie został rozwiązany, a problem z łącznością nadal istnieje.

## <a name="next-steps"></a>Następne kroki

 * [Tworzenie prywatnego punktu końcowego w zaktualizowanej podsieci (Azure Portal)](./create-private-endpoint-portal.md)
 * [Azure Private Link rozwiązywania problemów](troubleshoot-private-link-connectivity.md)
