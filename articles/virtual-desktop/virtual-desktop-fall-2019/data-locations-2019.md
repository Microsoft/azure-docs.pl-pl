---
title: Lokalizacje danych dla pulpitu wirtualnego systemu Windows (klasyczne) — Azure
description: Krótkie omówienie lokalizacji danych i metadanych pulpitu wirtualnego systemu Windows (klasycznego).
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 7726795582ffa3d85601ec123b58ab705442ef67
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445000"
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