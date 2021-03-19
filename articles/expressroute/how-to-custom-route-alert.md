---
title: 'ExpressRoute: Konfigurowanie alertów niestandardowych dla anonsowanych tras'
description: W tym artykule pokazano, jak używać Azure Automation i Logic Apps do monitorowania liczby tras anonsowanych z bramy ExpressRoute do sieci lokalnych w celu uniknięcia osiągnięcia limitu 1000 tras.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 05/29/2020
ms.author: duau
ms.openlocfilehash: 2291d1fa7f890296c59661060f5a823d8eb194ba
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104654394"
---
# <a name="configure-custom-alerts-to-monitor-advertised-routes"></a>Konfigurowanie alertów niestandardowych do monitorowania anonsowanych tras

Ten artykuł ułatwia używanie Azure Automation i Logic Apps do ciągłego monitorowania liczby tras anonsowanych z bramy ExpressRoute do sieci lokalnych. Monitorowanie może pomóc zapobiec osiągnięciu limitu 1000 tras] (ExpressRoute-często zadawanych pytań. MD # jak wiele prefiksów — może być anonsowane-z-a-VNET-on-on-on-on-on-on-ExpressRoute-Private-peer-Komunikacja równorzędna).

**Azure Automation** umożliwia Automatyzowanie wykonywania niestandardowego skryptu programu PowerShell przechowywanego w *elemencie Runbook*. W przypadku korzystania z konfiguracji w tym artykule element Runbook zawiera skrypt programu PowerShell, który wysyła zapytanie do co najmniej jednej bramy ExpressRoute. Zbiera zestaw danych zawierający grupę zasobów, nazwę bramy ExpressRoute oraz liczbę prefiksów sieci anonsowanych lokalnie.

**Azure Logic Apps** planuje niestandardowy przepływ pracy, który wywoła Azure Automation element Runbook. Wykonywanie elementu Runbook jest wykonywane przy użyciu zadania. Po uruchomieniu zbierania danych przepływ pracy Azure Logic Apps klasyfikuje dane i, na podstawie kryteriów dopasowania dla liczby prefiksów sieci powyżej lub poniżej wstępnie zdefiniowanego progu, wysyła informacje na docelowy adres e-mail.

### <a name="workflow"></a><a name="workflow"></a>Przepływ pracy

Konfigurowanie alertu niestandardowego odbywa się na podstawie trzech głównych kroków:

1. Utwórz konto usługi Automation z kontem i uprawnieniami "Uruchom jako".

2. Tworzenie i Konfigurowanie elementów Runbook.

3. Utwórz aplikację logiki, która będzie wyzwalać konto usługi Automation i wysyłać wiadomości e-mail o alertach, jeśli liczba jest większa niż wartość progowa (na przykład 160).

## <a name="before-you-begin"></a><a name="before"></a>Przed rozpoczęciem

Przed rozpoczęciem konfiguracji sprawdź, czy są spełnione następujące kryteria:

* W danym wdrożeniu znajduje się co najmniej jedna brama ExpressRoute.

* W Azure Automation można uzyskać podstawową wiedzę na temat [kont Uruchom jako](../automation/manage-runas-account.md) .

* Znasz [Azure Logic Apps](../logic-apps/logic-apps-overview.md).

* Wiesz już, jak korzystać z Azure PowerShell. Do zebrania prefiksów sieci w bramie ExpressRoute jest wymagane Azure PowerShell. Aby uzyskać więcej informacji na temat ogólnie Azure PowerShell, zapoznaj się z [dokumentacją Azure PowerShell](/powershell/azure/).

### <a name="notes-and-limitations"></a><a name="limitations"></a>Uwagi i ograniczenia

* Alert niestandardowy omówiony w tym artykule jest dodatkiem w celu osiągnięcia lepszej operacji i kontroli. Nie zastępuje alertów natywnych w ExpressRoute.
* Zbieranie danych dla bram ExpressRoute jest uruchamiane w tle. Środowisko uruchomieniowe może być dłuższe niż oczekiwano. Aby uniknąć kolejkowania zadań, cykl przepływu pracy musi być poprawnie skonfigurowany.
* Wdrożenia według skryptów lub szablonów ARM mogą być szybsze od niestandardowego wyzwalacza alarmu. Może to spowodować zwiększenie liczby prefiksów sieci w bramie ExpressRoute powyżej limitu 1000 tras.

## <a name="create-and-configure-accounts"></a><a name="accounts"></a>Tworzenie i Konfigurowanie kont

Po utworzeniu konta usługi Automation w Azure Portal automatycznie tworzone jest konto [Uruchom jako](../automation/automation-security-overview.md#run-as-accounts) . To konto wykonuje następujące akcje:

* Tworzy aplikację Azure Active Directory (Azure AD) z certyfikatem z podpisem własnym. Samo konto Uruchom jako ma certyfikat, który musi zostać odnowiony domyślnie co roku.

* Tworzy konto nazwy głównej usługi dla aplikacji w usłudze Azure AD.

* Przypisuje sobie rolę współautor (RBAC) w używanej subskrypcji platformy Azure. Ta rola zarządza zasobami Azure Resource Manager przy użyciu elementów Runbook.

Aby można było utworzyć konto usługi Automation, potrzebne są uprawnienia i uprawnienia. Aby uzyskać więcej informacji, zobacz [uprawnienia wymagane do utworzenia konta usługi Automation](../automation/automation-create-standalone-account.md#permissions-required-to-create-an-automation-account).

### <a name="1-create-an-automation-account"></a><a name="about"></a>1. Utwórz konto usługi Automation

Utwórz konto usługi Automation z uprawnieniami Uruchom jako. Aby uzyskać instrukcje, zobacz [Tworzenie konta Azure Automation](../automation/automation-quickstart-create-account.md).

:::image type="content" source="./media/custom-route-alert-portal/create-account.png" alt-text="Dodawanie konta usługi Automation" lightbox="./media/custom-route-alert-portal/create-account-expand.png":::

### <a name="2-assign-the-run-as-account-a-role"></a><a name="about"></a>2. Przypisz rolę "Uruchom jako"

Domyślnie rola **współautor** jest przypisywana do jednostki usługi, która jest używana przez konto **Uruchom jako** . Można zachować domyślną rolę przypisaną do jednostki usługi lub można ograniczyć uprawnienia, przypisując [wbudowaną rolę](../role-based-access-control/built-in-roles.md) (na przykład czytelnik) lub [rolę niestandardową](../active-directory/roles/custom-create.md).

 Wykonaj następujące kroki, aby określić rolę przypisaną do jednostki usługi, która jest używana przez konto Uruchom jako:

1. Przejdź do konta usługi Automation. Przejdź do **ustawień konta**, a następnie wybierz pozycję **konta Uruchom jako**.

2. Wybierz **role** , aby wyświetlić używane definicje ról.

   :::image type="content" source="./media/custom-route-alert-portal/run-as-account-permissions.png" alt-text="Przypisywanie roli":::

## <a name="create-and-configure-runbooks"></a><a name="runbooks"></a>Tworzenie i Konfigurowanie elementów Runbook

### <a name="1-install-modules"></a><a name="install-modules"></a>1. Zainstaluj moduły

Aby można było uruchamiać polecenia cmdlet programu PowerShell w programie Azure Automation Runbook, należy zainstalować kilka dodatkowych Azure PowerShell AZ modules. Wykonaj następujące kroki, aby zainstalować moduły:

1. Otwórz konto Azure Automation i przejdź do **modułów**.

   :::image type="content" source="./media/custom-route-alert-portal/navigate-modules.png" alt-text="Przejdź do modułów":::

2. Przeszukaj galerię i zaimportuj następujące moduły: **AZ. Accounts**, **AZ. Network**, **AZ. Automation** i **AZ. profile**.

   :::image type="content" source="./media/custom-route-alert-portal/import-modules.png" alt-text="Wyszukaj i zaimportuj moduły" lightbox="./media/custom-route-alert-portal/import-modules-expand.png":::
  
### <a name="2-create-a-runbook"></a><a name="create"></a>2. Tworzenie elementu Runbook

1. Aby utworzyć element Runbook programu PowerShell, przejdź do konta usługi Automation. W obszarze **Automatyzacja procesów** wybierz kafelek **elementy Runbook** , a następnie wybierz pozycję **Utwórz element Runbook**.

   :::image type="content" source="./media/custom-route-alert-portal/create-runbook.png" alt-text="Utwórz element Runbook.":::

2. Wybierz pozycję **Utwórz** , aby utworzyć element Runbook.

   :::image type="content" source="./media/custom-route-alert-portal/create-runbook-2.png" alt-text="Wybierz pozycję Utwórz.":::

3. Wybierz nowo utworzony element Runbook, a następnie wybierz pozycję **Edytuj**.

   :::image type="content" source="./media/custom-route-alert-portal/edit-runbook.png" alt-text="Edytowanie elementu Runbook":::

4. W obszarze **Edytuj** wklej skrypt programu PowerShell. [Przykładowy skrypt](#script) można zmodyfikować i użyć do monitorowania bram ExpressRoute w co najmniej jednej grupie zasobów.

   W przykładowym skrypcie Zwróć uwagę na następujące ustawienia:

    * Tablica **$rgList** zawiera listę grup zasobów z bramami ExpressRoute. Można dostosować bramy ExpressRoute oparte na listach.
    * Zmienna **$thresholdNumRoutes** definiuje próg liczby prefiksów sieci anonsowanych z bramy ExpressRoute do sieci lokalnych.

#### <a name="example-script"></a><a name="script"></a>Przykładowy skrypt

```powershell
  ################# Input parameters #################
# Resource Group Name where the ExR GWs resides in
$rgList= @('ASH-Cust10-02','ASH-Cust30')  
$thresholdNumRoutes = 160
###################################################

# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process | Out-Null

Try {

   $conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
   while(!($connectionResult) -And ($logonAttempt -le 5))
   {
        $LogonAttempt++
        # Logging in to Azure...
        $connectionResult =  Connect-AzAccount `
                               -ServicePrincipal `
                               -ApplicationId $conn.ApplicationId `
                               -Tenant $conn.TenantId `
                               -CertificateThumbprint $conn.CertificateThumbprint `
                               -Subscription $conn.SubscriptionId `
                               -Environment AzureCloud 
                               
        Start-Sleep -Seconds 10
    }
} Catch {
    if (!$conn)
    {
        $ErrorMessage = "Service principal not found."
        throw $ErrorMessage
    } 
    else
    {
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

# Get the name of the Azure subscription
$subscriptionName=(Get-AzSubscription -SubscriptionId $conn.SubscriptionId).Name

#write-Output "<br>$(Get-Date) - selection of the Azure subscription: $subscriptionName" 
Select-AzSubscription -SubscriptionId $conn.SubscriptionId | Out-Null


$GtwList = @()
$results= @()

foreach ($rgName in $rgList)
{
## Collect all the ExpressRoute gateways in a Resource Group
$GtwList=Get-AzVirtualNetworkGateway -ResourceGroupName $rgName 

## For each ExpressRoute gateway, get the IP addresses of the BGP peers and collect the number of routes advertised 
foreach ($gw in $GtwList) {
  
  $peers = Get-AzVirtualNetworkGatewayBGPPeerStatus -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName


 if ($peers[0].State -eq 'Connected') {
   $routes1=$null
   $routes1 = Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName -Peer $peers[0].Neighbor
 }
  if ($peers[1].State -eq 'Connected') {
  
   $routes2=$null
   $routes2 = Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName -Peer $peers[1].Neighbor
 }
 
  $sampleTime=(Get-Date).ToString("dd-MM-yyyy HH:mm:ss")
  if ($routes1.Count -eq $routes2.Count)
  {
     
     if ($routes1.Count -lt $thresholdNumRoutes){
       $status='OK'
       $alertMsg='number of routes below threshold'
     } 
     else {
       $status='ALERT'
       $alertMsg='number of routes above threshold'
     }
  } 
  else
  {
     $status='WARNING'
     $alertMsg='check ER Gateway'
  }
  
  $obj = [psCustomObject]@{
            resourceGroup =$rgName
            nameGtw  = $gw.Name
            peer1 = $peers[0].Neighbor
            peer2 = $peers[1].Neighbor
            numRoutesPeer1=  $routes1.Count
            numRoutesPeer2=  $routes2.Count
            time=$sampleTime
            status=$status
            alertMessage = $alertMsg
        }
  $results += $obj
} ### end foreach gateways in each resource group
} ### end foreach resource group
$jsonResults= ConvertTo-Json $results -Depth 100
Write-Output  $jsonResults
 
 ```

### <a name="3-save-and-publish-the-runbook"></a><a name="publish"></a>3. Zapisz i Opublikuj element Runbook

1. Wybierz pozycję **Zapisz** , aby zapisać kopię roboczą elementu Runbook.
2. Wybierz pozycję **Publikuj** , aby opublikować element Runbook jako oficjalną wersję elementu Runbook na koncie usługi Automation.

   :::image type="content" source="./media/custom-route-alert-portal/save-publish-runbook.png" alt-text="Zapisz i Opublikuj element Runbook.":::

Po uruchomieniu skryptu programu PowerShell jest pobierana lista wartości:
 
* Grupa zasobów

* Nazwa bramy ExpressRoute

* Adres IP pierwszego elementu równorzędnego protokołu BGP (Peer1)

* Adres IP drugiego elementu równorzędnego protokołu BGP (peer2)

* Liczba prefiksów sieci anonsowanych z bramy ExpressRoute do pierwszego elementu równorzędnego BGP (Peer1)

* Liczba prefiksów sieci anonsowanych z bramy ExpressRoute do drugiego elementu równorzędnego protokołu BGP (peer2)

* Znacznik czasu

* Stan, sklasyfikowany jako:

  * "OK", jeśli liczba tras jest mniejsza niż wartość progowa
  * "ALERT", jeśli liczba tras w przypadku powyżej wartości progowej
  * "Ostrzeżenie", jeśli liczba prefiksów sieci anonsowanych do dwóch elementów równorzędnych protokołu BGP jest inna

* Komunikat alertu dla pełnego opisu stanu (OK, ALERT, ostrzeżenie)

Skrypt programu PowerShell konwertuje zebrane informacje na dane wyjściowe JSON. Element Runbook używa polecenia cmdlet [Write-Output](/powershell/module/Microsoft.PowerShell.Utility/Write-Output)  programu PowerShell jako strumienia wyjściowego do przekazywania informacji do klienta.

### <a name="4-validate-the-runbook"></a><a name="validate"></a>4. Sprawdź poprawność elementu Runbook

Po utworzeniu elementu Runbook należy sprawdzić jego poprawność. Wybierz pozycję **Uruchom** i sprawdź dane wyjściowe oraz błędy dla różnych strumieni zadań.

:::image type="content" source="./media/custom-route-alert-portal/validate-runbook.png" alt-text="Weryfikowanie elementu Runbook" lightbox="./media/custom-route-alert-portal/validate-runbook-expand.png":::

## <a name="create-and-configure-a-logic-app"></a><a name="logic"></a>Tworzenie i Konfigurowanie aplikacji logiki

Azure Logic Apps jest koordynatorem wszystkich procesów kolekcji i akcji. W poniższych sekcjach opisano tworzenie przepływu pracy przy użyciu aplikacji logiki.

### <a name="workflow"></a>Przepływ pracy

W tym przepływie pracy utworzysz aplikację logiki, która regularnie monitoruje bramy ExpressRoute. Jeśli istnieją nowe elementy, aplikacja logiki wysyła wiadomość e-mail dotyczącą każdego elementu. Po ukończeniu aplikacja logiki będzie ogólnie wyglądać jak ten przepływ pracy:

:::image type="content" source="./media/custom-route-alert-portal/logic-apps-workflow.png" alt-text="Przepływ pracy Logic Apps":::

### <a name="1-create-a-logic-app"></a>1. Tworzenie aplikacji logiki

W **Projektancie aplikacji logiki** Utwórz aplikację logiki przy użyciu szablonu **pustej aplikacji logiki** . Aby uzyskać instrukcje, zobacz [tworzenie Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md#create-your-logic-app).

:::image type="content" source="./media/custom-route-alert-portal/blank-template.png" alt-text="Pusty szablon":::

### <a name="2-add-a-trigger"></a>2. Dodaj wyzwalacz

Każda aplikacja logiki jest uruchamiana przez wyzwalacz. Wyzwalacz wyzwalany po wystąpieniu określonego zdarzenia lub spełnieniu określonego warunku. Za każdym razem, gdy wyzwala wyzwalacz, aparat Azure Logic Apps tworzy wystąpienie aplikacji logiki, które uruchamia i uruchamia przepływ pracy.

Aby regularnie uruchamiać aplikację logiki opartą na wstępnie zdefiniowanym harmonogramie, Dodaj wbudowany **cykl: harmonogram** do przepływu pracy. W polu wyszukiwania wpisz polecenie **Schedule**. Wybierz pozycję **Wyzwalacze**. Z listy Wyzwalacze wybierz pozycję **harmonogram cyklu**.

:::image type="content" source="./media/custom-route-alert-portal/schedule.png" alt-text="Cykl: harmonogram":::

W wyzwalaczu harmonogramu cyklu można ustawić strefę czasową i cykl dla powtarzających się tego przepływu pracy. Interwał i częstotliwość określają harmonogram wyzwalacza aplikacji logiki. Aby określić rozsądną częstotliwość powtarzania cyklu, należy wziąć pod uwagę następujące czynniki:

* Wykonanie skryptu programu PowerShell w elemencie Runbook usługi Automation trwa czas. Środowisko uruchomieniowe zależy od liczby bram ExpressRoute do monitorowania. Zbyt krótka częstotliwość powtarzania spowoduje wystąpienie usługi kolejkowania zadań.

* Skrypt programu PowerShell jest uruchamiany jako zadanie w tle. Nie zaczyna się od razu; działa po zmiennym opóźnieniu.

* Zbyt krótka częstotliwość powtarzania spowoduje wygenerowanie niepotrzebnych obciążeń dla bram usługi Azure ExpressRoute.

Na końcu konfiguracji przepływu pracy można sprawdzić spójność częstotliwości cyklu, uruchamiając przepływ pracy kilka razy, a następnie sprawdzając wynik w **historii uruchamiania**.

:::image type="content" source="./media/custom-route-alert-portal/recurrence.png" alt-text="Zrzut ekranu pokazuje interwał cyklu i wartości częstotliwości." lightbox="./media/custom-route-alert-portal/recurrence-expand.png":::

### <a name="3-create-a-job"></a><a name="job"></a>3. Utwórz zadanie

Aplikacja logiki uzyskuje dostęp do innych aplikacji, usług i platform, chociaż łączniki. Następnym krokiem w tym przepływie pracy jest wybranie łącznika w celu uzyskania dostępu do konta Azure Automation zdefiniowanego wcześniej.

1. W **projektancie Logic Apps** **poniżej wybierz pozycję** **nowy krok**. W obszarze **Wybierz akcję** i pole wyszukiwania wybierz pozycję **wszystkie**.
2. W polu wyszukiwania wpisz **Azure Automation** i Wyszukaj. Wybierz pozycję **Utwórz zadanie**. **Utwórz zadanie** zostanie użyte do uruchomienia elementu Runbook usługi Automation, który został utworzony wcześniej.

   :::image type="content" source="./media/custom-route-alert-portal/create-job.png" alt-text="Tworzenie zadania":::

3. Zaloguj się przy użyciu nazwy głównej usługi. Możesz użyć istniejącej jednostki usługi lub utworzyć nową. Aby utworzyć nową nazwę główną usługi, zobacz [jak używać portalu do tworzenia jednostki usługi Azure AD, która może uzyskiwać dostęp do zasobów](../active-directory/develop/howto-create-service-principal-portal.md). Wybierz pozycję **Połącz z** jednostką usługi.

   :::image type="content" source="./media/custom-route-alert-portal/sign-in.png" alt-text="Zrzut ekranu pokazujący sekcję &quot;cykl&quot; z wyróżnioną akcją &quot;Połącz z jednostką usługi&quot;.":::

4. Wpisz **nazwę połączenia**, Dodaj **Identyfikator klienta** (Identyfikator aplikacji), **klucz tajny klienta** i **Identyfikator dzierżawy**. Następnie wybierz przycisk **Utwórz**.

   :::image type="content" source="./media/custom-route-alert-portal/connect-service-principal.png" alt-text="Nawiązywanie połączenia z jednostką usługi":::

5. Na stronie **Tworzenie zadania** jednostka usługi powinna mieć rolę "czytelnik" w **grupie zasobów** hostującym konto usługi Automation i "operator zadania automatyzacji" na **koncie usługi Automation**. Ponadto sprawdź, czy **Nazwa elementu Runbook** została dodana jako nowy parametr.

   :::image type="content" source="./media/custom-route-alert-portal/roles.png" alt-text="Zrzut ekranu przedstawia tworzenie wartości zadania w cyklu, gdzie można zweryfikować nazwę elementu Runbook." lightbox="./media/custom-route-alert-portal/roles-expand.png":::

### <a name="4-get-the-job-output"></a><a name="output"></a>4. Pobierz dane wyjściowe zadania

1. Wybierz pozycję **Nowy krok**. Wyszukaj ciąg "Azure Automation". Z listy **Akcje** wybierz pozycję **Pobierz dane wyjściowe zadania**.

   :::image type="content" source="./media/custom-route-alert-portal/get-output.png" alt-text="Pobieranie danych wyjściowych zadania":::

2. Na stronie **Pobierz dane wyjściowe zadania** Określ wymagane informacje, aby uzyskać dostęp do konta usługi Automation. Wybierz **subskrypcję, grupę zasobów** i **konto usługi Automation** , którego chcesz użyć. Kliknij wewnątrz pola **Identyfikator zadania** . Gdy zostanie wyświetlona lista **zawartości dynamicznej** , wybierz pozycję **Identyfikator zadania**.

   :::image type="content" source="./media/custom-route-alert-portal/job-id.png" alt-text="Identyfikator zadania" lightbox="./media/custom-route-alert-portal/job-id-expand.png":::

### <a name="5-parse-the-json"></a><a name="parse"></a>5. analizowanie pliku JSON

Informacje zawarte w danych wyjściowych z akcji "Azure Automation Utwórz zadanie" (poprzednie kroki) generują obiekt JSON. Logic Apps **Analiza JSON** to wbudowana akcja służąca do tworzenia tokenów przyjaznych dla użytkownika na podstawie właściwości i ich wartości w zawartości JSON. Możesz następnie użyć tych właściwości w przepływie pracy.

1. Dodaj akcję. W obszarze **Akcja Pobierz dane wyjściowe zadania >** wybierz pozycję **nowy krok**.
2. W polu wyszukiwania **Wybierz akcję** wpisz ciąg "Analizuj dane JSON", aby wyszukać łączniki, które oferują tę akcję. Na liście **Akcje** wybierz akcję **Przeanalizuj dane JSON** dla operacji na danych, których chcesz użyć.

   :::image type="content" source="./media/custom-route-alert-portal/parse-json.png" alt-text="Przeanalizuj dane JSON":::

3. Kliknij wewnątrz pola **zawartość** . Gdy zostanie wyświetlona lista zawartości dynamicznej, wybierz pozycję **zawartość**.

   :::image type="content" source="./media/custom-route-alert-portal/content.png" alt-text="Zrzut ekranu przedstawia okno dialogowe analizowanie pliku JSON z wybraną zawartością." lightbox="./media/custom-route-alert-portal/content-expand.png":::

4. Analizowanie pliku JSON wymaga schematu. Schemat można wygenerować przy użyciu danych wyjściowych elementu Runbook usługi Automation. Otwórz nową sesję przeglądarki sieci Web, Uruchom element Runbook usługi Automation i Pobierz dane wyjściowe. Wróć do akcji **Logic Apps analizowanie danych JSON** . W dolnej części strony wybierz pozycję **Użyj przykładowego ładunku do wygenerowania schematu**.

   :::image type="content" source="./media/custom-route-alert-portal/sample-payload.png" alt-text="Użyj przykładowego ładunku do wygenerowania schematu":::

5. Aby **wprowadzić lub wkleić przykładowy ładunek JSON**, wklej dane wyjściowe elementu Runbook automatyzacji i wybierz pozycję **gotowe**.

   :::image type="content" source="./media/custom-route-alert-portal/paste-payload.png" alt-text="Wklej przykładowy ładunek" lightbox="./media/custom-route-alert-portal/paste-payload-expand.png":::

6. Schemat jest generowany automatycznie przez analizowanie ładunku wejściowego JSON.

   :::image type="content" source="./media/custom-route-alert-portal/generate-schema.png" alt-text="Generuj schemat" lightbox="./media/custom-route-alert-portal/generate-schema-expand.png":::

### <a name="6-define-and-initialize-a-variable"></a><a name="define-variable"></a>6. Zdefiniuj i zainicjuj zmienną

W tym kroku przepływu pracy utworzymy warunek wysłania alarmu za pośrednictwem poczty e-mail. W przypadku elastycznego, niestandardowego formatowania komunikatu treści wiadomości e-mail jest wprowadzana zmienna pomocnicza w przepływie pracy.

1. W obszarze **Akcja Pobierz dane wyjściowe zadania** wybierz pozycję **nowy krok**. W polu wyszukiwania Znajdź i wybierz **zmienne**.

   :::image type="content" source="./media/custom-route-alert-portal/variables.png" alt-text="Zrzut ekranu przedstawia okno dialogowe Wybieranie akcji z zmienną w polu wyszukiwania i zmienne zaznaczone.":::

2. Z listy **Akcje** wybierz akcję **zainicjuj zmienną** .

   :::image type="content" source="./media/custom-route-alert-portal/initialize-variables.png" alt-text="Inicjowanie zmiennych":::

3. Określ nazwę zmiennej. W obszarze **Typ** wybierz pozycję **ciąg**. **Wartość** zmiennej zostanie przypisana później w przepływie pracy.

   :::image type="content" source="./media/custom-route-alert-portal/string.png" alt-text="Zrzut ekranu przedstawia kod JSON analizy skojarzony z zmienną Initialize, w której można wprowadzić nazwę, typ i wartość." lightbox="./media/custom-route-alert-portal/string-expand.png":::

### <a name="7-create-a-for-each-action"></a><a name="cycles-json"></a>7. Utwórz akcję "for each"

Po przeanalizowaniu kodu JSON Akcja **operacji analizy danych JSON** przechowuje *zawartość w danych wyjściowych* . Aby przetworzyć dane wyjściowe, można utworzyć pętlę "for each" powtarzające jedną lub więcej akcji dla każdego elementu w tablicy.

1. W obszarze **zainicjuj zmienną** wybierz pozycję **Dodaj akcję**. W polu wyszukiwania wpisz ciąg "for each" jako filtr.

   :::image type="content" source="./media/custom-route-alert-portal/control.png" alt-text="Zrzut ekranu przedstawia okno dialogowe Wybieranie akcji z opcją dla każdego w polu wyszukiwania i wybranym formancie.":::

2. Z listy **Akcje** wybierz akcję **dla każdej kontrolki**.

   :::image type="content" source="./media/custom-route-alert-portal/for-each.png" alt-text="Dla każdej kontrolki":::

3. Kliknij w polu tekstowym **Wybierz dane wyjściowe z poprzednich kroków** . Gdy zostanie wyświetlona lista **zawartości dynamicznej** , wybierz **treść**, która jest wyprowadzana z przeanalizowanego kodu JSON.

   :::image type="content" source="./media/custom-route-alert-portal/body.png" alt-text="Zrzut ekranu przedstawia zainicjowaną zmienną skojarzoną z for each, która zawiera pole tekstowe wybierz dane wyjściowe z poprzednich kroków.":::

4. Dla każdego elementu treści JSON chcemy ustawić warunek. Z grupy Akcja wybierz pozycję **Kontrola**.

   :::image type="content" source="./media/custom-route-alert-portal/condition-control.png" alt-text="Kontrola":::

5. Na liście **Akcje** wybierz pozycję **Kontrola warunku**. Condition-Control jest strukturą kontroli porównującą dane w przepływie pracy z określonymi wartościami lub polami. Następnie można określić różne akcje, które są uruchamiane w zależności od tego, czy dane spełniają warunek.

   :::image type="content" source="./media/custom-route-alert-portal/condition.png" alt-text="Kontrola warunku":::

6. W akcji głównej **warunku** Zmień operację logiki na **lub**.

   :::image type="content" source="./media/custom-route-alert-portal/condition-or.png" alt-text="Lub" lightbox="./media/custom-route-alert-portal/condition-or-expand.png":::

7. Sprawdź wartość w polu Liczba prefiksów sieci, które są anonsowane przez bramę ExpressRoute, do dwóch elementów równorzędnych protokołu BGP. Liczba tras jest dostępna w obszarze "numRoutePeer1" i "numRoutePeer2" w **zawartości dynamicznej**. W polu wartość wpisz wartość dla **numRoutePeer1**.

   :::image type="content" source="./media/custom-route-alert-portal/peer-1.png" alt-text="numRoutesPeer1":::

8. Aby dodać kolejny wiersz do warunku, wybierz polecenie **dodaj > Dodaj wiersz**. W drugim polu z **zawartości dynamicznej** wybierz pozycję **numRoutePeer2**.

   :::image type="content" source="./media/custom-route-alert-portal/peer-2.png" alt-text="numRoutesPeer2":::

9. Warunek logiki ma wartość true, jeśli jedna z dwóch zmiennych dynamicznych, numRoute1 lub numRoute2, jest większa niż wartość progowa. W tym przykładzie próg jest ustalony na 800 (80% maksymalnej wartości 1000 tras). Wartość progu można zmienić zgodnie z wymaganiami. W celu zapewnienia spójności wartość powinna być taka sama jak wartość używana w skrypcie programu PowerShell elementu Runbook.

   :::image type="content" source="./media/custom-route-alert-portal/logic-condition.png" alt-text="Warunek logiki":::

10. W obszarze **Jeśli prawda**, sformatuj i Utwórz akcje do wysłania alertu pocztą e-mail. W * * wybierz akcję, Wyszukaj i wybierz **zmienne**.

    :::image type="content" source="./media/custom-route-alert-portal/condition-if-true.png" alt-text="W przypadku wartości true":::

11. W obszarze Zmienne wybierz pozycję **Dodaj akcję**. Na liście **Akcje** wybierz pozycję **Ustaw zmienną**.

    :::image type="content" source="./media/custom-route-alert-portal/condition-set-variable.png" alt-text="Zrzut ekranu przedstawiający sekcję &quot;zmienne&quot; z wybraną kartą &quot;Actions&quot; i podświetloną pozycję &quot;Set variable&quot;.":::

12. W polu **Nazwa** wybierz zmienną o nazwie **EmailBody** , która została wcześniej utworzona. W polu **wartość** wklej skrypt HTML wymagany do sformatowania wiadomości e-mail dotyczącej alertu. Użyj **zawartości dynamicznej** , aby uwzględnić wartości treści JSON. Po skonfigurowaniu tych ustawień powstaje zmienna **emailBody** zawiera wszystkie informacje dotyczące alertu w formacie HTML.

    :::image type="content" source="./media/custom-route-alert-portal/paste-script.png" alt-text="Ustaw zmienną":::

### <a name="8-add-the-email-connector"></a><a name="email"></a>8. Dodawanie łącznika poczty E-mail

Logic Apps udostępnia wiele łączników poczty e-mail. W tym przykładzie dodamy łącznik programu Outlook do wysłania alertu pocztą e-mail. W obszarze **Ustaw zmienną** wybierz pozycję **Dodaj akcję**. W obszarze **Wybierz akcję** wpisz ciąg "Wyślij wiadomość e-mail" w polu wyszukiwania.

1. Wybierz pozycję **Office 365 Outlook**.

   :::image type="content" source="./media/custom-route-alert-portal/email.png" alt-text="Wyślij wiadomość e-mail":::

2. Na liście **Akcje** wybierz pozycję **Wyślij wiadomość E-mail (wersja 2)**.

   :::image type="content" source="./media/custom-route-alert-portal/email-v2.png" alt-text="Wyślij wiadomość e-mail (wersja 2)":::

3. Zaloguj się, aby utworzyć połączenie z pakietem Office 365 Outlook.

   :::image type="content" source="./media/custom-route-alert-portal/office-365.png" alt-text="Zaloguj się":::

4. W polu **treść** kliknij pozycję **Dodaj zawartość dynamiczną**. W panelu Zawartość dynamiczna Dodaj zmienną **emailBody**. Wypełnij pola **temat** i **do** .

   :::image type="content" source="./media/custom-route-alert-portal/emailbody.png" alt-text="Treść":::

5. Akcja **Wyślij wiadomość e-mail (v2)** Ukończ konfigurację przepływu pracy.

   :::image type="content" source="./media/custom-route-alert-portal/send-email-v2.png" alt-text="Wyślij wiadomość e-mail v2" lightbox="./media/custom-route-alert-portal/send-email-v2-expand.png":::

### <a name="9-workflow-validation"></a><a name="validation"></a>9. Sprawdzanie poprawności przepływu pracy

Ostatnim krokiem jest Walidacja przepływu pracy. W obszarze **przegląd Logic Apps** wybierz pozycję **Uruchom wyzwalacz**. Wybierz pozycję **cykl**. Przepływ pracy można monitorować i weryfikować w **historii uruchamiania**.

:::image type="content" source="./media/custom-route-alert-portal/trigger.png" alt-text="Uruchom wyzwalacz":::

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat dostosowywania przepływu pracy, zobacz [Azure Logic Apps](../logic-apps/logic-apps-overview.md).
