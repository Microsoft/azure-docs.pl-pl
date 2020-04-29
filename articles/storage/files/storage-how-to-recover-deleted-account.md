---
title: W jaki sposób odzyskać usunięte konto magazynu
description: Dowiedz się, jak odzyskać usunięte konto magazynu
author: todmccoy
manager: dcscontentpm
ms.service: storage
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: rogarana
ms.subservice: files
services: storage
tags: ''
ms.openlocfilehash: 05465d4a03335ac607ba8981116c66fd6dac9416
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78252629"
---
# <a name="how-to-recover-a-deleted-storage-account"></a>W jaki sposób odzyskać usunięte konto magazynu

Usługa Azure Storage zapewnia odporność na dane za pośrednictwem zautomatyzowanych replik, ale nie zapobiega uszkodzeniu i złośliwemu używaniu danych przez użytkowników ani kod aplikacji. Zachowanie dokładności danych podczas wystąpienia błędu aplikacji lub użytkownika wymaga bardziej zaawansowanych technik, takich jak kopiowanie danych do pomocniczej lokalizacji magazynu z dziennikiem inspekcji.

W poniższej tabeli przedstawiono omówienie zakresu odzyskiwania konta magazynu w zależności od strategii replikacji.

| |LRS|ZRS|GRS|RA-GRS|
|---|---|---|---|---|
|Azure Resource Manager konta magazynu|Tak|Tak|Tak|Tak|
|Konto magazynu klasyczne|Tak|Tak|Tak|Tak|

Zbierz następujące informacje i Zastąp żądanie pomocy technicznej pomoc techniczna firmy Microsoft:

* Nazwa konta magazynu
* Data usunięcia
* Region konta magazynu
* Jak zostało usunięte konto magazynu?
* Jaka metoda została usunięta z konta magazynu? (Portal, PowerShell itd.)

Ważne punkty

* Może to potrwać do 15 dni od momentu usunięcia usługi magazynu w celu wykonania wyrzucania elementów bezużytecznych, dzięki czemu odzyskiwanie kont magazynu może nie być odzyskiwane z umową SLA.
* Pomoc techniczna firmy Microsoft podejmie próbę odzyskania kontenera/konta na podstawie najlepszego wysiłku i nie gwarantuje odzyskania.

> [!NOTE]
> Odzyskiwanie może zakończyć się niepowodzeniem, jeśli konto zostało utworzone na nowo. Jeśli konto zostało już utworzone ponownie, należy najpierw usunąć je przed podjęciem próby odzyskania.
