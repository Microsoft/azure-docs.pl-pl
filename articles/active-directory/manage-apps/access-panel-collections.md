---
title: Tworzenie kolekcji dla Moje aplikacje portali w Azure Active Directory | Microsoft Docs
description: Użyj Moje aplikacje, aby dostosować Moje aplikacje stron, aby Moje aplikacje środowisko użytkownika końcowego. Organizowanie aplikacji w grupy przy użyciu oddzielnych kart.
services: active-directory
documentationcenter: ''
author: iantheninja
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2020
ms.author: iangithinji
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc79e8026cb91b8ef3eac2addbb097b9db83afa7
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377687"
---
# <a name="create-collections-on-the-my-apps-portal"></a>Tworzenie kolekcji w portalu Moje aplikacje

Użytkownicy mogą używać portalu Moje aplikacje, aby wyświetlać i uruchamiać aplikacje oparte na chmurze, do których mają dostęp. Domyślnie wszystkie aplikacje, do których użytkownik może uzyskać dostęp, są wyświetlane razem na jednej stronie. Aby lepiej zorganizować tę stronę dla użytkowników, jeśli masz licencję Azure AD — wersja Premium P1 lub P2, możesz skonfigurować kolekcje. Kolekcja umożliwia grupowanie powiązanych aplikacji (na przykład według roli zadania, zadania lub projektu) i wyświetlanie ich na osobnej karcie. Kolekcja zasadniczo stosuje filtr do aplikacji, do których użytkownik może już uzyskać dostęp, więc widzi tylko te aplikacje w kolekcji, które zostały do niego przypisane.

> [!NOTE]
> W tym artykule opisano, jak administrator może włączać i tworzyć kolekcje. Aby uzyskać informacje dla użytkownika końcowego dotyczące sposobu korzystania z portalu Moje aplikacje i kolekcji, zobacz Access and use collections (Uzyskiwanie dostępu do kolekcji i [korzystanie z nich).](../user-help/my-applications-portal-workspaces.md)

## <a name="enable-the-latest-my-apps-features"></a>Włączanie najnowszych Moje aplikacje aplikacji

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako administrator użytkowników lub administrator globalny.

2. Przejdź do **Azure Active Directory**  >  **Ustawienia użytkownika.**

3. W **obszarze Wersje zapoznawcze funkcji użytkownika** wybierz pozycję Zarządzaj **ustawieniami wersji zapoznawczej funkcji użytkownika.**

4. W **obszarze Użytkownicy mogą używać funkcji** w Moje aplikacje wersji zapoznawczej wybierz jedną z następujących opcji:
   * **Wybrane** — włącza funkcje dla określonej grupy. Użyj opcji **Wybierz grupę,** aby wybrać grupę, dla której chcesz włączyć funkcje.  
   * **Wszystkie** — włącza funkcje dla wszystkich użytkowników.

> [!NOTE]
> Aby otworzyć Moje aplikacje portal, użytkownicy mogą użyć linku lub dostosowanego linku dla `https://myapps.microsoft.com` organizacji, takiego jak `https://myapps.microsoft.com/contoso.com` . Po włączeniu nowego Moje aplikacje użytkownika w  górnej części strony Moje aplikacje zostanie wyświetlany baner Zaktualizowane środowisko Moje aplikacje.  Użytkownicy mogą wybrać pozycję Wypróbuj, aby wyświetlić nowe środowisko. Aby przestać korzystać z nowego oprogramowania,  użytkownicy mogą wybrać pozycję **Tak** na banerze Omiń nowe środowisko w górnej części strony.

## <a name="create-a-collection"></a>Tworzenie kolekcji

Aby utworzyć kolekcję, musisz mieć licencję Azure AD — wersja Premium P1 lub P2.

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako administrator przy użyciu Azure AD — wersja Premium P1 lub P2.

2. Przejdź do **Azure Active Directory**  >  **aplikacje dla przedsiębiorstw.**

3. W **obszarze Zarządzaj** wybierz pozycję **Kolekcje.**

4. Wybierz **pozycję Nowa kolekcja**. Na stronie **Nowa kolekcja** wprowadź **nazwę** kolekcji (nie zalecamy używania w nazwie słowa "kolekcja". Następnie wprowadź **opis**.

   ![Nowa strona kolekcji](media/acces-panel-collections/new-collection.png)

5. Wybierz **kartę** Aplikacje. Wybierz **pozycję + Dodaj** aplikację, a następnie na stronie Dodawanie aplikacji wybierz wszystkie aplikacje, które chcesz dodać do kolekcji, lub użyj pola Wyszukaj, aby znaleźć aplikacje.  

   ![Dodawanie aplikacji do kolekcji](media/acces-panel-collections/add-applications.png)

6. Po zakończeniu dodawania aplikacji wybierz pozycję **Dodaj**. Zostanie wyświetlona lista wybranych aplikacji. Za pomocą strzałek w górę można zmienić kolejność aplikacji na liście. Aby przenieść aplikację w dół lub usunąć ją z kolekcji, wybierz menu **Więcej** (**...**).

7. Wybierz **kartę Właściciele.** Wybierz **pozycję + Dodaj użytkowników** i  grupy, a następnie na stronie Dodawanie użytkowników i grup wybierz użytkowników lub grupy, do których chcesz przypisać własność. Po zakończeniu wybierania użytkowników i grup wybierz pozycję **Wybierz**.

9. Wybierz **kartę Użytkownicy i** grupy. Wybierz pozycję + Dodaj użytkowników i  **grupy,** a następnie na stronie Dodawanie użytkowników i grup wybierz użytkowników lub grupy, do których chcesz przypisać kolekcję. Możesz też użyć **pola Wyszukaj,** aby znaleźć użytkowników lub grupy. Po zakończeniu wybierania użytkowników i grup wybierz pozycję **Wybierz**.

   ![Dodawanie użytkowników i grup](media/acces-panel-collections/add-users-and-groups.png)

11. Wybierz pozycję **Przejrzyj i utwórz**. Zostaną wyświetlone właściwości nowej kolekcji.


## <a name="view-audit-logs"></a>Wyświetlanie dzienników inspekcji

Dzienniki inspekcji rejestr Moje aplikacje kolekcji, w tym akcje użytkownika końcowego związane z tworzeniem kolekcji. Następujące zdarzenia są generowane z Moje aplikacje:

* Tworzenie kolekcji
* Edytuj kolekcję
* Usuwanie kolekcji
* Uruchamianie aplikacji (użytkownik końcowy)
* Dodawanie aplikacji samoobsługowej (użytkownik końcowy)
* Samoobsługowe usuwanie aplikacji (użytkownik końcowy)

Dostęp do dzienników inspekcji można uzyskać w [Azure Portal,](https://portal.azure.com) wybierając pozycję **Azure Active Directory** inspekcji aplikacji dla przedsiębiorstw  >    >   w sekcji Działanie. W **przypadku usługi** wybierz pozycję **Moje aplikacje**.

## <a name="get-support-for-my-account-pages"></a>Uzyskiwanie pomocy technicznej dla stron Moje konto

Na stronie Moje aplikacje użytkownik może wybrać pozycję **Moje konto** Wyświetl moje konto, aby otworzyć ustawienia swojego  >   konta. Na stronie Moje konto **usługi** Azure AD użytkownicy mogą zarządzać informacjami zabezpieczającymi, urządzeniami, hasłami i nie tylko. Mogą również uzyskać dostęp do ustawień konta pakietu Office.

Jeśli musisz przesłać żądanie pomocy technicznej dotyczące problemu ze stroną konta usługi Azure AD lub stroną konta pakietu Office, wykonaj następujące kroki, aby żądanie zostało prawidłowo kierowane: 

* W przypadku problemów ze stroną **"Moje konto"** usługi Azure AD otwórz żądanie pomocy technicznej z poziomu Azure Portal. Przejdź do **Azure Portal**  >  **Azure Active Directory**  >  **nowy wniosek o pomoc techniczną.**

* W przypadku problemów ze stroną **"Moje konto" pakietu Office** otwórz żądanie pomocy technicznej z poziomu centrum administracyjne platformy Microsoft 365. Przejdź do **centrum administracyjne platformy Microsoft 365**  >  **pomocy technicznej.** 

## <a name="next-steps"></a>Następne kroki
[Środowisko użytkownika końcowego dla aplikacji w Azure Active Directory](end-user-experiences.md)