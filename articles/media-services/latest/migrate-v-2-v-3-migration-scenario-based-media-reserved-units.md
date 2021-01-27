---
title: Wskazówki dotyczące migracji jednostek zarezerwowanych multimediów (MRUs)
description: W tym artykule przedstawiono wskazówki dotyczące scenariusza, w których można przeprowadzić migrację z Azure Media Services V2 do wersji v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 375b4ecdc6153162b3a819b831af152ce390a5dc
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898328"
---
# <a name="media-reserved-units-mrus-scenario-based-migration-guidance"></a>Wskazówki dotyczące migracji jednostek zarezerwowanych multimediów (MRUs)

![logo przewodnika migracji](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![kroki migracji 2](./media/migration-guide/steps-4.svg)

W tym artykule przedstawiono wskazówki dotyczące scenariusza, w których można przeprowadzić migrację z Azure Media Services V2 do wersji v3.

- W przypadku nowych kont v3 utworzonych w Azure Portal lub z wersją 2020-05-01 interfejsu API V3 nie trzeba już ustawiać jednostek zarezerwowanych multimediów (MRUs). System będzie teraz automatycznie skalowany w górę i w dół na podstawie obciążenia.
- Jeśli masz konto v3 lub v2, które zostało utworzone przed wersją 2020-05-01 interfejsu API, możesz nadal kontrolować współbieżność i wydajność zadań przy użyciu jednostek zarezerwowanych multimediów. Aby uzyskać więcej informacji, zobacz Scaling Media Processing (Skalowanie przetwarzania multimediów). Można zarządzać MRUs przy użyciu interfejsu wiersza polecenia 2,0 dla Media Services v3 lub przy użyciu Azure Portal.  
- Jeśli nie widzisz opcji zarządzania MRUs w Azure Portal, używasz konta, które zostało utworzone za pomocą interfejsu API 2020-05-01 lub nowszego.
- Jeśli znasz ustawienia typu MRU na S3, wydajność zostanie zwiększona lub pozostanie taka sama.
- Jeśli jesteś istniejącym klientem v2, musisz utworzyć nowe konto w wersji 2 w celu obsługi istniejącej aplikacji przed ukończeniem migracji. 
- Może być konieczne ponowne włączenie indeksatora w wersji 1 lub inne procesory nośników, które nie są w pełni przestarzałe. 

Aby uzyskać więcej informacji na temat MRUs, zobacz [jednostki zarezerwowane multimediów](concept-media-reserved-units.md) i [sposób skalowania jednostek zarezerwowanych multimediów](media-reserved-units-cli-how-to.md).

## <a name="mru-concepts-tutorials-and-how-to-guides"></a>Pojęcia MRU, samouczki i instrukcje dotyczące przewodników

### <a name="concepts"></a>Pojęcia

[Jednostki zarezerwowane multimediów](concept-media-reserved-units.md)

### <a name="how-to-guides"></a>Przewodniki z instrukcjami

[Jak skalować jednostki zarezerwowane multimediów](media-reserved-units-cli-how-to.md)

## <a name="samples"></a>Samples

Możesz również [porównać kod v2 i V3 w przykładach kodu](migrate-v-2-v-3-migration-samples.md).

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
