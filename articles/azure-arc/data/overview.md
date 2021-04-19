---
title: Co to są usługi danych z obsługą usługi Azure Arc
description: Wprowadzenie do usługi danych z obsługą usługi Azure Arc
ms.custom: references_regions
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 03/31/2021
ms.topic: overview
ms.openlocfilehash: 2d866dcb5b2a0be9e6468b3d40258e37ac93834e
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107716101"
---
# <a name="what-are-azure-arc-enabled-data-services-preview"></a>Co to są usługi danych z obsługą usługi Azure Arc (wersja zapoznawcza)?

Azure Arc umożliwia uruchamianie usług danych platformy Azure lokalnie, na brzegu sieci i w chmurach publicznych przy użyciu platformy Kubernetes i wybranej infrastruktury.

Obecnie w wersji zapoznawczej usługi danych z obsługą usługi Azure Arc są dostępne następujące opcje:

- Wystąpienie zarządzane SQL
- PostgreSQL w hiperskali

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="always-current"></a>Zawsze aktualne

usługi danych z obsługą usługi Azure Arc takie jak Azure Arc sql managed instance i usługa baza danych PostgreSQL w warstwie Hiperskala z obsługą usługi Azure Arc często otrzymują aktualizacje, w tym poprawki obsługi i nowe funkcje podobne do obsługi na platformie Azure. Aktualizacje z Microsoft Container Registry są udostępniane, a czasy wdrażania są ustawiane zgodnie z zasadami. Dzięki temu lokalne bazy danych mogą być na bieżąco z zachowaniem kontroli. Ponieważ usługi danych z obsługą usługi Azure Arc są usługą subskrypcji, nie będziesz już mieć sytuacji zakończenia świadczenia pomocy technicznej dla baz danych.

## <a name="elastic-scale"></a>Elastyczne skalowanie

Elastyczność w chmurze w środowisku lokalnym umożliwia dynamiczne skalowanie baz danych w górę lub w dół w taki sam sposób jak na platformie Azure w zależności od dostępnej pojemności infrastruktury. Ta funkcja może spełniać potrzeby w zakresie serii, które mają nietrwałe potrzeby, w tym scenariusze, które wymagają pozysku danych i wykonywania na nich zapytań w czasie rzeczywistym, w dowolnej skali, z czasem odpowiedzi w sekundach mniejszej niż sekunda. Ponadto wystąpienia bazy danych można również skalować w zewnątrz przy użyciu unikatowej opcji wdrażania w hiperskali, Azure Database for PostgreSQL hiperskala. Ta funkcja zapewnia dodatkowe zwiększenie optymalizacji pojemności obciążeń danych przy użyciu unikatowych skalowania odczytów i zapisu.

## <a name="self-service-provisioning"></a>Aprowizowanie samoobsługowe

Azure Arc zapewnia również inne korzyści w chmurze, takie jak szybkie wdrażanie i automatyzacja na dużą skalę. Dzięki orkiestracji opartej na platformie Kubernetes można wdrożyć bazę danych w ciągu kilku sekund przy użyciu graficznego interfejsu użytkownika lub narzędzi interfejsu wiersza polecenia.

## <a name="unified-management"></a>Ujednolicone zarządzanie

Korzystając ze znanych narzędzi, takich jak Azure Portal, Azure Data Studio i , można teraz uzyskać ujednolicony widok wszystkich zasobów danych wdrożonych za pomocą [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] Azure Arc. Możesz nie tylko wyświetlać różne relacyjne bazy danych i zarządzać nimi w twoim środowisku i na platformie Azure, ale także uzyskać dzienniki i dane telemetryczne z interfejsów API platformy Kubernetes w celu przeanalizowania podstawowej pojemności i kondycji infrastruktury. Oprócz posiadania zlokalizowanej analizy dzienników i monitorowania wydajności można teraz korzystać Azure Monitor kompleksowych analiz operacyjnych w całej nieruchomości.

## <a name="disconnected-scenario-support"></a>Obsługa scenariuszy bez połączenia

Wiele usług, takich jak aprowizowanie samoobsługowe, automatyczne kopie zapasowe/przywracanie i monitorowanie, można uruchamiać lokalnie w infrastrukturze z bezpośrednim połączeniem z platformą Azure lub bez niego. Bezpośrednie połączenie z platformą Azure otwiera dodatkowe opcje integracji z innymi usługami platformy Azure, takimi jak Azure Monitor, oraz możliwość korzystania z interfejsów API usług Azure Portal i Azure Resource Manager z dowolnego miejsca na świecie do zarządzania usługi danych z obsługą usługi Azure Arc.

## <a name="supported-regions"></a>Obsługiwane regiony

W poniższej tabeli opisano scenariusze, które są obecnie obsługiwane w przypadku usług danych z obsługą usługi Arc.

|Regiony platformy Azure  |Tryb połączony bezpośrednio  |Tryb połączenia pośredniego  |
|---------|---------|---------|
|East US|Dostępne|Dostępne
|West Europe |Dostępne |Dostępne
|Europa Północna|Dostępne|Dostępne

## <a name="next-steps"></a>Następne kroki

> **Po prostu chcesz wypróbować?**  
> Szybkie rozpoczęcie pracy z [usługą Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) w usługach Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) lub na maszynie wirtualnej platformy Azure.

[Instalowanie narzędzi klienta](install-client-tools.md)

[Utwórz kontroler Azure Arc danych](create-data-controller.md) (wymaga najpierw zainstalowania narzędzi klienta)

[Tworzenie Azure SQL zarządzanego na Azure Arc](create-sql-managed-instance.md) (wymaga najpierw utworzenia Azure Arc danych)

[Tworzenie grupy Azure Database for PostgreSQL w hiperskali](create-postgresql-hyperscale-server-group.md) na Azure Arc (wymaga najpierw utworzenia Azure Arc danych)
