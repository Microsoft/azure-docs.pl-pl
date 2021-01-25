---
title: Model aplikacji | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o procesie rejestracji aplikacji, aby umożliwić integrację z platformą tożsamości firmy Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/28/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 2ba41e36d12b58da2e572cf870195716eacaddef
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98755687"
---
# <a name="application-model"></a>Model aplikacji

Aplikacje mogą logować się samodzielnie lub delegować użytkowników do dostawcy tożsamości. W tym temacie omówiono kroki wymagane do zarejestrowania aplikacji na platformie Microsoft Identity platform.

## <a name="registering-an-application"></a>Rejestrowanie aplikacji

Aby dostawca tożsamości mógł wiedzieć, że użytkownik ma dostęp do określonej aplikacji, zarówno użytkownik, jak i aplikacja muszą być zarejestrowane przy użyciu dostawcy tożsamości. Po zarejestrowaniu aplikacji w usłudze Azure AD udostępniamy konfigurację tożsamości dla aplikacji, która umożliwia integrację z platformą tożsamości firmy Microsoft. Zarejestrowanie aplikacji umożliwia również:

* Dostosuj znakowanie aplikacji w oknie dialogowym logowania. Jest to ważne, ponieważ jest to pierwsze środowisko użytkownika z Twoją aplikacją.
* Zdecyduj, czy chcesz zezwolić użytkownikom na logowanie się tylko wtedy, gdy należą do organizacji. Jest to jedna aplikacja dzierżawy. Lub zezwól użytkownikom na logowanie się przy użyciu dowolnego konta służbowego. Jest to aplikacja wielodostępna. Możesz również zezwolić na osobiste konta Microsoft lub konto społecznościowe w serwisie LinkedIn, Google i tak dalej.
* Uprawnienia zakresu żądania. Na przykład można zażądać zakresu "User. Read", który przyznaje uprawnienia do odczytu profilu zalogowanego użytkownika.
* Zdefiniuj zakresy, które definiują dostęp do internetowego interfejsu API. Zwykle, gdy aplikacja chce uzyskać dostęp do interfejsu API, będzie musiała zażądać uprawnień do zdefiniowanych zakresów.
* Udostępnienie tajnej platformy tożsamości firmy Microsoft, która udowadnia tożsamość aplikacji.  Ma to zastosowanie w przypadku, gdy aplikacja jest poufną aplikacją kliencką. Poufna aplikacja kliencka to aplikacja, która umożliwia bezpieczne przechowywanie poświadczeń. Wymagają one zaufanego serwera wewnętrznej bazy danych do przechowywania poświadczeń.

Po zarejestrowaniu aplikacja będzie mieć unikatowy identyfikator, który jest udostępniany przez aplikację z platformą tożsamości firmy Microsoft podczas żądania tokenów. Jeśli aplikacja jest [poufną aplikacją klienta](developer-glossary.md#client-application), będzie również współużytkować klucz tajny lub publiczny — w zależności od tego, czy zostały użyte certyfikaty lub wpisy tajne.

Platforma tożsamości firmy Microsoft reprezentuje aplikacje przy użyciu modelu, który spełnia dwie główne funkcje:

* Identyfikowanie aplikacji przez obsługiwane protokoły uwierzytelniania
* Podaj wszystkie identyfikatory, adresy URL, wpisy tajne i powiązane informacje, które są konieczne do uwierzytelnienia

Platforma tożsamości firmy Microsoft:

* Przechowuje wszystkie dane wymagane do obsługi uwierzytelniania w czasie wykonywania
* Program przechowuje wszystkie dane dotyczące podejmowania decyzji o zasobach, do których aplikacja może potrzebować, i w jakich okolicznościach należy spełnić dane żądanie.
* Zapewnia infrastrukturę do implementowania aprowizacji aplikacji w ramach dzierżawy dewelopera aplikacji oraz do dowolnej innej dzierżawy usługi Azure AD
* Obsługuje wyrażanie zgody użytkownika podczas żądania tokenu i ułatwia dynamiczne Inicjowanie obsługi aplikacji między dzierżawcami

**Wyrażanie zgody** polega na tym, że właściciel zasobu udziela autoryzacji dla aplikacji klienckiej w celu uzyskiwania dostępu do chronionych zasobów w ramach określonych uprawnień w imieniu właściciela zasobu. Platforma tożsamości firmy Microsoft:

* Umożliwia użytkownikom i administratorom dynamiczne wyrażanie zgody lub odmawianie zgody dla aplikacji na uzyskiwanie przez nią dostępu do zasobów w ich imieniu.
* Umożliwia administratorom ostateczne zdecydowanie, jakie działania mogą podejmować aplikacje, którzy użytkownicy mogą używać określonych aplikacji i w jaki sposób uzyskiwany jest dostęp do zasobów katalogu.

## <a name="multi-tenant-apps"></a>Aplikacje z wieloma dzierżawami

Na platformie tożsamości Microsoft [obiekt aplikacji](developer-glossary.md#application-object) opisuje aplikację. W czasie wdrażania platforma tożsamości firmy Microsoft używa obiektu aplikacji jako planu do utworzenia jednostki [usługi](developer-glossary.md#service-principal-object), która reprezentuje konkretne wystąpienie aplikacji w katalogu lub dzierżawie. Jednostka usługi definiuje, co aplikacja może w rzeczywistości wykonać w konkretnym katalogu docelowym, kto może z niego korzystać, do jakich zasobów ma dostęp i tak dalej. Platforma tożsamości firmy Microsoft tworzy jednostkę usługi z obiektu aplikacji za pomocą [zgody](developer-glossary.md#consent).

Na poniższym diagramie przedstawiono uproszczony przepływ aprowizacji platformy tożsamości firmy Microsoft, który jest oparty na wyrażaniu zgody. Pokazuje dwie dzierżawy *: a* i *B*.

* *Dzierżawca jest* właścicielem aplikacji.
* *Dzierżawca B* tworzy wystąpienie aplikacji za pośrednictwem nazwy głównej usługi.

![Uproszczony przepływ aprowizowania sterowany poprzez wyrażenie zgody](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

Ten przepływ aprowizowania składa się z następujących etapów:

1. Użytkownik z dzierżawy B próbuje zalogować się przy użyciu aplikacji, punkt końcowy autoryzacji żąda tokenu dla aplikacji.
1. Poświadczenia użytkownika są uzyskiwane i weryfikowane w celu uwierzytelnienia.
1. Użytkownik zostanie poproszony o podanie zgody dla aplikacji w celu uzyskania dostępu do dzierżawy B.
1. Platforma tożsamości firmy Microsoft używa obiektu aplikacji w dzierżawie A jako strategii tworzenia jednostki usługi w dzierżawie B.
1. Użytkownik otrzymuje żądany token.

Ten proces można powtórzyć w przypadku dodatkowych dzierżawców. Dzierżawca A zachowuje plan dla aplikacji (obiektu aplikacji). Użytkownicy i Administratorzy wszystkich innych dzierżawców, w których aplikacja otrzymuje zgodę, zachowują kontrolę nad tym, co aplikacja może wykonać za pośrednictwem odpowiedniego obiektu głównego usługi w każdej dzierżawie. Aby uzyskać więcej informacji, zobacz [obiekty główne aplikacji i usług na platformie tożsamości firmy Microsoft](app-objects-and-service-principals.md).

## <a name="next-steps"></a>Następne kroki

W przypadku innych tematów obejmujących podstawowe informacje dotyczące uwierzytelniania i autoryzacji:

* Zobacz [uwierzytelnianie i autoryzacja](authentication-vs-authorization.md) , aby dowiedzieć się więcej na temat podstawowych pojęć związanych z uwierzytelnianiem i autoryzacją w usłudze Microsoft Identity platform.
* Zobacz [tokeny zabezpieczające](security-tokens.md) , aby dowiedzieć się, jak tokeny dostępu, tokeny odświeżania i tokeny identyfikatorów są używane podczas uwierzytelniania i autoryzacji.
* Aby dowiedzieć się więcej o przepływie logowania aplikacji sieci Web, komputerów stacjonarnych i aplikacji mobilnych na platformie tożsamości firmy Microsoft, zobacz temat rejestrowanie w usłudze [Flow](app-sign-in-flow.md) .

Aby dowiedzieć się więcej na temat modelu aplikacji:

* Zobacz [, jak i dlaczego aplikacje są dodawane do usługi Azure AD](active-directory-how-applications-are-added.md) , aby uzyskać więcej informacji na temat obiektów aplikacji i jednostek usługi na platformie tożsamości firmy Microsoft.
* Aby uzyskać więcej informacji na temat aplikacji z jedną dzierżawą i aplikacji z wieloma dzierżawcami, zobacz temat [dzierżawa w Azure Active Directory](single-and-multi-tenant-apps.md) .
* Zapoznaj się z [dokumentacją Azure Active Directory B2C](../../active-directory-b2c/index.yml) , aby uzyskać więcej informacji o tym, jak usługa Azure AD udostępnia także Azure Active Directory B2C, dzięki czemu organizacje mogą logować użytkowników, zazwyczaj są to klienci korzystający z tożsamości społecznościowych, takich jak konto Google.