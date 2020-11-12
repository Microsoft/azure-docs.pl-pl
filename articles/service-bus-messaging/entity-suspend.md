---
title: Azure Service Bus-wstrzymywanie jednostek obsługi komunikatów
description: W tym artykule wyjaśniono, jak tymczasowo wstrzymywać i ponownie aktywować Azure Service Bus jednostki komunikatów (kolejki, tematy i subskrypcje).
ms.topic: article
ms.date: 09/29/2020
ms.openlocfilehash: ea1acab3d0a86b0064f8b3eef7bfd1496bd17041
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94543055"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Wstrzymywanie i ponowne uaktywnianie jednostek obsługi komunikatów (wyłączone)

Kolejki, tematy i subskrypcje mogą być tymczasowo zawieszone. Zawieszenie umieszcza jednostkę w stanie wyłączonym, w którym wszystkie komunikaty są przechowywane w magazynie. Jednak wiadomości nie mogą zostać usunięte ani dodane, a odpowiednie operacje protokołu zwracają błędy.

Możesz chcieć zawiesić jednostkę do pilnych powodów administracyjnych. Na przykład wadliwy odbiornik odbiera komunikaty z kolejki, przetwarzanie kończy się niepowodzeniem, a jeszcze niepoprawnie wypełnia komunikaty i usuwa je. W takim przypadku można wyłączyć odbieranie kolejki do momentu, gdy poprawisz i wdrażasz kod. 

Zawieszenie lub ponowna aktywacja może zostać wykonana przez użytkownika lub przez system. System zawiesza jedynie jednostki z powodu poważnej przyczyny administracyjne, takie jak skutki limitu wydatków na subskrypcję. Jednostki wyłączone przez system nie mogą być ponownie uaktywniane przez użytkownika, ale są przywracane po rozdaniu przyczyny zawieszenia.

## <a name="queue-status"></a>Stan kolejki 
Stany, które można ustawić dla **kolejki** , to:

-   **Aktywne** : kolejka jest aktywna. Można wysyłać wiadomości do i odbierać wiadomości z kolejki. 
-   **Wyłączone** : kolejka jest wstrzymana. Jest to równoznaczne z ustawieniem zarówno **SendDisabled** , jak i **ReceiveDisabled**. 
-   **SendDisabled** : nie można wysyłać komunikatów do kolejki, ale można z niej odbierać wiadomości. Jeśli spróbujesz wysyłać komunikaty do kolejki, otrzymasz wyjątek. 
-   **ReceiveDisabled** : można wysyłać komunikaty do kolejki, ale nie można odbierać z niej komunikatów. Jeśli spróbujesz odbierać komunikaty do kolejki, otrzymasz wyjątek.


### <a name="change-the-queue-status-in-the-azure-portal"></a>Zmień stan kolejki w Azure Portal: 

1. W Azure Portal przejdź do przestrzeni nazw Service Bus. 
1. Wybierz kolejkę, dla której chcesz zmienić stan. W środkowym okienku wyświetlane są kolejki. 
1. Na stronie **Service Bus Queue** (zapoznaj się z bieżącym stanem kolejki jako hiperłączem). Jeśli nie wybrano opcji **Przegląd** w menu po lewej stronie, wybierz ją, aby wyświetlić stan kolejki. Wybierz bieżący stan kolejki, aby ją zmienić. 

    :::image type="content" source="./media/entity-suspend/select-state.png" alt-text="Wybierz stan kolejki":::
4. Wybierz nowy stan dla kolejki, a następnie wybierz **przycisk OK**. 

    :::image type="content" source="./media/entity-suspend/entity-state-change.png" alt-text="Ustaw stan kolejki":::
    
Można również wyłączyć operacje wysyłania i odbierania przy użyciu interfejsów API Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) w zestawie SDK platformy .NET lub przy użyciu szablonu Azure Resource Manager za pośrednictwem interfejsu wiersza polecenia platformy Azure lub Azure PowerShell.

### <a name="change-the-queue-status-using-azure-powershell"></a>Zmiana stanu kolejki przy użyciu Azure PowerShell
W poniższym przykładzie pokazano polecenie programu PowerShell do wyłączania kolejki. Polecenie ponownej aktywacji jest równoznaczne z ustawieniem `Status` **aktywny**.

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="topic-status"></a>Stan tematu
Stan tematu można zmienić w Azure Portal. Wybierz bieżący stan tematu, aby wyświetlić następującą stronę, która umożliwia zmianę stanu. 

:::image type="content" source="./media/entity-suspend/topic-state-change.png" alt-text="Zmień stan tematu":::

Stany, które można ustawić dla **tematu** , to:
- **Aktywne** : temat jest aktywny. Komunikaty można wysyłać do tematu. 
- **Wyłączone** : temat jest zawieszony. Nie można wysyłać komunikatów do tematu. 
- **SendDisabled** : ten sam efekt jest **wyłączony**. Nie można wysyłać komunikatów do tematu. Wystąpi wyjątek w przypadku próby wysłania komunikatów do tematu. 

## <a name="subscription-status"></a>Stan subskrypcji
Stan subskrypcji można zmienić w Azure Portal. Wybierz bieżący stan subskrypcji, aby wyświetlić następującą stronę, która umożliwia zmianę stanu. 

:::image type="content" source="./media/entity-suspend/subscription-state-change.png" alt-text="Zmień stan subskrypcji":::

Stany, które można ustawić dla **subskrypcji** , to:
- **Aktywne** : subskrypcja jest aktywna. Można odbierać wiadomości FRM subskrypcję.
- **Wyłączone** : subskrypcja jest zawieszona. Nie można odbierać wiadomości z subskrypcji. 
- **ReceiveDisabled** : ten sam efekt jest **wyłączony**. Nie można odbierać wiadomości z subskrypcji. Jeśli spróbujesz odbierać komunikaty do subskrypcji, otrzymasz wyjątek.

| Stan tematu | Stan subskrypcji | Zachowanie | 
| ------------ | ------------------- | -------- | 
| Aktywna | Aktywna | Komunikaty można wysyłać do tematu i odbierać wiadomości z subskrypcji. | 
| Aktywna | Wyłączone lub odebrane wyłączone | Komunikaty można wysyłać do tematu, ale nie można odbierać komunikatów z subskrypcji | 
| Wyłączone lub wysłane | Aktywna | Nie można wysyłać komunikatów do tematu, ale można odbierać wiadomości, które już znajdują się w subskrypcji. | 
| Wyłączone lub wysłane | Wyłączone lub odebrane wyłączone | Nie można wysyłać komunikatów do tematu i nie można uzyskać od nich subskrypcji. | 

## <a name="other-statuses"></a>Inne Stany
Wyliczenie [EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) definiuje również zestaw stanów przejściowych, które mogą być ustawiane przez system. 


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Service Bus Messaging, zobacz następujące tematy:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/entity-state-change.png

