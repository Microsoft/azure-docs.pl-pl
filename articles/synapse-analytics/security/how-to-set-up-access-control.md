---
title: Zabezpiecz obszar roboczy Synapse (wersja zapoznawcza)
description: W tym artykule nauczy Cię, jak używać ról i kontroli dostępu do kontrolowania działań i dostępu do danych w obszarze roboczym Synapse.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: jrasnick
ms.openlocfilehash: ae8be848b5d12e01865fe6bd3b394b460252aa3e
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606008"
---
# <a name="secure-your-synapse-workspace-preview"></a>Zabezpiecz obszar roboczy Synapse (wersja zapoznawcza)

W tym artykule nauczy Cię, jak używać ról i kontroli dostępu do kontrolowania działań i dostępu do danych. Postępując zgodnie z tymi instrukcjami, kontrola dostępu w usłudze Azure Synapse Analytics jest uproszczona. Wystarczy dodać i usunąć użytkowników do jednej z trzech grup zabezpieczeń.

## <a name="overview"></a>Omówienie

Aby zabezpieczyć obszar roboczy Synapse (podgląd), należy wykonać wzór konfigurowania następujących elementów:

- Role platformy Azure (takie jak wbudowane, takie jak właściciel, współautor itp.)
- Rola synapse — te role są unikatowe dla Synapse i nie są oparte na rolach platformy Azure. Istnieją trzy z tych ról:
  - Administrator obszaru roboczego Synapse
  - Administrator Synapse SQL
  - Administrator Synapse Spark
- Kontrola dostępu do danych w usłudze Azure Data Lake Storage Gen 2 (ADLSGEN2).
- Kontrola dostępu dla baz danych Synapse SQL i Spark

## <a name="steps-to-secure-a-synapse-workspace"></a>Kroki mające na celu zabezpieczenie obszaru roboczego Synapse

Ten dokument używa standardowych nazw, aby uprościć instrukcje. Wymień je na wybrane nazwy.

|Ustawienie | Przykładowa wartość | Opis |
| :------ | :-------------- | :---------- |
| **Obszar roboczy Synapse** | WS1 |  Nazwa, którą będzie miał obszar roboczy Synapse. |
| **Konto ADLSGEN2** | StG1 (własnach) | Konto ADLS do użycia w obszarze roboczym. |
| **Kontener** | Z o.o. | Kontener w STG1, który będzie używany domyślnie w obszarze roboczym. |
| **Dzierżawa usługi Active Directory** | contoso | nazwę dzierżawy usługi active directory.|
||||

## <a name="step-1-set-up-security-groups"></a>KROK 1: Konfigurowanie grup zabezpieczeń

Tworzenie i wypełnianie trzech grup zabezpieczeń dla obszaru roboczego:

- **WS1\_WSAdmins** – dla użytkowników, którzy potrzebują pełnej kontroli nad obszarem roboczym
- **WS1\_SparkAdmins** – dla tych użytkowników, którzy potrzebują pełnej kontroli nad aspektami Spark obszaru roboczego
- **WS1\_SQLAdmins** — dla użytkowników, którzy potrzebują pełnej kontroli nad aspektami SQL obszaru roboczego
- Dodawanie **ws1\_WSAdmins** do **WS1\_SQLAdmins**
- Dodaj **WS1\_WSAdmins** do **WS1\_SparkAdmins**

## <a name="step-2-prepare-your-data-lake-storage-gen2-account"></a>KROK 2: Przygotuj swoje konto Data Lake Storage Gen2

Identyfikowanie tych informacji o pamięci masowej:

- Konto ADLSGEN2 do użycia w obszarze roboczym. Ten dokument nazywa go STG1.  STG1 jest uważany za "podstawowe" konto magazynu dla obszaru roboczego.
- Kontener wewnątrz WS1, który będzie używany domyślnie w obszarze roboczym Synapse. Ten dokument nazywa go CNT1.  Ten kontener jest używany do:
  - Przechowywanie plików danych kopii dla tabel platformy Spark
  - Dzienniki wykonywania dla zadań Platformy Spark

- Korzystając z witryny Azure Portal, przypisz grupom zabezpieczeń następujące role w witrynie CNT1

  - Przypisywanie **ws1\_WSAdmins** do roli **współautora danych obiektów blob magazynu**
  - Przypisywanie **ws1\_sparkadmins** do roli **współautora danych obiektów blob magazynu**
  - Przypisywanie **dodatków SQLAdmins WS1\_** do roli **współautora danych obiektów blob magazynu**

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>KROK 3: Tworzenie i konfigurowanie obszaru roboczego Synapse

W witrynie Azure portal utwórz obszar roboczy Synapse:

- Nadawanie nazwy obszarowi roboczemu WS1
- Wybierz STG1 dla konta Magazyn
- Wybierz CNT1 dla kontenera, który jest używany jako "system plików".
- Otwórz WS1 w Synapse Studio
- Wybierz **pozycję Zarządzaj** > **kontrolą dostępu** przypisz grupy zabezpieczeń do następujących ról Synapse.
  - Przypisywanie **ws1\_WSAdmins** do administratorów synapse workspace
  - Przypisywanie **WS1\_SparkAdmins** do administratorów synapse spark
  - Przypisywanie **WS1\_SQLAdmins** do administratorów synapse SQL

## <a name="step-4-configuring-data-lake-storage-gen2-for-use-by-synapse-workspace"></a>KROK 4: Konfigurowanie pamięci masowej Data Lake Storage Gen2 do użytku przez obszar roboczy Synapse

Obszar roboczy Synapse wymaga dostępu do STG1 i CNT1, dzięki czemu może uruchamiać potoki i wykonywać zadania systemowe.

- Otwieranie portalu platformy Azure
- Znajdź STG1
- Przejdź do CNT1
- Upewnij się, że msi (tożsamość usługi zarządzanej) dla WS1 jest przypisany do roli **współautora danych obiektów Blob platformy Azure** na CNT1
  - Jeśli nie widzisz przypisanego, przypisz go.
  - MSI ma taką samą nazwę jak obszar roboczy. W tym przypadku byłoby &quot;WS1&quot;.

## <a name="step-5-configure-admin-access-for-sql-pools"></a>KROK 5: Konfigurowanie dostępu administratora dla pul SQL

- Otwieranie portalu platformy Azure
- Przejdź do pozycji WS1
- W obszarze **Ustawienia**kliknij pozycję **Administrator usługi ACTIVE Directory SQL**
- Kliknij **pozycję Ustaw administratora** i wybierz pozycję WS1\_SQLAdmins

## <a name="step-6-maintaining-access-control"></a>KROK 6: Utrzymanie kontroli dostępu

Konfiguracja została zakończona.

Teraz, aby zarządzać dostępem dla użytkowników, możesz dodawać i usuwać użytkowników do trzech grup zabezpieczeń.

Chociaż można ręcznie przypisać użytkowników do ról Synapse, jeśli to zrobisz, nie będzie konfigurować rzeczy spójnie. Zamiast tego należy tylko dodawać lub usuwać użytkowników do grup zabezpieczeń.

## <a name="step-7-verify-access-for-users-in-the-roles"></a>KROK 7: Weryfikowanie dostępu użytkowników w rolach

Użytkownicy w każdej roli muszą wykonać następujące kroki:

|   | Krok | Administratorzy obszaru roboczego | Administratorzy platformy Spark | Administratorzy SQL |
| --- | --- | --- | --- | --- |
| 1 | Przekazywanie pliku parkietu do CNT1 | TAK | TAK | TAK |
| 2 | Odczytanie pliku parkietu przy użyciu języka SQL na żądanie | TAK | NO | TAK |
| 3 | Tworzenie puli platformy Spark | TAK [1] | TAK [1] | NO  |
| 4 | Odczytuje plik parkietu za pomocą notesu | TAK | TAK | NO |
| 5 | Tworzenie potoku z notesu i wyzwalanie potoku do uruchomienia teraz | TAK | NO | NO |
| 6 | Tworzenie puli SQL i uruchamianie &quot;skryptu SQL, takiego jak SELECT 1&quot; | TAK [1] | NO | TAK[1] |

> [!NOTE]
> [1] Aby utworzyć pule SQL lub Spark, użytkownik musi mieć co najmniej rolę współautora w obszarze roboczym Synapse.
> [!TIP]
>
> - Niektóre kroki celowo nie będą dozwolone w zależności od roli.
> - Należy pamiętać, że niektóre zadania mogą zakończyć się niepowodzeniem, jeśli zabezpieczenia nie zostały w pełni skonfigurowane. Zadania te są odnotowane w tabeli.

## <a name="step-8-network-security"></a>KROK 8: Bezpieczeństwo sieci

Aby skonfigurować zaporę obszaru roboczego, sieć wirtualną i [łącze prywatne](../../sql-database/sql-database-private-endpoint-overview.md).

## <a name="step-9-completion"></a>KROK 9: Zakończenie

Obszar roboczy jest teraz w pełni skonfigurowany i zabezpieczony.

## <a name="how-roles-interact-with-synapse-studio"></a>Jak role wchodzą w interakcje z Synapse Studio

Synapse Studio będzie zachowywać się inaczej w zależności od ról użytkowników. Niektóre elementy mogą być ukryte lub wyłączone, jeśli użytkownik nie jest przypisany do ról, które dają odpowiedni dostęp. W poniższej tabeli podsumowano wpływ na Synapse Studio.

| Zadanie | Administratorzy obszaru roboczego | Administratorzy platformy Spark | Administratorzy SQL |
| --- | --- | --- | --- |
| Otwórz Synapse Studio | TAK | TAK | TAK |
| Zobacz centrum macierzyste | TAK | TAK | TAK |
| Wyświetl Centrum danych | TAK | TAK | TAK |
| Centrum danych / Zobacz połączone konta i kontenery ADLSGen2 | TAK [1] | TAK[1] | TAK[1] |
| Centrum danych / Zobacz bazy danych | TAK | TAK | TAK |
| Centrum danych / Wyświetlanie obiektów w bazach danych | TAK | TAK | TAK |
| Centrum danych / Dostęp do danych w bazach danych puli SQL | TAK   | NO   | TAK   |
| Centrum danych / Dostęp do danych w bazach danych SQL na żądanie | TAK [2]  | NO  | TAK [2]  |
| Centrum danych / Dostęp do danych w bazach danych Spark | TAK [2] | TAK [2] | TAK [2] |
| Korzystanie z centrum twórz | TAK | TAK | TAK |
| Tworzenie skryptów SQL w centrum / autorze | TAK | NO | TAK |
| Tworzenie definicji zadań spark w centrum / autorze | TAK | TAK | NO |
| Tworzenie notesów centrum / autora | TAK | TAK | NO |
| Tworzenie przepływów danych w centrum / autorach | TAK | NO | NO |
| Korzystanie z centrum Orchestrate | TAK | TAK | TAK |
| Centrum aranżacji / korzystanie z potoków | TAK | NO | NO |
| Korzystanie z Centrum zarządzania | TAK | TAK | TAK |
| Zarządzanie pulami koncentratora / SQL | TAK | NO | TAK |
| Zarządzanie pulami centrum / iskrą | TAK | TAK | NO |
| Zarządzanie koncentratorem / wyzwalaczami | TAK | NO | NO |
| Zarządzanie usługami Centrum / Połączone | TAK | TAK | TAK |
| Zarządzanie koncentratorem / kontrolą dostępu (przypisywanie użytkowników do ról obszaru roboczego Synapse) | TAK | NO | NO |
| Zarządzanie środowiskami uruchomieniowymi centrum / integracji | TAK | TAK | TAK |

> [!NOTE]
> [1] Dostęp do danych w kontenerach zależy od kontroli dostępu w tabelach OD SQL ADLSGen2 [2] SQL i tabelach Spark przechowują swoje dane w ADLSGen2, a dostęp wymaga odpowiednich uprawnień do ADLSGen2.

## <a name="next-steps"></a>Następne kroki

Tworzenie [obszaru roboczego Synapse](../quickstart-create-workspace.md)
