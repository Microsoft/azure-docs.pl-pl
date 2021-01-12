---
title: Łączenie dzienników diagnostyki i inspekcji usługi Azure SQL Database z usługą Azure — wskaźnikiem
description: Dowiedz się, jak nawiązać połączenie dzienników diagnostycznych usługi Azure SQL Database i dzienników inspekcji zabezpieczeń z platformą Azure.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 01/06/2021
ms.author: yelevin
ms.openlocfilehash: df132c35ebb04596d91720431f5b08cb88e2abd9
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2021
ms.locfileid: "98104206"
---
# <a name="connect-azure-sql-database-diagnostics-and-auditing-logs"></a>Łączenie dzienników diagnostyki i inspekcji usługi Azure SQL Database

Azure SQL to w pełni zarządzany aparat bazy danych platformy jako usługi (PaaS), który obsługuje większość funkcji zarządzania bazami danych, takich jak uaktualnianie, stosowanie poprawek, tworzenie kopii zapasowych i monitorowanie, bez zaangażowania użytkowników. 

Łącznik usługi Azure SQL Database umożliwia strumieniowe przesyłanie dzienników inspekcji i diagnostyki baz danych do oprogramowania do produkcji wskaźnikowej, co pozwala na stałe monitorowanie aktywności we wszystkich wystąpieniach.

- Połączenie dzienników diagnostycznych umożliwia wysyłanie dzienników diagnostyki bazy danych o różnych typach danych do obszaru roboczego wskaźnikowego.

- Połączenie dzienników inspekcji umożliwia strumieniowe przesyłanie dzienników inspekcji zabezpieczeń ze wszystkich baz danych SQL Azure na poziomie serwera.

Dowiedz się więcej na temat [monitorowania baz danych Azure SQL](../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md)Database.

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć uprawnienia do odczytu i zapisu w obszarze roboczym wskaźnik platformy Azure.

- Aby nawiązać połączenie z dziennikami inspekcji, musisz mieć uprawnienia do odczytu i zapisu w ustawieniach inspekcji usługi Azure SQL Server.

## <a name="connect-to-azure-sql-database"></a>Łączenie z bazą danych Azure SQL Database
    
1. Z menu nawigacyjnego wskaźnikowego platformy Azure wybierz pozycję **Łączniki danych**.

1. Wybierz **Azure SQL Database** z galerii łączników danych, a następnie wybierz **Otwórz stronę łącznika**  w okienku podglądu.

1. W sekcji **Konfiguracja** na stronie łącznika Zanotuj dwie kategorie dzienników, z którymi można nawiązać połączenie.

### <a name="connect-diagnostics-logs"></a>Połącz dzienniki diagnostyki

1. W obszarze **dzienniki diagnostyczne** rozwiń **ręcznie Włącz dzienniki diagnostyczne dla każdej bazy danych Azure SQL**.

1. Wybierz łącze **otwórz >usługi Azure SQL** , aby otworzyć blok zasoby **usługi Azure SQL** .

1. **(Opcjonalnie)** Aby łatwo znaleźć zasób bazy danych, wybierz pozycję **Dodaj filtr** na pasku filtry u góry.
    1. Z listy rozwijanej **Filtr** wybierz **Typ zasobu**.
    1. Z listy rozwijanej **wartość** Usuń zaznaczenie opcji **Zaznacz wszystko**, a następnie wybierz pozycję **SQL Database**.
    1. Kliknij przycisk **Zastosuj**.
    
1. Wybierz zasób bazy danych, którego dzienniki diagnostyczne chcesz wysłać do badania wskaźnikowego platformy Azure.

    > [!NOTE]
    > Dla każdego zasobu bazy danych, którego dzienników chcesz zbierać, należy powtórzyć ten proces, rozpoczynając od tego kroku.

1. Na stronie zasób wybranej bazy danych w obszarze **monitorowanie** w menu nawigacji wybierz pozycję **Ustawienia diagnostyczne**.

    1. Wybierz łącze **+ Dodaj ustawienie diagnostyczne** w dolnej części tabeli.

    1. Na ekranie **Ustawienia diagnostyczne** wprowadź nazwę w polu  **Nazwa ustawienia diagnostycznego** .
    
    1. W kolumnie **szczegóły lokalizacji docelowej** zaznacz pole wyboru **Wyślij do log Analytics obszaru roboczego** . Zostaną wyświetlone dwa nowe pola poniżej. Wybierz odpowiednią **subskrypcję** i **log Analytics obszar roboczy** (gdzie znajduje się wskaźnik platformy Azure).

    1. W kolumnie **szczegóły kategorii** zaznacz pola wyboru dla dzienników i typów metryk, które chcesz pozyskać. Zalecamy wybranie wszystkich dostępnych typów w obszarze **Dziennik** i **Metryka**.

    1. Wybierz pozycję **Zapisz** w górnej części ekranu.

- Alternatywnie można użyć dostarczonego **skryptu programu PowerShell** do łączenia dzienników diagnostycznych.
    1. W obszarze **dzienniki diagnostyki** rozwiń węzeł **Włącz przez skrypt programu PowerShell**.

    1. Skopiuj blok kod i wklej go w programie PowerShell.

### <a name="connect-audit-logs"></a>Połącz dzienniki inspekcji

1. W obszarze **dzienniki inspekcji (wersja zapoznawcza)** rozwiń pozycję **Włącz dzienniki inspekcji we wszystkich bazach danych Azure SQL (na poziomie serwera)**.

1. Wybierz łącze **otwórz >usługi Azure SQL** , aby otworzyć blok zasobów **serwery SQL** .

1. Wybierz serwer SQL, którego dzienniki inspekcji chcesz wysłać do usługi Azure wskaźnikowej.

    > [!NOTE]
    > Dla każdego zasobu serwera, którego dzienniki mają być zbierane, należy powtórzyć ten proces, rozpoczynając od tego kroku.

1. Na stronie zasób wybranego serwera w obszarze **zabezpieczenia** w menu nawigacji wybierz pozycję **Inspekcja**.

    1. Przenieś ustawienie **Włącz inspekcję usługi Azure SQL** na wartość **włączone**.

    1. W obszarze **Lokalizacja docelowa dziennika inspekcji** wybierz pozycję **log Analytics (wersja zapoznawcza)**.
    
    1. Z wyświetlonej listy obszarów roboczych wybierz swój obszar roboczy (w którym znajduje się wskaźnik "Azure").

    1. Wybierz pozycję **Zapisz** w górnej części ekranu.

- Alternatywnie można użyć dostarczonego **skryptu programu PowerShell** do łączenia dzienników diagnostycznych.
    1. W obszarze **dzienniki inspekcji** rozwiń węzeł **Włącz przez skrypt programu PowerShell**.

    1. Skopiuj blok kod i wklej go w programie PowerShell.


> [!NOTE]
>
> Po tym konkretnym łączniku danych wskaźniki stanu łączności (pasek koloru w galerii łączników danych i ikony połączeń obok nazw typów danych) będą wyświetlane jako *połączone* (kolor zielony) tylko wtedy, gdy dane zostały pozyskane w pewnym momencie w ciągu ostatnich dwóch tygodni. Po upływie dwóch tygodni od pozyskania danych łącznik zostanie wyświetlony jako odłączony. Po przejściu do większej ilości danych zostanie wyświetlony stan *połączona* .

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób łączenia dzienników diagnostyki i inspekcji usługi Azure SQL Database z bazą danych Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).