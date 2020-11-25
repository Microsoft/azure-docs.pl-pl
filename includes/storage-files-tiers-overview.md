---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7d0286b63703c165dda6cd12bb625fc64272aac1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011745"
---
Azure Files oferuje cztery różne warstwy magazynu, Premium, zoptymalizowane, gorąca i chłodna, aby umożliwić dostosowanie Twoich udziałów do wymagań dotyczących wydajności i cen w Twoim scenariuszu:

- **Premium**: udziały plików w warstwie Premium są obsługiwane przez dyski półprzewodnikowe (dysków SSD) i są wdrażane w typie **konta magazynu FileStorage** . Udziały plików w warstwie Premium zapewniają spójną wysoką wydajność i małe opóźnienia w obrębie jednocyfrowych milisekund dla większości operacji we/wy dla obciążeń intensywnie korzystających z operacji we/wy. Udziały plików w warstwie Premium są odpowiednie dla różnorodnych obciążeń, takich jak bazy danych, hosting witryn sieci Web i środowiska deweloperskie. Udziałów plików w warstwie Premium można używać w połączeniu z protokołem SMB (Server Message Block) i systemem plików NFS.
- **Zoptymalizowane pod kątem** transakcji: udziały plików zoptymalizowane pod kątem transakcji umożliwiają duże obciążenia transakcji, które nie wymagają opóźnienia oferowanego przez udziały plików w warstwie Premium. Udziały plików zoptymalizowane pod kątem transakcji są oferowane na standardowym sprzęcie pamięci masowej (HDD) i są wdrażane w typie **konta magazynu ogólnego przeznaczenia w wersji 2 (GPv2)** . Zoptymalizowana transakcja została wywołana jako "Standardowa", jednak odnosi się to do typu nośnika magazynu zamiast samej warstwy (gorąca i chłodna są również warstwy "standardowe", ponieważ znajdują się na standardowym sprzęcie pamięci masowej).
- **Gorąca**: aktywne udziały plików oferują magazyn zoptymalizowany pod kątem scenariuszy udostępniania plików ogólnego przeznaczenia, takich jak udziały zespołowe i Azure File Sync. Aktywne udziały plików są oferowane na standardowym sprzęcie pamięci masowej programu HDD i są wdrażane w ramach typu **konta magazynu ogólnego przeznaczenia w wersji 2 (GPv2)** .
- **Chłodna**: udziały plików w postaci chłodnej oferują oszczędny magazyn zoptymalizowany pod kątem scenariuszy magazynu archiwum online. Azure File Sync może być również dobrym obciążeniem w przypadku wolniejszych obciążeń. W przypadku urządzeń magazynu w warstwie Standardowa w systemie HDD są oferowane chłodne udziały plików, które są wdrażane w ramach **konta magazynu ogólnego przeznaczenia w wersji 2 (GPv2)** .

Udziały plików w warstwie Premium są dostępne tylko w modelu rozliczania z obsługą administracyjną. Aby uzyskać więcej informacji na temat obsługiwanego modelu rozliczania dla udziałów plików w warstwie Premium, zobacz [Omówienie udostępniania udziałów plików w warstwie Premium](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares). Standardowe udziały plików, w tym Optymalizacja transakcji, gorąca i chłodna, są dostępne w ramach płatności zgodnie z rzeczywistym użyciem.

Gorące i chłodne udziały plików są dostępne we wszystkich regionach publicznych i Azure Government platformy Azure. Udziały plików zoptymalizowane pod kątem transakcji są dostępne we wszystkich regionach świadczenia usługi Azure, w tym w Chinach platformy Azure i regionach platformy Azure

> [!Important]  
> Udziały plików można przenosić między warstwami w ramach typów kont magazynu GPv2 (zoptymalizowane pod kątem transakcji, gorąca i chłodna). Udział przenoszony między warstwami pociąga za sobą transakcje: przeniesienie z warstwy hotter do warstwy chłodnicy spowoduje naliczenie opłaty za transakcje zapisu warstwy dla każdego pliku w udziale, podczas gdy przejście z warstwy z chłodnicy do warstwy hotter spowoduje naliczenie opłaty za transakcje odczytu warstwy chłodnej dla każdego pliku.