---
title: Obsługiwane Microsoft Graph operacje
titleSuffix: Azure AD B2C
description: Indeks operacji Microsoft Graph obsługiwanych przez zarządzanie zasobami Azure AD B2C, w tym użytkownikami, przepływami użytkowników, dostawcami tożsamości, zasadami niestandardowymi, kluczami zasad i innymi.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d8d898d3825fa40cbfd13337067c24cf14f9a544
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92102021"
---
# <a name="microsoft-graph-operations-available-for-azure-ad-b2c"></a>Microsoft Graph operacji dostępnych dla Azure AD B2C

Następujące Microsoft Graph operacje interfejsu API są obsługiwane na potrzeby zarządzania zasobami Azure AD B2C, w tym użytkownikami, dostawcami tożsamości, przepływami użytkowników, zasadami niestandardowymi i kluczami zasad.

Każdy link w poniższych sekcjach odwołuje się do odpowiedniej strony w ramach odwołania do interfejsu API Microsoft Graph dla tej operacji.

## <a name="user-management"></a>Zarządzanie użytkownikami

- [Wyświetlanie użytkowników](https://docs.microsoft.com/graph/api/user-list)
- [Tworzenie użytkownika odbiorcy](https://docs.microsoft.com/graph/api/user-post-users)
- [Pobierz użytkownika](https://docs.microsoft.com/graph/api/user-get)
- [Aktualizowanie użytkownika](https://docs.microsoft.com/graph/api/user-update)
- [Usuwanie użytkownika](https://docs.microsoft.com/graph/api/user-delete)

Aby uzyskać więcej informacji na temat zarządzania kontami użytkowników Azure AD B2C przy użyciu interfejsu API Microsoft Graph, zobacz [Azure AD B2C Zarządzanie kontami użytkowników przy użyciu Microsoft Graph](manage-user-accounts-graph-api.md).

## <a name="user-phone-number-management"></a>Zarządzanie numerami telefonów użytkowników

- [Dodaj](https://docs.microsoft.com/graph/api/authentication-post-phonemethods)
- [Pobierz](https://docs.microsoft.com/graph/api/b2cauthenticationmethodspolicy-get)
- [Aktualizowanie](https://docs.microsoft.com/graph/api/b2cauthenticationmethodspolicy-update)
- [Usuwanie](https://docs.microsoft.com/graph/api/phoneauthenticationmethod-delete)

Aby uzyskać więcej informacji na temat zarządzania numerem telefonu użytkownika przy użyciu interfejsu API Microsoft Graph, zobacz [metody uwierzytelniania B2C](https://docs.microsoft.com/graph/api/resources/b2cauthenticationmethodspolicy).

## <a name="identity-providers-user-flow"></a>Dostawcy tożsamości (przepływ użytkownika)

Zarządzaj dostawcami tożsamości dostępnymi dla przepływów użytkowników w dzierżawie Azure AD B2C.

- [Wyświetlanie listy dostawców tożsamości zarejestrowanych w dzierżawie Azure AD B2C](https://docs.microsoft.com/graph/api/identityprovider-list)
- [Tworzenie dostawcy tożsamości](https://docs.microsoft.com/graph/api/identityprovider-post-identityproviders)
- [Uzyskiwanie dostawcy tożsamości](https://docs.microsoft.com/graph/api/identityprovider-get)
- [Aktualizowanie dostawcy tożsamości](https://docs.microsoft.com/graph/api/identityprovider-update)
- [Usuwanie dostawcy tożsamości](https://docs.microsoft.com/graph/api/identityprovider-delete)

## <a name="user-flow"></a>Przepływ użytkownika

Skonfiguruj wstępnie skompilowane zasady rejestracji, logowania, połączonego rejestrowania i logowania, resetowania haseł i aktualizacji profilu.

- [Wyświetlanie listy przepływów użytkownika](https://docs.microsoft.com/graph/api/identityuserflow-list)
- [Tworzenie przepływu użytkownika](https://docs.microsoft.com/graph/api/identityuserflow-post-userflows)
- [Pobierz przepływ użytkownika](https://docs.microsoft.com/graph/api/identityuserflow-get)
- [Usuwanie przepływu użytkownika](https://docs.microsoft.com/graph/api/identityuserflow-delete)

## <a name="custom-policies"></a>Zasady niestandardowe

Poniższe operacje umożliwiają zarządzanie Azure AD B2C zasadami struktury zaufania, znanymi jako [zasady niestandardowe](custom-policy-overview.md).

- [Wyświetl listę wszystkich zasad struktury zaufania skonfigurowanych w dzierżawie](https://docs.microsoft.com/graph/api/trustframework-list-trustframeworkpolicies)
- [Utwórz zasady dotyczące struktury zaufania](https://docs.microsoft.com/graph/api/trustframework-post-trustframeworkpolicy)
- [Odczytaj właściwości istniejących zasad struktury zaufania](https://docs.microsoft.com/graph/api/trustframeworkpolicy-get)
- [Aktualizowanie lub tworzenie zasad struktury zaufania.](https://docs.microsoft.com/graph/api/trustframework-put-trustframeworkpolicy)
- [Usuwanie istniejących zasad struktury zaufania](https://docs.microsoft.com/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>Klucze zasad

Struktura środowiska tożsamości przechowuje wpisy tajne, do których odwołuje się zasada niestandardowa, aby ustanowić relację zaufania między składnikami. Te klucze tajne mogą być symetrycznymi lub asymetryczne klucze/wartości. W Azure Portal te jednostki są wyświetlane jako **klucze zasad**.

Zasób najwyższego poziomu dla kluczy zasad w interfejsie API Microsoft Graph jest [zaufanym zestawem kluczy](https://docs.microsoft.com/graph/api/resources/trustframeworkkeyset). Każdy **zestaw kluczy** zawiera co najmniej jeden **klucz**. Aby utworzyć klucz, najpierw utwórz pusty zestaw kluczy, a następnie Wygeneruj klucz w zestawie kluczy. Można utworzyć Ręczny klucz tajny, przekazać certyfikat lub klucz PKCS12. Klucz może być wygenerowanym wpisem tajnym, definiowanym przez Ciebie ciągiem (na przykład wpisem tajnym aplikacji w serwisie Facebook) lub przekazywanym certyfikatem. Jeśli zestaw kluczy ma wiele kluczy, tylko jeden z kluczy jest aktywny.

### <a name="trust-framework-policy-keyset"></a>Zestaw kluczy zasad zaufania platformy

- [Wyświetl listę zestawów ustawień struktury zaufania](https://docs.microsoft.com/graph/api/trustframework-list-keysets)
- [Tworzenie zestawów narzędzi struktury zaufania](https://docs.microsoft.com/graph/api/trustframework-post-keysets)
- [Pobierz zestaw kluczy](https://docs.microsoft.com/graph/api/trustframeworkkeyset-get)
- [Aktualizowanie zestawów danych struktury zaufania](https://docs.microsoft.com/graph/api/trustframeworkkeyset-update)
- [Usuwanie zestawów danych struktury zaufania](https://docs.microsoft.com/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>Klucz zasad zaufania platformy

- [Pobierz obecnie aktywny klucz w zestawie kluczy](https://docs.microsoft.com/graph/api/trustframeworkkeyset-getactivekey)
- [Generowanie klucza w zestawie kluczy](https://docs.microsoft.com/graph/api/trustframeworkkeyset-generatekey)
- [Przekazywanie klucza tajnego opartego na ciągu](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadsecret)
- [Przekaż certyfikat X. 509](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadcertificate)
- [Przekaż certyfikat formatu PKCS12](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>Aplikacje

- [Wyświetlanie listy aplikacji](https://docs.microsoft.com/graph/api/application-list)
- [Tworzenie aplikacji](https://docs.microsoft.com/graph/api/resources/application)
- [Aktualizuj aplikację](https://docs.microsoft.com/graph/api/application-update)
- [Utwórz obiekt serviceprincipal](https://docs.microsoft.com/graph/api/resources/serviceprincipal)
- [Utwórz przyznanie oauth2Permission](https://docs.microsoft.com/graph/api/resources/oauth2permissiongrant)
- [Usuń aplikację](https://docs.microsoft.com/graph/api/application-delete)

## <a name="application-extension-properties"></a>Właściwości rozszerzenia aplikacji

- [Wyświetl właściwości rozszerzenia](https://docs.microsoft.com/graph/api/application-list-extensionproperty)

Azure AD B2C udostępnia katalog, który może zawierać 100 atrybutów niestandardowych na użytkownika. W przypadku przepływów użytkowników te właściwości rozszerzenia są [zarządzane przy użyciu Azure Portal](custom-policy-custom-attributes.md). W przypadku zasad niestandardowych Azure AD B2C tworzy dla Ciebie właściwość, podczas gdy zasady zapisują wartość do właściwości rozszerzenia.

## <a name="audit-logs"></a>Dzienniki inspekcji

- [Wyświetl listę dzienników inspekcji](https://docs.microsoft.com/graph/api/directoryaudit-list)

Aby uzyskać więcej informacji na temat uzyskiwania dostępu do dzienników inspekcji Azure AD B2C za pomocą interfejsu API Microsoft Graph, zobacz [dostęp do dzienników inspekcji Azure AD B2C](view-audit-logs.md).
