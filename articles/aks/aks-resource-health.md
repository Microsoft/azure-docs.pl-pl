---
title: Sprawdź, czy Resource Health zdarzenia mające wpływ na klaster AKS (wersja zapoznawcza)
description: Sprawdź stan kondycji klastra AKS przy użyciu Azure Resource Health.
services: container-service
author: yunjchoi
ms.topic: troubleshooting
ms.date: 08/18/2020
ms.author: yunjchoi
ms.openlocfilehash: a409ac944c51d893fc344f82ae83d97559e055ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92070660"
---
# <a name="check-for-resource-health-events-impacting-your-aks-cluster-preview"></a>Sprawdź, czy Resource Health zdarzenia mające wpływ na klaster AKS (wersja zapoznawcza)


Podczas uruchamiania obciążeń kontenerów w usłudze AKS, należy się upewnić, że można rozwiązać problemy i naprawić je, gdy tylko zajdzie taka potrzeba, aby zminimalizować wpływ na dostępność obciążeń. [Azure Resource Health](../service-health/resource-health-overview.md) zapewnia wgląd w różne zdarzenia dotyczące kondycji, które mogą spowodować, że klaster AKS będzie niedostępny.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="open-resource-health"></a>Otwórz Resource Health

### <a name="to-access-resource-health-for-your-aks-cluster"></a>Aby uzyskać dostęp do Resource Health klastra AKS:

- Przejdź do klastra AKS w [Azure Portal](https://portal.azure.com).
- Wybierz pozycję **Resource Health** na lewym pasku nawigacyjnym.

### <a name="to-access-resource-health-for-all-clusters-on-your-subscription"></a>Aby uzyskać dostęp do Resource Health wszystkich klastrów w ramach subskrypcji:

- Wyszukaj **Service Health** w [Azure Portal](https://portal.azure.com) i przejdź do niego.
- W lewym okienku nawigacji wybierz pozycję **kondycja zasobu** .
- Wybierz subskrypcję i ustaw typ zasobu na Azure Kubernetes Service (AKS).

![Zrzut ekranu przedstawia kondycję zasobów dla klastrów K S.](./media/aks-resource-health/resource-health-check.png)

## <a name="check-the-health-status"></a>Sprawdź stan kondycji

Azure Resource Health pomaga zdiagnozować i uzyskać pomoc techniczną dotyczącą problemów z usługami, które mają wpływ na zasoby platformy Azure. Resource Health raporty dotyczące bieżącej i wcześniejszej kondycji zasobów oraz pozwalają określić, czy problem jest spowodowany przez akcję zainicjowaną przez użytkownika, czy też zdarzenie platformy.

Resource Health odbiera sygnały dla zarządzanego klastra w celu określenia stanu kondycji klastra. Bada stan kondycji klastra AKS i zgłasza akcje wymagane dla każdego sygnału kondycji. Sygnały te są różne od rozwiązywania problemów, planowanych aktualizacji, nieplanowanych zdarzeń związanych z kondycją i niedostępności spowodowanych przez akcje inicjowane przez użytkownika. Te sygnały są klasyfikowane przy użyciu stanu kondycji Azure Resource Health: *dostępne*, *niedostępne*, *nieznane* i o *obniżonej wydajności*.

- **Dostępne**: Jeśli nie ma żadnych znanych problemów wpływających na kondycję klastra, Resource Health zgłasza klaster jako *dostępny*.

- **Niedostępne**: w przypadku istnienia platformy lub zdarzenia niezwiązanego z platformą, która wpływa na kondycję klastra, Resource Health zgłasza klaster jako *niedostępny*.

- **Nieznane**: gdy istnieje tymczasowa utrata połączenia z metrykami kondycji klastra, Resource Health zgłasza klaster jako *nieznany*.

- **Obniżony** poziom: Jeśli wystąpi problem z kondycją wymagający działania, Resource Health zgłasza klaster jako *obniżony*.

Aby uzyskać dodatkowe informacje na temat tego, co wskazuje każdy stan kondycji, odwiedź [Resource Health Omówienie](../service-health/resource-health-overview.md#health-status).

### <a name="view-historical-data"></a>Wyświetlanie danych historycznych

Możesz również wyświetlić informacje historyczne Resource Health z ostatnich 30 dni w sekcji **historia kondycji** .

## <a name="next-steps"></a>Następne kroki

Uruchom testy w klastrze, aby rozwiązać problemy z klastrem za pomocą [diagnostyki AKS](./concepts-diagnostics.md).