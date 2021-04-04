---
title: Plik dyrektywy include
description: " — plik"
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: emuench
ms.author: mingshen
ms.date: 10/09/2020
ms.openlocfilehash: a1bc7cf1fd339ca3660c7b39326f37d2763c74b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92284829"
---
Jeśli potrzebujesz dodatkowej konfiguracji, użyj zaplanowanego zadania uruchamianego podczas uruchamiania, aby wprowadzić ostateczne zmiany do maszyny wirtualnej po jej wdrożeniu. Rozważ również następujące elementy:

- Jeśli jest to zadanie uruchamiane po raz, zadanie powinno zostać usunięte po pomyślnym zakończeniu.
- Konfiguracje nie powinny polegać na dyskach innych niż C lub D, ponieważ tylko te dwa dyski zawsze są spełnione (dysk C jest dyskiem systemu operacyjnego, a dysk D jest tymczasowym dyskiem lokalnym).

Aby uzyskać więcej informacji na temat dostosowań systemu Linux, zobacz [rozszerzenia i funkcje maszyny wirtualnej dla systemu Linux](../../virtual-machines/extensions/features-linux.md).