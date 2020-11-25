---
title: Połącz Operations Manager Azure Monitor | Microsoft Docs
description: Aby obsługiwać istniejącą inwestycję w programie System Center Operations Manager i korzystać z rozszerzonych możliwości usługi Log Analytics, można zintegrować program Operations Manager z obszarem roboczym.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/24/2020
ms.openlocfilehash: 2a4f24da51b9e9e78c3df3e7d1437a380306e300
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95975580"
---
# <a name="connect-operations-manager-to-azure-monitor"></a>Połącz Operations Manager z Azure Monitor

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Aby zachować istniejące inwestycje w [System Center Operations Manager](/system-center/scom/key-concepts?view=sc-om-1807) i korzystać z rozszerzonych możliwości z Azure monitor, możesz zintegrować Operations Manager z obszarem roboczym log Analytics. Dzięki temu można wykorzystać możliwości dzienników w Azure Monitor podczas kontynuowania korzystania z Operations Manager do:

* Monitorowanie kondycji usług IT za pomocą programu Operations Manager
* Utrzymywanie integracji z rozwiązaniami ITSM obsługującymi zarządzanie zdarzeniami i problemami
* Zarządzanie cyklem życia agentów wdrożonych lokalnie oraz na maszynach wirtualnych IaaS w chmurze publicznej monitorowanych za pomocą programu Operations Manager

Integracja z System Center Operations Manager dodaje wartość do strategii operacji usługi przy użyciu szybkości i wydajności Azure Monitor podczas zbierania, przechowywania i analizowania danych dziennika z Operations Manager. Azure Monitor kwerendy dzienników pomagają w skorelowaniu błędów i rozwiązaniu problemów oraz wykorzystaniu cykli w zakresie obsługi istniejącego procesu zarządzania problemami. Elastyczność aparatu zapytań w celu zbadania danych dotyczących wydajności, zdarzeń i alertów przy użyciu zaawansowanych pulpitów nawigacyjnych i funkcji raportowania w celu udostępnienia tych danych w zrozumiały sposób, demonstruje Azure Monitor w Operations Manager.

Agenci raportujący do Operations Manager grupy zarządzania zbierają dane z serwerów w oparciu o [log Analytics źródła danych](agent-data-sources.md) i rozwiązania, które zostały włączone w Twoim obszarze roboczym. W zależności od włączonych rozwiązań ich dane są wysyłane bezpośrednio z Operations Manager serwera zarządzania do usługi lub z powodu ilości danych zbieranych w systemie zarządzanym przez agenta są wysyłane bezpośrednio z agenta do Log Analytics obszaru roboczego. Serwer zarządzania przekazuje dane bezpośrednio do usługi. Nie są one nigdy zapisywane w magazynie danych ani w operacyjnej bazie danych. Gdy serwer zarządzania utraci łączność z Azure Monitor, przechowuje dane lokalnie w pamięci podręcznej do momentu ponownego ustanowienia komunikacji. Jeśli serwer zarządzania jest w trybie offline z powodu planowanej konserwacji lub nieplanowanej awarii, inny serwer zarządzania w grupie zarządzania wznawia łączność z Azure Monitor.  

Na poniższym diagramie przedstawiono połączenie między serwerami zarządzania i agentami w System Center Operations Manager grupie zarządzania i Azure Monitor, w tym kierunek i porty.

![oms-operations-manager-integration-diagram](./media/om-agents/oms-operations-manager-connection.png)

Jeśli zasady zabezpieczeń IT nie zezwalają komputerom w sieci na łączenie się z Internetem, serwery zarządzania można skonfigurować do łączenia się z bramą Log Analytics, aby otrzymywać informacje o konfiguracji i wysyłać zebrane dane w zależności od włączonych rozwiązań. Aby uzyskać więcej informacji i instrukcje dotyczące konfigurowania Operations Manager grupy zarządzania w celu komunikowania się za pośrednictwem bramy Log Analytics do Azure Monitor, zobacz [łączenie komputerów z Azure monitor przy użyciu bramy log Analytics](./gateway.md).  

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy zapoznać się z następującymi wymaganiami.

* Azure Monitor obsługuje tylko System Center Operations Manager 2016 lub nowsze, Operations Manager 2012 SP1 UR6 lub nowsze, a Operations Manager 2012 R2 UR2 lub nowszy. Obsługa serwera proxy została dodana w programach Operations Manager 2012 SP1 UR7 i Operations Manager 2012 R2 UR3.
* Integracja System Center Operations Manager 2016 z chmurą dla instytucji rządowych USA wymaga zaktualizowanego pakietu administracyjnego klasyfikatora zawartego w pakiecie zbiorczym aktualizacji 2 lub nowszym. System Center Operations Manager 2012 R2 wymaga zaktualizowanego pakietu administracyjnego klasyfikatora dołączonego do pakietu zbiorczego aktualizacji 3 lub nowszego.
* Wszystkie agenty programu Operations Manager muszą spełniać minimalne wymagania dotyczące obsługi. Upewnij się, że agenci są w minimalnej aktualizacji, w przeciwnym razie komunikacja z agentem systemu Windows może zakończyć się niepowodzeniem i wygenerować błędy w dzienniku zdarzeń Operations Manager.
* Obszar roboczy usługi Log Analytics. Aby uzyskać więcej informacji, zapoznaj się z tematem przegląd [log Analytics obszaru roboczego](design-logs-deployment.md).
* Uwierzytelnianie na platformie Azure odbywa się przy użyciu konta, które jest członkiem [roli współautor log Analytics](manage-access.md#manage-access-using-azure-permissions).

* Obsługiwane regiony — System Center Operations Manager do nawiązywania połączenia z obszarem roboczym Log Analytics, obsługiwane są tylko następujące regiony platformy Azure:
    - Zachodnio-środkowe stany USA
    - Australia Południowo-Wschodnia
    - West Europe
    - East US
    - Azja Południowo-Wschodnia
    - Japan East
    - Południowe Zjednoczone Królestwo
    - Indie Środkowe
    - Kanada Środkowa
    - Zachodnie stany USA 2

>[!NOTE]
>Najnowsze zmiany interfejsów API platformy Azure uniemożliwią klientom pomyślne skonfigurowanie integracji między grupą zarządzania a Azure Monitor po raz pierwszy. W przypadku klientów, którzy już mogli zintegrować grupę zarządzania z usługą, nie ma to wpływu, chyba że trzeba ponownie skonfigurować istniejące połączenie.  
>Wydano nowy pakiet administracyjny dla następujących wersji Operations Manager:
> - W przypadku System Center Operations Manager 2019 ten pakiet administracyjny jest dołączony do nośnika źródłowego i instalowany podczas instalacji nowej grupy zarządzania lub podczas uaktualniania.
>- Pakiet administracyjny Operations Manager 1801 ma również zastosowanie do Operations Manager 1807.
>- W przypadku System Center Operations Manager 1801 Pobierz pakiet administracyjny z tego [miejsca](https://www.microsoft.com/download/details.aspx?id=57173).
>- W przypadku programu System Center 2016 — Operations Manager Pobierz pakiet administracyjny z tego [miejsca](https://www.microsoft.com/download/details.aspx?id=57172).  
>- W przypadku System Center Operations Manager 2012 R2 Pobierz pakiet administracyjny z tego [miejsca](https://www.microsoft.com/download/details.aspx?id=57171).  


### <a name="network"></a>Sieć

W poniższych informacjach przedstawiono informacje o konfiguracji serwera proxy i zapory wymagane dla Operations Manager agenta, serwerów zarządzania i konsoli operacje w celu komunikowania się z Azure Monitor. Ruch z każdego składnika jest wychodzący z sieci do Azure Monitor.

|Zasób | Numer portu| Obejście inspekcji HTTP|  
|---------|------|-----------------------|  
|**Agent**|||  
|\*.ods.opinsights.azure.com| 443 |Tak|  
|\*.oms.opinsights.azure.com| 443|Tak|  
|\*.blob.core.windows.net| 443|Tak|  
|\*.azure-automation.net| 443|Tak|  
|**Serwer zarządzania**|||  
|\*.service.opinsights.azure.com| 443||  
|\*.blob.core.windows.net| 443| Tak|  
|\*.ods.opinsights.azure.com| 443| Tak|  
|*.azure-automation.net | 443| Tak|  
|**Operations Manager konsoli programu Azure Monitor**|||  
|service.systemcenteradvisor.com| 443||  
|\*.service.opinsights.azure.com| 443||  
|\*.live.com| 80 i 443||  
|\*.microsoft.com| 80 i 443||  
|\*.microsoftonline.com| 80 i 443||  
|\*.mms.microsoft.com| 80 i 443||  
|login.windows.net| 80 i 443||  
|portal.loganalytics.io| 80 i 443||
|api.loganalytics.io| 80 i 443||
|docs.loganalytics.io| 80 i 443||  

### <a name="tls-12-protocol"></a>Protokół TLS 1,2

Aby zapewnić bezpieczeństwo danych przesyłanych do Azure Monitor, zdecydowanie zachęcamy do skonfigurowania agenta i grupy zarządzania do korzystania z co najmniej Transport Layer Security (TLS) 1,2. Starsze wersje protokołu TLS/SSL (SSL) są zagrożone i chociaż nadal działają tak, aby umożliwić zgodność z poprzednimi wersjami, nie są **zalecane**. Aby uzyskać dodatkowe informacje, zapoznaj się z [bezpiecznym przesyłaniem danych przy użyciu protokołu TLS 1,2](./data-security.md#sending-data-securely-using-tls-12).

## <a name="connecting-operations-manager-to-azure-monitor"></a>Łączenie Operations Manager z Azure Monitor

Wykonaj następujące czynności, aby skonfigurować grupę zarządzania programu Operations Manager na potrzeby nawiązywania połączenia z jednym z obszarów roboczych usługi Log Analytics.

Podczas początkowej rejestracji grupy zarządzania Operations Manager z obszarem roboczym Log Analytics, opcja określania konfiguracji serwera proxy dla grupy zarządzania nie jest dostępna w konsoli operacje.  Ta opcja staje się dostępna dopiero po pomyślnym zarejestrowaniu grupy zarządzania w usłudze.  Aby obejść ten sposób, należy zaktualizować konfigurację serwera proxy systemu przy użyciu narzędzia Netsh w systemie, w którym uruchomiono konsolę Operacje programu, w celu skonfigurowania integracji i wszystkich serwerów zarządzania w grupie zarządzania.  

1. Otwórz wiersz polecenia z podwyższonymi uprawnieniami.
   a. Przejdź do pozycji **Start** i wpisz **cmd**.
   b. Kliknij prawym przyciskiem myszy **wiersz polecenia** i wybierz polecenie Uruchom jako administrator * *.
1. Wprowadź następujące polecenie i naciśnij klawisz **Enter**:

    `netsh winhttp set proxy <proxy>:<port>`

Po wykonaniu poniższych kroków w celu zintegrowania z programem Azure Monitor można usunąć konfigurację, uruchamiając polecenie, `netsh winhttp reset proxy` a następnie używając opcji **Konfiguruj serwer proxy** w konsoli operacje, aby określić serwer proxy lub log Analytics bramy.

1. W konsoli programu Operations Manager wybierz obszar roboczy **Administracja**.
1. Rozwiń węzeł Operations Management Suite i kliknij pozycję **Połączenie**.
1. Kliknij link **Zarejestruj się w konsoli Operations Management Suite**.
1. Na stronie **Kreator dołączania konsoli Operations Management Suite: Uwierzytelnianie** wprowadź adres e-mail lub numer telefonu i hasło konta administratora, które jest skojarzone z subskrypcją pakietu OMS, a następnie kliknij przycisk **Zaloguj się**.

   >[!NOTE]
   >Nazwa pakietu Operations Management Suite została wycofana.

1. Po pomyślnym uwierzytelnieniu w **Kreatorze przechodzenia do usługi Operations Management Suite** zostanie wyświetlony monit o wybranie obszaru roboczego dzierżawy, subskrypcji i log Analytics. Jeśli masz więcej niż jeden obszar roboczy, wybierz z listy rozwijanej ten obszar roboczy, który ma zostać zarejestrowany w grupie zarządzania programu Operations Manager, a następnie kliknij przycisk **Dalej**.

   > [!NOTE]
   > Program Operations Manager obsługuje jednocześnie tylko jeden obszar roboczy usługi Log Analytics. Połączenie i komputery zarejestrowane do Azure Monitor z poprzednim obszarem roboczym zostaną usunięte z Azure Monitor.
   >
   >
1. Na stronie **Kreator dołączania konsoli Operations Management Suite: Podsumowanie** sprawdź ustawienia i jeśli są one poprawne, kliknij przycisk **Utwórz**.
1. Na stronie **Kreator dołączania konsoli Operations Management Suite: Kończenie** kliknij przycisk **Zamknij**.

### <a name="add-agent-managed-computers"></a>Dodawanie komputerów zarządzanych przez agenta

Po skonfigurowaniu integracji z obszarem roboczym Log Analytics nawiązuje ono tylko połączenie z usługą. żadne dane nie są zbierane z agentów zgłaszanych do grupy zarządzania. Nie będzie to możliwe dopiero po skonfigurowaniu określonych komputerów zarządzanych przez agentów do zbierania danych dziennika dla Azure Monitor. Obiekty komputerów można wybierać indywidualnie lub przez wybranie grupy zawierającej obiekty komputerów z systemem Windows. Nie można wybrać grupy zawierającej wystąpienia innej klasy, na przykład dysków logicznych lub baz danych SQL.

1. Otwórz konsolę programu Operations Manager i wybierz obszar roboczy **Administracja**.
1. Rozwiń węzeł Operations Management Suite i kliknij pozycję **Połączenie**.
1. Kliknij link **Dodaj komputer/grupę** pod nagłówkiem Akcje w prawej części okienka.
1. W oknie dialogowym **Wyszukiwanie komputerów** można przeprowadzić wyszukiwanie komputerów lub grup monitorowanych przez program Operations Manager. Wybierz komputery lub grupy, w tym Operations Manager serwer zarządzania, aby dołączyć do Azure Monitor, kliknij przycisk **Dodaj**, a następnie kliknij przycisk **OK**.

Komputery i grupy skonfigurowane na potrzeby zbierania danych z węzła Komputery zarządzane można wyświetlić w sekcji Operations Management Suite w obszarze roboczym **Administracja** konsoli zarządzania operacjami. W tym miejscu możesz odpowiednio do potrzeb dodawać lub usuwać komputery i grupy.

### <a name="configure-proxy-settings-in-the-operations-console"></a>Konfigurowanie ustawień serwera proxy w konsoli zarządzania operacjami

Jeśli wewnętrzny serwer proxy jest między grupą zarządzania a Azure Monitor, wykonaj następujące czynności. Te ustawienia są zarządzane centralnie z grupy zarządzania i dystrybuowane do systemów zarządzanych przez agentów, które znajdują się w zakresie do zbierania danych dziennika dla Azure Monitor.  Jest to korzystne, gdy niektóre rozwiązania pomijają serwer zarządzania i wysyłają dane bezpośrednio do usługi.

1. Otwórz konsolę programu Operations Manager i wybierz obszar roboczy **Administracja**.
1. Rozwiń węzeł Operations Management Suite, a następnie kliknij pozycję **Połączenia**.
1. W widoku Połączenie OMS kliknij pozycję **Konfiguruj serwer proxy**.
1. Na stronie **Kreator konsoli Operations Management Suite: Serwer proxy** wybierz pozycję **Użyj serwera proxy, aby uzyskać dostęp do konsoli Operations Management Suite**, a następnie wpisz adres URL z numerem portu, na przykład http://corpproxy:80 i kliknij przycisk **Zakończ**.

Jeśli serwer proxy wymaga uwierzytelnienia, wykonaj następujące kroki, aby skonfigurować poświadczenia i ustawienia, które mają być propagowane do zarządzanych komputerów, które będą zgłaszać Azure Monitor w grupie zarządzania.

1. Otwórz konsolę programu Operations Manager i wybierz obszar roboczy **Administracja**.
1. W obszarze **Konfiguracja Uruchom jako** wybierz pozycję **Profile**.
1. Otwórz profil **Serwer proxy profilu Uruchom jako usługi System Center Advisor**.
1. W kreatorze profilu Uruchom jako kliknij przycisk Dodaj, aby użyć konta Uruchom jako. Możesz utworzyć [konto Uruchom jako](/previous-versions/system-center/system-center-2012-R2/hh321655(v=sc.12)) lub użyć istniejącego konta. Konto to musi mieć wystarczające uprawnienia do komunikacji za pośrednictwem serwera proxy.
1. Aby ustawić konto do zarządzania, wybierz pozycję **Wybrana klasa, grupa lub obiekt**, kliknij polecenie **Wybierz...**, a następnie kliknij pozycję **Grupuj...** w celu otwarcie okna **Wyszukiwanie grup**.
1. Wyszukaj **grupę serwerów monitorowania usługi Microsoft System Center Advisor**, a następnie ją wybierz. Po wybraniu grupy kliknij przycisk **OK**, aby zamknąć okno **Wyszukiwanie grup**.
1. Kliknij przycisk **OK**, aby zamknąć okno **Dodawanie konta Uruchom jako**.
1. Kliknij przycisk **Zapisz**, aby zakończyć pracę kreatora i zapisać zmiany.

Po utworzeniu połączenia i skonfigurowaniu agentów, którzy będą zbierać i raportować dane dziennika do Azure Monitor, następująca konfiguracja zostanie zastosowana w grupie zarządzania, niekoniecznie w kolejności:

* Tworzone jest konto Uruchom jako o nazwie **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate**. Zostaje ono skojarzone z profilem Uruchom jako **Microsoft System Center Advisor Run As Profile Blob** i dotyczy dwóch klas — **Collection Server** i **Operations Manager Management Group**.
* Tworzone są dwa łączniki.  Pierwszy nosi nazwę **Microsoft.SystemCenter. Advisor. Dataconnecter** i jest automatycznie konfigurowany z subskrypcją, która przekazuje wszystkie alerty wygenerowane z wystąpień wszystkich klas w grupie zarządzania do Azure monitor. Drugi łącznik to **Łącznik usługi Advisor**, który jest odpowiedzialny za komunikowanie się z Azure monitor i udostępnianiem danych.
* Agenci i grupy wybrane do zbierania danych w grupie zarządzania są dodawane do **grupy serwera monitorowania usługi Microsoft System Center Advisor**.

## <a name="management-pack-updates"></a>Aktualizacje pakietu administracyjnego

Po zakończeniu konfiguracji Operations Manager grupy zarządzania nawiązuje połączenie z Azure Monitor. Serwer zarządzania synchronizuje się z usługą internetową i odbiera zaktualizowane informacje o konfiguracji w postaci pakietów administracyjnych dla włączonych rozwiązań zintegrowanych z programem Operations Manager. Operations Manager sprawdza aktualizacje tych pakietów administracyjnych i automatycznie pobiera i importuje je, gdy są dostępne. To zachowanie jest kontrolowane w szczególności przez dwie reguły:

* **Microsoft.SystemCenter. Advisor. MPUpdate** — aktualizuje podstawowe Azure monitor pakiety administracyjne. Ta reguła jest domyślnie uruchamiana co 12 godzin.
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** — aktualizuje pakiety administracyjne rozwiązań, które zostały włączone w obszarze roboczym. Ta reguła jest domyślnie uruchamiana co pięć (5) minut.

Te dwie reguły można przesłonić, aby zapobiec automatycznemu pobieraniu, wyłączając je lub modyfikując częstotliwość synchronizacji serwera zarządzania z Azure Monitor, aby określić, czy nowy pakiet administracyjny jest dostępny i czy powinien zostać pobrany. Wykonaj instrukcje [How to Override a Rule or Monitor](/previous-versions/system-center/system-center-2012-R2/hh212869(v=sc.12)) (Jak przesłonić regułę lub monitor), aby zmodyfikować parametr **Frequency** (Częstotliwość) wartością w sekundach w celu zmiany harmonogramu synchronizacji lub parametr **Enabled** (Włączone) w celu wyłączenia reguł. Przesłonięcie dotyczy wszystkich obiektów klasy Operations Manager Management Group.

Aby kontynuować proces kontroli zmian w celu kontrolowania wersji pakietów administracyjnych w produkcyjnej grupie zarządzania, można wyłączyć reguły i włączyć je w określonym czasie, jeśli są dozwolone aktualizacje. Jeśli w swoim środowisku masz grupę deweloperską lub grupę zarządzania kontroli jakości, która ma połączenie z Internetem, możesz skonfigurować tę grupę zarządzania za pomocą obszaru roboczego usługi Log Analytics do obsługi tego scenariusza. Dzięki temu można przeglądać i oceniać iteracyjne wersje pakietów administracyjnych Azure Monitor przed ich zwolnieniem do produkcyjnej grupy zarządzania.

## <a name="switch-an-operations-manager-group-to-a-new-log-analytics-workspace"></a>Przełączanie grupy programu Operations Manager do nowego obszaru roboczego usługi Log Analytics

1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).
1. W witrynie Azure Portal kliknij pozycję **Więcej usług** w lewym dolnym rogu. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **Log Analytics** i utwórz obszar roboczy.  
1. Otwórz konsolę programu Operations Manager przy użyciu konta należącego do roli Administratorzy programu Operations Manager i wybierz obszar roboczy **Administracja**.
1. Rozwiń węzeł Log Analytics, a następnie wybierz pozycję **połączenia**.
1. Wybierz link **Skonfiguruj ponownie konsolę Operation Management Suite** na środku panelu.
1. Postępuj zgodnie z **kreatorem** dołączania log Analytics i wprowadź adres e-mail lub numer telefonu oraz hasło do konta administratora skojarzonego z nowym obszarem roboczym log Analytics.

   > [!NOTE]
   > Na stronie **Kreator dołączania konsoli Operations Management Suite: Wybieranie obszaru roboczego** przedstawiono istniejący używany obszar roboczy.
   >
   >

## <a name="validate-operations-manager-integration-with-azure-monitor"></a>Weryfikuj integrację Operations Manager z Azure Monitor

Użyj następującego zapytania, aby pobrać połączone wystąpienia Operations Manager:

```azurepowershell
union *
| where isnotempty(MG)
| where not(ObjectName == 'Advisor Metrics' or ObjectName == 'ManagedSpace')
| summarize LastData = max(TimeGenerated) by lowerCasedComputerName=tolower(Computer), MG, ManagementGroupName
| sort by lowerCasedComputerName asc
```

## <a name="remove-integration-with-azure-monitor"></a>Usuń integrację z usługą Azure Monitor

Jeśli integracja między grupą zarządzania programu Operations Manager i obszarem roboczym usługi Log Analytics nie jest już potrzebna, należy wykonać kilka czynności, aby prawidłowo usunąć połączenie i konfigurację grupy zarządzania. Poniższa procedura umożliwia zaktualizowanie obszaru roboczego Log Analytics przez usunięcie odwołania do grupy zarządzania, usunięcie łączników Azure Monitor, a następnie usunięcie pakietów administracyjnych obsługujących integrację z usługą.  

Pakiety administracyjne dla wdrożonych rozwiązań, które integrują się z Operations Manager i pakiety administracyjne wymagane do obsługi integracji z programem Azure Monitor, nie mogą być łatwo usunięte z grupy zarządzania. Wynika to z faktu, że niektóre pakiety administracyjne Azure Monitor mają zależności od innych powiązanych pakietów administracyjnych. Aby usunąć pakiety administracyjne, które są zależne od innych pakietów administracyjnych, pobierz skrypt [usuwający pakiet administracyjny z zależnościami](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e) z Centrum skryptów w witrynie TechNet.  

1. Otwórz powłokę poleceń programu Operations Manager przy użyciu konta należącego do roli Administratorzy programu Operations Manager.

    > [!WARNING]
    > Przed przejściem do następnego kroku upewnij się, nie masz żadnych niestandardowych pakietów administracyjnych z ciągiem Advisor lub IntelligencePack w nazwie. W przeciwnym razie kolejne kroki spowodują ich usunięcie z grupy zarządzania.
    >

1. W wierszu polecenia powłoki wpisz `Get-SCOMManagementPack -name "*Advisor*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. Następnie wpisz `Get-SCOMManagementPack -name "*IntelligencePack*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. Aby usunąć wszystkie pozostałe pakiety administracyjne, które są zależne od innych pakietów administracyjnych programu System Center Advisor, użyj skryptu *RecursiveRemove.ps1* pobranego wcześniej z Centrum skryptów w witrynie TechNet.  

    > [!NOTE]
    > Krok usuwania pakietów administracyjnych usługi Advisor przy użyciu programu PowerShell nie spowoduje automatycznego usunięcia wewnętrznych pakietów administracyjnych programu Microsoft System Center Advisor ani programu Microsoft System Center Advisor.  Nie należy próbować ich usunąć.  
    >  

1. Otwórz konsolę zarządzania operacjami programu Operations Manager przy użyciu konta należącego do roli Administratorzy programu Operations Manager.
1. W obszarze **Administracja** wybierz węzeł **Pakiety administracyjne**, a następnie w polu tekstowym **Wyszukaj:** wpisz **Advisor** i upewnij się, że następujące pakiety administracyjne są nadal zaimportowane w Twojej grupie zarządzania:

   * Microsoft System Center Advisor
   * Microsoft System Center Advisor Internal

1. W Azure Portal kliknij kafelek **Ustawienia** .
1. Wybierz pozycję **połączone źródła**.
1. W tabeli poniżej sekcji System Center Operations Manager powinna zostać wyświetlona nazwa grupy zarządzania, która ma zostać usunięta z obszaru roboczego. W kolumnie **Ostatnie dane** kliknij link **Usuń**.  

    > [!NOTE]
    > Link **Usuń** nie będzie dostępny po 14 dniach, jeśli nie zostanie wykryta żadna aktywność w połączonej grupie zarządzania.  
    >

1. Zostanie wyświetlone okno z prośbą o potwierdzenie operacji usunięcia.  Kliknij pozycję **Tak**, aby kontynuować.

Aby usunąć dwa łączniki — jeden o nazwie Microsoft.SystemCenter.Advisor.DataConnector, a drugi o nazwie Advisor Connector, zapisz poniższy skrypt programu PowerShell na swoim komputerze i wykonaj go, korzystając z poniższych przykładów:

```
    .\OM2012_DeleteConnectors.ps1 "Advisor Connector" <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 "Microsoft.SystemCenter.Advisor.DataConnector" <ManagementServerName>
```

> [!NOTE]
> Z wyjątkiem serwera zarządzania komputer, na którym uruchomisz ten skrypt, powinien mieć zainstalowaną odpowiednią powłokę poleceń programu Operations Manager w zależności od wersji grupy zarządzania.
>
>

```powershell
    param(
    [String] $connectorName,
    [String] $msName="localhost"
    )
    $mg = new-object Microsoft.EnterpriseManagement.ManagementGroup $msName
    $admin = $mg.GetConnectorFrameworkAdministration()
    ##########################################################################################
    # Configures a connector with the specified name.
    ##########################################################################################
    function New-Connector([String] $name)
    {
         $connectorForTest = $null;
         foreach($connector in $admin.GetMonitoringConnectors())
    {
    if($connectorName.Name -eq ${name})
    {
         $connectorForTest = Get-SCOMConnector -id $connector.id
    }
    }
    if ($connectorForTest -eq $null)
    {
         $testConnector = New-Object Microsoft.EnterpriseManagement.ConnectorFramework.ConnectorInfo
         $testConnector.Name = $name
         $testConnector.Description = "${name} Description"
         $testConnector.DiscoveryDataIsManaged = $false
         $connectorForTest = $admin.Setup($testConnector)
         $connectorForTest.Initialize();
    }
    return $connectorForTest
    }
    ##########################################################################################
    # Removes a connector with the specified name.
    ##########################################################################################
    function Remove-Connector([String] $name)
    {
        $testConnector = $null
        foreach($connector in $admin.GetMonitoringConnectors())
       {
        if($connector.Name -eq ${name})
       {
         $testConnector = Get-SCOMConnector -id $connector.id
       }
      }
     if ($testConnector -ne $null)
     {
        if($testConnector.Initialized)
     {
     foreach($alert in $testConnector.GetMonitoringAlerts())
     {
       $alert.ConnectorId = $null;
       $alert.Update("Delete Connector");
     }
     $testConnector.Uninitialize()
     }
     $connectorIdForTest = $admin.Cleanup($testConnector)
     }
    }
    ##########################################################################################
    # Delete a connector's Subscription
    ##########################################################################################
    function Delete-Subscription([String] $name)
    {
      foreach($testconnector in $admin.GetMonitoringConnectors())
      {
      if($testconnector.Name -eq $name)
      {
        $connector = Get-SCOMConnector -id $testconnector.id
      }
    }
    $subs = $admin.GetConnectorSubscriptions()
    foreach($sub in $subs)
    {
      if($sub.MonitoringConnectorId -eq $connector.id)
      {
        $admin.DeleteConnectorSubscription($admin.GetConnectorSubscription($sub.Id))
      }
     }
    }
    #New-Connector $connectorName
    write-host "Delete-Subscription"
    Delete-Subscription $connectorName
    write-host "Remove-Connector"
    Remove-Connector $connectorName
```

W przyszłości, jeśli planujesz ponowne połączenie grupy zarządzania z obszarem roboczym Log Analytics, musisz ponownie zaimportować `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb` plik pakietu administracyjnego. W zależności od wersji programu System Center Operations Manager wdrożonego w środowisku ten plik można znaleźć w następującej lokalizacji:

* Na nośniku źródłowym w folderze `\ManagementPacks` dla programu System Center 2016 — Operations Manager lub nowszego.
* W najnowszym pakiecie zbiorczym aktualizacji zastosowanym w grupie zarządzania. W przypadku Operations Manager 2012 folder źródłowy jest `%ProgramFiles%\Microsoft System Center 2012\Operations Manager\Server\Management Packs for Update Rollups` i dla 2012 R2 znajduje się w `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups` .

## <a name="next-steps"></a>Następne kroki

Aby dodać funkcje i zebrać dane, zobacz [Dodawanie rozwiązań Azure monitor z Galeria rozwiązań](../insights/solutions.md).

