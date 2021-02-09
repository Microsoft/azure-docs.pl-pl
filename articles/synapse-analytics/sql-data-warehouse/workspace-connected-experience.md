---
title: Włączanie funkcji obszaru roboczego Synapse w dedykowanej puli SQL (dawniej SQL DW)
description: W tym dokumencie opisano sposób, w jaki klient może uzyskać dostęp do istniejącego wystąpienia autonomicznego programu SQL DW w obszarze roboczym i korzystać z niego.
services: synapse-analytics
author: antvgski
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/23/2020
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: d477693667c8d78687d27b291d2b3c15612a0f30
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2021
ms.locfileid: "99989048"
---
# <a name="enabling-synapse-workspace-features-on-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>Włączanie funkcji obszaru roboczego Synapse w istniejącej dedykowanej puli SQL (dawniej SQL DW)

Wszyscy klienci usługi SQL Data Warehouse mogą teraz uzyskiwać dostęp do istniejącej dedykowanej puli SQL (dawniej SQL DW) i korzystać z niej za pośrednictwem programu Synapse Studio i obszaru roboczego bez wpływu na automatyzację, połączenia lub narzędzia. W tym artykule wyjaśniono, jak istniejący klient usługi Azure Synapse Analytics może tworzyć i rozwijać istniejące rozwiązania analityczne, wykorzystując nowe funkcje, które są teraz dostępne za pośrednictwem obszaru roboczego Synapse i Studio.   

## <a name="experience"></a>Środowisko użytkownika
 
Teraz, gdy obszar roboczy usługi Synapse jest w stanie GA, Nowa funkcja jest dostępna w sekcji Omówienie portalu magazynu danych, która umożliwia tworzenie obszaru roboczego Synapse dla istniejącej dedykowanej puli SQL (dawniej SQL DW). Ta nowa funkcja umożliwi połączenie serwera logicznego, który hostuje istniejące wystąpienia magazynu danych z nowym obszarem roboczym Synapse. Połączenie zapewnia, że wszystkie magazyny danych hostowane na tym serwerze są dostępne z obszaru roboczego i Studio i mogą być używane w połączeniu z usługami partnerskimi Synapse (bezserwerowa Pula SQL, Pula Apache Spark i ADF). Możesz rozpocząć uzyskiwanie dostępu do zasobów i korzystanie z nich zaraz po zakończeniu czynności inicjowania obsługi administracyjnej, a połączenie zostało nawiązane z nowo utworzonym obszarem roboczym.  
:::image type="content" source="media/workspace-connected-overview/workspace-connected-dw-portal-overview-pre-create.png" alt-text="Połączony obszar roboczy Synapse":::

## <a name="using-synapse-workspace-and-studio-features-to-access-and-use-a-dedicated-sql-pool-formerly-sql-dw"></a>Używanie funkcji Synapse Workspace i Studio do uzyskiwania dostępu do dedykowanej puli SQL (dawniej SQL DW) i korzystania z niej
 
Poniższe informacje będą stosowane w przypadku korzystania z dedykowanego magazynu danych SQL (dawniej SQL DW) z włączonymi funkcjami obszaru roboczego Synapse: 
- **Możliwości SQL** Wszystkie funkcje SQL będą pozostawać w logicznym programie SQL Server po włączeniu funkcji obszaru roboczego Synapse. Dostęp do serwera za pośrednictwem dostawcy zasobów SQL będzie nadal możliwy po włączeniu obszaru roboczego. Wszystkie funkcje zarządzania mogą być inicjowane za pośrednictwem obszaru roboczego, a operacja zostanie wykonana na SQL Server logicznym hostującym pule SQL. Żadna istniejąca Automatyzacja, narzędzia lub połączenia nie będą przerywane ani przerywane, gdy obszar roboczy jest włączony.  
- **Przenoszenie zasobów**  Zainicjowanie przenoszenia zasobu na serwerze z włączoną funkcją obszaru roboczego Synapse spowoduje przerwanie połączenia między serwerem i obszarem roboczym i uniemożliwi dostęp do istniejącej dedykowanej puli SQL (dawniej usługi SQL DW) z obszaru roboczego. Aby upewnić się, że połączenie jest zachowane, zaleca się, aby oba zasoby pozostawały w ramach tej samej subskrypcji i grupy zasobów. 
- **Monitorowanie** Żądania SQL przesłane za pośrednictwem programu Synapse Studio w ramach dedykowanej puli SQL z włączonym obszarem roboczym (dawniej SQL DW) można wyświetlić w centrum monitora. W przypadku wszystkich innych działań monitorowania można przejść do Azure Portal dedykowanej puli SQL (dawniej programu SQL DW). 
- Kontrola **zabezpieczeń** i **dostępu** , jak wspomniano powyżej, wszystkie funkcje zarządzania dla programu SQL Server i dedykowane pule SQL (dawniej SQL DW) będą nadal znajdować się na logicznym serwerze SQL Server. Te funkcje obejmują zarządzanie regułami zapory, Ustawianie administratora usługi Azure AD na serwerze i wszelką kontrolę dostępu dla danych w dedykowanej puli SQL (dawniej SQL DW). Należy wykonać następujące kroki, aby upewnić się, że dedykowana Pula SQL (wcześniej SQL DW) jest dostępna i może być używana za pośrednictwem obszaru roboczego Synapse. Członkostwa ról obszaru roboczego nie dają użytkownikom uprawnień do danych w dedykowanej puli SQL (dawniej: DW). Postępuj zgodnie z normalnymi zasadami [uwierzytelniania SQL](sql-data-warehouse-authentication.md) , aby zapewnić użytkownikom dostęp do dedykowanych wystąpień puli SQL (dawniej SQL DW) na serwerze logicznym. Jeśli dedykowana Pula SQL (dawniej SQL DW) ma już przypisaną tożsamość zarządzaną, nazwa tej tożsamości zarządzanej będzie taka sama jak tożsamość zarządzana w obszarze roboczym, która jest automatycznie tworzona do obsługi usług partnerskich obszaru roboczego (np. potoków ADF).  W połączonym scenariuszu mogą istnieć dwie zarządzane tożsamości o tej samej nazwie. Tożsamość zarządzana może być rozróżniana przez identyfikatory obiektów usługi Azure AD, dzięki czemu funkcja tworzenia użytkowników SQL korzystających z identyfikatorów obiektów jest dostępna wkrótce.

    ```sql
    CREATE USER [<workspace managed identity] FROM EXTERNAL PROVIDER 
    GRANT CONTROL ON DATABASE:: <dedicated SQL pool name> TO [<workspace managed identity>
    ```

    > [!NOTE] 
    > W połączonym obszarze roboczym Synapse Studio zostaną wyświetlone nazwy dedykowanych pul na podstawie uprawnień użytkownika na platformie Azure. Obiekty w ramach pul nie będą dostępne, jeśli użytkownik nie ma uprawnień do pul SQL. 

- **Zabezpieczenia sieci** Jeśli obszar roboczy Synapse włączony w istniejącej dedykowanej puli SQL (dawniej SQL DW) jest włączony do ochrony przed filtrowaniem danych. Utwórz zarządzane połączenie prywatnego punktu końcowego z obszaru roboczego do serwera logicznego SQL. Zatwierdź prywatne żądanie połączenia z punktem końcowym, aby umożliwić komunikację między serwerem i obszarem roboczym.
- **Studio** Pule SQL w **centrum danych** z obsługą dedykowanej puli SQL (dawniej SQL DW) można zidentyfikować za pomocą etykietki narzędzia w centrum danych. 
- **Tworzenie nowej dedykowanej puli SQL (dawniej SQL DW)** Nowe dedykowane pule SQL można utworzyć za pośrednictwem obszaru roboczego Synapse i Studio po włączeniu funkcji obszaru roboczego i udostępnieniu nowej puli na logicznym serwerze SQL Server. Nowe zasoby zostaną wyświetlone w portalu i Studio po zakończeniu aprowizacji.      

## <a name="next-steps"></a>Następne kroki
Włącz [funkcje obszaru roboczego Synapse](workspace-connected-create.md) w istniejącej dedykowanej puli SQL (dawniej SQL DW)
