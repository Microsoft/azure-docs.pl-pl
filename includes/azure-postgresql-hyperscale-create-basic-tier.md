---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: jonels-msft
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: include
ms.date: 04/07/2021
ms.author: jonels
ms.custom: include file
ms.openlocfilehash: 09eb6d9483268314febd9478a551595a059d973b
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107024078"
---
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).


Wykonaj następujące kroki, aby utworzyć serwer usługi Azure Database for PostgreSQL:
1. W lewym górnym rogu witryny Azure Portal kliknij polecenie **Utwórz zasób**.
2. Na stronie **Nowy** wybierz pozycję **Bazy danych**, a następnie na stronie **Bazy danych** wybierz pozycję **Azure Database for PostgreSQL**.
3. Dla opcji wdrożenie kliknij przycisk **Utwórz** w obszarze **Grupa serwerów Citus.**
4. Wypełnij formularz szczegółów nowego serwera, używając następujących informacji:
   - Grupa zasobów: kliknij link **Utwórz nowe** poniżej pola tekstowego dla tego pola. Wprowadź nazwę, taką jak moja **resourceName**.
   - Nazwa grupy serwerów: Wprowadź unikatową nazwę nowej grupy serwerów, która będzie również używana dla domeny podrzędnej serwera.
   - Aktywuj pole wyboru, **Włącz funkcje w wersji zapoznawczej**.
   - Nazwa użytkownika administratora: obecnie wymagana jest wartość **Citus** i nie można jej zmienić.
   - Hasło: musi mieć długość co najmniej ośmiu znaków i zawierać znaki z trzech z następujących kategorii: wielkie litery angielskie, małe litery angielskie, cyfry (0-9) i znaki inne niż alfanumeryczne (!, $, #,% itd.)
   - Lokalizacja: Użyj lokalizacji znajdującej się najbliżej Twoich użytkowników, aby zapewnić im najszybszy dostęp do danych.

   > [!IMPORTANT]
   > Hasło administratora serwera określone w tym miejscu jest wymagane do logowania się do serwera i jego baz danych. Zapamiętaj lub zapisz te informacje do wykorzystania w przyszłości.

5. Kliknij pozycję **Konfiguruj grupę serwerów**.
   - Wybierz **podstawowy** przycisk radiowy dla **warstwy**.
   - Kliknij pozycję **Zapisz**.
6. Kliknij przycisk **Dalej: sieć >** w dolnej części ekranu.

7. Na karcie **Sieć** kliknij przycisk radiowy **publiczny punkt końcowy** .
   ![Wybrano publiczny punkt końcowy](./media/azure-postgresql-hyperscale-create-db/network-public-endpoint.png)
8. Kliknij link **+ Dodaj bieżący adres IP klienta**.
   ![Dodano adres IP klienta](./media/azure-postgresql-hyperscale-create-db/network-add-client-ip.png)

   > [!NOTE]
   > Serwer Azure PostgreSQL komunikuje się przez port 5432. Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 5432 może być zablokowany przez zaporę sieciową. W takim przypadku nie można nawiązać połączenia z klastrem Citus, chyba że dział IT otworzy port 5432.
   >

9. Kliknij przycisk **Przegląd + Utwórz** , a następnie **Utwórz** , aby udostępnić serwer. Aprowizacja zajmuje kilka minut.
10. Strona zostanie przekierowana do monitorowania wdrożenia. Po zakończeniu wprowadzania zmian stanu na żywo z **wdrożenia** w toku **wdrożenia** kliknij element menu dane **wyjściowe** po lewej stronie.
11. Strona dane wyjściowe będzie zawierać nazwę hosta koordynatora z przyciskiem obok niego, aby skopiować wartość do Schowka. Zapisz te informacje do późniejszego użycia.

## <a name="connect-to-the-database-using-psql"></a>Łączenie się z bazą danych przy użyciu programu PSQL

Podczas tworzenia serwera Azure Database for PostgreSQL zostanie utworzona domyślna baza danych o nazwie **Citus** . Aby nawiązać połączenie z serwerem bazy danych, potrzebne są parametry połączenia i hasło administratora.

1. Uzyskaj parametry połączenia. Na stronie Grupa serwerów kliknij element menu **Parametry połączenia** . (W **ustawieniach**). Znajdź ciąg oznaczony jako **PSQL**. Będzie mieć postać:

   ```
   psql "host=hostname.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```

   Skopiuj ciąg. Należy zastąpić "{ \_ hasło}" wybranym wcześniej hasłem administracyjnym. System nie przechowuje hasła w postaci zwykłego tekstu i dlatego nie może wyświetlić go w parametrach połączenia.

2. Otwórz okno terminalu na komputerze lokalnym.

3. W wierszu polecenia Połącz się z serwerem Azure Database for PostgreSQL za pomocą narzędzia [PSQL](https://www.postgresql.org/docs/current/app-psql.html) . Przekaż parametry połączenia w cudzysłowie, aby upewnić się, że zawiera hasło:
   ```bash
   psql "host=..."
   ```

   Na przykład następujące polecenie nawiązuje połączenie z węzłem koordynatora grupy serwerów **mydemoserver**:

   ```bash
   psql "host=mydemoserver-c.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```
