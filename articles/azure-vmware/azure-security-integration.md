---
title: Ochrona maszyn wirtualnych rozwiązań VMware platformy Azure z integracją Azure Security Center
description: Dowiedz się, jak chronić maszyny wirtualne rozwiązań VMware platformy Azure przy użyciu natywnych narzędzi zabezpieczeń platformy Azure z poziomu jednego pulpitu nawigacyjnego w Azure Security Center.
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: e461e7e49619f7063c0583975f36a2e3dfe5a724
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96937342"
---
# <a name="protect-your-azure-vmware-solution-vms-with-azure-security-center-integration"></a>Ochrona maszyn wirtualnych rozwiązań VMware platformy Azure z integracją Azure Security Center

Narzędzia zabezpieczeń natywnych platformy Azure zapewniają bezpieczną infrastrukturę dla środowiska hybrydowego platformy Azure, rozwiązania VMware platformy Azure i lokalnych maszyn wirtualnych. W tym artykule opisano sposób konfigurowania zabezpieczeń środowiska hybrydowego dla narzędzi platformy Azure. Będziesz używać różnych narzędzi do identyfikowania i rozwiązywania różnych typów zagrożeń.

## <a name="azure-native-services"></a>Usługi natywne platformy Azure

Oto krótkie podsumowanie każdej usługi platformy Azure Native:

- **Log Analytics obszar roboczy:** Obszar roboczy Log Analytics jest unikatowym środowiskiem do przechowywania danych dziennika. Każdy obszar roboczy ma własne repozytorium danych i konfigurację. Źródła danych i rozwiązania są skonfigurowane do przechowywania danych w określonym obszarze roboczym.
- **Azure Security Center:** Azure Security Center to ujednolicony system zarządzania zabezpieczeniami infrastruktury. Wzmacnia stan zabezpieczeń centrów danych i zapewnia zaawansowaną ochronę przed zagrożeniami w ramach obciążeń hybrydowych w chmurze lub lokalnie.
- **Wskaźnik na platformie Azure:** Azure — wskaźnikowy to natywne w chmurze rozwiązanie do zarządzania zdarzeniami zabezpieczeń (SIEM) i usługa automatycznej reakcji aranżacji (o). Zapewnia ona inteligentne analizy zabezpieczeń i analizy zagrożeń w środowisku. Jest to pojedyncze rozwiązanie do wykrywania alertów, widoczności zagrożeń, aktywnego polowania i reagowania na zagrożenia.

## <a name="topology"></a>Topologia

![Diagram przedstawiający architekturę zintegrowanych zabezpieczeń platformy Azure.](media/azure-security-integration/azure-integrated-security-architecture.png)

Agent Log Analytics umożliwia zbieranie danych dziennika z platformy Azure, rozwiązania VMware platformy Azure i lokalnych maszyn wirtualnych. Dane dziennika są wysyłane do dzienników Azure Monitor i są przechowywane w Log Analytics obszarze roboczym. Agenta Log Analytics można wdrożyć przy użyciu serwerów z obsługą Arc [rozszerzeń maszyn wirtualnych z obsługą](../azure-arc/servers/manage-vm-extensions.md) nowych i istniejących maszyn wirtualnych. 

Po zebraniu dzienników przez obszar roboczy Log Analytics można skonfigurować Log Analytics obszar roboczy przy użyciu Azure Security Center. Azure Security Center ocenia stan luk w zabezpieczeniach maszyn wirtualnych rozwiązań VMware platformy Azure i zgłasza alert dla każdej krytycznej luki w zabezpieczeniach. Na przykład ocenia brakujące poprawki systemu operacyjnego, nieprawidłowe konfiguracje zabezpieczeń i program [Endpoint Protection](../security-center/security-center-services.md).

Obszar roboczy Log Analytics można skonfigurować przy użyciu funkcji wskaźnikowej platformy Azure na potrzeby wykrywania alertów, widoczności zagrożeń, aktywnego polowania i odpowiedzi na zagrożenia. Na powyższym diagramie Azure Security Center jest połączony z punktem kontrolnym platformy Azure przy użyciu łącznika Azure Security Center. Azure Security Center przekaże luki w zabezpieczeniach środowiska do usługi Azure wskaźnikowej, aby utworzyć zdarzenie i zmapować inne zagrożenia. Możesz również utworzyć zapytanie o zaplanowane reguły, aby wykryć niechciane działanie i przekonwertować je na zdarzenia.

## <a name="benefits"></a>Korzyści

- Natywnych usług platformy Azure można używać na potrzeby zabezpieczeń środowiska hybrydowego na platformie Azure, rozwiązań VMware platformy Azure i usług lokalnych.
- Za pomocą obszaru roboczego Log Analytics można zebrać dane lub dzienniki do jednego punktu i przedstawić te same dane w różnych usługach macierzystych platformy Azure.
- Azure Security Center zapewnia funkcje zabezpieczeń, takie jak monitorowanie integralności plików, wykrywanie ataków bez plików, Ocena poprawek systemu operacyjnego, Ocena niezgodności konfiguracji i Ocena programu Endpoint Protection.
- System Azure — wskaźnik kontrolny umożliwia:
    - Zbieraj dane w skali chmury między wszystkimi użytkownikami, urządzeniami, aplikacjami i infrastrukturą, zarówno lokalnie, jak i w wielu chmurach.
    - Wykrywaj wcześniej wykryte zagrożenia.
    - Zbadaj zagrożenia przy użyciu sztucznej analizy i Wyszukaj podejrzane działania na dużą skalę.
    - Szybko reaguj na zdarzenia dzięki wbudowanej aranżacji i automatyzacji typowych zadań.

## <a name="create-a-log-analytics-workspace"></a>Tworzenie obszaru roboczego usługi Log Analytics

Do zbierania danych z różnych źródeł potrzebny jest obszar roboczy Log Analytics. Zapoznaj się z instrukcjami w artykule [tworzenie log Analytics obszaru roboczego z Azure Portal](../azure-monitor/learn/quick-create-workspace.md). 

## <a name="deploy-security-center-and-configure-azure-vmware-solution-vms"></a>Wdrażanie Security Center i Konfigurowanie maszyn wirtualnych rozwiązań VMware platformy Azure

Azure Security Center jest wstępnie skonfigurowanym narzędziem i nie wymaga wdrożenia. W Azure Portal Wyszukaj **Security Center** i wybierz go.

### <a name="enable-azure-defender"></a>Włączanie usługi Azure Defender

Usługa Azure Defender Azure Security Center rozszerza zaawansowaną ochronę przed zagrożeniami w ramach obciążeń hybrydowych zarówno lokalnych, jak i w chmurze. Aby chronić maszyny wirtualne rozwiązań VMware platformy Azure, należy włączyć usługę Azure Defender. 

1. W Security Center wybierz opcję **wprowadzenie**.

2. Wybierz kartę **Uaktualnij** , a następnie wybierz swoją subskrypcję lub obszar roboczy. 

3. Wybierz pozycję **Uaktualnij** , aby włączyć usługę Azure Defender.

## <a name="add-azure-vmware-solution-vms-to-security-center"></a>Dodaj maszyny wirtualne rozwiązań VMware platformy Azure do Security Center

1. W Azure Portal Wyszukaj w **usłudze Azure Arc** i wybierz ją.

2. W obszarze Zasoby wybierz opcję **serwery** , a następnie pozycję **+ Dodaj**.

    :::image type="content" source="media/azure-security-integration/add-server-to-azure-arc.png" alt-text="Zrzut ekranu przedstawiający stronę serwery usługi Azure Arc służącą do dodawania maszyny wirtualnej rozwiązania VMware platformy Azure do platformy Azure.":::

3. Wybierz pozycję **Generuj skrypt**.
 
    :::image type="content" source="media/azure-security-integration/add-server-using-script.png" alt-text="Zrzut ekranu przedstawiający stronę usługi Azure Arc pokazującą opcję dodawania serwera przy użyciu interakcyjnego skryptu."::: 
 
4. Na karcie **wymagania wstępne** wybierz pozycję **dalej**.

5. Na karcie **szczegóły zasobu** wypełnij następujące informacje: 
    - Subskrypcja
    - Grupa zasobów
    - Region 
    - System operacyjny
    - Szczegóły serwera proxy
    
    Następnie wybierz kolejno pozycje **Dalej: Tagi**.

6. Na karcie **Tagi** wybierz pozycję **dalej**.

7. Na karcie **Pobierz i uruchom skrypt** wybierz pozycję **Pobierz**.

8. Określ swój system operacyjny i uruchom skrypt na maszynie wirtualnej rozwiązania VMware platformy Azure.

## <a name="view-recommendations-and-passed-assessments"></a>Wyświetlanie zaleceń i zakończonych ocen

1. W obszarze Azure Security Center wybierz pozycję **spis** w okienku po lewej stronie.

2. W obszarze Typ zasobu wybierz pozycję **serwery — Azure Arc**.
 
     :::image type="content" source="media/azure-security-integration/select-resource-in-security-center.png" alt-text="Zrzut ekranu przedstawiający stronę Azure Security Center spisu przedstawiającą serwery — łuk platformy Azure wybrany w obszarze Typ zasobu.":::

3. Wybierz nazwę zasobu. Zostanie wyświetlona strona zawierająca szczegóły kondycji zabezpieczeń zasobu.

4. W obszarze **rekomendacja** wybierz pozycję **zalecenia**, przetestowane **oceny** i **niedostępne oceny** , aby wyświetlić te szczegóły.

    :::image type="content" source="media/azure-security-integration/view-recommendations-assessments.png" alt-text="Zrzut ekranu przedstawiający Azure Security Center pokazywać zalecenia dotyczące zabezpieczeń i oceny.":::

## <a name="deploy-an-azure-sentinel-workspace"></a>Wdrażanie obszaru roboczego wskaźnikowego platformy Azure

Platforma Azure — Wskaźnikowanie jest tworzona na podstawie obszaru roboczego Log Analytics. Pierwszym krokiem w celu dołączenia do platformy Azure wskaźnikowego jest wybranie obszaru roboczego Log Analytics, który ma być używany w tym celu.

1. W Azure Portal Wyszukaj pozycję **Azure**, a następnie wybierz ją.

2. Na stronie obszary robocze wskaźnikowe platformy Azure wybierz pozycję **+ Dodaj**.

3. Wybierz obszar roboczy Log Analytics i wybierz pozycję **Dodaj**.

## <a name="enable-data-collector-for-security-events-on-azure-vmware-solution-vms"></a>Włączanie modułu zbierającego dane dla zdarzeń zabezpieczeń na maszynach wirtualnych rozwiązań VMware platformy Azure

Teraz możesz przystąpić do łączenia usługi Azure wskaźnikowej ze źródłami danych, w tym przypadku ze zdarzeniami zabezpieczeń.

1. Na stronie obszary robocze wskaźnikowe platformy Azure wybierz skonfigurowany obszar roboczy.

2. W obszarze Konfiguracja wybierz pozycję **Łączniki danych**.

3. W kolumnie Nazwa łącznika wybierz z listy pozycję **zdarzenia zabezpieczeń** , a następnie wybierz pozycję **Otwórz stronę łącznika**.

4. Na stronie łącznik wybierz zdarzenia, które chcesz przesyłać strumieniowo, a następnie wybierz pozycję **Zastosuj zmiany**.

    :::image type="content" source="media/azure-security-integration/select-events-you-want-to-stream.png" alt-text="Zrzut ekranu strony zdarzeń zabezpieczeń w obszarze wskaźnik platformy Azure, w którym można wybrać zdarzenia do przesyłania strumieniowego.":::

## <a name="connect-azure-sentinel-with-azure-security-center"></a>Połącz wskaźnik platformy Azure z usługą Azure Security Center  

1. Na stronie obszaru roboczego wskaźnikowego platformy Azure wybierz skonfigurowany obszar roboczy.

2. W obszarze Konfiguracja wybierz pozycję **Łączniki danych**.

3. Z listy wybierz pozycję **Azure Security Center** a następnie wybierz pozycję **Otwórz stronę łącznika**.

    :::image type="content" source="media/azure-security-integration/connect-security-center-with-azure-sentinel.png" alt-text="Zrzut ekranu przedstawiający stronę łączników danych w centrum kontrolnym platformy Azure z wyborem umożliwiającym łączenie Azure Security Center z platformą Azure.":::

4. Wybierz pozycję **Połącz** , aby połączyć Azure Security Center z platformą Azure — wskaźnikiem.

5. Włącz **Tworzenie zdarzenia** w celu wygenerowania zdarzenia dla Azure Security Center.

## <a name="create-rules-to-identify-security-threats"></a>Tworzenie reguł do identyfikowania zagrożeń bezpieczeństwa

Po połączeniu źródeł danych z platformą Azure wskaźnikiem, można utworzyć reguły generowania alertów na podstawie wykrytych zagrożeń. W poniższym przykładzie utworzymy regułę identyfikującą próby zalogowania się do systemu Windows Server przy użyciu nieprawidłowego hasła.

1. Na stronie Przegląd wskaźnikowego platformy Azure w obszarze konfiguracje wybierz pozycję **Analiza**.

2. W obszarze konfiguracje wybierz pozycję **Analiza**.

3. Wybierz pozycję **+ Utwórz** i na liście rozwijanej wybierz pozycję **zaplanowana reguła zapytania**.

4. Na karcie **Ogólne** wprowadź wymagane informacje.

    - Nazwa
    - Opis
    - Taktykę
    - Ważność
    - Stan

    Wybierz pozycję **Dalej: ustaw >logiki reguł**.

5. Na karcie **Ustawianie logiki reguły** wprowadź wymagane informacje.

    - Zapytanie reguły (tutaj pokazując nasze przykładowe zapytanie)
    
        ```
        SecurityEvent
        |where Activity startswith '4625'
        |summarize count () by IpAddress,Computer
        |where count_ > 3
        ```
        
    - Mapuj jednostki
    - Planowanie zapytań
    - Próg alertu
    - Grupowanie zdarzeń
    - Pomijanie

    Wybierz pozycję **Dalej**.

6. Na karcie **Ustawienia zdarzenia** Włącz opcję **Utwórz zdarzenia na podstawie alertów wyzwalanych przez tę regułę analizy** , a następnie wybierz kolejno pozycje **Dalej: automatyczna odpowiedź >**.
 
    :::image type="content" source="media/azure-security-integration/create-new-analytic-rule-wizard.png" alt-text="Zrzut ekranu kreatora reguły analitycznej służący do tworzenia nowej reguły w centrum kontrolnym Azure wskazujący na tworzenie zdarzeń na podstawie alertów wyzwalanych przez tę regułę analizy jako włączone.":::

7. Wybierz kolejno pozycje **Dalej: przegląd >**.

8. Na karcie **Przegląd i tworzenie** Przejrzyj informacje, a następnie wybierz pozycję **Utwórz**.

Po trzecim nieudanej próbie zalogowania się do systemu Windows Server utworzona reguła wyzwala zdarzenie dla każdej nieudanej próby.

## <a name="view-generated-alerts"></a>Wyświetl wygenerowane alerty

Wygenerowane zdarzenia można wyświetlić za pomocą platformy Azure — wskaźnik. Możesz również przypisywać zdarzenia i zamykać je po ich rozwiązaniu, a wszystko to w ramach platformy Azure.

1. Przejdź do strony przeglądu wskaźnikowego platformy Azure.

2. W obszarze Zarządzanie zagrożeniami wybierz pozycję **zdarzenia**.

3. Wybierz zdarzenie. Następnie można przypisać zdarzenie do zespołu w celu rozwiązania problemu.

    :::image type="content" source="media/azure-security-integration/assign-incident.png" alt-text="Zrzut ekranu przedstawiający stronę zdarzenia wskaźnikowego platformy Azure z wybranym zdarzeniem i opcję przypisywania zdarzenia do rozwiązania.":::

    Po rozwiązaniu problemu można go zamknąć.

## <a name="hunt-security-threats-with-queries"></a>Zapytanie dotyczące zagrożeń bezpieczeństwa z zapytaniami

Możesz tworzyć zapytania lub użyć dostępnego wstępnie zdefiniowanego zapytania w wskaźniku kontrolnym platformy Azure, aby identyfikować zagrożenia w Twoim środowisku. Poniższe kroki uruchamiają wstępnie zdefiniowane zapytanie.

1. Przejdź do strony przeglądu wskaźnikowego platformy Azure.

2. W obszarze Zarządzanie zagrożeniami wybierz pozycję **łowiectwo**. Zostanie wyświetlona lista wstępnie zdefiniowanych zapytań.

3. Wybierz zapytanie, a następnie wybierz polecenie **Uruchom zapytanie**.

4. Wybierz pozycję **Wyświetl wyniki** , aby sprawdzić wyniki.

### <a name="create-a-new-query"></a>Tworzenie nowego zapytania

1.  W obszarze Zarządzanie zagrożeniami wybierz pozycję **łowiectwo** , a następnie **+ nowe zapytanie**.

    :::image type="content" source="media/azure-security-integration/create-new-query.png" alt-text="Zrzut ekranu przedstawiający stronę łowiectwa wskaźnikowego platformy Azure z wyróżnioną i nową kwerendą.":::

2. Wypełnij poniższe informacje, aby utworzyć zapytanie niestandardowe.

    - Nazwa
    - Opis
    - Zapytanie niestandardowe
    - Wprowadź mapowanie
    - Taktykę
    
3. Wybierz przycisk **Utwórz**. Następnie można wybrać utworzone zapytanie, **uruchomić zapytanie** i **wyświetlić wyniki**.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak korzystać z [pulpitu nawigacyjnego usługi Azure Defender](../security-center/azure-defender-dashboard.md).
- Poznaj pełen zakres ochrony oferowany przez [usługę Azure Defender](../security-center/azure-defender.md).
- Dowiedz się więcej [na temat zaawansowanego wykrywania ataków potokach wieloetapowych na platformie Azure](../azure-monitor/learn/quick-create-workspace.md).
