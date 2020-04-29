---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 8106b1a77f11874b1b13f41bfc9e62ea800afc98
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "68361533"
---
### <a name="database-tier"></a>Warstwa bazy danych

Warstwa bazy danych zawiera wystąpienia bazy danych dla aplikacji. Baza danych może być w systemie Oracle DB, Oracle RAC lub Oracle Exadata Database. 

Jeśli chcesz użyć Oracle DB, wystąpienie bazy danych może zostać wdrożone na platformie Azure za pośrednictwem Oracle DB obrazów dostępnych w witrynie Azure Marketplace. Alternatywnie, możesz użyć połączenia między platformą Azure i OCI, aby wdrożyć Oracle DB w modelu PaaS na OCI.

W przypadku certyfikatu Oracle RAC można wdrożyć certyfikat certyfikatu Oracle w systemie Azure CloudSimple w modelu IaaS lub w modelu OCI w PaaS model. Zalecane jest użycie systemu dwuwęzłowego certyfikatu RAC. 

Na koniec w przypadku systemów Exadata Użyj połączenia OCI i Wdróż system Exadata w systemie OCI. Powyższy diagram architektury powyżej przedstawia system Exadata wdrożony w OCI w dwóch podsieciach.

W przypadku scenariuszy produkcyjnych Wdróż wiele wystąpień bazy danych w dwóch strefach dostępności (w przypadku wdrażania na platformie Azure) lub dwóch domen dostępności (w systemie OCI). Usługa Oracle Active Data Guard umożliwia synchronizowanie baz danych podstawowych i w stanie gotowości.

Warstwa bazy danych odbiera tylko żądania z warstwy środkowej. Zaleca się skonfigurowanie sieciowej grupy zabezpieczeń (w przypadku wdrażania bazy danych w systemie OCI), aby zezwalać na żądania na porcie 1521 z warstwy środkowej i portu 22 z serwera bastionu ze względów administracyjnych.

W przypadku baz danych wdrożonych w OCI należy skonfigurować oddzielną sieć wirtualną w chmurze przy użyciu bramy routingu dynamicznego (DRG), która jest połączona z obwodem usługi FastConnect.