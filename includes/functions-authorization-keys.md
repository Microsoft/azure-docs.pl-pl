---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 926434d7110877e234888682cb6c946afe3ae685
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648948"
---
Funkcje umożliwiają korzystanie z kluczy, aby utrudnić dostęp do punktów końcowych funkcji HTTP podczas opracowywania. Jeśli poziom dostępu HTTP w funkcji wyzwalanej przez protokół HTTP nie jest ustawiony na `anonymous` , żądania muszą zawierać klucz dostępu interfejsu API w żądaniu. 

#### <a name="authorization-scopes-function-level"></a>Zakresy autoryzacji (na poziomie funkcji)

Istnieją dwa zakresy dostępu dla kluczy poziomu funkcji:

* **Funkcja**: te klucze dotyczą tylko określonych funkcji, w których są zdefiniowane. Gdy jest używany jako klucz interfejsu API, zezwala na dostęp tylko do tej funkcji.

* **Host**: klucze z zakresem hosta mogą służyć do uzyskiwania dostępu do wszystkich funkcji w aplikacji funkcji. Gdy jest używany jako klucz interfejsu API, zezwalają na dostęp do dowolnej funkcji w aplikacji funkcji. 

Każdy klucz ma nazwę dla odwołania i istnieje klucz domyślny (o nazwie "domyślny") na poziomie funkcji i hosta. Klucze funkcji mają pierwszeństwo przed Kluczami hosta. Gdy dwa klucze są zdefiniowane z tą samą nazwą, klucz funkcji jest zawsze używany.

#### <a name="master-key-admin-level"></a>Klucz główny (poziom administratora) 

Każda aplikacja funkcji ma również klucz hosta na poziomie administratora o nazwie `_master` . Oprócz zapewniania dostępu na poziomie hosta do wszystkich funkcji w aplikacji klucz główny zapewnia również dostęp administracyjny do interfejsów API REST środowiska uruchomieniowego. Nie można odwołać tego klucza. Po ustawieniu poziomu dostępu programu `admin` żądania muszą używać klucza głównego; wszelkie inne kluczowe wyniki w przypadku niepowodzenia dostępu.

> [!CAUTION]  
> Ze względu na podwyższony poziom uprawnień w aplikacji funkcji udzielanej przez klucz główny nie należy udostępniać tego klucza innym podmiotom lub rozpowszechniać go w natywnych aplikacjach klienckich. Podczas wybierania poziomu dostępu administratora należy zachować ostrożność.
