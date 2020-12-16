---
title: Co to są usługi danych z obsługą usługi Azure Arc
description: Wprowadzenie do usług danych z obsługą usługi Azure Arc
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 09/22/2020
ms.topic: overview
ms.openlocfilehash: ef16a4c0b6ea40fb4934307916f1fe79ccea72f9
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97609143"
---
# <a name="what-are-azure-arc-enabled-data-services-preview"></a>Co to są usługi danych z obsługą usługi Azure Arc (wersja zapoznawcza)?

Usługa Azure Arc umożliwia uruchamianie usług danych platformy Azure lokalnie, na brzegu i w chmurach publicznych przy użyciu Kubernetes i wybranej infrastruktury.

Obecnie następujące usługi danych z obsługą usługi Azure Arc są dostępne w wersji zapoznawczej:

- Wystąpienie zarządzane SQL
- PostgreSQL

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="always-current"></a>Zawsze bieżące

Usługi danych z włączonym łukiem platformą Azure, takie jak Azure ARC z włączonym wystąpieniem zarządzanym SQL, oraz usługa Azure ARC z włączonym PostgreSQLem na platformie Azure umożliwiają częste odbieranie aktualizacji, w tym poprawek obsługi i nowych funkcji, podobnie jak w przypadku korzystania z Aktualizacje z Container Registry firmy Microsoft są udostępniane dla Ciebie i cadences wdrożenia są ustawiane zgodnie z zasadami. W ten sposób lokalne bazy danych mogą być aktualne, a użytkownik zachowuje kontrolę. Ponieważ usługi danych obsługujące usługę Azure Arc są usługą subskrypcji, nie będą już działać w przypadku baz danych.

## <a name="elastic-scale"></a>Elastyczne skalowanie

Elastyczna elastyczność w chmurze umożliwia skalowanie baz danych w górę i w dół w taki sam sposób, jak w przypadku platformy Azure, na podstawie dostępnej pojemności infrastruktury. Ta funkcja może spełnić scenariusze wieloskładnikowe, w tym scenariusze, które wymagają pozyskania i wykonywania zapytań dotyczących danych w czasie rzeczywistym, w dowolnej skali, z podsekundowym czasem odpowiedzi. Ponadto można skalować wystąpienia bazy danych w poziomie przy użyciu opcji unikatowego wdrożenia skalowania funkcji Hyper-deskalowanie Azure Database for PostgreSQL. Ta funkcja zapewnia obciążenia danych dodatkowe zwiększenie wydajności optymalizacji pojemności przy użyciu unikatowych odczytów i zapisów skalowalnych *w poziomie.*

## <a name="self-service-provisioning"></a>Samoobsługowe Inicjowanie obsługi administracyjnej

Usługa Azure Arc oferuje również inne korzyści w chmurze, takie jak szybkie wdrażanie i Automatyzacja w dużej skali. Dzięki aranżacji opartej na Kubernetes można wdrożyć bazę danych w kilka sekund przy użyciu interfejsu GUI lub narzędzi interfejsu wiersza polecenia.

## <a name="unified-management"></a>Ujednolicone zarządzanie

Korzystając ze znanych narzędzi, takich jak Azure Portal, Azure Data Studio i [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] , możesz teraz uzyskać ujednolicony widok wszystkich zasobów danych wdrożonych przy użyciu usługi Azure Arc. Możesz nie tylko wyświetlać i zarządzać różnymi relacyjnymi bazami danych w środowisku i na platformie Azure, ale również uzyskiwać dzienniki i dane telemetryczne z interfejsów API Kubernetes, aby analizować podstawową wydajność i kondycję infrastruktury. Oprócz zlokalizowanej usługi log Analytics i monitorowania wydajności można teraz wykorzystać Azure Monitor do kompleksowego wglądu w dane operacyjne.

## <a name="disconnected-scenario-support"></a>Obsługa scenariuszy rozłączonych

Wiele usług, takich jak samoobsługowe Inicjowanie obsługi, automatyczne tworzenie kopii zapasowych/przywracanie i monitorowanie, można uruchamiać lokalnie w infrastrukturze z lub bez bezpośredniego połączenia z platformą Azure. Bezpośrednie połączenie z platformą Azure powoduje otwarcie dodatkowych opcji integracji z innymi usługami platformy Azure, takimi jak Azure Monitor, oraz możliwość używania Azure Portal i Azure Resource Manager interfejsów API z dowolnego miejsca na świecie do zarządzania usługami danych z obsługą usługi Azure Arc.

## <a name="next-steps"></a>Następne kroki

> **Chcesz, aby wypróbować coś?**  
> Szybko Rozpocznij pracę z usługą [Azure Arc szybko Rozpocznij pracę](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) w usłudze Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (eks), aparacie Google Cloud Kubernetes Engine (GKE) lub na maszynie wirtualnej platformy Azure.

[Instalowanie narzędzi klienta](install-client-tools.md)

[Utwórz kontroler danych usługi Azure Arc](create-data-controller.md) (najpierw wymagane jest zainstalowanie narzędzi klienta)

[Tworzenie wystąpienia zarządzanego usługi Azure SQL w usłudze Azure Arc](create-sql-managed-instance.md) (wymaga najpierw utworzenia kontrolera danych usługi Azure ARC)

[Tworzenie grupy serwerów z Azure Database for PostgreSQLm skalowaniem w usłudze Azure Arc](create-postgresql-hyperscale-server-group.md) (wymaga najpierw utworzenia kontrolera danych usługi Azure ARC)
