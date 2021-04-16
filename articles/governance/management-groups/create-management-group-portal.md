---
title: 'Szybki start: tworzenie grupy zarządzania za pomocą portalu'
description: W tym przewodniku Szybki start utworzysz Azure Portal zarządzania w celu zorganizowania zasobów w hierarchię zasobów.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom:
- mode-portal
ms.openlocfilehash: 1b0f7b0d98b2cde1343325d5e4a2979e3e663b68
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535908"
---
# <a name="quickstart-create-a-management-group"></a>Szybki start: tworzenie grupy zarządzania

Grupy zarządzania to kontenery, które ułatwiają zarządzanie dostępem, zasadami i zgodnością w wielu subskrypcjach. Utwórz te kontenery, aby utworzyć efektywną i wydajną hierarchię, która może być używana z usługami [Azure Policy](../policy/overview.md) i kontrolą dostępu opartą [na rolach platformy Azure.](../../role-based-access-control/overview.md) Aby uzyskać więcej informacji na temat grup zarządzania, zobacz [Organize your resources with Azure management groups (Organizowanie zasobów przy użyciu grup zarządzania platformy Azure).](overview.md)

Ukończenie pierwszej grupy zarządzania utworzonej w katalogu może potrwać do 15 minut. Istnieją procesy uruchamiane po raz pierwszy w celu skonfigurowania usługi grup zarządzania na platformie Azure dla katalogu. Po zakończeniu procesu otrzymasz powiadomienie. Aby uzyskać więcej informacji, zobacz [początkową konfigurację grup zarządzania.](./overview.md#initial-setup-of-management-groups)

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

- Każdy użytkownik usługi Azure AD w dzierżawie może utworzyć grupę zarządzania bez przypisanego do niego uprawnienia do zapisu grupy zarządzania, jeśli ochrona hierarchii [nie](./how-to/protect-resource-hierarchy.md#setting---require-authorization) jest włączona. Ta nowa grupa zarządzania staje się elementem [](./how-to/protect-resource-hierarchy.md#setting---default-management-group) podrzędnym głównej grupy zarządzania lub domyślnej grupy zarządzania, a twórca ma przypisanie roli "Właściciel". Usługa grupy zarządzania umożliwia tę możliwość, dzięki czemu przypisania ról nie są wymagane na poziomie głównym. Żaden użytkownik nie ma dostępu do głównej grupy zarządzania po jej utworzeniu. Aby uniknąć przeszkód w znalezieniu administratorów globalnych usługi Azure AD w celu rozpoczęcia korzystania z grup zarządzania, zezwalamy na tworzenie początkowych grup zarządzania na poziomie głównym.

### <a name="create-in-portal"></a>Tworzenie w portalu

1. Zaloguj się do [Azure Portal](https://portal.azure.com).

1. Wybierz **pozycję Wszystkie usługi Zarządzanie** i  >  **ład.**

1. Wybierz **Grupy zarządzania**.

1. Wybierz **pozycję + Dodaj grupę zarządzania.**

   :::image type="content" source="./media/main.png" alt-text="Zrzut ekranu przedstawiający stronę Grupy zarządzania z podrzędnymi grupami zarządzania i subskrypcjami.":::

1. Pozostaw **zaznaczoną opcję** Utwórz nową i wypełnij pole Identyfikator grupy zarządzania.

   - Identyfikator **grupy zarządzania to** unikatowy identyfikator katalogu używany do przesyłania poleceń do tej grupy zarządzania. Tego identyfikatora nie można edytować po utworzeniu, ponieważ jest on używany w całym systemie platformy Azure do identyfikowania tej grupy. Główna [grupa zarządzania jest](./overview.md#root-management-group-for-each-directory) tworzona automatycznie z identyfikatorem, który jest identyfikatorem Azure Active Directory zarządzania. Dla wszystkich innych grup zarządzania przypisz unikatowy identyfikator.
   - Pole nazwy wyświetlanej to nazwa wyświetlana w Azure Portal. Oddzielna nazwa wyświetlana jest opcjonalnym polem podczas tworzenia grupy zarządzania i można ją zmienić w dowolnym momencie.

   :::image type="content" source="./media/create_context_menu.png" alt-text="Zrzut ekranu przedstawiający opcje &quot;Dodaj grupę zarządzania&quot; służące do tworzenia nowej grupy zarządzania.":::

1. Wybierz pozycję **Zapisz**.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć utworzoną grupę zarządzania, wykonaj następujące kroki:

1. Wybierz **pozycję Wszystkie usługi Zarządzanie** i  >  **ład.**

1. Wybierz **Grupy zarządzania**.

1. Znajdź utworzoną powyżej grupę zarządzania, wybierz ją, a następnie wybierz pozycję **Szczegóły** obok nazwy.
   Następnie wybierz **pozycję Usuń** i potwierdź monit.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono grupę zarządzania w celu zorganizowania hierarchii zasobów. Grupa zarządzania może zawierać subskrypcje lub inne grupy zarządzania.

Aby dowiedzieć się więcej na temat grup zarządzania i zarządzania hierarchią zasobów, przejdź do:

> [!div class="nextstepaction"]
> [Zarządzanie zasobami przy użyciu grup zarządzania](./manage.md)
