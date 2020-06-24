---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 5c3277394350036b8863185e83b7cfcd4975be1c
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081418"
---
Umożliwia ona rejestrację i Wyrejestrowanie z centrum powiadomień za pośrednictwem utworzonej usługi zaplecza. 

Alert jest wyświetlany, gdy zostanie określona akcja, a aplikacja znajduje się na pierwszym planie. W przeciwnym razie powiadomienia są wyświetlane w centrum powiadomień.

> [!NOTE]
> Wykonujesz zwykle akcje rejestracji (i wyrejestrowania) w odpowiednim punkcie cyklu życia aplikacji (lub jako część pierwszego uruchomienia środowiska) bez jawnego rejestrowania/wyrejestrowywania danych wejściowych użytkownika. Jednak w tym przykładzie będzie wymagane jawne wprowadzanie danych przez użytkownika, aby umożliwić ich eksplorowanie i przetestowanie.
