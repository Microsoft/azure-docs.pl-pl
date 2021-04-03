---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 617608f382d3331e59ae92c9eb272347c736b768
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91829224"
---
Funkcje umożliwiają korzystanie z kluczy, aby utrudnić dostęp do punktów końcowych funkcji HTTP podczas opracowywania. Jeśli poziom dostępu HTTP w funkcji wyzwalanej przez protokół HTTP nie jest ustawiony na `anonymous` , żądania muszą zawierać klucz dostępu interfejsu API w żądaniu. 

Chociaż klucze zapewniają domyślny mechanizm zabezpieczeń, warto rozważyć dodatkowe opcje zabezpieczania punktu końcowego HTTP w środowisku produkcyjnym. Na przykład zwykle nie jest dobrym sposobem dystrybuowania wspólnych kluczy tajnych w aplikacjach publicznych. Jeśli funkcja jest wywoływana z klienta publicznego, warto rozważyć zaimplementowanie innego mechanizmu zabezpieczeń. Aby dowiedzieć się więcej, zobacz temat [Zabezpieczanie punktu końcowego HTTP w środowisku produkcyjnym](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#secure-an-http-endpoint-in-production).

W przypadku odnowienia wartości klucza funkcji należy ręcznie ponownie przeprowadzić dystrybucję zaktualizowanych wartości klucza do wszystkich klientów, którzy wywołują funkcję.  

#### <a name="authorization-scopes-function-level"></a>Zakresy autoryzacji (na poziomie funkcji)

Istnieją dwa zakresy dostępu dla kluczy poziomu funkcji:

* **Funkcja**: te klucze dotyczą tylko określonych funkcji, w których są zdefiniowane. Gdy jest używany jako klucz interfejsu API, zezwala na dostęp tylko do tej funkcji.

* **Host**: klucze z zakresem hosta mogą służyć do uzyskiwania dostępu do wszystkich funkcji w aplikacji funkcji. Gdy jest używany jako klucz interfejsu API, zezwalają na dostęp do dowolnej funkcji w aplikacji funkcji. 

Każdy klucz ma nazwę dla odwołania i istnieje klucz domyślny (o nazwie "domyślny") na poziomie funkcji i hosta. Klucze funkcji mają pierwszeństwo przed Kluczami hosta. Gdy dwa klucze są zdefiniowane z tą samą nazwą, klucz funkcji jest zawsze używany.

#### <a name="master-key-admin-level"></a>Klucz główny (poziom administratora) 

Każda aplikacja funkcji ma również klucz hosta na poziomie administratora o nazwie `_master` . Oprócz zapewniania dostępu na poziomie hosta do wszystkich funkcji w aplikacji klucz główny zapewnia również dostęp administracyjny do interfejsów API REST środowiska uruchomieniowego. Nie można odwołać tego klucza. Po ustawieniu poziomu dostępu programu `admin` żądania muszą używać klucza głównego; wszelkie inne kluczowe wyniki w przypadku niepowodzenia dostępu.

> [!CAUTION]  
> Ze względu na podwyższony poziom uprawnień w aplikacji funkcji udzielanej przez klucz główny nie należy udostępniać tego klucza innym podmiotom lub rozpowszechniać go w natywnych aplikacjach klienckich. Podczas wybierania poziomu dostępu administratora należy zachować ostrożność.
