---
title: Odzyskiwanie usuniętego pulpitu nawigacyjnego w witrynie Azure Portal
description: Po usunięciu opublikowanego pulpitu nawigacyjnego w Azure Portal można odzyskać pulpit nawigacyjny.
ms.date: 01/21/2020
ms.topic: troubleshooting
ms.openlocfilehash: 095964691a3cb22f8a805af2e8fe37af4c47cb28
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96745625"
---
# <a name="recover-a-deleted-dashboard-in-the-azure-portal"></a>Odzyskiwanie usuniętego pulpitu nawigacyjnego w witrynie Azure Portal

Jeśli jesteś w publicznej chmurze platformy Azure i usuniesz _opublikowany_ pulpit nawigacyjny w Azure Portal, możesz odzyskać ten pulpit nawigacyjny w ciągu 14 dni od usunięcia. Jeśli jesteś w chmurze platformy Azure dla instytucji rządowych lub pulpit nawigacyjny nie jest opublikowany, nie można go odzyskać i należy go skompilować ponownie. Aby uzyskać więcej informacji na temat publikowania pulpitu nawigacyjnego, zobacz [Publikowanie pulpitu nawigacyjnego](azure-portal-dashboard-share-access.md#publish-dashboard). Wykonaj następujące kroki, aby odzyskać opublikowany pulpit nawigacyjny:

1. Z menu Azure Portal wybierz pozycję **grupy zasobów**, a następnie wybierz grupę zasobów, w której opublikowano pulpit nawigacyjny (domyślnie są to nazwanych **pulpitów nawigacyjnych**).

1. W obszarze **Dziennik aktywności** rozwiń operację **Usuń pulpit nawigacyjny** . Wybierz kartę **historia zmian** , a następnie wybierz pozycję **\<deleted resource\>** .

    ![Zrzut ekranu karty Historia zmian](media/recover-shared-deleted-dashboard/change-history-tab.png)

1. Wybierz i skopiuj zawartość okienka po lewej stronie, a następnie Zapisz w pliku tekstowym z rozszerzeniem _. JSON_ . Portal używa pliku JSON do ponownego utworzenia pulpitu nawigacyjnego.

    ![Zrzut ekranu różnic historii zmian](media/recover-shared-deleted-dashboard/change-history-diff.png)

1. Z menu Azure Portal wybierz pozycję **pulpity nawigacyjne**, a następnie wybierz pozycję **Przekaż**.

    ![Zrzut ekranu przedstawiający przekazywanie pulpitu nawigacyjnego](media/recover-shared-deleted-dashboard/dashboard-upload.png)

1. Wybierz zapisany plik JSON. Portal utworzy pulpit nawigacyjny z taką samą nazwą i elementami jak usunięty pulpit nawigacyjny.

1. Wybierz pozycję **Udostępnij** , aby opublikować pulpit nawigacyjny i ponownie nawiązać odpowiednią kontrolę dostępu.

    ![Zrzut ekranu przedstawiający udział pulpitu nawigacyjnego](media/recover-shared-deleted-dashboard/dashboard-share.png)
