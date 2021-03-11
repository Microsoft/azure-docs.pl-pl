---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/11/2020
ms.author: pafarley
ms.openlocfilehash: 45ac0e74733ade9801a6f3624d6200c594eff698
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102623479"
---
Aby pobrać adres URL sygnatury dostępu współdzielonego dla danych szkolenia modelu niestandardowego, przejdź do zasobu magazynu w Azure Portal i wybierz kartę **Eksplorator usługi Storage** . Przejdź do kontenera, kliknij prawym przyciskiem myszy, a następnie wybierz pozycję **Pobierz sygnaturę dostępu współdzielonego**. Ważne jest, aby uzyskać sygnaturę dostępu współdzielonego dla kontenera, a nie dla samego konta magazynu. Upewnij się, że zaznaczone są uprawnienia **Odczyt**, **zapis**, **usuwanie** i **Wyświetlanie** , a następnie kliknij przycisk **Utwórz**. Następnie skopiuj wartość z sekcji **URL** do lokalizacji tymczasowej. Powinna ona mieć postać: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
