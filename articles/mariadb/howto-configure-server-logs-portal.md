---
title: Dostęp do dzienników wolnych zapytań — Azure Portal-Azure Database for MariaDB
description: W tym artykule opisano sposób konfigurowania i uzyskiwania dostępu do dzienników wolnych zapytań w Azure Database for MariaDB z Azure Portal.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 4/13/2020
ms.openlocfilehash: c5ee948daecafc061910f36d2ac95d15338bfb38
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98662512"
---
# <a name="configure-and-access-azure-database-for-maria-db-slow-query-logs-from-the-azure-portal"></a>Skonfiguruj i uzyskaj dostęp do dzienników wolnych zapytań usługi Azure Database for Maria DB z poziomu Azure Portal

Można skonfigurować, wyświetlić i pobrać [Azure Database for MariaDB wolnych dzienników zapytań](concepts-server-logs.md) z Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne
Kroki opisane w tym artykule wymagają, aby [Azure Database for MariaDB Server](quickstart-create-mariadb-server-database-using-azure-portal.md).

## <a name="configure-logging"></a>Konfigurowanie rejestrowania
Skonfiguruj dostęp do dziennika wolnych zapytań. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Wybierz serwer Azure Database for MariaDB.

3. W sekcji **monitorowanie** na pasku bocznym wybierz pozycję **Dzienniki serwera**. 
   ![Zrzut ekranu przedstawiający opcje dzienników serwera](./media/howto-configure-server-logs-portal/1-select-server-logs-configure.png)

4. Aby wyświetlić parametry serwera, wybierz **pozycję kliknij tutaj, aby włączyć dzienniki i skonfigurować parametry dziennika**.

5. Włącz  opcję slow_query_log **.**

6. Wybierz lokalizację, do której mają być wyprowadzane dzienniki, przy użyciu **log_output**. Aby wysłać dzienniki do magazynu lokalnego i Azure Monitor dzienników diagnostycznych, wybierz pozycję **plik**. 

7. Zmień inne potrzebne parametry. 

8. Wybierz pozycję **Zapisz**. 

   :::image type="content" source="./media/howto-configure-server-logs-portal/3-save-discard.png" alt-text="Zrzut ekranu przedstawiający parametry dziennika wolnych zapytań i Zapisz.":::

Na stronie **parametry serwera** możesz powrócić do listy dzienników, zamykając stronę.

## <a name="view-list-and-download-logs"></a>Wyświetl listę i dzienniki pobierania
Po rozpoczęciu rejestrowania można wyświetlić listę dostępnych wolnych dzienników zapytań i pobrać pojedyncze pliki dziennika. 

1. Otwórz witrynę Azure Portal.

2. Wybierz serwer Azure Database for MariaDB.

3. W sekcji **monitorowanie** na pasku bocznym wybierz pozycję **Dzienniki serwera**. Na stronie zostanie wyświetlona lista plików dziennika.

   ![Zrzut ekranu strony dzienników serwera z wyróżnioną listą dzienników](./media/howto-configure-server-logs-portal/4-server-logs-list.png)

   > [!TIP]
   > Konwencja nazewnictwa dziennika to **MySQL-wolno-< nazwę serwera>-yyyymmddhh. log**. Data i godzina użyta w nazwie pliku to czas, kiedy dziennik został wystawiony. Pliki dziennika są obracane co 24 godziny lub 7,5 GB, zależnie od tego, co nastąpi wcześniej.

4. W razie potrzeby użyj pola wyszukiwania, aby szybko zawęzić do określonego dziennika na podstawie daty i godziny. Wyszukiwanie znajduje się na nazwie dziennika.

5. Aby pobrać pojedyncze pliki dziennika, wybierz ikonę strzałki w dół obok każdego pliku dziennika w wierszu tabeli.

   ![Zrzut ekranu strony Dzienniki serwera z wyróżnioną ikoną Strzałka w dół](./media/howto-configure-server-logs-portal/5-download.png)

## <a name="set-up-diagnostic-logs"></a>Konfigurowanie dzienników diagnostycznych

1. W sekcji **monitorowanie** na pasku bocznym wybierz pozycję **Ustawienia diagnostyczne**  >  **Dodaj ustawienie diagnostyczne**.

   ![Zrzut ekranu przedstawiający opcje ustawień diagnostycznych](./media/howto-configure-server-logs-portal/add-diagnostic-setting.png)

1. Podaj nazwę ustawienia diagnostycznego.

1. Określ, które ujścia danych mają wysyłać dzienniki wolnych zapytań (konto magazynu, centrum zdarzeń lub Log Analytics obszar roboczy).

1. W polu Typ dziennika wybierz pozycję **MySqlSlowLogs** .
![Zrzut ekranu przedstawiający opcje konfiguracji ustawień diagnostycznych](./media/howto-configure-server-logs-portal/configure-diagnostic-setting.png)

1. Po skonfigurowaniu ujścia danych w celu pomyślnego przetworzenia połączeń dzienników wolnych zapytań wybierz pozycję **Zapisz**.
![Zrzut ekranu opcji konfiguracji ustawień diagnostycznych z wyróżnioną pozycją Zapisz](./media/howto-configure-server-logs-portal/save-diagnostic-setting.png)

1. Uzyskaj dostęp do dzienników wolnych zapytań, badając je w skonfigurowanych ujściach danych. Wyświetlenie dzienników może potrwać do 10 minut.

## <a name="next-steps"></a>Następne kroki
- Zobacz [dostęp do dzienników wolnych zapytań w interfejsie wiersza polecenia](howto-configure-server-logs-cli.md) , aby dowiedzieć się, jak programowo pobrać dzienniki wolnych zapytań.
- Dowiedz się więcej o [wolnych dziennikach zapytań](concepts-server-logs.md) w Azure Database for MariaDB.
- Aby uzyskać więcej informacji na temat definicji parametrów i rejestrowania, zapoznaj się z dokumentacją MariaDB dotyczącą [dzienników](https://mariadb.com/kb/en/library/slow-query-log-overview/).