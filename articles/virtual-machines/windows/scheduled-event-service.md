---
title: Monitorowanie zaplanowanych zdarzeń dla maszyn wirtualnych z systemem Windows na platformie Azure
description: Dowiedz się, jak monitorować maszyny wirtualne platformy Azure pod kątem zaplanowanych zdarzeń.
author: mysarn
ms.service: virtual-machines-windows
ms.subservice: monitoring
ms.date: 08/20/2019
ms.author: sarn
ms.topic: how-to
ms.openlocfilehash: 0d1edde5ac1b83feab458eb5d12d524163d3ffb1
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96483304"
---
# <a name="monitoring-scheduled-events"></a>Scheduled Events monitorowania

Aktualizacje są stosowane do różnych części platformy Azure codziennie, aby zapewnić ich bezpieczeństwo i aktualność usług. Oprócz planowanych aktualizacji mogą wystąpić również niezaplanowane zdarzenia. Jeśli na przykład zostanie wykryte obniżenie wydajności sprzętu lub błędu, usługi platformy Azure mogą wymagać nieplanowanej konserwacji. Korzystanie z migracji na żywo, zachowywanie i ogólnie bardziej rygorystycznego paska wpływu na aktualizacje, w większości przypadków te zdarzenia są prawie niewidoczne dla klientów i nie mają wpływu na kilka sekund blokowania maszyn wirtualnych. Jednak w przypadku niektórych aplikacji nawet kilka sekund zablokowania maszyny wirtualnej może spowodować wpływ. Zapoznaj się z wyprzedzeniem o zbliżającej się konserwacji platformy Azure, aby zapewnić najlepsze środowisko dla tych aplikacji. [Usługa Scheduled Events](scheduled-events.md) udostępnia interfejs programistyczny, który będzie powiadamiany o nadchodzącej konserwacji i pozwala bezpiecznie obsłużyć konserwację. 

W tym artykule pokazano, jak można użyć zaplanowanych zdarzeń, aby otrzymywać powiadomienia o zdarzeniach konserwacji, które mogą mieć wpływ na maszyny wirtualne i utworzyć podstawową automatyzację, która może pomóc w monitorowaniu i analizie.


## <a name="routing-scheduled-events-to-log-analytics"></a>Kierowanie zaplanowanych zdarzeń do Log Analytics

Scheduled Events jest dostępny w ramach [instance Metadata Service platformy Azure](instance-metadata-service.md), który jest dostępny na wszystkich maszynach wirtualnych platformy Azure. Klienci mogą napisać automatyzację, aby wysyłać zapytania do punktu końcowego maszyn wirtualnych w celu znalezienia powiadomień o zaplanowanej konserwacji i wykonywania środków zaradczych, takich jak zapisywanie stanu i przejmowanie maszyny wirtualnej poza rotacją. Zalecamy utworzenie automatyzacji, aby zarejestrować Scheduled Events tak, aby można było rejestrować inspekcje zdarzeń konserwacji platformy Azure. 

W tym artykule przeprowadzimy Cię przez proces przechwytywania Scheduled Events konserwacji do Log Analytics. Następnie wyzwolimy pewne podstawowe akcje powiadamiania, takie jak wysyłanie wiadomości e-mail do zespołu i uzyskiwanie historycznego widoku wszystkich zdarzeń, które miały oddziaływać z maszynami wirtualnymi. W przypadku agregacji zdarzeń i automatyzacji będziemy używać [log Analytics](../../azure-monitor/learn/quick-create-workspace.md), ale można użyć dowolnego rozwiązania monitorowania w celu zebrania tych dzienników i wyzwolenia automatyzacji.

![Diagram przedstawiający cykl życia zdarzenia](./media/notifications/events.png)

## <a name="prerequisites"></a>Wymagania wstępne

Na potrzeby tego przykładu należy utworzyć [maszynę wirtualną z systemem Windows w zestawie dostępności](tutorial-availability-sets.md). Scheduled Events udostępniać powiadomienia o zmianach, które mogą mieć wpływ na wszystkie maszyny wirtualne w zestawie dostępności, usłudze w chmurze, zestawie skalowania maszyn wirtualnych lub autonomicznych maszyn wirtualnych. Będziemy korzystać z [usługi](https://github.com/microsoft/AzureScheduledEventsService) , która sonduje zaplanowanych zdarzeń na jednej z maszyn wirtualnych, które będą pełnić rolę modułu zbierającego, aby uzyskać zdarzenia dla wszystkich innych maszyn wirtualnych w zestawie dostępności.    

Nie usuwaj grupy zasobów grupy na końcu samouczka.

Należy również [utworzyć obszar roboczy log Analytics](../../azure-monitor/learn/quick-create-workspace.md) , który będzie używany do agregowania informacji z maszyn wirtualnych w zestawie dostępności.

## <a name="set-up-the-environment"></a>Konfigurowanie środowiska

W zestawie dostępności powinny znajdować się teraz 2 początkowe maszyny wirtualne. Teraz musimy utworzyć trzecią maszynę wirtualną o nazwie myCollectorVM w tym samym zestawie dostępności. 

```azurepowershell-interactive
New-AzVm `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Name "myCollectorVM" `
   -Location "East US" `
   -VirtualNetworkName "myVnet" `
   -SubnetName "mySubnet" `
   -SecurityGroupName "myNetworkSecurityGroup" `
   -OpenPorts 3389 `
   -PublicIpAddressName "myPublicIpAddress3" `
   -AvailabilitySetName "myAvailabilitySet" `
   -Credential $cred
```
 

Pobierz plik zip instalacji projektu z usługi [GitHub](https://github.com/microsoft/AzureScheduledEventsService/archive/master.zip).

Połącz się z usługą **myCollectorVM** i skopiuj plik zip na maszynę wirtualną i Wyodrębnij wszystkie pliki. Na maszynie wirtualnej Otwórz wiersz polecenia programu PowerShell. Przenieś monit do folderu zawierającego `SchService.ps1` , na przykład: `PS C:\Users\azureuser\AzureScheduledEventsService-master\AzureScheduledEventsService-master\Powershell>` , i skonfiguruj usługę.

```powershell
.\SchService.ps1 -Setup
```

Uruchom usługę.

```powershell
.\SchService.ps1 -Start
```

Usługa zacznie teraz sondować co 10 sekund w przypadku wszelkich zaplanowanych zdarzeń i zatwierdzić zdarzenia w celu przyspieszenia konserwacji.  Zablokuj, przeprowadź ponowny rozruch, ponowne wdrożenie i przemieszczenie zdarzeń przez zdarzenia harmonogramu.   Należy pamiętać, że można zwiększyć skrypt, aby wyzwolił pewne środki zaradcze przed zatwierdzeniem zdarzenia.

Sprawdź stan usługi i upewnij się, że jest uruchomiona.

```powershell
.\SchService.ps1 -status  
```

Powinno to zwrócić `Running` .

Usługa zacznie teraz sondować co 10 sekund w przypadku wszelkich zaplanowanych zdarzeń i zatwierdzić zdarzenia w celu przyspieszenia konserwacji.  Zablokuj, przeprowadź ponowny rozruch, ponownie Wdróż i Zastąp zdarzenia przechwycone przez zdarzenia harmonogramu. Możesz zwiększyć skrypt, aby wyzwolił pewne środki zaradcze przed zatwierdzeniem zdarzenia.

Gdy dowolne z powyższych zdarzeń jest przechwytywane przez usługę zdarzenia harmonogramu, zostanie zarejestrowane w dzienniku zdarzeń aplikacji stan zdarzenia, typ zdarzenia, zasoby (nazwy maszyn wirtualnych) i NotBefore (minimalny okres powiadomienia). Zdarzenia o IDENTYFIKATORze 1234 można znaleźć w dzienniku zdarzeń aplikacji.

Po skonfigurowaniu i uruchomieniu usługi będzie ona rejestrować zdarzenia w dziennikach aplikacji systemu Windows.   Aby sprawdzić, czy to działa, uruchom ponownie jedną z maszyn wirtualnych w zestawie dostępności, a w dziennikach systemu Windows powinno zostać wyświetlone zdarzenie, które jest rejestrowane, > dziennik aplikacji pokazujący uruchomioną maszynę wirtualną. 

![Zrzut ekranu przedstawiający Podgląd zdarzeń.](./media/notifications/event-viewer.png)

Gdy zdarzenia są przechwytywane przez usługę zdarzenia harmonogramu, zostanie ona zarejestrowana w aplikacji nawet w dzienniku stanu zdarzenia, typu zdarzenia, zasobów (Nazwa maszyny wirtualnej) i NotBefore (okres minimalny). Zdarzenia o IDENTYFIKATORze 1234 można znaleźć w dzienniku zdarzeń aplikacji.

> [!NOTE] 
> W tym przykładzie maszyny wirtualne znajdowały się w zestawie dostępności, który umożliwił nam wyznaczenie pojedynczej maszyny wirtualnej jako modułu zbierającego w celu nasłuchiwania i kierowania zaplanowanych zdarzeń do naszego obszaru działania usługi log Analytics. Jeśli masz autonomiczną maszynę wirtualną, możesz uruchomić tę usługę na każdej maszynie wirtualnej, a następnie połączyć je pojedynczo z obszarem roboczym usługi log Analytics.
>
> W naszej konfiguracji wybieramy system Windows, ale można zaprojektować podobne rozwiązanie w systemie Linux.

W dowolnym momencie możesz zatrzymać/usunąć zaplanowaną usługę zdarzeń przy użyciu przełączników `–stop` i `–remove` .

## <a name="connect-to-the-workspace"></a>Łączenie z obszarem roboczym


Teraz chcemy połączyć obszar roboczy Log Analytics z maszyną wirtualną modułu zbierającego. Obszar roboczy Log Analytics działa jako repozytorium i skonfigurujemy zbieranie dzienników zdarzeń w celu przechwycenia dzienników aplikacji z maszyny wirtualnej modułu zbierającego. 

 Aby skierować Scheduled Events do dziennika zdarzeń, który zostanie zapisany jako dziennik aplikacji przez naszą usługę, musisz połączyć maszynę wirtualną z obszarem roboczym Log Analytics.  
 
1. Otwórz stronę utworzonego obszaru roboczego.
1. W obszarze **Połącz ze źródłem danych** wybierz pozycję **Azure Virtual Machines (maszyny wirtualne)**.

    ![Nawiązywanie połączenia z maszyną wirtualną jako źródłem danych](./media/notifications/connect-to-data-source.png)

1. Wyszukaj i wybierz pozycję **myCollectorVM**. 
1. Na stronie New for **myCollectorVM** wybierz pozycję **Połącz**.

Spowoduje to zainstalowanie [programu Microsoft Monitoring Agent](../extensions/oms-windows.md) na maszynie wirtualnej. Połączenie maszyny wirtualnej z obszarem roboczym i zainstalowanie rozszerzenia potrwa kilka minut. 

## <a name="configure-the-workspace"></a>Konfigurowanie obszaru roboczego

1. Otwórz stronę obszaru roboczego i wybierz pozycję **Ustawienia zaawansowane**.
1. Z menu po lewej stronie wybierz pozycję **dane** , a następnie wybierz pozycję **dzienniki zdarzeń systemu Windows**.
1. W obszarze **zbieranie danych z poniższych dzienników zdarzeń** zacznij pisać *aplikację* , a następnie wybierz **aplikację** z listy.

    ![Wybierz ustawienia zaawansowane](./media/notifications/advanced.png)

1. Pozostaw zaznaczone pole wyboru **błąd**, **Ostrzeżenie** i **informacje** , a następnie wybierz pozycję **Zapisz** , aby zapisać ustawienia.


> [!NOTE]
> Istnieje kilka opóźnień, po upływie których dziennik będzie dostępny, może upłynąć do 10 minut. 


## <a name="creating-an-alert-rule-with-azure-monitor"></a>Tworzenie reguły alertu z Azure Monitor 


Po wypchnięciu zdarzeń do Log Analytics można uruchomić następujące [zapytanie](../../azure-monitor/log-query/log-analytics-tutorial.md) , aby wyszukać zdarzenia harmonogramu.

1. W górnej części strony wybierz pozycję **dzienniki** i wklej następujący tekst do pola tekstowego:

    ```
    Event
    | where EventLog == "Application" and Source contains "AzureScheduledEvents" and RenderedDescription contains "Scheduled" and RenderedDescription contains "EventStatus" 
    | project TimeGenerated, RenderedDescription
    | extend ReqJson= parse_json(RenderedDescription)
    | extend EventId = ReqJson["EventId"]
    ,EventStatus = ReqJson["EventStatus"]
    ,EventType = ReqJson["EventType"]
    ,NotBefore = ReqJson["NotBefore"]
    ,ResourceType = ReqJson["ResourceType"]
    ,Resources = ReqJson["Resources"]
    | project-away RenderedDescription,ReqJson
    ```

1. Wybierz **pozycję Zapisz**, a następnie *wpisz logQuery* w polu Nazwa, pozostaw **zapytanie** jako typ, wpisz *VMLogs* jako **kategorię**, a następnie wybierz pozycję **Zapisz**. 

    ![Zapisz zapytanie](./media/notifications/save-query.png)

1. Wybierz przycisk **Nowa reguła alertu**. 
1. Na stronie **Tworzenie reguły** pozostaw `collectorworkspace` jako **zasób**.
1. W obszarze **warunek** wybierz wpis *za każdym razem, gdy przeszukiwane są <login undefined> dzienniki klienta*. Zostanie otwarta strona **Konfigurowanie logiki sygnału** .
1. W obszarze **wartość progowa** wprowadź *0* , a następnie wybierz pozycję **gotowe**.
1. W obszarze **Akcje** wybierz pozycję **Utwórz grupę akcji**. Zostanie otwarta strona **Dodaj grupę akcji** .
1. W polu **Nazwa grupy akcji** wpisz *Akcja*.
1. W polu **krótka nazwa** wpisz **Akcja**.
1. W obszarze **Grupa zasobów** wybierz pozycję **myResourceGroupAvailability**.
1. W obszarze Akcje w polu **Nazwa akcji** wpisz **adres e-mail**, a następnie wybierz pozycję **poczta e-mail/SMS/wypychanie/głos**. Zostanie otwarta strona **wiadomości e-mail/SMS/wypychana/głosowa** .
1. Wybierz pozycję **poczta e-mail**, wpisz adres e-mail, a następnie wybierz przycisk **OK**.
1. Na stronie **Dodaj grupę akcji** wybierz pozycję **OK**. 
1. Na stronie **Tworzenie reguły** w obszarze **szczegóły alertu** wpisz *alert* dla **nazwy reguły alertu**, a następnie wpisz w polu **Opis** *regułę alertu e-mail* .
1. Po zakończeniu wybierz pozycję **Utwórz regułę alertu**.
1. Uruchom ponownie jedną z maszyn wirtualnych w zestawie dostępności. W ciągu kilku minut otrzymasz wiadomość e-mail z informacją, że alert został wyzwolony.

Aby zarządzać regułami alertów, przejdź do grupy zasobów, wybierz pozycję **alerty** z menu po lewej stronie, a następnie wybierz pozycję **Zarządzaj regułami alertów** w górnej części strony.

     
## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej, zobacz stronę [usługi zaplanowane zdarzenia](https://github.com/microsoft/AzureScheduledEventsService) w witrynie GitHub.