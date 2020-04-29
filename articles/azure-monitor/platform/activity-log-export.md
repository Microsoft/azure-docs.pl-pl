---
title: Eksportowanie dziennika aktywności platformy Azure
description: Eksportuj dziennik aktywności platformy Azure do magazynu w celu archiwizacji lub Event Hubs platformy Azure do eksportowania poza platformę Azure.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 12c750f96b8852cdd6a6039ebfa750c2ee792a6b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80396716"
---
# <a name="export-azure-activity-log-to-storage-or-azure-event-hubs"></a>Eksportowanie dziennika aktywności platformy Azure do magazynu lub Event Hubs platformy Azure

> [!IMPORTANT]
> Metoda wysyłania dziennika aktywności platformy Azure do usługi Azure Storage i usługi Azure Event Hubs zmieniła się na [Ustawienia diagnostyczne](diagnostic-settings.md). W tym artykule opisano starszą metodę, która jest w trakcie wycofywania. Zobacz Aktualizacja, aby [zebrać i przeanalizować dziennik aktywności platformy Azure w Azure monitor](activity-log-collect.md) na potrzeby porównania.


[Dziennik aktywności platformy Azure](platform-logs-overview.md) zapewnia wgląd w zdarzenia na poziomie subskrypcji, które wystąpiły w ramach subskrypcji platformy Azure. Oprócz wyświetlania dziennika aktywności w Azure Portal lub kopiowania go do Log Analytics obszaru roboczego, w którym można je analizować z innymi danymi zebranymi przez Azure Monitor, można utworzyć profil dziennika, aby zarchiwizować dziennik aktywności na koncie usługi Azure Storage lub przesłać strumieniowo do centrum zdarzeń.

## <a name="archive-activity-log"></a>Archiwizowanie dziennika aktywności
Archiwizowanie dziennika aktywności na koncie magazynu jest przydatne, jeśli chcesz przechowywać dane dziennika dłużej niż 90 dni (z pełną kontrolą nad zasadami przechowywania) na potrzeby inspekcji, statycznej analizy lub tworzenia kopii zapasowych. Jeśli musisz tylko zachować zdarzenia przez 90 dni lub mniej, nie musisz konfigurować archiwizowania do konta magazynu, ponieważ zdarzenia dziennika aktywności są przechowywane na platformie Azure przez 90 dni.

## <a name="stream-activity-log-to-event-hub"></a>Przesyłanie strumieniowe dziennika aktywności do centrum zdarzeń
[Azure Event Hubs](/azure/event-hubs/) to usługa przesyłania strumieniowego danych i usługi pozyskiwania zdarzeń, które mogą odbierać i przetwarzać miliony zdarzeń na sekundę. Dane wysłane do centrum zdarzeń mogą zostać przekształcone i zmagazynowane przy użyciu dowolnego dostawcy analityki czasu rzeczywistego lub adapterów przetwarzania wsadowego/magazynowania. W przypadku dziennika aktywności można użyć funkcji przesyłania strumieniowego na dwa sposoby:
* **Przesyłaj strumieniowo systemy rejestrowania i telemetrii innych firm**: w czasie usługa Azure Event Hubs Streaming stanie się mechanizmem potokowym dziennika aktywności w rozwiązaniach rozwiązań Siem i log Analytics innych firm.
* **Utwórz niestandardową platformę telemetrii i rejestrowania**: Jeśli masz już wbudowaną niestandardową platformę telemetrii lub zastanawiasz się, że tworzysz ten, wysoce skalowalny charakter publikowania/subskrybowania Event Hubs umożliwia elastyczne pozyskiwanie dziennika aktywności.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="storage-account"></a>Konto magazynu
W przypadku archiwizowania dziennika aktywności należy [utworzyć konto magazynu](../../storage/common/storage-account-create.md) , jeśli jeszcze go nie masz. Nie należy używać istniejącego konta magazynu, które ma inne niemonitorowane dane, które są w nim przechowywane, dzięki czemu można lepiej kontrolować dostęp do danych monitorowania. Jeśli dzienniki i metryki są również archiwizowane na koncie magazynu, możesz użyć tego samego konta magazynu, aby zachować wszystkie dane monitorowania w centralnej lokalizacji.

Konto magazynu nie musi znajdować się w tej samej subskrypcji co subskrypcja emitująca dzienniki, dopóki użytkownik, który konfiguruje ustawienie, ma dostęp do obu subskrypcji. 

> [!TIP]
> Zobacz [Konfigurowanie zapór usługi Azure Storage i sieci wirtualnych](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) w celu zapewnienia dostępu do konta magazynu w ramach zabezpieczonej sieci wirtualnej.

### <a name="event-hubs"></a>Event Hubs
Jeśli wysyłasz dziennik aktywności do centrum zdarzeń, musisz [utworzyć centrum zdarzeń](../../event-hubs/event-hubs-create.md) , jeśli jeszcze go nie masz. Jeśli wcześniej przesyłane strumieniowo zdarzenia dziennika aktywności do tej przestrzeni nazw Event Hubs, centrum zdarzeń zostanie ponownie użyte.

Zasady dostępu współdzielonego definiują uprawnienia, które ma mechanizm przesyłania strumieniowego. Przesyłanie strumieniowe do Event Hubs wymaga uprawnień do zarządzania, wysyłania i nasłuchiwania. Można utworzyć lub zmodyfikować zasady dostępu współdzielonego dla przestrzeni nazw Event Hubs w Azure Portal na karcie Konfiguracja dla przestrzeni nazw Event Hubs.

Aby zaktualizować profil dziennika aktywności w celu uwzględnienia przesyłania strumieniowego, musisz mieć uprawnienie ListKey dla tej reguły autoryzacji Event Hubs. Przestrzeń nazw Event Hubs nie musi znajdować się w tej samej subskrypcji co subskrypcja, która emituje dzienniki, pod warunkiem, że użytkownik, który konfiguruje ustawienie, ma dostęp do obu subskrypcji i obie subskrypcje są w tej samej dzierżawie usługi AAD.

Przesyłanie strumieniowe dziennika aktywności do centrum zdarzeń przez [utworzenie profilu dziennika](#create-a-log-profile).

## <a name="create-a-log-profile"></a>Tworzenie profilu dziennika
Użytkownik definiuje sposób eksportowania dziennika aktywności platformy Azure przy użyciu **profilu dziennika**. Każda subskrypcja platformy Azure może mieć tylko jeden profil dziennika. Te ustawienia można skonfigurować za pomocą opcji **Eksportuj** w bloku dziennika aktywności w portalu. Można je również skonfigurować programowo [przy użyciu interfejsu API REST Azure monitor](https://msdn.microsoft.com/library/azure/dn931927.aspx), poleceń cmdlet programu PowerShell lub interfejsu wiersza polecenia.

Profil dziennika definiuje następujące elementy.

**Miejsce, w którym ma być wysyłany dziennik aktywności.** Obecnie dostępne opcje to konto magazynu lub Event Hubs.

**Które kategorie zdarzeń powinny być wysyłane.** Znaczenie *kategorii* w profilach dzienników i zdarzeniach dziennika aktywności jest inne. W profilu dziennika *Kategoria* reprezentuje typ operacji (zapis, usuwanie, Akcja). W zdarzeniu dziennika aktywności *Kategoria*"* Właściwość reprezentuje źródło lub typ zdarzenia (na przykład administracja, usługa servicehealth i alert).

**Które regiony (lokalizacje) powinny zostać wyeksportowane.** Należy uwzględnić wszystkie lokalizacje, ponieważ wiele zdarzeń w dzienniku aktywności jest zdarzeniami globalnymi.

**Czas zachowywania dziennika aktywności na koncie magazynu.** Przechowywanie przez zero dni oznacza, że dzienniki są przechowywane w nieskończoność. W przeciwnym razie wartość może być dowolną liczbą dni z zakresu od 1 do 365.

Jeśli zasady przechowywania są ustawione, ale przechowywanie dzienników na koncie magazynu jest wyłączone, zasady przechowywania nie będą miały żadnego efektu. Zasady przechowywania są stosowane dziennie, więc po zakończeniu dnia (UTC) dzienniki są usuwane z dnia, w którym dane są już poza tymi zasadami przechowywania. Na przykład jeśli masz zasady przechowywania o jeden dzień, na początku dnia dzisiaj dzienniki z dnia przed wczoraj zostałyby usunięte. Proces usuwania rozpoczyna się o północy czasu UTC, ale należy pamiętać, że może upłynąć nawet 24 godziny, aby dzienniki zostały usunięte z konta magazynu.


> [!IMPORTANT]
> Jeśli nie zarejestrowano dostawcy zasobów Microsoft. Insights, podczas tworzenia profilu dziennika może zostać wyświetlony komunikat o błędzie. Zobacz [dostawcy zasobów platformy Azure i typy](../../azure-resource-manager/management/resource-providers-and-types.md) , aby zarejestrować tego dostawcę.


### <a name="create-log-profile-using-the-azure-portal"></a>Utwórz profil dziennika przy użyciu Azure Portal

Utwórz lub Edytuj profil dziennika za pomocą opcji **Eksportuj do centrum zdarzeń** w Azure Portal.

1. Z menu **Azure monitor** w Azure Portal wybierz pozycję **Dziennik aktywności**.
3. Kliknij pozycję **Ustawienia diagnostyczne**.

   ![Ustawienia diagnostyczne](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Kliknij transparent purpurowy dla starszego środowiska.

    ![Starsze środowisko](media/diagnostic-settings-subscription/legacy-experience.png)

3. W wyświetlonym bloku określ następujące elementy:
   * Regiony ze zdarzeniami do eksportowania. Należy zaznaczyć wszystkie regiony, aby upewnić się, że nie zostaną pominięte najważniejsze zdarzenia, ponieważ dziennik aktywności jest dziennikiem globalnym (nieregionalnym), dlatego większość zdarzeń nie ma skojarzonego regionu.
   * Jeśli chcesz zapisać na koncie magazynu:
       * Konto magazynu, do którego chcesz zapisać zdarzenia.
       * Liczba dni, przez jaką te zdarzenia mają być przechowywane w magazynie. Ustawienie 0 dni zachowuje dzienniki w nieskończoność.
   * Jeśli chcesz pisać do centrum zdarzeń:
       * Przestrzeń nazw Service Bus, w której chcesz utworzyć centrum zdarzeń na potrzeby przesyłania strumieniowego tych zdarzeń.

     ![Eksportuj blok dziennika aktywności](./media/activity-logs-overview/activity-logs-portal-export-blade.png)


4. Kliknij przycisk **Zapisz** , aby zapisać te ustawienia. Ustawienia są natychmiastowo stosowane do subskrypcji.


### <a name="configure-log-profile-using-powershell"></a>Konfigurowanie profilu dziennika przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Jeśli profil dziennika już istnieje, najpierw musisz usunąć istniejący profil dziennika, a następnie utworzyć nowy.

1. Użyj `Get-AzLogProfile` , aby określić, czy profil dziennika istnieje.  W przypadku istnienia profilu dziennika należy zwrócić uwagę na Właściwość *name* .

1. Użyj `Remove-AzLogProfile` polecenia, aby usunąć profil dziennika przy użyciu wartości z właściwości *Nazwa* .

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. Użyj `Add-AzLogProfile` , aby utworzyć nowy profil dziennika:

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | Właściwość | Wymagany | Opis |
    | --- | --- | --- |
    | Nazwa |Tak |Nazwa profilu dziennika. |
    | StorageAccountId |Nie |Identyfikator zasobu konta magazynu, w którym ma zostać zapisany dziennik aktywności. |
    | serviceBusRuleId |Nie |Service Bus Identyfikator reguły dla przestrzeni nazw Service Bus, w której chcesz utworzyć Centra zdarzeń. Jest to ciąg o formacie: `{service bus resource ID}/authorizationrules/{key name}`. |
    | Lokalizacja |Tak |Rozdzielana przecinkami lista regionów, dla których chcesz zbierać zdarzenia dziennika aktywności. |
    | RetentionInDays |Tak |Liczba dni, przez jaką zdarzenia mają być przechowywane na koncie magazynu, z zakresu od 1 do 365. Wartość zero przechowuje dzienniki w nieskończoność. |
    | Kategoria |Nie |Rozdzielana przecinkami lista kategorii zdarzeń, które mają być zbierane. Możliwe wartości to _Write_, _delete_i _Action_. |

### <a name="example-script"></a>Przykładowy skrypt
Poniżej znajduje się przykładowy skrypt programu PowerShell służący do tworzenia profilu dziennika, który zapisuje dziennik aktywności zarówno do konta magazynu, jak i do centrum zdarzeń.

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzLogProfile -Name $logProfileName -Location $locations -ServiceBusRuleId $serviceBusRuleId
   ```


### <a name="configure-log-profile-using-azure-cli"></a>Konfigurowanie profilu dziennika przy użyciu interfejsu wiersza polecenia platformy Azure

Jeśli profil dziennika już istnieje, należy najpierw usunąć istniejący profil dziennika, a następnie utworzyć nowy profil dziennika.

1. Użyj `az monitor log-profiles list` , aby określić, czy profil dziennika istnieje.
2. Użyj `az monitor log-profiles delete --name "<log profile name>` polecenia, aby usunąć profil dziennika przy użyciu wartości z właściwości *Nazwa* .
3. Użyj `az monitor log-profiles create` , aby utworzyć nowy profil dziennika:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | Właściwość | Wymagany | Opis |
    | --- | --- | --- |
    | name |Tak |Nazwa profilu dziennika. |
    | Magazyn — identyfikator konta |Tak |Identyfikator zasobu konta magazynu, do którego mają zostać zapisane dzienniki aktywności. |
    | locations |Tak |Rozdzielana spacjami lista regionów, dla których chcesz zbierać zdarzenia dziennika aktywności. Możesz wyświetlić listę wszystkich regionów dla subskrypcji przy użyciu programu `az account list-locations --query [].name`. |
    | dni |Tak |Liczba dni przechowywania zdarzeń między 1 a 365. Wartość zerowa spowoduje przechowywanie dzienników w nieskończoność (w nieskończoność).  Jeśli wartość jest równa zero, wartość parametru enabled powinna być równa false. |
    |enabled | Tak |Prawda lub fałsz.  Służy do włączania lub wyłączania zasad przechowywania.  W przypadku wartości true wartość parametru Days musi być większa niż 0.
    | categories |Tak |Rozdzielana spacjami lista kategorii zdarzeń, które powinny być zbierane. Możliwe wartości to Write, DELETE i Action. |



## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o dzienniku aktywności](../../azure-resource-manager/management/view-activity-logs.md)
* [Zbierz dziennik aktywności do dzienników Azure Monitor](activity-log-collect.md)
