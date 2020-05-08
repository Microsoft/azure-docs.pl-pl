---
title: Przewodnik publikowania według typu oferty — Microsoft Commercial Marketplace
description: W tym artykule opisano typy ofert, które są dostępne w komercyjnym portalu Microsoft Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: 39bc4e3cc653589686be539da127279b3db41be6
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872356"
---
# <a name="publishing-guide-by-offer-type"></a>Podręcznik publikowania dla różnych typów ofert

W tym artykule opisano typy ofert, które są dostępne w portalu komercyjnym. *Typ oferty* definiuje strukturę oferty, która obejmuje metadane, artefakty i inną zawartość używaną do prezentowania oferty w portalu Marketplace.

Przed utworzeniem oferty i po wybraniu [opcji publikowania](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type)należy wybrać typ oferty, który zostanie użyty do zaprezentowania oferty. Typ oferty będzie odpowiadać typowi rozwiązania, aplikacji lub oferty usługi, którą chcesz opublikować, a także jego wyrównaniem do produktów i usług firmy Microsoft.

Typ oferty pojedynczej można skonfigurować na różne sposoby, aby włączyć różne opcje publikowania, wywołania do akcji, aprowizacji lub cennika. Opcja publikowania i konfiguracja typu oferty są również zgodne z wymaganiami dotyczącymi ofert i wymaganiami technicznymi.

Przed utworzeniem oferty zapoznaj się z wymaganiami dotyczącymi wymagań dotyczących ofert i wymagań związanych z publikowaniem w sklepie.

## <a name="list-of-offer-types"></a>Lista typów ofert

Typy ofert w witrynie Azure Marketplace są wymienione w poniższej tabeli.

| **Typ oferty**    | **Opis**  |
| :------------------- | :-------------------|
| [**Maszyny wirtualne**](./marketplace-virtual-machines.md) | Podczas wdrażania urządzenia wirtualnego w ramach subskrypcji skojarzonej z klientem Użyj typu oferty maszyny wirtualnej. |
| [**Szablony rozwiązań**](./marketplace-solution-templates.md) | Użyj szablonu rozwiązania (znanego również jako aplikacja platformy Azure), gdy rozwiązanie wymaga dodatkowego wdrożenia i automatyzacji konfiguracji poza jedną maszyną wirtualną. Szablony rozwiązań mogą korzystać z wielu różnych rodzajów zasobów platformy Azure, w tym między innymi maszyn wirtualnych.  |
| [**Aplikacje zarządzane**](./marketplace-managed-apps.md) | Użyj usługi Azure App: Managed App — typ oferty, gdy wymagane są następujące warunki: <br> <ul> <li> Rozwiązanie oparte na subskrypcji można wdrożyć dla klienta przy użyciu maszyny wirtualnej lub całego rozwiązania opartego na IaaS. </li> <li>Ty lub Twój klient wymaga, aby rozwiązanie było zarządzane przez partnera. </li> <ul> |
| [**Aplikacje SaaS**](./marketplace-saas-applications-technical-publishing-guide.md) | Użyj typu oferty aplikacji SaaS, aby umożliwić klientowi kupowanie rozwiązań technicznych opartych na SaaS, w ramach subskrypcji. |
| [**Oferty kontenerów**](./marketplace-containers.md) | Użyj typu oferty kontenera, gdy rozwiązanie jest obrazem kontenera Docker, który został zainicjowany jako usługa kontenera platformy Azure oparta na Kubernetes. |
| [**Moduły Azure IoT Edge**](./iot-edge-module.md) | Moduły Azure IoT Edge są najmniejszymi jednostkami obliczeniowymi zarządzanymi przez IoT Edge i mogą zawierać usługi firmy Microsoft (takie jak Azure Stream Analytics), usługi innych firm lub własny kod specyficzny dla rozwiązania. |
| [**Usługi konsultingowe**](./consulting-services.md) | Usługi doradcze ułatwiają łączenie się z usługami w celu wspierania i zwiększania użytkowania usług Azure, Dynamics 365 i pakietów usługi.|
| [**Usługi Office 365, Dynamics 365 i Power BI**](./appsource-offer-publishing-guide.md) | Możesz publikować oferty AppSource, które kompilują lub rozszerą dynamikę Dynamics 365, pakiet Office 365, Power BI i aplikacje zaawansowane.|
| [**Zintegrowane rozwiązania**](./integrated-solutions-for-publishers.md) | Możesz publikować zintegrowane i wyrównane do branży rozwiązania, które łączą technologię i usługi w ramach jednej oferty.|

Aby uzyskać informacje o wymaganiach dotyczących rejestracji jednokrotnej przez wystawienie opcji i typów ofert, zobacz [**wymagania dotyczące usługi Azure AD**](./enable-appsource-marketplace-using-azure-ad.md).

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z wymaganiami dotyczącymi uprawnień w odpowiednim artykule dotyczącym typu oferty (w poniższych sekcjach), aby sfinalizować wybór i konfigurację oferty.
- Zapoznaj się z wzorcem publikowania według witryny sklepu, aby zapoznać się z przykładami dotyczącymi sposobu mapowania rozwiązania na typ oferty i konfigurację.
