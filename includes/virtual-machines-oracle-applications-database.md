---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 06/01/2020
ms.author: danlep
ms.openlocfilehash: b9e275cbdaf264df3d0a40f032693fd93553e520
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84317650"
---
### <a name="database-tier"></a>Warstwa bazy danych

Warstwa bazy danych zawiera wystąpienia bazy danych dla aplikacji. Baza danych może być w systemie Oracle DB, Oracle RAC lub Oracle Exadata Database. 

Jeśli chcesz użyć Oracle DB, wystąpienie bazy danych może zostać wdrożone na platformie Azure za pośrednictwem Oracle DB obrazów dostępnych w witrynie Azure Marketplace. Alternatywnie, możesz użyć połączenia między platformą Azure i OCI, aby wdrożyć Oracle DB w modelu PaaS na OCI.

W przypadku certyfikatu Oracle RAC można użyć OCI w modelu PaaS. Zalecane jest użycie systemu dwuwęzłowego certyfikatu RAC. Chociaż istnieje możliwość wdrożenia programu Oracle RAC na platformie Azure CloudSimple w modelu IaaS, nie jest to obsługiwana konfiguracja przez firmę Oracle. Zapoznaj się z [programami Oracle uprawnionymi do autoryzowanych środowisk chmury](http://www.oracle.com/us/corporate/pricing/authorized-cloud-environments-3493562.pdf).

Na koniec w przypadku systemów Exadata Użyj połączenia OCI i Wdróż system Exadata w systemie OCI. Powyższy diagram architektury powyżej przedstawia system Exadata wdrożony w OCI w dwóch podsieciach.

W przypadku scenariuszy produkcyjnych Wdróż wiele wystąpień bazy danych w dwóch strefach dostępności (w przypadku wdrażania na platformie Azure) lub dwóch domen dostępności (w systemie OCI). Usługa Oracle Active Data Guard umożliwia synchronizowanie baz danych podstawowych i w stanie gotowości.

Warstwa bazy danych odbiera tylko żądania z warstwy środkowej. Zaleca się skonfigurowanie sieciowej grupy zabezpieczeń (w przypadku wdrażania bazy danych w systemie OCI), aby zezwalać na żądania na porcie 1521 z warstwy środkowej i portu 22 z serwera bastionu ze względów administracyjnych.

W przypadku baz danych wdrożonych w OCI należy skonfigurować oddzielną sieć wirtualną w chmurze przy użyciu bramy routingu dynamicznego (DRG), która jest połączona z obwodem usługi FastConnect.