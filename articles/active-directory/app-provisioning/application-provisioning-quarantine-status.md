---
title: Stan aprowizacji aplikacji dla kwarantanny | Microsoft Docs
description: Po skonfigurowaniu aplikacji do automatycznego inicjowania obsługi administracyjnej, Dowiedz się, co to jest stan aprowizacji środków kwarantanny i jak go wyczyścić.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 03/18/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 49590c46588ad0d0f1c1b7b095679a3c3fce96eb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579505"
---
# <a name="application-provisioning-in-quarantine-status"></a>Inicjowanie obsługi aplikacji w stanie kwarantanny

Usługa Azure AD Provisioning monitoruje kondycję konfiguracji. W stanie "Kwarantanna" są również umieszczane aplikacje w złej kondycji. Jeśli większość lub wszystkie wywołania wykonane względem systemu docelowego konsekwentnie się nie powiodą, zadanie aprowizacji zostanie oznaczone jako w kwarantannie. Przykładem błędu jest błąd odebrany z powodu nieprawidłowych poświadczeń administratora.

W kwarantannie:
- Częstotliwość cykli przyrostowych jest stopniowo zmniejszana do raz dziennie.
- Zadanie aprowizacji jest usuwane z kwarantanny po naprawieniu wszystkich błędów i uruchomieniu następnego cyklu synchronizacji. 
- Jeśli zadanie aprowizacji pozostaje w kwarantannie przez ponad cztery tygodnie, zadanie aprowizacji jest wyłączone (zatrzymuje działanie).

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>Jak mogę sprawdzić, czy moja aplikacja znajduje się w kwarantannie?

Istnieją trzy sposoby, aby sprawdzić, czy aplikacja znajduje się w kwarantannie:
  
- W Azure Portal przejdź do **Azure Active Directory**  >  **aplikacje korporacyjne**  >  &lt; *Nazwa aplikacji* &gt;  >  **Inicjowanie obsługi administracyjnej** i przejrzyj pasek postępu dla komunikatu kwarantanny.   

  ![Pasek stanu aprowizacji przedstawiający stan kwarantanny](./media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- W Azure Portal przejdź do **Azure Active Directory**  >  **dzienników inspekcji** > filtrem **działania: Kwarantanna** i przejrzyj historię kwarantanny. Widok na pasku postępu, zgodnie z powyższym opisem, wskazuje, czy inicjowanie obsługi jest obecnie w kwarantannie. W dziennikach inspekcji zostanie wyświetlona historia kwarantanny dla aplikacji. 

- Użyj żądania Microsoft Graph [Get synchronizationJob](/graph/api/synchronization-synchronizationjob-get?tabs=http&view=graph-rest-beta&preserve-view=true) , aby programowo pobrać stan zadania aprowizacji:

```microsoft-graph
        GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/
```

- Sprawdź swój adres e-mail. Gdy aplikacja zostanie umieszczona w kwarantannie, zostanie wysłana jednorazowa wiadomość e-mail z powiadomieniem. W przypadku zmiany przyczyny kwarantanny zostanie wysłana zaktualizowana wiadomość e-mail z informacją o nowej przyczynie kwarantanny. Jeśli nie widzisz wiadomości e-mail:

  - Upewnij się, że podano prawidłowy **adres E-mail powiadomienia** w konfiguracji aprowizacji dla aplikacji.
  - Upewnij się, że w skrzynce odbiorczej wiadomości e-mail z powiadomieniem nie ma filtrowania spamu.
  - Upewnij się, że nie masz subskrypcji wiadomości e-mail.
  - Wyszukaj wiadomości e-mail z `azure-noreply@microsoft.com`

## <a name="why-is-my-application-in-quarantine"></a>Dlaczego moja aplikacja jest poddana kwarantannie?

|Opis|Zalecana akcja|
|---|---|
|**Standard scim zgodności:** Zwrócona odpowiedź HTTP/404 nie została znaleziona, a nie oczekiwana odpowiedź HTTP/200 OK. W takim przypadku usługa Azure AD Provisioning zgłosiła żądanie do aplikacji docelowej i odebrała nieoczekiwaną odpowiedź.|Zapoznaj się z sekcją poświadczenia administratora. Sprawdź, czy aplikacja wymaga określenia adresu URL dzierżawy oraz że adres URL jest poprawny. Jeśli nie widzisz problemu, skontaktuj się z deweloperem aplikacji, aby upewnić się, że ich usługi są zgodne z standard scim. https://tools.ietf.org/html/rfc7644#section-3.4.2 |
|**Nieprawidłowe poświadczenia:** Podczas próby autoryzacji dostęp do aplikacji docelowej otrzymał odpowiedź od aplikacji docelowej, która wskazuje, że podane poświadczenia są nieprawidłowe.|Przejdź do sekcji poświadczenia administratora w interfejsie użytkownika konfiguracji aprowizacji i ponownie Autoryzuj dostęp z prawidłowymi poświadczeniami. Jeśli aplikacja znajduje się w galerii, zapoznaj się z samouczkiem dotyczącym konfiguracji aplikacji, aby uzyskać już wymagane kroki.|
|**Zduplikowane role:** Role zaimportowane z niektórych aplikacji, takich jak Salesforce i systemu Zendesk, muszą być unikatowe. |Przejdź do [manifestu](../develop/reference-app-manifest.md) aplikacji w Azure Portal i Usuń zduplikowaną rolę.|

 Microsoft Graph żądanie pobrania stanu zadania aprowizacji wskazuje następujące przyczyny kwarantanny:
- `EncounteredQuarantineException` wskazuje, że podano nieprawidłowe poświadczenia. Usługa aprowizacji nie może nawiązać połączenia między systemem źródłowym i systemem docelowym.
- `EncounteredEscrowProportionThreshold` wskazuje, że inicjowanie obsługi przekroczyło próg Escrow. Ten stan występuje, gdy więcej niż 40% zdarzeń aprowizacji nie powiodło się. Aby uzyskać więcej informacji, zobacz szczegóły progu Escrow poniżej.
- `QuarantineOnDemand` oznacza, że wykryto problem z aplikacją i ręcznie ją ustawił na kwarantannę.

**Progi Escrow**

Jeśli zostanie osiągnięty proporcjonalny próg Escrow, zadanie aprowizacji zostanie umieszczone w kwarantannie. Ta logika może ulec zmianie, ale działa w sposób niezależny, zgodnie z poniższym opisem: 

Zadanie może przejść do kwarantanny niezależnie od liczby błędów w przypadku problemów, takich jak poświadczenia administratora lub zgodność Standard scim. Jednak ogólnie rzecz biorąc, błędy 5 000 są minimalne, aby rozpocząć ocenianie z powodu zbyt dużej liczby błędów. Na przykład zadanie z błędami 4 000 nie przejdzie do kwarantanny. Jednak zadanie z błędami 5 000 wywoła ocenę. W ocenie są stosowane następujące kryteria:  
- Jeśli więcej niż 40% zdarzeń aprowizacji zakończy się niepowodzeniem lub występuje więcej niż 40 000 błędów, zadanie aprowizacji zostanie przełączone do kwarantanny. Błędy odwołań nie będą zliczane jako część progu 40% lub 40 000. Na przykład nie można zaktualizować Menedżera lub członek grupy jest błędem odwołania.
- Zadanie, gdzie 45 000 użytkowników zostało niepomyślnie zainicjowana, doprowadziłoby do kwarantanny, ponieważ przekracza próg 40 000.
- Zadanie, w którym 30 000 użytkowników nie zakończyło się niepowodzeniem i 5 000, mogło prowadzić do kwarantanny, ponieważ przekroczy próg 40% i 5 000 minimum.
- Zadanie z 20 000 niepowodzeń i 100 000 powodzenie nie powiodło się, ponieważ Nowak nie przekracza progu błędu 40% lub niepowodzenia 40 000.  
- Istnieje bezwzględny próg 60 000 błędów, które są kontami zarówno dla błędów odwołania, jak i niezwiązanych z odwołaniem. Na przykład 40 000 nie udało się zainicjowanie obsługi administracyjnej użytkowników i nie powiodło się aktualizowanie Menedżera 21 000. Suma to 61 000 błędów i przekracza limit 60 000.


## <a name="how-do-i-get-my-application-out-of-quarantine"></a>Jak mogę uzyskać mojej aplikacji z kwarantanny?

Najpierw należy rozwiązać problem, który spowodował umieszczenie aplikacji w kwarantannie.

- Sprawdź ustawienia aprowizacji aplikacji, aby upewnić się, że [wprowadzono prawidłowe poświadczenia administratora](../app-provisioning/configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning). Usługa Azure AD musi ustanowić relację zaufania z aplikacją docelową. Upewnij się, że wprowadzono prawidłowe poświadczenia, a Twoje konto ma wymagane uprawnienia.

- Przejrzyj [dzienniki aprowizacji](../reports-monitoring/concept-provisioning-logs.md) , aby dokładniej zbadać, jakie błędy powodują Kwarantanna i rozwiązać problem. Przejdź do pozycji **Azure Active Directory** &gt; dzienniki aprowizacji **aplikacji w przedsiębiorstwie** &gt; **(wersja zapoznawcza)** w sekcji **działanie** .

Po rozwiązaniu problemu należy ponownie uruchomić zadanie aprowizacji. Pewne zmiany ustawień aprowizacji aplikacji, takie jak mapowania atrybutów lub filtry zakresu, będą automatycznie ponownie uruchamiać Inicjowanie obsługi. Pasek postępu na stronie **aprowizacji** aplikacji wskazuje czas ostatniego uruchomienia aprowizacji. Jeśli konieczne jest ręczne ponowne uruchomienie zadania aprowizacji, należy użyć jednej z następujących metod:  

- Użyj Azure Portal, aby ponownie uruchomić zadanie aprowizacji. Na stronie **aprowizacji** aplikacji wybierz pozycję **Uruchom ponownie Inicjowanie obsługi administracyjnej**. Ta akcja powoduje w pełni ponowne uruchomienie usługi aprowizacji, która może zająć trochę czasu. Pełny cykl początkowy zostanie uruchomiony ponownie, co oznacza, że usługa Escrow usunie aplikację z kwarantanny i wyczyści wszystkie znaki wodne. Następnie usługa ponownie oceni wszystkich użytkowników w systemie źródłowym i określi, czy znajdują się one w zakresie aprowizacji. Może to być przydatne, gdy aplikacja jest obecnie w kwarantannie, jak w tym artykule omówiono, lub należy wprowadzić zmiany mapowań atrybutów. Należy zauważyć, że cykl początkowy trwa dłużej niż typowy przyrostowy cykl ze względu na liczbę obiektów, które należy oszacować. Więcej informacji o wydajności początkowych i przyrostowych cykli można znaleźć [tutaj](application-provisioning-when-will-provisioning-finish-specific-user.md).

- Użyj Microsoft Graph, aby [ponownie uruchomić zadanie aprowizacji](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta&preserve-view=true). Będziesz mieć pełną kontrolę nad tym, co zostało ponownie uruchomione. Możesz wybrać opcję wyczyszczenia usługi Escrow (aby ponownie uruchomić licznik Escrow, który naliczy na status kwarantanny), wyczyścić opcję kwarantanny (w celu usunięcia aplikacji z kwarantanny) lub wyczyścić znaki wodne. Użyj następującego żądania:
 
```microsoft-graph
        POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart
```

Zastąp ciąg "{ID}" wartością identyfikatora aplikacji i Zastąp ciąg "{jobId}" [identyfikatorem zadania synchronizacji](/graph/api/resources/synchronization-configure-with-directory-extension-attributes?tabs=http&view=graph-rest-beta&preserve-view=true#list-synchronization-jobs-in-the-context-of-the-service-principal).
