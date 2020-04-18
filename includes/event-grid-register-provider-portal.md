---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 04/16/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 8833bb75f5f50372deda3e71c1df90bed9a20054
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605627"
---
## <a name="enable-event-grid-resource-provider"></a>Włączanie dostawcy zasobów usługi Event Grid

Jeśli usługa Event Grid nie była wcześniej używana w subskrypcji platformy Azure, może być konieczne zarejestrowanie dostawcy zasobów usługi Event Grid.

W witrynie Azure Portal:

1. Wybierz **subskrypcje** w menu po lewej stronie.
1. Wybierz subskrypcję, której używasz dla usługi Event Grid.
1. W menu po lewej stronie w obszarze **Ustawienia**wybierz pozycję **Dostawcy zasobów**.
1. Znajdź dostawcę **Microsoft.EventGrid**.
1. Jeśli nie jest on zarejestrowany, wybierz pozycję **Zarejestruj**. 

Ukończenie rejestracji może chwilę potrwać. Wybierz pozycję **Odśwież**, aby zaktualizować stan. Gdy **Stan** będzie miał wartość **Zarejestrowano**, możesz kontynuować.