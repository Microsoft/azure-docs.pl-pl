---
title: Lokalizacje danych dla pulpitu wirtualnego systemu Windows — Azure
description: Krótkie omówienie lokalizacji danych i metadanych pulpitu wirtualnego systemu Windows.
author: Heidilohr
ms.topic: conceptual
ms.custom: references_regions
ms.date: 02/17/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 12ec71a86a5df5954c14097e6a0ec5c8a5138fc5
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652432"
---
# <a name="data-and-metadata-locations-for-windows-virtual-desktop"></a>Lokalizacje danych i metadanych dla pulpitu wirtualnego systemu Windows

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows z Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli używasz pulpitu wirtualnego systemu Windows (klasycznego) bez Azure Resource Manager obiektów, zobacz [ten artykuł](./virtual-desktop-fall-2019/data-locations-2019.md).

Pulpit wirtualny systemu Windows jest obecnie dostępny dla wszystkich lokalizacji geograficznych. Administratorzy mogą wybrać lokalizację do przechowywania danych użytkownika podczas tworzenia maszyn wirtualnych puli hostów i skojarzonych usług, takich jak serwery plików. Dowiedz się więcej o usłudze Azure lokalizacje geograficzne na [mapie centrum danych platformy Azure](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>Firma Microsoft nie kontroluje ani nie ogranicza regionów, w których ty lub użytkownicy mogą uzyskiwać dostęp do danych specyficznych dla użytkownika i aplikacji.

>[!IMPORTANT]
>Pulpit wirtualny systemu Windows przechowuje globalne informacje o metadanych, takie jak nazwy dzierżawców, nazwy puli hostów, nazwy grup aplikacji i nazwy głównej użytkownika w centrum danych. Za każdym razem, gdy klient tworzy obiekt usługi, musi wprowadzić lokalizację dla obiektu usługi. Wprowadzona przez siebie Lokalizacja określa miejsce, w którym będą przechowywane metadane dla obiektu. Klient wybierze region świadczenia usługi Azure, a metadane będą przechowywane w powiązanej lokalizacji geograficznej. Aby zapoznać się z listą wszystkich regionów platformy Azure i powiązanych lokalizacje geograficzne, zobacz [Azure lokalizacje geograficzne](https://azure.microsoft.com/global-infrastructure/geographies/).

Obecnie obsługujemy przechowywanie metadanych w następujących lokalizacje geograficzne:

- Stany Zjednoczone (US) (ogólnie dostępna)
- Europa (UE) (publiczna wersja zapoznawcza) 

>[!NOTE]
> Po wybraniu regionu w celu utworzenia obiektów usługi pulpitu wirtualnego systemu Windows w programie zobaczysz regiony należące do USA i UE lokalizacje geograficzne. Aby upewnić się, który region będzie najlepiej działał dla danego wdrożenia, zapoznaj się z [naszą globalną mapą infrastruktury platformy Azure](https://azure.microsoft.com/global-infrastructure/geographies/#geographies).

Przechowywane metadane są szyfrowane w stanie spoczynku, a dublowane nadmiarowe są utrzymywane w obrębie lokalizacji geograficznej. Wszystkie dane klientów, takie jak ustawienia aplikacji i dane użytkownika, znajdują się w lokalizacji wybranej przez klienta i nie są zarządzane przez usługę. Coraz więcej lokalizacje geograficzne stanie się dostępna w miarę rozwoju usługi.

Metadane usługi są replikowane w obrębie lokalizacji geograficznej platformy Azure na potrzeby odzyskiwania po awarii.