---
title: Nawiązywanie połączenia z Azure Database for MySQL przy użyciu programu dbForge Studio for MySQL
description: W tym artykule pokazano, jak nawiązać połączenie z Azure Database for MySQL Server za pośrednictwem programu dbForge Studio for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 942651aadf4113c1aca32e4e1d2c558b0d764421
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377229"
---
# <a name="connect-to-azure-database-for-mysql-using-dbforge-studio-for-mysql"></a>Nawiązywanie połączenia z Azure Database for MySQL przy użyciu programu dbForge Studio for MySQL

Aby nawiązać połączenie z Azure Database for MySQL [przy użyciu programu dbForge Studio for MySQL:](https://www.devart.com/dbforge/mysql/studio/)

1. W menu Baza danych wybierz pozycję Nowe połączenie.

2. Podaj nazwę hosta i poświadczenia logowania.

3. Wybierz przycisk Testuj połączenie, aby sprawdzić konfigurację.

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/azure-connection-1.png" alt-text="Połączenie platformy Azure":::

## <a name="migrate-a-database-using-the-backup-and-restore-functionality"></a>Migrowanie bazy danych przy użyciu funkcji tworzenia kopii zapasowych i przywracania

Program Studio umożliwia migrowanie baz danych na platformę Azure na wiele sposobów, a wybór zależy wyłącznie od Twoich potrzeb. Jeśli musisz przenieść całą bazę danych, najlepiej użyć funkcji tworzenia kopii zapasowych i przywracania. W tym przykładzie przeprowadzamy migrację *bazy danych sakili,* która znajduje się na serwerze MySQL, do Azure Database for MySQL. Logika procesu migracji korzystająca z funkcji tworzenia kopii zapasowych i przywracania w programie dbForge Studio for MySQL to utworzenie kopii zapasowej bazy danych MySQL, a następnie przywrócenie jej w Azure Database for MySQL.

### <a name="back-up-the-database"></a>Kopii zapasowej bazy danych

1. W menu Baza danych wskaż polecenie Kopia zapasowa i Przywracanie, a następnie wybierz pozycję Kopia zapasowa bazy danych. Zostanie wyświetlony Kreator tworzenia kopii zapasowej bazy danych.

2. Na karcie Kopia zapasowa zawartości Kreatora tworzenia kopii zapasowej bazy danych wybierz obiekty bazy danych, których kopię zapasową chcesz utworzyć.

3. Na karcie Opcje skonfiguruj proces tworzenia kopii zapasowej, aby dopasować go do swoich wymagań.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/back-up-wizard-options.png" alt-text="Opcje kreatora kopii zapasowej":::

4. Następnie określ zachowanie przetwarzania błędów i opcje rejestrowania.

5. Wybierz pozycję Kopia zapasowa.

### <a name="restore-the-database"></a>Przywracanie bazy danych

1. Połącz się z usługą Azure for Database for MySQL zgodnie z powyższym opisem.

2. Kliknij prawym przyciskiem myszy treść Eksplorator bazy danych, wskaż polecenie Kopii zapasowej i przywróć, a następnie wybierz pozycję Przywróć bazę danych.

3. W kreatorze przywracania bazy danych, który zostanie otwarty, wybierz plik z kopią zapasową bazy danych.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/restore-step-1.png" alt-text="Krok przywracania":::

4. Wybierz pozycję Przywróć.

5. Sprawdź wynik.

## <a name="migrate-a-database-using-the-copy-databases-functionality"></a>Migrowanie bazy danych przy użyciu funkcji kopiowania baz danych

Funkcja kopiowania baz danych jest podobna do funkcji Tworzenia kopii zapasowej i przywracania, z tą różnicą, że do przeprowadzenia migracji bazy danych nie są wymagane dwa kroki. Ponadto funkcja umożliwia transferowanie co najmniej dwóch baz danych w jednym przypadku. Funkcja kopiowania baz danych jest dostępna tylko w wersji Enterprise programu dbForge Studio for MySQL.
W tym przykładzie przeprowadzamy migrację *bazy world_x* z serwera MySQL do Azure Database for MySQL.
Aby przeprowadzić migrację bazy danych przy użyciu funkcji kopiowania baz danych:

1. W menu Baza danych wybierz pozycję Kopiuj bazy danych. 

2. Na wyświetlonej karcie Kopiowanie baz danych określ połączenie źródłowe i docelowe, a następnie wybierz bazy danych do zmigrowania. Wprowadzamy połączenie z usługą Azure MySQL i wybieramy world_x *bazy* danych. Wybierz zieloną strzałkę, aby zainicjować proces.

3. Sprawdź wynik.

W wyniku naszych wysiłków w zakresie migracji bazy danych baza *world_x* pomyślnie pojawiła się w usłudze Azure MySQL.

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/copy-databases-result.png" alt-text="Wynik kopiowania baz danych":::

## <a name="migrate-a-database-using-schema-and-data-compare-tools"></a>Migrowanie bazy danych przy użyciu narzędzi do porównywania schematów i danych

Program dbForge Studio for MySQL zawiera kilka narzędzi, które umożliwiają migrowanie baz danych MySQL, schematów MySQL i\lub danych na platformę Azure. Wybór funkcji zależy od potrzeb i wymagań projektu. Jeśli musisz selektywnie przenieść bazę danych, czyli zmigrować niektóre tabele MySQL na platformę Azure, najlepiej użyć funkcji porównywania schematów i danych.
W tym przykładzie przeprowadzamy migrację *bazy danych world,* która znajduje się na serwerze MySQL, do Azure Database for MySQL. Logika procesu migracji korzystająca z funkcji porównywania schematów i danych w programie dbForge Studio for MySQL to utworzenie pustej bazy danych w programie Azure Database for MySQL, zsynchronizowanie jej z wymaganą bazą danych MySQL, a następnie użycie narzędzia do porównywania schematów. W ten sposób schematy i dane bazy danych MySQL są dokładnie przenoszone na platformę Azure.

### <a name="step-1-connect-to-azure-database-for-mysql-and-create-an-empty-database"></a>Krok 1. Nawiązywanie połączenia Azure Database for MySQL i tworzenie pustej bazy danych

### <a name="step-2-schema-synchronization"></a>Krok 2. Synchronizacja schematów

1. W menu Porównanie wybierz pozycję Nowe porównanie schematów.
Zostanie wyświetlony Kreator porównywania nowego schematu.

2. Wybierz wartości Source (Źródło) i Target (Cel), a następnie określ opcje porównania schematu. Wybierz pozycję Porównaj.

3. W wyświetlonej siatce wyników porównania wybierz obiekty do synchronizacji. Wybierz przycisk z zieloną strzałką, aby otworzyć Kreatora synchronizacji schematów.

4. Kroki kreatora konfigurowania synchronizacji. Wybierz pozycję Synchronizuj, aby wdrożyć zmiany.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/schema-sync-wizard.png" alt-text="Kreator synchronizacji schematu":::

### <a name="step-3-data-comparison"></a>Krok 3. Porównanie danych

1. W menu Porównanie wybierz pozycję Nowe porównanie danych. Zostanie wyświetlony Kreator nowego porównania danych.

2. Wybierz wartości Source (Źródło) i Target (Cel), a następnie określ opcje porównania danych i w razie potrzeby zmień mapowania. Wybierz pozycję Porównaj.

3. W wyświetlonej siatce wyników porównania wybierz obiekty do synchronizacji. Wybierz przycisk z zieloną strzałką, aby otworzyć Kreatora synchronizacji danych.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-comp-result.png" alt-text="Wynik comp danych":::

4. Kroki kreatora konfigurowania synchronizacji. Wybierz pozycję Synchronizuj, aby wdrożyć zmiany.

5. Sprawdź wynik.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-sync-result.png" alt-text="Wynik synchronizacji danych":::

## <a name="summary"></a>Podsumowanie

Obecnie coraz więcej firm przenosi swoje bazy danych do Azure Database for MySQL, ponieważ ta usługa bazy danych jest łatwa do skonfigurowania i skalowania oraz zarządzania nimi. Taka migracja nie musi być łagodna. Narzędzia do migracji dbForge Studio for MySQL są nieskalane, co może znacząco ułatwić ten proces. Program Studio umożliwia łatwe konfigurowanie, zapisanie, edytowanie, automatyczne i zaplanowane przesyłanie bazy danych.

## <a name="next-steps"></a>Następne kroki
- [Omówienie usługi MySQL](overview.md)
