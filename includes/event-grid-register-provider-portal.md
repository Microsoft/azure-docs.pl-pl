---
title: plik dołączania
description: plik dołączania
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 04/16/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 8833bb75f5f50372deda3e71c1df90bed9a20054
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "81605627"
---
## <a name="enable-event-grid-resource-provider"></a>Włączanie dostawcy zasobów usługi Event Grid

Jeśli usługa Event Grid nie była wcześniej używana w subskrypcji platformy Azure, może być konieczne zarejestrowanie dostawcy zasobów usługi Event Grid.

W witrynie Azure Portal:

1. W menu po lewej stronie wybierz pozycję **subskrypcje** .
1. Wybierz subskrypcję, której używasz dla usługi Event Grid.
1. W menu po lewej stronie w obszarze **Ustawienia**wybierz pozycję **dostawcy zasobów**.
1. Znajdź dostawcę **Microsoft.EventGrid**.
1. Jeśli nie jest on zarejestrowany, wybierz pozycję **Zarejestruj**. 

Ukończenie rejestracji może chwilę potrwać. Wybierz pozycję **Odśwież**, aby zaktualizować stan. Gdy **Stan** będzie miał wartość **Zarejestrowano**, możesz kontynuować.