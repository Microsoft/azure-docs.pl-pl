---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 10/01/2020
ms.author: ccompy
ms.openlocfilehash: 481bd4f50eb527bcad2ba79b5ba4b9df4b872bfc
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91739916"
---
* Wielodostępne systemy obsługujące pełny zakres planów cenowych, z wyjątkiem izolowanych.
* App Service Environment, który wdraża w sieci wirtualnej i obsługuje aplikacje izolowanego planu cenowego.

Funkcja integracji sieci wirtualnej jest używana w aplikacjach wielodostępnych. Jeśli Twoja aplikacja znajduje się w [App Service Environment][ASEintro], jest już w sieci wirtualnej i nie wymaga użycia funkcji integracji sieci wirtualnej w celu uzyskania dostępu do zasobów w tej samej sieci wirtualnej. Aby uzyskać więcej informacji o wszystkich funkcjach sieciowych, zobacz [App Service funkcje sieciowe][Networkingfeatures].

Integracja z siecią wirtualną umożliwia aplikacji dostęp do zasobów w sieci wirtualnej, ale nie przyznaje prywatnego dostępu do aplikacji w sieci wirtualnej. Dostęp do lokacji prywatnej odnosi się do udostępniania aplikacji tylko z sieci prywatnej, na przykład z poziomu sieci wirtualnej platformy Azure. Integracja z siecią wirtualną służy tylko do wykonywania wywołań wychodzących z aplikacji do sieci wirtualnej. Funkcja integracja z siecią wirtualną działa inaczej, gdy jest używana z siecią wirtualną w tym samym regionie i w innych regionach. Funkcja integracji sieci wirtualnej ma dwie odmiany:

* **Integracja z regionalną**siecią wirtualną: w przypadku łączenia się z Azure Resource Manager sieciami wirtualnymi w tym samym regionie należy mieć dedykowaną podsieć w sieci wirtualnej, z którą jest integrowany program.
* **Integracja sieci wirtualnej wymagana przez bramę**: w przypadku łączenia się z siecią wirtualną w innych regionach lub w klasycznej sieci wirtualnej w tym samym regionie potrzebna jest brama usługi Azure Virtual Network obsługiwana w docelowej lokalizacji wirtualnej.

Funkcje integracji sieci wirtualnej:

* Wymagany jest plan cenowy Standard, Premium, PremiumV2, PremiumV3 lub elastyczny.
* Obsługa protokołów TCP i UDP.
* Pracuj z aplikacjami Azure App Service i aplikacjami funkcji.

Istnieje kilka rzeczy, które nie są obsługiwane przez integrację sieci wirtualnej, na przykład:

* Instalowanie dysku.
* Active Directory integrację.
* NetBIOS.

Integracja sieci wirtualnej wymagana przez bramę zapewnia dostęp do zasobów tylko w docelowej sieci wirtualnej lub w sieciach połączonych z docelową siecią wirtualną za pomocą komunikacji równorzędnej lub sieci VPN. Integracja sieci wirtualnej wymagana przez bramę nie umożliwia dostępu do zasobów dostępnych w ramach połączeń usługi Azure ExpressRoute lub współdziała z punktami końcowymi usługi.

Niezależnie od używanej wersji Integracja sieci wirtualnej zapewnia aplikacji dostęp do zasobów w sieci wirtualnej, ale nie przyznaje prywatnego dostępu do aplikacji z sieci wirtualnej. Dostęp do lokacji prywatnej odnosi się do udostępnienia aplikacji tylko z sieci prywatnej, na przykład z poziomu wirtualnej platformy Azure. Integracja z siecią wirtualną dotyczy tylko wykonywania połączeń wychodzących z aplikacji do sieci wirtualnej.

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features
