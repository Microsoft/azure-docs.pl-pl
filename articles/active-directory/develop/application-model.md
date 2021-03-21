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
ms.openlocfilehash: 86543b961698e736b2211553b0dca367b28158ef
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98795650"
---
# <a name="application-model"></a>Model aplikacji

Aplikacje mogą logować się samodzielnie lub delegować użytkowników do dostawcy tożsamości. W tym artykule omówiono kroki wymagane do zarejestrowania aplikacji na platformie tożsamości firmy Microsoft.

## <a name="register-an-application"></a>Rejestrowanie aplikacji

Aby dostawca tożsamości mógł wiedzieć, że użytkownik ma dostęp do określonej aplikacji, zarówno użytkownik, jak i aplikacja muszą być zarejestrowane przy użyciu dostawcy tożsamości. Po zarejestrowaniu aplikacji w usłudze Azure Active Directory (Azure AD) nastąpi Konfiguracja tożsamości dla aplikacji, która umożliwia integrację z platformą tożsamości firmy Microsoft. Zarejestrowanie aplikacji umożliwia również:

* Dostosuj znakowanie aplikacji w oknie dialogowym logowania. Oznacza to, że znakowanie jest ważne, ponieważ podczas logowania użytkownik będzie musiał korzystać z aplikacji.
* Zdecyduj, czy chcesz zezwolić użytkownikom na logowanie się tylko wtedy, gdy należą do organizacji. Ta architektura jest znana jako aplikacja z jedną dzierżawą. Można też zezwolić użytkownikom na logowanie się przy użyciu dowolnego konta służbowego, które jest znane jako aplikacja wielodostępna. Możesz również zezwolić na osobiste konta Microsoft lub konto społecznościowe z serwisu LinkedIn, Google i tak dalej.
* Uprawnienia zakresu żądania. Na przykład można zażądać zakresu "User. Read", który przyznaje uprawnienia do odczytu profilu zalogowanego użytkownika.
* Zdefiniuj zakresy, które definiują dostęp do internetowego interfejsu API. Zwykle, gdy aplikacja chce uzyskać dostęp do interfejsu API, będzie musiała zażądać uprawnień do zdefiniowanych zakresów.
* Udostępnienie tajnej platformy tożsamości firmy Microsoft, która udowadnia tożsamość aplikacji. Użycie klucza tajnego jest istotne w przypadku, gdy aplikacja jest poufną aplikacją klienta. Poufna aplikacja kliencka to aplikacja, która umożliwia bezpieczne przechowywanie poświadczeń. Zaufany serwer zaplecza jest wymagany do przechowywania poświadczeń.

Po zarejestrowaniu aplikacji nastąpi jego unikatowy identyfikator, który jest udostępniany z platformą tożsamości firmy Microsoft podczas żądania tokenów. Jeśli aplikacja jest [poufną aplikacją klienta](developer-glossary.md#client-application), będzie również współużytkować klucz tajny lub publiczny w zależności od tego, czy zostały użyte certyfikaty lub wpisy tajne.

Platforma tożsamości firmy Microsoft reprezentuje aplikacje przy użyciu modelu, który spełnia dwie główne funkcje:

* Zidentyfikuj aplikację według obsługiwanych przez nią protokołów uwierzytelniania.
* Podaj wszystkie identyfikatory, adresy URL, wpisy tajne i powiązane informacje, które są konieczne do uwierzytelnienia.

Platforma tożsamości firmy Microsoft:

* Przechowuje wszystkie dane wymagane do obsługi uwierzytelniania w czasie wykonywania.
* Program przechowuje wszystkie dane dotyczące podejmowania decyzji o zasobach, do których aplikacja może potrzebować, i w jakich okolicznościach należy spełnić dane żądanie.
* Oferuje infrastrukturę do implementowania aprowizacji aplikacji w ramach dzierżawy dewelopera aplikacji oraz do innych dzierżaw usługi Azure AD.
* Obsługuje wyrażanie zgody użytkownika podczas żądania tokenu i ułatwia dynamiczne Inicjowanie obsługi aplikacji między dzierżawcami.

*Wyrażanie zgody* polega na tym, że właściciel zasobu udziela autoryzacji dla aplikacji klienckiej w celu uzyskiwania dostępu do chronionych zasobów w ramach określonych uprawnień w imieniu właściciela zasobu. Platforma tożsamości firmy Microsoft umożliwia:

* Użytkownicy i Administratorzy mogą dynamicznie przyznawać lub odmawiać zgody aplikacji na dostęp do zasobów w ich imieniu.
* Administratorzy, którzy ostatecznie decydują, jakie aplikacje mogą robić i które użytkownicy będą mogli korzystać z określonych aplikacji oraz jak są dostępne zasoby katalogów.

## <a name="multi-tenant-apps"></a>Aplikacje z wieloma dzierżawami

Na platformie tożsamości Microsoft [obiekt aplikacji](developer-glossary.md#application-object) opisuje aplikację. W czasie wdrażania platforma tożsamości firmy Microsoft używa obiektu aplikacji jako planu do utworzenia jednostki [usługi](developer-glossary.md#service-principal-object), która reprezentuje konkretne wystąpienie aplikacji w katalogu lub dzierżawie. Jednostka usługi definiuje, co aplikacja może w rzeczywistości wykonać w konkretnym katalogu docelowym, kto może z niego korzystać, do jakich zasobów ma dostęp i tak dalej. Platforma tożsamości firmy Microsoft tworzy jednostkę usługi z obiektu aplikacji za pomocą [zgody](developer-glossary.md#consent).

Na poniższym diagramie przedstawiono uproszczony przepływ aprowizacji platformy tożsamości firmy Microsoft, który jest oparty na wyrażaniu zgody. Pokazuje dwie dzierżawy *: a* i *B*.

* *Dzierżawca jest* właścicielem aplikacji.
* *Dzierżawca B* tworzy wystąpienie aplikacji za pośrednictwem nazwy głównej usługi.

![Diagram przedstawiający uproszczony przepływ aprowizacji oparty na wyrażaniu zgody.](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

Ten przepływ aprowizowania składa się z następujących etapów:

1. Użytkownik z dzierżawy B próbuje zalogować się przy użyciu aplikacji. Punkt końcowy autoryzacji żąda tokenu dla aplikacji.
1. Poświadczenia użytkownika są uzyskiwane i weryfikowane w celu uwierzytelnienia.
1. Użytkownik zostanie poproszony o podanie zgody dla aplikacji w celu uzyskania dostępu do dzierżawy B.
1. Platforma tożsamości firmy Microsoft używa obiektu aplikacji w dzierżawie A jako strategii tworzenia jednostki usługi w dzierżawie B.
1. Użytkownik otrzymuje żądany token.

Ten proces można powtórzyć dla większej liczby dzierżawców. Dzierżawca A zachowuje plan dla aplikacji (obiektu aplikacji). Użytkownicy i Administratorzy wszystkich innych dzierżawców, w których aplikacja otrzymuje zgodę, zachowują kontrolę nad tym, co aplikacja może wykonać za pośrednictwem odpowiedniego obiektu głównego usługi w każdej dzierżawie. Aby uzyskać więcej informacji, zobacz [obiekty główne aplikacji i usług na platformie tożsamości firmy Microsoft](app-objects-and-service-principals.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat uwierzytelniania i autoryzacji na platformie tożsamości firmy Microsoft, zobacz następujące artykuły:

* Aby dowiedzieć się więcej o podstawowych pojęciach dotyczących uwierzytelniania i autoryzacji, zobacz [uwierzytelnianie a autoryzacja](authentication-vs-authorization.md).
* Aby dowiedzieć się, jak tokeny dostępu, tokeny odświeżania i tokeny identyfikatorów są używane do uwierzytelniania i autoryzacji, zobacz [tokeny zabezpieczające](security-tokens.md).
* Aby dowiedzieć się więcej o przepływie logowania aplikacji sieci Web, komputerów stacjonarnych i mobilnych, zobacz temat [Rejestrowanie w usłudze Flow](app-sign-in-flow.md).

Aby uzyskać więcej informacji na temat modelu aplikacji, zobacz następujące artykuły:

* Aby uzyskać więcej informacji na temat obiektów aplikacji i jednostek usługi na platformie tożsamości firmy Microsoft, zobacz [jak i dlaczego aplikacje są dodawane do usługi Azure AD](active-directory-how-applications-are-added.md).
* Aby uzyskać więcej informacji na temat aplikacji z jedną dzierżawą i aplikacji wielodostępnych, zobacz [dzierżawca w Azure Active Directory](single-and-multi-tenant-apps.md).
* Aby uzyskać więcej informacji o tym, jak usługa Azure AD udostępnia również Azure Active Directory B2C, dzięki czemu organizacje mogą logować użytkowników, zazwyczaj są to klienci przy użyciu tożsamości społecznościowych, takich jak konto Google, zobacz [dokumentację dotyczącą Azure Active Directory B2C](../../active-directory-b2c/index.yml).