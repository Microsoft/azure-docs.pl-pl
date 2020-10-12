---
title: Parametry połączenia magazynu obrazu Service Fabric platformy Azure
description: Dowiedz się więcej na temat parametrów połączenia magazynu obrazu, w tym ich zastosowań i aplikacji do klastra Service Fabric.
author: alexwun
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: alexwun
ms.openlocfilehash: 8fc0239dd18fc7071823a129a7dbc4f102023d66
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86246201"
---
# <a name="understand-the-imagestoreconnectionstring-setting"></a>Omówienie ustawienia ImageStoreConnectionString

W niektórych naszych dokumentach krótko wspominamy o istnieniu parametru "ImageStoreConnectionString" bez opisywania, co naprawdę robi. Po przeprowadzeniu tego artykułu, takiego jak [wdrażanie i usuwanie aplikacji przy użyciu programu PowerShell][10], wygląda na to, że wszystko jest kopiowane/wklejane, jak pokazano w manifeście klastra klastra docelowego. Dlatego ustawienie musi być konfigurowalne dla klastra, ale podczas tworzenia klastra za pomocą [Azure Portal][11]nie jest dostępna opcja konfigurowania tego ustawienia i zawsze "Sieć szkieletowa: magazynu ImageStore". Co to jest cel tego ustawienia?

![Manifest klastra][img_cm]

Service Fabric wyłączone jako platforma dla wewnętrznego użytku firmy Microsoft przez wiele różnorodnych zespołów, więc niektóre aspekty tego typu są wysoce dostosowywane — "Magazyn obrazów" jest jednym z tych aspektów. Zasadniczo Magazyn obrazów to podłączane repozytorium do przechowywania pakietów aplikacji. Gdy aplikacja zostanie wdrożona w węźle klastra, ten węzeł pobierze zawartość pakietu aplikacji z Magazyn obrazów. ImageStoreConnectionString jest ustawieniem obejmującym wszystkie niezbędne informacje dla klientów i węzłów, aby znaleźć odpowiednie Magazyn obrazów dla danego klastra.

Obecnie istnieją trzy możliwe rodzaje Magazyn obrazów dostawców i odpowiadające im parametry połączenia są następujące:

1. Usługa Magazyn obrazów: "Sieć szkieletowa: magazynu ImageStore"

2. System plików: "plik: [ścieżka systemu plików]"

3. Azure Storage: "xstore: DefaultEndpointsProtocol = https; AccountName = [...]; AccountKey = [...]; Kontener = [...] "

Typ dostawcy używany w środowisku produkcyjnym to usługa Magazyn obrazów, która jest stanową utrwalaną usługą systemową, którą można zobaczyć z Service Fabric Explorer. 

![Usługa Magazyn obrazów][img_is]

Hosting Magazyn obrazów w usłudze systemowej w ramach klastra eliminuje zależności zewnętrzne dla repozytorium pakietów i daje nam większą kontrolę nad zaistnieniem magazynu. Przyszłe ulepszenia dotyczące Magazyn obrazów mogą najpierw kierować do dostawcy Magazyn obrazów, jeśli nie jest to wyłącznie. Parametry połączenia dla dostawcy usług Magazyn obrazów nie mają żadnych unikatowych informacji, ponieważ klient jest już połączony z klastrem docelowym. Klient musi tylko wiedzieć, że powinny być używane protokoły przeznaczone dla usługi systemowej.

Dostawca systemu plików jest używany zamiast usługi Magazyn obrazów lokalnych klastrów jednobox podczas opracowywania, aby nieco szybciej zainicjować klaster. Różnica jest zwykle mała, ale jest to przydatna Optymalizacja dla większości osób podczas opracowywania. Istnieje możliwość wdrożenia lokalnego klastra jednorazowego z innymi typami dostawcy magazynu, ale zazwyczaj nie jest to konieczne, ponieważ przepływ pracy tworzenia/testowania pozostaje taki sam niezależnie od dostawcy. Dostawca usługi Azure Storage istnieje tylko w przypadku starszej obsługi starych klastrów wdrożonych przed wprowadzeniem dostawcy usług Magazyn obrazów.

Ponadto nie należy używać dostawcy systemu plików ani dostawcy usługi Azure Storage jako metody udostępniania Magazyn obrazów między wieloma klastrami — spowoduje to uszkodzenie danych konfiguracji klastra, ponieważ każdy klaster może zapisywać dane powodujące konflikt z Magazyn obrazów. Aby udostępnić udostępniane pakiety aplikacji między wieloma klastrami, należy zamiast tego użyć plików [sfpkg][12] , które mogą być przekazywane do dowolnego zewnętrznego magazynu z identyfikatorem URI pobierania.

Mimo że ImageStoreConnectionString można skonfigurować, wystarczy użyć ustawienia domyślnego. W przypadku publikowania na platformie Azure za pomocą programu Visual Studio, parametr jest automatycznie ustawiany odpowiednio. W celu wdrożenia programistycznego w klastrach hostowanych na platformie Azure parametry połączenia to zawsze "Sieć szkieletowa: magazynu ImageStore". Chociaż w razie wątpliwości, jego wartość może być zawsze weryfikowana przez pobranie manifestu klastra przez program [PowerShell](/powershell/module/servicefabric/get-servicefabricclustermanifest), [.NET](/previous-versions/azure/reference/mt161375(v=azure.100))lub [rest](/rest/api/servicefabric/get-a-cluster-manifest). Zarówno lokalne klastry testowe, jak i produkcyjne powinny być zawsze skonfigurowane do korzystania z Magazyn obrazów dostawcy usług.

### <a name="next-steps"></a>Następne kroki
[Wdrażanie i usuwanie aplikacji przy użyciu programu PowerShell][10]

<!--Image references-->
[img_is]: ./media/service-fabric-image-store-connection-string/image_store_service.png
[img_cm]: ./media/service-fabric-image-store-connection-string/cluster_manifest.png

[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-cluster-creation-via-portal.md
[12]: service-fabric-package-apps.md#create-an-sfpkg
