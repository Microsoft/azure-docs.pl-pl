---
title: Zarządzanie subskrypcjami
description: Subskrypcje składają się z zarządzanych urządzeń i mogą być dołączane lub offboarded w razie konieczności.
ms.date: 3/30/2021
ms.topic: how-to
ms.openlocfilehash: 10098ec66d15b4c894106cc1d37d7ac339d508cd
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387350"
---
# <a name="manage-a-subscription"></a>Zarządzanie subskrypcją

Subskrypcje są zarządzane co miesiąc. Po dołączeniu subskrypcji opłaty zostaną naliczone za subskrypcję do końca miesiąca. Podobnie, jeśli podczas odłączania subskrypcji zostanie naliczona kwota za resztę miesiąca, w którym offboarded tę subskrypcję.

## <a name="onboard-a-subscription"></a>Dołączanie subskrypcji

Aby rozpocząć pracę z usługą Azure Defender dla IoT, musisz mieć subskrypcję Microsoft Azure. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do bezpłatnego konta. Jeśli masz już dostęp do subskrypcji platformy Azure, ale nie ma jej na liście, sprawdź szczegóły konta i Potwierdź swoje uprawnienia za pomocą właściciela subskrypcji.

Aby dołączyć subskrypcję:

1. Przejdź do strony cennika Azure Portal. 

   :::image type="content" source="media/how-to-manage-subscriptions/no-subscription.png" alt-text="Przejdź do strony cennika Azure Portal.":::

1. Wybierz pozycję **subskrypcja** dołączania.

1. W oknie **subskrypcja** dołączania wybierz subskrypcję oraz liczbę zatwierdzonych urządzeń z menu rozwijanych. 

   :::image type="content" source="media/how-to-manage-subscriptions/onboard-subscription.png" alt-text="Wybierz subskrypcję i liczbę zatwierdzonych urządzeń.":::

1. Wybierz **pozycję** dołączanie.

## <a name="offboard-a-subscription"></a>Odłączania subskrypcję

Subskrypcje są zarządzane co miesiąc. Po odłączania subskrypcji zostanie naliczona stawka za subskrypcję do końca miesiąca.

Odinstaluj wszystkie czujniki skojarzone z subskrypcją przed odłączaniem subskrypcji. Aby uzyskać więcej informacji na temat usuwania czujnika, zobacz [usuwanie czujnika](how-to-manage-sensors-on-the-cloud.md#delete-a-sensor). 

Aby odłączania subskrypcję:

1. Przejdź do strony **Cennik** .
1. Wybierz subskrypcję, a następnie wybierz ikonę **Usuń** :::image type="icon" source="media/how-to-manage-sensors-on-the-cloud/delete-icon.png" border="false"::: .
1. W oknie podręcznym potwierdzenie zaznacz pole wyboru, aby potwierdzić, że usunięto wszystkie czujniki skojarzone z subskrypcją.

    :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/offboard-popup.png" alt-text="Zaznacz pole wyboru i wybierz pozycję odłączania, aby odłączania czujnik.":::

1. Wybierz przycisk **odłączania** . 

## <a name="next-steps"></a>Następne kroki

[Aktywowanie i konfigurowanie lokalnej konsoli zarządzania](how-to-activate-and-set-up-your-on-premises-management-console.md)
