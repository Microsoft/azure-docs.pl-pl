---
title: Przepływy użytkownika w Azure Active Directory B2C | Microsoft Docs
titleSuffix: Azure AD B2C
description: Dowiedz się więcej o rozszerzalnym środowisku zasad Azure Active Directory B2C i sposobach tworzenia różnych przepływów użytkowników.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/30/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7a7736602fafb740d1d76fa09fd26da25e4ff9f5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87481601"
---
# <a name="user-flows-in-azure-active-directory-b2c"></a>Przepływy użytkownika w Azure Active Directory B2C

Aby ułatwić konfigurowanie typowych zadań związanych z tożsamościami dla aplikacji, Portal Azure AD B2C obejmuje wstępnie zdefiniowane, konfigurowalne zasady o nazwie **przepływy użytkownika**. Przepływ użytkownika umożliwia określenie sposobu, w jaki użytkownicy korzystają z aplikacji, gdy wykonują takie czynności jak logowanie, tworzenie konta, edytowanie profilu lub Resetowanie hasła. W przypadku przepływów użytkowników można kontrolować następujące możliwości:

- Typy kont używane do logowania, takie jak konta społecznościowe, takie jak konta w serwisie Facebook lub konto lokalne
- Atrybuty, które mają być zbierane od konsumenta, takie jak imię i nazwisko, kod pocztowy i rozmiar butów
- Azure Multi-Factor Authentication
- Dostosowanie interfejsu użytkownika
- Informacje odbierane przez aplikację jako oświadczenia w tokenie

W razie konieczności można utworzyć wiele przepływów użytkowników różnych typów w dzierżawie i używać ich w aplikacjach. Przepływy użytkowników mogą być ponownie używane między aplikacjami. Ta elastyczność umożliwia definiowanie i modyfikowanie środowisk tożsamości przy minimalnych lub niewielkich zmianach w kodzie. Aplikacja wyzwala przepływ użytkownika przy użyciu standardowego żądania uwierzytelniania HTTP, zawierającego parametr przepływu użytkownika. Jako odpowiedź otrzymano dostosowany [token](tokens-overview.md) .

W poniższych przykładach pokazano parametr ciągu zapytania "p", który określa przepływ użytkownika do użycia:

```
https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up user flow
```

```
https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in user flow
```

## <a name="user-flow-versions"></a>Wersje przepływu użytkownika

Azure AD B2C obejmuje kilka typów przepływów użytkownika:

- **Rejestracja i logowanie** — obsługuje zarówno środowisko rejestracji, jak i logowania przy użyciu jednej konfiguracji. Użytkownicy są w stanie wyprowadzić właściwą ścieżkę w zależności od kontekstu. Uwzględniono również oddzielne przepływy **rejestracji** lub **użytkowników logujących się.** Zwykle zalecamy łączenie się z przepływem użytkowników i logowaniem.
- **Edytowanie profilów** — umożliwia użytkownikom edytowanie informacji o profilu.
- **Resetowanie hasła** — pozwala określić, czy i jak użytkownicy mogą resetować swoje hasła.

Większość typów przepływów użytkownika ma zarówno **zalecaną** wersję, jak i wersję **standardową** . Aby uzyskać szczegółowe informacje, zobacz [wersje przepływu użytkownika](user-flow-versions.md).

> [!IMPORTANT]
> Jeśli pracujesz z przepływami użytkowników w Azure AD B2C wcześniej, zobaczysz, że został zmieniony sposób odniesienia do wersji przepływu użytkownika. Wcześniej oferujemy wersje V1 (produkcyjne gotowe) i wersje 1.1 i v2 (wersja zapoznawcza). Teraz zostały skonsolidowane przepływy użytkowników w dwie wersje:
>
>- **Zalecane** przepływy użytkowników to nowe wersje zapoznawcze przepływów użytkowników. Są one dokładnie przetestowane i łączą wszystkie funkcje starszych wersji **v2** i **1.1** . W przód nowe zalecane przepływy użytkowników zostaną utrzymane i zaktualizowane. Po przejściu do tych nowych zalecanych przepływów użytkowników będziesz mieć dostęp do nowych funkcji po ich udostępnieniu.
>- **Standardowe** przepływy użytkowników, znane wcześniej jako **V1**, są ogólnie dostępne, gotowe do użycia w środowisku produkcyjnym. Jeśli przepływ użytkownika jest krytyczny i zależy od wysoce stabilnych wersji, można nadal używać standardowych przepływów użytkowników, w przypadku których te wersje nie będą przechowywane i aktualizowane.
>
>Wszystkie starsze przepływy użytkowników w wersji zapoznawczej (wersja 1.1 i v2) znajdują się na ścieżce do wycofania od **1 sierpnia 2021**. O ile to możliwe, zdecydowanie zalecamy [przełączenie do nowych **zalecanych** przepływów użytkownika](user-flow-versions.md#how-to-switch-to-a-new-recommended-user-flow) najszybciej, jak to możliwe, aby zawsze korzystać z najnowszych funkcji i aktualizacji.

## <a name="linking-user-flows"></a>Łączenie przepływów użytkowników

Przepływ użytkownika **rejestracji lub logowania** z kontami lokalnymi zawiera link **zapomniane hasło?** na pierwszej stronie środowiska. Kliknięcie tego linku nie powoduje automatycznego wyzwolenia przepływu użytkownika resetowania hasła.

Zamiast tego kod błędu `AADB2C90118` jest zwracany do aplikacji. Aplikacja musi obsłużyć ten kod błędu przez uruchomienie określonego przepływu użytkownika, który resetuje hasło. Aby zobaczyć przykład, zapoznaj się z [prostym przykładem ASP.NET](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI) , który pokazuje łączenie przepływów użytkowników.

## <a name="email-address-storage"></a>Magazyn adresów e-mail

W ramach przepływu użytkownika może być wymagany adres e-mail. Jeśli użytkownik jest uwierzytelniany przy użyciu dostawcy tożsamości społecznościowej, adres e-mail jest przechowywany we właściwości **otherMails** . Jeśli konto lokalne jest oparte na nazwie użytkownika, adres e-mail jest przechowywany we właściwości szczegóły silnego uwierzytelniania. Jeśli konto lokalne jest oparte na adresie e-mail, adres e-mail jest przechowywany we właściwości **signInNames** .

W żadnym z tych przypadków nie gwarantuje się zweryfikowania adresu e-mail. Administrator dzierżawy może wyłączyć weryfikację wiadomości e-mail w podstawowych zasadach dla kont lokalnych. Nawet jeśli Weryfikacja adresu e-mail jest włączona, adresy nie są weryfikowane, jeśli pochodzą od dostawcy tożsamości społecznościowej i nie zostały zmienione.

Tylko właściwości **otherMails** i **signInNames** są udostępniane za pomocą interfejsu API Microsoft Graph. Adres e-mail we właściwości szczegółu silnego uwierzytelniania jest niedostępny.

## <a name="next-steps"></a>Następne kroki

Aby utworzyć zalecane przepływy użytkowników, postępuj zgodnie z instrukcjami podanymi w [samouczku: tworzenie przepływu użytkownika](tutorial-create-user-flows.md).
