---
title: Włącz kontroli wieku w Azure Active Directory B2C | Microsoft Docs
description: Dowiedz się więcej na temat identyfikowania małoletnich za pomocą aplikacji.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/13/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6bd93f9062f8446ce20436a7a04e2054aaf5be71
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85386135"
---
# <a name="enable-age-gating-in-azure-active-directory-b2c"></a>Włącz kontroli wieku w Azure Active Directory B2C

>[!IMPORTANT]
>Ta funkcja jest dostępna w publicznej wersji zapoznawczej. Nie używaj funkcji dla aplikacji produkcyjnych.
>

Kontroli wieku w Azure Active Directory B2C (Azure AD B2C) umożliwia zidentyfikowanie małoletnich, które chcą korzystać z aplikacji. Można zablokować, aby uniemożliwić logowanie do aplikacji. Użytkownicy mogą również wrócić do aplikacji i zidentyfikować jej grupę wiekową oraz stan ich zgody rodzicielskiej. Azure AD B2C mogą blokować małoletnie bez zgody rodzicielskiej. Azure AD B2C można również skonfigurować tak, aby umożliwić aplikacji decydowanie o tym, co zrobić z drobnymi wersjami.

Po włączeniu kontroli wieku w [przepływie użytkownika](user-flow-overview.md)użytkownicy są monitowani o ich pourodzenie i kraj/region, w którym się znajdują. Jeśli użytkownik zaloguje się do programu, który nie wprowadził wcześniej informacji, będzie musiał wprowadzić go przy następnym logowaniu. Reguły są stosowane za każdym razem, gdy użytkownik loguje się.

Azure AD B2C korzysta z informacji wprowadzonych przez użytkownika w celu ustalenia, czy są one małoletnim. Pole **grupy wiekowej** zostanie następnie zaktualizowane na swoim koncie. Wartość może być `null` ,,,, `Undefined` `Minor` `Adult` i `NotAdult` .  Pola **grupy wiekowej** i **consentProvidedForMinor** są następnie używane do obliczania wartości **legalAgeGroupClassification**.

Wiek kontroli obejmuje dwie wartości wiekowe: wiek, którego ktoś nie jest już traktowany jako drobny, a okres ważności musi mieć zgodę rodzicielską. W poniższej tabeli wymieniono reguły dotyczące wieku, które są używane do definiowania drobnych i drobnych wyrazów wymagających zgody.

| Kraj/region | Nazwa kraju/regionu | Niewielki wiek zgody | Wiek pomocniczy |
| -------------- | ------------------- | ----------------- | --------- |
| Domyślne | Brak | Brak | 18 |
| AE | Zjednoczone Emiraty Arabskie | Brak | 21 |
| AT | Austria | 14 | 18 |
| BE | Belgia | 14 | 18 |
| BG | Bułgaria | 16 | 18 |
| BH | Bahrajn | Brak | 21 |
| CM | Kamerun | Brak | 21 |
| CY | Cypr | 16 | 18 |
| CZ | Republika Czeska | 16 | 18 |
| DE | Niemcy | 16 | 18 |
| DK | Dania | 16 | 18 |
| EE | Estonia | 16 | 18 |
| EG | Egipt | Brak | 21 |
| ES | Hiszpania | 13 | 18 |
| PW | Francja | 16 | 18 |
| GB | Zjednoczone Królestwo | 13 | 18 |
| GR | Grecja | 16 | 18 |
| HR | Chorwacja | 16 | 18 |
| HU | Węgry | 16 | 18 |
| IE | Irlandia | 13 | 18 |
| IT | Włochy | 16 | 18 |
| KR | Korea Południowa | 14 | 18 |
| LT | Litwa | 16 | 18 |
| LU | Luksemburg | 16 | 18 |
| LV | Łotwa | 16 | 18 |
| MT | Malta | 16 | 18 |
| Nie dotyczy | Namibia | Brak | 21 |
| NL | Holandia | 16 | 18 |
| PL | Polska | 13 | 18 |
| PT | Portugalia | 16 | 18 |
| RO | Rumunia | 16 | 18 |
| SE | Szwecja | 13 | 18 |
| SG | Singapur | Brak | 21 |
| SI | Słowenia | 16 | 18 |
| SK | Słowacja | 16 | 18 |
| TD | Czad | Brak | 21 |
| TH | Tajlandia | Brak | 20 |
| TW | Tajwan | Brak | 20 |
| USA | Stany Zjednoczone | 13 | 18 |

## <a name="age-gating-options"></a>Opcje kontroli wieku

### <a name="allowing-minors-without-parental-consent"></a>Zezwalanie na drobne bez zgody rodziców

W przypadku przepływów użytkowników, które zezwalają na rejestrację, logowanie lub oba te elementy, można zezwolić na użycie małoletnich bez zgody na aplikację. Małoletnie bez zgody rodziców mogą się zalogować lub zarejestrować się jako normalne i Azure AD B2C wystawia token identyfikatora z **legalAgeGroupClassificationm** . To zgłoszenie definiuje środowisko użytkownika, takie jak zbieranie zgody rodzicielskiej i aktualizowanie pola **consentProvidedForMinor** .

### <a name="blocking-minors-without-parental-consent"></a>Blokowanie małoletnich bez zgody rodzicielskiej

W przypadku przepływów użytkowników, które umożliwiają rejestrowanie się, logowanie lub oba, można zablokować małoletnich bez zgody aplikacji. Następujące opcje są dostępne na potrzeby obsługi zablokowanych użytkowników w Azure AD B2C:

- Wyślij kod JSON z powrotem do aplikacji — ta opcja wysyła odpowiedź z powrotem do aplikacji, która została zablokowana.
- Pokaż stronę błędu — użytkownik jest pokazywany na stronie informującej o tym, że nie mogą uzyskać dostępu do aplikacji.

## <a name="set-up-your-tenant-for-age-gating"></a>Konfigurowanie dzierżawy na potrzeby kontroli wieku

Aby korzystać z kontroli wieku w przepływie użytkownika, należy skonfigurować dzierżawcę do posiadania dodatkowych właściwości.

1. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając w górnym menu pozycję **katalog i subskrypcja** . Wybierz katalog, w którym znajduje się Twoja dzierżawa.
2. Wybierz pozycję **wszystkie usługi** w lewym górnym rogu Azure Portal, Wyszukaj i wybierz pozycję **Azure AD B2C**.
3. Wybierz pozycję **Właściwości** dla dzierżawy w menu po lewej stronie.
2. W sekcji **wiek kontroli** kliknij pozycję **Konfiguruj**.
3. Poczekaj na zakończenie operacji, a dzierżawca zostanie skonfigurowany na potrzeby kontroli wieku.

## <a name="enable-age-gating-in-your-user-flow"></a>Włącz kontroli wieku w przepływie użytkownika

Po skonfigurowaniu dzierżawy do korzystania z kontroli wieku można użyć tej funkcji w [przepływach użytkowników](user-flow-versions.md) , gdzie jest ona włączona. Aby włączyć kontroli wieku, wykonaj następujące czynności:

1. Utwórz przepływ użytkownika z włączonym kontroli wieku.
2. Po utworzeniu przepływu użytkownika wybierz pozycję **Właściwości** w menu.
3. W sekcji **kontroli wieku** wybierz pozycję **włączone**.
4. Następnie zdecyduj, w jaki sposób chcesz zarządzać użytkownikami, które identyfikują jako małoletnie. W przypadku **rejestracji lub logowania**należy wybrać `Allow minors to access your application` lub `Block minors from accessing your application` . W przypadku wybrania opcji blokowania drobnych zaznaczania `Send a JSON back to the application` lub `Show an error message` .




