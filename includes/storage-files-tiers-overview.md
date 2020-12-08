---
title: plik dołączania
description: plik dołączania
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 741ace82a5ec0b44215e4a2df6c64f5b92e0c275
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96842715"
---
Azure Files oferuje cztery różne warstwy magazynu, Premium, zoptymalizowane, gorąca i chłodna, aby umożliwić dostosowanie Twoich udziałów do wymagań dotyczących wydajności i cen w Twoim scenariuszu:

- **Premium**: udziały plików w warstwie Premium są obsługiwane przez dyski półprzewodnikowe (dysków SSD) i zapewniają spójną wysoką wydajność i małe opóźnienia w ramach większości operacji we/wy na potrzeby obciążeń intensywnie korzystających z we/wy. Udziały plików w warstwie Premium są odpowiednie dla różnorodnych obciążeń, takich jak bazy danych, hosting witryn sieci Web i środowiska deweloperskie. Udziałów plików w warstwie Premium można używać w połączeniu z protokołem SMB (Server Message Block) i systemem plików NFS.
- **Zoptymalizowane pod kątem** transakcji: udziały plików zoptymalizowane pod kątem transakcji umożliwiają duże obciążenia transakcji, które nie wymagają opóźnienia oferowanego przez udziały plików w warstwie Premium. Udziały plików zoptymalizowane pod kątem transakcji są oferowane na standardowym sprzęcie pamięci masowej, który jest używany przez dyski twarde (HDD). Zoptymalizowana transakcja została wywołana jako "Standardowa", jednak odnosi się to do typu nośnika magazynu zamiast samej warstwy (gorąca i chłodna są również warstwy "standardowe", ponieważ znajdują się na standardowym sprzęcie pamięci masowej).
- **Gorąca**: aktywne udziały plików oferują magazyn zoptymalizowany pod kątem scenariuszy udostępniania plików ogólnego przeznaczenia, takich jak udziały zespołowe i Azure File Sync. Aktywne udziały plików są oferowane na standardowym sprzęcie pamięci masowej, którego kopia zapasowa jest HDD.
- **Chłodna**: udziały plików w postaci chłodnej oferują oszczędny magazyn zoptymalizowany pod kątem scenariuszy magazynu archiwum online. Azure File Sync może być również dobrym obciążeniem w przypadku wolniejszych obciążeń. W przypadku sprzętu magazynu w warstwie Standardowa obsługiwanego przez HDD są oferowane chłodne udziały plików.

Udziały plików w warstwie Premium są wdrażane w ramach **konta magazynu FileStorage** i są dostępne tylko w modelu rozliczania z zainicjowaną obsługą administracyjną. Aby uzyskać więcej informacji na temat obsługiwanego modelu rozliczania dla udziałów plików w warstwie Premium, zobacz [Omówienie udostępniania udziałów plików w warstwie Premium](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares). Standardowe udziały plików, w tym zoptymalizowane pod kątem transakcji, gorąca i chłodne udziały plików, są wdrażane w ramach **konta magazynu ogólnego przeznaczenia w wersji 2 (GPv2)** i są dostępne w ramach płatności zgodnie z rzeczywistym użyciem. Gorące i chłodne udziały plików są dostępne we wszystkich regionach publicznych i Azure Government platformy Azure. Udziały plików zoptymalizowane pod kątem transakcji są dostępne we wszystkich regionach świadczenia usługi Azure, w tym w Chinach platformy Azure i regionach platformy Azure

Podczas wybierania warstwy magazynowania dla obciążenia należy wziąć pod uwagę wymagania dotyczące wydajności i użycia. Jeśli obciążenie wymaga jednocyfrowego opóźnienia lub używasz nośnika magazynu SSD lokalnie, warstwa Premium prawdopodobnie najlepiej pasuje. Jeśli małe opóźnienia nie są tak duże, jak na przykład udziały zespołu zainstalowane lokalnie na platformie Azure lub w pamięci podręcznej przy użyciu Azure File Sync, magazyn w warstwie Standardowa może być lepszym rozwiązaniem z punktu widzenia kosztów.

Zoptymalizowane, gorąca i chłodna warstwa są przechowywane na dokładnie tym samym standardowym sprzęcie pamięci masowej. Główną różnicą między tymi trzema warstwami są ceny za magazyn danych w czasie spoczynku, które są niższe w warstwach usługi chłodziark i ceny transakcji, które są wyższe w warstwach chłodnicy. Składają się na to następujące elementy:

- Transakcja zoptymalizowana pod względem nazwy wskazuje, że optymalizuje ceny za duże obciążenia transakcji. Transakcja jest zoptymalizowana pod kątem najwyższej ceny za magazyn w spoczynku, ale najniższych cen transakcji.
- Gorąca jest dla aktywnych obciążeń, które nie obejmują dużej liczby transakcji, i ma nieco niższe ceny za magazyn w spoczynku, ale znacznie wyższe ceny transakcji w porównaniu z optymalizacją transakcji.
- Chłodna optymalizuje ceny dla obciążeń, które nie mają dużej aktywności, oferując najniższą cenę za przechowywanie danych w spoczynku, ale najwyższe ceny transakcji.

Twoje obciążenie i poziom aktywności określają najbardziej opłacalną warstwę dla standardowego udziału plików. Możesz wyświetlić dokładne ceny dla żądanego regionu, poziomu nadmiarowości i waluty na [stronie cennika Azure Files](https://azure.microsoft.com/pricing/details/storage/files/).

Po utworzeniu udziału plików na koncie magazynu nie można przenieść go do warstw z wyłączeniem do różnych rodzajów kont magazynu. Na przykład w celu przeniesienia udziału plików zoptymalizowanego pod kątem transakcji do warstwy Premium należy utworzyć nowy udział plików na koncie magazynu FileStorage i skopiować dane z oryginalnego udziału do nowego udziału plików na koncie FileStorage. Zalecamy używanie AzCopy do kopiowania danych między udziałami plików platformy Azure, ale można również używać takich narzędzi jak `robocopy` w przypadku systemu Windows lub `rsync` dla MacOS i Linux. 

Udziały plików wdrożone w ramach kont magazynu GPv2 można przenosić między warstwami Standard (zoptymalizowane pod kątem transakcji, gorącą i chłodną) bez tworzenia nowego konta magazynu i migrowania danych, ale podczas zmieniania warstwy będziesz ponosić koszty transakcji. Po przeniesieniu udziału z warstwy hotter do warstwy usługi w ramach usługi chłodnicy naliczane są opłaty za transakcje zapisu warstwy dla każdego pliku w udziale. Przeniesienie udziału plików z warstwy chłodnicy do warstwy hotter spowoduje naliczenie opłaty za transakcje odczytu z warstwy chłodnej dla każdego pliku w udziale.