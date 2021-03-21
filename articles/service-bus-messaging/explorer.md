---
title: Używanie Azure Service Bus Explorer do wykonywania operacji na danych na Service Bus (wersja zapoznawcza)
description: Ten artykuł zawiera informacje dotyczące korzystania z Eksploratora Azure Service Bus opartego na portalu w celu uzyskania dostępu do Azure Service Bus danych.
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: 0b5274c492a1dfb2523c52d7aea2b7ebf8eae675
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101738960"
---
# <a name="use-service-bus-explorer-to-perform-data-operations-on-service-bus-preview"></a>Używanie Service Bus Explorer do wykonywania operacji na danych na Service Bus (wersja zapoznawcza)

## <a name="overview"></a>Omówienie

Azure Service Bus umożliwia nadawcom i odbiorcom aplikacji klienckich oddzielenie ich logiki biznesowej przy użyciu znanych semantyki typu punkt-punkt (Kolejka) i publikowanie-subskrybowanie (subskrypcja tematu).

Operacje wykonywane na Azure Service Bus przestrzeni nazw mają dwa rodzaje 
   * Operacje zarządzania — tworzenie, aktualizowanie, usuwanie Service Bus przestrzeń nazw, kolejki, tematy i subskrypcje.
   * Operacje na danych — wysyłanie do i odbieranie komunikatów z kolejek, tematów i subskrypcji.

Eksplorator Azure Service Bus rozszerza funkcje portalu wykraczające poza operacje zarządzania, aby obsługiwać operacje na danych (wysyłać, odbierać, wglądu) w kolejkach, tematach i subskrypcjach (i ich podjednostkach utraconych) — bezpośrednio od Azure Portal samego.

> [!NOTE]
> W tym artykule przedstawiono funkcje Eksploratora Azure Service Bus, które znajdują się w Azure Portal.
>
> Narzędzie Azure Service Bus Explorer ***nie*** jest własnością społecznościową narzędzia OSS [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).
>

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć narzędzia Eksplorator Service Bus, należy wykonać następujące zadania: 

- Inicjowanie obsługi administracyjnej przestrzeni nazw Azure Service Bus.
- Utwórz kolejkę do wysyłania i odbierania wiadomości z lub tematu z subskrypcją w celu przetestowania funkcjonalności. Aby dowiedzieć się, jak tworzyć kolejki, tematy i subskrypcje, zobacz następujące artykuły: 
    - [Szybki Start — Tworzenie kolejek](service-bus-quickstart-portal.md)
    - [Szybki Start — Tworzenie tematów](service-bus-quickstart-topics-subscriptions-portal.md)
- Upewnij się, że jesteś członkiem jednej z tych ról w przestrzeni nazw: 
    - [Właściciel danych Service Bus](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner) 
    - [Współautor](../role-based-access-control/built-in-roles.md#contributor) 
    - [Właściciel](../role-based-access-control/built-in-roles.md#owner)


## <a name="using-the-service-bus-explorer"></a>Korzystanie z Eksploratora Service Bus

Aby użyć Eksploratora Azure Service Bus, należy przejść do przestrzeni nazw Service Bus, w której chcesz wykonać operacje wysyłania, wglądu i odbierania.

Jeśli chcesz wykonać operacje dotyczące kolejki, wybierz pozycję **"kolejki"** z menu nawigacji. Jeśli chcesz wykonać operacje dotyczące tematu (i powiązanych subskrypcji), wybierz **Tematy**. 

:::image type="content" source="./media/service-bus-explorer/queue-topics-left-navigation.png" alt-text="Wybór jednostki":::

Po wybraniu instrukcji **"Queues"** lub **"Tematy"** wybierz określoną kolejkę lub temat.

Wybierz pozycję **"Service Bus Explorer (wersja zapoznawcza)"** z menu nawigacji po lewej stronie

:::image type="content" source="./media/service-bus-explorer/left-navigation-menu-selected.png" alt-text="Menu nawigacji po lewej stronie Eksploratora SB":::

### <a name="sending-a-message-to-a-queue-or-topic"></a>Wysyłanie komunikatu do kolejki lub tematu

Aby wysłać komunikat do **kolejki** lub **tematu**, kliknij kartę **_wysyłanie_** w Eksploratorze Service Bus.

Aby zredagować wiadomość tutaj — 

1. Wybierz **Typ zawartości** jako "text/Plains", "Application/XML" lub "Application/JSON".
2. Dodaj **treść** wiadomości. Upewnij się, że jest zgodny z zestawem **typu zawartości** wcześniejszym.
3. Ustaw **Właściwości zaawansowane** (opcjonalnie) — dotyczy to zarówno identyfikatora korelacji, identyfikatora komunikatu, etykiety, ReplyTo, czasu wygaśnięcia (TTL), jak i zaplanowanego czasu dla kolejki (dla zaplanowanych wiadomości).
4. Ustawianie **właściwości niestandardowych** — mogą to być dowolne właściwości użytkownika ustawione dla klucza słownika.

Po utworzeniu komunikatu kliknij przycisk Wyślij.

:::image type="content" source="./media/service-bus-explorer/send-experience.png" alt-text="Redagowanie wiadomości":::

Po pomyślnym ukończeniu operacji wysyłania 

* W przypadku wysyłania do kolejki licznik metryki **aktywnych komunikatów** zostanie zwiększony.

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="QueueAfterSendMetrics":::

* W przypadku wysłania do tematu Licznik metryk **komunikatów aktywnych** zostanie zwiększony dla subskrypcji, do której wiadomość była kierowana.

    :::image type="content" source="./media/service-bus-explorer/topic-after-send-metrics.png" alt-text="TopicAfterSendMetrics":::

### <a name="receiving-a-message-from-a-queue"></a>Otrzymywanie komunikatu z kolejki

Funkcja Receive w Eksploratorze Service Bus zezwala na odbieranie pojedynczej wiadomości w danym momencie. Operacja odbierania jest przeprowadzana przy użyciu trybu **ReceiveAndDelete** .

> [!IMPORTANT]
> Należy pamiętać, że operacja odbierania wykonywana przez Eksploratora Service Bus jest ***odbierana jako destrukcyjne***, tj. komunikat jest usuwany z kolejki, gdy jest wyświetlany w narzędziu Eksplorator Service Bus.
>
> Aby przeglądać komunikaty bez usuwania ich z kolejki, należy rozważyć użycie funkcji ***wglądu*** .
>

Aby odebrać komunikat z kolejki (lub podkolejki utraconych wiadomości) 

1. Kliknij kartę ***Odbierz*** w Eksploratorze Service Bus.
2. Sprawdź metryki, aby sprawdzić, czy istnieją **aktywne komunikaty** lub **wiadomości utraconych** do odebrania.

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="QueueAfterSendMetrics":::

3. Wybierz **kolejkę kolejki *** podpłata * lub _ *_utraconych wiadomości_**.

    :::image type="content" source="./media/service-bus-explorer/queue-or-deadletter.png" alt-text="QueueOrDeadletter":::

4. Kliknij przycisk ***Odbierz** _, a *_następnie _ *_**, aby potwierdzić operację "Receive and DELETE".


Po pomyślnym przeprowadzeniu operacji odbierania szczegóły komunikatu będą wyświetlane w siatce poniżej. Możesz wybrać komunikat z siatki, aby wyświetlić jego szczegóły.

:::image type="content" source="./media/service-bus-explorer/receive-message-from-queue-2.png" alt-text="Zrzut ekranu okna kolejki w Eksploratorze Azure Service Bus z informacjami o komunikacie wyświetlanymi dla aktywnej wiadomości wybranej w kolejce.":::


### <a name="peeking-a-message-from-a-queue"></a>Wgląd do wiadomości z kolejki

Korzystając z funkcji wglądu, można użyć Eksploratora Service Bus do wyświetlania pierwszych 32 komunikatów w kolejce lub w kolejce utraconych wiadomości.

1. Aby uzyskać wgląd w komunikat w kolejce, kliknij kartę ***wgląd*** w Eksploratorze Service Bus.

    :::image type="content" source="./media/service-bus-explorer/peek-tab-selected.png" alt-text="PeekTab":::

2. Sprawdź metryki, aby sprawdzić, czy są **aktywne komunikaty** lub **wiadomości utracone** do wglądu.

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="QueueAfterSendMetrics":::

3. Następnie **Wybierz kolejkę kolejki *** podpłata * lub _ *_utraconych wiadomości_**.

    :::image type="content" source="./media/service-bus-explorer/queue-or-deadletter.png" alt-text="QueueOrDeadletter":::

4. Kliknij przycisk ***wgląd*** . 

Po zakończeniu operacji wglądu do 32 komunikatów będzie widocznych w siatce poniżej. Aby wyświetlić szczegóły konkretnego komunikatu, wybierz go z siatki. 

:::image type="content" source="./media/service-bus-explorer/peek-message-from-queue-2.png" alt-text="PeekMessageFromQueue":::

> [!NOTE]
>
> Ponieważ wgląd nie jest operacją niszczącą, komunikat **nie zostanie** usunięty z kolejki.
>

### <a name="receiving-a-message-from-a-subscription"></a>Otrzymywanie wiadomości z subskrypcji

Podobnie jak w przypadku kolejki, operację ***odbierania*** można wykonać w odniesieniu do subskrypcji (lub jej jednostki utraconych). Jednak ponieważ subskrypcja jest w kontekście tematu, operacja odbierania jest wykonywana przez przechodzenie do Eksploratora Service Bus dla danego tematu.

> [!IMPORTANT]
> Należy pamiętać, że operacja odbierania wykonywana przez Eksploratora Service Bus jest ***odbierana jako destrukcyjne***, tj. komunikat jest usuwany z kolejki, gdy jest wyświetlany w narzędziu Eksplorator Service Bus.
>
> Aby przeglądać komunikaty bez usuwania ich z kolejki, należy rozważyć użycie funkcji ***wglądu*** .
>

1. Kliknij kartę ***Odbierz** _ i wybierz określony _ *_subskrypcję_** z selektora listy rozwijanej.

    :::image type="content" source="./media/service-bus-explorer/receive-subscription-tab-selected.png" alt-text="ReceiveTabSelected":::

2. Wybierz między podklasą ***Subscription** _ lub _ *_utraconych wiadomości_**.

    :::image type="content" source="./media/service-bus-explorer/subscription-or-deadletter.png" alt-text="SubscriptionOrDeadletter":::

3. Kliknij przycisk ***Odbierz** _, a *_następnie _ *_**, aby potwierdzić operację "Receive and DELETE".

Po pomyślnym przeprowadzeniu operacji odbierania odebrany komunikat zostanie wyświetlony w siatce poniżej. Aby wyświetlić szczegóły komunikatu, kliknij komunikat.

:::image type="content" source="./media/service-bus-explorer/receive-message-from-subscription.png" alt-text="Zrzut ekranu przedstawiający kartę Odbierz w Eksploratorze Azure Service Bus z informacjami o komunikacie wyświetlanymi dla aktywnej wiadomości, która została odebrana.":::

### <a name="peeking-a-message-from-a-subscription"></a>Wgląd do wiadomości z subskrypcji

Aby po prostu przeglądać komunikaty w ramach subskrypcji lub jej podjednostki utraconych wiadomości, można również użyć funkcji ***wglądu*** w subskrypcji.

1. Kliknij kartę ***wgląd** _ i wybierz określony _ *_subskrypcję_** z selektora listy rozwijanej.

    :::image type="content" source="./media/service-bus-explorer/peek-subscription-tab-selected.png" alt-text="PeekTabSelected":::

2. Wybierz między podobiektem ***Subscription** _ lub _ *_utraconych wiadomości_**.

    :::image type="content" source="./media/service-bus-explorer/subscription-or-deadletter.png" alt-text="SubscriptionOrDeadletter":::

3. Kliknij przycisk ***wgląd*** .

Po zakończeniu operacji wglądu do 32 komunikatów będzie widocznych w siatce poniżej. Aby wyświetlić szczegóły konkretnego komunikatu, wybierz go z siatki. 

:::image type="content" source="./media/service-bus-explorer/peek-message-from-subscription.png" alt-text="PeekMessageFromSubscription":::

> [!NOTE]
>
> Ponieważ wgląd nie jest operacją niszczącą, komunikat **nie zostanie** usunięty z kolejki.
>

## <a name="next-steps"></a>Następne kroki

   * Dowiedz się więcej o Service Bus [kolejkach](service-bus-queues-topics-subscriptions.md#queues) i [tematach](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)
   * Dowiedz się więcej [na temat tworzenia kolejek Service Bus za pośrednictwem Azure Portal](service-bus-quickstart-portal.md)
   * Dowiedz się więcej [na temat tworzenia Service Bus tematów i subskrypcji za pośrednictwem Azure Portal](service-bus-quickstart-topics-subscriptions-portal.md)