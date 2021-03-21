---
title: Role kontroli dostępu opartej na rolach w usłudze Synapse
description: W tym artykule opisano wbudowane role RBAC Synapse
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 35f66732fa9cb48b94f80bab203534c9d04b7a7b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100102125"
---
# <a name="synapse-rbac-roles"></a>Role RBAC Synapse

W tym artykule opisano wbudowane role RBAC Synapse, przyznane im uprawnienia oraz zakresy, w których mogą być używane.  

## <a name="whats-changed-since-the-preview"></a>Co się zmieniło od wersji zapoznawczej?
W przypadku użytkowników znających role Synapse RBAC udostępniane w ramach wersji zapoznawczej obowiązują następujące zmiany:
- Nazwa administratora obszaru roboczego została zmieniona na **Synapse administrator**
- Administrator Apache Spark nazwa została zmieniona na **Synapse Apache Spark administrator** i ma uprawnienia do wyświetlania wszystkich opublikowanych artefaktów kodu, w tym skryptów SQL.  Ta rola nie daje już uprawnień do korzystania z pliku MSI obszaru roboczego, który wymaga roli użytkownika Credential Synapse.  To uprawnienie jest wymagane do uruchamiania potoków. 
- Nazwa administratora SQL została zmieniona na **Synapse administrator SQL** i ma uprawnienia do wyświetlania wszystkich opublikowanych artefaktów kodu, w tym notesów i zadań platformy Spark.  Ta rola nie daje już uprawnień do korzystania z pliku MSI obszaru roboczego, który wymaga roli użytkownika Credential Synapse. To uprawnienie jest wymagane do uruchamiania potoków.
- Wprowadzono **nowe precyzyjne Synapse role RBAC** , które koncentrują się na wspieraniu projektowania i operacji osób, a nie na określonych środowiskach analizy.  
- **Nowe zakresy niższego poziomu** są wprowadzane dla kilku ról.  Te zakresy umożliwiają ograniczenie ról do określonych zasobów lub obiektów.

>[!Note]
>**Nowe role RBAC Synapse i zakresy niższego poziomu są obecnie dostępne w wersji zapoznawczej**.  Zachęcamy do korzystania z tych nowych ról i zakresów, które są w pełni obsługiwane i do przesyłania opinii na temat ich użycia.

## <a name="built-in-synapse-rbac-roles-and-scopes"></a>Wbudowane role i zakresy Synapse RBAC

W poniższej tabeli opisano wbudowane role i zakresy, w których mogą być używane.

>[!Note]
> Użytkownicy z dowolną rolą Synapse RBAC w dowolnym zakresie automatycznie mają rolę użytkownika Synapse w zakresie obszaru roboczego. 

|Rola |Uprawnienia|Zakresy|
|---|---|-----|
|Synapse administrator  |Pełny Synapse dostęp do pul SQL bezserwerowych, pul Apache Spark i środowiska Integration Runtime.  Obejmuje uprawnienia do tworzenia, odczytu, aktualizowania i usuwania wszystkich opublikowanych artefaktów kodu.  Zawiera operator obliczeń, połączone Data Manager i uprawnienia użytkownika poświadczeń w ramach poświadczeń tożsamości systemu obszaru roboczego.  Obejmuje Przypisanie ról RBAC Synapse. Oprócz administratora Synapse właściciele platformy Azure mogą również przypisywać role RBAC Synapse. Uprawnienia platformy Azure są wymagane do tworzenia i usuwania zasobów obliczeniowych oraz zarządzania nimi. </br></br>_Może odczytywać i zapisywać artefakty </br> mogą wykonywać wszystkie działania w działaniach platformy Spark. </br> Może wyświetlać dzienniki puli platformy Spark </br> mogą wyświetlać zapisane Notesy i dane wyjściowe potoku </br> mogą korzystać z wpisów tajnych przechowywanych przez połączone usługi lub poświadczenia </br> mogą łączyć się z niektórymi punktami końcowymi programu SQL Server przy użyciu programu SQL `db_datareader` , `db_datawriter` , `connect` i uprawnienia do `grant` </br> przypisywania i odwoływania ról RBAC Synapse w bieżącym zakresie_|Workspace </br> Pula platformy Spark<br/>Integration Runtime </br>Połączona usługa</br>Poświadczenie |
|Synapse Apache Spark administrator</br>|Pełny dostęp Synapse do pul Apache Spark.  Twórz, Odczytuj, Aktualizuj i usuwaj dostęp do opublikowanych definicji zadań platformy Spark, notesów i ich wyjść oraz do bibliotek, połączonych usług i poświadczeń.  Obejmuje dostęp do odczytu do wszystkich innych opublikowanych artefaktów kodu. Nie obejmuje uprawnień do używania poświadczeń i uruchamiania potoków. Nie obejmuje udzielania dostępu. </br></br>_Może wykonywać wszystkie działania artefaktów platformy Spark </br> wszystkie akcje w działaniach platformy Spark_|Workspace</br>Pula platformy Spark|
|Synapse administratora SQL|Pełny dostęp Synapse do pul SQL bezserwerowych.  Twórz, Odczytuj, Aktualizuj i usuwaj dostęp do opublikowanych skryptów SQL, poświadczeń i połączonych usług.  Obejmuje dostęp do odczytu do wszystkich innych opublikowanych artefaktów kodu.  Nie obejmuje uprawnień do używania poświadczeń i uruchamiania potoków. Nie obejmuje udzielania dostępu. </br></br>*Może wykonywać wszystkie akcje w skryptach SQL <br/> , które mogą łączyć się z niektórymi punktami końcowymi programu SQL Server przy użyciu programu SQL `db_datareader` , `db_datawriter` , `connect` i `grant` uprawnień*|Workspace|
|Współautor Synapse|Pełny Synapse dostęp do pul Apache Spark i środowiska Integration Runtime. Obejmuje uprawnienia do tworzenia, odczytu, aktualizowania i usuwania wszystkich opublikowanych artefaktów kodu i ich danych wyjściowych, w tym poświadczeń i połączonych usług.  Obejmuje uprawnienia operatora obliczeniowego. Nie obejmuje uprawnień do używania poświadczeń i uruchamiania potoków. Nie obejmuje udzielania dostępu. </br></br>_Może odczytywać i zapisywać artefakty </br> mogą wyświetlać zapisane Notesy i dane wyjściowe potoku </br> . wszystkie akcje w działaniach platformy Spark </br> mogą wyświetlać dzienniki puli platformy Spark_|Workspace </br> Pula platformy Spark<br/> Integration Runtime|
|Wydawca artefaktu Synapse|Twórz, Odczytuj, Aktualizuj i usuwaj dostęp do opublikowanych artefaktów kodu i ich danych wyjściowych. Nie obejmuje uprawnień do uruchamiania kodu lub potoków ani do udzielania dostępu. </br></br>_Może odczytywać opublikowane artefakty i publikować artefakty </br> mogą wyświetlać zapisany Notes, zadanie Spark i dane wyjściowe potoku_|Workspace
|Użytkownik artefaktu Synapse|Dostęp do odczytu do opublikowanych artefaktów kodu i ich danych wyjściowych. Może tworzyć nowe artefakty, ale nie mogą publikować zmian ani uruchamiać kodu bez dodatkowych uprawnień.|Workspace
|Operator obliczeń Synapse |Przesyłaj zadania platformy Spark i notesy oraz Wyświetlaj dzienniki.  Obejmuje anulowanie zadań platformy Spark przesłanych przez dowolnego użytkownika. Wymaga dodatkowych uprawnień do korzystania z poświadczeń w ramach tożsamości systemu obszaru roboczego do uruchamiania potoków, wyświetlania uruchomień i wyjść potoków. </br></br>_Może przesyłać i anulować zadania, w tym zadania przesłane przez inne osoby </br> mogą wyświetlać dzienniki puli platformy Spark_|Workspace</br>Pula platformy Spark</br>Integration Runtime|
|Użytkownik poświadczenia Synapse|Środowisko uruchomieniowe i Konfiguracja użycia wpisów tajnych w ramach poświadczeń i połączonych usług w takich działaniach, jak uruchomienia potoków. Aby uruchomić potoki, ta rola jest wymagana w zakresie tożsamości systemu obszaru roboczego. </br></br>_W zakresie poświadczeń, zezwala na dostęp do danych za pośrednictwem połączonej usługi, która jest chroniona przez poświadczenie (wymaga również uprawnienia do użycia obliczeń), </br> umożliwia wykonywanie potoków chronionych przez poświadczenia tożsamości systemu obszaru roboczego (z dodatkowym uprawnieniem do użycia obliczeń)._|Workspace </br>Połączona usługa</br>Poświadczenie
|Synapse połączone Data Manager|Tworzenie i zarządzanie zarządzanymi prywatnymi punktami końcowymi, połączonymi usługami i poświadczeniami. Może tworzyć zarządzane prywatne punkty końcowe, które używają połączonych usług chronionych przez poświadczenia|Workspace|
|Synapse użytkownika|Wyświetlaj listy i Wyświetlaj szczegóły pul SQL, pul Apache Spark, środowisk Integration Runtime oraz opublikowanych połączonych usług i poświadczeń. Nie zawiera innych opublikowanych artefaktów kodu.  Można tworzyć nowe artefakty, ale nie można ich uruchamiać ani publikować bez dodatkowych uprawnień.</br></br>_Może wyświetlać i odczytywać pule platformy Spark, środowiska Integration Runtime._|Obszar roboczy, Pula platformy Spark</br>Połączona usługa </br>Poświadczenie|

## <a name="synapse-rbac-roles-and-the-actions-they-permit"></a>Synapse role RBAC i akcje, których zezwalają

>[!Note]
>- Wszystkie akcje wymienione w poniższych tabelach są poprzedzone prefiksem "Microsoft. Synapse/..."</br>
>- Wszystkie akcje odczytu, zapisu i usuwania artefaktów odnoszą się do opublikowanych artefaktów w usłudze Live.  Te uprawnienia nie wpływają na dostęp do artefaktów w połączonym repozytorium git.  

W poniższej tabeli wymieniono wbudowane role i akcje/uprawnienia, które każda z nich obsługuje.

Rola|Akcje
--|--
Synapse administrator|obszary robocze/odczyt</br>obszary robocze/roleAssignments/zapis, usuwanie</br>obszary robocze/managedPrivateEndpoint/zapis, usuwanie</br>obszary robocze/bigDataPools/useCompute/akcja</br>obszary robocze/bigDataPools/viewLogs/akcja</br>obszary robocze/integrationRuntimes/useCompute/akcja</br>obszary robocze/artefakty/odczyt</br>obszary robocze/notesy/zapis, usuwanie</br>obszary robocze/sparkJobDefinitions/zapis, usuwanie</br>obszary robocze/sqlscripters/Write, DELETE</br>obszary robocze/przepływy/zapisywanie, usuwanie</br>obszary robocze/potoki/zapis, usuwanie</br>obszary robocze/wyzwalacze/zapis, Usuń</br>obszary robocze/zestawy danych/zapisywanie, usuwanie</br>obszary robocze/biblioteki/zapis, usuwanie</br>obszary robocze/linkedServices/zapis, usuwanie</br>obszary robocze/poświadczenia/zapis, usuwanie</br>obszary robocze/notesy/viewOutputs/akcja</br>obszary robocze/potoki/viewOutputs/akcja</br>obszary robocze/linkedServices/useSecret/akcja</br>obszary robocze/poświadczenia/useSecret/akcja|
|Synapse Apache Spark administrator|obszary robocze/odczyt</br>obszary robocze/bigDataPools/useCompute/akcja</br>obszary robocze/bigDataPools/viewLogs/akcja</br>obszary robocze/notesy/viewOutputs/akcja</br>obszary robocze/artefakty/odczyt</br>obszary robocze/notesy/zapis, usuwanie</br>obszary robocze/sparkJobDefinitions/zapis, usuwanie</br>obszary robocze/biblioteki/zapis, usuwanie</br>obszary robocze/linkedServices/zapis, usuwanie</br>obszary robocze/poświadczenia/zapis, usuwanie|
|Synapse administratora SQL|obszary robocze/odczyt</br>obszary robocze/artefakty/odczyt</br>obszary robocze/sqlscripters/Write, DELETE</br>obszary robocze/linkedServices/zapis, usuwanie</br>obszary robocze/poświadczenia/zapis, usuwanie|
|Współautor Synapse|obszary robocze/odczyt</br>obszary robocze/bigDataPools/useCompute/akcja</br>obszary robocze/bigDataPools/viewLogs/akcja</br>obszary robocze/integrationRuntimes/useCompute/akcja</br>obszary robocze/integrationRuntimes/viewLogs/akcja</br>obszary robocze/artefakty/odczyt</br>obszary robocze/notesy/zapis, usuwanie</br>obszary robocze/sparkJobDefinitions/zapis, usuwanie</br>obszary robocze/sqlscripters/Write, DELETE</br>obszary robocze/przepływy/zapisywanie, usuwanie</br>obszary robocze/potoki/zapis, usuwanie</br>obszary robocze/wyzwalacze/zapis, Usuń</br>obszary robocze/zestawy danych/zapisywanie, usuwanie</br>obszary robocze/biblioteki/zapis, usuwanie</br>obszary robocze/linkedServices/zapis, usuwanie</br>obszary robocze/poświadczenia/zapis, usuwanie</br>obszary robocze/notesy/viewOutputs/akcja</br>obszary robocze/potoki/viewOutputs/akcja|
|Wydawca artefaktu Synapse|obszary robocze/odczyt</br>obszary robocze/artefakty/odczyt</br>obszary robocze/notesy/zapis, usuwanie</br>obszary robocze/sparkJobDefinitions/zapis, usuwanie</br>obszary robocze/sqlscripters/Write, DELETE</br>obszary robocze/przepływy/zapisywanie, usuwanie</br>obszary robocze/potoki/zapis, usuwanie</br>obszary robocze/wyzwalacze/zapis, Usuń</br>obszary robocze/zestawy danych/zapisywanie, usuwanie</br>obszary robocze/biblioteki/zapis, usuwanie</br>obszary robocze/linkedServices/zapis, usuwanie</br>obszary robocze/poświadczenia/zapis, usuwanie</br>obszary robocze/notesy/viewOutputs/akcja</br>obszary robocze/potoki/viewOutputs/akcja|
|Użytkownik artefaktu Synapse|obszary robocze/odczyt</br>obszary robocze/artefakty/odczyt</br>obszary robocze/notesy/viewOutputs/akcja</br>obszary robocze/potoki/viewOutputs/akcja|
|Operator obliczeń Synapse |obszary robocze/odczyt</br>obszary robocze/bigDataPools/useCompute/akcja</br>obszary robocze/bigDataPools/viewLogs/akcja</br>obszary robocze/integrationRuntimes/useCompute/akcja</br>obszary robocze/integrationRuntimes/viewLogs/akcja|
|Użytkownik poświadczenia Synapse|obszary robocze/odczyt</br>obszary robocze/linkedServices/useSecret/akcja</br>obszary robocze/poświadczenia/useSecret/akcja|
|Synapse połączone Data Manager|obszary robocze/odczyt</br>obszary robocze/managedPrivateEndpoint/zapis, usuwanie</br>obszary robocze/linkedServices/zapis, usuwanie</br>obszary robocze/poświadczenia/zapis, usuwanie|
|Synapse użytkownika|obszary robocze/odczyt|

## <a name="synapse-rbac-actions-and-the-roles-that-permit-them"></a>Synapse akcje RBAC i role, które je umożliwiają

W poniższej tabeli wymieniono akcje Synapse oraz wbudowane role zezwalające na następujące akcje:

Akcja|Rola
--|--
obszary robocze/odczyt|Synapse administrator</br>Synapse Apache Spark administrator</br>Synapse administratora SQL</br>Współautor Synapse</br>Wydawca artefaktu Synapse</br>Użytkownik artefaktu Synapse</br>Operator obliczeń Synapse </br>Użytkownik poświadczenia Synapse</br>Synapse połączone Data Manager</br>Synapse użytkownika 
obszary robocze/roleAssignments/zapis, usuwanie|Synapse administrator
obszary robocze/managedPrivateEndpoint/zapis, usuwanie|Synapse administrator</br>Synapse połączone Data Manager
obszary robocze/bigDataPools/useCompute/akcja|Synapse administrator</br>Synapse Apache Spark administrator</br>Współautor Synapse</br>Operator obliczeń Synapse 
obszary robocze/bigDataPools/viewLogs/akcja|Synapse administrator</br>Synapse Apache Spark administrator</br>Współautor Synapse</br>Operator obliczeń Synapse 
obszary robocze/integrationRuntimes/useCompute/akcja|Synapse administrator</br>Współautor Synapse</br>Operator obliczeń Synapse 
obszary robocze/artefakty/odczyt|Synapse administrator</br>Synapse Apache Spark administrator</br>Synapse administratora SQL</br>Współautor Synapse</br>Wydawca artefaktu Synapse</br>Użytkownik artefaktu Synapse
obszary robocze/notesy/zapis, usuwanie|Synapse administrator</br>Synapse Apache Spark administrator</br>Współautor Synapse</br>Wydawca artefaktu Synapse
obszary robocze/sparkJobDefinitions/zapis, usuwanie|Synapse administrator</br>Synapse Apache Spark administrator</br>Współautor Synapse</br>Wydawca artefaktu Synapse
obszary robocze/sqlscripters/Write, DELETE|Synapse administrator</br>Synapse administratora SQL</br>Współautor Synapse</br>Wydawca artefaktu Synapse
obszary robocze/przepływy/zapisywanie, usuwanie|Synapse administrator</br>Współautor Synapse</br>Wydawca artefaktu Synapse
obszary robocze/potoki/zapis, usuwanie|Synapse administrator</br>Współautor Synapse</br>Wydawca artefaktu Synapse
obszary robocze/wyzwalacze/zapis, Usuń|Synapse administrator</br>Współautor Synapse</br>Wydawca artefaktu Synapse
obszary robocze/zestawy danych/zapisywanie, usuwanie|Synapse administrator</br>Współautor Synapse</br>Wydawca artefaktu Synapse
obszary robocze/biblioteki/zapis, usuwanie|Synapse administrator</br>Synapse Apache Spark administrator</br>Współautor Synapse</br>Wydawca artefaktu Synapse
obszary robocze/linkedServices/zapis, usuwanie|Synapse administrator</br>Współautor Synapse</br>Wydawca artefaktu Synapse</br>Synapse połączone Data Manager
obszary robocze/poświadczenia/zapis, usuwanie|Synapse administrator</br>Współautor Synapse</br>Wydawca artefaktu Synapse</br>Synapse połączone Data Manager
obszary robocze/notesy/viewOutputs/akcja|Synapse administrator</br>Synapse Apache Spark administrator</br>Współautor Synapse</br>Wydawca artefaktu Synapse</br>Użytkownik artefaktu Synapse
obszary robocze/potoki/viewOutputs/akcja|Synapse administrator</br>Współautor Synapse</br>Wydawca artefaktu Synapse</br>Użytkownik artefaktu Synapse
obszary robocze/linkedServices/useSecret/akcja|Synapse administrator</br>Użytkownik poświadczenia Synapse
obszary robocze/poświadczenia/useSecret/akcja|Synapse administrator</br>Użytkownik poświadczenia Synapse

## <a name="synapse-rbac-scopes-and-their-supported-roles"></a>Synapse zakresy RBAC i ich obsługiwane role

W poniższej tabeli przedstawiono zakresy Synapse RBAC i role, które można przypisać do każdego zakresu. 

>[!note]
>Aby utworzyć lub usunąć obiekt, musisz mieć uprawnienia do zakresu wyższego poziomu.

Zakres|Role
--|--
Workspace |Synapse administrator</br>Synapse Apache Spark administrator</br>Synapse administratora SQL</br>Współautor Synapse</br>Wydawca artefaktu Synapse</br>Użytkownik artefaktu Synapse</br>Operator obliczeń Synapse </br>Użytkownik poświadczenia Synapse</br>Synapse połączone Data Manager</br>Synapse użytkownika
Pula Apache Spark | Synapse administrator </br>Współautor Synapse </br> Operator obliczeń Synapse
Integration Runtime | Synapse administrator </br>Współautor Synapse </br> Operator obliczeń Synapse
Połączona usługa |Synapse administrator </br>Użytkownik poświadczenia Synapse
Poświadczenie |Synapse administrator </br>Użytkownik poświadczenia Synapse
 
>[!note]
>Wszystkie role artefaktów i akcje są ograniczone do zakresu na poziomie obszaru roboczego. 

## <a name="next-steps"></a>Następne kroki

Dowiedz się [, jak przejrzeć przydziały ról RBAC Synapse](./how-to-review-synapse-rbac-role-assignments.md) dla obszaru roboczego.

Dowiedz się [, jak przypisać role RBAC Synapse](./how-to-manage-synapse-rbac-role-assignments.md)
