---
title: Lokalizacje danych dla Windows Virtual Desktop — Azure
description: Krótkie omówienie lokalizacji, w których Windows Virtual Desktop przechowywane są dane i metadane użytkownika.
author: Heidilohr
ms.topic: conceptual
ms.custom: references_regions
ms.date: 02/17/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: eeba3cb579c6ef9158379403a3206f99a2cfb060
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830632"
---
# <a name="data-and-metadata-locations-for-windows-virtual-desktop"></a>Lokalizacje danych i metadanych dla Windows Virtual Desktop

>[!IMPORTANT]
>Ta zawartość dotyczy Windows Virtual Desktop z Azure Resource Manager Windows Virtual Desktop obiektów. Jeśli używasz klasy Windows Virtual Desktop (klasycznej) bez obiektów Azure Resource Manager, zobacz [ten artykuł.](./virtual-desktop-fall-2019/data-locations-2019.md)

Windows Virtual Desktop jest obecnie dostępna dla wszystkich lokalizacji geograficznych. Administratorzy mogą wybrać lokalizację do przechowywania danych użytkownika podczas tworzenia maszyn wirtualnych puli hostów i skojarzonych usług, takich jak serwery plików. Dowiedz się więcej o lokalizacjach geograficznych platformy Azure na mapie [centrum danych platformy Azure.](https://azuredatacentermap.azurewebsites.net/)

>[!NOTE]
>Firma Microsoft nie kontroluje ani nie ogranicza regionów, w których Ty lub Twoi użytkownicy mogą uzyskać dostęp do danych użytkownika i aplikacji.

>[!IMPORTANT]
>Windows Virtual Desktop globalne informacje o metadanych, takie jak nazwy obszarów roboczych, nazwy puli hostów, nazwy grup aplikacji i główne nazwy użytkowników w centrum danych. Za każdym razem, gdy klient tworzy obiekt usługi, musi wprowadzić lokalizację obiektu usługi. Wejdą do nich lokalizacje, w których będą przechowywane metadane obiektu. Klient wybierze region świadczenia usługi Azure, a metadane będą przechowywane w powiązanej lokalizacji geograficznej. Aby uzyskać listę wszystkich regionów platformy Azure i powiązanych lokalizacji geograficznych, zobacz [Obszary geograficzne platformy Azure.](https://azure.microsoft.com/global-infrastructure/geographies/)

Obecnie obsługujemy przechowywanie metadanych w następujących lokalizacjach geograficznych:

- Stany Zjednoczone (USA) (ogólnie dostępne)
- Europa (UE) (publiczna wersja zapoznawcza) 

>[!NOTE]
> Podczas wybierania regionu, w którym mają być Windows Virtual Desktop usługi, zobaczysz regiony w lokalizacjach geograficznych USA i Unii Europejskiej. Aby upewnić się, że rozumiesz, który region będzie najlepiej działać dla Twojego wdrożenia, zobacz naszą globalną [mapę infrastruktury platformy Azure.](https://azure.microsoft.com/global-infrastructure/geographies/#geographies)

Przechowywane metadane są szyfrowane w spoczynku, a duplikaty geograficznie nadmiarowe są przechowywane w lokalizacji geograficznej. Wszystkie dane klienta, takie jak ustawienia aplikacji i dane użytkownika, znajdują się w lokalizacji wybieranej przez klienta i nie są zarządzane przez usługę. W miarę rozwoju usługi będzie dostępnych więcej lokalizacji geograficznych.

Metadane usługi są replikowane w lokalizacji geograficznej platformy Azure na potrzeby odzyskiwania po awarii.
