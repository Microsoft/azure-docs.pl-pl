---
author: laujan
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: ed5f94bdc372e317e9e312db07665d233b1b4139
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103467308"
---
Przejdź do Azure Portal i <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title=" Utwórz nowy formularz aparatu rozpoznawania, " target="_blank"> Utwórz nowy zasób aparatu rozpoznawania </a> . W okienku **Tworzenie** podaj następujące informacje:

| Szczegóły projektu   | Opis   |
|--|--|
| **Subskrypcja** | Wybierz subskrypcję platformy Azure, której udzielono dostępu. |
| **Grupa zasobów** | [Grupa zasobów platformy Azure](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group) , która będzie zawierać zasób. Można utworzyć nową grupę lub dodać ją do istniejącej grupy. |
| **Region** | Lokalizacja wystąpienia usługi poznawczej. Różne lokalizacje mogą wprowadzać opóźnienia, ale nie mają wpływu na dostępność zasobu przez środowisko uruchomieniowe. |
| **Nazwa** | Opisowa nazwa zasobu. Zalecamy użycie nazwy opisowej, na przykład *MyNameFormRecognizer*. |
| **Warstwa cenowa** | Koszt zasobu zależy od wybranej warstwy cenowej i użycia. Aby uzyskać więcej informacji, zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/cognitive-services/)interfejsu API.

> [!NOTE]
> Zwykle podczas tworzenia zasobu usługi poznawczej w Azure Portal można utworzyć klucz subskrypcji dla wielu usług (używany przez wiele usług poznawczych) lub klucz subskrypcji jednej usługi (używany tylko w przypadku konkretnej usługi poznawczej). Obecnie aparat rozpoznawania formularzy nie jest uwzględniony w subskrypcji wielousługowej.

Po zakończeniu wdrażania zasobu aparatu rozpoznawania formularzy Znajdź i wybierz go z listy **wszystkie zasoby** w portalu. Klucz i punkt końcowy będą znajdować się na stronie klucz zasobu i punkt końcowy w obszarze Zarządzanie zasobami. Zapisz oba te elementy w lokalizacji tymczasowej przed przejściem do przodu.
