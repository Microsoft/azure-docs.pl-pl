---
title: Zabezpieczanie obszaru roboczego usługi Synapse
description: W tym artykule opisano sposób korzystania z ról i kontroli dostępu w celu kontrolowania działań i uzyskiwania dostępu do danych w obszarze roboczym Synapse.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: jrasnick
ms.openlocfilehash: 25e191af919c5880045a6c4c7c79b675cf02520e
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96458711"
---
# <a name="secure-your-synapse-workspace"></a>Zabezpieczanie obszaru roboczego usługi Synapse 

W tym artykule opisano sposób korzystania z ról i kontroli dostępu w celu kontrolowania działań i uzyskiwania dostępu do danych. Zgodnie z tymi instrukcjami kontrola dostępu w usłudze Azure Synapse Analytics jest uproszczona. Wystarczy dodać i usunąć użytkowników z jednej z trzech grup zabezpieczeń.

## <a name="overview"></a>Omówienie

Aby zabezpieczyć obszar roboczy Synapse, należy postępować zgodnie ze wzorcem konfigurowania następujących elementów:

- Role platformy Azure (takie jak wbudowane, takie jak właściciel, współautor itp.)
- Role Synapse — te role są unikatowe dla Synapse i nie są oparte na rolach platformy Azure. Istnieją trzy z tych ról:
  - Administrator obszaru roboczego Synapse
  - Synapse administratora SQL
  - Apache Spark dla administratora usługi Azure Synapse Analytics
- Kontrola dostępu do danych w Azure Data Lake Storage Gen 2 (ADLSGEN2).
- Kontrola dostępu do Synapse baz danych SQL i Spark

## <a name="steps-to-secure-a-synapse-workspace"></a>Procedura zabezpieczania obszaru roboczego Synapse

Ten dokument używa standardowych nazw do uproszczenia instrukcji. Zastąp je wszystkimi wybranymi nazwami.

|Ustawienie | Przykładowa wartość | Opis |
| :------ | :-------------- | :---------- |
| **Obszar roboczy Synapse** | WS1 |  Nazwa, która będzie miała obszar roboczy Synapse. |
| **Konto ADLSGEN2** | STG1 | Konto ADLS, które ma być używane z Twoim obszarem roboczym. |
| **Kontener** | CNT1 | Kontener w STG1, który będzie używany domyślnie przez obszar roboczy. |
| **Dzierżawa usługi Active Directory** | contoso | Nazwa dzierżawy usługi Active Directory.|
||||

## <a name="step-1-set-up-security-groups"></a>Krok 1. Konfigurowanie grup zabezpieczeń

Tworzenie i wypełnianie trzech grup zabezpieczeń dla obszaru roboczego:

- **WS1 \_ WSAdmins** — dla użytkowników, którzy potrzebują pełnej kontroli nad obszarem roboczym
- **WS1 \_ SparkAdmins** — dla tych użytkowników, którzy potrzebują pełnej kontroli nad aspektami Spark obszaru roboczego
- **WS1 \_ Sqladmins** — dla użytkowników, którzy potrzebują pełnej kontroli nad aspektami programu SQL obszaru roboczego

## <a name="step-2-prepare-your-data-lake-storage-gen2-account"></a>Krok 2. Przygotowywanie konta Data Lake Storage Gen2

Zidentyfikuj te informacje o magazynie:

- Konto ADLSGEN2 do użycia w Twoim obszarze roboczym. Ten dokument wywołuje STG1 IT.  STG1 jest uznawana za "podstawowe" konto magazynu dla Twojego obszaru roboczego.
- Kontener w WS1, który będzie używany przez obszar roboczy Synapse domyślnie. Ten dokument wywołuje CNT1 IT.  Ten kontener jest używany na potrzeby:
  - Przechowywanie plików danych kopii zapasowej dla tabel platformy Spark
  - Dzienniki wykonywania dla zadań platformy Spark

- Przy użyciu Azure Portal Przypisz grupy zabezpieczeń następujące role w CNT1

  - Przypisywanie **WS1 \_ WSAdmins** do roli **współautor danych obiektów blob magazynu**
  - Przypisywanie **WS1 \_ SparkAdmins** do roli **współautor danych obiektów blob magazynu**
  - Przypisywanie **WS1 \_ sqladmins** do roli **współautor danych obiektów blob magazynu**

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>Krok 3. Tworzenie i Konfigurowanie obszaru roboczego Synapse

 W Azure Portal Utwórz obszar roboczy Synapse:

- Wybierz swoją subskrypcję
- Wybierz grupę zasobów — musisz mieć dostęp do grupy zasobów, do której przypisano rolę **właściciela** .
- Nazwij obszar roboczy WS1
- Wybierz pozycję STG1 dla konta magazynu —. Wybierz pozycję CNT1 dla kontenera, który jest używany jako "system plików".
- Otwórz WS1 w programie Synapse Studio
- Wybierz pozycję **Zarządzaj**  >  **Access Control** Przypisz grupy zabezpieczeń do następujących ról Synapse.
  - Przypisywanie **WS1 \_ WSAdmins** do administratorów obszaru roboczego Synapse
  - Przypisywanie **WS1 \_ SparkAdmins** do administratorów platformy Synapse Spark
  - Przypisywanie **WS1 \_ sqladmins** do Synapse Administratorzy SQL

## <a name="step-4-configure-data-lake-storage-gen2-for-use-by-synapse-workspace"></a>Krok 4. Konfigurowanie Data Lake Storage Gen2 do użycia przez obszar roboczy Synapse

Obszar roboczy Synapse wymaga dostępu do STG1 i CNT1, aby można było uruchamiać potoki i wykonywać zadania systemowe.

- Otwórz witrynę Azure Portal
- Znajdź STG1
- Przejdź do CNT1
- Upewnij się, że plik MSI (tożsamość usługi zarządzanej) dla WS1 jest przypisany do roli **współautor danych obiektów blob magazynu** w witrynie CNT1
  - Jeśli nie widzisz jej przypisanej, przypisz ją.
  - Plik MSI ma taką samą nazwę jak obszar roboczy. W takim przypadku &quot; WS1 &quot; .

## <a name="step-5-configure-admin-access-for-synapse-sql"></a>Krok 5. Konfigurowanie dostępu administratora dla usługi Synapse SQL

- Otwórz witrynę Azure Portal
- Przejdź do WS1
- W obszarze **Ustawienia** wybierz pozycję **SQL Active Directory administrator**
- Wybierz pozycję **Ustaw administratora** i wybierz pozycję WS1 \_ sqladmins

## <a name="step-6-maintain-access-control"></a>Krok 6. Obsługa kontroli dostępu

Konfiguracja została zakończona.

Teraz, aby zarządzać dostępem użytkowników, można dodawać i usuwać użytkowników z trzech grup zabezpieczeń.

Mimo że można ręcznie przypisać użytkowników do ról Synapse, jeśli tak się stanie, nie będzie on spójnie skonfigurowany. Zamiast tego należy dodawać i usuwać tylko użytkowników z grup zabezpieczeń.

## <a name="step-7-verify-access-for-users-in-the-roles"></a>Krok 7. Weryfikowanie dostępu dla użytkowników w rolach

Użytkownicy w każdej roli muszą wykonać następujące czynności:

| Liczba | Krok | Administratorzy obszaru roboczego | Administratorzy platformy Spark | Administratorzy SQL |
| --- | --- | --- | --- | --- |
| 1 | Przekaż plik Parquet do CNT1 | TAK | TAK | TAK |
| 2 | Odczytaj plik Parquet za pomocą puli SQL bezserwerowej | TAK | NO | TAK |
| 3 | Utwórz bezserwerową pulę Apache Spark | TAK [1] | TAK [1] | NO  |
| 4 | Odczytuje plik Parquet z notesem | TAK | TAK | NO |
| 5 | Tworzenie potoku z poziomu notesu i wyzwalanie potoku w celu uruchomienia go teraz | TAK | NO | NO |
| 6 | Utwórz dedykowaną pulę SQL i uruchom skrypt SQL, taki jak &quot; SELECT 1&quot; | TAK [1] | NO | TAK [1] |

> [!NOTE]
> [1] aby utworzyć pule SQL lub Spark, użytkownik musi mieć co najmniej rolę współautor w obszarze roboczym Synapse.
>
 
>[!TIP]
> - Niektóre kroki zostaną celowo zabronione w zależności od roli.
> - Należy pamiętać, że niektóre zadania mogą się nie powieść, jeśli zabezpieczenia nie zostały w pełni skonfigurowane. Te zadania są wymienione w tabeli.

## <a name="step-8-network-security"></a>Krok 8. zabezpieczenia sieci

W celu skonfigurowania zapory obszaru roboczego, sieci wirtualnej i [linku prywatnego](../../azure-sql/database/private-endpoint-overview.md).

## <a name="step-9-completion"></a>Krok 9. uzupełnianie

Obszar roboczy jest teraz w pełni skonfigurowany i zabezpieczony.

## <a name="how-roles-interact-with-synapse-studio"></a>Jak role współdziałają z programem Synapse Studio

Program Synapse Studio będzie zachowywać się inaczej w zależności od ról użytkownika. Niektóre elementy mogą być ukryte lub wyłączone, jeśli użytkownik nie jest przypisany do ról, które zapewniają odpowiedni dostęp. Poniższa tabela zawiera podsumowanie wpływu programu Synapse Studio.

| Zadanie | Administratorzy obszaru roboczego | Administratorzy platformy Spark | Administratorzy SQL |
| --- | --- | --- | --- |
| Otwórz Synapse Studio | TAK | TAK | TAK |
| Wyświetlanie centrum macierzystego | TAK | TAK | TAK |
| Wyświetlanie centrum danych | TAK | TAK | TAK |
| Centrum danych/zobacz połączone ADLS Gen2 konta i kontenery | TAK [1] | TAK [1] | TAK [1] |
| Centrum danych/Wyświetlanie baz danych | TAK | TAK | TAK |
| Centrum danych/wyświetlanie obiektów w bazach danych | TAK | TAK | TAK |
| Centra danych/dostęp do danych w Synapse bazach danych SQL | TAK   | NO   | TAK   |
| Centra danych/dostęp do danych w bezserwerowych bazach danych puli SQL | TAK [2]  | NO  | TAK [2]  |
| Centra danych/dostęp do danych w bazach danych Spark | TAK [2] | TAK [2] | TAK [2] |
| Korzystanie z centrum opracowywania | TAK | TAK | TAK |
| Opracowywanie skryptów SQL Hub/autora | TAK | NO | TAK |
| Opracowywanie definicji zadań centrum/autora platformy Spark | TAK | TAK | NO |
| Opracowywanie notesów centrum/autora | TAK | TAK | NO |
| Opracowywanie centrów/autora przepływu | TAK | NO | NO |
| Korzystanie z centrum aranżacji | TAK | TAK | TAK |
| Organizuj centra/Użyj potoków | TAK | NO | NO |
| Korzystanie z centrum zarządzania | TAK | TAK | TAK |
| Zarządzanie usługą Hub/Synapse SQL | TAK | NO | TAK |
| Zarządzanie pulami Hub/Spark | TAK | TAK | NO |
| Zarządzaj centrum/wyzwalaczami | TAK | NO | NO |
| Zarządzanie centrami/połączonymi usługami | TAK | TAK | TAK |
| Zarządzanie centrami/Access Control (przypisywanie użytkowników do ról obszaru roboczego Synapse) | TAK | NO | NO |
| Zarządzanie centrami/środowiskami Integration Runtime | TAK | TAK | TAK |
| Korzystanie z centrum monitora | TAK | TAK | TAK |
| Monitoruj uruchomienia centrów/aranżacji/potoku  | TAK | NO | NO |
| Monitorowanie uruchomień centrum/aranżacji/wyzwalacza  | TAK | NO | NO |
| Monitorowanie centrów/aranżacji/środowisk Integration Runtime  | TAK | TAK | TAK |
| Monitoruj centra/działania/aplikacje platformy Spark | TAK | TAK | NO  |
| Monitoruj centra/działania/żądania SQL | TAK | NO | TAK |
| Monitoruj centrum/działania/pule platformy Spark | TAK | TAK | NO  |
| Monitoruj centrum/wyzwalacze | TAK | NO | NO |
| Zarządzanie centrami/połączonymi usługami | TAK | TAK | TAK |
| Zarządzanie centrami/Access Control (przypisywanie użytkowników do ról obszaru roboczego Synapse) | TAK | NO | NO |
| Zarządzanie centrami/środowiskami Integration Runtime | TAK | TAK | TAK |


> [!NOTE]
> [1] dostęp do danych w kontenerach zależy od kontroli dostępu w ADLS Gen2. </br>
> [2] tabele SQL OD i tabele platformy Spark przechowują swoje dane w ADLS Gen2 i dostęp wymaga odpowiednich uprawnień do ADLS Gen2.

## <a name="next-steps"></a>Następne kroki

Tworzenie [obszaru roboczego Synapse](../quickstart-create-workspace.md)
