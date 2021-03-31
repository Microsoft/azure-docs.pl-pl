---
title: Lokalizacje danych dla pulpitu wirtualnego systemu Windows (klasyczne) — Azure
description: Krótkie omówienie lokalizacji danych i metadanych pulpitu wirtualnego systemu Windows (klasycznego).
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 869defde657c9cb4c8bea6bbacebb9458e5a2b96
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88008818"
---
# <a name="data-locations-for-windows-virtual-desktop-classic"></a>Lokalizacje danych dla pulpitu wirtualnego systemu Windows (klasyczne)

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows (klasycznego), który nie obsługuje Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli próbujesz zarządzać Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows, zobacz [ten artykuł](../data-locations.md).

Pulpit wirtualny systemu Windows jest obecnie dostępny dla wszystkich lokalizacji geograficznych. Początkowo metadane usługi mogą być przechowywane tylko w lokalizacji Stany Zjednoczone (US). Administratorzy mogą wybrać lokalizację do przechowywania danych użytkownika podczas tworzenia maszyn wirtualnych puli hostów i skojarzonych usług, takich jak serwery plików. Dowiedz się więcej o usłudze Azure lokalizacje geograficzne na [mapie centrum danych platformy Azure](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>Firma Microsoft nie kontroluje ani nie ogranicza regionów, w których ty lub użytkownicy mogą uzyskiwać dostęp do danych specyficznych dla użytkownika i aplikacji.

>[!IMPORTANT]
>Pulpit wirtualny systemu Windows przechowuje globalne informacje o metadanych, takie jak nazwy dzierżawców, nazwy puli hostów, nazwy grup aplikacji i nazwy głównej użytkownika w centrum danych znajdującym się w Stany Zjednoczone. Przechowywane metadane są szyfrowane w stanie spoczynku, a dublowane geograficznie są utrzymywane w Stany Zjednoczone. Wszystkie dane klientów, takie jak ustawienia aplikacji i dane użytkownika, znajdują się w lokalizacji wybranej przez klienta i nie są zarządzane przez usługę.

Metadane usługi są replikowane w Stany Zjednoczone na potrzeby odzyskiwania po awarii.