---
title: Włączanie funkcji obszaru roboczego Synapse
description: W tym dokumencie opisano sposób, w jaki użytkownik może włączyć funkcje obszaru roboczego Synapse w istniejącej dedykowanej puli SQL (dawniej SQL DW).
services: synapse-analytics
author: antvgski
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/25/2020
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 769ca4cf0ecbdba5fd80d977eb69c8a4f58df55f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98071191"
---
# <a name="enabling-synapse-workspace-features-for-a-dedicated-sql-pool-formerly-sql-dw"></a>Włączanie funkcji obszaru roboczego Synapse dla dedykowanej puli SQL (dawniej SQL DW)

Wszyscy użytkownicy programu SQL Data Warehouse mogą teraz uzyskiwać dostęp do istniejącej dedykowanej puli SQL (dawniej SQL DW) i korzystać z niej za pośrednictwem Synapse Studio i obszaru roboczego. Użytkownicy mogą korzystać z programu Synapse Studio i obszaru roboczego bez wpływu na automatyzację, połączenia lub narzędzia. W tym artykule wyjaśniono, jak istniejący użytkownik usługi Azure Synapse Analytics może włączyć funkcje obszaru roboczego Synapse dla istniejącej dedykowanej puli SQL (dawniej SQL DW). Użytkownik może rozszerzyć swoje istniejące rozwiązanie analityczne, korzystając z nowych możliwości bogatych w funkcje, które są teraz dostępne za pośrednictwem obszaru roboczego Synapse i Studio.   

## <a name="prerequisites"></a>Wymagania wstępne
Przed włączeniem funkcji obszaru roboczego Synapse w magazynie danych, należy upewnić się, że zostały opisane następujące czynności:
- Prawa do tworzenia zasobów SQL hostowanych na serwerze logicznym SQL i zarządzania nimi.
- Prawa do tworzenia zasobów usługi Azure Synapse.
- Administrator Azure Active Directory zidentyfikowany na serwerze logicznym

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="enabling-synapse-workspace-features-for-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>Włączanie funkcji obszaru roboczego Synapse dla istniejącej dedykowanej puli SQL (dawniej SQL DW)

Funkcje obszaru roboczego Synapse można włączyć dla dowolnej istniejącej dedykowanej puli SQL (dawniej SQL DW) w obsługiwanym regionie. Ta funkcja jest dostępna tylko za pośrednictwem Azure Portal.

Wykonaj następujące kroki, aby utworzyć obszar roboczy Synapse dla istniejącego magazynu danych.
1. Wybierz istniejącą dedykowaną pulę SQL (wcześniej SQL DW) i Otwórz stronę przegląd.
2. Wybierz pozycję **Nowy obszar roboczy Synapse** na górnym pasku menu lub komunikat bezpośrednio poniżej.
3. Po przejrzeniu listy magazynów danych, które są udostępniane za pośrednictwem nowego obszaru roboczego Synapse, na stronie **Utwórz nową usługę Azure Synapse Workspace** . Wybierz pozycję **Kontynuuj** , aby kontynuować.
4. Na stronie podstawy istniejąca dedykowana Pula SQL (szczegóły **projektu** powinna być wstępnie wypełniona z tą samą **subskrypcją** i **grupą zasobów** wdrożoną w ramach serwera logicznego). W obszarze **szczegóły obszaru roboczego** nazwa **obszaru roboczego** jest wstępnie wypełniona z tą samą nazwą i regionem serwera logicznego SQL, aby zapewnić, że połączenie między nowym obszarem roboczym usługi Synapse i serwerem logicznym można utworzyć podczas procesu aprowizacji. Aby zapewnić ciągły dostęp do dedykowanych wystąpień puli SQL (dawniej SQL DW) za pośrednictwem obszaru roboczego i Studio, należy zachować zainicjowanie obsługi administracyjnej.
5. Przejdź do opcji wybierz Data Lake Storage Gen 2.
6. Wybierz pozycję **Utwórz nową** lub wybierz istniejącą **Data Lake Storage Gen2** przed wybraniem **pozycji Dalej: sieć**.
7. Wybierz **hasło administratora SQL** dla **wystąpienia bezserwerowego**. Zmiana tego hasła nie aktualizuje ani nie zmienia poświadczeń serwera logicznego SQL. Jeśli wolisz hasła zdefiniowanego przez system, pozostaw to pole puste. To hasło można zmienić w dowolnym momencie w nowym obszarze roboczym. Nazwa administratora musi być taka sama, jak w SQL Server.
8. Wybierz preferowane **Ustawienia sieciowe** i wybierz pozycję **Przegląd + Utwórz** , aby rozpocząć Inicjowanie obsługi obszaru roboczego.
9. Wybierz pozycję **Przejdź do zasobu** , aby otworzyć nowy obszar roboczy.

    > [!NOTE]
    > Wszystkie wystąpienia dedykowanej puli SQL (dawniej SQL DW) hostowane na serwerze logicznym są dostępne za pośrednictwem nowego obszaru roboczego.

## <a name="post-provisioning-steps"></a>Kroki po aprowizacji
Poniższe kroki należy wykonać w celu upewnienia się, że do istniejącej dedykowanej puli SQL (dawniej SQL DW) można uzyskać dostęp za pośrednictwem programu Synapse Studio.
1. Na stronie Przegląd obszaru roboczego Synapse wybierz opcję **podłączony serwer**. **Połączony serwer** przenosi do połączonego serwera logicznego SQL, który hostuje magazyny danych. W podstawowym menu wybierz opcję **podłączony serwer**.
2. Otwórz **zaporę i sieci wirtualne** i upewnij się, że adres IP klienta lub wstępnie określony zakres adresów IP ma dostęp do serwera logicznego.
3. Otwórz **Active Directory administrator** i upewnij się, że na serwerze logicznym został ustawiony administrator usługi AAD.
4. Wybierz jedną z dedykowanych wystąpień puli SQL (dawniej SQL DW) hostowanej na serwerze logicznym. Na stronie Przegląd wybierz pozycję **Uruchom program Synapse Studio** lub przejdź do pozycji [Zaloguj się do programu Synapse Studio](https://web.azuresynapse.net) i zaloguj się w obszarze roboczym.

5. Otwórz **centrum danych** i rozwiń dedykowaną pulę SQL w Eksploratorze obiektów, aby upewnić się, że masz dostęp i można było wysyłać zapytania do magazynu danych.

    > [!NOTE] 
    > Połączony obszar roboczy można usunąć w dowolnym momencie. Usunięcie obszaru roboczego nie spowoduje usunięcia połączonej dedykowanej puli SQL (dawniej SQL DW). Funkcję obszaru roboczego można włączyć ponownie w dedykowanej puli SQL (wcześniej w programie SQL DW) po ukończeniu operacji usuwania.

## <a name="next-steps"></a>Następne kroki
Wprowadzenie do [obszaru roboczego Synapse i Studio](../get-started.md).
