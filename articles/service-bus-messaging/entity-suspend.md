---
title: Azure Service Bus-wstrzymywanie jednostek obsługi komunikatów
description: W tym artykule wyjaśniono, jak tymczasowo wstrzymywać i ponownie aktywować Azure Service Bus jednostki komunikatów (kolejki, tematy i subskrypcje).
ms.topic: article
ms.date: 09/29/2020
ms.openlocfilehash: f89e17e494cc777691b7f7ca47538cd29114d2dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91575262"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Wstrzymywanie i ponowne uaktywnianie jednostek obsługi komunikatów (wyłączone)

Kolejki, tematy i subskrypcje mogą być tymczasowo zawieszone. Zawieszenie umieszcza jednostkę w stanie wyłączonym, w którym wszystkie komunikaty są przechowywane w magazynie. Jednak wiadomości nie mogą zostać usunięte ani dodane, a odpowiednie operacje protokołu zwracają błędy.

Zawieszenie jednostki jest zwykle wykonywane z pilnymi przyczynami administracyjnymi. W jednym scenariuszu wdrożono wadliwy odbiornik, który pobiera komunikaty z kolejki, kończy się niepowodzeniem, a następnie nieprawidłowo wypełnia komunikaty i usuwa je. W przypadku zdiagnozowania tego zachowania Kolejka może zostać wyłączona dla odbieranych danych, dopóki nie zostanie wdrożony skorygowany kod i będzie można zapobiec dalszej utracie danych spowodowanych przez błędny kod.

Zawieszenie lub ponowna aktywacja może zostać wykonana przez użytkownika lub przez system. System zawiesza jedynie jednostki z powodu poważnego działania administracyjnego, takiego jak nakroczenie limitu wydatków na subskrypcję. Jednostki wyłączone przez system nie mogą być ponownie uaktywniane przez użytkownika, ale są przywracane po rozdaniu przyczyny zawieszenia.

## <a name="queue-status"></a>Stan kolejki 
Stany, które można ustawić dla kolejki, to:

-   **Aktywne**: kolejka jest aktywna.
-   **Wyłączone**: kolejka jest wstrzymana. Jest to równoznaczne z ustawieniem zarówno **SendDisabled** , jak i **ReceiveDisabled**. 
-   **SendDisabled**: kolejka jest częściowo zawieszona, a odbieranie jest dozwolone.
-   **ReceiveDisabled**: kolejka jest częściowo zawieszona z dozwolonym wysyłaniem.

### <a name="change-the-queue-status-in-the-azure-portal"></a>Zmień stan kolejki w Azure Portal: 

1. W Azure Portal przejdź do przestrzeni nazw Service Bus. 
1. Wybierz kolejkę, dla której chcesz zmienić stan. W środkowym okienku wyświetlane są kolejki. 
1. Na stronie **Service Bus Queue** (zapoznaj się z bieżącym stanem kolejki jako hiperłączem). Jeśli nie wybrano opcji **Przegląd** w menu po lewej stronie, wybierz ją, aby wyświetlić stan kolejki. Wybierz bieżący stan kolejki, aby ją zmienić. 

    :::image type="content" source="./media/entity-suspend/select-state.png" alt-text="Wybierz stan kolejki":::
4. Wybierz nowy stan dla kolejki, a następnie wybierz **przycisk OK**. 

    :::image type="content" source="./media/entity-suspend/entity-state-change.png" alt-text="Wybierz stan kolejki":::
    
Portal zezwala tylko na całkowite wyłączenie kolejek. Operacje wysyłania i odbierania można także wyłączać oddzielnie przy użyciu interfejsów API Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) w zestawie .NET Framework SDK lub z szablonem Azure Resource Manager za pomocą interfejsu wiersza polecenia platformy Azure lub Azure PowerShell.

### <a name="change-the-queue-status-using-azure-powershell"></a>Zmiana stanu kolejki przy użyciu Azure PowerShell
W poniższym przykładzie pokazano polecenie programu PowerShell do wyłączania kolejki. Polecenie ponownej aktywacji jest równoznaczne z ustawieniem `Status` **aktywny**.

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="topic-status"></a>Stan tematu
Zmiana stanu tematu w Azure Portal jest podobna do zmiany stanu kolejki. Po wybraniu bieżącego stanu tematu zostanie wyświetlona następująca strona, która umożliwia zmianę stanu. 

:::image type="content" source="./media/entity-suspend/topic-state-change.png" alt-text="Wybierz stan kolejki":::

Stany, które można ustawić dla tematu, to:
- **Aktywne**: temat jest aktywny.
- **Wyłączone**: temat jest zawieszony.
- **SendDisabled**: ten sam efekt jest **wyłączony**.

## <a name="subscription-status"></a>Stan subskrypcji
Zmiana stanu subskrypcji w Azure Portal jest podobna do zmiany stanu tematu lub kolejki. Po wybraniu bieżącego stanu subskrypcji zostanie wyświetlona następująca strona, która umożliwia zmianę stanu. 

:::image type="content" source="./media/entity-suspend/subscription-state-change.png" alt-text="Wybierz stan kolejki":::

Stany, które można ustawić dla tematu, to:
- **Aktywne**: temat jest aktywny.
- **Wyłączone**: temat jest zawieszony.
- **ReceiveDisabled**: ten sam efekt jest **wyłączony**.

## <a name="other-statuses"></a>Inne Stany
Wyliczenie [EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) definiuje również zestaw stanów przejściowych, które mogą być ustawiane przez system. 


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Service Bus Messaging, zobacz następujące tematy:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/entity-state-change.png

