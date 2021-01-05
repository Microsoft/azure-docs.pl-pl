---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/11/2020
ms.author: pafarley
ms.openlocfilehash: 3f4272f5445c8530e97726bd4fdca6bf662719c2
ms.sourcegitcommit: 02ed9acd4390b86c8432cad29075e2204f6b1bc3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/29/2020
ms.locfileid: "97807811"
---
Aby pobrać adres URL sygnatury dostępu współdzielonego dla danych szkolenia modelu niestandardowego, przejdź do zasobu magazynu w Azure Portal i wybierz kartę **Eksplorator usługi Storage** . Przejdź do kontenera, kliknij prawym przyciskiem myszy, a następnie wybierz pozycję **Pobierz sygnaturę dostępu współdzielonego**. Ważne jest, aby uzyskać sygnaturę dostępu współdzielonego dla kontenera, a nie dla samego konta magazynu. Upewnij się, że uprawnienia do **odczytu** i **listy** są zaznaczone, a następnie kliknij przycisk **Utwórz**. Następnie skopiuj wartość z sekcji **URL** do lokalizacji tymczasowej. Powinna ona mieć postać: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.