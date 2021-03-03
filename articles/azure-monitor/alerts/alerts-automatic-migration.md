---
title: Informacje o tym, jak działa proces automatycznej migracji dla Azure Monitor klasycznych alertów
description: Dowiedz się, jak działa proces automatycznego migrowania.
ms.topic: conceptual
ms.date: 02/14/2021
ms.subservice: alerts
ms.openlocfilehash: 65409a1710a2b4c6b6d5a52c5129ec3e82dc7cc2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734863"
---
# <a name="understand-the-automatic-migration-process-for-your-classic-alert-rules"></a>Omówienie procesu migracji automatycznej dla klasycznych reguł alertów

Jak [wcześniej ogłoszone](monitoring-classic-retirement.md), klasyczne alerty w Azure monitor są wycofywane dla użytkowników chmury publicznej, mimo że nadal są ograniczone do **31 maja 2021**. Alerty klasyczne dla usług Azure Government Cloud i Azure Chiny 21Vianet zostaną wycofane z **29 lutego 2024**.

[Narzędzie do migracji](alerts-using-migration-tool.md) jest dostępne w Azure Portal, aby klienci mogli wyzwolić migrację. W tym artykule opisano proces migracji automatycznej w chmurze publicznej, który rozpocznie się po 31 maja 2021. Przedstawiono w nim również informacje o problemach i rozwiązaniach, które mogą zostać uruchomione.

## <a name="important-things-to-note"></a>Ważne rzeczy do zanotowania

Proces migracji umożliwia konwertowanie klasycznych reguł alertów na nowe, równoważne reguły alertów i tworzenie grup akcji. W przygotowaniu należy pamiętać o następujących kwestiach:

- Formaty ładunku powiadomień dla nowych reguł alertów różnią się od ładunków klasycznych reguł alertów, ponieważ obsługują one więcej funkcji. Jeśli masz klasyczną regułę alertów z aplikacjami logiki, elementami Runbook lub elementami webhook, mogą one przestać działać zgodnie z oczekiwaniami po migracji, z powodu różnic w ładunku. [Dowiedz się, jak przygotować się do migracji](alerts-prepare-migration.md).

- Niektórych klasycznych reguł alertów nie można migrować za pomocą narzędzia. [Dowiedz się, których reguł nie można migrować i co należy zrobić z nimi](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts).

## <a name="what-will-happen-during-the-automatic-migration-process-in-public-cloud"></a>Co się stanie w procesie migracji automatycznej w chmurze publicznej?

- Od 31 maja 2021 nie będzie można tworzyć nowych klasycznych reguł alertów, a migracja klasycznych alertów zostanie wyzwolona w partiach.
- Wszystkie klasyczne reguły alertów monitorujące usunięte zasoby docelowe lub [metryki, które nie są już obsługiwane,](alerts-understand-migration.md#classic-alert-rules-on-deprecated-metrics) są uznawane za nieprawidłowe.
- Reguły alertów klasycznych, które są nieprawidłowe, zostaną usunięte jakiś czas po 31 maja 2021.
- Po rozpoczęciu migracji subskrypcji powinna ona zostać zakończona w ciągu godziny. Klienci mogą monitorować stan migracji [Narzędzia migracji w Azure monitor](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel).
- Właściciele subskrypcji otrzymają wiadomość e-mail o powodzeniu lub niepowodzeniu migracji.

    > [!NOTE]
    > Jeśli nie chcesz czekać na uruchomienie procesu migracji automatycznej, można nadal wyzwolić migrację dobrowolnie przy użyciu narzędzia migracji.

## <a name="what-if-the-automatic-migration-fails"></a>Co zrobić, jeśli automatyczna migracja nie powiedzie się?

Gdy proces migracji automatycznej nie powiedzie się, właściciele subskrypcji otrzymają wiadomość e-mail z powiadomieniem o problemie. Możesz użyć narzędzia migracji w Azure Monitor, aby wyświetlić szczegółowe informacje o problemie. Zobacz [Przewodnik rozwiązywania problemów](alerts-understand-migration.md#common-problems-and-remedies) , aby uzyskać pomoc dotyczącą problemów, które można napotkać podczas migracji.

  > [!NOTE]
  > W przypadku konieczności działania od klientów, takich jak tymczasowe wyłączenie blokady zasobu lub zmiana przypisania zasad, klienci będą musieli rozwiązać te problemy. Jeśli problemy nie są rozwiązywane przez program, nie można zagwarantować pomyślnej migracji alertów klasycznych.

## <a name="next-steps"></a>Następne kroki

- [Przygotowanie do migracji](alerts-prepare-migration.md)
- [Informacje o sposobie działania narzędzia do migracji](alerts-understand-migration.md)