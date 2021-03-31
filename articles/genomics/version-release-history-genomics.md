---
title: Historia wydań
titleSuffix: Microsoft Genomics
description: Historia wersji aktualizacji Microsoft Genomics klienta języka Python dla poprawek i nowych funkcji.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 01/11/2019
ms.openlocfilehash: 20475e2cde1b42790740889cf341b3a0a0afccc0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "76991086"
---
# <a name="version-release-history"></a>Historia wydań
Zespół Microsoft Genomics regularnie aktualizuje Microsoft Genomics klienta języka Python w celu naprawienia poprawek i nowych funkcji. 

## <a name="latest-release"></a>Najnowsza wersja
Bieżący klient języka Python jest w wersji 0.9.0. Wydano Luty 6 2019 i obsługuje uruchomione przepływy pracy z GATK 3,5 i GATK4. Obsługuje ona dane wyjściowe gVCF i może akceptować opcjonalny argument kompresji danych wyjściowych.


## <a name="release-history"></a>Historia wersji 
Nowe wersje klienta języka Python Microsoft Genomics są wydawane raz na rok. Po wydaniu nowych wersji Microsoft Genomics klienta języka Python Lista poprawek i funkcji jest aktualizowana w tym miejscu. Gdy zostaną wydane nowe wersje, wcześniejsze wersje powinny być nadal obsługiwane przez co najmniej 90 dni. Jeśli wcześniejsze wersje nie są już obsługiwane, zostaną one wskazane na tej stronie. 

### <a name="version-090"></a>0.9.0 wersja
Wersja 0.9.0 obejmuje obsługę kompresji danych wyjściowych. Jest to równoznaczne z uruchomieniem `-bgzip` , a następnie `-tabix` w danych wyjściowych VCF lub gvcf. Aby uzyskać więcej informacji, zobacz [często zadawane pytania](frequently-asked-questions-genomics.md). 

### <a name="version-081"></a>0.8.1 wersja
Wersja 0.8.1 zawiera drobne poprawki błędów.  

### <a name="version-080"></a>0.8.0 wersja
Wersja 0.8.0 obejmuje obsługę GATK4 i wyprowadzania gVCFs.  

### <a name="version-074"></a>0.7.4 wersja
Wersja 0.7.4 obejmuje obsługę akceptowania tokenów SAS zamiast kluczy konta w `config.txt` danych wejściowych. Aby uzyskać więcej informacji, zobacz [Input SAS Tokens — szybki start](quickstart-input-sas.md). 

### <a name="version-073"></a>0.7.3 wersja
Wersja 0.7.3 zawiera drobne poprawki błędów.

### <a name="version-072"></a>0.7.2 wersja
Wersja 0.7.2 jest wersją początkową. Wydano Listopad 1 2017.
