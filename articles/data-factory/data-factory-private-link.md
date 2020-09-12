---
title: Prywatny link do usługi Azure Azure Data Factory
description: Dowiedz się więcej o usłudze Azure Private link działa w Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/01/2020
ms.openlocfilehash: 164a1005d9382711874b644e14b23d2154d613a0
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/09/2020
ms.locfileid: "89596026"
---
# <a name="azure-private-link-for-azure-data-factory"></a>Prywatny link do usługi Azure Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Link prywatny umożliwia nawiązanie połączenia z różnymi usługami PaaS na platformie Azure za pośrednictwem prywatnego punktu końcowego. Aby zapoznać się z listą usług PaaS Services, które obsługują funkcję link prywatny, przejdź do [strony z dokumentacją linku prywatnego](https://docs.microsoft.com/azure/private-link/). Prywatny punkt końcowy to prywatny adres IP w ramach określonej sieci wirtualnej i podsieci.

## <a name="secure-communication-between-customer-network-and-azure-data-factory-service"></a>Bezpieczna komunikacja między siecią klienta a usługą Azure Data Factory
Aby chronić zasoby platformy Azure przed atakami w sieci publicznej lub umożliwić ich bezpieczną komunikację ze sobą, możesz skonfigurować platformę Azure Virtual Network jako logiczną reprezentację sieci w chmurze. Sieć lokalną można także połączyć z siecią wirtualną przez skonfigurowanie protokołu IPSec VPN (lokacja-lokacja) lub ExpressRoute (prywatna Komunikacja równorzędna). Samoobsługowe Integration Runtime można zainstalować na maszynie lokalnej lub maszynie wirtualnej w programie Virtual Network, aby uruchamiać działania kopiowania między magazynem danych w chmurze i magazynem danych w sieci prywatnej lub wysyłać działania transformacji do zasobów obliczeniowych w sieci lokalnej lub w sieci wirtualnej platformy Azure. 

Istnieje kilka kanałów komunikacji wymaganych między Data Factory i sieci wirtualnej klienta.


| **Domeny** | **Port** | **Opis** |
| ---------- | -------- | --------------- |
| `adf.azure.com` | 443 | Płaszczyzna kontroli. Wymagane przez Data Factory tworzenia i monitorowania. |
| `*.{region}.datafactory.azure.net` | 443 | Wymagane przez samoobsługowe Integration Runtime do nawiązywania połączenia z usługą Data Factory. |
| `*.servicebus.windows.net` | 443 | Wymagane przez samodzielną Integration Runtime do tworzenia interaktywnego. |
| `download.microsoft.com` | 443 | Wymagane przez samodzielną Integration Runtime do pobierania aktualizacji. |


Dzięki obsłudze prywatnego linku platformy Azure dla Azure Data Factory można utworzyć prywatny punkt końcowy (PE) w sieci wirtualnej i włączyć połączenie prywatne z konkretnym Azure Data Factory. Komunikacja do Azure Data Factory usługi odbywa się za pośrednictwem prywatnego linku platformy Azure, który zapewnia bezpieczną łączność prywatną. Nie trzeba konfigurować powyższych domen i portów w sieci wirtualnej ani firmowej zapory, która zapewnia bezpieczniejszy sposób ochrony zasobów.  

![Azure Data Factory architektura linku prywatnego](./media/data-factory-private-link/private-link-architecture.png)

Poniżej przedstawiono korzyści wynikające z włączenia usługi link prywatny dla każdego z kanałów komunikacyjnych przedstawionych powyżej:
- Obsługiwał Istnieje możliwość tworzenia i monitorowania Azure Data Factory w sieci wirtualnej, nawet w przypadku zablokowania całej komunikacji wychodzącej.
- Obsługiwał Komunikacja między samoobsługowym Integration Runtime i usługą Azure Data Factory można przeprowadzić bezpiecznie w środowisku sieci prywatnej. Ruch między samoobsługowym Integration Runtime i Azure Data Factory usługą odbywa się za pomocą prywatnego linku. 
- (Obecnie nie jest to obsługiwane) Interaktywny proces tworzenia przy użyciu samodzielnego Integration Runtime przechodzenia przez prywatny link, na przykład połączenie testowe, przeglądanie listy folderów i listy tabel, uzyskiwanie schematu i Podgląd danych.
- (Obecnie nie jest to obsługiwane) Nową wersję Integration Runtime samoobsługowego można automatycznie pobrać z centrum pobierania w przypadku włączenia automatycznej aktualizacji.

> [!NOTE]
> W przypadku funkcji, które nie są obecnie obsługiwane, nadal trzeba skonfigurować powyższą domenę i port w sieci wirtualnej lub zaporze firmowej. 

> [!WARNING]
> Podczas tworzenia połączonej usługi upewnij się, że poświadczenie jest przechowywane w Azure Key Vault. W przeciwnym razie nie działa po włączeniu usługi link prywatny w Azure Data Factory.

## <a name="how-to-set-up-private-link-for-azure-data-factory"></a>Jak skonfigurować link prywatny dla Azure Data Factory
Prywatne punkty końcowe można tworzyć przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure:

[Portal](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)


Możesz również przejść do Azure Data Factory w Azure Portal i utworzyć prywatny punkt końcowy (PE):

![Tworzenie prywatnego punktu końcowego](./media/data-factory-private-link/create-private-endpoint.png)


Jeśli chcesz zablokować publiczny dostęp do tego Azure Data Factory i zezwalać na dostęp tylko za poorednictwem linku prywatnego, możesz wyłączyć dostęp do sieci Azure Data Factory w Azure Portal:

![Tworzenie prywatnego punktu końcowego](./media/data-factory-private-link/disable-network-access.png)

> [!NOTE]
> Wyłączenie dostępu do sieci publicznej dotyczy wyłącznie samodzielnych Integration Runtime, a nie Azure Integration Runtime i SSIS Integration Runtime.

> [!NOTE]
> Po wyłączeniu dostępu do sieci publicznej użytkownicy nadal mogą uzyskać dostęp do portalu Azure Data Factory za poorednictwem sieci publicznej.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie fabryki danych przy użyciu interfejsu użytkownika Azure Data Factory](quickstart-create-data-factory-portal.md)

- [Wprowadzenie do usługi Azure Data Factory](introduction.md)

- [Tworzenie wizualne w Azure Data Factory](author-visually.md)

