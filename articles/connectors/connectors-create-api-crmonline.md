---
title: Łączenie z usługą Dynamics 365
description: Utwórz rekordy systemu Dynamics 365 i zarządzaj nimi za pomocą Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jdaly, logicappspm
ms.topic: conceptual
ms.date: 05/09/2020
tags: connectors
ms.openlocfilehash: 00bf8ea2b783e09711a95f203bdfcce0e6b90b2c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82994300"
---
# <a name="create-and-manage-records-in-dynamics-365-by-using-azure-logic-apps"></a>Tworzenie rekordów i zarządzanie nimi w usłudze Dynamics 365 przy użyciu Azure Logic Apps

Dynamics 365 używa [Common Data Service](https://docs.microsoft.com/powerapps/maker/common-data-service/data-platform-intro). W przypadku połączeń z usługą Dynamics 365 Użyj [łącznika Common Data Service](https://docs.microsoft.com/connectors/commondataservice/).

> [!IMPORTANT]
> [Łącznik Dynamics 365](https://docs.microsoft.com/connectors/dynamicscrmonline/) jest przestarzały, ale nadal działa do momentu jego usunięcia. Nie należy używać łącznika usługi Dynamics 365 dla nowych aplikacji logiki. Nie zapowiedziano jeszcze żadnych osi czasu na usunięcie łącznika usługi Dynamics 365. Konwersja istniejących aplikacji logiki na łącznik Common Data Service ani inny planowany łącznik nie jest konieczna, jednak trzeba będzie je przekonwertować po usunięciu obecnego łącznika. Aby uzyskać więcej informacji, zobacz [Łącznik Dynamics 365 jest przestarzały](https://docs.microsoft.com/power-platform/important-changes-coming).
>
> [Łącznik Common Data Service](https://docs.microsoft.com/connectors/commondataservice/) zapewnia te same możliwości, co przestarzały łącznik Dynamics 365, a do tego zawiera poprawki zwiększające niezawodność. Informacje o korzystaniu z łącznika Common Data Service w aplikacjach logiki znajdziesz w artykule [Create and manage Common Data Service records by using Azure Logic Apps](../connectors/connect-common-data-service.md) (Tworzenie rekordów Common Data Service i zarządzanie nimi za pomocą usługi Azure Logic Apps).

Aby uzyskać więcej informacji na temat Common Data Service, zobacz następujące tematy:

* [Dowiedz się: Rozpoczynanie pracy z usługą Common Data Service](https://docs.microsoft.com/learn/modules/get-started-with-powerapps-common-data-service/)
* [Dowiedz się: łączenie i analizowanie danych z usługi Dynamics 365 przy użyciu platformy i Common Data Service](https://docs.microsoft.com/learn/wwl/connect-analyze-dynamics-365-data/)