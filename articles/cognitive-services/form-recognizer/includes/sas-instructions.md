---
author: laujan
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/11/2020
ms.author: lajanuar
ms.openlocfilehash: 8bf78d4c2f703ee10b26b1936620f7cf23ed7c14
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467208"
---
Aby pobrać adres URL sygnatury dostępu współdzielonego dla danych szkolenia modelu niestandardowego, przejdź do zasobu magazynu w Azure Portal i wybierz kartę **Eksplorator usługi Storage** . Przejdź do kontenera, kliknij prawym przyciskiem myszy, a następnie wybierz pozycję **Pobierz sygnaturę dostępu współdzielonego**. Ważne jest, aby uzyskać sygnaturę dostępu współdzielonego dla kontenera, a nie dla samego konta magazynu. Upewnij się, że zaznaczone są uprawnienia **Odczyt**, **zapis**, **usuwanie** i **Wyświetlanie** , a następnie kliknij przycisk **Utwórz**. Następnie skopiuj wartość z sekcji **URL** do lokalizacji tymczasowej. Powinna ona mieć postać: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
