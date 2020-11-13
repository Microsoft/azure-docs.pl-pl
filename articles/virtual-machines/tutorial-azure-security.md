---
title: Samouczek — używanie Azure Security Center w przypadku maszyn wirtualnych na platformie Azure
description: W ramach tego samouczka nauczysz się, jak Azure Security Center funkcje ułatwiające ochronę i zabezpieczanie maszyn wirtualnych na platformie Azure.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 06/11/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: aec223ceca242e37e25f9e7288c8e0c0e2ea7535
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94595233"
---
# <a name="tutorial-use-azure-security-center-to-monitor-virtual-machines"></a>Samouczek: używanie Azure Security Center do monitorowania maszyn wirtualnych

Usługa Azure Security Center ułatwia wgląd w rozwiązania z zakresu zabezpieczeń zasobów platformy Azure. Usługa Security Center oferuje zintegrowane monitorowanie zabezpieczeń. Potrafi wykrywać zagrożenia, które inaczej mogłyby ujść uwadze. W tym samouczku poznasz usługę Azure Security Center oraz następujące zagadnienia:
 
> [!div class="checklist"]
> * Konfigurowanie zbierania danych
> * Konfigurowanie zasad zabezpieczeń
> * Wyświetlanie problemów z kondycją konfiguracji i ich rozwiązywanie
> * Przeglądanie wykrytych zagrożeń

## <a name="security-center-overview"></a>Security Center — Przegląd

Usługa Security Center identyfikuje potencjalne problemy z konfiguracją maszyny wirtualnej i ukierunkowane zagrożenia bezpieczeństwa. Mogą one obejmować maszyny wirtualne bez sieciowych grup zabezpieczeń, niezaszyfrowane dyski i ataki siłowe przez protokół RDP (Remote Desktop Protocol). Informacje są wyświetlane na pulpicie nawigacyjnym usługi Security Center w postaci czytelnych wykresów.

Aby uzyskać dostęp do pulpitu nawigacyjnego usługi Security Center, w menu w witrynie Azure Portal wybierz pozycję **Security Center**. Na pulpicie nawigacyjnym można sprawdzić kondycję zabezpieczeń środowiska platformy Azure, liczbę zalecanych rozwiązań i bieżący stan alertów dotyczących zagrożeń. Każdy wykres wysokiego poziomu można rozwinąć, aby wyświetlić bardziej szczegółowe informacje.

![Pulpit nawigacyjny usługi Security Center](./media/tutorial-azure-security/asc-dash.png)

Usługa Security Center wykracza poza odnajdywanie danych i udostępnia zalecenia dla wykrytych problemów. Na przykład jeśli maszyna wirtualna została wdrożona bez dołączonej sieciowej grupy zabezpieczeń, usługa Security Center wyświetla zalecenie z krokami korygowania do wykonania. Automatyczne kroki korygowania uzyskuje się bez opuszczania kontekstu usługi Security Center.  

![Zrzut ekranu przedstawia stronę zalecenia, która zawiera opis, zasób, stan i ważność zaleceń.](./media/tutorial-azure-security/recommendations.png)

## <a name="set-up-data-collection"></a>Konfigurowanie zbierania danych

Zanim będzie możliwe uzyskanie wglądu w konfiguracje zabezpieczeń maszyn wirtualnych, trzeba skonfigurować zbieranie danych przez usługę Security Center. Obejmuje to włączenie zbierania danych, co powoduje automatyczne zainstalowanie programu Microsoft Monitoring Agent na wszystkich maszynach wirtualnych w subskrypcji.

1. Na pulpicie nawigacyjnym usługi Security Center kliknij pozycję **Zasady zabezpieczeń** , a następnie wybierz swoją subskrypcję. 
2. W polu **Zbieranie danych** w obszarze **Automatyczna aprowizacja** wybierz pozycję **Włączone**.
3. W polu **Domyślna konfiguracja obszaru roboczego** pozostaw wartość **Używaj obszarów roboczych utworzonych przez usługę Security Center (domyślne)**.
4. W obszarze **Zdarzenia zabezpieczeń** zachowaj domyślną opcję **Typowe**.
4. Kliknij przycisk **Zapisz** w górnej części strony. 

Następnie na wszystkich maszynach wirtualnych jest instalowany agent zbierania danych usługi Security Center i rozpoczyna się zbieranie danych. 

## <a name="set-up-a-security-policy"></a>Konfigurowanie zasad zabezpieczeń

Zasady zabezpieczeń służą do definiowania elementów, dla których usługa Security Center zbiera dane i przygotowuje zalecenia. Różne zasady zabezpieczeń można stosować do różnych zestawów zasobów platformy Azure. Mimo że domyślnie zasoby platformy Azure są sprawdzane pod kątem wszystkich elementów zasad, można wyłączyć pojedyncze elementy zasad dla wszystkich zasobów platformy Azure lub dla grupy zasobów. Aby uzyskać szczegółowe informacje na temat zasad zabezpieczeń usługi Security Center, zobacz [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](../security-center/tutorial-security-policy.md). 

Aby skonfigurować zasady zabezpieczeń dla całej subskrypcji:

1. Na pulpicie nawigacyjnym usługi Security Center wybierz pozycję **Zasady zabezpieczeń** , a następnie wybierz swoją subskrypcję.
2. W bloku **Zasady zabezpieczeń** wybierz pozycję **Zasady zabezpieczeń**. 
3. W bloku **Zasady zabezpieczeń — Zasady zabezpieczeń** włącz lub wyłącz elementy zasad, które chcesz zastosować do subskrypcji.
4. Po zakończeniu wybierania ustawień wybierz przycisk **Zapisz** w górnej części bloku. 

![Unikatowe zasady](./media/tutorial-azure-security/unique-policy.png)

## <a name="view-vm-configuration-health"></a>Wyświetlanie kondycji konfiguracji maszyny wirtualnej

Po włączeniu zbierania danych i ustawieniu zasad zabezpieczeń usługa Security Center zacznie udostępniać alerty i zalecenia. Gdy maszyny wirtualne są wdrażane, instalowany jest agent zbierania danych. Usługa Security Center jest następnie wypełniana danymi dotyczącymi nowych maszyn wirtualnych. Aby uzyskać szczegółowe informacje o kondycji konfiguracji maszyny wirtualnej, zobacz [Ochrona maszyn wirtualnych w usłudze Security Center](../security-center/asset-inventory.md). 

W miarę gromadzenia danych agregowana jest kondycja zasobu dla każdej maszyny wirtualnej i powiązanego zasobu platformy Azure. Informacje są pokazywane na czytelnym wykresie. 

Aby wyświetlić kondycję zasobu:

1.  Na pulpicie nawigacyjnym usługi Security Center w obszarze **Zapobieganie** wybierz pozycję **Obliczanie**. 
2.  W bloku **Obliczanie** wybierz pozycję **Maszyny wirtualne i komputery**. Ten widok zawiera podsumowanie stanu konfiguracji dla wszystkich maszyn wirtualnych.

![Kondycja obliczeń](./media/tutorial-azure-security/compute-health.png)

Aby wyświetlić wszystkie zalecenia dotyczące maszyny wirtualnej, wybierz maszynę wirtualną. 

## <a name="remediate-configuration-issues"></a>Rozwiązywanie problemów z konfiguracją

Gdy już dane konfiguracji zaczną spływać do usługi Security Center, rozpocznie się przygotowywanie zaleceń na podstawie skonfigurowanych zasad zabezpieczeń. Na przykład jeśli maszyna wirtualna zostanie skonfigurowana bez skojarzonej sieciowej grupy zabezpieczeń, pojawi się zalecenie, aby ją utworzyć. 

Aby zobaczyć listę wszystkich zaleceń: 

1. Na pulpicie nawigacyjnym usługi Security Center wybierz pozycję **Zalecenia**.
2. Wybierz konkretne zalecenie. Zostanie wyświetlona lista wszystkich zasobów, dla których zalecenie ma zastosowanie.
3. Aby zastosować zalecenie, wybierz zasób. 
4. Postępuj zgodnie z instrukcjami, aby wykonać kroki korygowania. 

W wielu przypadkach usługa Security Center przedstawia kroki z możliwością działania, które można wykonać w celu zastosowania zalecenia bez opuszczania usługi Security Center. W poniższym przykładzie usługa Security Center wykrywa sieciową grupę zabezpieczeń, która ma nieograniczoną regułę ruchu przychodzącego. Na stronie z zaleceniem możesz wybrać przycisk **Edytuj reguły dla ruchu przychodzącego**. Zostanie wyświetlony interfejs użytkownika, który jest potrzebny do zmodyfikowania reguły. 

![Zalecenia](./media/tutorial-azure-security/remediation.png)

W miarę stosowania się do zaleceń są one oznaczane jako rozwiązane. 

## <a name="view-detected-threats"></a>Wyświetlanie wykrytych zagrożeń

Oprócz zaleceń dotyczących konfiguracji zasobów usługa Security Center wyświetla alerty dotyczące wykrywania zagrożeń. Funkcja alertów zabezpieczeń agreguje dane zbierane z każdej maszyny wirtualnej, dzienników sieci platformy Azure i połączonych rozwiązań partnerów w celu wykrywania zagrożeń bezpieczeństwa dotyczących zasobów platformy Azure. Aby uzyskać szczegółowe informacje na temat możliwości wykrywania zagrożeń Security Center, zobacz [jak usługa Security Center wykrywać zagrożenia?](../security-center/security-center-alerts-overview.md#detect-threats).

Funkcja alertów zabezpieczeń wymaga podniesienia warstwy cenowej usługi Security Center z *Bezpłatna* do *Standardowa*. Przy przechodzeniu na tę wyższą warstwę cenową jest dostępna **bezpłatna wersja próbna**. 

Aby zmienić warstwę cenową:  

1. Na pulpicie nawigacyjnym usługi Security Center kliknij pozycję **Zasady zabezpieczeń** , a następnie wybierz swoją subskrypcję.
2. Wybierz pozycję **warstwa cenowa**.
3. Wybierz pozycję **Standardowa** , a następnie kliknij przycisk **Zapisz** w górnej części bloku.


Po zmianie warstwy cenowej wykres alertów zabezpieczeń zacznie być wypełniany w miarę wykrywania zagrożeń.

![Alerty zabezpieczeń](./media/tutorial-azure-security/security-alerts.png)

Wybierz alert, aby wyświetlić informacje. Można na przykład zobaczyć opis zagrożenia, czas wykrycia, wszystkie wystąpienia zagrożenia i zalecane kroki korygowania. W poniższym przykładzie wykryto atak siłowy przez protokół RDP. Zarejestrowano 294 nieudane próby ataku. Udostępniono też zalecane rozwiązanie.

![Atak przez protokół RDP](./media/tutorial-azure-security/rdp-attack.png)

## <a name="next-steps"></a>Następne kroki
W tym samouczku skonfigurowano usługę Azure Security Center, a następnie sprawdzono maszyny wirtualne w usłudze Security Center. W tym samouczku omówiono:

> [!div class="checklist"]
> * Konfigurowanie zbierania danych
> * Konfigurowanie zasad zabezpieczeń
> * Wyświetlanie problemów z kondycją konfiguracji i ich rozwiązywanie
> * Przeglądanie wykrytych zagrożeń

Przejdź do następnego samouczka, aby dowiedzieć się więcej o tworzeniu potoku ciągłej integracji/ciągłego dostarczania za pomocą platform Jenkins, GitHub i Docker.

> [!div class="nextstepaction"]
> [Tworzenie infrastruktury ciągłej integracji/ciągłego dostarczania za pomocą platform Jenkins, GitHub i Docker](/azure/developer/jenkins/pipeline-with-github-and-docker)