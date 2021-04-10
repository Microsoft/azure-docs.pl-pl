---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 01/04/2021
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 5658b68081fae8dab528030f7fc1cd6fe4935730
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102178363"
---
- Nie można włączyć obsługi serii na żądanie na dysku SSD w warstwie Premium, który ma mniej niż 512 GiB. Dysków SSD w warstwie Premium mniejsza niż 512 GiB zawsze będzie korzystać z rozliczeń opartych na kredycie.
- Obsługa serii na żądanie jest obsługiwana tylko w przypadku wersji Premium dysków SSD. Jeśli dysk SSD Premium z włączoną obsługą serii na żądanie jest przełączany na inny typ dysku, nastąpi wyłączenie dysków.
- Zmiany warstwy wydajności na żądanie nie są automatycznie wyłączane. Jeśli chcesz zmienić warstwę wydajności, ale nie chcesz zachować obciążeń dysków, musisz ją wyłączyć.
- Możliwość obsługi serii na żądanie może być włączona tylko wtedy, gdy dysk jest odłączony od maszyny wirtualnej lub gdy maszyna wirtualna jest zatrzymana. Na żądanie można wyłączyć funkcję 12 godzin od jej włączenia.