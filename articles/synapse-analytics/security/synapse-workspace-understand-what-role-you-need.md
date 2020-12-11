---
title: Informacje o rolach wymaganych do wykonywania typowych zadań w programie Synapse
description: W tym artykule opisano, które wbudowane role RBAC Synapse są wymagane do wykonania określonych zadań
author: billgib
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: billgib
ms.reviewer: jrasnick
ms.openlocfilehash: 9735293c182e7fe67a498529425459c13a199101
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109797"
---
# <a name="understand-the-roles-required-to-perform-common-tasks-in-synapse"></a>Informacje o rolach wymaganych do wykonywania typowych zadań w programie Synapse

Ten artykuł ułatwi zapoznanie się z Synapse RBAC (kontroli dostępu opartego na rolach) lub ról RBAC platformy Azure, które należy wykonać w programie Synapse Studio.  

## <a name="synapse-studio-access-control-and-workflow-summary"></a>Synapse Studio — kontrola dostępu i podsumowanie przepływu pracy 

### <a name="accessing-synapse-studio-and-viewing-its-content"></a>Uzyskiwanie dostępu do programu Synapse Studio i wyświetlanie jego zawartości

- Można otworzyć program Synapse Studio i wyświetlić szczegóły obszaru roboczego oraz listę zasobów platformy Azure (pule SQL, pule Spark lub środowisko Integration Runtime), jeśli przypisano dowolną rolę RBAC Synapse lub rolę właściciela, współautora lub czytnika platformy Azure w obszarze roboczym.

### <a name="resource-management"></a>Zarządzanie zasobami

- Możesz tworzyć pule SQL, pule Apache Spark i środowiska Integration Runtime, jeśli jesteś właścicielem lub współautorem platformy Azure w obszarze roboczym.
- Możesz wstrzymać lub skalować dedykowaną pulę SQL, skonfigurować pulę platformy Spark lub środowisko Integration Runtime, jeśli jesteś właścicielem lub współautorem platformy Azure w obszarze roboczym lub tym zasobem.

### <a name="viewing-and-editing-code-artifacts"></a>Wyświetlanie i edytowanie artefaktów kodu

- Dzięki dostępowi do programu Synapse Studio można tworzyć nowe artefakty kodu, takie jak skrypty SQL, notesy, zadania platformy Spark, połączone usługi, potoki, przepływy danych, wyzwalacze i poświadczenia.  (Te artefakty mogą być publikowane lub zapisywane z dodatkowymi uprawnieniami).  
- Jeśli jesteś użytkownikiem artefaktu Synapse, Wydawca artefaktu Synapse, współautor Synapse lub administrator Synapse, można wyświetlać, otwierać i edytować już opublikowane artefakty kodu.

### <a name="executing-your-code"></a>Wykonywanie kodu

- Skrypty SQL w pulach SQL można wykonywać, jeśli masz wymagane uprawnienia SQL zdefiniowane w pulach SQL.  
- Można uruchamiać Notesy i zadania platformy Spark, jeśli masz uprawnienia operatora obliczeniowego Synapse w obszarze roboczym lub określonych pulach Apache Spark.  
- Za pomocą uprawnień operatora obliczeń w obszarze roboczym lub określonych środowiskach Integration Runtime i odpowiednich uprawnień poświadczeń można wykonywać potoki.

### <a name="monitoring-and-managing-execution"></a>Monitorowanie i Zarządzanie wykonywaniem
- Jeśli jesteś użytkownikiem Synapse, możesz sprawdzić stan uruchomionych notesów i zadań w pulach Apache Spark.
- Możesz przejrzeć dzienniki i anulować uruchomione zadania i potoki, jeśli jesteś operatorem obliczeń Synapse w obszarze roboczym lub dla określonej puli lub potoku.  

### <a name="publishing-and-saving-your-code"></a>Publikowanie i zapisywanie kodu

- Możesz publikować nowe lub zaktualizowane artefakty kodu w usłudze, jeśli jesteś Synapseem, Współpracownikem Synapse lub administratorem Synapse. 
- Możesz zatwierdzić artefakty kodu do roboczej gałęzi repozytorium git, jeśli obszar roboczy jest włączony przy użyciu usługi git i masz uprawnienia git. Po włączeniu usługi git publikowanie jest dozwolone tylko z gałęzi współpracy.
- Jeśli zamkniesz program Synapse Studio bez publikowania lub zatwierdzania zmian w artefaktach kodu, te zmiany zostaną utracone.


## <a name="tasks-and-required-roles"></a>Zadania i wymagane role

W poniższej tabeli wymieniono typowe zadania i dla każdego zadania, Synapse RBAC lub role RBAC platformy Azure.  

>[!Note]
>- Synapse administrator nie jest wymieniony dla każdego zadania, chyba że jest to jedyna rola, która zapewnia niezbędne uprawnienia.  Administrator Synapse może wykonywać wszystkie zadania włączone przez inne role Synapse RBAC.</br>
>- Jest wyświetlana minimalna wymagana rola RBAC Synapse.
>- Wszystkie role Synapse RBAC w dowolnym zakresie zapewniają Synapse uprawnienia użytkownika w obszarze roboczym
>- Wszystkie uprawnienia i akcje RBAC Synapse są wyświetlane w tabeli jako prefiksy Microsoft/Synapse/Workspaces/... </br>


Zadanie (chcę...) |Rola (muszę być...)|Synapse/akcja kontroli RBAC
--|--|--
|Otwórz Synapse Studio w obszarze roboczym|Synapse użytkownika lub|przeczytaj
| |Właściciel, współautor lub czytelnik platformy Azure w obszarze roboczym|brak
|Wyświetl listę pul SQL, pul Apache Spark, środowisk Integration Runtime i uzyskaj dostęp do ich szczegółów konfiguracji|Synapse użytkownika lub|przeczytaj|
||Właściciel, współautor lub czytelnik platformy Azure w obszarze roboczym|brak
|Wyświetlanie listy połączonych usług, poświadczeń, zarządzanych prywatnych punktów końcowych|Synapse użytkownika|przeczytaj
PULE SQL|
Tworzenie dedykowanej puli SQL lub bezserwerowej puli SQL|Właściciel lub współautor platformy Azure w obszarze roboczym|brak
Zarządzanie (wstrzymywanie, skalowanie lub usuwanie) dedykowanej puli SQL|Właściciel lub współautor platformy Azure w puli lub obszarze roboczym SQL|brak
Tworzenie skryptu SQL</br>|Synapse użytkownika lub </br>Właściciel lub współautor platformy Azure w obszarze roboczym </br>*Aby można było uruchomić skrypt SQL, opublikować lub zatwierdzić zmiany, wymagane są dodatkowe uprawnienia SQL*.|
Wyświetl listę i Otwórz dowolny opublikowany skrypt SQL| Użytkownik artefaktu Synapse, Wydawca artefaktów, współautor Synapse|artefakty/odczyt
Uruchamianie skryptu SQL w puli SQL bezserwerowej|Uprawnienia SQL do puli (przyznane automatycznie administratorowi Synapse)|brak
Uruchamianie skryptu SQL w dedykowanej puli SQL|Uprawnienia SQL do puli|brak
Opublikuj nowy, zaktualizowany lub usunięty skrypt SQL|Wydawca artefaktu Synapse, współautor Synapse|sqlscripts/Write, DELETE
Zatwierdź zmiany w skrypcie SQL w repozytorium git|Wymaga uprawnień Git w repozytorium|
Przypisywanie Active Directory administratora w obszarze roboczym (za pomocą właściwości obszaru roboczego w witrynie Azure Portal)|Właściciel lub współautor platformy Azure w obszarze roboczym |
PULE PLATFORMY APACHE SPARK|
Tworzenie puli Apache Spark|Właściciel lub współautor platformy Azure w obszarze roboczym|
Monitorowanie aplikacji Apache Spark| Synapse użytkownika|przeczytaj
Wyświetlanie dzienników dla notesu i wykonywania zadań |Operator obliczeń Synapse|
Anulowanie dowolnego notesu lub zadania Spark uruchomionego w puli Apache Spark|Synapse operator obliczeń w puli Apache Spark.|bigDataPools/useCompute
Tworzenie notesu lub definicji zadania|Synapse użytkownika lub </br>Właściciel, współautor lub czytelnik platformy Azure w obszarze roboczym</br> *Do uruchamiania, publikowania lub zatwierdzania zmian są wymagane dodatkowe uprawnienia*|przeczytaj</br></br></br></br></br> 
Wyświetlenie listy i otwarcie opublikowanego notesu lub definicji zadania, w tym przeglądanie zapisanych danych wyjściowych|Użytkownik artefaktu Synapse, Wydawca artefaktu Synapse, współautor Synapse w obszarze roboczym|artefakty/odczyt
Uruchamianie notesu i przeglądanie jego danych wyjściowych|Synapse Apache Spark administrator, operator obliczeń Synapse na wybranej puli Apache Spark|bigDataPools/useCompute 
Publikowanie lub usuwanie notesu lub definicji zadania (łącznie z danymi wyjściowymi) do usługi|Wydawca artefaktu w obszarze roboczym, Synapse Apache Spark administrator|Notesy/zapis, usuwanie
Zatwierdź zmiany w notesie lub definicji zadania w repozytorium git|Uprawnienia git|brak
POTOKI, ŚRODOWISKA INTEGRATION RUNTIME, DATAFLOWS, ZESTAWY DANYCH & WYZWALACZE|
Tworzenie, aktualizowanie lub usuwanie środowiska Integration Runtime|Właściciel lub współautor platformy Azure w obszarze roboczym|
Monitoruj stan środowiska Integration Runtime|Synapse użytkownika|Odczyt, potoki/viewOutputs
Przejrzyj uruchomienia potoku|Współautor artefaktu Synapse/Synapse|Odczyt, potoki/viewOutputs 
Tworzenie potoku |Synapse użytkownika</br>*Aby debugować, dodać wyzwalacze, opublikować lub zatwierdzić zmiany, wymagane są dodatkowe uprawnienia Synapse*|przeczytaj
Tworzenie przepływu danych lub zestawu danych |Synapse użytkownika</br>*Aby opublikować lub zatwierdzić zmiany, wymagane są dodatkowe uprawnienia Synapse.*|przeczytaj
Wyświetlanie i otwieranie opublikowanego potoku |Użytkownik artefaktu Synapse | artefakty/odczyt
Podgląd danych zestawu danych|Synapse użytkownika i poświadczenia Synapse na WorkspaceSystemIdentity| 
Debugowanie potoku przy użyciu domyślnego środowiska Integration Runtime|Synapse użytkownika i poświadczenia Synapse użytkownika w poświadczeniu WorkspaceSystemIdentity|Przeczytaj </br>poświadczenia/useSecret
Utwórz wyzwalacz, włącznie z wyzwalaczem teraz (wymaga uprawnień do wykonania potoku)|Synapse użytkownika i poświadczenia Synapse na WorkspaceSystemIdentity|Odczyt, poświadczenia/useSecret/akcja
Wykonaj/Uruchom potok|Synapse użytkownika i poświadczenia Synapse na WorkspaceSystemIdentity|Odczyt, poświadczenia/useSecret/akcja
Kopiowanie danych przy użyciu narzędzia Kopiowanie danych|Synapse użytkownika i poświadczenia Synapse użytkownika na tożsamość systemu obszaru roboczego|Odczyt, poświadczenia/useSecret/akcja
Pobieranie danych (przy użyciu harmonogramu)|Synapse autora + Synapse użytkownika poświadczeń na tożsamości systemu obszaru roboczego|Odczyt, poświadczenia/useSecret/akcja
Opublikuj nowy, zaktualizowany lub usunięty potok, przepływu danych lub wyzwalacz do usługi|Synapse Wydawca artefaktu w obszarze roboczym|potoki/zapis, usuwanie</br>dataflows/Write, DELETE</br>Wyzwalacze/zapis, usuwanie
Zatwierdź zmiany w potokach, dataflows, zestawach danych lub wyzwalaczach w repozytorium git |Uprawnienia git|brak 
POŁĄCZONE USŁUGI|
Tworzenie połączonej usługi (łącznie z przypisaniem poświadczenia)|Synapse użytkownika</br>*Aby można było użyć połączonej usługi z poświadczeniami lub opublikować lub zatwierdzić zmiany, wymagane są dodatkowe uprawnienia.*|przeczytaj
Wyświetlanie listy i otwieranie opublikowanej połączonej usługi|Użytkownik artefaktu Synapse|linkedServices/Write, DELETE  
Test connection w połączonej usłudze zabezpieczonej przez poświadczenie|Użytkownik Synapse i administrator poświadczeń Synapse|poświadczenia/useSecret/akcja|
Publikowanie połączonej usługi|Wydawca artefaktu Synapse, Synapse połączone Data Manager|linkedServices/Write, DELETE
Zatwierdź definicje połączonych usług do repozytorium git|Uprawnienia git|brak
ZARZĄDZANIE DOSTĘPEM|
Przeglądanie przypisań ról Synapse RBAC w dowolnym zakresie|Synapse użytkownika|przeczytaj
Przypisywanie i usuwanie przypisań roli RBAC Synapse dla użytkowników, grup i jednostek usługi| Synapse administratora w obszarze roboczym lub w określonym zakresie elementów obszaru roboczego|roleAssignments/Write, DELETE 

>[!Note]
>Użytkownicy-Goście z innej dzierżawy nie mogą przeglądać, dodawać ani zmieniać przypisań ról niezależnie od przypisanej roli. 

## <a name="next-steps"></a>Następne kroki

Dowiedz się [, jak przejrzeć przydziały ról RBAC Synapse](./how-to-review-synapse-rbac-role-assignments.md)

Dowiedz się [, jak zarządzać przypisaniami ról RBAC Synapse](./how-to-manage-synapse-rbac-role-assignments.md). 