---
title: Nawiązywanie połączenia z usługą Azure Database for MySQL przy użyciu programu dbfałszerstw Studio for MySQL
description: W tym artykule przedstawiono sposób nawiązywania połączenia z serwerem Azure Database for MySQL za pośrednictwem programu dbfałszerstw Studio for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: d5d694820c2ffd09868d81693d4f98f839a139d7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104591881"
---
# <a name="connect-to-azure-database-for-mysql-using-dbforge-studio-for-mysql"></a>Nawiązywanie połączenia z usługą Azure Database for MySQL przy użyciu programu dbfałszerstw Studio for MySQL

Aby nawiązać połączenie z usługą Azure Database for MySQL przy użyciu programu [Dbfałszerstw Studio for MySQL](https://www.devart.com/dbforge/mysql/studio/):

1. W menu Baza danych wybierz pozycję nowe połączenie.

2. Podaj nazwę hosta i poświadczenia logowania.

3. Wybierz przycisk Testuj połączenie, aby sprawdzić konfigurację.

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/azure-connection-1.png" alt-text="Połączenie platformy Azure":::

## <a name="migrate-a-database-using-the-backup-and-restore-functionality"></a>Migrowanie bazy danych przy użyciu funkcji tworzenia kopii zapasowych i przywracania

Program Studio umożliwia migrowanie baz danych na platformę Azure na wiele sposobów, ale wybór zależy wyłącznie od Twoich potrzeb. W przypadku konieczności przeniesienia całej bazy danych najlepiej użyć funkcji tworzenia kopii zapasowych i przywracania. W tym przykładzie przeprowadzimy migrację bazy danych *sakila* , która znajduje się na serwerze MySQL, aby Azure Database for MySQL. Logika w tle procesu migracji przy użyciu funkcji tworzenia kopii zapasowych i przywracania w programie MySQL w programie webfałszerstwe to tworzenie kopii zapasowej bazy danych MySQL, a następnie przywracanie jej w Azure Database for MySQL.

### <a name="back-up-the-database"></a>Tworzenie kopii zapasowej bazy danych

1. W menu Baza danych wskaż polecenie Utwórz kopię zapasową i Przywróć, a następnie wybierz pozycję Kopia zapasowa bazy danych. Zostanie wyświetlony Kreator tworzenia kopii zapasowej bazy danych.

2. Na karcie zawartość kopii zapasowej Kreatora tworzenia kopii zapasowej bazy danych wybierz obiekty bazy danych, dla których chcesz utworzyć kopię zapasową.

3. Na karcie Opcje Skonfiguruj proces tworzenia kopii zapasowej zgodnie z wymaganiami.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/back-up-wizard-options.png" alt-text="Opcje kreatora kopii zapasowej":::

4. Następnie określ błędy przetwarzania i opcje rejestrowania.

5. Wybierz pozycję Kopia zapasowa.

### <a name="restore-the-database"></a>Przywracanie bazy danych

1. Połącz się z platformą Azure dla bazy danych MySQL zgodnie z powyższym opisem.

2. Kliknij prawym przyciskiem myszy treść Eksplorator bazy danych, wskaż polecenie Utwórz kopię zapasową i Przywróć, a następnie wybierz polecenie Przywróć bazę danych.

3. W otwartym Kreatorze przywracania bazy danych wybierz plik z kopią zapasową bazy danych.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/restore-step-1.png" alt-text="Krok przywracania":::

4. Wybierz pozycję Przywróć.

5. Sprawdź wynik.

## <a name="migrate-a-database-using-the-copy-databases-functionality"></a>Migrowanie bazy danych przy użyciu funkcji kopiowania baz danych

Funkcje kopiowania baz danych są podobne do kopii zapasowych i przywracania, z tą różnicą, że nie wymagają dwóch kroków, aby przeprowadzić migrację bazy danych. Co więcej, funkcja umożliwia przesyłanie co najmniej dwóch baz danych. Funkcje kopiowania baz danych są dostępne tylko w wersji Enterprise programu dbfałszerstw Studio for MySQL.
W tym przykładzie przeprowadzimy migrację bazy danych *world_x* z serwera MySQL do Azure Database for MySQL.
Aby przeprowadzić migrację bazy danych przy użyciu funkcji kopiowania baz danych:

1. W menu Baza danych wybierz opcję Kopiuj bazy danych. 

2. Na wyświetlonej karcie Kopiuj bazy danych określ połączenie źródłowe i docelowe oraz wybierz bazy danych, które mają zostać zmigrowane. Wprowadzimy połączenie Azure MySQL i wybierzesz bazę danych *world_x* . Wybierz zieloną strzałkę, aby zainicjować proces.

3. Sprawdź wynik.

W wyniku naszych działań związanych z migracją bazy danych pomyślnie pojawiła się baza danych *world_x* w usłudze Azure MySQL.

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/copy-databases-result.png" alt-text="Wynik kopiowania baz danych":::

## <a name="migrate-a-database-using-schema-and-data-compare-tools"></a>Migrowanie bazy danych przy użyciu narzędzi do porównywania schematów i danych

Program dbfałszerstw Studio for MySQL obejmuje kilka narzędzi, które umożliwiają Migrowanie baz danych MySQL, schematy MySQL and\or dane na platformę Azure. Wybór funkcjonalności zależy od potrzeb i wymagań projektu. Jeśli musisz wybiórczo przenieść bazę danych, czyli zmigrować pewne tabele MySQL na platformę Azure, najlepiej użyć funkcji porównywania schematu i danych.
W tym przykładzie Migrujemy *światową* bazę danych, która znajduje się na serwerze MySQL, aby Azure Database for MySQL. Logika w ramach procesu migracji przy użyciu funkcji porównywania schematów i danych w programie dbfałszerstw Studio for MySQL polega na utworzeniu pustej bazy danych w Azure Database for MySQL, zsynchronizuj ją z wymaganą bazą danych MySQL, najpierw używając narzędzia do porównywania schematów, a następnie używając narzędzia do porównywania danych. Dzięki temu schematy i dane programu MySQL są prawidłowo przenoszone na platformę Azure.

### <a name="connect-to-azure-database-for-mysql-and-create-an-empty-database"></a>Nawiązywanie połączenia z Azure Database for MySQL i tworzenie pustej bazy danych

Połącz się z Azure Database for MySQL i Utwórz pustą bazę danych.

### <a name="step-2-schema-synchronization"></a>Krok 2. Synchronizacja schematu

1. W menu porównanie wybierz pozycję nowe porównanie schematów.
Zostanie wyświetlony Kreator nowego porównania schematów.

2. Wybierz źródło i cel, a następnie określ opcje porównywania schematu. Wybierz pozycję Porównaj.

3. W wyświetlonej siatce wyników porównania wybierz pozycję obiekty do synchronizacji. Wybierz przycisk Zielona strzałka, aby otworzyć Kreatora synchronizacji schematów.

4. Zapoznaj się z instrukcjami Kreatora konfigurowania synchronizacji. Wybierz pozycję Synchronizuj, aby wdrożyć zmiany.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/schema-sync-wizard.png" alt-text="Kreator synchronizacji schematów":::

### <a name="data-comparison"></a>Porównanie danych

1. W menu porównanie wybierz pozycję nowe porównanie danych. Zostanie wyświetlony Kreator nowych danych porównawczych.

2. Wybierz źródło i cel, a następnie określ opcje porównania danych i Zmień mapowania, jeśli to konieczne. Wybierz pozycję Porównaj.

3. W wyświetlonej siatce wyników porównania wybierz pozycję obiekty do synchronizacji. Wybierz przycisk Zielona strzałka, aby otworzyć Kreatora synchronizacji danych.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-comp-result.png" alt-text="Wynik realizacji danych":::

4. Zapoznaj się z instrukcjami Kreatora konfigurowania synchronizacji. Wybierz pozycję Synchronizuj, aby wdrożyć zmiany.

5. Sprawdź wynik.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-sync-result.png" alt-text="Wynik synchronizacji danych":::

## <a name="summary"></a>Podsumowanie

Obecnie więcej firm Przenieś swoje bazy danych do Azure Database for MySQL, ponieważ usługa bazy danych jest łatwa do skonfigurowania, zarządzania i skalowania. Migracja nie musi być bolesnym. Narzędzia migracji dbfałszerstw Studio for MySQL boasts Immaculate, które mogą znacznie ułatwić proces. Program Studio umożliwia łatwe konfigurowanie, zapisywanie, edytowanie, automatyczne i planowanie transferu bazy danych.

## <a name="next-steps"></a>Następne kroki
- [Baza danych MySQL — Omówienie](overview.md)
