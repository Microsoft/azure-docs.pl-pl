---
title: Tworzenie i wyświetlanie alertów dziennika aktywności oraz zarządzanie nimi w Azure Monitor
description: Tworzenie alertów dziennika aktywności przy użyciu Azure Portal, szablonu Azure Resource Manager i Azure PowerShell.
ms.topic: conceptual
ms.subservice: alerts
ms.date: 06/25/2019
ms.openlocfilehash: bb4c1410d046389ae9e82986c6b0ed3d133fcf2a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101704467"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Tworzenie i wyświetlanie alertów dziennika aktywności oraz zarządzanie nimi za pomocą Azure Monitor  

## <a name="overview"></a>Omówienie

Alerty dziennika aktywności są alertami, które są aktywowane w przypadku wystąpienia nowego zdarzenia dziennika aktywności zgodnego z warunkami określonymi w alercie.

Te alerty dotyczą zasobów platformy Azure i można je utworzyć przy użyciu szablonu Azure Resource Manager. Można je także tworzyć, aktualizować lub usuwać w Azure Portal. Zazwyczaj tworzysz alerty dziennika aktywności, aby otrzymywać powiadomienia o wystąpieniu konkretnych zmian w zasobach w ramach subskrypcji platformy Azure. Alerty często są ograniczone do określonych grup zasobów lub zasobów. Na przykład możesz chcieć otrzymywać powiadomienia, gdy dowolna maszyna wirtualna w przykładowej grupie zasobów **myProductionResourceGroup** jest usuwana. Możesz również otrzymać powiadomienie, jeśli dowolna z nowych ról zostanie przypisana do użytkownika w ramach subskrypcji.

> [!IMPORTANT]
> Nie można utworzyć alertów dotyczących powiadomienia o kondycji usługi za pośrednictwem interfejsu dla tworzenia alertów dziennika aktywności. Aby dowiedzieć się więcej na temat tworzenia i używania powiadomień o kondycji usługi, zobacz [otrzymywanie alertów dziennika aktywności w powiadomieniach o kondycji usługi](../../service-health/alerts-activity-log-service-notifications-portal.md).

Podczas tworzenia reguł alertów należy zapewnić następujące czynności:

- Subskrypcja w zakresie nie różni się od subskrypcji, w której został utworzony alert.
- Kryteria muszą być kategoriami poziomów, stan, obiekt wywołujący, Grupa zasobów, identyfikator zasobu lub typ zasobu, dla których skonfigurowano alert.
- Dozwolony jest tylko jeden warunek "allOf".
- "AnyOf" może służyć do zezwalania na wiele warunków w wielu polach (na przykład, jeśli pola "status" lub "Substatus" są równe określonej wartości). Należy pamiętać, że użycie "AnyOf" jest obecnie ograniczone do tworzenia reguły alertu przy użyciu wdrożenia szablonu ARM.
- "ContainsAny" może służyć do zezwalania na wiele wartości tego samego pola (na przykład jeśli "operacja" jest równa "Delete" lub "Modify"). Należy pamiętać, że użycie "ContainsAny" jest obecnie ograniczone do tworzenia reguły alertu przy użyciu wdrożenia szablonu ARM.
- Jeśli kategoria ma wartość "administracyjne", musisz określić co najmniej jedno z powyższych kryteriów w alercie. Nie można utworzyć alertu, który jest uaktywniany za każdym razem, gdy zdarzenie jest tworzone w dziennikach aktywności.
- Nie można tworzyć alertów dla zdarzeń w kategorii alertów dziennika aktywności.

## <a name="azure-portal"></a>Azure Portal

Za pomocą Azure Portal można tworzyć i modyfikować reguły alertów dziennika aktywności. Środowisko jest zintegrowane z dziennikiem aktywności platformy Azure w celu zapewnienia bezproblemowego tworzenia alertów dla konkretnych interesujących Cię zdarzeń.

### <a name="create-with-the-azure-portal"></a>Utwórz za pomocą Azure Portal

Wykonaj poniższą procedurę.

1. W Azure Portal wybierz pozycję **Monitoruj**  >  **alerty**.
2. W lewym górnym rogu okna **alerty** wybierz pozycję **Nowa reguła alertu** .

     ![Nowa reguła alertu](media/alerts-activity-log/AlertsPreviewOption.png)

     Zostanie wyświetlone okno **Utwórz regułę** .

      ![Nowe opcje reguły alertu](media/alerts-activity-log/create-new-alert-rule-options.png)

3. W obszarze **Zdefiniuj warunek alertu** podaj następujące informacje, a następnie wybierz pozycję **gotowe**:

   - **Obiekt docelowy alertu:** Aby wyświetlić i wybrać cel dla nowego alertu, użyj **filtru Filtruj według subskrypcji**  /  **według typu zasobu**. Wybierz zasób lub grupę zasobów z wyświetlonej listy.

     > [!NOTE]
     > 
     > Dla sygnału dziennika aktywności można wybrać tylko [Azure Resource Manager](../../azure-resource-manager/management/overview.md) śledzony zasób, grupę zasobów lub całą subskrypcję. 

     **Przykładowy widok docelowy alertu**

     ![Wybieranie obiektu docelowego](media/alerts-activity-log/select-target.png)

   - W obszarze **kryteria docelowe** wybierz pozycję **Dodaj kryteria**. Wyświetlane są wszystkie dostępne sygnały dla elementu docelowego, które obejmują te z różnych kategorii **dziennika aktywności**. Nazwa kategorii jest dołączana do nazwy **usługi monitorowania** .

   - Wybierz sygnał z listy wyświetlanych różnych operacji dla typu **dziennika aktywności**.

     Można wybrać oś czasu historii dziennika oraz odpowiednią logikę alertów dla tego sygnału docelowego:

     **Ekran dodawania kryteriów**

     ![Dodaj kryteria](media/alerts-activity-log/add-criteria.png)
     
     > [!NOTE]
     > 
     >  Aby można było korzystać z wysokiej jakości i skutecznych reguł, prosimy o dodanie co najmniej jednego warunku do reguł ze sygnałem "wszystkie administracyjne". 
     > W ramach definicji alertu należy wypełnić jedną z listy rozwijanej: "poziom zdarzenia", "stan" lub "zainicjowane przez" i według tego, że reguła będzie bardziej konkretna.

     - **Czas historii**: zdarzenia dostępne dla wybranej operacji można wykreślić w ciągu ostatnich 6, 12 lub 24 godzin lub w ciągu ostatniego tygodnia.

     - **Logika alertu**:

       - **Poziom zdarzenia**: poziom ważności zdarzenia: _verbose_, _informacyjny_, _ostrzegawczy_, _Error_ lub _krytyczny_.
       - **Stan**: stan zdarzenia: _rozpoczęte_, _zakończone niepowodzeniem_ lub _zakończone powodzeniem_.
       - **Zdarzenie zainicjowane przez**: nazywane również obiektem wywołującym. Adres e-mail lub identyfikator Azure Active Directory użytkownika, który wykonał operację.

       Na tym grafie sygnału przykładu została zastosowana logika alertu:

       ![Wybrane kryteria](media/alerts-activity-log/criteria-selected.png)

4. W obszarze **Zdefiniuj szczegóły alertu** podaj następujące informacje:

    - **Nazwa reguły alertu**: Nazwa nowej reguły alertu.
    - **Opis**: Opis nowej reguły alertu.
    - **Zapisz alert w grupie zasobów**: Wybierz grupę zasobów, w której chcesz zapisać nową regułę.

5. W obszarze **Grupa akcji** z menu rozwijanego wybierz grupę akcji, która ma zostać przypisana do nowej reguły alertu. Lub [Utwórz nową grupę akcji](./action-groups.md) i przypisz ją do nowej reguły. Aby utworzyć nową grupę, wybierz pozycję **+ Nowa grupa**.

6. Aby włączyć reguły po ich utworzeniu, wybierz opcję **tak** dla opcji **Włącz regułę przy tworzeniu** .
7. Wybierz pozycję **Utwórz regułę alertu**.

    Zostanie utworzona Nowa reguła alertu dla dziennika aktywności, a w prawym górnym rogu okna pojawi się komunikat z potwierdzeniem.

    Możesz włączyć, wyłączyć, edytować lub usunąć regułę. Dowiedz się więcej o sposobach zarządzania zasadami dziennika aktywności.


Prostą analogową dla zrozumienie warunków, w których można tworzyć reguły alertów w dzienniku aktywności, jest Eksplorowanie lub filtrowanie zdarzeń za pośrednictwem [dziennika aktywności w Azure Portal](../essentials/activity-log.md#view-the-activity-log). Na ekranie **dziennika aktywności Azure monitor** można odfiltrować lub znaleźć niezbędne zdarzenie, a następnie utworzyć alert przy użyciu przycisku **Dodaj alert dziennika aktywności** . Następnie wykonaj kroki od 4 do 7, jak pokazano wcześniej.
    
 ![Dodawanie alertu z dziennika aktywności](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>Wyświetlanie w Azure Portal i zarządzanie nimi

1. W Azure Portal wybierz pozycję **Monitoruj**  >  **alerty**. Wybierz pozycję **Zarządzaj regułami alertów** w lewym górnym rogu okna.

    ![Zrzut ekranu przedstawia dziennik aktywności z wyróżnionym polem wyszukiwania.](media/alerts-activity-log/manage-alert-rules.png)

    Zostanie wyświetlona lista dostępnych reguł.

2. Wyszukaj regułę dziennika aktywności do zmodyfikowania.

    ![Wyszukaj reguły alertów dziennika aktywności](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    Aby znaleźć regułę działania, którą chcesz edytować, można użyć dostępnych filtrów, _subskrypcji_, _grupy zasobów_,  _zasobu_, _typu sygnału_ lub _stanu_.

   > [!NOTE]
   > 
   > Można edytować tylko **Opis**, **kryteria docelowe** i **grupy akcji**.

3. Wybierz regułę, a następnie kliknij ją dwukrotnie, aby edytować opcje reguły. Wprowadź wymagane zmiany, a następnie wybierz pozycję **Zapisz**.

   ![Zarządzaj regułami alertów](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. Możesz włączyć, wyłączyć lub usunąć regułę. Wybierz odpowiednią opcję w górnej części okna, po wybraniu reguły zgodnie z opisem w kroku 2.


## <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager
Aby utworzyć regułę alertu dziennika aktywności przy użyciu szablonu Azure Resource Manager, należy utworzyć zasób typu `microsoft.insights/activityLogAlerts` . Następnie Wypełnij wszystkie powiązane właściwości. Oto szablon, który tworzy regułę alertu dziennika aktywności:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```
Poprzedni przykładowy kod JSON można zapisać jako, na przykład sampleActivityLogAlert.jsna potrzeby tego przewodnika i można go wdrożyć przy użyciu [Azure Resource Manager w Azure Portal](../../azure-resource-manager/templates/deploy-portal.md).

  > [!NOTE]
  > 
  > Należy zauważyć, że można zdefiniować alerty dziennika aktywności najwyższego poziomu.
  > Oznacza to, że nie ma możliwości zdefiniowania alertu dla kilku subskrypcji, dlatego definicja powinna mieć wartość alert na subskrypcję.

Następujące pola są opcjami, których można użyć w szablonie Azure Resource Manager dla pól warunki: należy zauważyć, że "Resource Health", "Advisor" i "Service Health" mają dodatkowe pola właściwości dla ich pól specjalnych. 
1. resourceId: Identyfikator zasobu zasobu, którego dotyczy problem, w zdarzeniu dziennika aktywności, w którym ma zostać wygenerowany alert.
2. Kategoria: Kategoria w zdarzeniu dziennika aktywności. Na przykład: administracyjne, servicehealth, ResourceHealth, automatyczne skalowanie, zabezpieczenia, zalecenie, zasady.
3. Obiekt wywołujący: adres e-mail lub identyfikator Azure Active Directory użytkownika, który wykonał operację zdarzenia dziennika aktywności.
4. poziom: poziom aktywności w zdarzeniu dziennika aktywności, w którym ma zostać wygenerowany alert. Na przykład: krytyczne, błąd, ostrzeżenie, informacje, pełne.
5. OperationName: nazwa operacji w zdarzeniu dziennika aktywności. Na przykład: Microsoft. resources/Deployments/Write
6. Grupa zasobów: nazwa dla zasobu, którego dotyczy problem, w zdarzeniu dziennika aktywności.
7. resourceProvider: [dostawcy zasobów platformy Azure i typy wyjaśnień](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fresource-providers-and-types&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373543634&sdata=4RjpTkO5jsdOgPdt%2F%2FDOlYjIFE2%2B%2BuoHq5%2F7lHpCwQw%3D&reserved=0). Aby uzyskać listę, która mapuje dostawców zasobów na usługi platformy Azure, zobacz [dostawcy zasobów dla usług platformy Azure](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fazure-services-resource-providers&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373553639&sdata=0ZgJPK7BYuJsRifBKFytqphMOxMrkfkEwDqgVH1g8lw%3D&reserved=0).
8. status: ciąg opisujący stan operacji w zdarzeniu działania. Na przykład: rozpoczęte, w toku, zakończone powodzeniem, zakończone niepowodzeniem, aktywne, rozwiązane
9. Substatus: zazwyczaj kod stanu HTTP odpowiadającego wywołania REST, ale może również zawierać inne ciągi opisujące podstan.   Na przykład: OK (kod stanu HTTP: 200), utworzony (kod stanu HTTP: 201), zaakceptowany (kod stanu HTTP: 202), brak zawartości (kod stanu HTTP: 204), złe żądanie (kod stanu HTTP: 400), nie znaleziono (kod stanu HTTP: 404), konflikt (kod stanu http: 409), wewnętrzny błąd serwera (kod stanu http: 500), Usługa niedostępna (kod stanu HTTP: 503), limit czasu bramy (kod stanu HTTP: 504).
10. ResourceType: typ zasobu, którego dotyczy zdarzenie. Na przykład: Microsoft. resources/Deployments

Na przykład:

```json
"condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        }

```
Więcej szczegółów na temat pól dziennika aktywności można znaleźć [tutaj](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-monitor%2Fplatform%2Factivity-log-schema&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373563632&sdata=6QXLswwZgUHFXCuF%2FgOSowLzA8iOALVgvL3GMVhkYJY%3D&reserved=0).



> [!NOTE]
> Skonfigurowanie nowej reguły alertu dziennika aktywności może potrwać do 5 minut.

## <a name="rest-api"></a>Interfejs API REST 
[Interfejs API alertów dziennika aktywności Azure monitor](/rest/api/monitor/activitylogalerts) jest interfejsem API REST. Jest w pełni zgodna z interfejsem API REST Azure Resource Manager. Można go użyć za pośrednictwem programu PowerShell za pomocą polecenia cmdlet Menedżer zasobów lub interfejsu wiersza poleceń platformy Azure.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>Wdrażanie szablonu Menedżer zasobów przy użyciu programu PowerShell
Aby użyć programu PowerShell do wdrożenia przykładowego szablonu Menedżer zasobów pokazanego w poprzedniej sekcji [szablonu Azure Resource Manager](#azure-resource-manager-template) , użyj następującego polecenia:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

gdzie sampleActivityLogAlert.parameters.json zawiera wartości podane dla parametrów wymaganych do utworzenia reguły alertu.

### <a name="use-activity-log-powershell-cmdlets"></a>Korzystanie z poleceń cmdlet programu PowerShell dotyczących dzienników aktywności

Alerty dziennika aktywności mają dostępne dedykowane polecenia cmdlet programu PowerShell:

- [Set-AzActivityLogAlert](/powershell/module/az.monitor/set-azactivitylogalert): tworzy nowy Alert dziennika aktywności lub aktualizuje istniejący alert dziennika aktywności.
- [Get-AzActivityLogAlert](/powershell/module/az.monitor/get-azactivitylogalert): Pobiera co najmniej jeden zasób alertu dziennika aktywności.
- [Enable-AzActivityLogAlert](/powershell/module/az.monitor/enable-azactivitylogalert): włącza istniejący alert dziennika aktywności i ustawia jego Tagi.
- [Disable-AzActivityLogAlert](/powershell/module/az.monitor/disable-azactivitylogalert): wyłącza istniejący alert dziennika aktywności i ustawia jego Tagi.
- [Remove-AzActivityLogAlert](/powershell/module/az.monitor/remove-azactivitylogalert): usuwa alert dziennika aktywności.

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Dedykowane polecenia platformy Azure w obszarze Ustaw [AZ monitor Activity-Log alert](/cli/azure/monitor/activity-log/alert) są dostępne do zarządzania regułami alertów dziennika aktywności.

Aby utworzyć nową regułę alertu dziennika aktywności, użyj następujących poleceń w następującej kolejności:

1. [AZ monitor Activity-Log alert Create](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create): Utwórz nowy zasób reguły alertu dziennika aktywności.
1. [AZ monitor Activity-Log zakres alertu](/cli/azure/monitor/activity-log/alert/scope): Dodawanie zakresu dla utworzonej reguły alertu dziennika aktywności.
1. [AZ monitor Activity-Log Action Alert-Group](/cli/azure/monitor/activity-log/alert/action-group): Dodaj grupę akcji do reguły alertu dziennika aktywności.

Aby pobrać jeden zasób reguły alertu dziennika aktywności, użyj polecenia [AZ monitor Activity-Log alert](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
). Aby wyświetlić wszystkie zasoby reguły alertu dziennika aktywności w grupie zasobów, użyj polecenie [AZ monitor Activity-Log alert list](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list).
Zasoby reguły alertu dziennika aktywności można usunąć przy użyciu polecenia [AZ monitor Activity-Log alert Delete](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete).

## <a name="next-steps"></a>Następne kroki

- Informacje o [schemacie elementu webhook dla dzienników aktywności](./activity-log-alerts-webhook.md).
- Zapoznaj się z [omówieniem dzienników aktywności](./activity-log-alerts.md).
- Dowiedz się więcej na temat [grup akcji](./action-groups.md).  
- Dowiedz się więcej o [powiadomieniach o kondycji usługi](../../service-health/service-notifications.md).
