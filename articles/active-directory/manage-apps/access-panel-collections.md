---
title: Tworzenie kolekcji dla portali my Apps w Azure Active Directory | Microsoft Docs
description: Za pomocą kolekcji Moje aplikacje można dostosować strony Moje aplikacje, aby uprościć środowisko moje aplikacje dla użytkowników końcowych. Organizuj aplikacje w grupy z osobnymi kartami.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2020
ms.author: kenwith
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7743a1af54b01f848c4ac6f0cb1d4526d66f8132
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99254986"
---
# <a name="create-collections-on-the-my-apps-portal"></a>Tworzenie kolekcji w portalu Moje aplikacje

Użytkownicy mogą używać portalu My Apps do wyświetlania i uruchamiania aplikacji opartych na chmurze, do których mają dostęp. Domyślnie wszystkie aplikacje, do których użytkownik ma dostęp, są wyświetlane razem na jednej stronie. Aby lepiej zorganizować Tę stronę dla użytkowników, jeśli masz licencję na Azure AD — wersja Premium P1 lub P2, możesz skonfigurować kolekcje. Za pomocą kolekcji można grupować połączone aplikacje, które są powiązane (na przykład według roli zadania, zadania lub projektu) i wyświetlać je na osobnej karcie. Kolekcja zasadniczo stosuje filtr do aplikacji, do których użytkownik może uzyskać dostęp, więc użytkownik widzi tylko te aplikacje w kolekcji, które zostały do nich przypisane.

> [!NOTE]
> W tym artykule opisano sposób, w jaki administrator może włączyć i utworzyć kolekcje. Informacje dla użytkownika końcowego dotyczące korzystania z portalu Moje aplikacje i kolekcji znajdują się w temacie [Access and use Collections](../user-help/my-applications-portal-workspaces.md).

## <a name="enable-the-latest-my-apps-features"></a>Włącz najnowsze funkcje my Apps

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako administrator użytkownika lub Administrator globalny.

2. Przejdź do pozycji **Azure Active Directory**  >  **Ustawienia użytkownika**.

3. W obszarze **Podgląd funkcji użytkownika** wybierz pozycję **Zarządzaj ustawieniami wersji zapoznawczej funkcji użytkownika**.

4. W obszarze **Użytkownicy mogą używać funkcji w wersji zapoznawczej dla moich aplikacji**, wybierz jedną z następujących opcji:
   * **Zaznaczone** — włącza funkcje dla określonej grupy. Użyj opcji **Wybierz grupę** , aby wybrać grupę, dla której chcesz włączyć funkcje.  
   * **Wszystkie** — włącza funkcje dla wszystkich użytkowników.

> [!NOTE]
> Aby otworzyć Portal moje aplikacje, użytkownicy mogą używać linku `https://myapps.microsoft.com` lub niestandardowego linku dla organizacji, na przykład `https://myapps.microsoft.com/contoso.com` . Po włączeniu środowiska moje aplikacje zostanie wyświetlony transparent " **zaktualizowane środowisko moje aplikacje** " w górnej części strony Moje aplikacje, a użytkownicy mogą wybrać opcję **Wypróbuj** , aby wyświetlić nowe środowisko. Aby zrezygnować z korzystania z nowego środowiska, użytkownicy mogą wybrać opcję **tak** z transparentu **Opuść nowe środowisko** w górnej części strony.

## <a name="create-a-collection"></a>Tworzenie kolekcji

Aby utworzyć kolekcję, musisz mieć licencję na Azure AD — wersja Premium P1 lub P2.

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako administrator z licencją Azure AD — wersja Premium P1 lub P2.

2. Przejdź do pozycji **Azure Active Directory**  >  **aplikacje dla przedsiębiorstw**.

3. W obszarze **Zarządzaj** wybierz pozycję **kolekcje**.

4. Wybierz pozycję **Nowa kolekcja**. Na stronie **Nowa kolekcja** wprowadź **nazwę** kolekcji (nie zaleca się używania "kolekcji" w nazwie. Następnie wprowadź **Opis**.

   ![Nowa strona kolekcji](media/acces-panel-collections/new-collection.png)

5. Wybierz kartę **aplikacje** . Wybierz pozycję **+ Dodaj aplikację**, a następnie na stronie **Dodaj aplikacje** zaznacz wszystkie aplikacje, które chcesz dodać do kolekcji, lub użyj pola **wyszukiwania** , aby znaleźć aplikacje.

   ![Dodawanie aplikacji do kolekcji](media/acces-panel-collections/add-applications.png)

6. Po zakończeniu dodawania aplikacji wybierz pozycję **Dodaj**. Zostanie wyświetlona lista wybranych aplikacji. Aby zmienić kolejność aplikacji na liście, można użyć strzałek w górę. Aby przenieść aplikację w dół lub usunąć ją z kolekcji, wybierz menu **więcej** (**...**).

7. Wybierz kartę **właściciele** . Wybierz pozycję **+ Dodaj użytkowników i grupy**, a następnie na stronie **Dodawanie użytkowników i grup** wybierz użytkowników lub grupy, do których chcesz przypisać własność. Po zakończeniu wybierania pozycji Użytkownicy i grupy wybierz **pozycję Wybierz**.

9. Wybierz kartę **Użytkownicy i grupy** . Wybierz pozycję **+ Dodaj użytkowników i grupy**, a następnie na stronie **Dodawanie użytkowników i grup** wybierz użytkowników lub grupy, do których chcesz przypisać kolekcję. Lub użyj pola **wyszukiwania** , aby znaleźć użytkowników lub grupy. Po zakończeniu wybierania pozycji Użytkownicy i grupy wybierz **pozycję Wybierz**.

   ![Dodawanie użytkowników i grup](media/acces-panel-collections/add-users-and-groups.png)

11. Wybierz pozycję **Przejrzyj i utwórz**. Zostanie wyświetlona Właściwość nowej kolekcji.


## <a name="view-audit-logs"></a>Wyświetlanie dzienników inspekcji

Dzienniki inspekcji rejestrują operacje kolekcje moje aplikacje, w tym tworzenie kolekcji — akcje użytkownika końcowego. Następujące zdarzenia są generowane przez moje aplikacje:

* Utwórz kolekcję
* Edytuj kolekcję
* Usuwanie kolekcji
* Uruchom aplikację (użytkownik końcowy)
* Dodawanie aplikacji samoobsługowej (użytkownik końcowy)
* Usuwanie aplikacji samoobsługowej (użytkownik końcowy)

Aby uzyskać dostęp do dzienników inspekcji w [Azure Portal](https://portal.azure.com) , wybierz pozycję **Azure Active Directory**  >    >  **dzienniki inspekcji** aplikacji w przedsiębiorstwie w sekcji działanie. W obszarze **Usługa** wybierz pozycję **Moje aplikacje**.

## <a name="get-support-for-my-account-pages"></a>Uzyskaj pomoc techniczną dotyczącą stron My Account

Na stronie Moje aplikacje użytkownik może wybrać pozycję Moje konto   >  **Wyświetl moje konto** , aby otworzyć ustawienia konta. Na stronie **Moje konto** usługi Azure AD użytkownicy mogą zarządzać swoimi informacjami zabezpieczeń, urządzeniami, hasłami i nie tylko. Mogą również uzyskiwać dostęp do ustawień konta pakietu Office.

Jeśli musisz przesłać żądanie pomocy technicznej w celu uzyskania problemu na stronie konta usługi Azure AD lub stronie konta pakietu Office, wykonaj następujące kroki, aby Twoje żądanie zostało prawidłowo przekierowane: 

* W przypadku problemów z stroną **Moje konto usługi Azure AD** Otwórz żądanie pomocy technicznej z poziomu Azure Portal. Przejdź do **Azure Portal**  >  **Azure Active Directory**  >  **nowe żądanie obsługi**.

* W przypadku problemów z stroną **"Moje konto" pakietu Office** Otwórz żądanie pomocy technicznej w centrum administracyjnym Microsoft 365. Przejdź do pomocy technicznej **Centrum administracyjnego Microsoft 365**  >  . 

## <a name="next-steps"></a>Następne kroki
[Środowisko użytkownika końcowego dla aplikacji w Azure Active Directory](end-user-experiences.md)