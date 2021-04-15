---
title: Powiadomienia dotyczące konserwacji zestawów skalowania maszyn wirtualnych na platformie Azure
description: Wyświetlanie powiadomień dotyczących konserwacji i uruchamianie samoobsługowej konserwacji zestawów skalowania maszyn wirtualnych na platformie Azure.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: maintenance-control
ms.date: 11/12/2020
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 355e29cf062b731ed26670497a8de75fef266b99
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375716"
---
# <a name="planned-maintenance-notifications-for-virtual-machine-scale-sets"></a>Powiadomienia dotyczące planowanej konserwacji zestawów skalowania maszyn wirtualnych


Platforma Azure okresowo przeprowadza aktualizacje w celu zwiększenia niezawodności, wydajności i bezpieczeństwa infrastruktury hosta dla maszyn wirtualnych. Aktualizacje mogą obejmować stosowanie poprawek do środowiska hostingu lub uaktualnianie i likwidowanie sprzętu. Większość aktualizacji nie ma wpływu na hostowane maszyny wirtualne. Jednak aktualizacje wpływają na maszyny wirtualne w tych scenariuszach:

- Jeśli konserwacja nie wymaga ponownego uruchomienia, platforma Azure wstrzymuje maszynę wirtualną na kilka sekund podczas aktualizowania hosta. Tego typu operacje konserwacji są stosowane w domenie błędów według domeny błędów. Postęp jest zatrzymywany, jeśli zostaną odebrane jakiekolwiek sygnały ostrzegawcze dotyczące kondycji.

- Jeśli konserwacja wymaga ponownego uruchomienia, otrzymasz powiadomienie o planowanym czasie konserwacji. W takich przypadkach masz podany czas, który zazwyczaj wynosi 35 dni, w którym możesz samodzielnie rozpocząć konserwację, gdy będzie to możliwe.


Planowana konserwacja, która wymaga ponownego uruchomienia, jest zaplanowana w falach. Każda fala ma inny zakres (regiony):

- Fala rozpoczyna się od powiadomienia do klientów. Domyślnie powiadomienie jest wysyłane do właściciela i współwłaścicieli subskrypcji. Do powiadomień można dodawać adresatów i opcje obsługi wiadomości, takie jak wiadomości e-mail, wiadomości SMS i webhook, przy użyciu alertów dziennika [aktywności platformy](../azure-monitor/essentials/platform-logs-overview.md)Azure.  
- Po powiadomieniu *zostanie udostępnione okno* samoobsługi. W tym oknie, które zwykle trwa 35 dni, można sprawdzić, które maszyny wirtualne znajdują się na fali. Możesz aktywnie rozpocząć konserwację zgodnie z własnymi potrzebami w zakresie planowania.
- Po oknie samoobsługi rozpoczyna *się okno zaplanowanej* konserwacji. W pewnym momencie w tym oknie platforma Azure planuje i stosuje wymaganą konserwację maszyny wirtualnej. 

Celem posiadania dwóch okien jest zapewninie wystarczającej ilości czasu na rozpoczęcie konserwacji i ponowne uruchomienie maszyny wirtualnej przy jednoczesnym poznaniu, kiedy platforma Azure automatycznie rozpocznie konserwację.

Możesz użyć interfejsu Azure Portal, programu PowerShell, interfejsu API REST i interfejsu wiersza polecenia platformy Azure do wykonywania zapytań o okna obsługi maszyn wirtualnych zestawu skalowania maszyn wirtualnych i uruchamiania konserwacji samoobsługowej.

## <a name="should-you-start-maintenance-during-the-self-service-window"></a>Czy należy rozpocząć konserwację w oknie samoobsługi?  

Poniższe wskazówki mogą pomóc w podjęciu decyzji o rozpoczęciu konserwacji w czasie, który wybierzesz.

> [!NOTE] 
> Konserwacja samoobsługowa może nie być dostępna dla wszystkich maszyn wirtualnych. Aby określić, czy aktywne ponowne uruchamianie jest dostępne  dla maszyny wirtualnej, poszukaj w stanie konserwacji rozpocznij teraz. Obecnie konserwacja samoobsługowa nie jest dostępna dla usług Azure Cloud Services (rola Sieć Web/Proces roboczy) i Azure Service Fabric.


Konserwacja samoobsługowa nie jest zalecana w przypadku wdrożeń, które korzystają z *zestawów dostępności.* Zestawy dostępności to konfiguracje o wysokiej dostępności, w których w dowolnym momencie ma to wpływ tylko na jedną domenę aktualizacji. W przypadku zestawów dostępności:

- Pozwól platformie Azure wyzwolić konserwację. W przypadku konserwacji wymagającej ponownego uruchomienia jest wykonywana konserwacja domeny aktualizacji według domeny aktualizacji. Domeny aktualizacji nie zawsze otrzymują konserwację sekwencyjnie. Między domenami aktualizacji jest 30-minutowa przerwa.
- Jeśli tymczasowa utrata części pojemności (1/liczba domen aktualizacji) jest problemem, możesz łatwo wyrównać stratę, przydzielając dodatkowe wystąpienia w okresie konserwacji.
- W przypadku konserwacji, która nie wymaga ponownego uruchomienia, aktualizacje są stosowane na poziomie domeny błędów. 
    
**Nie używaj konserwacji** samoobsługowej w następujących scenariuszach: 

- Jeśli maszyny wirtualne są często zamykane, ręcznie, przy użyciu usługi DevTest Labs, przy użyciu automatycznego zamykania lub zgodnie z harmonogramem. Konserwacja samoobsługowa w tych scenariuszach może przywrócić stan konserwacji i spowodować dodatkowe przestoje.
- W przypadku maszyn wirtualnych o krótkim okresie eksploatacji wiadomo, że zostaną usunięte przed zakończeniem fali konserwacji. 
- W przypadku obciążeń o dużym stanie przechowywanym na dysku lokalnym (efemeryjnie), który ma być utrzymywany po aktualizacji. 
- Jeśli często zmieniasz rozmiar maszyny wirtualnej. Ten scenariusz może przywrócić stan konserwacji. 
- Jeśli przyjęto zaplanowane zdarzenia, które umożliwiają aktywne tryb failover lub graceful zamykanie obciążenia 15 minut przed rozpoczęciem zamykania konserwacji.

**Użyj** konserwacji samoobsługowej, jeśli planujesz uruchamianie maszyny wirtualnej bez przerywania w fazie zaplanowanej konserwacji i żadne z powyższych działań zawężenia nie ma zastosowania. 

Najlepiej używać konserwacji samoobsługowej w następujących przypadkach:

- Należy przekazać dokładne okno obsługi do kierownictwa lub klienta. 
- Konserwację należy ukończyć do określonej daty. 
- Aby zagwarantować bezpieczne odzyskiwanie, należy kontrolować sekwencję konserwacji, na przykład w aplikacji wielowarstwowej.
- Potrzebujesz więcej niż 30 minut czasu odzyskiwania maszyny wirtualnej między dwiema domenami aktualizacji. Aby kontrolować czas między domenami aktualizacji, należy wyzwolić konserwację maszyn wirtualnych po jednej domenie aktualizacji na raz.

 
## <a name="view-virtual-machine-scale-sets-that-are-affected-by-maintenance-in-the-portal"></a>Wyświetlanie zestawów skalowania maszyn wirtualnych, na które ma wpływ konserwacja w portalu

Po zaplanowaniu planowanej fali konserwacji można wyświetlić listę zestawów skalowania maszyn wirtualnych, na które ma wpływ nadchodząca fala konserwacji, korzystając z Azure Portal. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu po lewej stronie wybierz pozycję **Wszystkie usługi,** a następnie wybierz **pozycję Zestawy skalowania maszyn wirtualnych.**
3. W **obszarze Zestawy skalowania maszyn wirtualnych** wybierz pozycję Edytuj **kolumny,** aby otworzyć listę dostępnych kolumn.
4. W sekcji **Dostępne kolumny** wybierz pozycję **Konserwacja samoobsługowa,** a następnie przenieś ją do **listy Wybrane kolumny.** Wybierz przycisk **Zastosuj**.  

    Aby ułatwić **znajdowanie elementu** Konserwacja samoobsługowa, możesz zmienić opcję listy rozwijanej w sekcji Dostępne kolumny z Wszystkie na **Właściwości**.  

Kolumna **Konserwacja samoobsługowa** jest teraz wyświetlana na liście zestawów skalowania maszyn wirtualnych. Każdy zestaw skalowania maszyn wirtualnych może mieć jedną z następujących wartości dla kolumny konserwacji samoobsługowej:

| Wartość | Opis |
|-------|-------------|
| Tak | Co najmniej jedna maszyna wirtualna w zestawie skalowania maszyn wirtualnych znajduje się w oknie samoobsługi. Konserwację można rozpocząć w dowolnym momencie w tym oknie samoobsługi. | 
| Nie | W zestawie skalowania maszyn wirtualnych, którego dotyczy problem, nie ma żadnych maszyn wirtualnych w oknie samoobsługi. | 
| - | Zestawy skalowania maszyn wirtualnych nie są częścią fali planowanej konserwacji.| 

## <a name="notification-and-alerts-in-the-portal"></a>Powiadomienia i alerty w portalu

Platforma Azure komunikuje harmonogram planowanej konserwacji, wysyłając wiadomość e-mail do właściciela subskrypcji i grupy współwłaścicieli. Do tej komunikacji możesz dodać adresatów i kanały, tworząc alerty dziennika aktywności. Aby uzyskać więcej informacji, zobacz [Monitorowanie aktywności subskrypcji za pomocą dziennika aktywności platformy Azure.](../azure-monitor/essentials/platform-logs-overview.md)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu po lewej stronie wybierz pozycję **Monitoruj.** 
3. W **okienku Monitor — alerty (klasyczne)** wybierz **pozycję +Dodaj alert dziennika aktywności.**
4. Na stronie **Dodawanie alertu dziennika** aktywności wybierz lub wprowadź żądane informacje. W **kryteria**, upewnij się, że ustawiono następujące wartości:
   - **Kategoria zdarzenia:** wybierz **Service Health**.
   - **Usługi:** wybierz **Virtual Machine Scale Sets i Virtual Machines**.
   - **Typ:** wybierz pozycję **Planowana konserwacja.** 
    
Aby dowiedzieć się więcej na temat konfigurowania alertów dziennika aktywności, zobacz [Tworzenie alertów dziennika aktywności](../azure-monitor/alerts/activity-log-alerts.md)
    
    
## <a name="start-maintenance-on-your-virtual-machine-scale-set-from-the-portal"></a>Rozpoczynanie konserwacji zestawu skalowania maszyn wirtualnych z portalu

Więcej szczegółów dotyczących konserwacji można znaleźć w temacie Overview of virtual machine scale sets (Omówienie zestawów skalowania maszyn wirtualnych). Jeśli co najmniej jedna maszyna wirtualna w zestawie skalowania maszyn wirtualnych jest uwzględniona w fali planowanej konserwacji, w górnej części strony zostanie dodana nowa wstążka powiadomień. Wybierz wstążkę powiadomień, aby przejść do **strony** Konserwacja. 

Na stronie **Konserwacja** możesz zobaczyć, na które wystąpienie maszyny wirtualnej ma wpływ planowana konserwacja. Aby rozpocząć konserwację, zaznacz pole wyboru odpowiadające maszynie wirtualnej, na która ona ma wpływ. Następnie wybierz pozycję **Rozpocznij konserwację.**

Po rozpoczęciu konserwacji maszyny wirtualne, których dotyczy problem, w zestawie skalowania maszyn wirtualnych są poddawane konserwacji i są tymczasowo niedostępne. Jeśli pominięto okno samoobsługi, nadal można zobaczyć okno czasu, w którym zestaw skalowania maszyn wirtualnych będzie utrzymywany przez platformę Azure.
 
## <a name="check-maintenance-status-by-using-powershell"></a>Sprawdzanie stanu konserwacji przy użyciu programu PowerShell

Możesz użyć Azure PowerShell, aby zobaczyć, kiedy maszyny wirtualne w zestawach skalowania maszyn wirtualnych są zaplanowane do konserwacji. Informacje dotyczące planowanej konserwacji są dostępne za pomocą polecenia cmdlet [Get-AzVmss](/powershell/module/az.compute/get-azvmss) w przypadku użycia `-InstanceView` parametru .
 
Informacje o konserwacji są zwracane tylko w przypadku planowanej konserwacji. Jeśli nie zaplanowano żadnej konserwacji, która ma wpływ na wystąpienie maszyny wirtualnej, polecenie cmdlet nie zwraca żadnych informacji o konserwacji. 

```powershell
Get-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -InstanceView
```

Następujące właściwości są zwracane w **obszarze MaintenanceRedeployStatus:** 

| Wartość | Opis   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Wskazuje, czy w tej chwili można rozpocząć konserwację maszyny wirtualnej. |
| PreMaintenanceWindowStartTime         | Początek okna samoobsługi konserwacji, w którym można zainicjować konserwację na maszynie wirtualnej. |
| PreMaintenanceWindowEndTime           | Koniec okna samoobsługi konserwacji, w którym można zainicjować konserwację na maszynie wirtualnej. |
| MaintenanceWindowStartTime            | Początek zaplanowanej konserwacji, w ramach której platforma Azure inicjuje konserwację maszyny wirtualnej. |
| MaintenanceWindowEndTime              | Koniec zaplanowanego okna konserwacji, w którym platforma Azure inicjuje konserwację maszyny wirtualnej. |
| LastOperationResultCode               | Wynik ostatniej próby zainicjowania konserwacji na maszynie wirtualnej. |



### <a name="start-maintenance-on-your-vm-instance-by-using-powershell"></a>Rozpoczynanie konserwacji wystąpienia maszyny wirtualnej przy użyciu programu PowerShell

Konserwację maszyny wirtualnej można rozpocząć, jeśli dla ustawienia **IsCustomerInitiatedMaintenanceAllowed** ustawiono wartość **true**. Użyj polecenia cmdlet [Set-AzVmss](/powershell/module/az.compute/set-azvmss) z `-PerformMaintenance` parametrem .

```powershell
Set-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -PerformMaintenance 
```

## <a name="check-maintenance-status-by-using-the-cli"></a>Sprawdzanie stanu konserwacji przy użyciu interfejsu wiersza polecenia

Informacje o planowanej konserwacji można wyświetlić za pomocą [narzędzia az vmss list-instances.](/cli/azure/vmss#az-vmss-list-instances)
 
Informacje o konserwacji są zwracane tylko w przypadku planowanej konserwacji. Jeśli nie zaplanowano żadnej konserwacji, która ma wpływ na wystąpienie maszyny wirtualnej, polecenie nie zwraca żadnych informacji o konserwacji. 

```azurecli
az vmss list-instances -g rgName -n vmssName --expand instanceView
```

Następujące właściwości są zwracane w obszarze **MaintenanceRedeployStatus** dla każdego wystąpienia maszyny wirtualnej: 

| Wartość | Opis   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Wskazuje, czy w tej chwili można rozpocząć konserwację maszyny wirtualnej. |
| PreMaintenanceWindowStartTime         | Początek okna samoobsługi konserwacji, w którym można zainicjować konserwację maszyny wirtualnej. |
| PreMaintenanceWindowEndTime           | Koniec okna samoobsługi konserwacji, w którym można zainicjować konserwację maszyny wirtualnej. |
| MaintenanceWindowStartTime            | Początek zaplanowanej konserwacji, w ramach której platforma Azure inicjuje konserwację maszyny wirtualnej. |
| MaintenanceWindowEndTime              | Koniec okna zaplanowanej konserwacji, w którym platforma Azure inicjuje konserwację maszyny wirtualnej. |
| LastOperationResultCode               | Wynik ostatniej próby zainicjowania konserwacji na maszynie wirtualnej. |


### <a name="start-maintenance-on-your-vm-instance-by-using-the-cli"></a>Rozpoczynanie konserwacji wystąpienia maszyny wirtualnej przy użyciu interfejsu wiersza polecenia

Następujące wywołanie inicjuje konserwację wystąpienia maszyny wirtualnej, jeśli `IsCustomerInitiatedMaintenanceAllowed` jest ustawiona na **wartość true:**

```azurecli
az vmss perform-maintenance -g rgName -n vmssName --instance-ids id
```

## <a name="faq"></a>Często zadawane pytania

**Pytanie: Dlaczego należy teraz ponownie uruchomić moje maszyny wirtualne?**

**A:** Mimo że większość aktualizacji i uaktualnień platformy Azure nie ma wpływu na dostępność maszyn wirtualnych, w niektórych przypadkach nie można uniknąć ponownego uruchamiania maszyn wirtualnych hostowanych na platformie Azure. Zebraliśmy kilka zmian, które wymagają ponownego uruchomienia serwerów, co spowoduje ponowne uruchomienie maszyny wirtualnej.

**Pytanie: Czy jeśli postępuję zgodnie z zaleceniami w zakresie wysokiej dostępności przy użyciu zestawu dostępności, czy mogę bezpiecznie?**

**A:** Maszyny wirtualne wdrożone w zestawie dostępności lub w zestawach skalowania maszyn wirtualnych używają domen aktualizacji. Podczas przeprowadzania konserwacji platforma Azure honoruje ograniczenie domeny aktualizacji i nie uruchamia ponownie maszyn wirtualnych z innej domeny aktualizacji (w tym samym zestawie dostępności). Platforma Azure czeka również co najmniej 30 minut przed przejściem do następnej grupy maszyn wirtualnych. 

Aby uzyskać więcej informacji na temat wysokiej dostępności, zobacz [Regions and availability for virtual machines in Azure (Regiony i dostępność maszyn wirtualnych na platformie Azure).](../virtual-machines/availability.md)

**Pytanie: Jak mogę być powiadamiany o planowanej konserwacji?**

**A:** Fala planowanej konserwacji rozpoczyna się od ustawienia harmonogramu na co najmniej jeden region świadczenia usługi Azure. Wkrótce do administratorów subskrypcji, współadministracyjnym, właścicieli i współautorów zostanie wysłane powiadomienie e-mail (jedna wiadomość e-mail na subskrypcję). Dodatkowe kanały i adresatów tego powiadomienia można skonfigurować przy użyciu alertów dziennika aktywności. W przypadku wdrożenia maszyny wirtualnej w regionie, w którym planowana konserwacja jest już zaplanowana, nie otrzymasz powiadomienia. Zamiast tego sprawdź stan konserwacji maszyny wirtualnej.

**Pytanie: Nie widzę żadnego wskazania dotyczącej planowanej konserwacji w portalu, programie PowerShell lub interfejsie wiersza polecenia. Co jest nie tak?**

**A:** Informacje związane z planowaną konserwacją są dostępne podczas fali planowanej konserwacji tylko dla maszyn wirtualnych, których dotyczy planowana konserwacja. Jeśli nie widzisz danych, fala konserwacji może być już zakończona (lub nie została uruchomiona) lub maszyna wirtualna może być już hostowana na zaktualizowanym serwerze.

**Pytanie: Czy istnieje sposób, aby dowiedzieć się dokładnie, kiedy będzie to dotyczyć mojej maszyny wirtualnej?**

**A:** Gdy ustawimy harmonogram, zdefiniujemy kilkudniowy okres czasu. Dokładne sekwencjonowanie serwerów (i maszyn wirtualnych) w tym oknie (przedziale czasu) jest nieznane. Jeśli chcesz dowiedzieć się, o której dokładnie godzinie maszyny wirtualne zostaną zaktualizowane, możesz użyć [zaplanowanych zdarzeń.](../virtual-machines/windows/scheduled-events.md) W przypadku korzystania z zaplanowanych zdarzeń można zapytania z poziomu maszyny wirtualnej i odbierać 15-minutowe powiadomienie przed ponownym uruchomieniem maszyny wirtualnej.

**Pytanie: Jak długo potrwa ponowne uruchomienie mojej maszyny wirtualnej?**

**A:**  W zależności od rozmiaru maszyny wirtualnej ponowne uruchomienie może potrwać do kilku minut w oknie konserwacji samoobsługowej. Podczas ponownego uruchamiania zainicjowanego przez platformę Azure w oknie zaplanowanej konserwacji ponowne uruchomienie trwa zwykle około 25 minut. Jeśli używasz Cloud Services (rola sieć Web/proces roboczy), zestawów skalowania maszyn wirtualnych lub zestawów dostępności, podczas zaplanowanego okna obsługi masz dostęp do 30 minut między każdą grupą maszyn wirtualnych (domeną aktualizacji). 

**Pytanie: Nie widzę żadnych informacji o konserwacji moich maszyn wirtualnych. Co poszło nie tak?**

**A:** Istnieje kilka powodów, dla których nie są dostępne żadne informacje dotyczące konserwacji maszyn wirtualnych:
   - Używasz subskrypcji oznaczonej jako *Microsoft Internal*.
   - Maszyny wirtualne nie są zaplanowane do konserwacji. Być może fala konserwacji została zakończona, anulowana lub zmodyfikowana, aby nie miała już na nie wpływu.
   - Kolumna Konserwacja nie jest **dodana** do widoku listy maszyn wirtualnych. Mimo że dodaliśmy tę kolumnę do widoku domyślnego, jeśli skonfigurujesz widok tak, aby wyświetlał kolumny inne niż domyślne, musisz ręcznie dodać kolumnę Konserwacja do widoku listy maszyn wirtualnych. 

**Pytanie: Moja maszyna wirtualna została zaplanowana do konserwacji po raz drugi. Dlaczego?**

**A:** W kilku przypadkach użycia maszyna wirtualna jest zaplanowana do konserwacji po zakończeniu konserwacji i jej ponownego zakończeniu:
   - Anulowaliśmy falę konserwacji i uruchomiliśmy ją ponownie z innym ładunkiem. Może się okazać, że wykryliśmy uszkodzony ładunek i musimy po prostu wdrożyć dodatkowy ładunek.
   - Twoja maszyna *wirtualna została zrównana z* innym węzłem z powodu błędu sprzętowego.
   - Wybrano opcję zatrzymania (cofenia alokacji) i ponownego uruchomienia maszyny wirtualnej.
   - Dla maszyny **wirtualnej jest** włączone automatyczne zamykanie.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak rejestrować zdarzenia konserwacji z poziomu maszyny wirtualnej przy użyciu [zaplanowanych zdarzeń.](../virtual-machines/windows/scheduled-events.md)
