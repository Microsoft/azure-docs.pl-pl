---
title: Rezydencja danych
description: Opisuje dane zamieszkania podczas korzystania z funkcji renderowania zdalnego na platformie Azure.
author: rapete
ms.author: rapete
ms.date: 02/04/2021
ms.topic: reference
ms.openlocfilehash: f20b3bb3de877ac627f5f6909c98cb9e1553f2a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "99576896"
---
# <a name="azure-remote-rendering-data-residency"></a>Dane zdalnego renderowania na platformie Azure 
W tym artykule wyjaśniono koncepcję zamieszkania i sposobu zastosowania do zdalnego renderowania platformy Azure. 

## <a name="data-residency"></a>Rezydencja danych 
Zdalne renderowanie na platformie Azure używa udostępnionych przez użytkownika kontenerów obiektów blob platformy Azure do przechowywania modeli. Gdy model nie jest używany, pozostaje w wybranym przez użytkownika regionie Blob Storage platformy Azure. Gdy model jest używany do renderowania, dane są kopiowane do regionu wybieranego przez użytkownika podczas uruchamiania sesji renderowania.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się, jak skonfigurować kontener Blob Storage platformy Azure na potrzeby renderowania zdalnego na platformie Azure, zapoznaj się z tematem [konwertowanie modelu do renderowania](../quickstarts/convert-model.md).
