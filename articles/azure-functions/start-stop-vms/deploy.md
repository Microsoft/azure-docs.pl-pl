---
title: Wdrażanie uruchamiania/zatrzymywania maszyn wirtualnych v2 (wersja zapoznawcza)
description: W tym artykule opisano sposób wdrażania funkcji uruchamiania/zatrzymywania maszyn wirtualnych v2 (wersja zapoznawcza) dla maszyn wirtualnych platformy Azure w ramach subskrypcji platformy Azure.
services: azure-functions
ms.subservice: ''
ms.date: 03/29/2021
ms.topic: conceptual
ms.openlocfilehash: 9ca808fffbd26c8837ad9a43447f60e99f89d922
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111981"
---
# <a name="deploy-startstop-vms-v2-preview"></a>Wdrażanie uruchamiania/zatrzymywania maszyn wirtualnych v2 (wersja zapoznawcza)

Wykonaj kroki opisane w tym temacie w sekwencji, aby zainstalować funkcję uruchamiania/zatrzymywania maszyn wirtualnych v2 (wersja zapoznawcza). Po zakończeniu procesu instalacji Skonfiguruj harmonogramy, aby dostosować je do swoich wymagań.

## <a name="deploy-feature"></a>Wdróż funkcję

Wdrożenie jest inicjowane z poziomu witryny uruchamiania/zatrzymywania maszyn wirtualnych w serwisie GitHub w wersji 2 [tutaj](https://github.com/microsoft/startstopv2-deployments/blob/main/README.md). Chociaż ta funkcja jest przeznaczona do zarządzania wszystkimi maszynami wirtualnymi w ramach subskrypcji we wszystkich grupach zasobów z jednego wdrożenia w ramach subskrypcji, można zainstalować inne wystąpienie w oparciu o model operacji lub wymagania organizacji. Można również skonfigurować centralne zarządzanie maszynami wirtualnymi w wielu subskrypcjach.

Aby uprościć zarządzanie i usuwanie, zalecamy wdrożenie uruchamiania/zatrzymywania maszyn wirtualnych w wersji 2 (wersja zapoznawcza) w dedykowanej grupie zasobów.

> [!NOTE]
> Obecnie ta wersja zapoznawcza nie obsługuje określania istniejącego konta magazynu lub zasobu Application Insights.

1. Otwórz przeglądarkę i przejdź do organizacji Start/Stop VM w serwisie [GitHub](https://github.com/microsoft/startstopv2-deployments/blob/main/README.md).
1. Wybierz opcję wdrażania w oparciu o środowisko chmury platformy Azure, w którym są tworzone maszyny wirtualne platformy Azure. Spowoduje to otwarcie strony niestandardowe wdrożenie Azure Resource Manager w Azure Portal.
1. Jeśli zostanie wyświetlony monit, zaloguj się do [Azure Portal](https://portal.azure.com).
1. Podaj następujące wartości:

    |Nazwa |Wartość |
    |-----|------|
    |Region (Region) |Wybierz region znajdujący się w najbliższym obszarze dla nowych zasobów.|
    |Nazwa grupy zasobów |Określ nazwę grupy zasobów, która będzie zawierać poszczególne zasoby dotyczące uruchamiania/zatrzymywania maszyn wirtualnych. |
    |Region grupy zasobów |Określ region dla grupy zasobów. Na przykład **Środkowe stany USA**.|
    |Nazwa aplikacja funkcji platformy Azure |Wpisz nazwę, która jest prawidłowa w ścieżce URL. Wpisana nazwa zostanie sprawdzona, aby upewnić się, że jest ona unikatowa w Azure Functions. |
    |Nazwa Application Insights |Określ nazwę wystąpienia Application Insights, w którym będą przechowywane maszyny wirtualne do uruchamiania/zatrzymywania maszyn wirtualnych. |
    |Region Application Insights |Określ region dla wystąpienia Application Insights.|
    |Nazwa konta magazynu |Określ nazwę konta usługi Azure Storage do przechowywania danych telemetrycznych wykonywania uruchamiania/zatrzymywania maszyn wirtualnych. |
    |Adres e-mail |Określ co najmniej jeden adres e-mail, aby otrzymywać powiadomienia o stanie, oddzielone przecinkami (,).|

    :::image type="content" source="media/deploy/deployment-template-details.png" alt-text="Uruchom/Zatrzymaj konfigurację wdrożenia szablonu maszyn wirtualnych":::

1. Wybierz pozycję **Przegląd + Utwórz** w dolnej części strony.
1. Wybierz pozycję **Utwórz** , aby rozpocząć wdrażanie.
1. Wybierz ikonę dzwonka (powiadomienia) w górnej części ekranu, aby wyświetlić stan wdrożenia. Powinien zostać wyświetlony komunikat **Wdrożenie jest w toku**. Poczekaj na zakończenie wdrożenia.
1. W okienku powiadomień wybierz pozycję **Przejdź do grupy zasobów**. Zobaczysz ekran podobny do poniższego:

    :::image type="content" source="media/deploy/deployment-results-resource-list.png" alt-text="Lista zasobów wdrażania uruchamiania/zatrzymywania maszyn wirtualnych":::

## <a name="enable-multiple-subscriptions"></a>Włącz wiele subskrypcji

Po zakończeniu wdrażania uruchamiania/zatrzymywania wykonaj następujące kroki, aby włączyć Uruchamianie/zatrzymywanie maszyn wirtualnych v2 (wersja zapoznawcza) w celu podjęcia akcji dla wielu subskrypcji.

1. Skopiuj wartość dla nazwy aplikacja funkcji platformy Azure, która została określona podczas wdrażania.

1. W portalu przejdź do subskrypcji pomocniczej. Wybierz subskrypcję, a następnie wybierz pozycję **Access Control (IAM)**

1. Wybierz pozycję **Dodaj** , a następnie wybierz pozycję **Dodaj przypisanie roli**.

1. Wybierz rolę **współautor** z listy rozwijanej **rola** .

1. Wprowadź nazwę aplikacji funkcji platformy Azure w polu **Wybierz** . W wynikach wybierz nazwę funkcji.

1. Wybierz pozycję **Zapisz** , aby zatwierdzić zmiany.

## <a name="configure-schedules-overview"></a>Omówienie konfigurowania harmonogramów

Aby zarządzać metodą automatyzacji w celu kontrolowania uruchamiania i zatrzymywania maszyn wirtualnych, należy skonfigurować co najmniej jedną dołączoną aplikację logiki zgodnie z wymaganiami.

- Akcje zaplanowanego uruchomienia i zatrzymania są oparte na określonym harmonogramie dla Azure Resource Manager i klasycznych maszyn wirtualnych.**ststv2_vms_Scheduled_start** i **ststv2_vms_Scheduled_stop** Skonfiguruj zaplanowane uruchamianie i zatrzymywanie.

- Sekwencyjne — akcje uruchamiania i zatrzymywania są oparte na maszynach wirtualnych dotyczących harmonogramu ze wstępnie zdefiniowanymi tagami sekwencjonowania. Obsługiwane są tylko dwa nazwane Tagi — **sequencestart** i **sequencestop**. **ststv2_vms_Sequenced_start** i **ststv2_vms_Sequenced_stop** skonfigurować sekwencję Uruchom i Zatrzymaj.

    > [!NOTE]
    > W tym scenariuszu obsługiwane są tylko Azure Resource Manager maszyny wirtualne.

- Autozatrzymaj — ta funkcja jest używana tylko do wykonywania akcji zatrzymania dla Azure Resource Manager i klasycznych maszyn wirtualnych na podstawie użycia procesora CPU. Może to być również *Akcja podejmowana* zgodnie z harmonogramem, która tworzy alerty na maszynach wirtualnych i w oparciu o warunek, alert jest wyzwalany w celu wykonania akcji zatrzymania.**ststv2_vms_AutoStop** konfiguruje funkcję zatrzymywania.

Jeśli potrzebujesz dodatkowych harmonogramów, możesz zduplikować jeden z Logic Apps udostępnianych przy użyciu opcji **klonowania** w Azure Portal.

:::image type="content" source="media/deploy/logic-apps-clone-option.png" alt-text="Wybierz opcję klonowania, aby zduplikować aplikację logiki":::

## <a name="scheduled-start-and-stop-scenario"></a>Zaplanowany scenariusz uruchamiania i zatrzymywania

Wykonaj następujące kroki, aby skonfigurować akcję zaplanowanego uruchamiania i zatrzymywania dla Azure Resource Manager i klasycznych maszyn wirtualnych. Na przykład możesz skonfigurować harmonogram **ststv2_vms_Scheduled_start** , aby uruchamiać je w biurze, gdy jesteś w pakiecie Office, i zatrzymać wszystkie maszyny wirtualne w ramach subskrypcji, gdy opuścisz pracę w wieczorie na podstawie harmonogramu **ststv2_vms_Scheduled_stop** .

Konfigurowanie aplikacji logiki do uruchamiania maszyn wirtualnych jest obsługiwane.

Dla każdego scenariusza można wykonać akcję dla jednej lub wielu subskrypcji, jedną lub wiele grup zasobów i określić co najmniej jedną maszynę wirtualną na liście dołączania lub wykluczania. Nie można określać ich razem w tej samej aplikacji logiki.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) a następnie przejdź do **aplikacji Logic Apps**.

1. Z listy aplikacji logiki, aby skonfigurować zaplanowane rozpoczęcie, wybierz **ststv2_vms_Scheduled_start**. Aby skonfigurować zaplanowaną zatrzymanie, wybierz pozycję **ststv2_vms_Scheduled_stop**.

1. Wybierz pozycję **Projektant aplikacji logiki** z okienka po lewej stronie.

1. Gdy zostanie wyświetlony projektant aplikacji logiki, w okienku Projektant wybierz pozycję **cykl** , aby skonfigurować harmonogram aplikacji logiki. Aby dowiedzieć się więcej o określonych opcjach cyklu, zobacz [Planowanie cykliczne zadania](../../connectors/connectors-native-recurrence.md#add-the-recurrence-trigger).

    :::image type="content" source="media/deploy/schedule-recurrence-property.png" alt-text="Konfigurowanie częstotliwości cyklu dla aplikacji logiki":::

1. W okienku projektanta wybierz pozycję **Funkcja — spróbuj** skonfigurować ustawienia docelowe. Jeśli chcesz zarządzać maszynami wirtualnymi we wszystkich grupach zasobów w subskrypcji, w treści żądania należy zmodyfikować treść żądania, jak pokazano w poniższym przykładzie.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/"
        ]
     }
    }
    ```

    Określ wiele subskrypcji w `subscriptions` tablicy z każdą wartością rozdzieloną przecinkami, tak jak w poniższym przykładzie.

    ```json
    "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/"
        ]
    ```

    Jeśli chcesz zarządzać maszynami wirtualnymi dla określonych grup zasobów, w treści żądania zmodyfikuj treść żądania, jak pokazano w poniższym przykładzie. Każda określona ścieżka zasobu musi być oddzielona przecinkiem. W razie potrzeby można określić jedną grupę zasobów lub więcej.

    Ten przykład ilustruje także wyłączenie maszyny wirtualnej. Maszynę wirtualną można wykluczyć, określając ścieżkę zasobów maszyn wirtualnych lub symbol wieloznaczny.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ResourceGroups": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/"
        ],
        "ExcludedVMLists": [
         "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/providers/Microsoft.Compute/virtualMachines/vm1"
        ]
      }
    }
    ```

    W tym miejscu akcja zostanie wykonana na wszystkich maszynach wirtualnych, z wyjątkiem nazwy maszyny wirtualnej, rozpoczyna się od AZ i BZ w obu subskrypcjach.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ExcludedVMLists": [“Az*”,“Bz*”],
       "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/"
    
        ]
      }
    }
    ```

    Jeśli chcesz zarządzać określonym zestawem maszyn wirtualnych w ramach subskrypcji, w treści żądania zmodyfikuj treść żądania, jak pokazano w poniższym przykładzie. Każda określona ścieżka zasobu musi być oddzielona przecinkiem. W razie potrzeby możesz określić jedną maszynę wirtualną.

    ```json
    {
      "Action": "start",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "VMLists": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachines/vm1",
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg3/providers/Microsoft.Compute/virtualMachines/vm2",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/providers/Microsoft.ClassicCompute/virtualMachines/vm30"
          
        ]
    }
    ```

## <a name="sequenced-start-and-stop-scenario"></a>Scenariusz uruchamiania i zatrzymywania sekwencyjnego

W środowisku zawierającym co najmniej dwa składniki na wielu maszynach wirtualnych Azure Resource Manager w architekturze aplikacji rozproszonej, obsługa sekwencji, w której składniki zostały uruchomione i zatrzymane, jest ważna.

1. Z listy aplikacji logiki, aby skonfigurować Start sekwencyjny, wybierz pozycję **ststv2_vms_Sequenced_start**. Aby skonfigurować zatrzymywanie sekwencji, wybierz pozycję **ststv2_vms_Sequenced_stop**.

1. Wybierz pozycję **Projektant aplikacji logiki** z okienka po lewej stronie.

1. Gdy zostanie wyświetlony projektant aplikacji logiki, w okienku Projektant wybierz pozycję **cykl** , aby skonfigurować harmonogram aplikacji logiki. Aby dowiedzieć się więcej o określonych opcjach cyklu, zobacz [Planowanie cykliczne zadania](../../connectors/connectors-native-recurrence.md#add-the-recurrence-trigger).

    :::image type="content" source="media/deploy/schedule-recurrence-property.png" alt-text="Konfigurowanie częstotliwości cyklu dla aplikacji logiki":::

1. W okienku projektanta wybierz pozycję **Funkcja — spróbuj** skonfigurować ustawienia docelowe. Jeśli chcesz zarządzać maszynami wirtualnymi we wszystkich grupach zasobów w subskrypcji, w treści żądania należy zmodyfikować treść żądania, jak pokazano w poniższym przykładzie.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/"
        ]
     },
       "Sequenced": true
    }
    ```

    Określ wiele subskrypcji w `subscriptions` tablicy z każdą wartością rozdzieloną przecinkami, tak jak w poniższym przykładzie.

    ```json
    "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/"
        ]
    ```

    Jeśli chcesz zarządzać maszynami wirtualnymi dla określonych grup zasobów, w treści żądania zmodyfikuj treść żądania, jak pokazano w poniższym przykładzie. Każda określona ścieżka zasobu musi być oddzielona przecinkiem. W razie potrzeby można określić jedną grupę zasobów.

    Ten przykład ilustruje również wykluczenie maszyny wirtualnej za pomocą jej ścieżki zasobów w porównaniu z przykładem dla zaplanowanego uruchomienia/zatrzymania, który używa symboli wieloznacznych.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ResourceGroups": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/"
        ],
        "ExcludedVMLists": [
         "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/providers/Microsoft.Compute/virtualMachines/vm1"
        ]
      },
       "Sequenced": true
    }
    ```

    W treści żądania, jeśli chcesz zarządzać określonym zestawem maszyn wirtualnych w ramach subskrypcji, zmodyfikuj treść żądania, jak pokazano w poniższym przykładzie. Każda określona ścieżka zasobu musi być oddzielona przecinkiem. W razie potrzeby możesz określić jedną maszynę wirtualną.

    ```json
    {
      "Action": "start",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "VMLists": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachines/vm1",
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg2/providers/Microsoft.ClassicCompute/virtualMachines/vm2",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/providers/Microsoft.ClassicCompute/virtualMachines/vm30"
        ]
      },
       "Sequenced": true
    }
    ```

## <a name="auto-stop-scenario"></a>Scenariusz zatrzymywania

Uruchamianie/zatrzymywanie maszyn wirtualnych w wersji 2 (wersja zapoznawcza) może pomóc w zarządzaniu kosztami uruchamiania Azure Resource Manager i klasycznych maszyn wirtualnych w ramach subskrypcji przez ocenę maszyn, które nie są używane w okresach poza szczytem, na przykład po godzinach, i ich automatyczne zamknięcie w przypadku, gdy użycie procesora jest mniejsze niż określona wartość procentowa.

Następujące właściwości alertu metryki w treści żądania obsługują Dostosowywanie:

- AutoStop_MetricName
- AutoStop_Condition
- AutoStop_Threshold
- AutoStop_Description
- AutoStop_Frequency
- AutoStop_Severity
- AutoStop_Threshold
- AutoStop_TimeAggregationOperator
- AutoStop_TimeWindow

Aby dowiedzieć się więcej o tym, jak Azure Monitor alerty metryk działają i jak je skonfigurować, zobacz [alerty metryk w Azure monitor](../../azure-monitor/alerts/alerts-metric-overview.md).

1. Z listy aplikacji logiki, aby skonfigurować autozatrzymanie, wybierz pozycję **ststv2_vms_AutoStop**.

1. Wybierz pozycję **Projektant aplikacji logiki** z okienka po lewej stronie.

1. Gdy zostanie wyświetlony projektant aplikacji logiki, w okienku Projektant wybierz pozycję **cykl** , aby skonfigurować harmonogram aplikacji logiki. Aby dowiedzieć się więcej o określonych opcjach cyklu, zobacz [Planowanie cykliczne zadania](../../connectors/connectors-native-recurrence.md#add-the-recurrence-trigger).

    :::image type="content" source="media/deploy/schedule-recurrence-property.png" alt-text="Konfigurowanie częstotliwości cyklu dla aplikacji logiki":::

1. W okienku projektanta wybierz pozycję **Funkcja — spróbuj** skonfigurować ustawienia docelowe. Jeśli chcesz zarządzać maszynami wirtualnymi we wszystkich grupach zasobów w subskrypcji, w treści żądania należy zmodyfikować treść żądania, jak pokazano w poniższym przykładzie.

    ```json
    {
      "Action": "stop",
      "EnableClassic": false,    
      "AutoStop_MetricName": "Percentage CPU",
      "AutoStop_Condition": "LessThan",
      "AutoStop_Description": "Alert to stop the VM if the CPU % exceed the threshold",
      "AutoStop_Frequency": "00:05:00",
      "AutoStop_Severity": "2",
      "AutoStop_Threshold": "5",
      "AutoStop_TimeAggregationOperator": "Average",
      "AutoStop_TimeWindow": "06:00:00",
      "RequestScopes":{        
        "Subscriptions":[
            "/subscriptions/12345678-1111-2222-3333-1234567891234/",
            "/subscriptions/12345678-2222-4444-5555-1234567891234/"
        ],
        "ExcludedVMLists":[]
      }        
    }
    ```

    Jeśli chcesz zarządzać maszynami wirtualnymi dla określonych grup zasobów, w treści żądania zmodyfikuj treść żądania, jak pokazano w poniższym przykładzie. Każda określona ścieżka zasobu musi być oddzielona przecinkiem. W razie potrzeby można określić jedną grupę zasobów.

    ```json
    {
      "Action": "stop",
      "AutoStop_Condition": "LessThan",
      "AutoStop_Description": "Alert to stop the VM if the CPU % exceed the threshold",
      "AutoStop_Frequency": "00:05:00",
      "AutoStop_MetricName": "Percentage CPU",
      "AutoStop_Severity": "2",
      "AutoStop_Threshold": "5",
      "AutoStop_TimeAggregationOperator": "Average",
      "AutoStop_TimeWindow": "06:00:00",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "ResourceGroups": [
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/",
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroupsvmrg2/",
          "/subscriptions/12345678-2222-4444-5555-1234567891234/resourceGroups/VMHostingRG/"
          ]
      }
    }
    ```

    Jeśli chcesz zarządzać określonym zestawem maszyn wirtualnych w ramach subskrypcji, w treści żądania zmodyfikuj treść żądania, jak pokazano w poniższym przykładzie. Każda określona ścieżka zasobu musi być oddzielona przecinkiem. W razie potrzeby możesz określić jedną maszynę wirtualną.

    ```json
    {
      "Action": "stop",
      "AutoStop_Condition": "LessThan",
      "AutoStop_Description": "Alert to stop the VM if the CPU % exceed the threshold",
      "AutoStop_Frequency": "00:05:00",
      "AutoStop_MetricName": "Percentage CPU",
      "AutoStop_Severity": "2",
      "AutoStop_Threshold": "5",
      "AutoStop_TimeAggregationOperator": "Average",
      "AutoStop_TimeWindow": "06:00:00",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "VMLists": [
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/rg3/providers/Microsoft.ClassicCompute/virtualMachines/Clasyvm11",
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/providers/Microsoft.Compute/virtualMachines/vm1"
        ]
      }
    }
    ```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak monitorować stan maszyn wirtualnych platformy Azure zarządzanych przez funkcję uruchamiania/zatrzymywania maszyn wirtualnych v2 (wersja zapoznawcza) i wykonywać inne zadania związane z zarządzaniem, zobacz artykuł [Zarządzanie maszynami wirtualnymi Start/Stop](manage.md) .