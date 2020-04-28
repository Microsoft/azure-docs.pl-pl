---
title: Nowy przewodnik szkoleniowy na temat rejestracji aplikacji w witrynie Azure Portal
description: Wprowadzenie do nowego środowiska rejestracji aplikacji Azure Portal
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: marsma
ms.reviewer: lenalepa, keyam
ms.custom: aaddev
ms.openlocfilehash: a437d54dac50be7ddaad899a1cf0a3e93aade8f5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80154580"
---
# <a name="new-azure-portal-app-registration-training-guide"></a>Nowy przewodnik szkoleniowy na temat rejestracji aplikacji w witrynie Azure Portal

Wiele ulepszeń można znaleźć w [Rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) Azure Portal. Jeśli znasz już środowisko Rejestracje aplikacji (starsze) w Azure Portal, Skorzystaj z tego przewodnika szkoleniowego, aby rozpocząć korzystanie z nowego środowiska.

W Azure Active Directory nowe środowisko rejestracji aplikacji opisane w tym miejscu jest ogólnie dostępne (GA). W Azure Active Directory B2C (Azure AD B2C) to środowisko jest w wersji zapoznawczej.

## <a name="key-changes"></a>Zmiany kluczy

- Rejestracje aplikacji nie są ograniczone do *aplikacji sieci Web ani interfejsu API* ani aplikacji *natywnych* . Możesz użyć tej samej rejestracji aplikacji dla wszystkich tych aplikacji, rejestrując odpowiednie identyfikatory URI przekierowania.

- W starszych wersjach obsługiwane aplikacje, które logują się przy użyciu konta organizacji (Azure AD). Aplikacje zostały zarejestrowane jako pojedyncze dzierżawy. Aplikacje obsługują tylko konta organizacji z katalogu, w którym zarejestrowano aplikację. Aplikacje można modyfikować w taki sposób, aby obsługiwały wiele dzierżawców i obsługiwać wszystkie konta organizacji. Nowe środowisko pozwala zarejestrować aplikacje, które mogą obsługiwać obie te opcje, a także trzecią opcję: wszystkie konta organizacji, a także osobiste konta Microsoft.

- Starsze środowisko było dostępne tylko po zalogowaniu się do Azure Portal przy użyciu konta organizacyjnego. Korzystając z nowego środowiska, możesz używać osobistych kont Microsoft, które nie są skojarzone z katalogiem.

## <a name="list-of-applications"></a>Lista aplikacji

Nowa lista aplikacji zawiera aplikacje, które zostały zarejestrowane za pomocą środowiska rejestracji starszej aplikacji w Azure Portal. Te aplikacje logują się przy użyciu kont usługi Azure AD. Nowa lista aplikacji zawiera również aplikacje zarejestrowane za pośrednictwem portalu rejestracji aplikacji. Te aplikacje logują się przy użyciu usługi Azure AD i osobistych kont Microsoft.

>[!NOTE]
>Portal rejestracji aplikacji został uznany za przestarzały.

Nowa lista aplikacji nie ma kolumny **typu aplikacji** , ponieważ rejestracja pojedynczej aplikacji może być kilku typów. Lista zawiera dwie dodatkowe kolumny: **utworzone** i **Certyfikaty & wpisy tajne**. **Certyfikaty & wpisy tajne** pokazują stan poświadczeń, które zostały zarejestrowane w aplikacji. Stany obejmują **bieżące**, **wygasają wkrótce**i **utraciły ważność**.

## <a name="new-app-registration"></a>Rejestrowanie nowej aplikacji

W starszej wersji, aby zarejestrować aplikację, należy podać: **nazwę**, **Typ aplikacji**i **adres URL logowania/identyfikator URI przekierowania**. Utworzone aplikacje dotyczyły tylko aplikacji z jedną dzierżawą. Są one obsługiwane tylko przez konta organizacji z katalogu, w którym zarejestrowano aplikację.

W nowym środowisku należy podać **nazwę** aplikacji i wybrać **obsługiwane typy kont**. Opcjonalnie możesz podać **Identyfikator URI przekierowania**. Jeśli podasz identyfikator URI przekierowania, musisz określić, czy jest on w sieci Web, czy publicznej (Mobile and Desktop). Aby uzyskać więcej informacji, zobacz [Szybki Start: rejestrowanie aplikacji na platformie tożsamości firmy Microsoft](quickstart-register-app.md). Aby uzyskać Azure AD B2C, zobacz [Rejestrowanie aplikacji w programie Azure Active Directory B2C](../../active-directory-b2c/tutorial-register-applications.md).

## <a name="differences-between-the-application-registration-portal-and-app-registrations-page"></a>Różnice między portalem rejestracji aplikacji a stroną Rejestracje aplikacji

### <a name="the-legacy-properties-page"></a>Strona właściwości starszej wersji

Starsze środowisko miało stronę **Właściwości** . **Właściwości** miały następujące pola:

- **Nazwa**
- **Identyfikator obiektu**
- **Identyfikator aplikacji**
- **Identyfikator URI identyfikatora aplikacji**
- **Logo**
- **Adres URL strony głównej**
- **Adres URL wylogowywania**
- **Adres URL warunków użytkowania**
- **Adres URL zasad zachowania poufności informacji**
- **Typ aplikacji**
- **Wiele dzierżawców**

Nowe środowisko nie ma tej strony. Tutaj można znaleźć funkcje równoważne:

- **Nazwa**, **logo**, **adres URL strony głównej**, **adres URL warunków**użytkowania i **adres URL zasad zachowania poufności informacji** znajdują się teraz na stronie **znakowania** aplikacji.
- Identyfikator **obiektu** i **Identyfikator aplikacji (klienta)** znajdują się na stronie **Przegląd** .
- Funkcje kontrolowane przez przełącznik z **wieloma dzierżawcami** w starszym środowisku zostały zastąpione przez **obsługiwane typy kont** na stronie **uwierzytelnianie** . Aby uzyskać więcej informacji, zobacz [Szybki Start: modyfikowanie kont obsługiwanych przez aplikację](quickstart-modify-supported-accounts.md).
- **Adres URL wylogowywania** znajduje się teraz na stronie **uwierzytelnianie** .
- **Typ aplikacji** nie jest już prawidłowym polem. Zamiast tego identyfikatory URI przekierowania, które można znaleźć na stronie **uwierzytelnianie** , określają, które typy aplikacji są obsługiwane.
- **Identyfikator URI aplikacji** jest teraz nazywany identyfikatorem **URI identyfikatora aplikacji** i można go znaleźć na **uwidocznieniu interfejsu API**. W starszych wersjach ta właściwość została zarejestrowana jako autorejestrowanie przy użyciu następującego formatu `https://{tenantdomain}/{appID}`:, na przykład `https://microsoft.onmicrosoft.com/492439af-3282-44c3-b297-45463339544b`. W nowym środowisku jest automatycznie generowane jako `api://{appID}`, ale należy je jawnie zapisać. W Azure AD B2C dzierżawy nadal jest `https://{tenantdomain}/{appID}` używany format.

### <a name="reply-urlsredirect-urls"></a>Adresy URL odpowiedzi/adresy URL przekierowań

W starszej wersji aplikacja zawierała **adres URL odpowiedzi** . W nowym środowisku adresy URL odpowiedzi można znaleźć na stronie **uwierzytelniania** aplikacji. Są teraz nazywane identyfikatorami **URI przekierowania**.

Format identyfikatorów URI przekierowania został zmieniony. Są one wymagane do skojarzenia z typem aplikacji, Internet lub public. Ze względów bezpieczeństwa nie są obsługiwane `http://` symbole wieloznaczne i schematy, *http://localhost*z wyjątkiem.

### <a name="keyscertificates--secrets"></a>Klucze/certyfikaty & wpisy tajne

W starszym środowisku aplikacja zawierała **klucze** na stronie. W nowym środowisku została zmieniona nazwa na **certificates &** Secret.

**Klucze publiczne** są teraz nazywane **certyfikatami**. **Hasła** są teraz określane jako wpisy **tajne klienta**.

### <a name="required-permissionsapi-permissions"></a>Wymagane uprawnienia/uprawnienia interfejsu API

W starszym środowisku aplikacja miała **wymaganą stronę uprawnień** . W nowym środowisku zmieniono nazwę na **uprawnienia interfejsu API**.

Po wybraniu interfejsu API w starszym środowisku można wybrać jedną z małych list interfejsów API firmy Microsoft. Można również przeszukiwać nazwy główne usług w dzierżawie. W nowym środowisku można wybierać spośród wielu kart: **interfejsów API firmy Microsoft**, **interfejsów API używanych przez moją organizację**lub **moich interfejsów API**. Pasek wyszukiwania w obszarze **interfejsy API Moja organizacja** używa wyszukiwania na kartach za pośrednictwem jednostek usługi w dzierżawie.

> [!NOTE]
> Ta karta nie zostanie wyświetlona, jeśli aplikacja nie jest skojarzona z dzierżawcą. Aby uzyskać więcej informacji na temat żądania uprawnień, zobacz [Szybki Start: Konfigurowanie aplikacji klienckiej w celu uzyskiwania dostępu do interfejsów API sieci Web](quickstart-configure-app-access-web-apis.md).

Starsze środowisko ma przycisk **Udziel uprawnień** w górnej części strony **żądane uprawnienia** . W nowym środowisku Strona zgody na **udzielanie** uprawnień ma przycisk **Udziel zgody administratora** w sekcji **uprawnienia interfejsu API** aplikacji. Istnieją także pewne różnice w sposobach funkcji przycisków.

W starszym środowisku logika jest zróżnicowana w zależności od zalogowanego użytkownika i żądanych uprawnień. Logika:

- Jeśli zażądano tylko uprawnień użytkownika, a zalogowany użytkownik nie jest administratorem, użytkownik może udzielić zgody użytkownika na wymagane uprawnienia.
- Jeśli zażądano co najmniej jednego uprawnienia, które wymaga zgody administratora, a zalogowany użytkownik nie jest administratorem, wystąpił błąd podczas próby udzielenia zgody.
- Jeśli zalogowany użytkownik był administratorem, przyznano zgodę administratora na wszystkie żądane uprawnienia.

W nowym środowisku tylko administrator może udzielić zgody. Gdy administrator wybierze pozycję **Udziel zgody administratora**, zgoda administratora zostanie udzielona na wszystkie żądane uprawnienia.

## <a name="deleting-an-app-registration"></a>Usuwanie rejestracji aplikacji

W starszym środowisku można usunąć tylko aplikacje z jedną dzierżawą. Przycisk Usuń został wyłączony dla aplikacji wielodostępnych. W nowym środowisku można usuwać aplikacje w dowolnym stanie, ale należy potwierdzić akcję. Aby uzyskać więcej informacji, zobacz [Szybki Start: Usuwanie aplikacji zarejestrowanej za pomocą platformy tożsamości firmy Microsoft](quickstart-remove-app.md).

## <a name="application-manifest"></a>Manifest aplikacji

Starsze i nowe środowiska używają różne wersje formatu JSON w edytorze manifestu. Aby uzyskać więcej informacji, zobacz [Azure Active Directory manifest aplikacji](reference-app-manifest.md).

## <a name="new-ui"></a>Nowy interfejs użytkownika

Nowe środowisko dodaje kontrolki interfejsu użytkownika dla następujących właściwości:

- Na stronie **uwierzytelnianie** występuje **niejawny przepływ** (`oauth2AllowImplicitFlow`). W przeciwieństwie do starszych wersji środowiska, można włączyć **tokeny dostępu** lub **tokeny identyfikatora**lub oba te elementy.
- Strona **Uwidacznianie interfejsu API** zawiera **zakresy zdefiniowane przez ten interfejs API** (`oauth2Permissions`) i **autoryzowane aplikacje klienckie** (`preAuthorizedApplications`). Aby uzyskać więcej informacji na temat konfigurowania aplikacji jako interfejsu API sieci Web i uwidaczniania uprawnień/zakresów, zobacz [Szybki Start: Konfigurowanie aplikacji do udostępniania interfejsów API sieci Web](quickstart-configure-app-expose-web-apis.md).
- Strona **znakowania** zawiera **domenę wydawcy**. Domena wydawcy jest wyświetlana użytkownikom z [monitem o zgodę aplikacji](application-consent-experience.md). Aby uzyskać więcej informacji, zobacz [How to: Configure a Application Publisher Domain](howto-configure-publisher-domain.md).

## <a name="limitations"></a>Ograniczenia

Nowe środowisko ma następujące ograniczenia:

- Format wpisów tajnych klienta (hasła aplikacji) różni się od starszego środowiska i może spowodować przerwanie interfejsu wiersza polecenia.
- Zmiana wartości obsługiwanych kont nie jest obsługiwana w interfejsie użytkownika. Musisz używać manifestu aplikacji, chyba że przełączasz się między jedną dzierżawą i wieloma dzierżawcami usługi Azure AD.
