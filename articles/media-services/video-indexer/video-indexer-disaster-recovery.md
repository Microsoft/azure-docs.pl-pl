---
title: Video Indexer tryb failover i odzyskiwanie po awarii
titleSuffix: Azure Media Services
description: Dowiedz się, jak przełączeć w tryb failover do pomocniczego konta Video Indexer w przypadku wystąpienia awarii lub awarii centrum danych.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 07/29/2019
ms.author: juliako
ms.openlocfilehash: 17c21900448fcb6d0a40fe5407f3b8bd62f9e3e4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "79499610"
---
# <a name="video-indexer-failover-and-disaster-recovery"></a>Video Indexer tryb failover i odzyskiwanie po awarii

Azure Media Services Video Indexer nie zapewnia natychmiastowej pracy w trybie failover usługi, jeśli istnieje regionalna awaria lub awaria centrum danych. W tym artykule wyjaśniono, jak skonfigurować środowisko do pracy w trybie failover w celu zapewnienia optymalnej dostępności aplikacji i zminimalizowanego czasu odzyskiwania w przypadku wystąpienia awarii.

Zalecamy skonfigurowanie odzyskiwania po awarii (BCDR) w różnych regionach regionalnych, aby korzystać z zasad izolacji i dostępności platformy Azure. Aby uzyskać więcej informacji, zobacz [regiony sparowane na platformie Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

## <a name="prerequisites"></a>Wymagania wstępne

Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, zarejestruj się, aby skorzystać z [bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/free/).

## <a name="failover-to-a-secondary-account"></a>Przełączenie w tryb failover na konto pomocnicze

Aby zaimplementować BCDR, musisz mieć dwa konta Video Indexer, aby obsłużyć nadmiarowość.

1. Utwórz dwa konta Video Indexer połączone z platformą Azure (zobacz [Tworzenie konta Video Indexer](connect-to-azure.md)). Utwórz jedno konto dla regionu podstawowego, a drugie dla sparowanego regionu platformy Azure.
1. Jeśli wystąpi awaria w regionie podstawowym, przełącz się do indeksowania przy użyciu konta pomocniczego.

> [!TIP]
> Możesz zautomatyzować BCDR przez skonfigurowanie alertów dziennika aktywności dla powiadomień o kondycji usługi, jak na potrzeby [tworzenia alertów dziennika aktywności w powiadomieniach usługi](../../service-health/alerts-activity-log-service-notifications.md).

Aby uzyskać informacje o używaniu wielu dzierżawców, zobacz [Zarządzanie wieloma dzierżawcami](manage-multiple-tenants.md). Aby zaimplementować BCDR, wybierz jedną z następujących opcji: [Video Indexer konto na dzierżawcę](manage-multiple-tenants.md#video-indexer-account-per-tenant) lub [subskrypcję platformy Azure dla dzierżawy](manage-multiple-tenants.md#azure-subscription-per-tenant).

## <a name="next-steps"></a>Następne kroki

[Zarządzanie kontem Video Indexer połączonym z platformą Azure](manage-account-connected-to-azure.md).
