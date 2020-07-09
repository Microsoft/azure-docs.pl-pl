---
title: Dostęp do maszyny wirtualnej just in Time w Azure Security Center | Microsoft Docs
description: W tym dokumencie pokazano, jak dostęp just in Time do maszyny wirtualnej w Azure Security Center pomaga kontrolować dostęp do maszyn wirtualnych platformy Azure.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: b24e0487aef73ed7852cb4a64766a1f8d92aff94
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84677439"
---
# <a name="secure-your-management-ports-with-just-in-time-access"></a>Zabezpieczanie portów zarządzania przy użyciu dostępu just in Time

Jeśli korzystasz z standardowej warstwy cenowej Security Center (zobacz [Cennik](/azure/security-center/security-center-pricing)), możesz zablokować ruch przychodzący do maszyn wirtualnych platformy Azure za pomocą dostępu do maszyny wirtualnej "just in Time" (VM). Pozwala to ograniczyć narażenie na ataki, zapewniając łatwy dostęp do połączeń z maszynami wirtualnymi w razie potrzeby.

> [!NOTE]
> Security Center dostęp do maszyny wirtualnej just in Time obsługuje obecnie tylko maszyny wirtualne wdrożone za pośrednictwem Azure Resource Manager. Aby dowiedzieć się więcej na temat modeli wdrażania klasycznego i Menedżer zasobów, zobacz [Azure Resource Manager a wdrożenie klasyczne](../azure-resource-manager/management/deployment-models.md).

[!INCLUDE [security-center-jit-description](../../includes/security-center-jit-description.md)]

## <a name="configure-jit-on-a-vm"></a>Konfigurowanie JIT na maszynie wirtualnej

Istnieją trzy sposoby konfigurowania zasad JIT na maszynie wirtualnej:

- [Konfigurowanie dostępu JIT w Azure Security Center](#jit-asc)
- [Konfigurowanie dostępu JIT na stronie maszyny wirtualnej platformy Azure](#jit-vm)
- [Programowe Konfigurowanie zasad JIT na maszynie wirtualnej](#jit-program)

## <a name="configure-jit-in-azure-security-center"></a>Konfigurowanie JIT w Azure Security Center

W Security Center można skonfigurować zasady JIT i zażądać dostępu do maszyny wirtualnej przy użyciu zasad JIT

### <a name="configure-jit-access-on-a-vm-in-security-center"></a>Konfigurowanie dostępu JIT na maszynie wirtualnej w Security Center<a name="jit-asc"></a>

1. Otwórz pulpit nawigacyjny usługi **Security Center**.

1. W lewym okienku wybierz pozycję **dostęp just in Time do maszyny wirtualnej**.

    ![Kafelek dostępu just in Time do maszyny wirtualnej](./media/security-center-just-in-time/just-in-time.png)

    Zostanie otwarte okno **dostęp just in Time do maszyny wirtualnej** , w którym są wyświetlane informacje o stanie maszyn wirtualnych:

    - **Skonfigurowane** — maszyny wirtualne, które zostały skonfigurowane do obsługi dostępu just in Time do maszyny wirtualnej. Przedstawione dane dotyczą ostatniego tygodnia i obejmują dla każdej maszyny wirtualnej liczbę zatwierdzonych żądań, datę ostatniego dostępu i godzinę ostatniego użytkownika.
    - **Zalecane** — maszyny wirtualne, które mogą obsługiwać dostęp do maszyny wirtualnej just-in-Time, ale nie zostały skonfigurowane do programu. Zalecamy włączenie kontroli dostępu just in Time do maszyn wirtualnych.
    - **Brak zaleceń** — powody, dla których maszyna wirtualna może nie mieć zaleceń:
      - Brak sieciowej grupy zabezpieczeń — rozwiązanie just in Time wymaga, aby sieciowej grupy zabezpieczeń.
      - Klasyczna maszyna wirtualna — Security Center dostęp do maszyny wirtualnej just in Time aktualnie obsługuje tylko maszyny wirtualne wdrożone za pośrednictwem Azure Resource Manager. Wdrożenie klasyczne nie jest obsługiwane przez rozwiązanie just-in-Time. 
      - Inne — maszyna wirtualna jest w tej kategorii, jeśli rozwiązanie just in time jest wyłączone w zasadach zabezpieczeń subskrypcji lub grupy zasobów lub jeśli maszyna wirtualna nie ma publicznego adresu IP i nie ma sieciowej grupy zabezpieczeń.

1. Wybierz kartę **zalecane** .

1. W obszarze **maszyna wirtualna**kliknij maszyny wirtualne, które chcesz włączyć. Spowoduje to umieszczenie znacznika wyboru obok maszyny wirtualnej.

      ![Włącz dostęp just in Time](./media/security-center-just-in-time/enable-just-in-time.png)

1. Kliknij pozycję **Włącz JIT na maszynach wirtualnych**. Zostanie otwarte okienko zawierające porty domyślne zalecane przez Azure Security Center:
    - 22 — SSH
    - 3389 — RDP
    - 5985 — WinRM 
    - 5986 — WinRM
1. Opcjonalnie można dodać do listy porty niestandardowe:

      1. Kliknij pozycję **Dodaj**. Zostanie otwarte okno **Dodaj konfigurację portu** .
      1. Dla każdego skonfigurowanego portu, zarówno domyślnego, jak i niestandardowego, można dostosować następujące ustawienia:
            - **Typ protokołu**— protokół, który jest dozwolony na tym porcie w przypadku zatwierdzenia żądania.
            - **Dozwolone źródłowe adresy IP**— zakresy adresów IP, które są dozwolone na tym porcie, gdy żądanie zostanie zatwierdzone.
            - **Maksymalny czas żądania**— maksymalny przedział czasu, w którym można otworzyć określony port.

     1. Kliknij przycisk **OK**.

1. Kliknij pozycję **Zapisz**.

> [!NOTE]
>Gdy dla maszyny wirtualnej jest włączony dostęp JIT dla maszyny wirtualnej, Azure Security Center tworzy reguły "Odmów wszystkim ruchem przychodzącym" dla wybranych portów w grupach zabezpieczeń sieci skojarzonych i z tą zaporą platformy Azure. Jeśli zostały utworzone inne reguły dla wybranych portów, istniejące reguły mają pierwszeństwo przed nowym regułą "odmowa całego ruchu przychodzącego". Jeśli nie ma żadnych istniejących reguł na wybranych portach, nowy reguły "Odrzuć cały ruch przychodzący" mają najwyższy priorytet w grupach zabezpieczeń sieci i zaporze platformy Azure.


## <a name="request-jit-access-via-security-center"></a>Zażądaj dostępu JIT za pośrednictwem Security Center

Aby zażądać dostępu do maszyny wirtualnej za pośrednictwem Security Center:

1. W obszarze **dostęp do maszyny wirtualnej just-in-Time**wybierz kartę **skonfigurowane** .

1. W obszarze **maszyna wirtualna**kliknij maszyny wirtualne, dla których chcesz uzyskać dostęp. Spowoduje to umieszczenie znacznika wyboru obok maszyny wirtualnej.

    - Ikona w kolumnie **szczegóły połączenia** wskazuje, czy JIT jest włączona w sieciowej grupy zabezpieczeń czy PD. Jeśli ta opcja jest włączona, zostanie wyświetlona tylko ikona zapory.

    - Kolumna **szczegóły połączenia** zawiera informacje wymagane do nawiązania połączenia z maszyną wirtualną oraz otwarte porty.

      ![Żądanie dostępu just in time](./media/security-center-just-in-time/request-just-in-time-access.png)

1. Kliknij pozycję **Żądaj dostępu**. Zostanie otwarte okno **żądania dostępu** .

      ![Szczegóły JIT](./media/security-center-just-in-time/just-in-time-details.png)

1. W obszarze **Żądaj dostępu**dla każdej maszyny wirtualnej Skonfiguruj porty, które chcesz otworzyć, oraz źródłowe adresy IP, na których jest otwierany port, oraz przedział czasu, w którym port zostanie otwarty. Możliwe będzie tylko żądanie dostępu do portów skonfigurowanych w zasadach just in Time. Każdy port ma maksymalny dozwolony czas uzyskany na podstawie zasad just in Time.

1. Kliknij pozycję **Otwórz porty**.

> [!NOTE]
> Jeśli użytkownik, który żąda dostępu, znajduje się za serwerem proxy, opcja **My IP** może nie zadziałać. Może być konieczne zdefiniowanie pełnego zakresu adresów IP organizacji.



## <a name="edit-a-jit-access-policy-via-security-center"></a>Edytowanie zasad dostępu JIT za pośrednictwem Security Center

Istniejące zasady just in Time maszyny wirtualnej można zmienić, dodając i konfigurując nowy port do ochrony dla tej maszyny wirtualnej albo zmieniając inne ustawienia związane z już chronionym portem.

Aby edytować istniejące zasady just in Time maszyny wirtualnej:

1. Na karcie **Konfiguracja** w obszarze **maszyny wirtualne**wybierz maszynę wirtualną, do której chcesz dodać port, klikając trzy kropki w wierszu dla tej maszyny wirtualnej. 

1. Wybierz pozycję **Edytuj**.

1. W obszarze **Konfiguracja dostępu JIT do maszyny wirtualnej**można edytować istniejące ustawienia już chronionego portu lub dodać nowy port niestandardowy. 
  ![dostęp JIT do maszyny wirtualnej](./media/security-center-just-in-time/edit-policy.png)



## <a name="audit-jit-access-activity-in-security-center"></a>Inspekcja aktywności dostępu JIT w Security Center

Szczegółowe informacje na temat działań maszyn wirtualnych można uzyskać przy użyciu funkcji przeszukiwania dzienników. Aby wyświetlić dzienniki:

1. W obszarze **dostęp do maszyny wirtualnej just-in-Time**wybierz kartę **skonfigurowane** .
2. W obszarze **maszyny wirtualne**wybierz maszynę wirtualną, aby wyświetlić informacje, klikając trzy kropki w wierszu dla tej maszyny wirtualnej, a następnie wybierz pozycję **Dziennik aktywności** z menu. Zostanie otwarty **Dziennik aktywności** .

   ![Wybierz Dziennik aktywności](./media/security-center-just-in-time/select-activity-log.png)

   **Dziennik aktywności** zawiera filtrowany widok poprzednich operacji dla tej maszyny wirtualnej oraz godzinę, datę i subskrypcję.

Informacje dziennika można pobrać, wybierając **pozycję kliknij tutaj, aby pobrać wszystkie elementy jako wolumin CSV**.

Zmodyfikuj filtry i kliknij przycisk **Zastosuj** , aby utworzyć wyszukiwanie i dziennik.



## <a name="configure-jit-access-from-an-azure-vms-page"></a>Konfigurowanie dostępu JIT ze strony maszyny wirtualnej platformy Azure<a name="jit-vm"></a>

Dla wygody można nawiązać połączenie z maszyną wirtualną przy użyciu JIT bezpośrednio z poziomu strony maszyny wirtualnej w Security Center.

### <a name="configure-jit-access-on-a-vm-via-the-azure-vm-page"></a>Konfigurowanie dostępu JIT na maszynie wirtualnej za pośrednictwem strony maszyny wirtualnej platformy Azure

Aby ułatwić uzyskiwanie dostępu just in Time do maszyn wirtualnych, można ustawić maszynę wirtualną tak, aby zezwalała na dostęp tylko just in Time bezpośrednio z poziomu maszyny wirtualnej.

1. W [Azure Portal](https://ms.portal.azure.com)Wyszukaj i wybierz pozycję **maszyny wirtualne**. 
2. Wybierz maszynę wirtualną, którą chcesz ograniczyć do dostępu just in Time.
3. W menu wybierz pozycję **Konfiguracja**.
4. W obszarze **dostęp just in Time**wybierz pozycję **Włącz just in Time**. 

Pozwala to na dostęp just in Time do maszyny wirtualnej przy użyciu następujących ustawień:

- Serwery z systemem Windows:
    - Port RDP 3389
    - Trzy godziny maksymalnego dozwolonego dostępu
    - Dozwolone źródłowe adresy IP są ustawione na wartość any
- Serwery z systemem Linux:
    - Port SSH 22
    - Trzy godziny maksymalnego dozwolonego dostępu
    - Dozwolone źródłowe adresy IP są ustawione na wartość any
     
Jeśli maszyna wirtualna ma już włączoną funkcję just-in-Time, po przejściu na stronę konfiguracji będzie można zobaczyć, że jest włączona funkcja just in Time. można także użyć linku, aby otworzyć zasady w Azure Security Center, aby wyświetlić i zmienić ustawienia.

![Konfiguracja JIT w maszynie wirtualnej](./media/security-center-just-in-time/jit-vm-config.png)

### <a name="request-jit-access-to-a-vm-via-an-azure-vms-page"></a>Zażądaj dostępu JIT do maszyny wirtualnej za pośrednictwem strony maszyny wirtualnej platformy Azure

W Azure Portal podczas próby nawiązania połączenia z maszyną wirtualną platforma Azure sprawdzi, czy dla tej maszyny wirtualnej skonfigurowano zasady dostępu just in Time.

- Jeśli na maszynie wirtualnej skonfigurowano zasady JIT, można kliknąć pozycję **Żądaj dostępu** , aby udzielić dostępu zgodnie z zasadami JIT ustawionymi dla maszyny wirtualnej. 

  >![żądanie JIT](./media/security-center-just-in-time/jit-request.png)

  Zażądano dostępu z następującymi domyślnymi parametrami:

  - **źródłowy adres IP**: "any" (*) (nie można zmienić)
  - **zakres czasu**: trzy godziny (nie można zmienić) <!--Isn't this set in the policy-->
  - **numer portu** Port RDP 3389 dla Windows/port 22 dla systemu Linux (można go zmienić)

    > [!NOTE]
    > Po zatwierdzeniu żądania dla maszyny wirtualnej chronionej przez zaporę platformy Azure Security Center udostępnia użytkownikowi odpowiednie szczegóły połączenia (mapowanie portów z tabeli DNAT), które ma być używane do nawiązywania połączenia z maszyną wirtualną.

- Jeśli nie skonfigurowano JIT na maszynie wirtualnej, zostanie wyświetlony monit o skonfigurowanie zasad JIT.

  ![monit JIT](./media/security-center-just-in-time/jit-prompt.png)

## <a name="configure-a-jit-policy-on-a-vm-programmatically"></a>Programowe Konfigurowanie zasad JIT na maszynie wirtualnej<a name="jit-program"></a>

Można skonfigurować i używać just-in-Time za pośrednictwem interfejsów API REST i programu PowerShell.

### <a name="jit-vm-access-via-rest-apis"></a>Dostęp JIT do maszyny wirtualnej za pośrednictwem interfejsów API REST

Funkcja dostępu just in Time do maszyny wirtualnej może być używana za pośrednictwem interfejsu API Azure Security Center. Możesz uzyskać informacje na temat skonfigurowanych maszyn wirtualnych, dodać nowe, zażądać dostępu do maszyny wirtualnej, a nie tylko za pośrednictwem tego interfejsu API. Zobacz [zasady dostępu do sieci JIT](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies), aby dowiedzieć się więcej na temat interfejsu API REST just-in-Time.

### <a name="jit-vm-access-via-powershell"></a>Dostęp JIT do maszyny wirtualnej za pośrednictwem programu PowerShell

Aby korzystać z rozwiązania dostępu just in Time do maszyny wirtualnej za pośrednictwem programu PowerShell, należy użyć oficjalnych poleceń cmdlet programu PowerShell Azure Security Center i w ten sposób `Set-AzJitNetworkAccessPolicy` .

Poniższy przykład ustawia zasady dostępu just-in-Time dla określonej maszyny wirtualnej i ustawia następujące elementy:

1.    Zamknij porty 22 i 3389.

2.    Dla każdego z nich ustaw maksymalne przedziały czasu (3 godziny), tak aby mogły być otwierane zgodnie z zatwierdzonymi żądaniami.
3.    Zezwala użytkownikowi, który żąda dostępu do kontrolowania źródłowych adresów IP i umożliwia użytkownikowi ustanowienie pomyślnej sesji na podstawie zatwierdzonego żądania dostępu just in Time.

Uruchom następujące polecenia w programie PowerShell, aby to zrobić:

1.    Przypisz zmienną, która zawiera zasady dostępu just in Time do maszyny wirtualnej:

        $JitPolicy = (@ {ID = "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME";   porty = (@ {Number = 22;        Protokół = " \* ";        allowedSourceAddressPrefix = @ (" \* ");        maxRequestAccessDuration = "PT3H"}, @ {Number = 3389;        Protokół = " \* ";        allowedSourceAddressPrefix = @ (" \* ");        maxRequestAccessDuration = "PT3H"})

2.    Wstaw zasady dostępu just in Time do maszyny wirtualnej do tablicy:
    
        $JitPolicyArr = @ ($JitPolicy)

3.    Skonfiguruj zasady dostępu just in Time do maszyny wirtualnej na wybranej maszynie wirtualnej:
    
        Set-AzJitNetworkAccessPolicy-Kind "podstawowa" — "lokalizacja" — nazwa "domyślna"-ResourceGroupName "resourceName"-VirtualMachine $JitPolicyArr 

### <a name="request-access-to-a-vm-via-powershell"></a>Żądanie dostępu do maszyny wirtualnej za pośrednictwem programu PowerShell

W poniższym przykładzie można zobaczyć żądanie dostępu just in Time do określonej maszyny wirtualnej, w której zażądano otwarcia portu 22 dla określonego adresu IP i przez określony czas:

Uruchom następujące polecenia w programie PowerShell:
1.    Konfigurowanie właściwości dostępu żądania maszyny wirtualnej

        $JitPolicyVm 1 = (@ {ID = "/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME";   porty = (@ {Number = 22;      endTimeUtc = "2018 r-09-17T17:00:00.3658798 Z";      allowedSourceAddressPrefix = @ ("IPV4ADDRESS")})})
2.    Wstawianie parametrów żądania dostępu do maszyny wirtualnej w tablicy:

        $JitPolicyArr = @ ($JitPolicyVm 1)
3.    Wyślij żądanie dostępu (Użyj identyfikatora zasobu, który został uzyskany w kroku 1)

        Start-AzJitNetworkAccessPolicy-ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default"-VirtualMachine $JitPolicyArr

Aby uzyskać więcej informacji, zobacz [dokumentację poleceń cmdlet programu PowerShell](https://docs.microsoft.com/powershell/scripting/developer/cmdlet/cmdlet-overview).


## <a name="automatic-cleanup-of-redundant-jit-rules"></a>Automatyczne czyszczenie nadmiarowych reguł JIT 

Za każdym razem, gdy aktualizujesz zasady JIT, narzędzie do czyszczenia zostanie automatycznie uruchomione, aby sprawdzić poprawność całego zestawu reguł. Narzędzie szuka niezgodności między regułami w zasadach i regułami w sieciowej grupy zabezpieczeń. Jeśli narzędzie do czyszczenia znajdzie niezgodność, określa przyczynę i, gdy jest to bezpieczne, usuwa wbudowane reguły, które nie są jeszcze potrzebne. Oczyszczarka nigdy nie usuwa reguł, które zostały utworzone.

Przykłady scenariuszy, w których odkurzacz może usunąć regułę wbudowaną:

- Gdy istnieją dwie reguły z identycznymi definicjami, a jeden ma wyższy priorytet niż drugi (znaczenie, reguła o niższym priorytecie nigdy nie będzie używana)
- Gdy opis reguły zawiera nazwę maszyny wirtualnej, która nie jest zgodna z docelowym adresem IP w regule 


## <a name="next-steps"></a>Następne kroki

W tym artykule wyjaśniono, jak dostęp just in Time do maszyny wirtualnej w Security Center pomaga kontrolować dostęp do maszyn wirtualnych platformy Azure.

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

- Moduł Microsoft Learn [chroni serwery i maszyny wirtualne przed atakami typu "z wymuszeniem i złośliwym oprogramowaniem" przy użyciu Azure Security Center](https://docs.microsoft.com/learn/modules/secure-vms-with-azure-security-center/)
- [Ustawianie zasad zabezpieczeń](tutorial-security-policy.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów platformy Azure.
- [Zarządzanie zaleceniami](security-center-recommendations.md) dotyczącymi zabezpieczeń — Dowiedz się, jak zalecenia ułatwiają ochronę zasobów platformy Azure.
- [Monitorowanie kondycji zabezpieczeń](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
