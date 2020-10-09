---
title: Uwierzytelnianie i autoryzacja dla Web Apps statycznej platformy Azure
description: Dowiedz się, jak używać różnych dostawców autoryzacji do zabezpieczania aplikacji statycznej.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: e95cd313d341844eabf4f5c5feae8a8ca3dc9c2e
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91826545"
---
# <a name="authentication-and-authorization-for-azure-static-web-apps-preview"></a>Uwierzytelnianie i autoryzacja dla usługi Azure Static Web Apps (wersja zapoznawcza)

Usługa Azure static Web Apps usprawnia proces uwierzytelniania, zarządzając uwierzytelnianiem przy użyciu następujących dostawców:

- Azure Active Directory
- GitHub
- Facebook
- Google<sup>1</sup>
- Twitter

[Zaproszenia](#invitations) specyficzne dla dostawcy kojarzą użytkowników z rolami, a autoryzowani użytkownicy uzyskują dostęp do [tras](routes.md) według reguł zdefiniowanych w _routes.js_ pliku.

Wszyscy dostawcy uwierzytelniania są domyślnie włączeni. Aby ograniczyć dostawcę uwierzytelniania, [Zablokuj dostęp](#block-an-authorization-provider) za pomocą niestandardowej reguły trasy.

Tematy dotyczące uwierzytelniania i autoryzacji znacząco nakładają się na koncepcje routingu. Zapoznaj się z [przewodnikiem routingu](routes.md) oraz z tym artykułem.

## <a name="roles"></a>Role

Każdy użytkownik, który uzyskuje dostęp do statycznej aplikacji sieci Web, należy do jednej lub kilku ról.  Istnieją dwie wbudowane role, do których użytkownicy mogą należeć:

- **anonimowe**: Wszyscy użytkownicy automatycznie należą do roli _anonimowej_ .
- **uwierzytelniono**: Wszyscy zalogowani użytkownicy należą do roli _uwierzytelnionej_ .

Poza wbudowanymi rolami można tworzyć nowe role, przypisywać je do użytkowników za pośrednictwem zaproszeń i odwoływać się do nich w _routes.js_ pliku.

## <a name="role-management"></a>Zarządzanie rolami

### <a name="add-a-user-to-a-role"></a>Dodawanie użytkownika do roli

Aby dodać użytkowników do witryny sieci Web, można generować zaproszenia, które umożliwiają kojarzenie użytkowników z określonymi rolami. Role są definiowane i obsługiwane w _routes.js_ pliku.

<a name="invitations" id="invitations"></a>

#### <a name="create-an-invitation"></a>Utwórz zaproszenie

Zaproszenia są specyficzne dla poszczególnych dostawców autoryzacji, dlatego należy wziąć pod uwagę potrzeby aplikacji podczas wybierania dostawców do obsługi. Niektórzy dostawcy ujawniają adresy e-mail użytkownika, a inne zapewniają tylko nazwę użytkownika witryny.

<a name="provider-user-details" id="provider-user-details"></a>

| Dostawca autoryzacji | Uwidacznia użytkownikowi  |
| ---------------------- | ----------------- |
| Azure Active Directory | Adres e-mail     |
| Facebook               | Adres e-mail     |
| GitHub                 | nazwa użytkownika          |
| Google<sup>1</sup>     | Adres e-mail     |
| Twitter                | nazwa użytkownika          |

1. Przejdź do statycznego zasobu Web Apps w [Azure Portal](https://portal.azure.com).
1. W obszarze _Ustawienia_kliknij pozycję **Zarządzanie rolami**.
1. Kliknij przycisk **Zaproś** .
1. Wybierz _dostawcę autoryzacji_ z listy opcji.
1. W polu _Zaproś szczegóły_ Dodaj nazwę użytkownika lub adres e-mail adresata.
    - W przypadku usługi GitHub i serwisu Twitter wprowadź nazwę użytkownika. Dla wszystkich innych użytkowników wprowadź adres e-mail adresata.
1. Z listy rozwijanej _domena_ wybierz domenę witryny statycznej.
    - Wybrana domena jest domeną widoczną w zaproszeniu. Jeśli masz domenę niestandardową skojarzoną z witryną, prawdopodobnie chcesz wybrać domenę niestandardową.
1. Dodaj rozdzieloną przecinkami listę nazw ról w polu _rola_ .
1. Wprowadź maksymalną liczbę godzin, przez jaką zaproszenie ma pozostawać ważne.
    - Maksymalny możliwy limit to 168 godzin, co wynosi 7 dni.
1. Kliknij przycisk **Generate** (Generuj).
1. Skopiuj link w polu _zapraszanie linku_ .
1. Wyślij wiadomość e-mail z zaproszeniem do osoby, której przyznano dostęp do aplikacji.

Gdy użytkownik kliknie link w zaproszeniu, zostanie wyświetlony monit o zalogowanie się przy użyciu odpowiedniego konta. Po pomyślnym zalogowaniu użytkownik jest skojarzony z wybranymi rolami.

> [!CAUTION]
> Upewnij się, że reguły trasy nie powodują konfliktu z wybranymi dostawcami uwierzytelniania. Zablokowanie dostawcy z regułą trasy uniemożliwi użytkownikom akceptowanie zaproszeń.

### <a name="update-role-assignments"></a>Aktualizowanie przypisań ról

1. Przejdź do statycznego zasobu Web Apps w [Azure Portal](https://portal.azure.com).
1. W obszarze _Ustawienia_kliknij pozycję **Zarządzanie rolami**.
1. Kliknij użytkownika na liście.
1. Edytuj listę ról w polu _rola_ .
1. Kliknij przycisk **Aktualizuj** .

### <a name="remove-user"></a>Usuwanie użytkownika

1. Przejdź do statycznego zasobu Web Apps w [Azure Portal](https://portal.azure.com).
1. W obszarze _Ustawienia_kliknij pozycję **Zarządzanie rolami**.
1. Znajdź użytkownika na liście.
1. Zaznacz pole wyboru w wierszu użytkownika.
1. Kliknij przycisk **Usuń**.

Podczas usuwania użytkownika należy wziąć pod uwagę następujące elementy:

1. Usunięcie użytkownika unieważnia swoje uprawnienia.
1. Propagacja na całym świecie może potrwać kilka minut.
1. Jeśli użytkownik zostanie dodany z powrotem do aplikacji, zmiany zostaną [ `userId` wprowadzone](user-information.md).

## <a name="remove-personal-identifying-information"></a>Usuń informacje o tożsamości osobistej

Po udzieleniu zgody na aplikację jako użytkownik końcowy aplikacja ma dostęp do Twojego adresu e-mail lub nazwy użytkownika w zależności od dostawcy tożsamości. Po udostępnieniu tych informacji właściciel aplikacji decyduje o sposobie zarządzania informacjami identyfikującymi dane osobowe.

Użytkownicy końcowi muszą kontaktować się z administratorami poszczególnych aplikacji sieci Web, aby odwołać te informacje z ich systemów.

Aby usunąć informacje identyfikujące dane osobowe z platformy Azure static Web Apps i uniemożliwić dostarczenie tych informacji przez platformę w przyszłych żądaniach, Prześlij żądanie przy użyciu adresu URL:

```url
https://identity.azurestaticapps.net/.auth/purge/<AUTHENTICATION_PROVIDER_NAME>
```

Aby zapobiec dostarczaniu przez platformę tych informacji na potrzeby przyszłych żądań do poszczególnych aplikacji, Prześlij żądanie do następującego adresu URL:

```url
https://<WEB_APP_DOMAIN_NAME>/.auth/purge/<AUTHENTICATION_PROVIDER_NAME>
```

## <a name="system-folder"></a>Folder systemowy

Usługa Azure static Web Apps używa `/.auth` folderu systemowego w celu zapewnienia dostępu do interfejsów API związanych z autoryzacją. Zamiast uwidaczniać jakiekolwiek trasy w `/.auth` folderze bezpośrednio użytkownikom końcowym, należy rozważyć utworzenie [reguł routingu](routes.md) w celu utworzenia przyjaznych adresów URL.

## <a name="login"></a>Zaloguj się

Skorzystaj z poniższej tabeli, aby znaleźć trasę logowania specyficzną dla dostawcy.

| Dostawca autoryzacji | Trasa logowania             |
| ---------------------- | ----------------------- |
| Azure Active Directory | `/.auth/login/aad`      |
| Facebook               | `/.auth/login/facebook` |
| GitHub                 | `/.auth/login/github`   |
| Google<sup>1</sup>     | `/.auth/login/google`   |
| Twitter                | `/.auth/login/twitter`  |

Na przykład, aby zalogować się za pomocą usługi GitHub, możesz dołączyć łącze logowania podobne do następującego fragmentu kodu:

```html
<a href="/.auth/login/github">Login</a>
```

Jeśli wybrano obsługę więcej niż jednego dostawcy, należy udostępnić link specyficzny dla dostawcy dla każdego z nich w witrynie sieci Web.

Możesz użyć [reguły trasy](routes.md) , aby zamapować domyślnego dostawcę na przyjazną trasę, taką jak _/login_.

```json
{
  "route": "/login",
  "serve": "/.auth/login/github"
}
```

### <a name="post-login-redirect"></a>Przekierowanie po zalogowaniu

Jeśli chcesz, aby użytkownik powrócił do określonej strony po zalogowaniu, podaj adres URL w `post_login_redirect_uri` parametrze ciągu zapytania.


## <a name="logout"></a>Logout

`/.auth/logout`Trasa rejestruje użytkowników z witryny sieci Web. Możesz dodać link do nawigacji w witrynie, aby zezwolić użytkownikowi na wylogowanie się, jak pokazano w poniższym przykładzie.

```html
<a href="/.auth/logout">Log out</a>
```

Możesz użyć [reguły trasy](routes.md) do mapowania przyjaznej trasy, takiej jak _/Logout_.

```json
{
  "route": "/logout",
  "serve": "/.auth/logout"
}
```

### <a name="post-logout-redirect"></a>Przekierowanie po wylogowaniu

Jeśli chcesz, aby użytkownik powrócił do określonej strony po wylogowaniu, podaj adres URL w `post_logout_redirect_uri` parametrze ciągu zapytania.

## <a name="block-an-authorization-provider"></a>Blokowanie dostawcy autoryzacji

Możesz chcieć ograniczyć swoją aplikację do używania dostawcy autoryzacji. Na przykład aplikacja może chcieć znormalizować tylko na [dostawców, którzy uwidaczniają adresy e-mail](#provider-user-details).

Aby zablokować dostawcę, można utworzyć [reguły trasy](routes.md) zwracające 404 dla żądań do zablokowanej trasy specyficznej dla dostawcy. Na przykład aby ograniczyć dostęp do usługi Twitter jako dostawcę, Dodaj następującą regułę trasy.

```json
{
  "route": "/.auth/login/twitter",
  "statusCode": "404"
}
```

## <a name="restrictions"></a>Ograniczenia

Zapoznaj się z [artykułem](quotas.md) dotyczącym przydziału ogólnych ograniczeń i ograniczeń.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dostęp do danych uwierzytelniania i autoryzacji użytkowników](user-information.md)

<sup>1</sup> oczekiwanie na certyfikat zewnętrzny.
