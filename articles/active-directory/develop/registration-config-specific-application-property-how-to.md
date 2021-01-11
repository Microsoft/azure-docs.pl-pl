---
title: Pola rejestracji aplikacji niestandardowej w witrynie Azure Portal
description: Wskazówki dotyczące rejestrowania niestandardowej aplikacji przy użyciu usługi Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ROBOTS: NOINDEX
ms.openlocfilehash: 222c961f9e5587cd5526778e6ef1c800f5085a69
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2021
ms.locfileid: "98064304"
---
# <a name="azure-portal-registration-fields-for-custom-developed-apps"></a>Pola rejestracji aplikacji niestandardowej w witrynie Azure Portal

Ten artykuł zawiera krótki opis wszystkich dostępnych pól w formularzu rejestracji aplikacji w [Azure Portal](https://portal.azure.com).

## <a name="register-a-new-application"></a>Rejestrowanie nowej aplikacji

-   Aby zarejestrować nową aplikację, przejdź do <a href="https://portal.azure.com/" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.

-   W okienku nawigacji po lewej stronie kliknij pozycję **Azure Active Directory.**

-   Wybierz **rejestracje aplikacji** i kliknij przycisk **Dodaj**.

-   Spowoduje to otwarcie formularza rejestracji aplikacji.

## <a name="fields-in-the-application-registration-form"></a>Pola w formularzu rejestracji aplikacji

| Pole            | Opis                                                                              |
|------------------|------------------------------------------------------------------------------------------|
| Nazwa             | Nazwa aplikacji. Powinna zawierać co najmniej cztery znaki.                |
| Obsługiwane typy kont| Wybierz konta, które mają być obsługiwane przez Twoją aplikację: konta w tym katalogu organizacyjnym, konta w dowolnym katalogu organizacyjnym lub konta w dowolnym katalogu organizacyjnym i osobiste konta Microsoft.  |
| Identyfikator URI przekierowania (opcjonalnie) | Wybierz typ aplikacji, którą tworzysz, **Sieć Web** lub **Klient publiczny (Mobile & Desktop)**, a następnie wprowadź identyfikator URI przekierowania (lub adres URL odpowiedzi) dla aplikacji. W przypadku aplikacji internetowej podaj podstawowy adres URL aplikacji. Na przykład ciąg http://localhost:31544 może być adresem URL aplikacji internetowej uruchomionej na komputerze lokalnym. Użytkownicy mogą użyć tego adresu URL, aby zalogować się do aplikacji klienta internetowego. W przypadku publicznych aplikacji klienckich podaj identyfikator URI używany przez usługę Azure AD do zwracania odpowiedzi tokenu. Wprowadź wartość specyficzną dla swojej aplikacji, taką jak myapp://auth. Aby zobaczyć konkretne przykłady dla aplikacji sieci Web lub aplikacji natywnych, zapoznaj się z [przewodnikami szybki start](./index.yml).|

Po wypełnieniu powyższych pól aplikacja zostanie zarejestrowana w Azure Portal i nastąpi przekierowanie do strony przeglądu aplikacji. Strony Ustawienia w lewym okienku w obszarze **Zarządzaj** zawierają więcej pól pozwalających dostosować aplikację. W poniższych tabelach opisano wszystkie pola. Podzbiór tych pól zostanie wyświetlony tylko w zależności od tego, czy utworzono aplikację sieci Web, czy publiczną aplikację kliencką.

### <a name="overview"></a>Omówienie

| Pole           | Opis        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Identyfikator aplikacji  | Po zarejestrowaniu aplikacji usługa Azure AD przypisuje aplikację identyfikator aplikacji. IDENTYFIKATORA aplikacji można użyć do unikatowego identyfikowania aplikacji w żądaniach uwierzytelniania do usługi Azure AD, a także do uzyskiwania dostępu do zasobów, takich jak interfejs API programu Graph.                                                          |
| Identyfikator URI identyfikatora aplikacji      | Powinien to być unikatowy identyfikator URI, zazwyczaj w postaci **&lt; \_ &gt; / &lt; \_ nazwy &gt; aplikacji https://dzierżawy.** Ta wartość jest używana podczas przepływu przydzielania autoryzacji jako unikatowy identyfikator, aby określić zasób, dla którego ma zostać wystawiony token. Jego stan zmieni się również na "AUD" w wystawionym tokenie dostępu. |

### <a name="branding"></a>Znakowanie

| Pole           | Opis        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Przekaż nowe logo | Możesz użyć tego do przekazania logo dla aplikacji. Logo musi być w formacie BMP, jpg lub PNG, a rozmiar pliku musi być mniejszy niż 100 KB. Wymiary obrazu powinny być 215x215 pikseli, a centralne wymiary obrazu 94x94 pikseli.|
| Adres URL strony głównej   | Jest to adres URL logowania określony podczas rejestracji aplikacji.|

### <a name="authentication"></a>Uwierzytelnianie

| Pole           | Opis        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Adres URL wylogowywania      | Jest to adres URL wylogowania jednokrotnego. Usługa Azure AD wysyła do tego adresu URL żądanie wylogowania, gdy użytkownik czyści swoją sesję w usłudze Azure AD przy użyciu innej zarejestrowanej aplikacji.|
| Obsługiwane typy kont  | Ten przełącznik określa, czy aplikacja może być używana przez wielu dzierżawców. Zazwyczaj oznacza to, że organizacje zewnętrzne mogą korzystać z aplikacji, rejestrując ją w swojej dzierżawie i przyznając dostęp do danych organizacji.|
| Adresy URL przekierowania      | Adresy URL przekierowania lub odpowiedzi są punktami końcowymi, w których usługa Azure AD zwraca wszelkie tokeny, których żąda aplikacja. W przypadku aplikacji natywnych jest to miejsce, w którym użytkownik jest wysyłany po pomyślnej autoryzacji. Usługa Azure AD sprawdza, czy identyfikator URI przekierowania udostępniany przez aplikację w żądaniu protokołu OAuth 2,0 jest zgodny z jedną z zarejestrowanej wartości w portalu.|

### <a name="certificates-and-secrets"></a>Certyfikaty i wpisy tajne

| Pole           | Opis        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Klucze tajne klienta            | Można utworzyć klucze tajne klienta lub kluczy, aby programowo uzyskać dostęp do interfejsów API sieci Web zabezpieczonych przez usługę Azure AD bez interakcji z użytkownikiem. Na stronie **Nowy wpis tajny klienta** wprowadź opis klucza oraz datę wygaśnięcia i Zapisz w celu wygenerowania klucza. Upewnij się, że zapiszesz ją w bezpiecznym miejscu, ponieważ nie będzie można później uzyskać do niej dostępu.             |

## <a name="next-steps"></a>Następne kroki

[Zarządzanie aplikacjami za pomocą Azure Active Directory](../manage-apps/what-is-application-management.md)