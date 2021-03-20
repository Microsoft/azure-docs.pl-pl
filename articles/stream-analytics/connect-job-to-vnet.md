---
title: Łączenie Stream Analytics zadań z zasobami w usłudze Azure Virtual Network (VNET)
description: W tym artykule opisano sposób łączenia Azure Stream Analytics zadania z zasobami, które znajdują się w sieci wirtualnej.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/04/2021
ms.custom: devx-track-js
ms.openlocfilehash: 4701cb4122b4196b08b2a427b34d49c7784b91a7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98878243"
---
# <a name="connect-stream-analytics-jobs-to-resources-in-an-azure-virtual-network-vnet"></a>Łączenie Stream Analytics zadań z zasobami w usłudze Azure Virtual Network (VNet)

Zadania Stream Analytics umożliwiają nawiązywanie połączeń wychodzących z danymi wejściowymi i wyjściowymi platformy Azure w celu przetwarzania danych w czasie rzeczywistym i generowania wyników. Te zasoby wejściowe i wyjściowe (na przykład Azure Event Hubs i Azure SQL Database) mogą znajdować się za zaporą platformy Azure lub w Virtual Network platformy Azure. Usługa Stream Analytics działa z sieci, które nie mogą być bezpośrednio uwzględniane w regułach sieci.

Istnieją jednak dwa sposoby bezpiecznego łączenia Stream Analytics zadań z zasobami wejściowymi i wyjściowymi w takich scenariuszach.
* Używanie prywatnych punktów końcowych w klastrach Stream Analytics.
* Używanie trybu uwierzytelniania tożsamości zarządzanej połączonego z ustawieniem sieci "Zezwalaj na zaufane usługi".

Zadanie Stream Analytics nie akceptuje żadnego połączenia przychodzącego.

## <a name="private-endpoints-in-stream-analytics-clusters"></a>Prywatne punkty końcowe w klastrach Stream Analytics.
[Klastry Stream Analytics](./cluster-overview.md) to dedykowany klaster obliczeniowy o pojedynczej dzierżawie, który umożliwia uruchamianie Stream Analytics zadań. Można utworzyć zarządzane prywatne punkty końcowe w klastrze Stream Analytics, co umożliwia wykonywanie wszystkich zadań uruchomionych w klastrze w celu nawiązania bezpiecznego połączenia wychodzącego z zasobami wejściowymi i wyjściowymi.

Tworzenie prywatnych punktów końcowych w klastrze Stream Analytics jest [operacją dwuetapową](./private-endpoints.md). Ta opcja jest najlepszym rozwiązaniem dla średnich i dużych obciążeń przesyłania strumieniowego, ponieważ minimalnym rozmiarem klastra Stream Analytics jest 36 usług SUs (mimo 36 że usługi SUs można współużytkować przez różne zadania w różnych subskrypcjach lub środowiskach, takich jak programowanie, testowanie i produkcja).

## <a name="managed-identity-authentication-with-allow-trusted-services-configuration"></a>Uwierzytelnianie tożsamości zarządzanej z konfiguracją "Zezwalaj na zaufane usługi"
Niektóre usługi platformy Azure oferują ustawienie **Zaufane sieci usług firmy Microsoft** , które po włączeniu umożliwia Stream Analytics zadań do bezpiecznego łączenia się z zasobem przy użyciu silnego uwierzytelniania. Ta opcja umożliwia łączenie zadań z zasobami wejściowymi i wyjściowymi bez konieczności Stream Analytics klastra i prywatnych punktów końcowych. Skonfigurowanie zadania do korzystania z tej techniki jest operacją dwuetapową:
* Użyj trybu uwierzytelniania tożsamości zarządzanej podczas konfigurowania danych wejściowych lub wyjściowych w zadaniu Stream Analytics.
* Przyznaj określonemu Stream Analytics zadania jawne dostęp do zasobów docelowych, przypisując rolę platformy Azure do zarządzanej tożsamości przypisanej do systemu. 

Włączenie **zezwalania zaufanym usługom firmy Microsoft** nie udziela dostępu zbiorczego do żadnych zadań. Zapewnia to pełną kontrolę nad tym, które konkretne Stream Analytics zadania mogą bezpiecznie uzyskiwać dostęp do zasobów. 

Zadania mogą łączyć się z następującymi usługami platformy Azure przy użyciu tej metody:
1. [BLOB Storage lub Azure Data Lake Storage Gen2](./blob-output-managed-identity.md) — może to być konto magazynu zadania, dane wejściowe lub wyjściowe przesyłania strumieniowego.
2. [Event Hubs platformy Azure](./event-hubs-managed-identity.md) — może to być dane wejściowe lub wyjściowe przesyłania strumieniowego.

Jeśli zadania muszą łączyć się z innymi typami wejściowymi lub wyjściowymi, można napisać od Stream Analytics, aby Event Hubs dane wyjściowe najpierw, a następnie do dowolnie wybranego miejsca docelowego przy użyciu Azure Functions. Jeśli chcesz bezpośrednio pisać z Stream Analytics do innych typów wyjściowych zabezpieczonych w sieci wirtualnej lub zaporze, jedyną opcją jest użycie prywatnych punktów końcowych w klastrach Stream Analytics.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie i usuwanie prywatnych punktów końcowych w klastrach Stream Analytics](./private-endpoints.md)
* [Nawiązywanie połączenia z Event Hubs w sieci wirtualnej przy użyciu uwierzytelniania tożsamości zarządzanej](./event-hubs-managed-identity.md)
* [Łączenie się z usługą BLOB Storage i ADLS Gen2 w sieci wirtualnej przy użyciu uwierzytelniania tożsamości zarządzanej](./blob-output-managed-identity.md)