---
title: Konfigurowanie aktywnej replikacji geograficznej usługi Azure cache dla wystąpień Redis
description: Dowiedz się, jak replikować pamięć podręczną platformy Azure dla wystąpień Redis przedsiębiorstwa w regionach platformy Azure
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: fe777c3aa7b314dc56a42cc64712d18281a6ea7d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121171"
---
# <a name="configure-active-geo-replication-for-enterprise-azure-cache-for-redis-instances-preview"></a>Konfigurowanie aktywnej replikacji geograficznej usługi Azure cache dla wystąpień Redis (wersja zapoznawcza)

W tym artykule dowiesz się, jak skonfigurować aktywną geograficzną pamięć podręczną platformy Azure przy użyciu Azure Portal.

Aktywne grupy replikacji geograficznej — dwie lub więcej pamięci podręcznej platformy Azure dla wystąpień Redis w jednej pamięci podręcznej, która obejmuje wiele regionów świadczenia usługi Azure. Wszystkie wystąpienia działają jako Primaries lokalny. Aplikacja decyduje o wystąpieniach, które mają być używane na potrzeby żądań odczytu i zapisu.

## <a name="create-or-join-an-active-geo-replication-group"></a>Tworzenie aktywnej grupy replikacji geograficznej lub dołączanie do niej

> [!IMPORTANT]
> Podczas tworzenia pamięci podręcznej platformy Azure dla usługi Redis należy włączyć aktywną replikację geograficzną.
>
>

1. W nowym interfejsie użytkownika tworzenia **Redis Cache** kliknij pozycję **Konfiguruj** , aby skonfigurować **aktywną replikację geograficzną** na karcie **Zaawansowane** .

    ![Konfigurowanie aktywnej replikacji geograficznej](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-not-configured.png)

1. Utwórz nową grupę replikacji, dla pierwszej instancji pamięci podręcznej lub wybierz istniejącą z listy.

    ![Buforuj linki](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-new-group.png)

1. Kliknij pozycję **Konfiguruj** , aby zakończyć.

    ![Skonfigurowano aktywną replikację geograficzną](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-configured.png)

1. Powtórz powyższe kroki dla każdego dodatkowego wystąpienia pamięci podręcznej w grupie replikacji geograficznej.

## <a name="remove-from-an-active-geo-replication-group"></a>Usuń z aktywnej grupy replikacji geograficznej

Aby usunąć wystąpienie pamięci podręcznej z aktywnej grupy replikacji geograficznej, po prostu usuń to wystąpienie. Pozostałe wystąpienia spowodują samodzielną konfigurację automatycznie.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o funkcjach usługi Azure cache for Redis.

* [Pamięć podręczna platformy Azure dla warstw usług Redis](cache-overview.md#service-tiers)
* [Wysoka dostępność dla usługi Azure cache for Redis](cache-high-availability.md)
