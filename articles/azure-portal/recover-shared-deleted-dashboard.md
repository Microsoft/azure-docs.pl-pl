---
title: Odzyskaj usunięty pulpit nawigacyjny w Azure Portal | Microsoft Docs
description: Po usunięciu opublikowanego pulpitu nawigacyjnego w Azure Portal można odzyskać pulpit nawigacyjny.
services: azure-portal
author: mgblythe
ms.author: mblythe
ms.date: 01/21/2020
ms.topic: troubleshooting
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 7b3cc088a87731d2a118a4fe5183831e4d1bd6cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84763979"
---
# <a name="recover-a-deleted-dashboard-in-the-azure-portal"></a>Odzyskiwanie usuniętego pulpitu nawigacyjnego w witrynie Azure Portal

Jeśli jesteś w publicznej chmurze platformy Azure i usuniesz _opublikowany_ pulpit nawigacyjny w Azure Portal, możesz odzyskać ten pulpit nawigacyjny w ciągu 14 dni od usunięcia. Jeśli jesteś w chmurze platformy Azure dla instytucji rządowych lub pulpit nawigacyjny nie jest opublikowany, nie można go odzyskać i należy go skompilować ponownie. Aby uzyskać więcej informacji na temat publikowania pulpitu nawigacyjnego, zobacz [Publikowanie pulpitu nawigacyjnego](azure-portal-dashboard-share-access.md#publish-dashboard). Wykonaj następujące kroki, aby odzyskać opublikowany pulpit nawigacyjny:

1. Z menu Azure Portal wybierz pozycję **grupy zasobów**, a następnie wybierz grupę zasobów, w której opublikowano pulpit nawigacyjny (domyślnie są to nazwanych **pulpitów nawigacyjnych**).

1. W obszarze **Dziennik aktywności**rozwiń operację **Usuń pulpit nawigacyjny** . Wybierz kartę **historia zmian** , a następnie wybierz pozycję **\<deleted resource\>** .

    ![Zrzut ekranu karty Historia zmian](media/recover-shared-deleted-dashboard/change-history-tab.png)

1. Wybierz i skopiuj zawartość okienka po lewej stronie, a następnie Zapisz w pliku tekstowym z rozszerzeniem _. JSON_ . Portal używa pliku JSON do ponownego utworzenia pulpitu nawigacyjnego.

    ![Zrzut ekranu różnic historii zmian](media/recover-shared-deleted-dashboard/change-history-diff.png)

1. Z menu Azure Portal wybierz pozycję **pulpity nawigacyjne**, a następnie wybierz pozycję **Przekaż**.

    ![Zrzut ekranu przedstawiający przekazywanie pulpitu nawigacyjnego](media/recover-shared-deleted-dashboard/dashboard-upload.png)

1. Wybierz zapisany plik JSON. Portal utworzy pulpit nawigacyjny z taką samą nazwą i elementami jak usunięty pulpit nawigacyjny.

1. Wybierz pozycję **Udostępnij** , aby opublikować pulpit nawigacyjny i ponownie nawiązać odpowiednią kontrolę dostępu.

    ![Zrzut ekranu przedstawiający udział pulpitu nawigacyjnego](media/recover-shared-deleted-dashboard/dashboard-share.png)
