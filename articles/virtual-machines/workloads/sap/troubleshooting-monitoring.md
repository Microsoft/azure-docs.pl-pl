---
title: Monitorowanie SAP HANA na platformie Azure (duże wystąpienia) | Microsoft Docs
description: Monitoruj SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 246b9398824597ec337ee9e9ea3dc24267311f60
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101669600"
---
# <a name="how-to-monitor-sap-hana-large-instances-on-azure"></a>Jak monitorować SAP HANA (duże wystąpienia) na platformie Azure

SAP HANA na platformie Azure (duże wystąpienia) nie różnią się od innych wdrożeń IaaS — należy monitorować działanie systemu operacyjnego i aplikacji oraz sposób, w jaki aplikacje używają następujących zasobów:

- Procesor CPU
- Memory (Pamięć)
- Przepustowość sieci
- Miejsce na dysku

Za pomocą usługi Azure Virtual Machines należy ustalić, czy klasy zasobów nazwane powyżej są wystarczające, czy są one zubożone. Poniżej przedstawiono bardziej szczegółowe informacje na temat każdej z różnych klas:

Użycie **zasobów procesora CPU:** Współczynnik, który został zdefiniowany dla niektórych obciążeń względem platformy HANA, jest wymuszany, aby upewnić się, że dostępne są wystarczające zasoby procesora CPU do pracy za pomocą danych przechowywanych w pamięci. Niemniej jednak mogą wystąpić sytuacje, w których HANA zużywa wiele procesorów wykonujących zapytania z powodu brakujących indeksów lub podobnych problemów. Oznacza to, że należy monitorować użycie zasobów procesora dla jednostki dużego wystąpienia HANA, a także zasoby procesora używane przez konkretne usługi platformy HANA.

**Użycie pamięci:** Jest ważne, aby monitorować z poziomu platformy HANA, a także poza platformą HANA w jednostce. W ramach platformy HANA Monitoruj, jak dane zużywają pamięć przydzieloną platformy HANA, aby zachować w ramach wymaganych wytycznych dotyczących ustalania wielkości dla SAP. Warto również monitorować użycie pamięci na poziomie dużego wystąpienia, aby upewnić się, że dodatkowe zainstalowane oprogramowanie inne niż HANA nie zużywa zbyt dużej ilości pamięci i w związku z tym konkuruje z platformą HANA dla pamięci.

**Przepustowość sieci:** Brama sieci wirtualnej platformy Azure ma ograniczoną przepustowość danych przenoszonych do sieci wirtualnej platformy Azure, dlatego warto monitorować dane odbierane przez wszystkie maszyny wirtualne platformy Azure w sieci wirtualnej, aby ustalić, jak zamykasz limity wybranej jednostki SKU bramy platformy Azure. W jednostce dużego wystąpienia programu HANA warto monitorować również ruch sieciowy przychodzący i wychodzący oraz śledzić woluminy, które są obsługiwane w czasie.

**Miejsce na dysku:** Użycie miejsca na dysku zwykle rośnie w miarę upływu czasu. Najczęstszymi przyczynami są: zwiększenie ilości danych, wykonanie kopii zapasowych dziennika transakcji, przechowywanie plików śledzenia i wykonywanie migawek magazynu. W związku z tym ważne jest, aby monitorować użycie miejsca na dysku i zarządzać przestrzenią dyskową skojarzoną z jednostką dużego wystąpienia HANA.

Dla **jednostek SKU typu II** dużych wystąpień Hana serwer zawiera wstępnie załadowanych narzędzi diagnostycznych systemu. Możesz użyć tych narzędzi diagnostycznych, aby przeprowadzić kontrolę kondycji systemu. Uruchom następujące polecenie, aby wygenerować plik dziennika kontroli kondycji w/var/log/health_check.
```
/opt/sgi/health_check/microsoft_tdi.sh
```
Podczas pracy z zespołem pomoc techniczna firmy Microsoft w celu rozwiązania problemu może być również poproszony o podanie plików dziennika za pomocą tych narzędzi diagnostycznych. Plik można zip przy użyciu poniższego polecenia.
```
tar  -czvf health_check_logs.tar.gz /var/log/health_check
```

**Następne kroki**

- Zobacz [, jak monitorować SAP HANA (duże wystąpienia) na platformie Azure](./hana-monitor-troubleshoot.md).
