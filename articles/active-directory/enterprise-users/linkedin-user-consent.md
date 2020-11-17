---
title: Udostępnianie danych w serwisie LinkedIn i wyrażanie zgody — Azure Active Directory | Microsoft Docs
description: Wyjaśnia, jak integracja z usługą LinkedIn udostępnia dane za pośrednictwem aplikacji firmy Microsoft w Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: enterprise-users
ms.topic: how-to
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ac04bdad1f4a93c430009a04ed328be0440d6ff
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94646971"
---
# <a name="linkedin-account-connections-data-sharing-and-consent"></a>Udostępnianie i zgoda danych połączeń konta LinkedIn

Możesz zezwolić użytkownikom w organizacji usługi Active Directory (Azure AD) na wyrażanie zgody na połączenie swojego konta służbowego firmy Microsoft z kontem usługi LinkedIn. Gdy użytkownik łączy swoje konta, informacje i najważniejsze elementy z serwisu LinkedIn są dostępne w niektórych aplikacjach i usługach firmy Microsoft. Użytkownicy mogą również oczekiwać, że środowisko sieciowe w serwisie LinkedIn zostanie udoskonalone i wzbogacone o informacje od firmy Microsoft.

Aby wyświetlić informacje o serwisie LinkedIn w aplikacjach i usługach firmy Microsoft, użytkownicy muszą wyrazić zgodę na połączenie własnych kont Microsoft i LinkedIn. Użytkownicy są monitowani o połączenie ich kont przy pierwszym kliknięciu, aby zobaczyć informacje o witrynie LinkedIn na karcie profilu w programie Outlook, usłudze OneDrive lub usłudze SharePoint Online. Połączenia konta serwisu LinkedIn nie są w pełni włączone dla użytkowników, dopóki nie wyrazią zgody na środowisko pracy i nie zostaną połączone z kontami.

[!INCLUDE [active-directory-gdpr-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="benefits-of-sharing-linkedin-information"></a>Zalety udostępniania informacji w serwisie LinkedIn

Dostęp do informacji w serwisie LinkedIn w ramach aplikacji i usług firmy Microsoft ułatwia użytkownikom łączenie, angażowanie i tworzenie profesjonalnych relacji ze współpracownikami, klientami i partnerami w organizacji i poza nią. Nowi użytkownicy mogą szybciej uzyskać szybkość, łącząc się ze współpracownikami, Dowiedz się więcej o nich i łatwo Uzyskuj dostęp do dodatkowych informacji. Oto przykład sposobu wyświetlania informacji o usłudze LinkedIn na karcie profilu w aplikacjach firmy Microsoft:

![Włączanie integracji serwisu LinkedIn w organizacji](./media/linkedin-user-consent/display-example.png)

## <a name="enable-and-announce-linkedin-integration"></a>Włącz i Ogłoś integrację serwisu LinkedIn

Aby zarządzać ustawieniem organizacji, musisz być administratorem Azure Active Directory. Można ją włączyć dla wszystkich użytkowników lub dla określonego zbioru użytkowników.

1. Aby włączyć lub wyłączyć integrację, wykonaj kroki opisane w sekcji [wyrażanie zgody na integrację serwisu LinkedIn dla organizacji usługi Azure AD](linkedin-integration.md).
2. Po zaogłaszaniu integracji serwisu LinkedIn w organizacji należy wskazać swoim użytkownikom pytania dotyczące informacji o [serwisie LinkedIn w aplikacjach i usługach firmy Microsoft](https://support.office.com/article/about-linkedin-information-and-features-in-microsoft-apps-and-services-dc81cc70-4d64-4755-9f1c-b9536e34d381). Ten artykuł zawiera informacje o tym, gdzie są wyświetlane informacje o usłudze LinkedIn, [udostępnianiu i poufności danych](https://support.microsoft.com/office/your-data-ae9c08a7-4d06-45b5-a065-320a97bc1400), [sposobach łączenia kont](https://support.microsoft.com/office/connect-your-linkedin-and-work-or-school-accounts-c7c245f2-fa56-4c9b-ba20-3fceb23c5772) i innych.

Musisz ogłosić integrację serwisu LinkedIn z użytkownikami, dostarczając im wszystkie informacje związane z [udostępnianiem i ochroną danych za pomocą integracji serwisu LinkedIn](https://support.microsoft.com/office/your-data-ae9c08a7-4d06-45b5-a065-320a97bc1400). 

## <a name="user-consent-for-data-access-in-microsoft-and-linkedin"></a>Użytkownik wyraża zgodę na dostęp do danych w firmie Microsoft i LinkedIn

Dane, do których uzyskiwany jest dostęp z serwisu LinkedIn, nie są trwale przechowywane w usługach firmy Microsoft. Dane, do których uzyskuje się dostęp z firmy Microsoft, nie są przechowywane trwale w serwisie LinkedIn, z wyjątkiem kontaktów. Kontakty firmy Microsoft są przechowywane w serwisie LinkedIn do momentu ich usunięcia przez użytkowników zgodnie z opisem w artykule [usuwanie zaimportowanych kontaktów z serwisu LinkedIn](https://www.linkedin.com/help/linkedin/answer/43377).

Gdy użytkownicy łączą swoje konta, informacje i szczegółowe dane z serwisu LinkedIn są dostępne w niektórych aplikacjach firmy Microsoft, takich jak karta profilu. Użytkownicy mogą również oczekiwać, że środowisko sieciowe w serwisie LinkedIn zostanie udoskonalone i wzbogacone o informacje od firmy Microsoft.
Gdy użytkownicy w Twojej organizacji nawiązują połączenie z kontami służbowymi w serwisie LinkedIn i firmą Microsoft, mają dwie opcje:

* Udziel uprawnień do uzyskiwania dostępu do danych z obu kont. Oznacza to, że przyznają im uprawnienia do konta Microsoft lub służbowego dostępu do danych z konta usługi LinkedIn oraz do [konta usługi LinkedIn, aby uzyskiwać dostęp do danych z konta służbowego firmy Microsoft](https://www.linkedin.com/help/linkedin/answer/84077).
* Udziel uprawnień tylko do danych serwisu LinkedIn, które mają być dostępne na koncie służbowym firmy Microsoft.

Użytkownicy mogą rozłączać konta i usuwać w dowolnym momencie uprawnienia dostępu do danych, a [Użytkownicy mogą kontrolować sposób wyświetlania własnego profilu serwisu LinkedIn](https://www.linkedin.com/help/linkedin/answer/83), w tym informacje o tym, czy ich profil może być wyświetlany w aplikacjach firmy Microsoft.

### <a name="linkedin-account-data"></a>Dane konta LinkedIn

Po nawiązaniu połączenia z kontami Microsoft i LinkedIn można zezwolić usłudze LinkedIn na dostarczenie następujących danych firmie Microsoft:

* Dane profilowe — obejmują tożsamość LinkedIn, informacje kontaktowe oraz informacje udostępniane innym użytkownikom w Twoim [profilu LinkedIn](https://www.linkedin.com/help/linkedin/answer/15493).
* Dane dotyczące zainteresowania — obejmują zainteresowania serwisu LinkedIn, takie jak osoby i tematy, z których korzystasz, grupy kursów oraz zawartość, którą lubisz.
* Dane subskrypcji — obejmuje subskrypcje aplikacji i usług serwisu LinkedIn wraz ze skojarzonymi danymi. 
* Dane połączeń — obejmuje [Sieć serwisu LinkedIn](https://www.linkedin.com/help/linkedin/answer/110) , w tym profile i informacje kontaktowe dla połączeń pierwszego stopnia.

Dane, do których uzyskiwany jest dostęp z serwisu LinkedIn, nie są trwale przechowywane w usługach firmy Microsoft. Aby uzyskać więcej informacji o korzystaniu z danych osobowych przez firmę Microsoft, zapoznaj się z [zasadami zachowania poufności informacji firmy Microsoft](https://privacy.microsoft.com/privacystatement/).

### <a name="microsoft-work-or-school-account-data"></a>Dane konta służbowego firmy Microsoft

Po nawiązaniu połączenia z kontami Microsoft i LinkedIn firma Microsoft może dostarczyć następujące dane do serwisu LinkedIn:

* Dane profilu — zawiera informacje, takie jak imię i nazwisko, nazwisko, zdjęcie profilu, adres e-mail, Menedżer i zarządzane przez siebie osoby.
* Dane kalendarza — obejmuje spotkania w Twoich kalendarzach, ich godziny, lokalizacje i informacje kontaktowe uczestników. Informacje o spotkaniu, takie jak Agenda, zawartość lub tytuł spotkania, nie są uwzględniane w danych kalendarza.
* Dane dotyczące zainteresowania — obejmuje interesy skojarzone z Twoim kontem w oparciu o korzystanie z usług firmy Microsoft, takich jak Cortana i Bing dla firm.
* Dane subskrypcji — obejmuje subskrypcje udostępniane przez organizację do aplikacji i usług firmy Microsoft, takich jak Microsoft 365.
* Dane kontaktów — obejmują listy kontaktów w programie Outlook, Skypie i innych konto Microsoft usługach, w tym informacje kontaktowe dla osób, które często komunikują się lub wspólnie z nimi pracują. Kontakty będą okresowo importowane, przechowywane i używane przez serwis LinkedIn, na przykład w celu zasugerowania połączeń, ułatwienia organizowania kontaktów i wyświetlania aktualizacji dotyczących kontaktów.

Dane, do których uzyskuje się dostęp z firmy Microsoft, nie są przechowywane trwale w serwisie LinkedIn, z wyjątkiem kontaktów. Kontakty firmy Microsoft są przechowywane w serwisie LinkedIn do momentu ich usunięcia przez użytkowników. Dowiedz się więcej [na temat usuwania zaimportowanych kontaktów z serwisu LinkedIn](https://www.linkedin.com/help/linkedin/answer/43377).

Aby uzyskać więcej informacji na temat korzystania z danych osobowych przez serwis LinkedIn, zobacz [zasady zachowania poufności informacji w serwisie LinkedIn](https://www.linkedin.com/legal/privacy-policy). W przypadku usług LinkedIn, transferu danych i magazynu dane mogą przepływać z Unii Europejskiej do Stany Zjednoczone i z powrotem, a ochrona prywatności jest chroniona zgodnie z opisem w [transferach danych w Unii Europejskiej](https://www.linkedin.com/help/linkedin/answer/62533).

## <a name="next-steps"></a>Następne kroki

* [Serwis LinkedIn w aplikacjach firmy Microsoft przy użyciu konta służbowego](https://www.linkedin.com/help/linkedin/answer/84077)
