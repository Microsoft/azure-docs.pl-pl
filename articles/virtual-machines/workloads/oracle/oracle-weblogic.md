---
title: Co to są rozwiązania do uruchamiania programu Oracle webWebLogicc Server na platformie Azure Virtual Machines
description: Dowiedz się, jak uruchomić serwer Oracle WebLogic na Microsoft Azure Virtual Machines.
author: rezar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 03/23/2021
ms.author: rezar
ms.openlocfilehash: 59bae8bfea29ee458288751209ffc860b5b9ce9d
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104956007"
---
# <a name="what-are-solutions-for-running-oracle-weblogic-server-on-azure-virtual-machines"></a>Jakie rozwiązania umożliwiają uruchamianie serwera Oracle WebLogic Server w usłudze Azure Virtual Machines?

Na tej stronie opisano rozwiązania do uruchamiania programu Oracle webWebLogicc Server (WLS) na maszynach wirtualnych platformy Azure. Te rozwiązania są wspólnie opracowywane i obsługiwane przez oprogramowanie Oracle i firmę Microsoft.

Usługę WLS można również uruchomić w usłudze Azure Kubernetes. Rozwiązania, które należy wykonać, zostały opisane w [tym artykule firmy Microsoft](./weblogic-aks.md).

WLS to wiodący serwer aplikacji Java, na którym działają niektóre z najważniejszych aplikacji Java przedsiębiorstwa na całym świecie. Usługi WLS tworzą podstawę programu pośredniczącego dla pakietu oprogramowania Oracle. Firmy Oracle i Microsoft są zaangażowane w umożliwienie klientom usługi WLS wyboru i elastyczności uruchamiania obciążeń na platformie Azure jako wiodącej platformy w chmurze.

Rozwiązania usługi Azure WLS mają na celu ułatwienie migracji aplikacji Java do maszyn wirtualnych platformy Azure. Rozwiązania tego problemu generują wdrożone zasoby dla najpopularniejszych scenariuszy aprowizacji w chmurze. Rozwiązania automatycznie udostępniają zasoby sieci wirtualnej, magazynu, środowiska Java, usługi WLS i systemu Linux. Przy minimalnym wysiłku serwer WebLogic jest instalowany. Rozwiązania mogą konfigurować zabezpieczenia za pomocą sieciowej grupy zabezpieczeń, równoważenia obciążenia za pomocą usługi Azure App Gateway, uwierzytelniania Azure Active Directory, scentralizowanego rejestrowania przy użyciu ELK i rozproszonego buforowania z spójnością programu Oracle. Możesz również automatycznie połączyć się z istniejącą bazą danych, w tym Azure PostgreSQL, Azure SQL i Oracle DB w chmurze Oracle lub na platformie Azure. 

:::image type="content" source="media/oracle-weblogic/wls-on-azure.gif" alt-text="Za pomocą Azure Portal można wdrożyć serwer WebLogic na platformie Azure":::

Dostępne są cztery oferty do realizacji różnych scenariuszy: [pojedynczy węzeł bez serwera administracyjnego](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls), [pojedynczy węzeł z serwerem administracyjnym](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin), [klastrem](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)i [klastrem dynamicznym](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster). Oferty są dostępne bezpłatnie. Te oferty zostały opisane i połączone poniżej.

_Oferty te są oferowane w ramach własnych licencji_. Zakładamy, że masz już odpowiednie licencje na oprogramowanie Oracle i są prawidłowo licencjonowane do uruchamiania ofert na platformie Azure.

Oferty obsługują różne wersje systemów operacyjnych, Java i WLS za pomocą obrazów podstawowych (takich jak WebLogic Server 14 i JDK 11 w Oracle Linux 7,6). Te obrazy podstawowe są również dostępne na platformie Azure. Obrazy podstawowe są odpowiednie dla klientów, którzy wymagają złożonych, niestandardowych wdrożeń platformy Azure. Bieżący zestaw obrazów podstawowych jest dostępny [tutaj](https://azuremarketplace.microsoft.com/marketplace/apps?search=WebLogic%20Server%20Base%20Image&page=1).

_Jeśli interesuje Cię ścisłą pracę nad scenariuszami migracji z zespołem inżynierów, opracowując te oferty, wybierz przycisk [kontakt ze mną](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview)_ na [stronie przeglądu Oferty Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview). Menedżery programów, architekty i inżynierowie będą wkrótce dotrzeć do Ciebie i zaczynają bliskią współpracę. Możliwość współpracy w ramach scenariusza migracji jest bezpłatna, gdy oferty są aktywne.

## <a name="oracle-weblogic-server-single-node"></a>Pojedynczy węzeł serwera Oracle WebLogic Server

[Ta oferta udostępnia](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls) pojedynczą maszynę wirtualną i instaluje ją w usłudze WLS. Nie tworzy domeny ani nie uruchamia serwera administracyjnego. Oferta jednego węzła jest przydatna w scenariuszach z wysoce dostosowaną konfiguracją domeny.

## <a name="oracle-weblogic-server-with-admin-server"></a>Serwer Oracle webWebLogicc Server z serwerem administracyjnym

[Ta oferta udostępnia](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin) pojedynczą maszynę wirtualną i instaluje ją w usłudze WLS. Tworzy domenę i uruchamia serwer administracyjny. Możesz zarządzać domeną i zacząć od razu rozpocząć pracę z wdrożeniami aplikacji.

## <a name="oracle-weblogic-server-cluster"></a>Klaster serwera Oracle WebLogic

[Ta oferta](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster) tworzy klaster o wysokiej dostępności dla maszyn wirtualnych z systemem WLS. Serwer administracyjny i wszystkie zarządzane serwery są uruchamiane domyślnie. Można zarządzać klastrem i od razu zacząć korzystać z aplikacji o wysokiej dostępności.

## <a name="oracle-weblogic-server-dynamic-cluster"></a>Dynamiczny klaster programu Oracle WebLogic Server

[Ta oferta](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster) tworzy wysoce dostępny i skalowalny dynamiczny klaster maszyn wirtualnych z systemem WLS. Serwer administracyjny i wszystkie zarządzane serwery są uruchamiane domyślnie.

Rozwiązania te umożliwiają szeroką gamę architektur wdrożenia gotowych do użycia w środowisku produkcyjnym. Większość przypadków migracji można osiągnąć w najbardziej wydajny sposób, co pozwala skupić się na tworzeniu aplikacji biznesowej.

:::image type="content" source="media/oracle-weblogic/weblogic-architecture-vms.png" alt-text="Złożone wdrożenia serwera WebLogic są włączone na platformie Azure":::

Oprócz tego, co jest automatycznie obsługiwane przez rozwiązania, klienci mają pełną elastyczność, aby jeszcze bardziej dostosować swoje wdrożenia. Prawdopodobnie na podstawie wdrażania aplikacji klienci będą integrować dalsze zasoby platformy Azure ze swoimi wdrożeniami. Zachęcamy klientów do [nawiązywania połączeń z zespołem programistycznym](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview) i przesyłania opinii na temat dalszej poprawy rozwiązań.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z ofertami na platformie Azure.

> [!div class="nextstepaction"]
> [Pojedynczy węzeł serwera Oracle WebLogic Server](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls)

> [!div class="nextstepaction"]
> [Serwer Oracle webWebLogicc Server z serwerem administracyjnym](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin)

> [!div class="nextstepaction"]
> [Klaster serwera Oracle WebLogic](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)

> [!div class="nextstepaction"]
> [Dynamiczny klaster programu Oracle WebLogic Server](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster)
