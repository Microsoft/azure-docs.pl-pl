---
title: Co to jest Oracle webWebLogicc Server na platformie Azure?
description: Dowiedz się, jak uruchomić serwer Oracle WebLogic na Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: edburns
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2020
ms.author: edburns
ms.openlocfilehash: fe247e75040f658beb94a66176f802993268a7d8
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87851866"
---
# <a name="what-is-oracle-weblogic-server-on-azure"></a>Co to jest Oracle webWebLogicc Server na platformie Azure?

Na tej stronie opisano rozwiązania do uruchamiania programu WebLogic Server (WLS) na platformie Azure Virtual Machines.  Te rozwiązania są wspólnie opracowywane przez oprogramowanie Oracle i firmę Microsoft.

Oracle webWebLogicc Server to pierwszy, natywny chmurowy serwer aplikacji platformy Java dla przedsiębiorstw na potrzeby tworzenia i wdrażania aplikacji rozproszonego przedsiębiorstwa wielowarstwowego. Oferty serwera usługi Azure WebLogic umożliwiają wdrażanie rozwiązań w chmurze.  Będziesz mieć większy wybór i elastyczność migracji WebLogic, w tym Podnieś i Przenieś aplikacje Java EE do chmury platformy Azure.   Usługa WLS na platformie Azure daje duży wpływ na niewielki nakład pracy. Oferty pozwalają na szybkie rozpoczęcie pracy z aplikacjami biznesowymi (LOB).  Każda oferta automatycznie inicjuje obsługę zasobów sieci wirtualnej, magazynu i systemu Linux.  W przypadku zerowego wysiłku serwer WebLogic jest instalowany.  Usługa WLS na platformie Azure konfiguruje zabezpieczenia za pomocą sieciowej grupy zabezpieczeń, równoważenia obciążenia za pomocą usługi Azure App Gateway, uwierzytelniania przy użyciu Azure Active Directory i automatycznie nawiązuje połączenie z istniejącą bazą danych.

:::image type="content" source="media/oracle-weblogic/wls-on-azure.gif" alt-text="Za pomocą witryny Azure Portal możesz wdrożyć serwer WebLogic na platformie Azure":::

Dostępne są cztery oferty do realizacji różnych scenariuszy: pojedynczy węzeł bez serwera administracyjnego, pojedynczy węzeł z serwerem administracyjnym, klastrem i klastrem dynamicznym.  Wypróbuj oferty, które są dostępne bezpłatnie.

_Oferty te są oferowane w ramach własnych licencji_. Zakładamy, że masz już odpowiednie licencje na oprogramowanie Oracle i są prawidłowo licencjonowane do uruchamiania ofert w Microsoft Azure.

_Jeśli chcesz ściśle współpracować z scenariuszami migracji z zespołem inżynieryjnym, opracowując te oferty, wybierz przycisk [kontakt ze mną](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview) _ w [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview). Menedżery programów, architekty i inżynierowie będą mogli wkrótce przejść do swojej współpracy.

## <a name="oracle-weblogic-server-single-node"></a>Pojedynczy węzeł serwera Oracle WebLogic Server

Ta oferta udostępnia pojedynczą maszynę wirtualną i instaluje ją w usłudze WLS. Nie tworzy domeny ani nie uruchamia serwera administracyjnego. Pojedynczy węzeł jest przydatny w scenariuszach z wysoce dostosowaną konfiguracją domeny.

## <a name="oracle-weblogic-server-with-admin-server"></a>Serwer Oracle webWebLogicc Server z serwerem administracyjnym

Ta oferta udostępnia pojedynczą maszynę wirtualną i instaluje ją w usłudze WLS. Tworzy domenę i uruchamia serwer administracyjny, który umożliwia zarządzanie domeną.

## <a name="oracle-weblogic-server-cluster"></a>Klaster serwera Oracle WebLogic

Ta oferta tworzy klaster o wysokiej dostępności dla maszyn wirtualnych z systemem WLS. Serwer administracyjny i wszystkie zarządzane serwery są uruchamiane domyślnie, co umożliwia zarządzanie domeną.

## <a name="oracle-weblogic-server-dynamic-cluster"></a>Dynamiczny klaster programu Oracle WebLogic Server

Ta oferta tworzy wysoce dostępny i skalowalny dynamiczny klaster maszyn wirtualnych z systemem WLS. Serwer administracyjny i wszystkie zarządzane serwery są uruchamiane domyślnie, co umożliwia zarządzanie domeną.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z ofertami w portalu Azure Marketplace.

> [!div class="nextstepaction"]
> [Pojedynczy węzeł serwera Oracle WebLogic Server](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls)

> [!div class="nextstepaction"]
> [Serwer Oracle webWebLogicc Server z serwerem administracyjnym](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin)

> [!div class="nextstepaction"]
> [Klaster serwera Oracle WebLogic](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)

> [!div class="nextstepaction"]
> [Dynamiczny klaster programu Oracle WebLogic Server](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster)
