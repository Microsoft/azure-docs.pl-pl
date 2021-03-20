---
title: Tryb failover i odzyskiwanie po awarii w usłudze Video Indexer
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
ms.openlocfilehash: eab376c44065979de86e5c70b796be952fccffaa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87065415"
---
# <a name="video-indexer-failover-and-disaster-recovery"></a>Tryb failover i odzyskiwanie po awarii w usłudze Video Indexer

Usługa Video Indexer w ramach usługi Azure Media Services nie zapewnia natychmiastowego trybu failover usługi w razie wystąpienia awarii lub błędu w regionalnym centrum danych. W tym artykule wyjaśniono, jak skonfigurować środowisko do pracy w trybie failover w celu zapewnienia optymalnej dostępności aplikacji i zminimalizowanego czasu odzyskiwania w przypadku wystąpienia awarii.

Zalecamy skonfigurowanie ciągłości działania i odzyskiwania po awarii (BCDR) w różnych parach regionalnych, aby móc korzystać z zasad dostępności i izolacji platformy Azure. Aby uzyskać więcej informacji, zobacz [regiony sparowane na platformie Azure](../../best-practices-availability-paired-regions.md).

## <a name="prerequisites"></a>Wymagania wstępne

Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, zarejestruj się, aby skorzystać z [bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/free/).

## <a name="failover-to-a-secondary-account"></a>Przełączenie w tryb failover na konto pomocnicze

Aby zaimplementować BCDR, musisz mieć dwa konta Video Indexer, aby obsłużyć nadmiarowość.

1. Utwórz dwa konta Video Indexer połączone z platformą Azure (zobacz [Tworzenie konta Video Indexer](connect-to-azure.md)). Utwórz jedno konto dla regionu podstawowego, a drugie dla sparowanego regionu platformy Azure.
1. Jeśli wystąpi awaria w regionie podstawowym, przełącz się do indeksowania przy użyciu konta pomocniczego.

> [!TIP]
> Możesz zautomatyzować BCDR przez skonfigurowanie alertów dziennika aktywności dla powiadomień o kondycji usługi, jak na potrzeby [tworzenia alertów dziennika aktywności w powiadomieniach usługi](../../service-health/alerts-activity-log-service-notifications-portal.md).

Aby uzyskać informacje o używaniu wielu dzierżawców, zobacz [Zarządzanie wieloma dzierżawcami](manage-multiple-tenants.md). Aby zaimplementować BCDR, wybierz jedną z następujących opcji: [Video Indexer konto na dzierżawcę](manage-multiple-tenants.md#video-indexer-account-per-tenant) lub [subskrypcję platformy Azure dla dzierżawy](manage-multiple-tenants.md#azure-subscription-per-tenant).

## <a name="next-steps"></a>Następne kroki

[Zarządzanie kontem Video Indexer połączonym z platformą Azure](manage-account-connected-to-azure.md).
