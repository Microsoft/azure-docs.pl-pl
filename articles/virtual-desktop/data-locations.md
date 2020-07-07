---
title: Lokalizacje danych dla pulpitu wirtualnego systemu Windows — Azure
description: Krótkie omówienie lokalizacji danych i metadanych pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 853f5766c4fd6344eecfd7be1d7911163133a8a5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82611538"
---
# <a name="data-locations-for-windows-virtual-desktop"></a>Lokalizacje danych dla pulpitu wirtualnego systemu Windows

>[!IMPORTANT]
>Ta zawartość ma zastosowanie do aktualizacji wiosennej 2020 z Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli używasz pulpitu wirtualnego systemu Windows, wykorzystaj wersję 2019 bez obiektów Azure Resource Manager, zobacz [ten artykuł](./virtual-desktop-fall-2019/data-locations-2019.md).
>
> Aktualizacja systemu Windows Virtual Desktop wiosna 2020 jest obecnie dostępna w publicznej wersji zapoznawczej. Ta wersja zapoznawcza jest świadczona bez umowy dotyczącej poziomu usług i nie zalecamy jej używania w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. 
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pulpit wirtualny systemu Windows jest obecnie dostępny dla wszystkich lokalizacji geograficznych. Administratorzy mogą wybrać lokalizację do przechowywania danych użytkownika podczas tworzenia maszyn wirtualnych puli hostów i skojarzonych usług, takich jak serwery plików. Dowiedz się więcej o usłudze Azure lokalizacje geograficzne na [mapie centrum danych platformy Azure](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>Firma Microsoft nie kontroluje ani nie ogranicza regionów, w których ty lub użytkownicy mogą uzyskiwać dostęp do danych specyficznych dla użytkownika i aplikacji.

>[!IMPORTANT]
>Pulpit wirtualny systemu Windows przechowuje globalne informacje o metadanych, takie jak nazwy dzierżawców, nazwy puli hostów, nazwy grup aplikacji i nazwy głównej użytkownika w centrum danych. Za każdym razem, gdy klient tworzy obiekt usługi, musi wprowadzić lokalizację dla obiektu usługi. Wprowadzona przez siebie Lokalizacja określa miejsce, w którym będą przechowywane metadane dla obiektu. Klient wybierze region świadczenia usługi Azure, a metadane będą przechowywane w powiązanej lokalizacji geograficznej. Aby zapoznać się z listą wszystkich regionów platformy Azure i powiązanych lokalizacje geograficzne, zobacz [Azure lokalizacje geograficzne](https://azure.microsoft.com/global-infrastructure/geographies/).

Obecnie obsługujemy przechowywanie metadanych w Stany Zjednoczoneej geograficznej platformy Azure. Przechowywane metadane są szyfrowane w stanie spoczynku, a dublowane nadmiarowe są utrzymywane w obrębie lokalizacji geograficznej. Wszystkie dane klientów, takie jak ustawienia aplikacji i dane użytkownika, znajdują się w lokalizacji wybranej przez klienta i nie są zarządzane przez usługę. Coraz więcej lokalizacje geograficzne stanie się dostępna w miarę rozwoju usługi.

Metadane usługi są replikowane w obrębie lokalizacji geograficznej platformy Azure na potrzeby odzyskiwania po awarii.