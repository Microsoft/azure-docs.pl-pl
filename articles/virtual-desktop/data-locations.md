---
title: Lokalizacje danych dla pulpitu wirtualnego systemu Windows — Azure
description: Krótkie omówienie lokalizacji danych i metadanych pulpitu wirtualnego systemu Windows.
author: Heidilohr
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: e626b7e729e394b1012848904f5ce12279c3ef24
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88010076"
---
# <a name="data-and-metadata-locations-for-windows-virtual-desktop"></a>Lokalizacje danych i metadanych dla pulpitu wirtualnego systemu Windows

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows z Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli używasz pulpitu wirtualnego systemu Windows (klasycznego) bez Azure Resource Manager obiektów, zobacz [ten artykuł](./virtual-desktop-fall-2019/data-locations-2019.md).

Pulpit wirtualny systemu Windows jest obecnie dostępny dla wszystkich lokalizacji geograficznych. Administratorzy mogą wybrać lokalizację do przechowywania danych użytkownika podczas tworzenia maszyn wirtualnych puli hostów i skojarzonych usług, takich jak serwery plików. Dowiedz się więcej o usłudze Azure lokalizacje geograficzne na [mapie centrum danych platformy Azure](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>Firma Microsoft nie kontroluje ani nie ogranicza regionów, w których ty lub użytkownicy mogą uzyskiwać dostęp do danych specyficznych dla użytkownika i aplikacji.

>[!IMPORTANT]
>Pulpit wirtualny systemu Windows przechowuje globalne informacje o metadanych, takie jak nazwy dzierżawców, nazwy puli hostów, nazwy grup aplikacji i nazwy głównej użytkownika w centrum danych. Za każdym razem, gdy klient tworzy obiekt usługi, musi wprowadzić lokalizację dla obiektu usługi. Wprowadzona przez siebie Lokalizacja określa miejsce, w którym będą przechowywane metadane dla obiektu. Klient wybierze region świadczenia usługi Azure, a metadane będą przechowywane w powiązanej lokalizacji geograficznej. Aby zapoznać się z listą wszystkich regionów platformy Azure i powiązanych lokalizacje geograficzne, zobacz [Azure lokalizacje geograficzne](https://azure.microsoft.com/global-infrastructure/geographies/).

Obecnie obsługujemy przechowywanie metadanych w Stany Zjednoczoneej geograficznej platformy Azure. Przechowywane metadane są szyfrowane w stanie spoczynku, a dublowane nadmiarowe są utrzymywane w obrębie lokalizacji geograficznej. Wszystkie dane klientów, takie jak ustawienia aplikacji i dane użytkownika, znajdują się w lokalizacji wybranej przez klienta i nie są zarządzane przez usługę. Coraz więcej lokalizacje geograficzne stanie się dostępna w miarę rozwoju usługi.

Metadane usługi są replikowane w obrębie lokalizacji geograficznej platformy Azure na potrzeby odzyskiwania po awarii.