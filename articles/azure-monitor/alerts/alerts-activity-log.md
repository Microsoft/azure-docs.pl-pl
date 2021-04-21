---
title: Tworzenie i wyświetlanie alertów dziennika aktywności oraz zarządzanie nimi w Azure Monitor
description: Alerty dziennika aktywności można tworzyć przy użyciu Azure Portal, szablonu Azure Resource Manager i Azure PowerShell.
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 647378d7e93ab383441b363315a84cea8a5ab773
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772545"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Tworzenie i wyświetlanie alertów dziennika aktywności oraz zarządzanie nimi przy użyciu Azure Monitor  

## <a name="overview"></a>Omówienie

Alerty dziennika aktywności to alerty aktywowane w przypadku wystąpienia nowego zdarzenia dziennika aktywności, które odpowiada warunków określonych w alercie.

Te alerty są dla zasobów platformy Azure i można je tworzyć przy użyciu Azure Resource Manager szablonu. Można je również tworzyć, aktualizować lub usuwać w Azure Portal. Zazwyczaj tworzy się alerty dziennika aktywności w celu otrzymywania powiadomień w przypadku wystąpienia określonych zmian w zasobach w subskrypcji platformy Azure. Alerty są często ograniczony do określonych grup zasobów lub zasobów. Na przykład możesz chcieć być powiadamiany, gdy dowolna maszyna wirtualna w przykładowej grupie zasobów **myProductionResourceGroup** zostanie usunięta. Możesz też chcieć otrzymać powiadomienie, jeśli nowe role są przypisane do użytkownika w subskrypcji.

> [!IMPORTANT]
> Alertów dotyczących powiadomień dotyczących kondycji usługi nie można tworzyć za pośrednictwem interfejsu służącego do tworzenia alertów dziennika aktywności. Aby dowiedzieć się więcej na temat tworzenia i używania powiadomień dotyczących kondycji usługi, zobacz Receive activity log alerts on service health notifications (Otrzymywanie alertów dziennika aktywności [dotyczących powiadomień dotyczących kondycji usługi).](../../service-health/alerts-activity-log-service-notifications-portal.md)

Podczas tworzenia reguł alertów upewnij się, że:

- Subskrypcja w zakresie nie różni się od subskrypcji, w której utworzono alert.
- Kryteria muszą być następujące: poziom, stan, wywołujący, grupa zasobów, identyfikator zasobu lub kategoria zdarzenia typu zasobu, dla której skonfigurowano alert.
- Dozwolony jest tylko jeden warunek "allOf".
- Wartość "AnyOf" może służyć do zezwalania na wiele warunków w wielu polach (na przykład jeśli pola "status" lub "subStatus" są równe określonej wartości). Należy pamiętać, że użycie funkcji "AnyOf" jest obecnie ograniczone do tworzenia reguły alertu przy użyciu wdrożenia szablonu usługi ARM.
- Za pomocą opcji "ContainsAny" można zezwolić na wiele wartości tego samego pola (na przykład jeśli "operation" ma wartość "delete" lub "modify"). Należy pamiętać, że użycie funkcji "ContainsAny" jest obecnie ograniczone do tworzenia reguły alertu przy użyciu wdrożenia szablonu usługi ARM.
- W przypadku kategorii "administracyjnej" należy określić co najmniej jedno z poprzednich kryteriów w alercie. Nie można utworzyć alertu, który jest aktywowany za każdym razem, gdy w dziennikach aktywności zostanie utworzone zdarzenie.
- Nie można tworzyć alertów dla zdarzeń w kategorii Alert dziennika aktywności.

## <a name="azure-portal"></a>Azure Portal

Za pomocą tego Azure Portal można tworzyć i modyfikować reguły alertów dziennika aktywności. Środowisko jest zintegrowane z dziennika aktywności platformy Azure, aby zapewnić bezproblemowe tworzenie alertów dla określonych zdarzeń, które Cię interesują.

### <a name="create-with-the-azure-portal"></a>Utwórz za pomocą Azure Portal

Użyj poniższej procedury.

1. W Azure Portal pozycję **Monitoruj**  >  **alerty.**
2. Wybierz **pozycję Nowa reguła alertu** w lewym górnym rogu okna **Alerty.**

     ![Nowa reguła alertu](media/alerts-activity-log/AlertsPreviewOption.png)

     Zostanie **wyświetlone okno Tworzenie** reguły.

      ![Opcje nowej reguły alertu](media/alerts-activity-log/create-new-alert-rule-options.png)

3. W **obszarze Zdefiniuj warunek** alertu podaj następujące informacje i wybierz pozycję **Gotowe:**

   - **Cel alertu:** Aby wyświetlić i wybrać element docelowy nowego alertu, użyj opcji **Filtruj według subskrypcji**  /  **Filtruj według typu zasobu.** Wybierz zasób lub grupę zasobów z wyświetlonej listy.

     > [!NOTE]
     > 
     > Możesz wybrać tylko [Azure Resource Manager](../../azure-resource-manager/management/overview.md) śledzony zasób, grupę zasobów lub całą subskrypcję dla sygnału dziennika aktywności. 

     **Widok przykładu obiektu docelowego alertu**

     ![Wybieranie obiektu docelowego](media/alerts-activity-log/select-target.png)

   - W **obszarze Kryteria docelowe** wybierz pozycję Dodaj **kryteria.** Zostaną wyświetlone wszystkie dostępne sygnały dla obiektu docelowego, w tym te z różnych kategorii **dziennika aktywności.** Nazwa kategorii jest dołączana do **nazwy usługi** monitorowania.

   - Wybierz sygnał z listy wyświetlanych dla różnych operacji możliwych dla typu **Dziennik aktywności.**

     Możesz wybrać oś czasu historii dziennika i odpowiednią logikę alertu dla tego sygnału docelowego:

     **Ekran Dodawania kryteriów**

     ![Dodawanie kryteriów](media/alerts-activity-log/add-criteria.png)
     
     > [!NOTE]
     > 
     >  Aby zapewnić wysoką jakość i skuteczność reguł, prosimy o dodanie co najmniej jednego warunku do reguł z sygnałem "Wszystkie administracyjne". 
     > W ramach definicji alertu należy wypełnić jedną z listy rozwijanych: "Poziom zdarzenia", "Stan" lub "Zainicjowane przez" i przez to reguła będzie bardziej specyficzna.

     - **Czas historii:** zdarzenia dostępne dla wybranej operacji można wykreślić w ciągu ostatnich 6, 12 lub 24 godzin bądź w ostatnim tygodniu.

     - **Logika alertów:**

       - **Poziom zdarzenia:** poziom ważności _zdarzenia:_ _Pełne,_ Informacyjne, _Ostrzeżenie,_ _Błąd_ lub _Krytyczny._
       - **Stan:** stan _zdarzenia:_ Rozpoczęto, _Niepowodzenie_ lub _Powodzenie._
       - **Zdarzenie zainicjowane przez**: nazywane również wywołującym. Adres e-mail Azure Active Directory identyfikator użytkownika, który wykonał operację.

       Ten przykładowy wykres sygnału ma zastosowaną logikę alertu:

       ![Wybrane kryteria](media/alerts-activity-log/criteria-selected.png)

4. W **obszarze Zdefiniuj szczegóły** alertu podaj następujące szczegóły:

    - **Nazwa reguły alertu:** nazwa nowej reguły alertu.
    - **Opis:** opis nowej reguły alertu.
    - **Zapisz alert w grupie zasobów:** wybierz grupę zasobów, w której chcesz zapisać nową regułę.

5. W **obszarze Grupa** akcji z menu rozwijanego określ grupę akcji, którą chcesz przypisać do nowej reguły alertu. Możesz też [utworzyć nową grupę akcji](./action-groups.md) i przypisać ją do nowej reguły. Aby utworzyć nową grupę, wybierz **pozycję + Nowa grupa.**

6. Aby włączyć reguły po ich utworzeniu, wybierz opcję **Tak** dla opcji **Włącz regułę po utworzeniu.**
7. Wybierz pozycję **Utwórz regułę alertu**.

    Zostanie utworzona nowa reguła alertu dla dziennika aktywności, a w prawym górnym rogu okna zostanie wyświetlony komunikat potwierdzający.

    Regułę można włączać, wyłączać, edytować lub usuwać. Dowiedz się więcej na temat zarządzania regułami dziennika aktywności.


Prostą analogią do zrozumienia warunków, w których można utworzyć reguły alertów w dzienniku aktywności, jest eksplorowanie lub filtrowanie zdarzeń za pośrednictwem dziennika aktywności w [Azure Portal](../essentials/activity-log.md#view-the-activity-log). Na **ekranie Azure Monitor — Dziennik** aktywności możesz odfiltrować lub znaleźć wymagane zdarzenie, a następnie utworzyć alert przy użyciu przycisku Dodaj **alert dziennika** aktywności. Następnie wykonaj kroki od 4 do 7, jak pokazano wcześniej.
    
 ![Dodawanie alertu z dziennika aktywności](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>Wyświetlanie i zarządzanie w Azure Portal

1. W Azure Portal pozycję **Monitoruj**  >  **alerty.** Wybierz **pozycję Zarządzaj regułami alertów** w lewym górnym rogu okna.

    ![Zrzut ekranu przedstawia dziennik aktywności z wyróżnionem polem wyszukiwania.](media/alerts-activity-log/manage-alert-rules.png)

    Zostanie wyświetlona lista dostępnych reguł.

2. Wyszukaj regułę dziennika aktywności do zmodyfikowania.

    ![Reguły alertów dziennika aktywności wyszukiwania](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    Aby znaleźć regułę działania, którą chcesz edytować, możesz użyć dostępnych filtrów: _Subskrypcja,_ Grupa _zasobów,_ _Zasób,_ Typ sygnału lub Stan.

   > [!NOTE]
   > 
   > Można edytować tylko **opisy,** **kryteria docelowe** i **grupy akcji.**

3. Wybierz regułę, a następnie kliknij dwukrotnie, aby edytować opcje reguły. Wprowadzić wymagane zmiany, a następnie wybierz pozycję **Zapisz.**

   ![Zarządzaj regułami alertów](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. Regułę można włączać, wyłączać lub usuwać. Wybierz odpowiednią opcję w górnej części okna po wybraniu reguły zgodnie z opisem w kroku 2.


## <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager
Aby utworzyć regułę alertu dziennika aktywności przy użyciu Azure Resource Manager szablonu, należy utworzyć zasób typu `microsoft.insights/activityLogAlerts` . Następnie należy wypełnić wszystkie powiązane właściwości. Oto szablon, który tworzy regułę alertu dziennika aktywności:

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
Poprzedni przykładowy kod JSON można zapisać jako na przykład sampleActivityLogAlert.jsna potrzeby tego przykładu i można go wdrożyć przy użyciu Azure Resource Manager w Azure Portal [.](../../azure-resource-manager/templates/deploy-portal.md)

  > [!NOTE]
  > 
  > Zwróć uwagę, że alerty dziennika aktywności najwyższego poziomu można zdefiniować jako subskrypcję.
  > Oznacza to, że nie ma opcji definiowania alertu dla kilku subskrypcji, dlatego definicja powinna być alertem dla subskrypcji.

Następujące pola to opcje, których można użyć w szablonie usługi Azure Resource Manager dla pól warunków: Zwróć uwagę, że pola "Resource Health", "Advisor" i "Service Health" mają dodatkowe pola właściwości dla swoich pól specjalnych. 
1. resourceId: identyfikator zasobu, na który ma to wpływ, w zdarzeniu dziennika aktywności, w przypadku których powinien zostać wygenerowany alert.
2. category: kategoria w zdarzeniu dziennika aktywności. Na przykład: Administracyjne, ServiceHealth, ResourceHealth, Autoskalowanie, Zabezpieczenia, Zalecenie, Zasady.
3. wywołujący: adres e-mail lub Azure Active Directory użytkownika, który wykonał operację zdarzenia dziennika aktywności.
4. level: poziom aktywności w zdarzeniu dziennika aktywności, na który powinien zostać wygenerowany alert. Na przykład: Krytyczne, Błąd, Ostrzeżenie, Informacyjne, Pełne.
5. operationName: nazwa operacji w zdarzeniu dziennika aktywności. Na przykład: Microsoft.Resources/deployments/write
6. resourceGroup: nazwa grupy zasobów dla zasobu, na który ma to wpływ, w zdarzeniu dziennika aktywności.
7. resourceProvider: [Objaśnienie dostawców zasobów platformy Azure i typów](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fresource-providers-and-types&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373543634&sdata=4RjpTkO5jsdOgPdt%2F%2FDOlYjIFE2%2B%2BuoHq5%2F7lHpCwQw%3D&reserved=0). Aby uzyskać listę map, która mapuje dostawców zasobów na usługi platformy Azure, zobacz [Dostawcy zasobów dla usług platformy Azure.](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fazure-services-resource-providers&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373553639&sdata=0ZgJPK7BYuJsRifBKFytqphMOxMrkfkEwDqgVH1g8lw%3D&reserved=0)
8. status: ciąg opisujący stan operacji w zdarzeniu działania. Na przykład: Rozpoczęte, W toku, Powodzenie, Niepowodzenie, Aktywne, Rozwiązane
9. subStatus: zwykle kod stanu HTTP odpowiedniego wywołania REST, ale może również zawierać inne ciągi opisujące stan podrzędny.   Na przykład: OK (kod stanu HTTP: 200), utworzone (kod stanu HTTP: 201), zaakceptowane (kod stanu HTTP: 202), brak zawartości (kod stanu HTTP: 204), złe żądanie (kod stanu HTTP: 400), nie Znaleziono (kod stanu HTTP: 404), konflikt (kod stanu HTTP: 409), wewnętrzny błąd serwera (kod stanu HTTP: 500), usługa niedostępna (kod stanu HTTP: 503), limit czasu bramy (kod stanu HTTP: 504).
10. resourceType: typ zasobu, na który zdarzenie miało wpływ. Na przykład: Microsoft.Resources/deployments

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
Więcej szczegółów na temat pól dziennika aktywności można znaleźć [tutaj.](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-monitor%2Fplatform%2Factivity-log-schema&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373563632&sdata=6QXLswwZgUHFXCuF%2FgOSowLzA8iOALVgvL3GMVhkYJY%3D&reserved=0)



> [!NOTE]
> Może upłynieć do 5 minut, aż nowa reguła alertu dziennika aktywności stanie się aktywna.

## <a name="rest-api"></a>Interfejs API REST 
Interfejs [API Azure Monitor alertów dziennika aktywności](/rest/api/monitor/activitylogalerts) to interfejs API REST. Jest on w pełni zgodny z interfejsem API REST Azure Resource Manager api. Można go używać za pośrednictwem programu PowerShell przy użyciu Resource Manager cmdlet lub interfejsu wiersza polecenia platformy Azure.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>Wdrażanie szablonu Resource Manager za pomocą programu PowerShell
Aby użyć programu PowerShell do wdrożenia przykładowego Resource Manager szablonu pokazanego w poprzedniej sekcji Azure Resource Manager [szablonu,](#azure-resource-manager-template) użyj następującego polecenia:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

gdzie sampleActivityLogAlert.parameters.jszawiera wartości podane dla parametrów wymaganych do utworzenia reguły alertu.

### <a name="use-activity-log-powershell-cmdlets"></a>Korzystanie z poleceń cmdlet programu PowerShell dziennika aktywności

Alerty dziennika aktywności mają dostępne dedykowane polecenia cmdlet programu PowerShell:

- [Set-AzActivityLogAlert:](/powershell/module/az.monitor/set-azactivitylogalert)tworzy nowy alert dziennika aktywności lub aktualizuje istniejący alert dziennika aktywności.
- [Get-AzActivityLogAlert:](/powershell/module/az.monitor/get-azactivitylogalert)pobiera jeden lub więcej zasobów alertów dziennika aktywności.
- [Enable-AzActivityLogAlert:](/powershell/module/az.monitor/enable-azactivitylogalert)włącza istniejący alert dziennika aktywności i ustawia jego tagi.
- [Disable-AzActivityLogAlert:](/powershell/module/az.monitor/disable-azactivitylogalert)wyłącza istniejący alert dziennika aktywności i ustawia jego tagi.
- [Remove-AzActivityLogAlert:](/powershell/module/az.monitor/remove-azactivitylogalert)usuwa alert dziennika aktywności.

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Dedykowane polecenia interfejsu wiersza polecenia platformy Azure w ramach [zestawu alertu az monitor activity-log](/cli/azure/monitor/activity-log/alert) są dostępne do zarządzania regułami alertów dziennika aktywności.

Aby utworzyć nową regułę alertu dziennika aktywności, użyj następujących poleceń w tej kolejności:

1. [az monitor activity-log alert create](/cli/azure/monitor/activity-log/alert#az_monitor_activity_log_alert_create): Utwórz nowy zasób reguły alertu dziennika aktywności.
1. [az monitor activity-log alert scope](/cli/azure/monitor/activity-log/alert/scope): Dodaj zakres dla utworzonej reguły alertu dziennika aktywności.
1. [az monitor activity-log alert action-group](/cli/azure/monitor/activity-log/alert/action-group): Dodaj grupę akcji do reguły alertu dziennika aktywności.

Aby pobrać jeden zasób reguły alertu dziennika aktywności, użyj polecenia [az monitor activity-log alert show](/cli/azure/monitor/activity-log/alert#az_monitor_activity_log_alert_show
)interfejsu wiersza polecenia platformy Azure. Aby wyświetlić wszystkie zasoby reguły alertu dziennika aktywności w grupie zasobów, użyj az [monitor activity-log alert list](/cli/azure/monitor/activity-log/alert#az_monitor_activity_log_alert_list).
Zasoby reguły alertu dziennika aktywności można usunąć za pomocą polecenia [az monitor activity-log alert delete](/cli/azure/monitor/activity-log/alert#az_monitor_activity_log_alert_delete)interfejsu wiersza polecenia platformy Azure.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej [o schemacie obiektu webhook dla dzienników aktywności.](./activity-log-alerts-webhook.md)
- Zapoznaj się [z omówieniem dzienników aktywności.](./activity-log-alerts.md)
- Dowiedz się więcej o [grupach akcji.](./action-groups.md)  
- Dowiedz się więcej [o powiadomieniach dotyczących kondycji usługi.](../../service-health/service-notifications.md)
