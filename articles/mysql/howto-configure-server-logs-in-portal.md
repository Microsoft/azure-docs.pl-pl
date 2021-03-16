---
title: Dostęp do dzienników wolnych zapytań — Azure Portal-Azure Database for MySQL
description: W tym artykule opisano sposób konfigurowania i uzyskiwania dostępu do powolnych dzienników w Azure Database for MySQL z Azure Portal.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: how-to
ms.date: 3/15/2021
ms.openlocfilehash: 91569780aa71861e07c7e96bec5eac879642760d
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103496222"
---
# <a name="configure-and-access-slow-query-logs-from-the-azure-portal"></a>Skonfiguruj i uzyskaj dostęp do dzienników wolnych zapytań z Azure Portal

Można skonfigurować, wyświetlić i pobrać [Azure Database for MySQL wolnych dzienników zapytań](concepts-server-logs.md) z Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne
Kroki opisane w tym artykule wymagają, aby [Azure Database for MySQL Server](quickstart-create-mysql-server-database-using-azure-portal.md).

## <a name="configure-logging"></a>Konfigurowanie rejestrowania
Skonfiguruj dostęp do dziennika wolnych zapytań programu MySQL. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Wybierz serwer Azure Database for MySQL.

3. W sekcji **monitorowanie** na pasku bocznym wybierz pozycję **Dzienniki serwera**. 
   :::image type="content" source="./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png" alt-text="Zrzut ekranu przedstawiający opcje dzienników serwera":::

4. Aby wyświetlić parametry serwera, wybierz **pozycję kliknij tutaj, aby włączyć dzienniki i skonfigurować parametry dziennika**.

5. Włącz  opcję slow_query_log **.**

6. Wybierz lokalizację, do której mają być wyprowadzane dzienniki, przy użyciu **log_output**. Aby wysłać dzienniki do magazynu lokalnego i Azure Monitor dzienników diagnostycznych, wybierz pozycję **plik**.

7. Rozważ ustawienie "long_query_time", który reprezentuje próg czasu zapytania dla zapytań, które będą zbierane w wolnym pliku dziennika zapytania, minimalna i domyślna wartość long_query_time są odpowiednio 0 i 10.

8. Dostosuj inne parametry, takie jak log_slow_admin_statements, aby rejestrować instrukcje administracyjne. Domyślnie instrukcje administracyjne nie są rejestrowane ani nie są zapytania, które nie używają indeksów do wyszukiwania. 

9. Wybierz pozycję **Zapisz**. 

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/3-save-discard.png" alt-text="Zrzut ekranu przedstawiający parametry dziennika wolnych zapytań i Zapisz.":::

Na stronie **parametry serwera** możesz powrócić do listy dzienników, zamykając stronę.

## <a name="view-list-and-download-logs"></a>Wyświetl listę i dzienniki pobierania
Po rozpoczęciu rejestrowania można wyświetlić listę dostępnych wolnych dzienników zapytań i pobrać pojedyncze pliki dziennika.

1. Otwórz witrynę Azure Portal.

2. Wybierz serwer Azure Database for MySQL.

3. W sekcji **monitorowanie** na pasku bocznym wybierz pozycję **Dzienniki serwera**. Na stronie zostanie wyświetlona lista plików dziennika.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/4-server-logs-list.png" alt-text="Zrzut ekranu strony dzienników serwera z wyróżnioną listą dzienników":::

   > [!TIP]
   > Konwencja nazewnictwa dziennika to **MySQL-wolno-< nazwę serwera>-yyyymmddhh. log**. Data i godzina użyta w nazwie pliku to czas, kiedy dziennik został wystawiony. Pliki dziennika są obracane co 24 godziny lub 7,5 GB, zależnie od tego, co nastąpi wcześniej. 

4. W razie potrzeby użyj pola wyszukiwania, aby szybko zawęzić do określonego dziennika na podstawie daty i godziny. Wyszukiwanie znajduje się na nazwie dziennika.

5. Aby pobrać pojedyncze pliki dziennika, wybierz ikonę strzałki w dół obok każdego pliku dziennika w wierszu tabeli.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/5-download.png" alt-text="Zrzut ekranu strony Dzienniki serwera z wyróżnioną ikoną Strzałka w dół":::

## <a name="set-up-diagnostic-logs"></a>Konfigurowanie dzienników diagnostycznych

1. W sekcji **monitorowanie** na pasku bocznym wybierz pozycję **Ustawienia diagnostyczne**  >  **Dodaj ustawienia diagnostyczne**.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/add-diagnostic-setting.png" alt-text="Zrzut ekranu przedstawiający opcje ustawień diagnostycznych":::

2. Podaj nazwę ustawienia diagnostycznego.

3. Określ, które ujścia danych mają wysyłać dzienniki wolnych zapytań (konto magazynu, centrum zdarzeń lub Log Analytics obszar roboczy).

4. W polu Typ dziennika wybierz pozycję **MySqlSlowLogs** .
:::image type="content" source="./media/howto-configure-server-logs-in-portal/configure-diagnostic-setting.png" alt-text="Zrzut ekranu przedstawiający opcje konfiguracji ustawień diagnostycznych":::

5. Po skonfigurowaniu ujścia danych w celu pomyślnego przetworzenia połączeń dzienników wolnych zapytań wybierz pozycję **Zapisz**.
:::image type="content" source="./media/howto-configure-server-logs-in-portal/save-diagnostic-setting.png" alt-text="Zrzut ekranu opcji konfiguracji ustawień diagnostycznych z wyróżnioną pozycją Zapisz":::

6. Uzyskaj dostęp do dzienników wolnych zapytań, badając je w skonfigurowanych ujściach danych. Wyświetlenie dzienników może potrwać do 10 minut.

## <a name="next-steps"></a>Następne kroki
- Zobacz [dostęp do dzienników wolnych zapytań w interfejsie wiersza polecenia](howto-configure-server-logs-in-cli.md) , aby dowiedzieć się, jak programowo pobrać dzienniki wolnych zapytań.
- Dowiedz się więcej o [wolnych dziennikach zapytań](concepts-server-logs.md) w Azure Database for MySQL.
- Aby uzyskać więcej informacji na temat definicji parametrów i rejestrowania MySQL, zobacz dokumentację programu MySQL w [dzienniku](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).