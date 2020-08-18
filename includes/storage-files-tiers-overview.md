---
title: dołączanie pliku
description: dołączanie pliku
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 24f92443acddb17c0a2d337f51dbf9183996c49f
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88520530"
---
Azure Files oferuje cztery różne warstwy magazynu, Premium, zoptymalizowane, gorąca i chłodna, aby umożliwić dostosowanie Twoich udziałów do wymagań dotyczących wydajności i cen w Twoim scenariuszu:

- **Premium**: udziały plików w warstwie Premium są obsługiwane przez dyski półprzewodnikowe (dysków SSD) i są wdrażane w typie **konta magazynu FileStorage** . Udziały plików w warstwie Premium zapewniają spójną wysoką wydajność i małe opóźnienia w obrębie jednocyfrowych milisekund dla większości operacji we/wy dla obciążeń intensywnie korzystających z operacji we/wy. Dzięki temu są one odpowiednie dla różnorodnych obciążeń, takich jak bazy danych, hosting witryn sieci Web i środowiska deweloperskie. 
- **Zoptymalizowane pod kątem**transakcji: udziały plików zoptymalizowane pod kątem transakcji umożliwiają duże obciążenia transakcji, które nie wymagają opóźnienia oferowanego przez udziały plików w warstwie Premium. Udziały plików zoptymalizowane pod kątem transakcji są oferowane na standardowym sprzęcie pamięci masowej (HDD) i są wdrażane w typie **konta magazynu ogólnego przeznaczenia w wersji 2 (GPv2)** . Ta warstwa magazynu ma historycznie nazywaną "standardową", jednak odnosi się to do typu nośnika magazynu, a nie do samej warstwy (gorąca i chłodna są również warstwy "standardowe", ponieważ znajdują się na standardowym sprzęcie pamięci masowej).
- **Gorąca**: aktywne udziały plików oferują magazyn zoptymalizowany pod kątem scenariuszy udostępniania plików ogólnego przeznaczenia, takich jak udziały zespołowe i Azure File Sync. Aktywne udziały plików są oferowane na standardowym sprzęcie pamięci masowej programu HDD i są wdrażane w ramach typu **konta magazynu ogólnego przeznaczenia w wersji 2 (GPv2)** .
- **Chłodna**: udziały plików w postaci chłodnej oferują oszczędny magazyn zoptymalizowany pod kątem scenariuszy magazynu archiwum online. Azure File Sync może być również dobrym obciążeniem w przypadku wolniejszych obciążeń. W przypadku urządzeń magazynu w warstwie Standardowa w systemie HDD są oferowane chłodne udziały plików, które są wdrażane w ramach **konta magazynu ogólnego przeznaczenia w wersji 2 (GPv2)** .

Udziały plików w warstwie Premium są dostępne tylko w modelu rozliczania z obsługą administracyjną. Aby uzyskać więcej informacji na temat obsługiwanego modelu rozliczania dla udziałów plików w warstwie Premium, zobacz [Omówienie udostępniania udziałów plików w warstwie Premium](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares). Standardowe udziały plików, w tym zoptymalizowane pod kątem transakcji, gorąca i chłodna, są dostępne w modelu płatność zgodnie z rzeczywistym użyciem.

Gorące i chłodne udziały plików są obecnie dostępne w następującym podzbiorze regionów publicznych (w przypadku udziałów plików zoptymalizowanych pod kątem transakcji są dostępne we wszystkich regionach platformy Azure):

- Australia Środkowa
- Australia Środkowa 2
- Australia Wschodnia
- Australia Południowo-Wschodnia
- Francja Środkowa
- Francja Południowa
- Korea Środkowa
- Korea Południowa

Aby wdrożyć gorącą lub chłodną część pliku, zobacz [Tworzenie gorącego lub chłodnego udziału plików](../articles/storage/files/storage-how-to-create-file-share.md#create-a-hot-or-cool-file-share). 