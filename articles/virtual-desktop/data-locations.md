---
title: Lokalizacje danych dla pulpitu wirtualnego systemu Windows — Azure
description: Krótkie omówienie lokalizacji danych i metadanych pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5f48586f38c310c77c61f470d3e2fbc819c33f71
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79128042"
---
# <a name="data-locations-for-windows-virtual-desktop"></a>Lokalizacje danych dla pulpitu wirtualnego systemu Windows

Pulpit wirtualny systemu Windows jest obecnie dostępny dla wszystkich lokalizacji geograficznych. Początkowo metadane usługi mogą być przechowywane tylko w lokalizacji Stany Zjednoczone (US). Administratorzy mogą wybrać lokalizację do przechowywania danych użytkownika podczas tworzenia maszyn wirtualnych puli hostów i skojarzonych usług, takich jak serwery plików. Dowiedz się więcej o usłudze Azure lokalizacje geograficzne na [mapie centrum danych platformy Azure](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>Firma Microsoft nie kontroluje ani nie ogranicza regionów, w których ty lub użytkownicy mogą uzyskiwać dostęp do danych specyficznych dla użytkownika i aplikacji.

>[!IMPORTANT]
>Pulpit wirtualny systemu Windows przechowuje globalne informacje o metadanych, takie jak nazwy dzierżawców, nazwy puli hostów, nazwy grup aplikacji i nazwy głównej użytkownika w centrum danych znajdującym się w Stany Zjednoczone. Przechowywane metadane są szyfrowane w stanie spoczynku, a dublowane geograficznie są utrzymywane w Stany Zjednoczone. Wszystkie dane klientów, takie jak ustawienia aplikacji i dane użytkownika, znajdują się w lokalizacji wybranej przez klienta i nie są zarządzane przez usługę.

Metadane usługi są replikowane w Stany Zjednoczone na potrzeby odzyskiwania po awarii.