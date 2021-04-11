---
title: Konfigurowanie parametrów serwera-Azure Portal-Azure Database for PostgreSQL-elastyczny serwer
description: W tym artykule opisano sposób konfigurowania parametrów Postgres w Azure Database for PostgreSQL elastycznym serwerze przy użyciu Azure Portal.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 29d85cc33dc55f3819bba6898a265c46b7e7ef85
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105607443"
---
# <a name="configure-server-parameters-in-azure-database-for-postgresql---flexible-server-via-the-azure-portal"></a>Konfigurowanie parametrów serwera w Azure Database for PostgreSQL-elastycznym serwerze za pośrednictwem Azure Portal 

Można wyświetlić, wyświetlić i zaktualizować parametry konfiguracji dla Azure Database for PostgreSQL serwera elastycznego za pomocą Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby krokowo uzyskać ten przewodnik:
- [Azure Database for PostgreSQL elastyczny serwer](quickstart-create-server-portal.md)

## <a name="viewing-and-editing-parameters"></a>Wyświetlanie i edytowanie parametrów

1. Otwórz witrynę [Azure Portal](https://portal.azure.com).

2. Wybierz swój elastyczny serwer.

3. W sekcji **Ustawienia** wybierz opcję **parametry serwera**. Na stronie zostanie wyświetlona lista parametrów, ich wartości i opisy.
![Strona przeglądu parametrów](./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png)

4. Wybierz przycisk **listy rozwijanej** , aby wyświetlić możliwe wartości parametrów wyliczanych, takich jak client_min_messages.
![Wyliczenie listy rozwijanej](./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png)

5. Zaznacz lub umieść wskaźnik myszy nad przyciskiem **i** (informacjami), aby zobaczyć zakres możliwych wartości parametrów liczbowych, takich jak cpu_index_tuple_cost.
![przycisk informacji](./media/howto-configure-server-parameters-in-portal/4-information-button.png)

6. W razie potrzeby użyj **pola wyszukiwania** , aby zawęzić do określonego parametru. Wyszukiwanie dotyczy nazwy i opisu parametrów.
![Wyniki wyszukiwania](./media/howto-configure-server-parameters-in-portal/5-search.png)

7. Zmień wartości parametrów, które chcesz dostosować. Wszystkie zmiany wprowadzone w sesji są wyróżnione kolorem purpurowym. Po zmianie wartości możesz wybrać pozycję **Zapisz**. Możesz też **odrzucić** zmiany.
![Zapisz lub Odrzuć zmiany](./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png)

8. Jeśli Zapisano nowe wartości parametrów, zawsze możesz przywrócić wszystkie elementy z powrotem do wartości domyślnych, wybierając pozycję **Zresetuj wszystkie do domyślnych**.
![Zresetuj wszystkie do domyślnych](./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej:

- [Przegląd parametrów serwera w Azure Database for PostgreSQL](concepts-servers.md)
- [Konfigurowanie parametrów przy użyciu interfejsu wiersza polecenia platformy Azure](howto-configure-server-parameters-using-cli.md)
