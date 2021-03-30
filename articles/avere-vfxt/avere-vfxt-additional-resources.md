---
title: Dodatkowe linki dotyczące avere vFXT dla platformy Azure
description: Skorzystaj z tych zasobów, aby uzyskać dodatkowe informacje na temat avere vFXT for Azure, w tym dokumentacji dotyczącej klastrów avere i dokumentacji zarządzania vFXT.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 6f9a9f8e6d2a279871ded3dff614be99dd2901df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88271094"
---
# <a name="additional-documentation"></a>Dodatkowa dokumentacja

Ten artykuł zawiera linki do dodatkowej dokumentacji dotyczącej interfejsu zarządzania avere panelu sterowania i tematów pokrewnych.

## <a name="avere-cluster-documentation"></a>Dokumentacja klastra avere

Dodatkową dokumentację dotyczącą klastrów avere można znaleźć w witrynie sieci Web pod adresem <https://azure.github.io/Avere/> . Te dokumenty mogą pomóc zrozumieć możliwości klastra i sposób konfigurowania jego ustawień.

* [Przewodnik tworzenia klastra FXT](<https://azure.github.io/Avere/#fxt_cluster>) jest przeznaczony dla klastrów składających się z fizycznych węzłów sprzętowych, ale niektóre informacje zawarte w dokumencie dotyczą również klastrów vFXT. W szczególności nowe Administratorzy klastrów vFXT mogą skorzystać z odczytania tych sekcji:
  * [Dostosowanie ustawień obsługi i monitorowania](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_support.html#config-support>) wyjaśnia, jak dostosować obsługę ustawień przekazywania i włączyć zdalne monitorowanie.
  * [Konfigurowanie VServers i globalna przestrzeń nazw](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_vserver.html#config-vserver>) zawiera informacje o tworzeniu przestrzeni nazw dla klienta.
  * [Konfigurowanie systemu DNS dla klastra avere](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_network.html#dns-overview>) objaśnia sposób konfigurowania usługi DNS w trybie okrężnym.
  * [Dodawanie dokumentów magazynu zaplecza](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_core_filer.html#add-core-filer>) jak dodać podstawowe pliki.

* [Przewodnik konfiguracji klastra](<https://azure.github.io/Avere/#operations>) to kompletne odwołanie do ustawień i opcji klastra avere. Klaster vFXT używa podzestawu tych opcji, ale większość z tych samych stron konfiguracji ma zastosowanie.

* [Przewodnik po pulpicie nawigacyjnym](<https://azure.github.io/Avere/#operations>) wyjaśnia, jak używać funkcji monitorowania klastra w panelu sterowania avere.

## <a name="vfxt-creation-and-management-documentation"></a>Dokumentacja tworzenia i zarządzania vFXT

Pełny przewodnik dotyczący korzystania z vfxt.py, narzędzia do tworzenia i zarządzania klastrem w chmurze opartym na skrypcie znajduje się w witrynie GitHub: [Zarządzanie klastrem w chmurze za pomocą usługi vfxt.py](https://github.com/Azure/AvereSDK/blob/master/docs/README.md).
