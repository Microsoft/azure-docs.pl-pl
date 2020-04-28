---
title: Stan aprowizacji aplikacji dla kwarantanny | Microsoft Docs
description: Po skonfigurowaniu aplikacji do automatycznego inicjowania obsługi administracyjnej, Dowiedz się, co to jest stan aprowizacji środków kwarantanny i jak go wyczyścić.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 563c049bf3d1606e87db54e3b003dac987594610
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80154631"
---
# <a name="application-provisioning-in-quarantine-status"></a>Inicjowanie obsługi aplikacji w stanie kwarantanny

Usługa Azure AD Provisioning monitoruje kondycję konfiguracji i umieszcza w stanie "Kwarantanna" aplikacje w złej kondycji. Jeśli większość lub wszystkie wywołania związane z systemem docelowym są spójne niepowodzeniem z powodu błędu, na przykład nieprawidłowe poświadczenia administratora, zadanie aprowizacji zostanie oznaczone jako w kwarantannie.

Podczas kwarantanny Częstotliwość cykli przyrostowych jest stopniowo zmniejszana do raz dziennie. Zadanie aprowizacji jest usuwane z kwarantanny po naprawieniu wszystkich błędów i uruchomieniu następnego cyklu synchronizacji. Jeśli zadanie aprowizacji pozostaje w kwarantannie przez ponad cztery tygodnie, zadanie aprowizacji jest wyłączone (zatrzymuje działanie).

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>Jak mogę sprawdzić, czy moja aplikacja znajduje się w kwarantannie?

Istnieją trzy sposoby, aby sprawdzić, czy aplikacja znajduje się w kwarantannie:
  
- W Azure Portal przejdź do **Azure Active Directory** > **aplikacje** > &lt;korporacyjne*Nazwa*&gt; > aplikacji**Inicjowanie obsługi** i przewiń na pasek postępu u dołu.  

  ![Pasek stanu aprowizacji przedstawiający stan kwarantanny](./media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- W Azure Portal przejdź do **Azure Active Directory** > **dzienników inspekcji** > filtrem **działania: Kwarantanna** i przejrzyj historię kwarantanny. Gdy widok na pasku postępu, jak opisano powyżej, wskazuje, czy inicjowanie obsługi jest obecnie w kwarantannie, dzienniki inspekcji umożliwiają wyświetlenie historii kwarantanny dla aplikacji. 

- Użyj żądania Microsoft Graph [Get synchronizationJob](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-get?view=graph-rest-beta&tabs=http) , aby programowo pobrać stan zadania aprowizacji:

        `GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/`

- Sprawdź swój adres e-mail. Gdy aplikacja zostanie umieszczona w kwarantannie, zostanie wysłana jednorazowa wiadomość e-mail z powiadomieniem. W przypadku zmiany przyczyny kwarantanny zostanie wysłana zaktualizowana wiadomość e-mail z informacją o nowej przyczynie kwarantanny. Jeśli nie widzisz wiadomości e-mail:

  - Upewnij się, że podano prawidłowy **adres E-mail powiadomienia** w konfiguracji aprowizacji dla aplikacji.
  - Upewnij się, że w skrzynce odbiorczej wiadomości e-mail z powiadomieniem nie ma filtrowania spamu.
  - Upewnij się, że nie masz subskrypcji wiadomości e-mail.

## <a name="why-is-my-application-in-quarantine"></a>Dlaczego moja aplikacja jest poddana kwarantannie?

Microsoft Graph żądanie pobrania stanu zadania aprowizacji wskazuje następujące przyczyny kwarantanny:

- `EncounteredQuarantineException`wskazuje, że podano nieprawidłowe poświadczenia. Usługa aprowizacji nie może nawiązać połączenia między systemem źródłowym i systemem docelowym.

- `EncounteredEscrowProportionThreshold`wskazuje, że inicjowanie obsługi przekroczyło próg Escrow. Ten stan występuje, gdy więcej niż 60% zdarzeń aprowizacji nie powiodło się.

- `QuarantineOnDemand`oznacza, że wykryto problem z aplikacją i ręcznie ją ustawił na kwarantannę.

## <a name="how-do-i-get-my-application-out-of-quarantine"></a>Jak mogę uzyskać mojej aplikacji z kwarantanny?

Najpierw należy rozwiązać problem, który spowodował umieszczenie aplikacji w kwarantannie.

- Sprawdź ustawienia aprowizacji aplikacji, aby upewnić się, że [wprowadzono prawidłowe poświadczenia administratora](../app-provisioning/configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning). Usługa Azure AD musi być w stanie ustanowić relację zaufania z aplikacją docelową. Upewnij się, że wprowadzono prawidłowe poświadczenia, a Twoje konto ma wymagane uprawnienia.

- Przejrzyj [dzienniki aprowizacji](../reports-monitoring/concept-provisioning-logs.md) , aby dokładniej zbadać, jakie błędy powodują Kwarantanna i rozwiązać problem. Uzyskaj dostęp do dzienników aprowizacji w Azure Portal, przechodząc do **Azure Active Directory** &gt; dzienniki aprowizacji **aplikacji** &gt; w przedsiębiorstwie **(wersja zapoznawcza)** w sekcji **działanie** .

Po rozwiązaniu problemu należy ponownie uruchomić zadanie aprowizacji. Pewne zmiany ustawień aprowizacji aplikacji, takie jak mapowania atrybutów lub filtry zakresu, będą automatycznie ponownie uruchamiać Inicjowanie obsługi. Pasek postępu na stronie **aprowizacji** aplikacji wskazuje czas ostatniego uruchomienia aprowizacji. Jeśli konieczne jest ręczne ponowne uruchomienie zadania aprowizacji, należy użyć jednej z następujących metod:  

- Użyj Azure Portal, aby ponownie uruchomić zadanie aprowizacji. Na stronie **aprowizacji** aplikacji w obszarze **Ustawienia**wybierz pozycję **Wyczyść stan i ponownie uruchom synchronizację** , a następnie ustaw **stan aprowizacji** na **włączone**. Ta akcja powoduje w pełni ponowne uruchomienie usługi aprowizacji, która może zająć trochę czasu. Pełny cykl początkowy zostanie uruchomiony ponownie, co oznacza, że usługa Escrow usunie aplikację z kwarantanny i wyczyści wszystkie znaki wodne.

- Użyj Microsoft Graph, aby [ponownie uruchomić zadanie aprowizacji](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http). Będziesz mieć pełną kontrolę nad tym, co zostało ponownie uruchomione. Możesz wybrać opcję wyczyszczenia usługi Escrow (aby ponownie uruchomić licznik Escrow, który naliczy na status kwarantanny), wyczyścić opcję kwarantanny (w celu usunięcia aplikacji z kwarantanny) lub wyczyścić znaki wodne. Użyj następującego żądania:
 
       `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`
