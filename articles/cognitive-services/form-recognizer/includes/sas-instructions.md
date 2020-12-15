---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/11/2020
ms.author: pafarley
ms.openlocfilehash: 8ad682ccd7feb9c7f089e9cf0f066cb1be8c756c
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97505328"
---
Aby pobrać adres URL sygnatury dostępu współdzielonego dla danych szkolenia modelu niestandardowego, przejdź do zasobu magazynu w Azure Portal i wybierz kartę **Eksplorator usługi Storage** . Przejdź do kontenera, kliknij prawym przyciskiem myszy, a następnie wybierz pozycję **Pobierz sygnaturę dostępu współdzielonego**. Ważne jest, aby uzyskać sygnaturę dostępu współdzielonego dla kontenera, a nie dla samego konta magazynu. Upewnij się, że uprawnienia do **odczytu** i **listy** są zaznaczone, a następnie kliknij przycisk **Utwórz**. Następnie skopiuj wartość z sekcji **URL** do lokalizacji tymczasowej. Powinna ona mieć postać: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
   > [!div class="mx-imgBorder"]
   > ![alt-text](../media/quickstarts/get-sas-url.png)