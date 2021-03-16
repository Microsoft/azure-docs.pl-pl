---
title: Rozwiązywanie problemów z błędami AMQP na platformie Azure Event Hubs | Microsoft Docs
description: Zawiera listę AMQP błędów, które mogą być wyświetlane podczas korzystania z platformy Azure Event Hubs i przyczyn tych błędów.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 51b96792f6921bae9364212c6e5f9c987ff05e2a
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103466069"
---
# <a name="amqp-errors-in-azure-event-hubs"></a>AMQP błędy na platformie Azure Event Hubs
W tym artykule przedstawiono niektóre z błędów, które pojawiają się podczas korzystania z programu AMQP z platformą Azure Event Hubs. Są to standardowe zachowania usługi. Można je uniknąć, wykonując wywołania Wyślij/Odbierz dla połączenia/linku, co powoduje automatyczne odtworzenie połączenia/linku.

## <a name="link-is-closed"></a>Łącze jest zamknięte 
Wystąpi następujący błąd, gdy połączenie AMQP i link są aktywne, ale żadne wywołania (na przykład Send lub Receive) nie są wykonywane przy użyciu linku przez 30 minut. Łącze jest zamykane. Połączenie jest nadal otwarte.

"AMQP: link: Odłącz — wymuszone: link" G2:7223832: user.tenant0.cud_00000000000-0000-0000-0000-00000000000000 "jest wymuszany przez brokera ze względu na błędy wystąpiły w wydawcy (link164614). Odłącz pochodzenie: AmqpMessagePublisher. IdleTimerExpired: limit czasu bezczynności: 00:30:00. TrackingId: 00000000000000000000000000000000000000_G2_B3, SystemTracker: przestrzeń nazw: temat: temat, sygnatura czasowa: 2/16/2018 11:10:40 PM

## <a name="connection-is-closed"></a>Połączenie zostało zamknięte
Gdy wszystkie linki w połączeniu zostały zamknięte, zobaczysz następujący błąd w połączeniu z usługą AMQP, ponieważ nie ma żadnych działań (bezczynny), a nowy link nie został utworzony w ciągu 5 minut.

"Error {Condition = AMQP: Connection: wymuszono, Description =" połączenie było nieaktywne przez więcej niż dozwolone 300000 milisekund i jest zamknięte przez kontener "LinkTracker". TrackingId: 00000000000000000000000000000000000_G21, SystemTracker: gateway5, sygnatura czasowa: 2019-03-06T17:32:00 ', info = null} "

## <a name="link-isnt-created"></a>Link nie został utworzony 
Ten błąd jest wyświetlany podczas tworzenia nowego połączenia usługi AMQP, ale link nie jest tworzony w ciągu 1 minuty od utworzenia połączenia AMQP.

"Error {Condition = AMQP: Connection: wymuszono, Description =" połączenie było nieaktywne przez więcej niż dozwolone 60000 milisekund i jest zamknięte przez kontener "LinkTracker". TrackingId: 0000000000000000000000000000000000000_G21, SystemTracker: gateway5, sygnatura czasowa: 2019-03-06T18:41:51 ', info = null} "

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat AMQP, zobacz [Przewodnik po protokole AMQP 1,0](../service-bus-messaging/service-bus-amqp-protocol-guide.md).
