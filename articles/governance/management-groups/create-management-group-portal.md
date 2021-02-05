---
title: 'Szybki Start: Tworzenie grupy zarządzania przy użyciu portalu'
description: W tym przewodniku szybki start utworzysz grupę zarządzania w celu zorganizowania zasobów w hierarchii zasobów przy użyciu Azure Portal.
ms.date: 02/05/2021
ms.topic: quickstart
ms.openlocfilehash: 5a336fe4caf5d2f825a93b5c7b8f8d05f6255c39
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592436"
---
# <a name="quickstart-create-a-management-group"></a>Szybki Start: Tworzenie grupy zarządzania

Grupy zarządzania to kontenery ułatwiające zarządzanie dostępem, zasadami i zgodnością w wielu subskrypcjach. Utwórz te kontenery, aby utworzyć efektywną i wydajną hierarchię, która może być używana z [Azure Policy](../policy/overview.md) i [kontroli dostępu opartej na rolach platformy Azure](../../role-based-access-control/overview.md). Aby uzyskać więcej informacji na temat grup zarządzania, zobacz [organizowanie zasobów przy użyciu grup zarządzania platformy Azure](overview.md).

Wykonanie pierwszej grupy zarządzania utworzonej w katalogu może potrwać do 15 minut. Istnieją procesy, które są uruchamiane po raz pierwszy w celu skonfigurowania usługi grup zarządzania na platformie Azure dla katalogu. Po zakończeniu procesu otrzymasz powiadomienie. Aby uzyskać więcej informacji, zobacz [początkowa konfiguracja grup zarządzania](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

- Każdy użytkownik usługi Azure AD w dzierżawie może utworzyć grupę zarządzania bez uprawnienia do zapisu grupy zarządzania przypisanej do tego użytkownika, jeśli [Ochrona hierarchii](./how-to/protect-resource-hierarchy.md#setting---require-authorization) nie jest włączona. Ta nowa grupa zarządzania jest elementem podrzędnym głównej grupy zarządzania lub [domyślną grupą zarządzania](./how-to/protect-resource-hierarchy.md#setting---default-management-group) , a twórca otrzymuje przypisanie roli "właściciel". Usługa Grupa zarządzania umożliwia taką możliwość, aby przypisania ról nie były potrzebne na poziomie głównym. Brak dostępu użytkowników do głównej grupy zarządzania podczas jej tworzenia. Aby uniknąć niestosowania progów wyszukiwania administratorów globalnych usługi Azure AD w celu rozpoczęcia korzystania z grup zarządzania, zezwalamy na tworzenie początkowych grup zarządzania na poziomie głównym.

### <a name="create-in-portal"></a>Utwórz w portalu

1. Zaloguj się do [Azure Portal](https://portal.azure.com).

1. Wybierz kolejno pozycje **wszystkie usługi**  >  **Zarządzanie i nadzór**.

1. Wybierz **grupy zarządzania**.

1. Wybierz pozycję **+ Dodaj grupę zarządzania**.

   :::image type="content" source="./media/main.png" alt-text="Zrzut ekranu przedstawiający stronę grupy zarządzania z podrzędnymi grupami zarządzania i subskrypcjami." border="false":::

1. Pozostaw opcję **Utwórz nowe** i wypełnij pole Identyfikator grupy zarządzania.

   - **Identyfikator grupy zarządzania** jest unikatowym identyfikatorem katalogu, który jest używany do przesyłania poleceń z tej grupy zarządzania. Ten identyfikator nie jest edytowalny po utworzeniu, ponieważ jest używany w całym systemie Azure do identyfikowania tej grupy. [Główna Grupa zarządzania](./overview.md#root-management-group-for-each-directory) jest automatycznie tworzona z identyfikatorem, który jest identyfikatorem Azure Active Directory. W przypadku wszystkich innych grup zarządzania przypisz unikatowy identyfikator.
   - Pole Nazwa wyświetlana to nazwa wyświetlana w Azure Portal. Oddzielna nazwa wyświetlana jest polem opcjonalnym podczas tworzenia grupy zarządzania i można ją zmienić w dowolnym momencie.

   :::image type="content" source="./media/create_context_menu.png" alt-text="Zrzut ekranu przedstawiający opcje &quot;Dodaj grupę zarządzania&quot; służącą do tworzenia nowej grupy zarządzania.":::

1. Wybierz pozycję **Zapisz**.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć utworzoną grupę zarządzania, wykonaj następujące kroki:

1. Wybierz kolejno pozycje **wszystkie usługi**  >  **Zarządzanie i nadzór**.

1. Wybierz **grupy zarządzania**.

1. Znajdź utworzoną powyżej grupę zarządzania, wybierz ją, a następnie wybierz **szczegóły** obok nazwy.
   Następnie wybierz pozycję **Usuń** i Potwierdź monit.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono grupę zarządzania w celu zorganizowania hierarchii zasobów. Grupa zarządzania może zawierać subskrypcje lub inne grupy zarządzania.

Aby dowiedzieć się więcej na temat grup zarządzania i zarządzania hierarchią zasobów, przejdź do:

> [!div class="nextstepaction"]
> [Zarządzanie zasobami za pomocą grup zarządzania](./manage.md)