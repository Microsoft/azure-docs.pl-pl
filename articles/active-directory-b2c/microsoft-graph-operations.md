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
ms.openlocfilehash: e4463b932c95e224981b74093effbb355de24f6b
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97509822"
---
# <a name="microsoft-graph-operations-available-for-azure-ad-b2c"></a>Microsoft Graph operacji dostępnych dla Azure AD B2C

Następujące Microsoft Graph operacje interfejsu API są obsługiwane na potrzeby zarządzania zasobami Azure AD B2C, w tym użytkownikami, dostawcami tożsamości, przepływami użytkowników, zasadami niestandardowymi i kluczami zasad.

Każdy link w poniższych sekcjach odwołuje się do odpowiedniej strony w ramach odwołania do interfejsu API Microsoft Graph dla tej operacji.

## <a name="user-management"></a>Zarządzanie użytkownikami

- [Wyświetlanie użytkowników](/graph/api/user-list)
- [Tworzenie użytkownika odbiorcy](/graph/api/user-post-users)
- [Pobierz użytkownika](/graph/api/user-get)
- [Aktualizowanie użytkownika](/graph/api/user-update)
- [Usuwanie użytkownika](/graph/api/user-delete)

Aby uzyskać więcej informacji na temat zarządzania kontami użytkowników Azure AD B2C przy użyciu interfejsu API Microsoft Graph, zobacz [Azure AD B2C Zarządzanie kontami użytkowników przy użyciu Microsoft Graph](manage-user-accounts-graph-api.md).

## <a name="user-phone-number-management"></a>Zarządzanie numerami telefonów użytkowników

- [Dodaj](/graph/api/authentication-post-phonemethods)
- [Pobierz](/graph/api/b2cauthenticationmethodspolicy-get)
- [Aktualizowanie](/graph/api/b2cauthenticationmethodspolicy-update)
- [Usuwanie](/graph/api/phoneauthenticationmethod-delete)

Aby uzyskać więcej informacji na temat zarządzania numerem telefonu użytkownika przy użyciu interfejsu API Microsoft Graph, zobacz [metody uwierzytelniania B2C](/graph/api/resources/b2cauthenticationmethodspolicy).

## <a name="identity-providers-user-flow"></a>Dostawcy tożsamości (przepływ użytkownika)

Zarządzaj dostawcami tożsamości dostępnymi dla przepływów użytkowników w dzierżawie Azure AD B2C.

- [Wyświetlanie listy dostawców tożsamości zarejestrowanych w dzierżawie Azure AD B2C](/graph/api/identityprovider-list)
- [Tworzenie dostawcy tożsamości](/graph/api/identityprovider-post-identityproviders)
- [Uzyskiwanie dostawcy tożsamości](/graph/api/identityprovider-get)
- [Aktualizowanie dostawcy tożsamości](/graph/api/identityprovider-update)
- [Usuwanie dostawcy tożsamości](/graph/api/identityprovider-delete)

## <a name="user-flow"></a>Przepływ użytkownika

Skonfiguruj wstępnie skompilowane zasady rejestracji, logowania, połączonego rejestrowania i logowania, resetowania haseł i aktualizacji profilu.

- [Wyświetlanie listy przepływów użytkownika](/graph/api/identityuserflow-list)
- [Tworzenie przepływu użytkownika](/graph/api/identityuserflow-post-userflows)
- [Pobierz przepływ użytkownika](/graph/api/identityuserflow-get)
- [Usuwanie przepływu użytkownika](/graph/api/identityuserflow-delete)

## <a name="custom-policies"></a>Zasady niestandardowe

Poniższe operacje umożliwiają zarządzanie Azure AD B2C zasadami struktury zaufania, znanymi jako [zasady niestandardowe](custom-policy-overview.md).

- [Wyświetl listę wszystkich zasad struktury zaufania skonfigurowanych w dzierżawie](/graph/api/trustframework-list-trustframeworkpolicies)
- [Utwórz zasady dotyczące struktury zaufania](/graph/api/trustframework-post-trustframeworkpolicy)
- [Odczytaj właściwości istniejących zasad struktury zaufania](/graph/api/trustframeworkpolicy-get)
- [Aktualizowanie lub tworzenie zasad struktury zaufania.](/graph/api/trustframework-put-trustframeworkpolicy)
- [Usuwanie istniejących zasad struktury zaufania](/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>Klucze zasad

Struktura środowiska tożsamości przechowuje wpisy tajne, do których odwołuje się zasada niestandardowa, aby ustanowić relację zaufania między składnikami. Te klucze tajne mogą być symetrycznymi lub asymetryczne klucze/wartości. W Azure Portal te jednostki są wyświetlane jako **klucze zasad**.

Zasób najwyższego poziomu dla kluczy zasad w interfejsie API Microsoft Graph jest [zaufanym zestawem kluczy](/graph/api/resources/trustframeworkkeyset). Każdy **zestaw kluczy** zawiera co najmniej jeden **klucz**. Aby utworzyć klucz, najpierw utwórz pusty zestaw kluczy, a następnie Wygeneruj klucz w zestawie kluczy. Można utworzyć Ręczny klucz tajny, przekazać certyfikat lub klucz PKCS12. Klucz może być wygenerowanym wpisem tajnym, definiowanym przez Ciebie ciągiem (na przykład wpisem tajnym aplikacji w serwisie Facebook) lub przekazywanym certyfikatem. Jeśli zestaw kluczy ma wiele kluczy, tylko jeden z kluczy jest aktywny.

### <a name="trust-framework-policy-keyset"></a>Zestaw kluczy zasad zaufania platformy

- [Wyświetl listę zestawów ustawień struktury zaufania](/graph/api/trustframework-list-keysets)
- [Tworzenie zestawów narzędzi struktury zaufania](/graph/api/trustframework-post-keysets)
- [Pobierz zestaw kluczy](/graph/api/trustframeworkkeyset-get)
- [Aktualizowanie zestawów danych struktury zaufania](/graph/api/trustframeworkkeyset-update)
- [Usuwanie zestawów danych struktury zaufania](/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>Klucz zasad zaufania platformy

- [Pobierz obecnie aktywny klucz w zestawie kluczy](/graph/api/trustframeworkkeyset-getactivekey)
- [Generowanie klucza w zestawie kluczy](/graph/api/trustframeworkkeyset-generatekey)
- [Przekazywanie klucza tajnego opartego na ciągu](/graph/api/trustframeworkkeyset-uploadsecret)
- [Przekaż certyfikat X. 509](/graph/api/trustframeworkkeyset-uploadcertificate)
- [Przekaż certyfikat formatu PKCS12](/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>Aplikacje

- [Wyświetlanie listy aplikacji](/graph/api/application-list)
- [Tworzenie aplikacji](/graph/api/resources/application)
- [Aktualizuj aplikację](/graph/api/application-update)
- [Utwórz obiekt serviceprincipal](/graph/api/resources/serviceprincipal)
- [Utwórz przyznanie oauth2Permission](/graph/api/resources/oauth2permissiongrant)
- [Usuń aplikację](/graph/api/application-delete)

## <a name="application-extension-properties"></a>Właściwości rozszerzenia aplikacji

- [Wyświetl właściwości rozszerzenia](/graph/api/application-list-extensionproperty)

Azure AD B2C udostępnia katalog, który może zawierać 100 atrybutów niestandardowych na użytkownika. W przypadku przepływów użytkowników te właściwości rozszerzenia są [zarządzane przy użyciu Azure Portal](user-flow-custom-attributes.md). W przypadku zasad niestandardowych Azure AD B2C tworzy dla Ciebie właściwość, podczas gdy zasady zapisują wartość do właściwości rozszerzenia.

## <a name="audit-logs"></a>Dzienniki inspekcji

- [Wyświetl listę dzienników inspekcji](/graph/api/directoryaudit-list)

Aby uzyskać więcej informacji na temat uzyskiwania dostępu do dzienników inspekcji Azure AD B2C za pomocą interfejsu API Microsoft Graph, zobacz [dostęp do dzienników inspekcji Azure AD B2C](view-audit-logs.md).