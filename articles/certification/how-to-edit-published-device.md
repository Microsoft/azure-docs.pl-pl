---
title: Jak edytować opublikowane urządzenie z certyfikatem platformy Azure
description: Przewodnik po edytowaniu informacji o urządzeniu po certyfikowaniu i opublikowaniu urządzenia za pomocą programu certyfikowanego urządzenia platformy Azure.
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: how-to
ms.date: 03/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 14a29d438103f07dd35b3a3380b7cc094f215824
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304447"
---
# <a name="edit-your-published-device"></a>Edytowanie opublikowanego urządzenia

Gdy urządzenie zostanie certyfikowane i opublikowane w katalogu certyfikowanych urządzeń platformy Azure, może być konieczne zaktualizowanie szczegółów urządzenia. Może to być spowodowane aktualizacją listy dystrybutorów, zmianami adresów URL stron zakupu lub aktualizacjami specyfikacji sprzętu (takich jak wersja systemu operacyjnego lub dodaniem nowego składnika). Za pomocą portalu z certyfikatem platformy Azure Ułatwiamy aktualizowanie informacji o urządzeniu bez usuwania produktu z naszego katalogu.

## <a name="prerequisites"></a>Wymagania wstępne

- Należy się zalogować i mieć **zatwierdzony** projekt dla urządzenia w [portalu certyfikowanych urządzeń platformy Azure](https://certify.azure.com). Jeśli nie masz certyfikowanego urządzenia, możesz wyświetlić ten [samouczek](tutorial-01-creating-your-project.md) , aby rozpocząć pracę.

## <a name="editing-your-published-project"></a>Edytowanie opublikowanego projektu

Na stronie Podsumowanie projektu należy zauważyć, że projekt jest w trybie tylko do odczytu, ponieważ został już zrecenzowany i zaakceptowany. Aby wprowadzić zmiany, trzeba będzie zażądać edycji projektu i zaktualizować aktualizację przez zespół certyfikacji platformy Azure.

1. Kliknij `Request Metadata Edit` przycisk w górnej części strony  

    ![Żądaj aktualizacji metadanych](./media/images/request-metadata-edit.png)

1. Potwierdź powiadomienie na stronie, że użytkownik będzie musiał przesłać produkt do recenzji po jego edycji.
    > [!NOTE]
    > Potwierdzenie tej edycji spowoduje, że urządzenie **nie** zostanie usunięte z wykazu urządzeń z certyfikatem platformy Azure, jeśli został już opublikowany. Twoja Poprzednia wersja produktu pozostanie w wykazie do momentu ponownego opublikowania urządzenia.

1. Po podaniu tego ostrzeżenia można edytować szczegóły urządzenia. Upewnij się, że zawarto adnotację w `Comments for Reviewer` sekcji `Device Details` co zostało zmienione.

    ![Uwaga dotycząca edytowania metadanych](./media/images/edit-notes.png)

1. Na stronie Podsumowanie projektu kliknij, `Submit for review` Aby zmiany zostały zatwierdzone przez zespół certyfikacji platformy Azure.
1. Po przejrzeniu i zatwierdzeniu zmian możesz ponownie opublikować zmiany w katalogu za pomocą portalu (Zobacz nasz [samouczek](./tutorial-04-publishing-your-device.md)).

## <a name="next-steps"></a>Następne kroki

Pomyślnie edytowano urządzenie w katalogu certyfikowanych urządzeń platformy Azure. Możesz wyewidencjonować zmiany w wykazie lub zatwierdzić inne urządzenie.
- [Wykaz urządzeń z certyfikatem platformy Azure](https://devicecatalog.azure.com/)
- [Wprowadzenie do certyfikowania urządzenia](./tutorial-01-creating-your-project.md)
