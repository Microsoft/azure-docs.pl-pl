---
title: Monitoruj Azure AD B2C z Azure Monitor
titleSuffix: Azure AD B2C
description: Dowiedz się, jak rejestrować Azure AD B2C zdarzenia za pomocą Azure Monitor przy użyciu delegowanego zarządzania zasobami.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.author: mimart
ms.subservice: B2C
ms.date: 01/29/2021
ms.openlocfilehash: e44a029c61db5a22513387772c2b0d7a3e4d1a40
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2021
ms.locfileid: "99219234"
---
# <a name="monitor-azure-ad-b2c-with-azure-monitor"></a>Monitoruj Azure AD B2C z Azure Monitor

Użyj Azure Monitor, aby kierować dzienniki logowania i [inspekcji](view-audit-logs.md) w usłudze Azure Active Directory B2C (Azure AD B2C) do różnych rozwiązań monitorowania. Dzienniki można zachować do długoterminowego użytku lub zintegrować z narzędziami do zarządzania informacjami i zdarzeniami zabezpieczeń innych firm (SIEM), aby uzyskać wgląd w środowisko.

Zdarzenia dziennika można kierować do:

* Konto usługi Azure [Storage](../storage/blobs/storage-blobs-introduction.md).
* [Obszar roboczy log Analytics](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace) (do analizowania danych, tworzenia pulpitów nawigacyjnych i alertów dotyczących określonych zdarzeń).
* [Centrum zdarzeń](../event-hubs/event-hubs-about.md) platformy Azure (i integracja z wystąpieniami logiki Splunk i Sumo).

![Azure Monitor](./media/azure-monitor/azure-monitor-flow.png)

W tym artykule dowiesz się, jak przenieść dzienniki do obszaru roboczego usługi Azure Log Analytics. Następnie możesz utworzyć pulpit nawigacyjny lub utworzyć alerty, które są oparte na działaniach Azure AD B2C użytkownikach.

> [!IMPORTANT]
> Planując transfer dzienników Azure AD B2C do różnych rozwiązań monitorowania lub repozytorium, należy wziąć pod uwagę następujące kwestie. Dzienniki Azure AD B2C zawierają dane osobowe. Takie dane powinny być przetwarzane w sposób zapewniający odpowiednie bezpieczeństwo danych osobowych, w tym ochronę przed nieautoryzowanym lub sprzecznym przetwarzaniem, przy użyciu odpowiednich środków technicznych lub organizacyjnych.


## <a name="deployment-overview"></a>Omówienie wdrażania

Azure AD B2C wykorzystuje [monitorowanie Azure Active Directory](../active-directory/reports-monitoring/overview-monitoring.md). Aby włączyć *Ustawienia diagnostyczne* w Azure Active Directory w dzierżawie Azure AD B2C, należy użyć [usługi Azure Lighthouse](../lighthouse/concepts/azure-delegated-resource-management.md) do [delegowania zasobu](../lighthouse/concepts/azure-delegated-resource-management.md), co umożliwi Azure AD B2C ( **dostawcy usług**) Zarządzanie zasobem usługi Azure AD ( **klienta**). Po wykonaniu kroków opisanych w tym artykule będziesz mieć dostęp do grupy zasobów *Azure-AD-B2C-monitor* zawierającej [obszar roboczy Log Analytics](../azure-monitor/learn/quick-create-workspace.md) w portalu **Azure AD B2C** . Będziesz mieć możliwość transferu dzienników z Azure AD B2C do Log Analytics obszaru roboczego.

Podczas tego wdrożenia autoryzujesz użytkownika lub grupę w katalogu Azure AD B2C, aby skonfigurować wystąpienie obszaru roboczego Log Analytics w ramach dzierżawy zawierającej subskrypcję platformy Azure. Aby utworzyć autoryzację, należy wdrożyć szablon [Azure Resource Manager](../azure-resource-manager/index.yml) w dzierżawie usługi Azure AD zawierającym subskrypcję programu.

Na poniższym diagramie przedstawiono składniki, które można skonfigurować w usłudze Azure AD i dzierżawy Azure AD B2C.

![Projekcja grupy zasobów](./media/azure-monitor/resource-group-projection.png)

Podczas tego wdrożenia skonfigurujesz dzierżawę Azure AD B2C i dzierżawę usługi Azure AD, w której będzie hostowana Log Analytics obszar roboczy. Konto używane do uruchamiania wdrożenia musi mieć przypisaną rolę [administratora globalnego](../active-directory/roles/permissions-reference.md#limit-use-of-global-administrator) w obu tych dzierżawcach. Ważne jest również, aby upewnić się, że użytkownik jest zalogowany do poprawnego katalogu, jak opisano w sekcji.

## <a name="1-create-or-choose-resource-group"></a>1. Utwórz lub wybierz grupę zasobów

Najpierw utwórz lub wybierz grupę zasobów zawierającą Log Analytics docelowy obszar roboczy, który będzie otrzymywał dane z Azure AD B2C. Podczas wdrażania szablonu Azure Resource Manager należy określić nazwę grupy zasobów.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Na pasku narzędzi portalu wybierz ikonę **katalogów i subskrypcji** , a następnie wybierz katalog zawierający **dzierżawę usługi Azure AD**.
1. [Utwórz grupę zasobów](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) lub wybierz istniejącą. W tym przykładzie stosowana jest Grupa zasobów o nazwie *Azure-AD-B2C-monitor*.

## <a name="2-create-a-log-analytics-workspace"></a>2. Utwórz obszar roboczy Log Analytics

**Obszar roboczy log Analytics** jest unikatowym środowiskiem dla Azure monitor danych dziennika. Ten obszar roboczy Log Analytics służy do zbierania danych z Azure AD B2C [dzienników inspekcji](view-audit-logs.md), a następnie wizualizowania go przy użyciu zapytań i skoroszytów lub tworzenia alertów.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Na pasku narzędzi portalu wybierz ikonę **katalogów i subskrypcji** , a następnie wybierz katalog zawierający **dzierżawę usługi Azure AD**.
1. [Utwórz obszar roboczy log Analytics](../azure-monitor/learn/quick-create-workspace.md). Ten przykład używa Log Analytics obszaru roboczego o nazwie *AzureAdB2C* w grupie zasobów o nazwie *Azure-AD-B2C-monitor*.

## <a name="3-delegate-resource-management"></a>3. delegowanie zarządzania zasobami

W tym kroku wybierasz dzierżawę Azure AD B2C jako **dostawcę usługi**. Należy również zdefiniować autoryzacje potrzebne do przypisywania odpowiednich wbudowanych ról platformy Azure do grup w dzierżawie usługi Azure AD.

### <a name="31-get-your-azure-ad-b2c-tenant-id"></a>3,1. Uzyskaj identyfikator dzierżawy Azure AD B2C

Najpierw pobierz **Identyfikator dzierżawy** katalogu Azure AD B2C (znany również jako identyfikator katalogu).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę **Azure AD B2C** .
1. Wybierz pozycję **Azure Active Directory**, wybierz pozycję **Przegląd**.
1. Zapisz **Identyfikator dzierżawy**.

### <a name="32-select-a-security-group"></a>3,2 Wybierz grupę zabezpieczeń

Teraz wybierz grupę lub użytkownika Azure AD B2C, do której chcesz nadać uprawnienia grupie zasobów utworzonej wcześniej w katalogu zawierającym twoją subskrypcję.  

Aby ułatwić zarządzanie, zalecamy korzystanie z *grup* użytkowników usługi Azure AD dla każdej roli, co pozwala na dodawanie lub usuwanie poszczególnych użytkowników do grupy zamiast przypisywania uprawnień bezpośrednio do tego użytkownika. W tym instruktażu dodamy grupę zabezpieczeń.

> [!IMPORTANT]
> Aby można było dodać uprawnienia dla grupy usługi Azure AD, **Typ grupy** musi być ustawiony na **zabezpieczenia**. Ta opcja jest wybierana podczas tworzenia grupy. Aby uzyskać więcej informacji, zobacz [Tworzenie podstawowej grupy i dodawanie członków w usłudze Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

1. Po wybraniu **Azure Active Directory** w katalogu **Azure AD B2C** wybierz pozycję **grupy**, a następnie wybierz grupę. Jeśli nie masz istniejącej grupy, Utwórz grupę **zabezpieczeń** , a następnie Dodaj członków. Aby uzyskać więcej informacji, postępuj zgodnie z procedurą [Utwórz podstawową grupę i Dodaj członków przy użyciu Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). 
1. Wybierz pozycję **Przegląd** i Zapisz **Identyfikator obiektu** grupy.

### <a name="33-create-an-azure-resource-manager-template"></a>3,3 Tworzenie szablonu Azure Resource Manager

Następnie utworzysz szablon Azure Resource Manager, który przydaje Azure AD B2C dostęp do utworzonej wcześniej grupy zasobów usługi Azure AD (na przykład *Azure-AD-B2C-monitor*). Wdróż szablon z przykładu GitHub za pomocą przycisku **Wdróż na platformie Azure** , który otwiera Azure Portal i umożliwia skonfigurowanie i wdrożenie szablonu bezpośrednio w portalu. Aby wykonać te kroki, upewnij się, że zalogowano się do dzierżawy usługi Azure AD (nie dzierżawy Azure AD B2C).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Na pasku narzędzi portalu wybierz ikonę **katalogów i subskrypcji** , a następnie wybierz katalog zawierający dzierżawę usługi **Azure AD** .
3. Użyj przycisku **Wdróż na platformie Azure** , aby otworzyć Azure Portal i wdrożyć szablon bezpośrednio w portalu. Aby uzyskać więcej informacji, zobacz [Tworzenie szablonu Azure Resource Manager](../lighthouse/how-to/onboard-customer.md#create-an-azure-resource-manager-template).

   [![Wdrażanie na platformie Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Lighthouse-samples%2Fmaster%2Ftemplates%2Frg-delegated-resource-management%2FrgDelegatedResourceManagement.json)

5. Na stronie **wdrożenie niestandardowe** wprowadź następujące informacje:

   | Pole   | Definicja |
   |---------|------------|
   | Subskrypcja |  Wybierz katalog zawierający subskrypcję platformy Azure, w której utworzono grupę zasobów *usługi Azure-AD-B2C-monitor* . |
   | Region (Region)| Wybierz region, w którym zostanie wdrożony zasób.  | 
   | Nazwa oferty msp| Nazwa opisująca tę definicję. Na przykład *Azure AD B2C monitorowanie*.  |
   | Opis oferty msp| Krótki opis oferty. Na przykład program *włącza Azure monitor w Azure AD B2C*.|
   | Zarządzane przez identyfikator dzierżawy| **Identyfikator dzierżawy** dzierżawy Azure AD B2C (znany również jako identyfikator katalogu). |
   |Autoryzacje|Określ tablicę JSON obiektów, która obejmuje usługę Azure AD `principalId` , `principalIdDisplayName` i platformę Azure `roleDefinitionId` . `principalId`Jest **identyfikatorem obiektu** grupy B2C lub użytkownika, który będzie miał dostęp do zasobów w ramach tej subskrypcji platformy Azure. W tym instruktażu Określ identyfikator obiektu grupy, który został wcześniej zarejestrowany. Dla programu `roleDefinitionId` Użyj [wbudowanej wartości roli](../role-based-access-control/built-in-roles.md) *współautor* `b24988ac-6180-42a0-ab88-20f7382dd24c` .|
   | Nazwa RG | Nazwa grupy zasobów utworzonej wcześniej w dzierżawie usługi Azure AD. Na przykład *usługa Azure-AD-B2C-monitor*. |

   Poniższy przykład ilustruje tablicę autoryzacji z jedną grupą zabezpieczeń.

   ```json
   [
       {
           "principalId": "<Replace with group's OBJECT ID>",
           "principalIdDisplayName": "Azure AD B2C tenant administrators",
           "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
       }
   ]
   ```

Po wdrożeniu szablonu może upłynąć kilka minut (zazwyczaj nie więcej niż pięć), aby można było wykonać projekcję zasobu. Możesz zweryfikować wdrożenie w dzierżawie usługi Azure AD i uzyskać szczegółowe informacje o projekcji zasobów. Aby uzyskać więcej informacji, zobacz [Wyświetlanie i zarządzanie dostawcami usług](../lighthouse/how-to/view-manage-service-providers.md).

## <a name="4-select-your-subscription"></a>4. Wybierz subskrypcję

Po wdrożeniu szablonu i poczekaniu kilku minut na ukończenie projekcji zasobów wykonaj następujące kroki, aby skojarzyć subskrypcję z katalogiem Azure AD B2C.

1. Wyloguj się z Azure Portal, jeśli użytkownik jest obecnie zalogowany (umożliwia to odświeżenie poświadczeń sesji w następnym kroku).
2. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu **Azure AD B2C** konta administracyjnego. To konto musi być członkiem grupy zabezpieczeń określonej w kroku [Zarządzanie zasobami delegowanymi](#3-delegate-resource-management) .
3. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** .
4. Wybierz katalog usługi Azure AD zawierający subskrypcję platformy Azure oraz utworzoną grupę zasobów *Azure-AD-B2C-monitor* .

    ![Przełącz katalog](./media/azure-monitor/azure-monitor-portal-03-select-subscription.png)

1. Sprawdź, czy wybrano prawidłowy katalog i subskrypcję. W tym przykładzie wybrano wszystkie katalogi i wszystkie subskrypcje.

    ![Wszystkie katalogi wybrane w & filtr subskrypcji usługi katalogowej](./media/azure-monitor/azure-monitor-portal-04-subscriptions-selected.png)

## <a name="5-configure-diagnostic-settings"></a>5. Konfigurowanie ustawień diagnostycznych

Ustawienia diagnostyczne definiują, gdzie należy wysyłać dzienniki i metryki dla zasobu. Możliwe miejsca docelowe to:

- [Konto usługi Azure Storage](../azure-monitor/platform/resource-logs.md#send-to-azure-storage)
- Rozwiązania [centrów zdarzeń](../azure-monitor/platform/resource-logs.md#send-to-azure-event-hubs)
- [Log Analytics obszar roboczy](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace)

W tym przykładzie użyjemy obszaru roboczego Log Analytics, aby utworzyć pulpit nawigacyjny.

### <a name="51-create-diagnostic-settings"></a>5,1 Tworzenie ustawień diagnostycznych

Możesz przystąpić do [tworzenia ustawień diagnostycznych](../active-directory/reports-monitoring/overview-monitoring.md) w Azure Portal.

Aby skonfigurować ustawienia monitorowania dla Azure AD B2C dzienników aktywności:

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu Azure AD B2C konta administracyjnego. To konto musi być członkiem grupy zabezpieczeń określonej w kroku [Wybierz grupę zabezpieczeń](#32-select-a-security-group) .
1. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. Wybierz **Azure Active Directory**
1. W obszarze **Monitorowanie** wybierz pozycję **Ustawienia diagnostyczne**.
1. Jeśli istnieją jakieś ustawienia dla zasobu, zostanie wyświetlona lista ustawień, które zostały już skonfigurowane. Wybierz opcję **Dodaj ustawienie diagnostyczne** , aby dodać nowe ustawienie, lub wybierz pozycję **Edytuj** , aby edytować istniejące ustawienie. Każde ustawienie nie może mieć więcej niż jednego z typów docelowych.

    ![Okienko ustawień diagnostycznych w Azure Portal](./media/azure-monitor/azure-monitor-portal-05-diagnostic-settings-pane-enabled.png)

1. Nadaj ustawieniu nazwę, jeśli jeszcze jej nie ma.
1. Zaznacz pole wyboru dla każdego miejsca docelowego, aby wysłać dzienniki. Wybierz pozycję **Konfiguruj** , aby określić swoje ustawienia zgodnie z **opisem w poniższej tabeli**.
1. Wybierz pozycję **Wyślij do log Analytics**, a następnie wybierz utworzoną wcześniej **nazwę obszaru roboczego** ( `AzureAdB2C` ).
1. Wybierz pozycję **AuditLogs** i **SignInLogs**.
1. Wybierz pozycję **Zapisz**.

> [!NOTE]
> Po wydaniu zdarzenia [w obszarze roboczym log Analytics](../azure-monitor/platform/data-ingestion-time.md)może upłynąć do 15 minut. Dowiedz się więcej o [Active Directory opóźnień raportowania](../active-directory/reports-monitoring/reference-reports-latencies.md), które mogą mieć wpływ na nieaktualność danych i odgrywają ważną rolę w raportowaniu.

Jeśli zostanie wyświetlony komunikat o błędzie "Aby skonfigurować ustawienia diagnostyczne do korzystania z Azure Monitor dla katalogu Azure AD B2C, należy skonfigurować zarządzanie zasobami delegowanymi". Upewnij się, że logujesz się przy użyciu użytkownika, który jest członkiem [grupy zabezpieczeń](#32-select-a-security-group) , i [Wybierz swoją subskrypcję](#4-select-your-subscription).

## <a name="6-visualize-your-data"></a>6. Wizualizuj dane

Teraz można skonfigurować obszar roboczy Log Analytics do wizualizacji danych i konfigurowania alertów. Te konfiguracje można wprowadzać zarówno w dzierżawie usługi Azure AD, jak i w dzierżawie Azure AD B2C.

### <a name="61-create-a-query"></a>6,1 Tworzenie zapytania

Zapytania dzienników ułatwiają całkowite wykorzystanie wartości danych zebranych w dziennikach Azure Monitor. Zaawansowany język zapytań umożliwia sprzęganie danych z wielu tabel, agregowanie dużych zestawów danych i wykonywanie złożonych operacji przy minimalnym kodzie. Niemal każde pytanie może być odpowiedzią i analizą wykonywaną, o ile dane pomocnicze zostały zebrane, i zrozumieć, jak utworzyć odpowiednie zapytanie. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z zapytaniami dzienników w Azure monitor](../azure-monitor/log-query/get-started-queries.md).

1. W **obszarze roboczym log Analytics** wybierz pozycję **dzienniki**
1. W edytorze zapytań Wklej następujące zapytanie dotyczące [języka zapytań Kusto](/azure/data-explorer/kusto/query/) . To zapytanie pokazuje użycie zasad według operacji w ciągu ostatnich x dni. Domyślny czas trwania wynosi 90 dni (90d). Zwróć uwagę, że zapytanie jest skoncentrowane tylko na operacji, w której token/kod jest wystawiony przez zasady.

    ```kusto
    AuditLogs
    | where TimeGenerated  > ago(90d)
    | where OperationName contains "issue"
    | extend  UserId=extractjson("$.[0].id",tostring(TargetResources))
    | extend Policy=extractjson("$.[1].value",tostring(AdditionalDetails))
    | summarize SignInCount = count() by Policy, OperationName
    | order by SignInCount desc  nulls last
    ```

1. Wybierz pozycję **Uruchom**. Wyniki zapytania są wyświetlane u dołu ekranu.
1. Aby zapisać zapytanie do późniejszego użycia, wybierz pozycję **Zapisz**.

   ![Log Analytics Edytor dziennika](./media/azure-monitor/query-policy-usage.png)

1. Podaj następujące informacje:

    - **Nazwa** — wprowadź nazwę zapytania.
    - **Zapisz jako** -SELECT `query` .
    - Wybierz **kategorię** `Log` .

1. Wybierz pozycję **Zapisz**.

Możesz również zmienić zapytanie, aby wizualizować dane przy użyciu operatora [renderowania](/azure/data-explorer/kusto/query/renderoperator?pivots=azuremonitor) .

```kusto
AuditLogs
| where TimeGenerated  > ago(90d)
| where OperationName contains "issue"
| extend  UserId=extractjson("$.[0].id",tostring(TargetResources))
| extend Policy=extractjson("$.[1].value",tostring(AdditionalDetails))
| summarize SignInCount = count() by Policy
| order by SignInCount desc  nulls last
| render  piechart
```

![Log Analytics kołowy edytora dziennika](./media/azure-monitor/query-policy-usage-pie.png)

Aby uzyskać więcej przykładów, zobacz [repozytorium Azure AD B2C Siem](https://aka.ms/b2csiem)w witrynie GitHub.

### <a name="62-create-a-workbook"></a>6,2 Tworzenie skoroszytu

Skoroszyty udostępniają elastyczną kanwę do analizy danych i tworzenia zaawansowanych raportów wizualnych w witrynie Azure Portal. Umożliwiają one naciskanie na wiele źródeł danych z platformy Azure i łączenie ich w ujednolicone interaktywne środowiska. Aby uzyskać więcej informacji, zobacz [Azure monitor skoroszytów](../azure-monitor/platform/workbooks-overview.md).

Postępuj zgodnie z poniższymi instrukcjami, aby utworzyć nowy skoroszyt przy użyciu szablonu galerii JSON. Ten skoroszyt zawiera pulpit nawigacyjny usługi **User Insights** i **uwierzytelniania** dla dzierżawy Azure AD B2C.

1. W **obszarze roboczym log Analytics** wybierz pozycję **skoroszyty**.
1. Na pasku narzędzi wybierz pozycję **+ Nowa** opcja, aby utworzyć nowy skoroszyt.
1. Na stronie **nowy skoroszyt** wybierz **Edytor zaawansowany** przy użyciu **</>** opcji na pasku narzędzi.

     ![Szablon galerii](./media/azure-monitor/wrkb-adv-editor.png)

1. Wybierz **szablon galerii**.
1. Zastąp kod JSON w **szablonie galerii**  zawartością ze [skoroszytu Azure AD B2C Basic](https://raw.githubusercontent.com/azure-ad-b2c/siem/master/workbooks/dashboard.json):
1. Zastosuj szablon przy użyciu przycisku **Zastosuj** .
1. Wybierz przycisk **gotowe do edycji** na pasku narzędzi, aby zakończyć edytowanie skoroszytu.
1. Na koniec Zapisz skoroszyt za pomocą przycisku **Zapisz** na pasku narzędzi.
1. Podaj **tytuł**, taki jak *Azure AD B2C pulpitu nawigacyjnego*.
1. Wybierz pozycję **Zapisz**.

    ![Zapisz skoroszyt](./media/azure-monitor/wrkb-title.png)

Skoroszyt będzie wyświetlał raporty w formie pulpitu nawigacyjnego.

![Pierwszy pulpit nawigacyjny skoroszytu](./media/azure-monitor/wkrb-dashboard-1.png)

![Drugi pulpit nawigacyjny skoroszytu](./media/azure-monitor/wrkb-dashboard-2.png)

![Skoroszyt trzeciego pulpitu nawigacyjnego](./media/azure-monitor/wrkb-dashboard-3.png)


## <a name="create-alerts"></a>Tworzenie alertów

Alerty są tworzone na podstawie reguł alertów na platformie Azure Monitor i mogą automatycznie uruchamiać zapisane zapytania lub niestandardowe wyszukiwania dziennika w regularnych odstępach czasu. Możesz utworzyć alerty w oparciu o konkretne metryki wydajności lub w momencie, w którym zostają utworzone określone zdarzenia, w przypadku braku zdarzenia, a także w sytuacji, w której wiele zdarzeń zostaje utworzonych w danym przedziale czasu. Na przykład można użyć alertów, aby powiadomić Cię, gdy średnia liczba logowań przekroczy określony próg. Aby uzyskać więcej informacji, zobacz [tworzenie alertów](../azure-monitor/learn/tutorial-response.md).


Skorzystaj z poniższych instrukcji, aby utworzyć nowy Alert platformy Azure, który wyśle [wiadomość e-mail z powiadomieniem](../azure-monitor/platform/action-groups.md#configure-notifications) za każdym razem, gdy w **łączu** do poprzedniego okresu zostanie wyświetlonych 25% żądań. Alert będzie uruchamiany co 5 minut i poszukiwany w ciągu ostatnich 24 godzin. Alerty są tworzone przy użyciu języka zapytań Kusto.


1. W **obszarze roboczym log Analytics** wybierz pozycję **dzienniki**. 
1. Utwórz nowe **zapytanie Kusto** przy użyciu poniższego zapytania.

    ```kusto
    let start = ago(24h);
    let end = now();
    let threshold = -25; //25% decrease in total requests.
    AuditLogs
    | serialize TimeGenerated, CorrelationId, Result
    | make-series TotalRequests=dcount(CorrelationId) on TimeGenerated in range(start, end, 1h)
    | mvexpand TimeGenerated, TotalRequests
    | where TotalRequests > 0
    | serialize TotalRequests, TimeGenerated, TimeGeneratedFormatted=format_datetime(todatetime(TimeGenerated), 'yyyy-M-dd [hh:mm:ss tt]')
    | project   TimeGeneratedFormatted, TotalRequests, PercentageChange= ((toreal(TotalRequests) - toreal(prev(TotalRequests,1)))/toreal(prev(TotalRequests,1)))*100
    | order by TimeGeneratedFormatted
    | where PercentageChange <= threshold   //Trigger's alert rule if matched.
    ```

1. Wybierz pozycję **Uruchom**, aby przetestować zapytanie. Wyniki powinny zostać wyświetlone, jeśli w ciągu ostatnich 24 godzin wystąpi spadek o 25% lub więcej.
1. Aby utworzyć regułę alertu opartą na powyższym zapytaniu, użyj opcji **+ Nowa reguła alertu** dostępnej na pasku narzędzi.
1. Na stronie **Tworzenie reguły alertu** wybierz pozycję **nazwa warunku** . 
1. Na stronie **Konfiguruj logikę sygnału** ustaw następujące wartości, a następnie użyj przycisku **gotowe** , aby zapisać zmiany.
    * Logika alertu: Ustaw **liczbę wyników** **większą niż** **0**.
    * Obliczanie na podstawie: Wybierz **1440** dla okresu (w minutach) i **5** , aby uzyskać częstotliwość (w minutach) 

    ![Utwórz warunek reguły alertu](./media/azure-monitor/alert-create-rule-condition.png)

Po utworzeniu alertu przejdź do **obszaru roboczego log Analytics** i wybierz pozycję **alerty**. Ta strona zawiera wszystkie alerty, które zostały wyzwolone w opcji czas trwania ustawiony przez **zakres czasu** .  

### <a name="configure-action-groups"></a>Konfiguruj grupy akcji

Alerty Azure Monitor i Service Health umożliwiają Powiadamianie użytkowników o wyzwoleniu alertu. Możesz uwzględnić wysyłanie połączenia głosowego, wiadomości e-mail i poczty elektronicznej. lub wyzwalając różne typy zautomatyzowanych akcji. Postępuj zgodnie ze wskazówkami [Tworzenie grup akcji i zarządzanie nimi w Azure Portal](../azure-monitor/platform/action-groups.md)

Oto przykład wiadomości e-mail z powiadomieniem o alercie. 

   ![Powiadomienie e-mail](./media/azure-monitor/alert-email-notification.png)

## <a name="multiple-tenants"></a>Wielu dzierżawców

Aby dołączyć wiele Azure AD B2C dzienników dzierżawy do tego samego obszaru roboczego Log Analytics (lub konta usługi Azure Storage lub centrum zdarzeń), należy wykonać oddzielne wdrożenia z różnymi wartościami **nazw oferty msp** . Upewnij się, że obszar roboczy Log Analytics znajduje się w tej samej grupie zasobów co ta, która została skonfigurowana w obszarze [Utwórz lub wybierz grupę zasobów](#1-create-or-choose-resource-group).

Podczas pracy z wieloma obszarami roboczymi Log Analytics Użyj [zapytania między obszarami roboczymi](../azure-monitor/log-query/cross-workspace-query.md) , aby utworzyć zapytania, które działają w wielu obszarach roboczych. Na przykład następujące zapytanie wykonuje sprzężenie dwóch dzienników inspekcji z różnych dzierżawców na podstawie tej samej kategorii (na przykład uwierzytelniania):

```kusto
workspace("AD-B2C-TENANT1").AuditLogs
| join  workspace("AD-B2C-TENANT2").AuditLogs
  on $left.Category== $right.Category
```

## <a name="change-the-data-retention-period"></a>Change the data retention period (Zmienianie okresu przechowywania danych)

Dzienniki Azure Monitor są przeznaczone do skalowania i obsługi zbierania, indeksowania i przechowywania dużych ilości danych dziennie z dowolnego źródła w przedsiębiorstwie lub wdrożonego na platformie Azure. Domyślnie dzienniki są przechowywane przez 30 dni, ale czas przechowywania można zwiększyć do maksymalnie dwóch lat. Dowiedz się [, jak zarządzać użyciem i kosztami za pomocą dzienników Azure monitor](../azure-monitor/platform/manage-cost-storage.md). Po wybraniu warstwy cenowej można [zmienić okres przechowywania danych](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period).

## <a name="next-steps"></a>Następne kroki

* Więcej przykładów znajdziesz w Galerii Azure AD B2C [Siem](https://aka.ms/b2csiem). 

* Aby uzyskać więcej informacji na temat dodawania i konfigurowania ustawień diagnostycznych w Azure Monitor, zobacz [Samouczek: zbieranie i analizowanie dzienników zasobów z zasobów platformy Azure](../azure-monitor/insights/monitor-azure-resource.md).

* Aby uzyskać informacje o przesyłaniu strumieniowym dzienników usługi Azure AD do centrum zdarzeń, zobacz [Samouczek: przesyłanie strumieniowe dzienników Azure Active Directory do centrum zdarzeń platformy Azure](../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).
