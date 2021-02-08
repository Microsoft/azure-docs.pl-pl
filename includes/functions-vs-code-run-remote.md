---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2021
ms.author: glenga
ms.openlocfilehash: 121b10cc568cce089c90e66b9c6f292c74f4acbe
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809573"
---
## <a name="run-the-function-in-azure"></a>Uruchamianie funkcji na platformie Azure

1. Wróć do obszaru **Azure: Functions** na pasku bocznym, rozwiń swoją subskrypcję, nową aplikację funkcji i **funkcje**. Kliknij prawym przyciskiem myszy (Windows) lub <kbd>Ctrl-</kbd> kliknij (macOS) `HttpExample` funkcję i wybierz pozycję **Wykonaj funkcję teraz...**.

    :::image type="content" source="media/functions-vs-code-run-remote/execute-function-now.png" alt-text="Wykonaj funkcję teraz na platformie Azure z Visual Studio Code":::

1. W obszarze **wprowadź treść żądania** zobaczysz wartość treść komunikatu żądania `{ "name": "Azure" }` . Naciśnij klawisz ENTER, aby wysłać ten komunikat żądania do funkcji.  

1. Gdy funkcja zostanie wykonana na platformie Azure i zwróci odpowiedź, w Visual Studio Code zostanie zgłoszone powiadomienie.
