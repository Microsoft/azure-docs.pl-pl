---
title: Arkose Labs z Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Dowiedz się, jak zintegrować uwierzytelnianie Azure AD B2C z Arkose Labs, aby pomóc w ochronie przed atakami bot, atakami na przejmowanie kont i nieuczciwych otwartych kontach.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 333bb42643539cedec04d37680749c749a003536
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994064"
---
# <a name="tutorial-for-configuring-arkose-labs-with-azure-active-directory-b2c"></a>Samouczek dotyczący konfigurowania Arkose Labs przy użyciu Azure Active Directory B2C

W tym samouczku dowiesz się, jak zintegrować uwierzytelnianie Azure AD B2C z Arkose Labs. Arkose Labs pomagają organizacjom przed atakami bot, atakami polegającymi na przejęciu kont i oszustwem.  

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, musisz:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* [Dzierżawa Azure AD B2C](tutorial-create-tenant.md) , która jest połączona z subskrypcją platformy Azure.

## <a name="scenario-description"></a>Opis scenariusza

Na poniższym diagramie opisano, jak Arkose Labs integrują się z Azure AD B2C.

![Diagram architektury Arkose Labs](media/partner-arkose-labs/arkose-architecture-diagram.png)

| Krok  | Opis |
|---|---|
|1     | Użytkownik loguje się przy użyciu wcześniej utworzonego konta. Gdy użytkownik wybierze opcję Prześlij, pojawi się wyzwanie Arkose Labs. Gdy użytkownik zakończy wyzwanie, stan zostanie wysłany do Arkose Labs w celu wygenerowania tokenu.        |
|2     |  Arkose Labs wysyła token z powrotem do Azure AD B2C.       |
|3     |  Przed przesłaniem formularza logowania token jest wysyłany do Arkose Labs w celu weryfikacji.       |
|4     |  Arkose wysyła wynik sukcesu lub niepowodzenia z wyzwania.       |
|5     |  Jeśli wyzwanie zostanie zakończone pomyślnie, formularz logowania zostanie przesłany do Azure AD B2C i Azure AD B2C kończy uwierzytelnianie.       |
|   |   |

## <a name="onboard-with-arkose-labs"></a>Dołączanie do Arkose Labs

1. Zacznij od skontaktowania się z [Arkose Labs](https://www.arkoselabs.com/book-a-demo/) i utworzenia konta.

2. Po utworzeniu konta przejdź do https://dashboard.arkoselabs.com/login .

3. Na pulpicie nawigacyjnym przejdź do ustawień witryny, aby znaleźć klucz publiczny i klucz prywatny. Te informacje będą konieczne później w celu skonfigurowania Azure AD B2C.

## <a name="integrate-with-azure-ad-b2c"></a>Integracja z usługą Azure AD B2C

### <a name="part-1--create-blob-storage-to-store-the-custom-html"></a>Część 1 — Tworzenie magazynu obiektów BLOB do przechowywania niestandardowego kodu HTML

Aby utworzyć konto magazynu, wykonaj następujące czynności:  

1. Zaloguj się do Azure Portal.

2. Upewnij się, że korzystasz z katalogu, który zawiera subskrypcję platformy Azure. W górnym menu wybierz pozycję **katalog i subskrypcja** , a następnie wybierz katalog, który zawiera twoją subskrypcję. Ten katalog jest inny niż ten, który zawiera dzierżawę usługi Azure B2C.

3. Wybierz pozycję **wszystkie usługi** w lewym górnym rogu Azure Portal, a następnie wyszukaj i wybierz pozycję  **konta magazynu**.

4. Wybierz przycisk **Dodaj**.

5. W obszarze  **Grupa zasobów** wybierz pozycję  **Utwórz nową**, wprowadź nazwę nowej grupy zasobów, a następnie wybierz przycisk **OK**.

6. Wprowadź nazwę konta magazynu. Wybrana nazwa musi być unikatowa w obrębie platformy Azure, musi mieć długość od 3 do 24 znaków oraz może zawierać tylko cyfry i małe litery.

7. Wybierz lokalizację konta magazynu lub zaakceptuj lokalizację domyślną.

8. Zaakceptuj wszystkie pozostałe wartości domyślne, wybierz pozycję   **Przejrzyj & Utwórz**  >  **Utwórz**.

9. Po utworzeniu konta magazynu wybierz pozycję  **Przejdź do zasobu**.

#### <a name="create-a-container"></a>Tworzenie kontenera

1. Na stronie Przegląd konta magazynu wybierz pozycję  **obiekty blob**.

2. Wybierz pozycję   **kontener**, wprowadź nazwę kontenera, wybierz pozycję   **obiekt BLOB** (Anonimowy dostęp do odczytu tylko dla obiektów BLOB), a następnie wybierz przycisk **OK**.

#### <a name="enable-cross-origin-resource-sharing-cors"></a>Włącz współużytkowanie zasobów między źródłami (CORS)

Azure AD B2C kod w przeglądarce korzysta z nowoczesnego i standardowego podejścia do załadowania niestandardowej zawartości z adresu URL określonego w przepływie użytkownika. Mechanizm CORS umożliwia żądanie zasobów na stronie sieci Web z innych domen.

1. Z menu wybierz pozycję  **CORS**.

2. Dla  **dozwolonych źródeł** wprowadź  `https://your-tenant-name.b2clogin.com` . Zastąp swoją nazwę dzierżawy nazwą swojej dzierżawy Azure AD B2C. Na przykład  `https://fabrikam.b2clogin.com`. W przypadku wprowadzania nazwy dzierżawy używaj wszystkich małych liter.

3. Dla  **dozwolonych metod** wybierz pozycję  **Get**, **Put** i  **Options**.

4. Dla **dozwolonych nagłówków** Wprowadź gwiazdkę (*).

5. W przypadku **widocznych nagłówków** Wprowadź gwiazdkę (*).

6. W obszarze **Maksymalny wiek** wprowadź 200.

   ![Rejestracja i logowanie w usłudze Arkose Labs](media/partner-arkose-labs/signup-signin-arkose.png)

7. Wybierz pozycję **Zapisz**.

### <a name="part-2--set-up-a-back-end-server"></a>Część 2 — Konfigurowanie serwera zaplecza

Pobierz narzędzie git bash i wykonaj poniższe czynności:

1. Postępuj zgodnie z instrukcjami, aby [utworzyć aplikację sieci Web](../app-service/quickstart-php.md), dopóki nie zostanie wyświetlony komunikat "gratulacje!Twoja pierwsza aplikacja w języku PHP została wdrożona do App Service ".

2. Otwórz folder lokalny i Zmień nazwę pliku **index. php** na **verify-token. php**.

3. Otwórz plik verify-token. php o nowo zmienionej nazwie i:

   a. Zastąp zawartość zawartością z pliku VERIFY-token. php znajdującą się w [repozytorium GitHub](https://github.com/ArkoseLabs/Azure-AD-B2C).

   b. Zastąp <private_key> w wierszu 3 kluczem prywatnym uzyskanym z pulpitu nawigacyjnego Arkose Labs.

4. W oknie terminalu lokalnego Zatwierdź zmiany w usłudze git, a następnie wypchnij zmiany kodu na platformę Azure, wpisując następujące polecenie w narzędziu git bash:

   ``git commit -am "updated output"``

   ``git push azure master``  

### <a name="part-3---final-setup"></a>Część 3 — Instalacja końcowa

#### <a name="store-the-custom-html"></a>Przechowywanie niestandardowego kodu HTML

1. Otwórz plik index.html przechowywany w [repozytorium GitHub](https://github.com/ArkoseLabs/Azure-AD-B2C).

2. Zamień wszystkie wystąpienia z `<tenantname>` nazwą dzierżawy b2C (innymi słowy `<tenantname>.b2clogin.com` ). Powinny istnieć cztery wystąpienia.

3. Zastąp wartość `<appname>` nazwą aplikacji utworzoną w części 2, krok 1.

   ![Zrzut ekranu przedstawiający interfejs wiersza polecenia platformy Azure Arkose Labs](media/partner-arkose-labs/arkose-azure-cli.png)

4. Zastąp `<public_key>` wiersz w wierszu 64 kluczem publicznym uzyskanym z poziomu pulpitu nawigacyjnego Arkose Labs.

5. Przekaż plik index.html do utworzonego powyżej magazynu obiektów BLOB.

6. Przejdź do **Storage**  >  **Container**  >  **przekazywania** kontenera magazynu.

#### <a name="set-up-azure-ad-b2c"></a>Skonfiguruj Azure AD B2C

> [!NOTE]
> Jeśli jeszcze tego nie zrobiono, [Utwórz dzierżawę Azure AD B2C](tutorial-create-tenant.md) , która jest połączona z subskrypcją platformy Azure.

1. Utwórz przepływ użytkownika na podstawie informacji znajdujących się [tutaj](tutorial-create-user-flows.md). Zatrzymaj, gdy dojdziesz do sekcji **testowanie przepływu użytkownika**.

2. Włącz język JavaScript w [przepływie użytkownika](user-flow-javascript-overview.md).

3. Na tej samej stronie przepływu użytkownika Włącz adres URL strony niestandardowej: Przejdź do pozycji **przepływ użytkownika**  >  **Układ strony**,  >  **Użyj niestandardowej zawartości strony**  =  **tak**, aby  >  **wstawić niestandardowy adres URL strony**.
Ten adres URL strony niestandardowej jest uzyskiwany z lokalizacji pliku index.html wewnątrz magazynu obiektów BLOB  

   ![Zrzut ekranu przedstawiający adres URL magazynu Arkose Labs](media/partner-arkose-labs/arkose-storage-url.png)

## <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. Otwórz dzierżawcę Azure AD B2C i w obszarze **zasady** wybierz pozycję **przepływy użytkownika**.

2. Wybierz wcześniej utworzony przepływ użytkownika.

3. Wybierz pozycję **Uruchom przepływ użytkownika** i wybierz ustawienia:

   a. **Aplikacja** — wybierz zarejestrowanej aplikacji (przykład: JWT).

   b. **Adres URL odpowiedzi** — wybierz adres URL przekierowania.

   c. Wybierz pozycję **Uruchom przepływ użytkownika**.

4. Przejdź do przepływu rejestracji i Utwórz konto.

5. Wyloguj się.

6. Przejdź przez przepływ logowania.

7. Po wybraniu opcji **Kontynuuj** zostanie wyświetlona Arkose Labs.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dodatkowe informacje, zapoznaj się z następującymi artykułami:

- [Zasady niestandardowe w usłudze Azure AD B2C](custom-policy-overview.md)

- [Wprowadzenie do zasad niestandardowych w Azure AD B2C](custom-policy-get-started.md?tabs=applications)