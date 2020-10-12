---
title: Rozwiązywanie błędów AMQP w Azure Service Bus | Microsoft Docs
description: Zawiera listę błędów AMQP, które mogą być wyświetlane podczas korzystania z Azure Service Bus, i przyczyn tych błędów.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 88b10940e0b910f50e6ccf7f8c53134fa7f0ba2f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88064353"
---
# <a name="amqp-errors-in-azure-service-bus"></a>Błędy AMQP w Azure Service Bus
W tym artykule przedstawiono niektóre z błędów, które pojawiają się podczas korzystania z programu AMQP z Azure Service Bus. Są to standardowe zachowania usługi. Można je uniknąć, wykonując wywołania Wyślij/Odbierz dla połączenia/linku, co powoduje automatyczne odtworzenie połączenia/linku.

## <a name="link-is-closed"></a>Łącze jest zamknięte 
Następujący błąd jest wyświetlany, gdy połączenie AMQP i link są aktywne, ale żadne wywołania (na przykład Send lub Receive) nie są wykonywane za pomocą linku przez 10 minut. Łącze jest zamykane. Połączenie jest nadal otwarte.

```
amqp:link:detach-forced:The link 'G2:7223832:user.tenant0.cud_00000000000-0000-0000-0000-00000000000000' is force detached by the broker due to errors occurred in publisher(link164614). Detach origin: AmqpMessagePublisher.IdleTimerExpired: Idle timeout: 00:10:00. TrackingId:00000000000000000000000000000000000000_G2_B3, SystemTracker:mynamespace:Topic:MyTopic, Timestamp:2/16/2018 11:10:40 PM
```

## <a name="connection-is-closed"></a>Połączenie zostało zamknięte
Gdy wszystkie linki w połączeniu zostały zamknięte, zobaczysz następujący błąd w połączeniu z usługą AMQP, ponieważ nie ma żadnych działań (bezczynny), a nowy link nie został utworzony w ciągu 5 minut.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 300000 milliseconds and is closed by container 'LinkTracker'. TrackingId:00000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T17:32:00', info=null}
```

## <a name="link-is-not-created"></a>Link nie został utworzony 
Ten błąd jest wyświetlany podczas tworzenia nowego połączenia usługi AMQP, ale link nie jest tworzony w ciągu 1 minuty od utworzenia połączenia AMQP.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 60000 milliseconds and is closed by container 'LinkTracker'. TrackingId:0000000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T18:41:51', info=null}
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat AMQP i Service Bus, odwiedź następujące linki:

* [Service Bus AMQP — Omówienie]
* [Przewodnik dotyczący protokołu AMQP 1.0]
* [AMQP w Service Bus dla systemu Windows Server]

[Service Bus AMQP — Omówienie]: service-bus-amqp-overview.md
[Przewodnik dotyczący protokołu AMQP 1.0]: service-bus-amqp-protocol-guide.md
[AMQP w Service Bus dla systemu Windows Server]: /previous-versions/service-bus-archive/dn282144(v=azure.100)