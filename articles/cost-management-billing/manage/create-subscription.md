---
title: Tworzenie dodatkowej subskrypcji platformy Azure
description: Dowiedz się, jak dodać nową subskrypcję platformy Azure w witrynie Azure Portal. Zapoznaj się z informacjami na temat formularzy kont rozliczeniowych i wyświetl dodatkowe dostępne zasoby.
author: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 27594ff4d7673038e9de38c5969a615be60aeadf
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844697"
---
# <a name="create-an-additional-azure-subscription"></a>Tworzenie dodatkowej subskrypcji platformy Azure

Możesz utworzyć dodatkową subskrypcję dla konta rozliczeniowego [umowy Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), [umowy klienta firmy Microsoft](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) lub [umowy partnerskiej firmy Microsoft](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement) w witrynie Azure Portal. Dzięki dodatkowej subskrypcji możesz na przykład unikać przekraczania limitów subskrypcji, tworzyć oddzielne środowiska na potrzeby zabezpieczeń lub izolować dane ze względu na zapewnienie zgodności.

Jeśli masz konto rozliczeniowe Microsoft Online Service Program (MOSP), możesz tworzyć dodatkowe subskrypcje [na stronie rejestracji w witrynie Azure Portal](https://account.azure.com/signup?offer=ms-azr-0003p).

Aby dowiedzieć się więcej na temat kont rozliczeniowych i identyfikowania typu konta rozliczeniowego, zobacz [Wyświetlanie kont rozliczeniowych w witrynie Azure Portal](view-all-accounts.md).

## <a name="permission-required-to-create-azure-subscriptions"></a>Uprawnienia wymagane do tworzenia subskrypcji platformy Azure

Do tworzenia subskrypcji wymagane są następujące uprawnienia:

|Konto billingowe  |Uprawnienie  |
|---------|---------|
|Umowa Enterprise Agreement (EA) |  Rola właściciela konta rejestracji umowy Enterprise Aby uzyskać więcej informacji, zobacz [Omówienie ról administracyjnych dla umowy Azure Enterprise Agreement na platformie Azure](understand-ea-roles.md).    |
|Umowa klienta firmy Microsoft (MCA) |  Rola właściciela lub współautora w sekcji faktury, profilu rozliczeń lub koncie rozliczeniowym Rola twórcy subskrypcji platformy Azure w sekcji faktury  Aby uzyskać więcej informacji, zobacz [Role rozliczeniowe i zadania w subskrypcji](understand-mca-roles.md#subscription-billing-roles-and-tasks).    |
|Umowa partnerska firmy Microsoft |   Rola administratora globalnego i agenta administratora w organizacji partnera CSP. Aby dowiedzieć się więcej, zobacz [Centrum partnerskie — Przypisywanie ról i uprawnień użytkowników](/partner-center/permissions-overview).  Użytkownik musi zalogować się do dzierżawy partnera w celu utworzenia subskrypcji platformy Azure.   |

## <a name="create-a-subscription-in-the-azure-portal"></a>Tworzenie subskrypcji w witrynie Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyszukaj pozycję **Subskrypcje**.

   ![Zrzut ekranu przedstawiający wyszukiwanie subskrypcji w portalu](./media/create-subscription/billing-search-subscription-portal.png)

1. Wybierz pozycję **Dodaj**.

   ![Zrzut ekranu przedstawiający przycisk Dodaj w widoku Subskrypcje](./media/create-subscription/subscription-add.png)

1. Jeśli masz dostęp do wielu kont rozliczeń, wybierz konto rozliczeniowe, dla którego chcesz utworzyć subskrypcję.

1. Wypełnij formularz i kliknij przycisk **Utwórz**. W poniższych tabelach przedstawiono pola formularza dla poszczególnych typów kont rozliczeniowych.

**Umowa Enterprise Agreement**

|Pole  |Definicja  |
|---------|---------|
|Nazwa     | Nazwa wyświetlana ułatwia znajdowanie subskrypcji w witrynie Azure Portal.  |
|Oferta     | Wybierz ofertę EA Tworzenie i testowanie, jeśli zamierzasz używać tej subskrypcji do obciążeń programistycznych lub testowych. W przeciwnym razie wybierz ofertę Microsoft Azure Enterprise. Oferta Tworzenie i testowanie musi być włączona dla konta rejestracji, aby można było tworzyć subskrypcje w ramach umowy EA Tworzenie i testowanie.|

**Umowa klienta firmy Microsoft**

|Pole  |Definicja  |
|---------|---------|
|Profil rozliczeniowy     | Opłaty za subskrypcję będą naliczane w wybranym profilu rozliczeniowym. Jeśli masz dostęp tylko do jednego profilu rozliczeniowego, opcje wyboru będą wyszarzone.     |
|Sekcja faktury     | Opłaty za subskrypcję będą wyświetlane w tej sekcji faktury w profilu rozliczeniowym. Jeśli masz dostęp tylko do jednej sekcji faktury, opcje wyboru będą wyszarzone.  |
|Planowanie     | Wybierz plan platformy Microsoft Azure na potrzeby tworzenia i testowania, jeśli zamierzasz używać tej subskrypcji do obciążeń programistycznych lub testowych. W przeciwnym razie wybierz plan platformy Microsoft Azure. Jeśli dla profilu rozliczeniowego włączono tylko jeden plan, opcje wyboru będą wyszarzone.  |
|Nazwa     | Nazwa wyświetlana ułatwia znajdowanie subskrypcji w witrynie Azure Portal.  |

**Umowa partnerska firmy Microsoft**

|Pole  |Definicja  |
|---------|---------|
|Klient    | Subskrypcja zostanie utworzona dla wybranego klienta. Jeśli masz tylko jednego klienta, opcje do wyboru będą wyszarzone.  |
|Odsprzedawca    | Odsprzedawca, który będzie świadczyć usługi klientom. To jest pole opcjonalne, które ma zastosowanie tylko do dostawców pośrednich w dwuwarstwowym modelu usług CSP. |
|Nazwa     | Nazwa wyświetlana ułatwia znajdowanie subskrypcji w witrynie Azure Portal.  |

## <a name="create-an-additional-azure-subscription-programmatically"></a>Programowe tworzenie dodatkowej subskrypcji platformy Azure

Dodatkowe subskrypcje można również tworzyć programowo. Aby uzyskać więcej informacji, zobacz:

- [Programowe tworzenie subskrypcji umowy EA za pomocą najnowszego interfejsu API](programmatically-create-subscription-enterprise-agreement.md)
- [Programowe tworzenie subskrypcji Umowy z Klientem Microsoft za pomocą najnowszego interfejsu API](programmatically-create-subscription-microsoft-customer-agreement.md)
- [Programowe tworzenie subskrypcji umowy MPA za pomocą najnowszego interfejsu API](Programmatically-create-subscription-microsoft-customer-agreement.md)

## <a name="next-steps"></a>Następne kroki

- [Dodawanie lub zmienianie administratorów subskrypcji platformy Azure](add-change-subscription-administrator.md)
- [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Tworzenie grup zarządzania na potrzeby organizowania zasobów i zarządzania nimi](../../governance/management-groups/create-management-group-portal.md)
- [Anulowanie subskrypcji platformy Azure](cancel-azure-subscription.md)

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).