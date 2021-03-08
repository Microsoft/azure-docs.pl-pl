---
title: Azure Security Center często zadawane pytania — zbieranie danych i agenci
description: Często zadawane pytania dotyczące zbierania danych, agentów i obszarów roboczych dla Azure Security Center, produktu, który pomaga zapobiegać zagrożeniom, wykrywać je i reagować na nie
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2020
ms.author: memildin
ms.openlocfilehash: 4ef01045d1ca16d0101cdd9ccfcd118231cd28de
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102456117"
---
# <a name="faq---questions-about-data-collection-agents-and-workspaces"></a>Często zadawane pytania — pytania dotyczące zbierania danych, agentów i obszarów roboczych

Security Center zbiera dane z maszyn wirtualnych platformy Azure, zestawów skalowania maszyn wirtualnych, kontenerów IaaS oraz komputerów spoza platformy Azure (w tym maszyn lokalnych) do monitorowania luk w zabezpieczeniach i zagrożeń. Dane są zbierane przy użyciu agenta Log Analytics, który odczytuje różne konfiguracje związane z zabezpieczeniami i dzienniki zdarzeń z komputera i kopiuje dane do obszaru roboczego w celu przeprowadzenia analizy.


## <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>Czy są naliczane opłaty za dzienniki Azure Monitor w obszarach roboczych utworzonych przez Security Center?

Nie. Obszary robocze utworzone przez Security Center, podczas gdy skonfigurowano do rozliczania Azure Monitor dzienników na węzeł, nie są naliczane opłaty za dzienniki Azure Monitor. Rozliczanie Security Center jest zawsze uzależnione od zasad zabezpieczeń Security Center i rozwiązań zainstalowanych w obszarze roboczym:

- **Usługa Azure Defender wyłączona** — Security Center umożliwia korzystanie z rozwiązania "SecurityCenterFree" w domyślnym obszarze roboczym. Nie zostanie naliczona opłata, jeśli usługa Azure Defender jest wyłączona.

- **Usługa Azure Defender on** — Security Center włącza rozwiązanie "zabezpieczenia" w domyślnym obszarze roboczym.

Aby uzyskać szczegółowe informacje o cenach w wybranej walucie i według regionu, zobacz [Security Center Cennik](https://azure.microsoft.com/pricing/details/security-center/).

> [!NOTE]
> Warstwa cenowa usługi log Analytics dla obszarów roboczych utworzonych przez Security Center nie ma wpływu na Security Center rozliczanie.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a name="what-is-the-log-analytics-agent"></a>Co to jest Agent Log Analytics?

Aby monitorować luki w zabezpieczeniach i zagrożeniach, Azure Security Center zależy od [agenta log Analytics](../azure-monitor/agents/log-analytics-agent.md) — jest to ten sam Agent, który jest używany przez usługę Azure monitor. 

Agent jest czasami określany jako Microsoft Monitoring Agent (lub "MMA"). 

Agent zbiera informacje o różnych konfiguracjach związanych z zabezpieczeniami i dziennikach zdarzeń z połączonych maszyn, a następnie kopiuje dane do obszaru roboczego Log Analytics w celu dalszej analizy. Przykładami takich danych są: typ i wersja systemu operacyjnego, Dzienniki systemu operacyjnego (dzienniki zdarzeń systemu Windows), uruchomione procesy, Nazwa maszyny, adresy IP i zalogowany użytkownik.

Upewnij się, że na komputerach jest uruchomiony jeden z obsługiwanych systemów operacyjnych dla agenta zgodnie z opisem na następujących stronach:

* [Agent Log Analytics obsługujący systemy operacyjne Windows](../azure-monitor/agents/agents-overview.md#supported-operating-systems)

* [Agent Log Analytics dla systemów operacyjnych obsługiwanych przez system Linux](../azure-monitor/agents/agents-overview.md#supported-operating-systems)

Dowiedz się więcej o [danych zbieranych przez agenta log Analytics](security-center-enable-data-collection.md).




## <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-log-analytics-agent-installation"></a>Co kwalifikuje maszynę wirtualną do automatycznej aprowizacji instalacji agenta Log Analytics?

Maszyny wirtualne z systemem Windows lub Linux IaaS kwalifikują się, jeśli:

- Rozszerzenie agenta Log Analytics nie jest obecnie zainstalowane na maszynie wirtualnej.
- Maszyna wirtualna jest w stanie uruchomienia.
- [Agent maszyny wirtualnej platformy Azure](../virtual-machines/extensions/agent-windows.md) z systemem Windows lub Linux jest zainstalowany.
- Maszyna wirtualna nie jest używana jako urządzenie, takie jak Zapora aplikacji sieci Web lub Zapora nowej generacji.


## <a name="where-is-the-default-log-analytics-workspace-created"></a>Gdzie jest tworzony domyślny obszar roboczy Log Analytics?

Lokalizacja domyślnego obszaru roboczego zależy od regionu platformy Azure:

- W przypadku maszyn wirtualnych w Stany Zjednoczone i Brazylia lokalizacją obszaru roboczego jest Stany Zjednoczone
- W przypadku maszyn wirtualnych w Kanadzie lokalizacją obszaru roboczego jest Kanada
- W przypadku maszyn wirtualnych w Europie lokalizacją obszaru roboczego jest Europa
- W przypadku maszyn wirtualnych w Wielkiej Brytanii lokalizacją obszaru roboczego jest Zjednoczone Królestwo
- W przypadku maszyn wirtualnych w Azja Wschodnia i Azja Południowo-Wschodnia Lokalizacja obszaru roboczego to Azja
- W przypadku maszyn wirtualnych w Korei Lokalizacja obszaru roboczego to Korea
- W przypadku maszyn wirtualnych w Indiach Lokalizacja obszaru roboczego to Indie
- W przypadku maszyn wirtualnych w Japonii Lokalizacja obszaru roboczego to Japonia
- W przypadku maszyn wirtualnych w Chinach Lokalizacja obszaru roboczego to Chiny
- W przypadku maszyn wirtualnych w Australii lokalizacją obszaru roboczego jest Australia


## <a name="what-security-events-are-collected-by-the-log-analytics-agent"></a>Jakie zdarzenia zabezpieczeń są zbierane przez agenta Log Analytics?

Aby zapoznać się z pełną listą zdarzeń zabezpieczeń zebranych przez agenta, zobacz, [jakie typy zdarzeń są przechowywane dla ustawień "typowe" i "minimalne" zdarzeń zabezpieczeń?](security-center-enable-data-collection.md#what-event-types-are-stored-for-common-and-minimal).

> [!IMPORTANT]
> Należy pamiętać, że w przypadku niektórych usług, takich jak Zapora platformy Azure, jeśli włączono rejestrowanie i wybrano zasób z czatem do zarejestrowania (na przykład ustawienie dziennika na *pełne*) może zostać wyświetlony znaczący wpływ na potrzeby magazynu log Analytics obszaru roboczego. 


## <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Czy mogę usunąć domyślne obszary robocze utworzone przez Security Center?

**Nie zaleca się usuwania domyślnego obszaru roboczego.** Security Center używa domyślnych obszarów roboczych do przechowywania danych zabezpieczeń z maszyn wirtualnych. W przypadku usunięcia obszaru roboczego Security Center nie będzie mógł zebrać tych danych, a niektóre zalecenia dotyczące zabezpieczeń i alerty są niedostępne.

Aby odzyskać, Usuń agenta Log Analytics na maszynach wirtualnych połączonych z usuniętym obszarem roboczym. Security Center ponownie instaluje agenta i tworzy nowe domyślne obszary robocze.

## <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Jak mogę użyć istniejącego obszaru roboczego Log Analytics?

Można wybrać istniejący obszar roboczy Log Analytics do przechowywania danych zbieranych przez Security Center. Aby użyć istniejącego obszaru roboczego Log Analytics:

- Obszar roboczy musi być skojarzony z wybraną subskrypcją platformy Azure.
- Aby uzyskać dostęp do obszaru roboczego, musisz mieć uprawnienia do odczytu.

Aby wybrać istniejący obszar roboczy Log Analytics:

1. W menu Security Center wybierz pozycję **cennik & ustawienia**.
1. Wybierz odpowiednią subskrypcję.
1. Otwórz stronę **autoaprowizacji** .
1. W przypadku agenta Log Analytics wybierz pozycję **Edytuj konfigurację**. 

    :::image type="content" source="./media/security-center-enable-data-collection/edit-configuration-auto-deploy-agent.png" alt-text="Konfiguracja agenta Log Analytics do użycia podczas wdrażania automatycznej" lightbox="./media/security-center-enable-data-collection/edit-configuration-auto-deploy-agent.png":::

1. Wybierz pozycję **Połącz maszyny wirtualne platformy Azure z innym obszarem roboczym** i wybierz istniejący obszar roboczy.

    :::image type="content" source="./media/security-center-enable-data-collection/choose-workspace.png" alt-text="Wybór obszaru roboczego innego niż domyślny dla agenta Log Analytics do raportowania" lightbox="./media/security-center-enable-data-collection/choose-workspace.png":::

    > [!TIP]
    > Lista zawiera tylko obszary robocze, do których masz dostęp, i które znajdują się w subskrypcji platformy Azure.

1. Wybierz pozycję **Zapisz**. Zostanie wyświetlony monit o ponowne skonfigurowanie monitorowanych maszyn wirtualnych.

    - Wybierz pozycję **nie** , jeśli chcesz, aby nowe ustawienia obszaru roboczego były **stosowane tylko do nowych maszyn wirtualnych**. Nowe ustawienia obszaru roboczego mają zastosowanie tylko do nowych instalacji agenta; nowo odnalezione maszyny wirtualne, na których nie zainstalowano agenta Log Analytics.
    - Wybierz pozycję **tak** , jeśli chcesz, aby nowe ustawienia obszaru roboczego były **stosowane do wszystkich maszyn wirtualnych**. Ponadto wszystkie maszyny wirtualne połączone z Security Center utworzonym obszarem roboczym są ponownie połączone z nowym docelowym obszarem roboczym.

    > [!NOTE]
    > W przypadku wybrania opcji **tak** nie usuwaj żadnych obszarów roboczych utworzonych przez Security Center, dopóki wszystkie maszyny wirtualne nie zostaną ponownie połączone z nowym docelowym obszarem roboczym. Ta operacja kończy się niepowodzeniem, jeśli obszar roboczy zostanie zbyt wcześnie usunięty.


## <a name="what-if-the-log-analytics-agent-was-already-installed-as-an-extension-on-the-vm"></a>Co zrobić, jeśli Agent Log Analytics został już zainstalowany jako rozszerzenie na maszynie wirtualnej?<a name="mmaextensioninstalled"></a>

Gdy Agent monitorowania jest zainstalowany jako rozszerzenie, konfiguracja rozszerzenia umożliwia raportowanie tylko jednego obszaru roboczego. Security Center nie przesłania istniejących połączeń z obszarami roboczymi użytkowników. Security Center będą przechowywać dane zabezpieczeń z maszyny wirtualnej w obszarze roboczym, który jest już połączony, pod warunkiem, że zostało na nim zainstalowane rozwiązanie "Security" lub "SecurityCenterFree". Security Center może uaktualnić wersję rozszerzenia do najnowszej wersji w tym procesie.

Aby uzyskać więcej informacji, zobacz [Automatyczne Inicjowanie obsługi w przypadku istniejącej instalacji agenta](security-center-enable-data-collection.md#preexisting).



## <a name="what-if-a-log-analytics-agent-is-directly-installed-on-the-machine-but-not-as-an-extension-direct-agent"></a>Co zrobić, jeśli Agent Log Analytics jest bezpośrednio zainstalowany na komputerze, ale nie jako rozszerzenie (Agent bezpośredni)?<a name="directagentinstalled"></a>

Jeśli Agent Log Analytics jest instalowany bezpośrednio na maszynie wirtualnej (nie jako rozszerzenie platformy Azure), Security Center zainstaluje rozszerzenie agenta Log Analytics i może uaktualnić agenta Log Analytics do najnowszej wersji.

Zainstalowany agent będzie kontynuował raportowanie do już skonfigurowanych obszarów roboczych, a ponadto wyśle raport do obszaru roboczego skonfigurowanego w Security Center (wiele multihostingu jest obsługiwana na maszynach z systemem Windows).

Jeśli skonfigurowany obszar roboczy jest obszarem roboczym użytkownika (nie Security Center domyślnym obszarem roboczym), należy zainstalować na nim rozwiązanie "Security" lub "SecurityCenterFree" dla Security Center, aby rozpocząć przetwarzanie zdarzeń z maszyn wirtualnych i komputerów zgłaszanych do tego obszaru roboczego.

W przypadku maszyn z systemem Linux Agent multihostingu nie jest jeszcze obsługiwany — w związku z tym jeśli zostanie wykryta istniejąca instalacja agenta, automatyczne Inicjowanie obsługi nie zostanie wykonane i konfiguracja maszyny nie zostanie zmieniona.

W przypadku istniejących maszyn w ramach subskrypcji dołączanych do Security Center przed marcem 17 2019, gdy zostanie wykryty istniejący Agent, rozszerzenie agenta Log Analytics nie zostanie zainstalowane i nie będzie to miało takiego oddziaływania. W przypadku tych maszyn Zobacz zalecenie "Rozwiązywanie problemów z kondycją agenta monitorowania na komputerach", aby rozwiązać problemy z instalacją agenta na tych maszynach

Aby uzyskać więcej informacji, zobacz następną sekcję [co się stanie, jeśli System Center Operations Manager lub Agent usługi OMS Direct jest już zainstalowany na mojej maszynie wirtualnej?](#scomomsinstalled)

## <a name="what-if-a-system-center-operations-manager-agent-is-already-installed-on-my-vm"></a>Co zrobić, jeśli Agent System Center Operations Manager jest już zainstalowany na mojej maszynie wirtualnej?<a name="scomomsinstalled"></a>

Program Security Center zainstaluje rozszerzenie agenta Log Analytics obok istniejącej System Center Operations Manager agenta. Istniejący agent będzie kontynuował raportowanie do serwera System Center Operations Manager w normalny sposób. Należy pamiętać, że Agent Operations Manager i Agent Log Analytics mają wspólne biblioteki uruchomieniowe, które zostaną zaktualizowane do najnowszej wersji w trakcie tego procesu. Uwaga — Jeśli jest zainstalowana wersja 2012 agenta Operations Manager, nie włączaj automatycznej aprowizacji (możliwości zarządzania mogą zostać utracone, gdy serwer Operations Manager jest również w wersji 2012).


## <a name="what-is-the-impact-of-removing-these-extensions"></a>Jaki jest wpływ usunięcia tych rozszerzeń?

Jeśli usuniesz rozszerzenie Microsoft Monitoring, Security Center nie będzie w stanie zbierać danych zabezpieczeń z maszyny wirtualnej, a niektóre zalecenia dotyczące zabezpieczeń i alerty są niedostępne. W ciągu 24 godzin Security Center określa, że maszyna wirtualna nie ma rozszerzenia i ponownie instaluje rozszerzenie.


## <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Jak mogę zatrzymać automatyczną instalację agenta i tworzenie obszaru roboczego?

Automatyczne Inicjowanie obsługi dla subskrypcji w zasadach zabezpieczeń można wyłączyć, ale nie jest to zalecane. Wyłączanie limitów udostępniania autoaprowizacji Security Center zaleceń i alertów. Aby wyłączyć automatyczne Inicjowanie obsługi:

1. W menu Security Center wybierz pozycję **cennik & ustawienia**.
1. Wybierz odpowiednią subskrypcję.
1. Jeśli subskrypcja ma włączoną usługę Azure Defender, Otwórz **plan usługi Azure Defender** i wybierz pozycję **Azure Defender off**.

    :::image type="content" source="./media/security-center-platform-migration-faq/pricing-tier.png" alt-text="Włączanie lub wyłączanie usługi Azure Defender":::

1. Na stronie **autozastrzeganie** wybierz opcję pióro i Wyłącz funkcję autoaprowizacji na stronie  **zbierania danych zasady zabezpieczeń** .

    :::image type="content" source="./media/security-center-enable-data-collection/agent-toggles.png" alt-text="Włącz autowdrażanie dla agenta Log Analytics":::

1. Wybierz pozycję **Zapisz**.


## <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>Czy należy zrezygnować z automatycznej instalacji agenta i tworzenia obszaru roboczego?

> [!NOTE]
> Pamiętaj, aby przejrzeć sekcje [, jakie są implikacje rezygnacji?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) i [zalecanych czynności](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) , jeśli zdecydujesz się zrezygnować z automatycznej aprowizacji.

Możesz zrezygnować z automatycznej aprowizacji, jeśli ma to zastosowanie:

- Automatyczna instalacja agenta Security Center ma zastosowanie do całej subskrypcji. Nie można zastosować instalacji automatycznej do podzestawu maszyn wirtualnych. Jeśli istnieją krytyczne maszyny wirtualne, których nie można zainstalować za pomocą agenta Log Analytics, należy zrezygnować z automatycznej aprowizacji.
- Instalacja rozszerzenia agenta Log Analytics aktualizuje wersję agenta. Dotyczy to zarówno agenta bezpośredniego, jak i agenta System Center Operations Managerowego (w tym ostatnim przypadku, w którym Agent Operations Manager i Log Analytics udostępnia wspólne biblioteki środowiska uruchomieniowego, które zostaną zaktualizowane w procesie). Jeśli zainstalowany agent Operations Manager jest w wersji 2012 i zostanie uaktualniony, możliwości zarządzania mogą zostać utracone, gdy serwer Operations Manager jest również w wersji 2012. Rozważ rezygnację z automatycznej aprowizacji, jeśli zainstalowany agent Operations Manager jest w wersji 2012.
- Jeśli masz niestandardowy obszar roboczy poza subskrypcją (scentralizowany obszar roboczy), należy zrezygnować z automatycznej aprowizacji. Możesz ręcznie zainstalować rozszerzenie agenta Log Analytics i połączyć je z obszarem roboczym bez Security Center zastępowania połączenia.
- Jeśli chcesz uniknąć tworzenia wielu obszarów roboczych na subskrypcję i masz własny niestandardowy obszar roboczy w ramach subskrypcji, będziesz mieć dwie opcje:

   1. Możesz zrezygnować z automatycznej aprowizacji. Po migracji Ustaw domyślne ustawienia obszaru roboczego zgodnie z opisem w artykule [Jak mogę użyć istniejącego obszaru roboczego log Analytics?](#how-can-i-use-my-existing-log-analytics-workspace)

   1. Można też zezwolić na ukończenie migracji, Log Analytics agenta do zainstalowania na maszynach wirtualnych oraz maszyn wirtualnych połączonych z utworzonym obszarem roboczym. Następnie wybierz własny niestandardowy obszar roboczy przez ustawienie domyślnego ustawienia obszaru roboczego z ustawieniem w celu ponownego skonfigurowania już zainstalowanych agentów. Aby uzyskać więcej informacji, zobacz [Jak mogę korzystać z istniejącego obszaru roboczego log Analytics?](#how-can-i-use-my-existing-log-analytics-workspace)


## <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Jakie są implikacje rezygnacji z automatycznej aprowizacji?

Po zakończeniu migracji Security Center nie może zbierać danych zabezpieczeń z maszyny wirtualnej, a niektóre zalecenia dotyczące zabezpieczeń i alerty są niedostępne. Jeśli zrezygnujesz z programu, Zainstaluj agenta Log Analytics ręcznie. Przed [rezygnacją Zobacz zalecane kroki](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning).


## <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Jakie zalecane kroki należy wykonać w przypadku rezygnacji z automatycznej aprowizacji?

Ręcznie zainstaluj rozszerzenie agenta Log Analytics, aby Security Center mógł zbierać dane dotyczące zabezpieczeń z maszyn wirtualnych i udostępniać zalecenia i alerty. Aby uzyskać wskazówki dotyczące instalacji, zobacz [Instalacja agenta dla maszyny wirtualnej z systemem Windows](../virtual-machines/extensions/oms-windows.md) lub [instalacji agenta dla maszyny wirtualnej z systemem Linux](../virtual-machines/extensions/oms-linux.md) .

Możesz połączyć agenta z dowolnym istniejącym obszarem roboczym lub Security Center utworzonym obszarem roboczym. Jeśli dla niestandardowego obszaru roboczego nie włączono rozwiązań "Security" lub "SecurityCenterFree", należy zastosować rozwiązanie. Aby zastosować tę opcję, wybierz niestandardowy obszar roboczy lub subskrypcję i Zastosuj warstwę cenową na stronie **zasady zabezpieczeń — warstwa cenowa** .

:::image type="content" source="./media/security-center-platform-migration-faq/pricing-tier.png" alt-text="Włączanie lub wyłączanie usługi Azure Defender":::

Security Center włączy poprawne rozwiązanie w obszarze roboczym w oparciu o wybraną warstwę cenową.


## <a name="how-do-i-remove-oms-extensions-installed-by-security-center"></a>Jak mogę usunąć rozszerzenia pakietu OMS zainstalowane przez program Security Center?<a name="remove-oms"></a>

Agenta Log Analytics można usunąć ręcznie. Nie jest to zalecane, ponieważ ogranicza Security Center zaleceń i alertów.

> [!NOTE]
> Jeśli zbieranie danych jest włączone, Security Center zainstaluje agenta po jego usunięciu.  Należy wyłączyć zbieranie danych przed ręcznym usunięciem agenta. Zobacz Jak mogę Zatrzymaj automatyczną instalację agenta i tworzenie obszaru roboczego? instrukcje dotyczące wyłączania zbierania danych.

Aby ręcznie usunąć agenta:

1.    W portalu Otwórz **log Analytics**.

1.    Na stronie Log Analytics wybierz obszar roboczy:

1.    Wybierz Maszyny wirtualne, które nie mają być monitorowane, i wybierz pozycję **Rozłącz**.

   ![Usuwanie agenta][3]

> [!NOTE]
> Jeśli maszyna wirtualna z systemem Linux ma już agenta pakietu OMS bez rozszerzenia, usunięcie tego rozszerzenia spowoduje również konieczność jego ponownego zainstalowania.


## <a name="how-do-i-disable-data-collection"></a>Jak mogę wyłączyć zbieranie danych?

Automatyczna obsługa administracyjna jest zdecydowanie zalecana w celu uzyskania alertów zabezpieczeń i zaleceń dotyczących aktualizacji systemu, luk w zabezpieczeniach systemu operacyjnego i programu Endpoint Protection. Funkcja autoaprowizacji jest domyślnie wyłączona.

Jeśli ta funkcja została włączona, ale teraz chcesz ją wyłączyć:

1. W [Azure Portal](https://portal.azure.com)Otwórz **Security Center** i wybierz pozycję **Cennik i ustawienia**.

1. Wybierz subskrypcję, dla której chcesz wyłączyć automatyczne Inicjowanie obsługi.

1. W obszarze **autoinicjowanie obsługi** wyłącz przełącznik dla agenta log Analytics.


## <a name="how-do-i-enable-data-collection"></a>Jak mogę włączyć zbieranie danych?

Zbieranie danych dla subskrypcji platformy Azure można włączyć w zasadach zabezpieczeń. Aby włączyć zbieranie danych. [Zaloguj się do Azure Portal](https://portal.azure.com), wybierz pozycję **Przeglądaj**, wybierz pozycję **Security Center**, a następnie wybierz pozycję **zasady zabezpieczeń**. Wybierz subskrypcję, dla której chcesz włączyć automatyczną obsługę administracyjną. Po wybraniu zasad zabezpieczeń subskrypcji zostanie otwarte **zbieranie danych** . W obszarze **autoinicjowanie obsługi** wybierz pozycję **włączone**.


## <a name="what-happens-when-data-collection-is-enabled"></a>Co się stanie, gdy zbieranie danych jest włączone?

Gdy włączona jest Automatyczna obsługa administracyjna, Security Center inicjuje agenta Log Analytics na wszystkich obsługiwanych maszynach wirtualnych platformy Azure i utworzonych nowych. Zalecana jest Automatyczna obsługa administracyjna, ale jest również dostępna ręczna instalacja agenta. [Dowiedz się, jak zainstalować rozszerzenie agenta log Analytics](../azure-monitor/vm/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension). 

Agent włącza zdarzenie tworzenia procesu 4688 i pole *wiersza polecenia* wewnątrz zdarzenia 4688. Nowe procesy utworzone na maszynie wirtualnej są rejestrowane przez dziennik zdarzeń i monitorowane przez usługi wykrywania Security Center. Aby uzyskać więcej informacji na temat szczegółów zarejestrowanych dla każdego nowego procesu, zobacz [Opis pól w 4688](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). Agent zbiera również zdarzenia 4688 utworzone na maszynie wirtualnej i zapisuje je w wyszukiwaniu.

Agent umożliwia również zbieranie danych na potrzeby [adaptacyjnych kontrolek aplikacji](security-center-adaptive-application.md), Security Center konfiguruje lokalne zasady funkcji AppLocker w trybie inspekcji, aby zezwolić na wszystkie aplikacje. Te zasady spowodują generowanie przez funkcję AppLocker zdarzeń, które następnie są zbierane i wykorzystywane przez Security Center. Należy pamiętać, że te zasady nie zostaną skonfigurowane na wszystkich komputerach, na których już skonfigurowano zasady funkcji AppLocker. 

Gdy Security Center wykryje podejrzane działanie na maszynie wirtualnej, klient zostanie powiadomiony pocztą e-mail, jeśli podano [Informacje o kontakcie zabezpieczeń](security-center-provide-security-contact-details.md) . Alert jest również widoczny na pulpicie nawigacyjnym alertów zabezpieczeń Security Center.


## <a name="will-security-center-work-using-an-oms-gateway"></a>Czy Security Center będzie działała przy użyciu bramy pakietu OMS?

Tak. Azure Security Center wykorzystuje Azure Monitor do zbierania danych z maszyn wirtualnych i serwerów platformy Azure przy użyciu agenta Log Analytics.
Aby zebrać dane, każda maszyna wirtualna i serwer muszą łączyć się z Internetem przy użyciu protokołu HTTPS. Połączenie może być bezpośrednie, przy użyciu serwera proxy lub przez [bramę pakietu OMS](../azure-monitor/agents/gateway.md).


## <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Czy Agent monitorowania ma wpływ na wydajność moich serwerów?

Agent zużywa nominalną ilość zasobów systemowych i powinien mieć niewielki wpływ na wydajność. Aby uzyskać więcej informacji na temat wpływu na wydajność oraz agenta i rozszerzenia, zobacz [Przewodnik planowania i](security-center-planning-and-operations-guide.md#data-collection-and-storage)obsługi.




<!--Image references-->
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/use-another-workspace.png