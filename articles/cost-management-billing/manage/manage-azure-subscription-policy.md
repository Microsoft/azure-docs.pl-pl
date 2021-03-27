---
title: Zarządzanie zasadami subskrypcji platformy Azure
description: Dowiedz się, jak zarządzać zasadami subskrypcji platformy Azure w celu kontrolowania przenoszenia subskrypcji platformy Azure z i do katalogów.
author: anuragdalmia
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/10/2021
ms.reviewer: banders
ms.author: andalmia
ms.openlocfilehash: 105e090655021ed4046f8880ef9816d7f7ff559f
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105631863"
---
# <a name="manage-azure-subscription-policies"></a>Zarządzanie zasadami subskrypcji platformy Azure

>[!NOTE]
>Ta funkcja jest obecnie dostępna w wersji zapoznawczej i jest stopniowo wdrażana, dlatego nie wszyscy użytkownicy mogą zobaczyć to środowisko jeszcze na Azure Portal.

Ten artykuł pomaga w konfigurowaniu zasad subskrypcji platformy Azure dla operacji subskrypcji w celu kontrolowania przenoszenia subskrypcji platformy Azure z i do katalogów.

## <a name="prerequisites"></a>Wymagania wstępne

- Tylko [administratorzy globalni](../../active-directory/roles/permissions-reference.md#global-administrator) katalogu mogą edytować zasady subskrypcji. Przed rozpoczęciem edytowania zasad subskrypcji Administrator globalny musi [podnieść dostęp do zarządzania wszystkimi subskrypcjami i grupami zarządzania platformy Azure](../../role-based-access-control/elevate-access-global-admin.md). Następnie mogą edytować zasady subskrypcji.
- Wszyscy inni użytkownicy mogą tylko odczytać bieżące ustawienie zasad.

## <a name="available-subscription-policy-settings"></a>Dostępne ustawienia zasad subskrypcji

Użyj następujących ustawień zasad, aby kontrolować przenoszenie subskrypcji platformy Azure z i do katalogów.

### <a name="subscriptions-leaving-aad-directory"></a>Subskrypcje opuszczające katalog usługi AAD

Zasady umożliwiają lub uniemożliwiają użytkownikom przeniesienie subskrypcji z bieżącego katalogu. [Właściciele subskrypcji](../../role-based-access-control/built-in-roles.md#owner) mogą [zmieniać katalog subskrypcji platformy Azure](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) na inną, w której są członkami. Stanowi ono problemy z zarządzaniem, dlatego administratorzy globalni mogą zezwalać na zmianę katalogu lub uniemożliwić użytkownikom usługi Active Directory.

### <a name="subscriptions-entering-aad-directory"></a>Subskrypcje wprowadzające katalog usługi AAD

Zasady umożliwiają lub uniemożliwiają użytkownikom z innych katalogów, którzy mają dostęp w bieżącym katalogu, do przenoszenia subskrypcji do bieżącego katalogu. [Właściciele subskrypcji](../../role-based-access-control/built-in-roles.md#owner) mogą [zmieniać katalog subskrypcji platformy Azure](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) na inną, w której są członkami. Stanowi ono problemy z zarządzaniem, dlatego administratorzy globalni mogą zezwalać na zmianę katalogu lub uniemożliwić użytkownikom usługi Active Directory.

### <a name="exempted-users"></a>Wykluczeni użytkownicy

Z przyczyn związanych z zarządzaniem Administratorzy globalni mogą blokować przenoszenie wszystkich katalogów subskrypcji do naszego z bieżącego katalogu. Jednak mogą chcieć zezwolić określonym użytkownikom na wykonywanie operacji. W każdej sytuacji można skonfigurować listę wykluczonych użytkowników, która umożliwia użytkownikom ominięcie ustawienia zasad, które ma zastosowanie do wszystkich innych.

## <a name="setting-subscription-policy"></a>Ustawianie zasad subskrypcji

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Przejdź do **subskrypcji**. **Zarządzanie zasadami** jest widoczne na pasku poleceń.  
    :::image type="content" source="./media/manage-azure-subscription-policy/subscription-blade-manage-policies.png" alt-text="Zrzut ekranu przedstawiający zasady zarządzania w subskrypcjach." lightbox="./media/manage-azure-subscription-policy/subscription-blade-manage-policies.png" :::
1. Wybierz pozycję **Zarządzaj zasadami** , aby wyświetlić szczegóły dotyczące bieżących zasad subskrypcji ustawionych dla katalogu. Administrator globalny z [podwyższonym poziomem uprawnień](../../role-based-access-control/elevate-access-global-admin.md) może wprowadzać zmiany w ustawieniach, w tym dodawanie lub usuwanie wykluczonych użytkowników.  
    :::image type="content" source="./media/manage-azure-subscription-policy/subscription-blade-policies.png" alt-text="Zrzut ekranu przedstawiający określone ustawienia zasad i wykluczonych użytkowników." lightbox="./media/manage-azure-subscription-policy/subscription-blade-policies.png" :::
1. Wybierz pozycję **Zapisz zmiany** u dołu, aby zapisać zmiany. Zmiany zaczynają obowiązywać natychmiast.

## <a name="read-subscription-policy"></a>Odczytaj zasady subskrypcji

Administratorzy niebędący administratorami globalnymi nadal mogą przejść do obszaru zasady subskrypcji, aby wyświetlić ustawienia zasad katalogu. Nie mogą wprowadzać żadnych zmian. Nie widzą listy wykluczonych użytkowników z przyczyn związanych z ochroną prywatności. Mogą oni wyświetlać administratorów globalnych do przesyłania żądań dotyczących zmian zasad, o ile ustawienia katalogu zezwalają na to.

:::image type="content" source="./media/manage-azure-subscription-policy/subscription-blade-policies-reader.png" alt-text="Zrzut ekranu przedstawiający Zarządzanie zasadami w subskrypcjach jako czytelnik." lightbox="./media/manage-azure-subscription-policy/subscription-blade-policies-reader.png" :::

## <a name="next-steps"></a>Następne kroki

- Przeczytaj [dokumentację dotyczącą Rozliczeń Cost Management i](../index.yml)