---
title: Ochrona maszyn Azure VMware Solution wirtualnych za pomocą Azure Security Center integracji
description: Chroń swoje Azure VMware Solution wirtualne za pomocą natywnych narzędzi zabezpieczeń platformy Azure z pulpitu Azure Security Center nawigacyjnego.
ms.topic: how-to
ms.date: 02/12/2021
ms.openlocfilehash: d04f0ac3e3934442ce5b6d5fbf4b53e18b3dff18
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877520"
---
# <a name="protect-your-azure-vmware-solution-vms-with-azure-security-center-integration"></a>Ochrona maszyn Azure VMware Solution wirtualnych za pomocą Azure Security Center integracji

Natywne narzędzia zabezpieczeń platformy Azure zapewniają ochronę środowiska hybrydowego platformy Azure, Azure VMware Solution i lokalnych maszyn wirtualnych. W tym artykule pokazano, jak skonfigurować narzędzia platformy Azure do zabezpieczeń środowiska hybrydowego. Użyjesz tych narzędzi, aby identyfikować różne zagrożenia i je identyfikować.

## <a name="azure-native-services"></a>Usługi natywne platformy Azure

Oto krótkie podsumowanie usług natywnych platformy Azure:

- **Obszar roboczy usługi Log Analytics:** Obszar roboczy usługi Log Analytics to unikatowe środowisko do przechowywania danych dzienników. Każdy obszar roboczy ma własne repozytorium danych i konfigurację. Źródła danych i rozwiązania są skonfigurowane do przechowywania danych w określonym obszarze roboczym.
- **Azure Security Center:** Azure Security Center to ujednolicony system zarządzania zabezpieczeniami infrastruktury. Zwiększa bezpieczeństwo centrów danych i zapewnia zaawansowaną ochronę przed zagrożeniami w obciążeniach hybrydowych w chmurze lub lokalnie.
- **Azure Sentinel:** Azure Sentinel to natywne dla chmury rozwiązanie do zarządzania zdarzeniami informacji o zabezpieczeniach (SIEM). Zapewnia analizę zabezpieczeń, wykrywanie alertów i automatyczne reagowanie na zagrożenia w całym środowisku.

## <a name="topology"></a>Topologia

![Diagram przedstawiający architekturę zintegrowanych zabezpieczeń platformy Azure.](media/azure-security-integration/azure-integrated-security-architecture.png)

Agent usługi Log Analytics umożliwia zbieranie danych dzienników z platformy Azure, Azure VMware Solution i lokalnych maszyn wirtualnych. Dane dziennika są wysyłane do usługi Azure Monitor i przechowywane w obszarze roboczym usługi Log Analytics. Agenta usługi Log Analytics można wdrożyć przy użyciu rozszerzeń maszyn wirtualnych z obsługą serwerów z [obsługą](../azure-arc/servers/manage-vm-extensions.md) usługi Arc dla nowych i istniejących maszyn wirtualnych. 

Po zebrania dzienników w obszarze roboczym usługi Log Analytics można skonfigurować obszar roboczy usługi Log Analytics przy użyciu Azure Security Center. Azure Security Center oceni stan luk w zabezpieczeniach maszyn wirtualnych Azure VMware Solution i zniesie alert o wszelkich krytycznych lukach w zabezpieczeniach. Na przykład ocenia brakujące poprawki systemu operacyjnego, błędy konfiguracji zabezpieczeń i ochronę [punktu końcowego.](../security-center/security-center-services.md)

Obszar roboczy usługi Log Analytics można skonfigurować przy użyciu Azure Sentinel wykrywania alertów, widoczności zagrożeń, zagrożeń i reagowania na nie. Na powyższym diagramie połączenie Azure Security Center z usługą Azure Sentinel użyciu Azure Security Center łącznika. Azure Security Center przekaże lukę w zabezpieczeniach środowiska do Azure Sentinel, aby utworzyć zdarzenie i zamapować je na inne zagrożenia. Można również utworzyć zapytanie zaplanowanych reguł, aby wykryć niepożądane działania i przekonwertować je na zdarzenia.

## <a name="benefits"></a>Korzyści

- Usługi natywne platformy Azure mogą służyć do zabezpieczenia środowiska hybrydowego na platformie Azure, Azure VMware Solution i usług lokalnych.
- Za pomocą obszaru roboczego usługi Log Analytics można zbierać dane lub dzienniki w jednym punkcie i prezentować te same dane różnym usługom natywnym platformy Azure.
- Azure Security Center oferuje wiele funkcji, w tym:
    - Monitorowanie integralności plików
    - Wykrywanie ataków bez plików
    - Ocena poprawek systemu operacyjnego 
    - Ocena błędnych konfiguracji zabezpieczeń
    - Ocena programu Endpoint Protection
- Azure Sentinel umożliwia:
    - Zbieraj dane w skali chmury dla wszystkich użytkowników, urządzeń, aplikacji i infrastruktury, zarówno lokalnie, jak i w wielu chmurach.
    - Wykrywanie wcześniej niewykrywanych zagrożeń.
    - Badanie zagrożeń za pomocą sztucznej inteligencji i polunie podejrzanych działań na dużą skalę.
    - Błyskawiczne reagowanie na zdarzenia dzięki wbudowanej orkiestracji i automatyzacji typowych zadań.

## <a name="create-a-log-analytics-workspace"></a>Tworzenie obszaru roboczego usługi Log Analytics

Obszar roboczy usługi Log Analytics będzie potrzebny do zbierania danych z różnych źródeł. Aby uzyskać więcej informacji, [zobacz Tworzenie obszaru roboczego usługi Log Analytics na Azure Portal](../azure-monitor/logs/quick-create-workspace.md). 

## <a name="deploy-security-center-and-configure-azure-vmware-solution-vms"></a>Wdrażanie Security Center i konfigurowanie Azure VMware Solution wirtualnych

Azure Security Center to wstępnie skonfigurowane narzędzie, które nie wymaga wdrożenia. W Azure Portal wyszukaj pozycję **Security Center** i wybierz ją.

### <a name="enable-azure-defender"></a>Włączanie usługi Azure Defender

Azure Defender rozszerza Azure Security Center zaawansowaną ochronę przed zagrożeniami w obciążeniach hybrydowych, zarówno lokalnych, jak i w chmurze. Aby chronić maszyny Azure VMware Solution wirtualne, należy włączyć Azure Defender. 

1. W Security Center wybierz pozycję **Wprowadzenie.**

2. Wybierz **kartę Uaktualnienie,** a następnie wybierz swoją subskrypcję lub obszar roboczy. 

3. Wybierz **pozycję Uaktualnij,** aby włączyć Azure Defender.

## <a name="add-azure-vmware-solution-vms-to-security-center"></a>Dodawanie Azure VMware Solution wirtualnych do Security Center

1. W Azure Portal wyszukaj **pozycję** Azure Arc i wybierz ją.

2. W obszarze Zasoby wybierz pozycję **Serwery,** a następnie **pozycję +Dodaj.**

    :::image type="content" source="media/azure-security-integration/add-server-to-azure-arc.png" alt-text="Zrzut ekranu przedstawiający Azure Arc serwerów dodawania maszyny Azure VMware Solution wirtualnej na platformie Azure.":::

3. Wybierz **pozycję Generuj skrypt.**
 
    :::image type="content" source="media/azure-security-integration/add-server-using-script.png" alt-text="Zrzut ekranu przedstawiający Azure Arc z opcją dodawania serwera przy użyciu skryptu interakcyjnego."::: 
 
4. Na karcie **Wymagania wstępne** wybierz pozycję **Dalej.**

5. Na karcie **Szczegóły zasobu** wprowadź następujące informacje: 
    - Subskrypcja
    - Grupa zasobów
    - Region (Region) 
    - System operacyjny
    - Szczegóły serwera proxy
    
    Następnie wybierz **pozycję Dalej: Tagi**.

6. Na karcie **Tagi** wybierz pozycję **Dalej.**

7. Na karcie **Pobieranie i uruchamianie skryptu** wybierz pozycję **Pobierz**.

8. Określ system operacyjny i uruchom skrypt na maszynie Azure VMware Solution wirtualnej.

## <a name="view-recommendations-and-passed-assessments"></a>Wyświetlanie rekomendacji i pomyślnie przekazanych ocen

1. W Azure Security Center wybierz pozycję **Spis** w okienku po lewej stronie.

2. W przypadku opcji Typ zasobu **wybierz pozycję Serwery — Azure Arc**.
 
     :::image type="content" source="media/azure-security-integration/select-resource-in-security-center.png" alt-text="Zrzut ekranu przedstawiający Azure Security Center Spis serwerów — Azure Arc w obszarze Typ zasobu.":::

3. Wybierz nazwę zasobu. Zostanie otwarta strona przedstawiająca szczegóły kondycji zabezpieczeń zasobu.

4. W **obszarze Lista zaleceń** wybierz karty **Zalecenia,** **Przekazane oceny** i Oceny **niedostępne,** aby wyświetlić te szczegóły.

    :::image type="content" source="media/azure-security-integration/view-recommendations-assessments.png" alt-text="Zrzut ekranu przedstawiający Azure Security Center zalecenia i oceny zabezpieczeń.":::

## <a name="deploy-an-azure-sentinel-workspace"></a>Wdrażanie Azure Sentinel roboczego

Azure Sentinel jest zbudowany na podstawie obszaru roboczego usługi Log Analytics. Pierwszym krokiem podczas dołączania Azure Sentinel jest wybranie obszaru roboczego usługi Log Analytics, który ma być do tego celu.

1. W polu Azure Portal wyszukaj pozycję **Azure Sentinel** i wybierz ją.

2. Na stronie Azure Sentinel obszarów roboczych wybierz pozycję **+Dodaj.**

3. Wybierz obszar roboczy usługi Log Analytics i wybierz pozycję **Dodaj**.

## <a name="enable-data-collector-for-security-events-on-azure-vmware-solution-vms"></a>Włączanie modułu zbierającego dane dla zdarzeń zabezpieczeń na Azure VMware Solution wirtualnych

Teraz możesz nawiązać połączenie z Azure Sentinel źródłami danych, w tym przypadku zdarzeniami zabezpieczeń.

1. Na stronie Azure Sentinel obszarów roboczych wybierz skonfigurowany obszar roboczy.

2. W obszarze Konfiguracja wybierz pozycję **Łączniki danych.**

3. W kolumnie Nazwa łącznika wybierz z listy pozycję **Zdarzenia** zabezpieczeń, a następnie wybierz **pozycję Otwórz stronę łącznika**.

4. Na stronie łącznika wybierz zdarzenia, które chcesz przesyłać strumieniowo, a następnie wybierz **pozycję Zastosuj zmiany.**

    :::image type="content" source="media/azure-security-integration/select-events-you-want-to-stream.png" alt-text="Zrzut ekranu przedstawiający stronę Zdarzeń zabezpieczeń Azure Sentinel, na której można wybrać zdarzenia do strumieniowego przesyłania strumieniowego.":::

## <a name="connect-azure-sentinel-with-azure-security-center"></a>Łączenie Azure Sentinel z Azure Security Center  

1. Na stronie Azure Sentinel obszaru roboczego wybierz skonfigurowany obszar roboczy.

2. W obszarze Konfiguracja wybierz pozycję **Łączniki danych.**

3. Wybierz **Azure Security Center** z listy, a następnie wybierz pozycję **Otwórz stronę łącznika.**

    :::image type="content" source="media/azure-security-integration/connect-security-center-with-azure-sentinel.png" alt-text="Zrzut ekranu przedstawiający stronę Łączniki danych w Azure Sentinel z wyborem w celu nawiązania połączenia Azure Security Center z Azure Sentinel.":::

4. Wybierz **pozycję Połącz,** aby połączyć Azure Security Center z Azure Sentinel.

5. Włącz **opcję Utwórz zdarzenie,** aby wygenerować zdarzenie dla Azure Security Center.

## <a name="create-rules-to-identify-security-threats"></a>Tworzenie reguł w celu identyfikowania zagrożeń bezpieczeństwa

Po połączeniu źródeł danych z Azure Sentinel można utworzyć reguły generowania alertów dotyczących wykrytych zagrożeń. W poniższym przykładzie utworzymy regułę dla prób zalogowania się do systemu Windows Server przy użyciu nieprawidłowego hasła.

1. Na stronie przeglądu Azure Sentinel w obszarze Konfiguracje wybierz pozycję **Analiza.**

2. W obszarze Konfiguracje wybierz pozycję **Analiza.**

3. Wybierz **pozycję +Utwórz,** a następnie z listy rozwijanej wybierz pozycję **Reguła zaplanowanego zapytania.**

4. Na karcie **Ogólne** wprowadź wymagane informacje.

    - Nazwa
    - Opis
    - Taktyka
    - Ważność
    - Stan

    Wybierz **pozycję Dalej: Ustaw logikę >**.

5. Na karcie **Ustaw logikę reguły** wprowadź wymagane informacje.

    - Zapytanie reguły (tutaj jest wyświetlane nasze przykładowe zapytanie)
    
        ```
        SecurityEvent
        |where Activity startswith '4625'
        |summarize count () by IpAddress,Computer
        |where count_ > 3
        ```
        
    - Mapowanie jednostek
    - Planowanie zapytań
    - Próg alertu
    - Grupowanie zdarzeń
    - Pomijanie

    Wybierz opcję **Dalej**.

6. Na karcie Ustawienia zdarzenia  **włącz** opcję Utwórz zdarzenia na podstawie alertów wyzwalanych przez tę regułę analizy i wybierz pozycję **Dalej: Automatyczna odpowiedź >**.
 
    :::image type="content" source="media/azure-security-integration/create-new-analytic-rule-wizard.png" alt-text="Zrzut ekranu przedstawiający kreatora reguły analitycznej służącego do tworzenia nowej reguły w Azure Sentinel. Pokazuje tworzenie zdarzeń z alertów wyzwalanych przez tę regułę jako włączone.":::

7. Wybierz **pozycję Dalej: Przejrzyj >.**

8. Na karcie **Przeglądanie i tworzenie** przejrzyj informacje i wybierz pozycję **Utwórz.**

Po trzeciej nieudanej próbie zalogowania się do systemu Windows Server utworzona reguła wyzwala zdarzenie dla każdej nieudanej próby.

## <a name="view-alerts"></a>Wyświetlanie alertów

Wygenerowane zdarzenia można wyświetlić za pomocą Azure Sentinel. Możesz również przypisywać zdarzenia i zamykać je po ich rozwiązaniach, a wszystko to z poziomu Azure Sentinel.

1. Przejdź do Azure Sentinel przeglądu aplikacji.

2. W obszarze Zarządzanie zagrożeniami wybierz **pozycję Zdarzenia.**

3. Wybierz zdarzenie. Następnie możesz przypisać zdarzenie do zespołu w celu rozwiązania problemu.

    :::image type="content" source="media/azure-security-integration/assign-incident.png" alt-text="Zrzut ekranu Azure Sentinel Zdarzenia z wybranym zdarzeniem i opcją przypisania zdarzenia do rozwiązania.":::

    Po rozwiązaniu problemu możesz go zamknąć.

## <a name="hunt-security-threats-with-queries"></a>Wyszukiwania zagrożeń bezpieczeństwa za pomocą zapytań

Możesz tworzyć zapytania lub używać dostępnych wstępnie zdefiniowanych zapytań w Azure Sentinel, aby identyfikować zagrożenia w środowisku. Poniższe kroki uruchamiają wstępnie zdefiniowane zapytanie.

1. Przejdź do Azure Sentinel przeglądu aplikacji.

2. W obszarze Zarządzanie zagrożeniami wybierz pozycję **Wykrywanie zagrożeń.** Zostanie wyświetlona lista wstępnie zdefiniowanych zapytań.

3. Wybierz zapytanie, a następnie wybierz pozycję **Uruchom zapytanie.**

4. Wybierz **pozycję Wyświetl wyniki,** aby sprawdzić wyniki.

### <a name="create-a-new-query"></a>Tworzenie nowego zapytania

1.  W obszarze Zarządzanie zagrożeniami wybierz pozycję **Wyszukiwania** zagrożeń, a następnie **pozycję +Nowe zapytanie.**

    :::image type="content" source="media/azure-security-integration/create-new-query.png" alt-text="Zrzut ekranu Azure Sentinel wyszukiwania z wyróżnionem + Nowe zapytanie.":::

2. Wypełnij poniższe informacje, aby utworzyć zapytanie niestandardowe.

    - Nazwa
    - Opis
    - Zapytanie niestandardowe
    - Wprowadź mapowanie
    - Taktyka
    
3. Wybierz przycisk **Utwórz**. Następnie możesz wybrać utworzone zapytanie, uruchomić **zapytanie** i **wyświetlić wyniki.**

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak chronić maszyny Azure VMware Solution wirtualne, możesz dowiedzieć się więcej na temat:

- Korzystanie z [pulpitu Azure Defender nawigacyjnego](../security-center/azure-defender-dashboard.md)
- [Zaawansowane wieloetapowe wykrywanie ataków w Azure Sentinel](../azure-monitor/logs/quick-create-workspace.md)
- [Zarządzanie cyklem życia maszyn Azure VMware Solution wirtualnych](lifecycle-management-of-azure-vmware-solution-vms.md)
