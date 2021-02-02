---
title: Eksportuj konfigurację aprowizacji i przywracaj do znanego dobrego stanu na potrzeby odzyskiwania po awarii
description: Dowiedz się, jak wyeksportować konfigurację aprowizacji i wrócić do znanego dobrego stanu na potrzeby odzyskiwania po awarii.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 03/19/2020
ms.author: kenwith
ms.openlocfilehash: c6af42c78bda66c4b397cbb99b26af7d6a5c7f07
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256377"
---
# <a name="how-to-export-provisioning-configuration-and-roll-back-to-a-known-good-state"></a>Porada: eksportowanie konfiguracji aprowizacji i przywracanie do znanego dobrego stanu

Ten artykuł obejmuje następujące zagadnienia:

- Wyeksportuj i zaimportuj konfigurację aprowizacji z Azure Portal
- Eksportowanie i Importowanie konfiguracji aprowizacji za pomocą interfejsu API Microsoft Graph

## <a name="export-and-import-your-provisioning-configuration-from-the-azure-portal"></a>Wyeksportuj i zaimportuj konfigurację aprowizacji z Azure Portal

### <a name="export-your-provisioning-configuration"></a>Eksportowanie konfiguracji aprowizacji

Aby wyeksportować konfigurację:

1. W [Azure Portal](https://portal.azure.com/)w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.
1. W okienku **Azure Active Directory** wybierz pozycję **aplikacje dla przedsiębiorstw** i wybierz aplikację.
1. W okienku nawigacji po lewej stronie wybierz opcję **Inicjowanie obsługi**. Na stronie Konfiguracja aprowizacji kliknij pozycję **mapowania atrybutów**, a następnie **Wyświetl opcje zaawansowane** i **na koniec Przejrzyj schemat**. Spowoduje to przejście do Edytora schematu.
1. Kliknij pozycję Pobierz na pasku poleceń w górnej części strony, aby pobrać schemat.

### <a name="disaster-recovery---roll-back-to-a-known-good-state"></a>Odzyskiwanie po awarii — przywracanie do znanego dobrego stanu

Eksportowanie i Zapisywanie konfiguracji pozwala przywrócić poprzednią wersję konfiguracji. Zalecamy eksportowanie konfiguracji aprowizacji i zapisanie jej w celu późniejszego użycia w dowolnym momencie, gdy wprowadzisz zmiany do mapowań atrybutów lub filtrów zakresu. Wystarczy otworzyć plik JSON, który został pobrany w powyższych krokach, skopiować całą zawartość pliku JSON, zastąpić całą zawartość ładunku JSON w edytorze schematu, a następnie zapisać. Jeśli istnieje aktywny cykl aprowizacji, zostanie ukończony, a następny cykl będzie korzystał ze zaktualizowanego schematu. Następny cykl będzie również cyklem wstępnym, który ponownie oblicza każdy użytkownik i grupę w oparciu o nową konfigurację. Podczas wycofywania do poprzedniej konfiguracji należy wziąć pod uwagę następujące kwestie:

- Użytkownicy będą ponownie oceniani, aby określić, czy powinny znajdować się w zakresie. Jeśli filtry zakresu zostały zmienione, użytkownik nie znajduje się w zakresie, co spowoduje, że nie zostaną one wyłączone. Chociaż jest to odpowiednie zachowanie w większości przypadków, istnieją przypadki, w których może być konieczne ich uniemożliwienie i użycie funkcji [pomijania usunięć poza zakresem](./skip-out-of-scope-deletions.md) . 
- Zmiana konfiguracji aprowizacji powoduje ponowne uruchomienie usługi i wyzwala [cykl początkowy](./how-provisioning-works.md#provisioning-cycles-initial-and-incremental).

## <a name="export-and-import-your-provisioning-configuration-by-using-the-microsoft-graph-api"></a>Eksportowanie i Importowanie konfiguracji aprowizacji za pomocą interfejsu API Microsoft Graph

Możesz użyć interfejsu API Microsoft Graph i Eksploratora Microsoft Graph, aby wyeksportować mapowania atrybutów aprowizacji użytkowników i schemat do pliku JSON, a następnie zaimportować go z powrotem do usługi Azure AD. Możesz również użyć tutaj przechwyconych kroków, aby utworzyć kopię zapasową konfiguracji aprowizacji.

### <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Krok 1. Pobieranie inicjowania obsługi App Service Identyfikator podmiotu zabezpieczeń (identyfikator obiektu)

1. Uruchom [Azure Portal](https://portal.azure.com)i przejdź do sekcji właściwości aplikacji aprowizacji. Na przykład jeśli chcesz wyeksportować swój *dzień roboczy do usługi AD mapowanie aplikacji* , przejdź do sekcji Właściwości tej aplikacji.
1. W sekcji właściwości aplikacji aprowizacji skopiuj wartość identyfikatora GUID skojarzoną z polem *Identyfikator obiektu* . Ta wartość jest również nazywana **ServicePrincipalId** aplikacji i będzie używana w operacjach Microsoft Graph Explorer.

   ![Identyfikator podmiotu zabezpieczeń App Service Workday](./media/export-import-provisioning-configuration/wd_export_01.png)

### <a name="step-2-sign-into-microsoft-graph-explorer"></a>Krok 2. Logowanie do Microsoft Graph Explorer

1. Uruchom [eksploratora Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer)
1. Kliknij przycisk "Zaloguj się za pomocą firmy Microsoft" i zaloguj się przy użyciu poświadczeń administratora globalnego usługi Azure AD lub administratora aplikacji.

    ![Logowanie Microsoft Graph](./media/export-import-provisioning-configuration/wd_export_02.png)

1. Po pomyślnym zalogowaniu zobaczysz szczegóły konta użytkownika w okienku po lewej stronie.

### <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>Krok 3. Pobieranie identyfikatora zadania aprowizacji dla aplikacji aprowizacji

W Eksploratorze Microsoft Graph Uruchom następujące polecenie GET Query zastępujące [servicePrincipalId] z **servicePrincipalId** wyodrębnionym z [kroku 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

Otrzymasz odpowiedź, jak pokazano poniżej. Skopiuj "identyfikator atrybutu" obecny w odpowiedzi. Ta wartość jest **ProvisioningJobId** i zostanie użyta do pobrania metadanych bazowego schematu.

   [![Identyfikator zadania aprowizacji](./media/export-import-provisioning-configuration/wd_export_03.png)](./media/export-import-provisioning-configuration/wd_export_03.png#lightbox)

### <a name="step-4-download-the-provisioning-schema"></a>Krok 4. Pobieranie schematu aprowizacji

W Eksploratorze Microsoft Graph Uruchom następujące zapytanie GET, zastępując [servicePrincipalId] i [ProvisioningJobId] ServicePrincipalId i ProvisioningJobId pobrane w poprzednich krokach.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Skopiuj obiekt JSON z odpowiedzi i Zapisz go w pliku, aby utworzyć kopię zapasową schematu.

### <a name="step-5-import-the-provisioning-schema"></a>Krok 5. Importowanie schematu aprowizacji

> [!CAUTION]
> Wykonaj ten krok tylko wtedy, gdy musisz zmodyfikować schemat konfiguracji, którego nie można zmienić przy użyciu Azure Portal lub jeśli musisz przywrócić konfigurację z wcześniej wykonanej kopii zapasowej, używając prawidłowego i roboczego schematu.

W Eksploratorze Microsoft Graph skonfiguruj następujące zapytanie PUT, zastępując [servicePrincipalId] i [ProvisioningJobId] ServicePrincipalId i ProvisioningJobId pobrane w poprzednich krokach.

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Na karcie "treść żądania" Skopiuj zawartość pliku schematu JSON.

   [![Treść żądania](./media/export-import-provisioning-configuration/wd_export_04.png)](./media/export-import-provisioning-configuration/wd_export_04.png#lightbox)

Na karcie "nagłówki żądań" Dodaj atrybut nagłówka Content-Type z wartością "Application/JSON"

   [![Nagłówki żądań](./media/export-import-provisioning-configuration/wd_export_05.png)](./media/export-import-provisioning-configuration/wd_export_05.png#lightbox)

Wybierz pozycję **Uruchom zapytanie** , aby zaimportować nowy schemat.