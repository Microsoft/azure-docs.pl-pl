---
title: Przepływy użytkownika w Azure Active Directory B2C | Microsoft Docs
description: Dowiedz się więcej o rozszerzalnym środowisku zasad Azure Active Directory B2C i sposobach tworzenia różnych przepływów użytkowników.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c11bc48742c398d2048a236c7d00af044971f845
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78185614"
---
# <a name="user-flows-in-azure-active-directory-b2c"></a>Przepływy użytkownika w Azure Active Directory B2C

Rozszerzalna struktura zasad Azure Active Directory B2C (Azure AD B2C) jest podstawową siłą usługi. Zasady w pełni opisują środowiska tożsamości, takie jak rejestrowanie, logowanie lub edytowanie profilów. Aby ułatwić skonfigurowanie najczęstszych zadań związanych z tożsamościami, Portal Azure AD B2C obejmuje wstępnie zdefiniowane, konfigurowalne zasady o nazwie **przepływy użytkownika**.

## <a name="what-are-user-flows"></a>Co to są przepływy użytkowników?

Przepływ użytkownika umożliwia kontrolowanie zachowań w aplikacjach przez skonfigurowanie następujących ustawień:

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

W Azure Portal nowe [wersje przepływów użytkowników](user-flow-versions.md) są dodawane przez cały czas. Po rozpoczęciu pracy z Azure AD B2C przetestowane przepływy użytkowników są zalecane do użycia. Podczas tworzenia nowego przepływu użytkownika należy wybrać wymagany przepływ użytkownika z karty **zalecane** .

Obecnie zalecane są następujące przepływy użytkowników:

- **Rejestracja i logowanie** — obsługuje zarówno środowisko rejestracji, jak i logowania przy użyciu jednej konfiguracji. Użytkownicy są w stanie wyprowadzić właściwą ścieżkę w zależności od kontekstu. Zaleca się użycie tego przepływu użytkownika w przepływie użytkownika podczas **rejestrowania** lub w przepływie użytkownika **logowania** .
- **Edytowanie profilów** — umożliwia użytkownikom edytowanie informacji o profilu.
- **Resetowanie hasła** — pozwala określić, czy i jak użytkownicy mogą resetować swoje hasła.

## <a name="linking-user-flows"></a>Łączenie przepływów użytkowników

Przepływ użytkownika **rejestracji lub logowania** z kontami lokalnymi zawiera link **zapomniane hasło?** na pierwszej stronie środowiska. Kliknięcie tego linku nie powoduje automatycznego wyzwolenia przepływu użytkownika resetowania hasła.

Zamiast tego kod `AADB2C90118` błędu jest zwracany do aplikacji. Aplikacja musi obsłużyć ten kod błędu przez uruchomienie określonego przepływu użytkownika, który resetuje hasło. Aby zobaczyć przykład, zapoznaj się z [prostym przykładem ASP.NET](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI) , który pokazuje łączenie przepływów użytkowników.

## <a name="email-address-storage"></a>Magazyn adresów e-mail

W ramach przepływu użytkownika może być wymagany adres e-mail. Jeśli użytkownik jest uwierzytelniany przy użyciu dostawcy tożsamości społecznościowej, adres e-mail jest przechowywany we właściwości **otherMails** . Jeśli konto lokalne jest oparte na nazwie użytkownika, adres e-mail jest przechowywany we właściwości szczegóły silnego uwierzytelniania. Jeśli konto lokalne jest oparte na adresie e-mail, adres e-mail jest przechowywany we właściwości **signInNames** .

W żadnym z tych przypadków nie gwarantuje się zweryfikowania adresu e-mail. Administrator dzierżawy może wyłączyć weryfikację wiadomości e-mail w podstawowych zasadach dla kont lokalnych. Nawet jeśli Weryfikacja adresu e-mail jest włączona, adresy nie są weryfikowane, jeśli pochodzą od dostawcy tożsamości społecznościowej i nie zostały zmienione.

Tylko właściwości **otherMails** i **signInNames** są udostępniane za pomocą interfejsu API Microsoft Graph. Adres e-mail we właściwości szczegółu silnego uwierzytelniania jest niedostępny.

## <a name="next-steps"></a>Następne kroki

Aby utworzyć zalecane przepływy użytkowników, postępuj zgodnie z instrukcjami podanymi w [samouczku: tworzenie przepływu użytkownika](tutorial-create-user-flows.md).
