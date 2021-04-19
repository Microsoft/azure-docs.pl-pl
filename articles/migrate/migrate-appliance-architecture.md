---
title: Architektura urządzenia usługi Azure Migrate
description: Zawiera omówienie urządzenia Azure Migrate używanego podczas odnajdywania, oceny i migracji serwera.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 4fc71f3242cc5607acebc68b62c5c0565b8f8e56
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107715014"
---
# <a name="azure-migrate-appliance-architecture"></a>Architektura urządzenia usługi Azure Migrate

W tym artykule opisano Azure Migrate architektury i procesów urządzenia. Urządzenie Azure Migrate jest lekkim urządzeniem wdrożonym lokalnie, które wykrywa serwery do migracji na platformę Azure.

## <a name="deployment-scenarios"></a>Scenariusze wdrażania

Urządzenie Azure Migrate jest używane w następujących scenariuszach.

**Scenariusz** | **Narzędzie** | **Używane do**
--- | --- | ---
**Odnajdywanie i ocena serwerów uruchomionych w środowisku VMware** | Azure Migrate: odnajdywanie i ocena | Odnajdywanie serwerów uruchomionych w środowisku VMware<br/><br/> Odnajdywanie spisu zainstalowanego oprogramowania, analizy zależności bez agenta i odnajdywanie SQL Server i baz danych.<br/><br/> Zbierz metadane dotyczące konfiguracji i wydajności serwera dla ocen.
**Migracja bez agenta serwerów działających w środowisku VMware** | Azure Migrate: Migracja serwera | Odnajdowanie serwerów uruchomionych w środowisku VMware.<br/><br/> Replikowanie serwerów bez instalowania na nich agentów.
**Odnajdywanie i ocena serwerów uruchomionych w środowisku funkcji Hyper-V** | Azure Migrate: odnajdywanie i ocena | Odnajdowanie serwerów działających w środowisku funkcji Hyper-V.<br/><br/> Zbierz metadane dotyczące konfiguracji i wydajności serwera dla ocen.
**Odnajdywanie i ocena serwerów fizycznych lub zwirtualizowanych w środowisku lokalnym** |  Azure Migrate: odnajdywanie i ocena |  Odnajdywanie serwerów fizycznych lub zwirtualizowanych w środowisku lokalnym.<br/><br/> Zbieraj metadane konfiguracji i wydajności serwera dla ocen.

## <a name="deployment-methods"></a>Metody wdrażania

Urządzenie można wdrożyć przy użyciu kilku metod:

- Urządzenie można wdrożyć przy użyciu szablonu dla serwerów działających w środowisku VMware lub Hyper-V (szablon OVA dla[oprogramowania VMware](how-to-set-up-appliance-vmware.md) lub [dysk VHD dla funkcji Hyper-V).](how-to-set-up-appliance-hyper-v.md)
- Jeśli nie chcesz używać szablonu, możesz wdrożyć urządzenie dla środowiska VMware lub Hyper-V przy użyciu skryptu [instalatora programu PowerShell.](deploy-appliance-script.md)
- W Azure Government należy wdrożyć urządzenie za pomocą skryptu instalatora programu PowerShell. Kroki wdrażania można znaleźć [tutaj.](deploy-appliance-script-government.md)
- W przypadku serwerów fizycznych lub zwirtualizowanych w środowisku lokalnym lub dowolnej innej chmurze urządzenie jest zawsze wdrażane przy użyciu skryptu instalatora programu PowerShell. Kroki wdrażania można znaleźć [tutaj.](how-to-set-up-appliance-physical.md)
- Linki pobierania są dostępne w poniższych tabelach.

## <a name="appliance-services"></a>Usługi urządzeń

Urządzenie ma następujące usługi:

- **Menedżer konfiguracji urządzenia:** jest to aplikacja internetowa, którą można skonfigurować przy użyciu szczegółów źródła, aby rozpocząć odnajdywanie i ocenę serwerów. 
- **Agent odnajdywania:** agent zbiera metadane konfiguracji serwera, których można użyć do tworzenia ocen lokalnych.
- **Agent oceny:** agent zbiera metadane wydajności serwera, których można użyć do tworzenia ocen opartych na wydajności.
- **Usługa automatycznej aktualizacji:** usługa zapewnia, że wszyscy agenci na urządzeniu są aktualne. Jest ona uruchamiana automatycznie co 24 godziny.
- **Agent dra:** orkiestruje replikację serwera i koordynuje komunikację między replikowanych serwerami i platformą Azure. Używany tylko podczas replikowania serwerów na platformę Azure przy użyciu migracji bez agenta.
- **Brama:** wysyła replikowane dane na platformę Azure. Używany tylko podczas replikowania serwerów na platformę Azure przy użyciu migracji bez agenta.
- **Agent odnajdywania i oceny SQL:** wysyła metadane konfiguracji i wydajności SQL Server i baz danych na platformę Azure.

> [!Note]
> Ostatnie 3 usługi są dostępne tylko w urządzeniu używanym do odnajdywania i oceny serwerów uruchomionych w środowisku VMware.<br/> Odnajdywanie i ocena SQL Server i baz danych uruchomionych w środowisku VMware jest teraz w wersji zapoznawczej. Aby wypróbować tę funkcję, użyj [**tego linku**](https://aka.ms/AzureMigrate/SQL) w celu utworzenia projektu w regionie **Australia Wschodnia**. Jeśli masz już projekt w regionie Australia Wschodnia i chcesz wypróbować tę funkcję, upewnij się, że zostały spełnione te [**wymagania wstępne**](how-to-discover-sql-existing-project.md) w portalu.

## <a name="discovery-and-collection-process"></a>Proces odnajdywania i zbierania

:::image type="content" source="./media/migrate-appliance-architecture/architecture1.png" alt-text="Architektura urządzenia":::

Urządzenie komunikuje się ze źródłami odnajdywania przy użyciu następującego procesu.

**Proces** | **Urządzenie VMware** | **Urządzenie funkcji Hyper-V** | **Urządzenie fizyczne**
---|---|---|---
**Uruchamianie odnajdywania** | Urządzenie domyślnie komunikuje się z serwerem vCenter na porcie TCP 443. Jeśli serwer vCenter nasłuchuje na innym porcie, można skonfigurować go w menedżerze konfiguracji urządzenia. | Urządzenie komunikuje się z hostami funkcji Hyper-V na porcie 5985 (HTTP) usługi WinRM. | Urządzenie komunikuje się z serwerami z systemem Windows za pośrednictwem portu 5985 (HTTP) usługi WinRM z serwerami z systemem Linux za pośrednictwem portu 22 (TCP).
**Zbieranie metadanych konfiguracji i wydajności** | Urządzenie zbiera metadane serwerów uruchomionych na serwerze vCenter Server przy użyciu interfejsów API vSphere, łącząc się na porcie 443 (port domyślny) lub dowolnym innym porcie, na vCenter Server nasłuchuje. | Urządzenie zbiera metadane serwerów działających na hostach funkcji Hyper-V przy użyciu sesji model wspólnych informacji (CIM) z hostami na porcie 5985.| Urządzenie zbiera metadane z serwerów z systemem Windows przy użyciu sesji model wspólnych informacji (CIM) z serwerami na porcie 5985 i z serwerów z systemem Linux przy użyciu łączności SSH na porcie 22.
**Wysyłanie danych odnajdywania** | Urządzenie wysyła zebrane dane do usługi Azure Migrate: odnajdywanie i ocena oraz Azure Migrate: migracja serwera za pośrednictwem portu SSL 443.<br/><br/>  Urządzenie może łączyć się z platformą Azure za pośrednictwem Internetu, prywatnej komunikacji równorzędnej ExpressRoute lub obwodów komunikacji równorzędnej firmy Microsoft. | Urządzenie wysyła zebrane dane do usługi Azure Migrate: odnajdywanie i ocena za pośrednictwem portu SSL 443.<br/><br/> Urządzenie może łączyć się z platformą Azure za pośrednictwem Internetu, prywatnej komunikacji równorzędnej ExpressRoute lub obwodów komunikacji równorzędnej firmy Microsoft. | Urządzenie wysyła zebrane dane do usługi Azure Migrate: odnajdywanie i ocena za pośrednictwem portu SSL 443.<br/><br/> Urządzenie może łączyć się z platformą Azure za pośrednictwem Internetu, prywatnej komunikacji równorzędnej ExpressRoute lub obwodów komunikacji równorzędnej firmy Microsoft. 
**Częstotliwość zbierania danych** | Metadane konfiguracji są zbierane i wysyłane co 30 minut. <br/><br/> Metadane wydajności są zbierane co 20 sekund i agregowane w celu wysyłania punktu danych na platformę Azure co 10 minut. <br/><br/> Dane spisu oprogramowania są wysyłane na platformę Azure co 12 godzin. <br/><br/> Dane zależności bez agenta są zbierane co 5 minut, agregowane na urządzeniu i wysyłane na platformę Azure co 6 godzin. <br/><br/> Dane SQL Server są aktualizowane co 24 godziny, a dane wydajności są przechwytywane co 30 sekund.| Metadane konfiguracji są zbierane i wysyłane co 30 minut. <br/><br/> Metadane wydajności są zbierane co 30 sekund i agregowane w celu wysyłania punktu danych na platformę Azure co 10 minut.|  Metadane konfiguracji są zbierane i wysyłane co 30 minut. <br/><br/> Metadane wydajności są zbierane co 5 minut i agregowane w celu wysyłania punktu danych na platformę Azure co 10 minut.
**Ocenianie i migrowanie** | Oceny można tworzyć na podstawie metadanych zebranych przez urządzenie przy użyciu Azure Migrate: Odnajdywanie i ocena.<br/><br/>Ponadto możesz również rozpocząć migrowanie serwerów uruchomionych w środowisku VMware przy użyciu narzędzia Azure Migrate: Server Migration, aby aranżować replikację serwera bez agenta.| Oceny można tworzyć na podstawie metadanych zebranych przez urządzenie przy użyciu Azure Migrate: Odnajdywanie i ocena. | Oceny można tworzyć na podstawie metadanych zebranych przez urządzenie przy użyciu narzędzia Azure Migrate: odnajdywanie i ocena.

## <a name="next-steps"></a>Następne kroki

[Zapoznaj się](migrate-appliance.md) z macierzą obsługi urządzeń.