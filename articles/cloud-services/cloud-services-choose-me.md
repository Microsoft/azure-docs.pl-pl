---
title: Co to jest platforma Azure Cloud Services | Microsoft Docs
description: Dowiedz się, co to jest usługa Azure Cloud Services, w odróżnieniu od tego, czy jest ona przeznaczona do obsługi aplikacji, które są skalowalne, niezawodne i niedrogie do działania.
services: cloud-services
author: tgore03
ms.service: multiple
ms.topic: article
ms.date: 04/19/2017
ms.author: tagore
ms.openlocfilehash: 0013a3a29bae9d2dde7896b3ae23d0d358946f2b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88224292"
---
# <a name="overview-of-azure-cloud-services"></a>Omówienie Cloud Services platformy Azure
Platforma Azure Cloud Services to przykład [platformy jako usługi](https://azure.microsoft.com/overview/what-is-paas/) (PaaS). Podobnie jak [Azure App Service](../app-service/overview.md), Technologia ta została zaprojektowana do obsługi aplikacji, które są skalowalne, niezawodne i niedrogie do działania. W taki sam sposób, w jaki App Service jest hostowany na maszynach wirtualnych, tak więc usługa Azure Cloud Services. Jednak masz większą kontrolę nad maszynami wirtualnymi. Możesz zainstalować własne oprogramowanie na maszynach wirtualnych korzystających z usługi Azure Cloud Services i uzyskać do nich dostęp zdalnie.

![Diagram Cloud Services platformy Azure](./media/cloud-services-choose-me/diagram.png)

Większa kontrola oznacza także mniejszą łatwość użycia. O ile nie potrzebujesz dodatkowych opcji kontroli, zazwyczaj jest to szybsze i łatwiejsze w objęcie działaniem aplikacji sieci Web w Web Apps funkcji App Service w porównaniu do Cloud Services platformy Azure.

Istnieją dwa typy ról Cloud Services platformy Azure. Jedyną różnicą między nimi jest sposób, w jaki Twoja rola jest hostowana na maszynach wirtualnych:

* **Rola sieci Web**: automatycznie wdraża i hostuje aplikację za poorednictwem usług IIS.

* **Rola procesu roboczego**: nie korzysta z usług IIS i uruchamia autonomiczną aplikację.

Przykładowo prosta aplikacja może używać tylko jednej roli sieci Web, która obsługuje witrynę sieci Web. Bardziej złożona aplikacja może używać roli sieci Web do obsługi żądań przychodzących od użytkowników, a następnie przekazać te żądania do roli procesu roboczego w celu przetworzenia. (Ta komunikacja może używać [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) lub [usługi Azure queue storage](../storage/common/storage-introduction.md)).

Zgodnie z powyższym rysunkiem sugeruje, wszystkie maszyny wirtualne w pojedynczej aplikacji działają w tej samej usłudze w chmurze. Użytkownicy uzyskują dostęp do aplikacji za pośrednictwem jednego publicznego adresu IP, a żądania są automatycznie równoważone przez maszyny wirtualne aplikacji. Platforma umożliwia [skalowanie i wdrażanie](cloud-services-how-to-scale-portal.md) maszyn wirtualnych w aplikacji Cloud Services platformy Azure w taki sposób, aby uniknąć pojedynczego punktu awarii sprzętu.

Chociaż aplikacje są uruchamiane na maszynach wirtualnych, ważne jest, aby zrozumieć, że usługa Azure Cloud Services udostępnia PaaS, a nie infrastrukturę jako usługę (IaaS). Oto jeden ze sposobów, aby myśleć o tym. Korzystając z IaaS, takiego jak Azure Virtual Machines, należy najpierw utworzyć i skonfigurować środowisko, w którym aplikacja jest uruchamiana. Następnie wdrażasz aplikację w tym środowisku. Użytkownik jest odpowiedzialny za zarządzanie większością tego świata, wykonując takie czynności jak wdrażanie nowych poprawek systemu operacyjnego w ramach każdej maszyny wirtualnej. W PaaS, z kolei, tak jakby środowisko już istnieje. Wystarczy wdrożyć aplikację. Zarządzanie uruchomioną platformą, w tym wdrażanie nowych wersji systemu operacyjnego, jest obsługiwane przez użytkownika.

## <a name="scaling-and-management"></a>Skalowanie i zarządzanie
Usługa Azure Cloud Services nie tworzy maszyn wirtualnych. Zamiast tego należy podać plik konfiguracji, który informuje platformę Azure, jak wiele z nich ma być, takich jak "trzy wystąpienia roli sieci Web" i "dwa wystąpienia roli procesu roboczego". Następnie zostanie utworzona przez platformę. Nadal wybierasz [rozmiar](cloud-services-sizes-specs.md) tych maszyn wirtualnych, które powinny być zapasowe, ale nie można ich jawnie tworzyć. Jeśli aplikacja wymaga obsługi większego obciążenia, możesz poszukiwać więcej maszyn wirtualnych, a platforma Azure tworzy te wystąpienia. Jeśli obciążenie zmniejszy się, możesz zamknąć te wystąpienia i zaprzestać płacenia za nie.

Aplikacja Cloud Services platformy Azure jest zwykle udostępniana użytkownikom za pośrednictwem procesu dwuetapowego. Deweloper najpierw [przekazuje aplikację](cloud-services-how-to-create-deploy-portal.md) do obszaru przejściowego platformy. Gdy deweloper jest gotowy do udostępnienia aplikacji, używa Azure Portal do wymiany etapów przejściowych w środowisku produkcyjnym. Ten [przełącznik między przejściową i produkcyjną](cloud-services-how-to-manage-portal.md#swap-deployments-to-promote-a-staged-deployment-to-production) może być wykonywany bez przestojów, co umożliwia uaktualnienie uruchomionej aplikacji do nowej wersji bez zakłócania jej użytkowników.

## <a name="monitoring"></a>Monitorowanie
Usługa Azure Cloud Services udostępnia również monitorowanie. Podobnie jak Virtual Machines, wykrywa uszkodzony serwer fizyczny i ponownie uruchamia maszyny wirtualne, które były uruchomione na tym serwerze na nowym komputerze. Jednak usługa Azure Cloud Services wykrywa także niepowodzenia maszyn wirtualnych i aplikacji, a nie tylko błędy sprzętowe. W przeciwieństwie do Virtual Machines, ma agenta w ramach każdej roli sieci Web i procesu roboczego, dzięki czemu można uruchamiać nowe maszyny wirtualne i wystąpienia aplikacji w przypadku wystąpienia błędów.

PaaS charakter platformy Azure Cloud Services ma inne konsekwencje. Jednym z najważniejszych jest to, że aplikacje skompilowane w tej technologii należy napisać tak, aby działały prawidłowo w przypadku awarii dowolnego wystąpienia roli sieci Web lub procesu roboczego. Aby to osiągnąć, aplikacja platformy Azure Cloud Services nie powinna zachować stanu w systemie plików własnych maszyn wirtualnych. W przeciwieństwie do maszyn wirtualnych utworzonych przy użyciu Virtual Machines zapisy wprowadzone do maszyn wirtualnych Cloud Services platformy Azure nie są trwałe. Nie ma niczego jak dysk danych Virtual Machines. Zamiast tego aplikacja Cloud Services platformy Azure powinna jawnie zapisywać wszystkie stany do Azure SQL Database, obiektów blob, tabel lub innych magazynów zewnętrznych. Tworzenie aplikacji w ten sposób ułatwia skalowanie i bardziej odporne na awarie, które są ważnymi celami Cloud Services platformy Azure.

## <a name="next-steps"></a>Następne kroki
* [Tworzenie aplikacji usługi w chmurze w programie .NET](cloud-services-dotnet-get-started.md) 
* [Tworzenie aplikacji usługi w chmurze w Node.js](cloud-services-nodejs-develop-deploy-app.md) 
* [Tworzenie aplikacji usługi w chmurze w języku PHP](../cloud-services-php-create-web-role.md) 
* [Tworzenie aplikacji usługi w chmurze w języku Python](cloud-services-python-ptvs.md)






