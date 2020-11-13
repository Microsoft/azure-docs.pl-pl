---
title: Powiadomienia o konserwacji dla zestawów skalowania maszyn wirtualnych na platformie Azure
description: Wyświetl powiadomienia dotyczące konserwacji i uruchom samoobsługową konserwację zestawów skalowania maszyn wirtualnych na platformie Azure.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 11/12/2020
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 2aa589d237a8cfeb8e0dc947896dba82e755631c
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94564773"
---
# <a name="planned-maintenance-notifications-for-virtual-machine-scale-sets"></a>Powiadomienia dotyczące planowanej konserwacji zestawów skalowania maszyn wirtualnych


Platforma Azure okresowo przeprowadza aktualizacje, aby zwiększyć niezawodność, wydajność i bezpieczeństwo infrastruktury hosta dla maszyn wirtualnych. Aktualizacje mogą obejmować stosowanie poprawek do środowiska hostingu lub uaktualnianie i likwidowanie sprzętu. Większość aktualizacji nie ma wpływu na hostowane maszyny wirtualne. Jednak aktualizacje mają wpływ na maszyny wirtualne w tych scenariuszach:

- Jeśli konserwacja nie wymaga ponownego uruchomienia, platforma Azure wstrzymuje maszynę wirtualną przez kilka sekund, gdy host zostanie zaktualizowany. Te typy operacji konserwacyjnych są stosowane w domenie błędów przez domenę błędów. Postęp jest zatrzymany w przypadku odebrania ostrzeżeń o ostrzeżeniach o kondycji.

- Jeśli konserwacja wymaga ponownego uruchomienia, otrzymasz powiadomienie o zaplanowaniu konserwacji. W takich przypadkach użytkownik otrzymuje przedział czasu, który zwykle 35 dni, w którym można samodzielnie rozpocząć konserwację, gdy będzie ona działać.


Planowana konserwacja wymagająca ponownego uruchomienia jest zaplanowana na fale. Każda fala ma inny zakres (regiony):

- Fala rozpoczyna się od powiadomienia do klientów. Domyślnie powiadomienia są wysyłane do właściciela subskrypcji i współwłaścicieli. Za pomocą [alertów dziennika aktywności](../azure-monitor/platform/platform-logs-overview.md)platformy Azure Możesz dodać adresatów i opcje obsługi wiadomości, takie jak wiadomości e-mail, wiadomości SMS i elementy webhook do powiadomień.  
- Z powiadomieniem jest udostępniane *okno* samoobsługowe. W tym oknie, które jest zwykle 35 dni, można znaleźć, które maszyny wirtualne znajdują się w tej fazie. Możesz proaktywnie rozpocząć konserwację zgodnie z własnymi potrzebami w zakresie planowania.
- Po włączeniu okna samoobsługowego zostanie rozpoczęte *zaplanowane okno obsługi* . W pewnym momencie w tym oknie usługa Azure planuje i stosuje wymaganą konserwację na maszynie wirtualnej. 

Celem dwóch okien jest udostępnienie wystarczającej ilości czasu na rozpoczęcie konserwacji i ponowne uruchomienie maszyny wirtualnej, co pozwoli na automatyczne rozpoczęcie konserwacji przez platformę Azure.

Możesz użyć Azure Portal, programu PowerShell, interfejsu API REST i wiersza polecenia platformy Azure, aby wykonać zapytanie o okna obsługi dla maszyn wirtualnych zestawu skalowania maszyny wirtualnej oraz uruchomić samoobsługową konserwację.

## <a name="should-you-start-maintenance-during-the-self-service-window"></a>Czy należy uruchomić konserwację w trakcie samoobsługowego okna?  

Poniższe wskazówki ułatwią podjęcie decyzji o konieczności rozpoczęcia konserwacji w wybranym momencie.

> [!NOTE] 
> Konserwacja samoobsługowa może być niedostępna dla wszystkich maszyn wirtualnych. Aby określić, czy aktywne ponowne wdrażanie jest dostępne dla maszyny wirtualnej, poszukaj w obszarze stan konserwacji pozycję **Rozpocznij teraz** . Obecnie konserwacja samoobsługowa nie jest dostępna dla usługi Azure Cloud Services (rola sieć Web/proces roboczy) i Azure Service Fabric.


Konserwacja samoobsługowa nie jest zalecana w przypadku wdrożeń korzystających z *zestawów dostępności*. Zestawy dostępności są instalacjami o dużej dostępności, w których dotyczy tylko jednej domeny aktualizacji. Dla zestawów dostępności:

- Pozwól, aby platforma Azure wyzwolił konserwację. Aby przeprowadzić konserwację wymagającą ponownego uruchomienia, konserwacja jest gotowa do zaktualizowania domeny za pomocą domeny aktualizacji. Domeny aktualizacji niekoniecznie odbierają konserwację sekwencyjnie. Między domenami aktualizacji istnieje 30-minutowe wstrzymanie.
- Jeśli nieprzerwana utrata pewnej pojemności (1/aktualizacja liczby domen) jest istotna, można łatwo wyrównać straty, przydzielając dodatkowe wystąpienia w okresie konserwacji.
- W przypadku konserwacji, która nie wymaga ponownego uruchomienia, aktualizacje są stosowane na poziomie domeny błędów. 
    
**Nie** należy używać samoobsługowej konserwacji w następujących scenariuszach: 

- Jeśli regularnie zamykasz maszyny wirtualne, ręcznie przy użyciu usługi DevTest Labs, za pomocą automatycznego zamykania lub zgodnie z harmonogramem. Samoobsługowe konserwacje w tych scenariuszach mogą spowodować przywrócenie stanu konserwacji i wypełnianie dodatkowych przestojów.
- Na krótkoterminowych maszynach wirtualnych, które znasz, zostaną usunięte przed końcem fazy obsługi. 
- W przypadku obciążeń o dużym stanie przechowywanym na dysku lokalnym (tymczasowych), który ma być utrzymywany po aktualizacji. 
- W przypadku częstego zmieniania rozmiaru maszyny wirtualnej. Ten scenariusz może spowodować przywrócenie stanu konserwacji. 
- W przypadku zaakceptowania zaplanowanych zdarzeń, które umożliwiają aktywne przejście w tryb failover lub bezpieczne zamknięcie obciążenia 15 minut przed rozpoczęciem zamykania konserwacji.

**Użyj konserwacji** samoobsługowej, jeśli planujesz uruchomić maszynę wirtualną w trybie nieprzerwanym w fazie zaplanowanej konserwacji, a żaden z powyższych counterindications nie ma zastosowania. 

Najlepiej używać samoobsługowej konserwacji w następujących przypadkach:

- Należy skontaktować się z dokładnym oknem obsługi, aby zarządzać lub klientem. 
- Należy przeprowadzić konserwację w określonym dniu. 
- W celu zagwarantowania bezpiecznego odzyskiwania należy kontrolować sekwencję konserwacji, na przykład w aplikacji wielowarstwowej.
- Między dwiema domenami aktualizacji wymagana jest więcej niż 30 minut czasu odzyskiwania maszyny wirtualnej. Aby kontrolować czas między domenami aktualizacji, należy wyzwolić konserwację na maszynach wirtualnych w jednej domenie aktualizacji naraz.

 
## <a name="view-virtual-machine-scale-sets-that-are-affected-by-maintenance-in-the-portal"></a>Wyświetlanie zestawów skalowania maszyn wirtualnych, na które mają wpływ konserwacja w portalu

Po zaplanowaniu planowanej fazy konserwacji można wyświetlić listę zestawów skalowania maszyn wirtualnych, na które ma wpływ nadchodząca fala konserwacji, przy użyciu Azure Portal. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu po lewej stronie wybierz pozycję **wszystkie usługi** , a następnie wybierz pozycję **zestawy skalowania maszyn wirtualnych**.
3. W obszarze **zestawy skalowania maszyn wirtualnych** wybierz pozycję **Edytuj kolumny** , aby otworzyć listę dostępnych kolumn.
4. W sekcji **Dostępne kolumny** wybierz opcję **konserwacja** samoobsługowa, a następnie przenieś ją na listę **wybrane kolumny** . Wybierz pozycję **Zastosuj**.  

    Aby ułatwić znalezienie elementu **konserwacji samoobsługowej** , można zmienić opcję listy rozwijanej w sekcji **Dostępne kolumny** ze **wszystkich** na **Właściwości**.

Na liście zestawów skalowania maszyn wirtualnych jest teraz wyświetlana kolumna **konserwacja samoobsługowa** . Każdy zestaw skalowania maszyn wirtualnych może mieć jedną z następujących wartości w kolumnie konserwacja samoobsługowa:

| Wartość | Opis |
|-------|-------------|
| Tak | Co najmniej jedna maszyna wirtualna w zestawie skalowania maszyn wirtualnych znajduje się w oknie samoobsługowym. Konserwację można uruchomić w dowolnym momencie podczas tego okna samoobsługowego. | 
| Nie | Żadne maszyny wirtualne nie znajdują się w oknie samoobsługowym w zestawie skalowania maszyn wirtualnych, których to dotyczy. | 
| - | Zestawy skalowania maszyn wirtualnych nie są częścią planowanej fazy konserwacji.| 

## <a name="notification-and-alerts-in-the-portal"></a>Powiadomienia i alerty w portalu

Platforma Azure komunikuje harmonogram planowanej konserwacji, wysyłając wiadomość e-mail do właściciela subskrypcji i grupy współwłaścicieli. Aby dodać adresatów i kanały do tej komunikacji, można utworzyć alerty dziennika aktywności. Aby uzyskać więcej informacji, zobacz [monitorowanie aktywności subskrypcji przy użyciu dziennika aktywności platformy Azure](../azure-monitor/platform/platform-logs-overview.md).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu po lewej stronie wybierz pozycję **Monitoruj**. 
3. W okienku **monitorowanie — alerty (klasyczne)** wybierz pozycję **+ Dodaj alert dziennika aktywności**.
4. Na stronie **Dodawanie alertu dziennika aktywności** wybierz lub wprowadź żądane informacje. W obszarze **kryteria** upewnij się, że ustawisz następujące wartości:
   - **Kategoria zdarzenia** : wybierz pozycję **Service Health**.
   - **Usługi** : Wybierz **Virtual Machine Scale Sets i Virtual Machines**.
   - **Typ** : wybierz pozycję **Planowana konserwacja**. 
    
Aby dowiedzieć się więcej o konfigurowaniu alertów dziennika aktywności, zobacz [tworzenie alertów dziennika aktywności](../azure-monitor/platform/activity-log-alerts.md)
    
    
## <a name="start-maintenance-on-your-virtual-machine-scale-set-from-the-portal"></a>Rozpocznij konserwację na zestawie skalowania maszyn wirtualnych z poziomu portalu

Więcej szczegółów dotyczących konserwacji można znaleźć w temacie Omówienie zestawów skalowania maszyn wirtualnych. Jeśli co najmniej jedna maszyna wirtualna w zestawie skalowania maszyn wirtualnych jest uwzględniona w planowanej fazie konserwacji, w górnej części strony zostanie dodana nowa wstążka powiadomień. Wybierz Wstążkę powiadomienia, aby przejść do strony **konserwacji** . 

Na stronie **Obsługa** można sprawdzić, które wystąpienie maszyny wirtualnej ma wpływ na zaplanowaną konserwację. Aby rozpocząć konserwację, zaznacz pole wyboru odpowiadające maszynie wirtualnej, której to dotyczy. Następnie wybierz pozycję  **Rozpocznij konserwację**.

Po rozpoczęciu konserwacji odpowiednie maszyny wirtualne w zestawie skalowania maszyn wirtualnych są poddawane konserwacji i są tymczasowo niedostępne. Jeśli nie pozostało okna samoobsługowego, nadal możesz zobaczyć przedział czasu, gdy zestaw skalowania maszyn wirtualnych będzie utrzymywany przez platformę Azure.
 
## <a name="check-maintenance-status-by-using-powershell"></a>Sprawdzanie stanu konserwacji przy użyciu programu PowerShell

Możesz użyć Azure PowerShell, aby sprawdzić, kiedy maszyny wirtualne w zestawach skalowania maszyn wirtualnych są zaplanowane do konserwacji. Informacje o planowanej konserwacji są dostępne za pomocą polecenia cmdlet [Get-AzVmss](/powershell/module/az.compute/get-azvmss) przy użyciu `-InstanceView` parametru.
 
Informacje o konserwacji są zwracane tylko wtedy, gdy konserwacja jest zaplanowana. Jeśli nie zaplanowano żadnej konserwacji, która ma wpływ na wystąpienie maszyny wirtualnej, polecenie cmdlet nie zwróci żadnych informacji o obsłudze. 

```powershell
Get-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -InstanceView
```

Następujące właściwości są zwracane w obszarze **MaintenanceRedeployStatus** : 

| Wartość | Opis   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Wskazuje, czy w tej chwili można uruchomić konserwację maszyny wirtualnej. |
| PreMaintenanceWindowStartTime         | Początek okna samoobsługowego konserwacji, gdy można zainicjować konserwację na maszynie wirtualnej. |
| PreMaintenanceWindowEndTime           | Koniec okna samoobsługowego konserwacji, gdy można zainicjować konserwację na maszynie wirtualnej. |
| MaintenanceWindowStartTime            | Rozpoczęcie konserwacji zaplanowanej, w której platforma Azure inicjuje konserwację na maszynie wirtualnej. |
| MaintenanceWindowEndTime              | Koniec zaplanowanego okna konserwacji, w którym platforma Azure inicjuje konserwację na maszynie wirtualnej. |
| LastOperationResultCode               | Wynik ostatniej próby zainicjowania konserwacji na maszynie wirtualnej. |



### <a name="start-maintenance-on-your-vm-instance-by-using-powershell"></a>Rozpocznij konserwację w wystąpieniu maszyny wirtualnej przy użyciu programu PowerShell

Możesz rozpocząć konserwację na maszynie wirtualnej, jeśli **IsCustomerInitiatedMaintenanceAllowed** jest ustawiona na **wartość true**. Użyj polecenia cmdlet [Set-AzVmss](/powershell/module/az.compute/set-azvmss) z `-PerformMaintenance` parametrem.

```powershell
Set-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -PerformMaintenance 
```

## <a name="check-maintenance-status-by-using-the-cli"></a>Sprawdzanie stanu konserwacji przy użyciu interfejsu wiersza polecenia

Informacje o planowanej konserwacji można wyświetlić za pomocą polecenia [AZ VMSS list-Instances](/cli/azure/vmss?view=azure-cli-latest#az-vmss-list-instances).
 
Informacje o konserwacji są zwracane tylko wtedy, gdy konserwacja jest zaplanowana. Jeśli nie ma żadnej konserwacji wpływającej na wystąpienie maszyny wirtualnej, polecenie nie zwróci żadnych informacji o obsłudze. 

```azurecli
az vmss list-instances -g rgName -n vmssName --expand instanceView
```

Następujące właściwości są zwracane w obszarze **MaintenanceRedeployStatus** dla każdego wystąpienia maszyny wirtualnej: 

| Wartość | Opis   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Wskazuje, czy w tej chwili można uruchomić konserwację maszyny wirtualnej. |
| PreMaintenanceWindowStartTime         | Początek okna samoobsługowego konserwacji, gdy można zainicjować konserwację na maszynie wirtualnej. |
| PreMaintenanceWindowEndTime           | Koniec okna samoobsługowego konserwacji, gdy można zainicjować konserwację na maszynie wirtualnej. |
| MaintenanceWindowStartTime            | Rozpoczęcie konserwacji zaplanowanej, w której platforma Azure inicjuje konserwację na maszynie wirtualnej. |
| MaintenanceWindowEndTime              | Koniec zaplanowanego okna konserwacji, w którym platforma Azure inicjuje konserwację na maszynie wirtualnej. |
| LastOperationResultCode               | Wynik ostatniej próby zainicjowania konserwacji na maszynie wirtualnej. |


### <a name="start-maintenance-on-your-vm-instance-by-using-the-cli"></a>Uruchom konserwację wystąpienia maszyny wirtualnej przy użyciu interfejsu wiersza polecenia

Następujące wywołanie inicjuje konserwację w wystąpieniu maszyny wirtualnej, jeśli `IsCustomerInitiatedMaintenanceAllowed` jest ustawione na **wartość true** :

```azurecli
az vmss perform-maintenance -g rgName -n vmssName --instance-ids id
```

## <a name="faq"></a>Często zadawane pytania

**P: Dlaczego musisz teraz ponownie uruchomić moje maszyny wirtualne?**

Odp **.:** Chociaż większość aktualizacji i uaktualnień na platformie Azure nie ma wpływu na dostępność maszyny wirtualnej, w niektórych przypadkach nie można uniknąć ponownego uruchamiania maszyn wirtualnych hostowanych na platformie Azure. Zebrano kilka zmian, które wymagają ponownego uruchomienia naszych serwerów, co spowoduje ponowne uruchomienie maszyny wirtualnej.

**P: Jeśli obserwuję zalecenia dotyczące wysokiej dostępności przy użyciu zestawu dostępności, czy jest to bezpieczne?**

Odp **.:** Maszyny wirtualne wdrożone w zestawie dostępności lub w zestawach skalowania maszyn wirtualnych używają domen aktualizacji. Podczas przeprowadzania konserwacji platforma Azure uznaje ograniczenie domeny aktualizacji i nie uruchamia ponownie maszyn wirtualnych z innej domeny aktualizacji (w ramach tego samego zestawu dostępności). Platforma Azure czeka także przez co najmniej 30 minut przed przejściem do następnej grupy maszyn wirtualnych. 

Aby uzyskać więcej informacji o wysokiej dostępności, zobacz [regiony i dostępność maszyn wirtualnych na platformie Azure](../virtual-machines/availability.md).

**P: Jak mogę otrzymywać powiadomienia o planowanej konserwacji?**

Odp **.:** Planowana fala konserwacji jest uruchamiana przez ustawienie harmonogramu do co najmniej jednego regionu świadczenia usługi Azure. Wkrótce po otrzymaniu powiadomienia e-mail do administratorów subskrypcji, współadministratorów, właścicieli i współautorów (jednej wiadomości e-mail na subskrypcję). Dodatkowe kanały i adresatów dla tego powiadomienia można skonfigurować przy użyciu alertów dziennika aktywności. W przypadku wdrożenia maszyny wirtualnej w regionie, w którym zaplanowano zaplanowaną konserwację, nie otrzymasz powiadomienia. Zamiast tego Sprawdź stan konserwacji maszyny wirtualnej.

**P: nie widzę żadnych oznak planowanej konserwacji w portalu, programie PowerShell lub interfejsie wiersza polecenia. Co jest nie tak?**

Odp **.:** Informacje związane z planowaną konserwacją są dostępne podczas planowanej fazy konserwacji tylko dla maszyn wirtualnych, na które ma wpływ planowana konserwacja. Jeśli nie widzisz danych, fala konserwacji może już zostać zakończona (lub nie została uruchomiona) lub maszyna wirtualna może już być hostowana na zaktualizowanym serwerze.

**P: czy istnieje sposób, aby dokładnie poznać, kiedy wpłynie na moją maszynę wirtualną?**

Odp **.:** Po ustawieniu harmonogramu zdefiniujemy przedział czasu kilka dni. Dokładne sekwencjonowanie serwerów (i maszyn wirtualnych) w tym oknie (przedziale czasu) jest nieznane. Jeśli chcesz dowiedzieć się, kiedy maszyny wirtualne zostaną zaktualizowane, możesz użyć [zdarzeń zaplanowanych](../virtual-machines/windows/scheduled-events.md). W przypadku korzystania z zaplanowanych zdarzeń można wysyłać zapytania z maszyny wirtualnej i odbierać 15-minutowy powiadomienie przed ponownym uruchomieniem maszyny wirtualnej.

**P: jak długo zajmiemy się ponownym uruchomieniem maszyny wirtualnej?**

Odp **.:**  W zależności od rozmiaru maszyny wirtualnej ponowny rozruch może potrwać kilka minut podczas samoobsługowego okna obsługi. Po ponownym uruchomieniu inicjowanym przez platformę Azure w oknie zaplanowanej konserwacji ponowne uruchomienie zazwyczaj trwa około 25 minut. Jeśli używasz Cloud Services (roli sieć Web/proces roboczy), zestawów skalowania maszyn wirtualnych lub zestawów dostępności, otrzymasz 30 minut między każdą grupą maszyn wirtualnych (Aktualizuj domenę) w oknie zaplanowanej konserwacji. 

**P: nie widzę żadnych informacji o konserwacji na moich maszynach wirtualnych. Co się stało?**

Odp **.:** Istnieje kilka powodów, dla których nie widzisz żadnych informacji na temat obsługi maszyn wirtualnych:
   - Używasz subskrypcji oznaczonej jako *wewnętrzne firmy Microsoft*.
   - Maszyny wirtualne nie są zaplanowane do konserwacji. Może się zdarzyć, że fala konserwacji została zakończona, została anulowana, lub została zmodyfikowana, tak aby maszyny wirtualne nie miały już na nie wpływ.
   - Do widoku listy maszyn wirtualnych nie została dodana kolumna **konserwacja** . Mimo że Dodaliśmy tę kolumnę do widoku domyślnego, w przypadku skonfigurowania widoku do wyświetlania kolumn innych niż domyślne należy ręcznie dodać kolumnę **konserwacji** do widoku listy maszyn wirtualnych.

**P: zaplanowano konserwację maszyny wirtualnej po raz drugi. Zalet?**

Odp **.:** W przypadku kilku przypadków użycia zaplanowano konserwację maszyny wirtualnej po zakończeniu konserwacji i ponownym wdrożeniu:
   - Twoja fala została anulowana i uruchomiona ponownie z innym ładunkiem. Może się okazać, że wykryto błąd ładunku, a po prostu musimy wdrożyć dodatkowy ładunek.
   - Twoja maszyna wirtualna została zaakceptowana w innym *węźle z powodu* błędu sprzętowego.
   - Wybrano zatrzymanie (Cofnięcie alokacji) i ponowne uruchomienie maszyny wirtualnej.
   - Włączono **automatyczne zamykanie** dla maszyny wirtualnej.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak zarejestrować zdarzenia konserwacji z poziomu maszyny wirtualnej za pomocą [zaplanowanych zdarzeń](../virtual-machines/windows/scheduled-events.md).
