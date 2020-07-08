---
title: dołączanie pliku
description: dołączanie pliku
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 86d03e980a233a60e4f3a6541462264dae0862af
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "74005589"
---
Ten artykuł zawiera wskazówki dotyczące tworzenia aplikacji o wysokiej wydajności przy użyciu usługi Azure Premium Storage. Możesz użyć instrukcji przedstawionych w tym dokumencie wraz z najlepszymi rozwiązaniami dotyczącymi wydajności mającymi zastosowanie do technologii używanych przez aplikację. Aby zilustrować wskazówki, firma Microsoft użyła SQL Server działającego na Premium Storage na przykład w tym dokumencie.

Chociaż w tym artykule opisano scenariusze wydajności dla warstwy magazynowania, należy zoptymalizować warstwę aplikacji. Na przykład w przypadku hostowania farmy programu SharePoint w usłudze Azure Premium Storage można użyć przykładów SQL Server z tego artykułu, aby zoptymalizować serwer bazy danych. Ponadto Zoptymalizuj serwer sieci Web i serwer aplikacji farmy programu SharePoint w celu uzyskania najwyższej wydajności.

Ten artykuł pomoże odpowiedzieć na często zadawane pytania dotyczące optymalizowania wydajności aplikacji na platformie Azure Premium Storage,

* Jak zmierzyć wydajność aplikacji?  
* Dlaczego nie widzisz oczekiwanej wysokiej wydajności?  
* Jakie czynniki wpływają na wydajność aplikacji na Premium Storage?  
* Jak te czynniki wpływają na wydajność aplikacji na Premium Storage?  
* Jak można zoptymalizować na potrzeby operacji wejścia/wyjścia na sekundę, przepustowości i opóźnień?  

Te wytyczne zostały podane w odniesieniu do Premium Storage, ponieważ obciążenia działające na Premium Storage są wysoce wrażliwe na wydajność. W razie potrzeby podano przykłady. Niektóre z tych wytycznych można także zastosować do aplikacji uruchamianych na maszynach wirtualnych IaaS przy użyciu dysków magazynu w warstwie Standardowa.