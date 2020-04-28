---
title: Dodatkowe linki dotyczące avere vFXT dla platformy Azure
description: Linki do dodatkowych informacji na temat avere vFXT dla platformy Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 78e0500057baf407f1d26afe29d9df1e525cbf3d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76153772"
---
# <a name="additional-documentation"></a>Dodatkowa dokumentacja

Ten artykuł zawiera linki do dodatkowej dokumentacji dotyczącej interfejsu zarządzania avere panelu sterowania i tematów pokrewnych.

## <a name="avere-cluster-documentation"></a>Dokumentacja klastra avere

Dodatkową dokumentację dotyczącą klastrów avere można znaleźć w witrynie sieci <https://azure.github.io/Avere/>Web pod adresem. Te dokumenty mogą pomóc zrozumieć możliwości klastra i sposób konfigurowania jego ustawień.

* [Przewodnik tworzenia klastra FXT](<https://azure.github.io/Avere/#fxt_cluster>) jest przeznaczony dla klastrów składających się z fizycznych węzłów sprzętowych, ale niektóre informacje zawarte w dokumencie dotyczą również klastrów vFXT. W szczególności nowe Administratorzy klastrów vFXT mogą skorzystać z odczytania tych sekcji:
  * [Dostosowanie ustawień obsługi i monitorowania](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_support.html#config-support>) wyjaśnia, jak dostosować obsługę ustawień przekazywania i włączyć zdalne monitorowanie.
  * [Konfigurowanie VServers i globalna przestrzeń nazw](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_vserver.html#config-vserver>) zawiera informacje o tworzeniu przestrzeni nazw dla klienta.
  * [Konfigurowanie systemu DNS dla klastra avere](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_network.html#dns-overview>) objaśnia sposób konfigurowania usługi DNS w trybie okrężnym.
  * [Dodawanie dokumentów magazynu zaplecza](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_core_filer.html#add-core-filer>) jak dodać podstawowe pliki.

* [Przewodnik konfiguracji klastra](<https://azure.github.io/Avere/#operations>) to kompletne odwołanie do ustawień i opcji klastra avere. Klaster vFXT używa podzestawu tych opcji, ale większość z tych samych stron konfiguracji ma zastosowanie.

* [Przewodnik po pulpicie nawigacyjnym](<https://azure.github.io/Avere/#operations>) wyjaśnia, jak używać funkcji monitorowania klastra w panelu sterowania avere.

## <a name="vfxt-creation-and-management-documentation"></a>Dokumentacja tworzenia i zarządzania vFXT

Pełny przewodnik dotyczący korzystania z vfxt.py, narzędzia do tworzenia i zarządzania klastrem w chmurze opartym na skrypcie znajduje się w witrynie GitHub: [Zarządzanie klastrem w chmurze za pomocą usługi vfxt.py](https://github.com/Azure/AvereSDK/blob/master/docs/README.md).
