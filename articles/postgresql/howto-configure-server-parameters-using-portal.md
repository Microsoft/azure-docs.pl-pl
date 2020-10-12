---
title: Konfigurowanie parametrów serwera-Azure Portal-Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano sposób konfigurowania parametrów Postgres w Azure Database for PostgreSQL przez Azure Portal.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 02/28/2018
ms.openlocfilehash: e1b40e3116d56e87a2f397350ef2ba5510e04c0c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91707697"
---
# <a name="configure-server-parameters-in-azure-database-for-postgresql---single-server-via-the-azure-portal"></a>Konfigurowanie parametrów serwera w Azure Database for PostgreSQL-pojedynczym serwerze za pośrednictwem Azure Portal 
Można wyświetlić, wyświetlić i zaktualizować parametry konfiguracji dla Azure Database for PostgreSQL serwera za pomocą Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne
Aby krokowo uzyskać ten przewodnik:
- [Serwer Azure Database for PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="viewing-and-editing-parameters"></a>Wyświetlanie i edytowanie parametrów
1. Otwórz witrynę [Azure Portal](https://portal.azure.com).

2. Wybierz serwer usługi Azure Database for PostgreSQL.

3. W sekcji **Ustawienia** wybierz opcję **parametry serwera**. Na stronie zostanie wyświetlona lista parametrów, ich wartości i opisy.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png" alt-text="Strona przeglądu parametrów":::

4. Wybierz przycisk **listy rozwijanej** , aby wyświetlić możliwe wartości parametrów wyliczanych, takich jak client_min_messages.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png" alt-text="Strona przeglądu parametrów":::

5. Zaznacz lub umieść wskaźnik myszy nad przyciskiem **i** (informacjami), aby zobaczyć zakres możliwych wartości parametrów liczbowych, takich jak cpu_index_tuple_cost.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/4-information-button.png" alt-text="Strona przeglądu parametrów":::

6. W razie potrzeby użyj **pola wyszukiwania** , aby zawęzić do określonego parametru. Wyszukiwanie dotyczy nazwy i opisu parametrów.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/5-search.png" alt-text="Strona przeglądu parametrów":::

7. Zmień wartości parametrów, które chcesz dostosować. Wszystkie zmiany wprowadzone w sesji są wyróżnione kolorem purpurowym. Po zmianie wartości możesz wybrać pozycję **Zapisz**. Możesz też **odrzucić** zmiany.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png" alt-text="Strona przeglądu parametrów":::

8. Jeśli Zapisano nowe wartości parametrów, zawsze możesz przywrócić wszystkie elementy z powrotem do wartości domyślnych, wybierając pozycję **Zresetuj wszystkie do domyślnych**.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png" alt-text="Strona przeglądu parametrów":::

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej:
- [Przegląd parametrów serwera w Azure Database for PostgreSQL](concepts-servers.md)
- [Konfigurowanie parametrów przy użyciu interfejsu wiersza polecenia platformy Azure](howto-configure-server-parameters-using-cli.md)
