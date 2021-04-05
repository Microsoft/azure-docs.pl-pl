---
title: Synapse obszaru roboczego — Omówienie kontroli dostępu
description: W tym artykule opisano mechanizmy służące do kontrolowania dostępu do obszaru roboczego Synapse oraz zasobów i artefaktów kodu, które zawiera.
services: synapse-analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 12/03/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 20614b1c397bdf24e807d48d3de33f0033da14bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100105117"
---
# <a name="synapse-access-control"></a>Synapse kontroli dostępu 

Ten artykuł zawiera omówienie mechanizmów dostępnych do kontrolowania dostępu do zasobów i danych obliczeniowych Synapse.  

## <a name="overview"></a>Omówienie

Synapse zapewnia kompleksowy i szczegółowy system kontroli dostępu, który integruje się z: 
- **Role platformy Azure** służące do zarządzania zasobami i uzyskiwania dostępu do danych w magazynie, 
- **Role Synapse** do zarządzania dostępem na żywo do kodu i wykonywania, 
- **Role SQL** umożliwiające dostęp do danych w pulach SQL w ramach płaszczyzny danych. 
- **Uprawnienia git** do kontroli kodu źródłowego, w tym obsługa ciągłej integracji i wdrażania.  

Role Synapse zapewniają zestawy uprawnień, które mogą być stosowane w różnych zakresach. Ten stopień szczegółowości ułatwia udzielenie dostępu do administratorów, deweloperów, personelu zabezpieczeń i operatorów w celu obliczenia zasobów i danych.

Kontrolę dostępu można uprościć, korzystając z grup zabezpieczeń, które są wyrównane do ról zadań osób. Aby zarządzać dostępem, wystarczy dodawać i usuwać użytkowników z odpowiednich grup zabezpieczeń.

## <a name="access-control-elements"></a>Elementy kontroli dostępu

### <a name="creating-and-managing-synapse-compute-resources"></a>Tworzenie zasobów obliczeniowych Synapse i zarządzanie nimi

Role platformy Azure służą do kontrolowania zarządzania programu: 
- Dedykowane pule SQL, 
- Pule Apache Spark i 
- Środowisko Integration Runtime. 

Aby *utworzyć* te zasoby, musisz być właścicielem lub współautorem platformy Azure w grupie zasobów. Aby *zarządzać* nimi po utworzeniu, musisz być właścicielem lub współautorem platformy Azure w grupie zasobów lub poszczególnych zasobów. 

### <a name="developing-and-executing-code-in-synapse"></a>Opracowywanie i wykonywanie kodu w Synapse 

Synapse obsługuje dwa modele deweloperskie.

- **Synapse na żywo**. Opracowujesz i debugujesz kod w programie Synapse Studio, a następnie **publikujesz** go w celu zapisywania i wykonywania.  Usługa Synapse jest źródłem prawdy do edytowania i wykonywania kodu.  Nieopublikowana pracy zostanie utracona po zamknięciu programu Synapse Studio.  
- **Programowanie z obsługą usługi git**. Opracowujesz i debugujesz kod w programie Synapse Studio i **zatwierdzasz** zmiany w roboczej gałęzi repozytorium git. Praca z co najmniej jednej gałęzi jest zintegrowana z gałęzią współpracy, w której **publikujesz** ją w usłudze. Repozytorium git jest źródłem prawdy do edycji kodu, a usługa jest źródłem prawdy do wykonania. Przed zamknięciem programu Synapse Studio należy zatwierdzić zmiany w repozytorium Git lub opublikować je w usłudze. [Dowiedz się więcej](../cicd/continuous-integration-deployment.md) o korzystaniu z Synapse Analytics z usługą git.

W obu modelach programistycznych każdy użytkownik mający dostęp do programu Synapse Studio może tworzyć artefakty kodu. Wymagane są jednak dodatkowe uprawnienia do publikowania artefaktów w usłudze, odczytywania opublikowanych artefaktów, zatwierdzania zmian w usłudze git, wykonywania kodu i uzyskiwania dostępu do połączonych danych chronionych przez poświadczenia.

### <a name="synapse-roles"></a>Role Synapse

Role Synapse są używane do kontrolowania dostępu do usługi Synapse, która umożliwia: 
- Wyświetlanie listy artefaktów opublikowanych kodu, 
- Publikuj artefakty kodu, połączone usługi i definicje poświadczeń,
- Wykonaj kod lub potoki korzystające z zasobów Synapse COMPUTE,
- Wykonaj kod lub potoki, które uzyskują dostęp do połączonych danych chronionych za pomocą poświadczeń
- Wyświetlanie danych wyjściowych skojarzonych z opublikowanymi artefaktami kodu
- Monitoruj stan zasobów obliczeniowych i wyświetlaj dzienniki środowiska uruchomieniowego.

Role Synapse można przypisywać w zakresie obszaru roboczego lub w bardziej precyzyjnych zakresach, aby ograniczyć uprawnienia przyznane do określonych zasobów Synapse.

### <a name="git-permissions"></a>Uprawnienia git

W przypadku korzystania z funkcji deweloperskich z obsługą usługi Git w trybie git Twoje uprawnienia git określają, czy można odczytywać i zatwierdzać zmiany artefaktów kodu, w tym połączone usługi i definicje poświadczeń.   
   
### <a name="accessing-data-in-sql"></a>Uzyskiwanie dostępu do danych w programie SQL

Podczas pracy z dedykowanymi i bezserwerowymi pulami SQL dostęp do płaszczyzny danych jest kontrolowany przy użyciu uprawnień SQL. 

Twórca obszaru roboczego jest przypisany jako administrator Active Directory w obszarze roboczym. Po utworzeniu tej roli można przypisać do innego użytkownika lub do grupy zabezpieczeń w Azure Portal.

**Pule SQL bezserwerowe**: Synapse Administratorzy są przyznany `db_owner` ( `DBO` ) uprawnienia w puli SQL bezserwerowej, "wbudowane". Aby udzielić innym użytkownikom dostępu do pul SQL bezserwerowych, Administratorzy Synapse muszą uruchamiać skrypty SQL w każdej puli bezserwerowej.  

**Dedykowane pule SQL**: uprawnienia administratora Active Directory są udzielane twórcy obszaru roboczego i pliku MSI obszaru roboczego.  Uprawnienia dostępu do dedykowanych pul SQL nie są udzielane automatycznie. Aby udzielić innym użytkownikom lub grupom dostępu do dedykowanych pul SQL, administrator Active Directory musi uruchamiać skrypty SQL dla każdej dedykowanej puli SQL.

Zobacz [jak skonfigurować Synapse Access Control](./how-to-set-up-access-control.md) , aby poznać przykłady skryptów SQL na potrzeby przyznawania uprawnień SQL w pulach SQL.  

 ### <a name="accessing-system-managed-data-in-storage"></a>Uzyskiwanie dostępu do danych zarządzanych przez system w magazynie

Pule SQL bezserwerowe i tabele Apache Spark przechowują swoje dane w kontenerze ADLS Gen2 skojarzonym z obszarem roboczym. Biblioteki Apache Spark zainstalowane przez użytkownika również są zarządzane w ramach tego samego konta magazynu. Aby włączyć te przypadki użycia, użytkownicy i plik MSI obszaru roboczego muszą mieć przyznaną funkcję dostępu **współautor danych obiektów BLOB** do tego obszaru roboczego ADLS Gen2 kontenerem magazynu.  

## <a name="using-security-groups-as-a-best-practice"></a>Korzystanie z grup zabezpieczeń jako najlepszych rozwiązań

Aby uprościć zarządzanie kontrolą dostępu, można użyć grup zabezpieczeń do przypisywania ról do osób i grup. Grupy zabezpieczeń mogą być tworzone w celu dublowania funkcji osób lub zadań w organizacji, które wymagają dostępu do zasobów Synapse lub artefaktów.  Do tych grup zabezpieczeń opartych na osobach można przypisać jedną lub więcej ról platformy Azure, ról Synapse, uprawnień SQL lub uprawnień git. Za pomocą dobrze wybranych grup zabezpieczeń można łatwo przypisywać użytkownikowi wymagane uprawnienia, dodając je do odpowiedniej grupy zabezpieczeń. 

>[!Note]
>W przypadku korzystania z grup zabezpieczeń do zarządzania dostępem istnieje dodatkowe opóźnienie wprowadzane przez Azure Active Directory, zanim zmiany zaczną obowiązywać. 

## <a name="access-control-enforcement-in-synapse-studio"></a>Wymuszanie kontroli dostępu w programie Synapse Studio

Program Synapse Studio zadziała inaczej w zależności od uprawnień i bieżącego trybu:
- **Synapse tryb na żywo:** Program Synapse Studio uniemożliwi wyświetlanie opublikowanej zawartości, publikowanie zawartości lub podejmowanie innych akcji, jeśli nie masz wymaganego uprawnienia.  W niektórych przypadkach nie będzie można tworzyć artefaktów kodu, których nie można użyć ani zapisać. 
- **Tryb git:** Jeśli masz uprawnienia git, które umożliwiają zatwierdzenie zmian w bieżącej gałęzi, Akcja zatwierdzania będzie dozwolona nawet wtedy, gdy nie masz uprawnień do publikowania zmian w usłudze na żywo.  

W niektórych przypadkach można tworzyć artefakty kodu nawet bez uprawnień do publikowania lub zatwierdzania. Dzięki temu można wykonać kod (z wymaganymi uprawnieniami wykonywania). [Dowiedz się więcej](./synapse-workspace-understand-what-role-you-need.md) o rolach wymaganych do wykonywania typowych zadań. 

Jeśli funkcja jest wyłączona w programie Synapse Studio, etykietka narzędzia będzie wskazywać wymagane uprawnienia. Użyj [przewodnika ról RBAC Synapse](./synapse-workspace-synapse-rbac-roles.md#synapse-rbac-actions-and-the-roles-that-permit-them) , aby wyszukać rolę wymaganą do dostarczenia brakującego uprawnienia.


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [Synapse RBAC](./synapse-workspace-synapse-rbac.md)
- Dowiedz się więcej o [ROLACH RBAC Synapse](./synapse-workspace-synapse-rbac-roles.md)
- Dowiedz się [, jak skonfigurować kontrolę dostępu](./how-to-set-up-access-control.md) dla obszaru roboczego Synapse przy użyciu grup zabezpieczeń.
- Dowiedz się [, jak przejrzeć przydziały ról RBAC Synapse](./how-to-review-synapse-rbac-role-assignments.md)
- Dowiedz się [, jak zarządzać przypisaniami ról RBAC Synapse](./how-to-manage-synapse-rbac-role-assignments.md)
