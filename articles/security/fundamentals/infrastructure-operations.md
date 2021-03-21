---
title: Zarządzanie siecią produkcyjną platformy Azure — Microsoft Azure
description: W tym artykule opisano, jak firma Microsoft zarządza siecią produkcyjną platformy Azure i obsługuje ją w celu zabezpieczenia centrów danych platformy Azure.
services: security
documentationcenter: n
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2019
ms.author: terrylan
ms.openlocfilehash: d41fe409b4a44a4c2af3670d76dd3a83a300feae
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "68727126"
---
# <a name="management-and-operation-of-the-azure-production-network"></a>Zarządzanie i eksploatacja sieci produkcyjnej platformy Azure    
W tym artykule opisano, jak firma Microsoft zarządza siecią produkcyjną platformy Azure i obsługuje ją w celu zabezpieczenia centrów danych platformy Azure.

## <a name="monitor-log-and-report"></a>Monitorowanie, rejestrowanie i raportowanie

Zarządzanie i działanie sieci produkcyjnej platformy Azure to skoordynowane wysiłki między zespołami operacyjnymi platformy Azure i Azure SQL Database. Zespoły korzystają z kilku narzędzi do monitorowania wydajności systemu i aplikacji w środowisku. I używają odpowiednich narzędzi do monitorowania urządzeń sieciowych, serwerów, usług i procesów aplikacji.

Aby zapewnić bezpieczne wykonywanie usług działających w środowisku platformy Azure, zespoły operacji implementują wiele poziomów monitorowania, rejestrowania i raportowania, w tym następujące akcje:

- Przede wszystkim Microsoft Monitoring Agent (MMA) gromadzi informacje dziennika monitorowania i diagnostyki z wielu miejsc, w tym kontrolera sieci szkieletowej (FC) i głównego systemu operacyjnego (OS), i zapisuje je w plikach dziennika. Agent ostatecznie wypchnięcie szyfrowanego podzbiór informacji do wstępnie skonfigurowanego konta usługi Azure Storage. Ponadto, wolnostojąca usługa monitorowania i diagnostyki odczytuje różne dane dziennika monitorowania i diagnostyki oraz podsumowuje informacje. Usługa monitorowania i diagnostyki zapisuje te informacje w dzienniku zintegrowanym. Platforma Azure używa wbudowanego niestandardowego monitorowania zabezpieczeń platformy Azure, które jest rozszerzeniem systemu monitorowania platformy Azure. Ma składniki, które obserwują, analizują i raportują zdarzenia związane z zabezpieczeniami z różnych punktów na platformie.

- Platforma Azure SQL Database Windows Fabric zapewnia zarządzanie, wdrażanie, programowanie i usługi nadzoru operacyjnego w Azure SQL Database. Platforma oferuje rozproszone, wieloetapowe usługi wdrażania, monitorowanie kondycji, automatyczne naprawy i zgodność wersji usług. Dostępne są następujące usługi:

   - Możliwości modelowania usług w środowisku programistycznym o wysokiej wierności (klastry centrów danych są drogie i niedostępne).
   - Wdrożenie jednego kliknięcia i przepływy pracy uaktualniania dla ładowania początkowego i konserwacji usługi.
   - Raportowanie kondycji z zautomatyzowanymi przepływami pracy naprawy w celu włączenia samonaprawiania.
   - Funkcje monitorowania, ostrzegania i debugowania w czasie rzeczywistym w węzłach systemu rozproszonego.
   - Scentralizowana kolekcja danych operacyjnych i metryki na potrzeby analizy rozproszonej przyczyny głównej i usługi Service Insight.
   - Narzędzia operacyjne do wdrażania, zarządzania zmianami i monitorowania.
   - Azure SQL Database Windows Fabric platform i skryptów licznika alarmowego działają w sposób ciągły i monitorowane w czasie rzeczywistym.

Jeśli wystąpią jakieś anomalie, proces reagowania na zdarzenia, po którym następuje zespół usługi Azure incydent Klasyfikacja. Odpowiedni personel pomocy technicznej platformy Azure zostanie powiadomiony o tym, że zareaguje na zdarzenie. Śledzenie i rozwiązywanie problemów są udokumentowane i zarządzane w scentralizowanym systemie biletów. Metryki czasu systemu są dostępne w ramach umowy o nieujawnianiu (NDA) i na żądanie.

## <a name="corporate-network-and-multi-factor-access-to-production"></a>Sieć firmowa i wieloskładnikowy dostęp do środowiska produkcyjnego
Baza użytkownika sieci firmowej obejmuje personel pomocy technicznej platformy Azure. Sieć firmowa obsługuje wewnętrzne funkcje firmowe i obejmuje dostęp do wewnętrznych aplikacji, które są używane do obsługi klienta platformy Azure. Sieć firmowa jest logicznie i fizycznie oddzielona od sieci produkcyjnej platformy Azure. Personel platformy Azure uzyskuje dostęp do sieci firmowej przy użyciu stacji roboczych i laptopów platformy Azure. Wszyscy użytkownicy muszą mieć konto Azure Active Directory (Azure AD), w tym nazwę użytkownika i hasło, aby uzyskać dostęp do firmowych zasobów sieciowych. Dostęp do sieci firmowej korzysta z kont usługi Azure AD, które są wystawiane dla wszystkich pracowników, wykonawców i dostawców firmy Microsoft, którzy są zarządzani przez technologię informatyczną firmy Microsoft. Unikatowe identyfikatory użytkowników odróżnią personel na podstawie jego stanu zatrudnienia w firmie Microsoft.

Dostęp do wewnętrznych aplikacji platformy Azure jest kontrolowany za pomocą uwierzytelniania przy użyciu Active Directory Federation Services (AD FS). AD FS to usługa hostowana przez technologię informacji firmy Microsoft, która umożliwia uwierzytelnianie użytkowników sieci firmowej za pomocą stosowania bezpiecznego tokenu i oświadczeń użytkowników. AD FS umożliwia wewnętrznym aplikacjom platformy Azure uwierzytelnianie użytkowników w firmowej domenie usługi Active Directory firmy Microsoft. Aby można było uzyskać dostęp do sieci produkcyjnej ze środowiska sieci firmowej, użytkownicy muszą uwierzytelniać się przy użyciu uwierzytelniania wieloskładnikowego.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat sposobu zabezpieczania infrastruktury platformy Azure przez firmę Microsoft, zobacz:

- [Funkcje platformy Azure, lokalne i zabezpieczenia fizyczne](physical-security.md)
- [Dostępność infrastruktury platformy Azure](infrastructure-availability.md)
- [Składniki i granice systemu informacji platformy Azure](infrastructure-components.md)
- [Architektura sieci platformy Azure](infrastructure-network.md)
- [Sieć produkcyjna platformy Azure](production-network.md)
- [Azure SQL Database funkcje zabezpieczeń](infrastructure-sql.md)
- [Monitorowanie infrastruktury platformy Azure](infrastructure-monitoring.md)
- [Integralność infrastruktury platformy Azure](infrastructure-integrity.md)
- [Ochrona danych klienta platformy Azure](protection-customer-data.md)
