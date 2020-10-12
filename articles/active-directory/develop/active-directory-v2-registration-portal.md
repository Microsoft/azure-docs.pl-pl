---
title: Dokumentacja portalu rejestracji aplikacji | Azure
titleSuffix: Microsoft identity platform
description: Opis funkcji w portalu rejestracji aplikacji firmy Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: ryanwi
ms.reviewer: lenalepa
ms.custom: aaddev
ms.openlocfilehash: 0574eb0b8ab0b5d4de671ad44a45a85e434393f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88115581"
---
# <a name="app-registration-reference"></a>Dokumentacja rejestracji aplikacji

Ten dokument zawiera kontekst i opisy różnych funkcji dostępnych w [rejestracje aplikacji](https://aka.ms/appregistrations) środowisku Azure Portal.

## <a name="my-applications-or-converged-applications"></a>Moje aplikacje lub aplikacje, które są zbieżne

Ta lista zawiera wszystkie aplikacje zarejestrowane do użycia z punktem końcowym Microsoft Identity platform (v 2.0). Te aplikacje mają możliwość logowania użytkowników z kontami osobistymi firmy Microsoft i kont służbowych z Azure Active Directory. Aby dowiedzieć się więcej o punkcie końcowym platformy tożsamości, zobacz [Omówienie programu v 2.0](./v2-overview.md). Te aplikacje mogą być również używane do integracji z punktem końcowym uwierzytelniania konto Microsoft `https://login.live.com` .

## <a name="azure-ad-only-applications"></a>Tylko aplikacje usługi Azure AD

Ta lista zawiera wszystkie aplikacje zarejestrowane do użycia w punkcie końcowym usługi Azure AD v 1.0. Te aplikacje mają tylko możliwość logowania użytkowników przy użyciu kont służbowych z Azure Active Directory. Ta lista zawiera aplikacje, które zostały zarejestrowane przy użyciu środowiska **rejestracje aplikacji** w [Azure Portal](https://portal.azure.com).

## <a name="live-sdk-applications"></a>Dynamiczne aplikacje zestawu SDK

Ta lista zawiera wszystkie aplikacje zarejestrowane do użytku wyłącznie z konto Microsoft. Nie są one dostępne do użycia z Azure Active Directory. Tutaj znajdziesz wszystkie aplikacje, które zostały wcześniej zarejestrowane w portalu dla deweloperów w usłudze MSA `https://account.live.com/developers/applications` . Wszystkie funkcje, które były wykonane wcześniej, `https://account.live.com/developers/applications` można teraz wykonać w [rejestracje aplikacji](https://aka.ms/appregistrations).

## <a name="application-secrets"></a>Wpisy tajne aplikacji

Wpisy tajne aplikacji są poświadczeniami, które umożliwiają aplikacji wykonywanie niezawodnego [uwierzytelniania klienta](https://tools.ietf.org/html/rfc6749#section-2.3) za pomocą platformy tożsamości firmy Microsoft. W programie OAuth & OpenID Connect Connect, klucz tajny aplikacji jest często określany jako `client_secret` . W protokole v 2.0 każda aplikacja, która otrzymuje token zabezpieczający w lokalizacji adresowej w sieci Web (przy użyciu `https` schematu), musi używać klucza tajnego aplikacji, aby identyfikować się na platformie tożsamości firmy Microsoft w momencie wykupu tego tokenu zabezpieczającego. Ponadto każdy natywny klient, który odbiera tokeny na urządzeniu, będzie zabroniony przy użyciu klucza tajnego aplikacji w celu uwierzytelniania klientów. Nie zaleca się przechowywania wpisów tajnych w środowiskach niezabezpieczonych.

Każda aplikacja może zawierać dwa prawidłowe wpisy tajne aplikacji w danym momencie. Utrzymując dwa klucze tajne, można wykonywać okresowe Przerzucanie kluczy w całym środowisku aplikacji. Po przeprowadzeniu migracji całości aplikacji do nowego klucza tajnego możesz usunąć stary klucz tajny i udostępnić go nowym.

W tym momencie w portalu rejestracji aplikacji są dozwolone tylko dwa typy wpisów tajnych aplikacji. Wybranie opcji **Generuj nowe hasło** generuje i zapisuje wspólny klucz tajny w odpowiednim magazynie danych, którego można użyć w aplikacji. Wybranie opcji **Generuj nową parę kluczy** powoduje utworzenie nowej pary kluczy publicznych/prywatnych, która może zostać pobrana i użyta do uwierzytelniania klientów na platformie tożsamości firmy Microsoft. Wybranie opcji **Przekaż klucz publiczny** umożliwia użycie własnej pary kluczy publicznych/prywatnych.
Musisz przekazać certyfikat zawierający klucz publiczny.

## <a name="profile"></a>Profil

Sekcja profil portalu rejestracji aplikacji może służyć do dostosowywania strony logowania do aplikacji. W tym momencie można zmienić logo aplikacji strony logowania, adres URL warunków użytkowania i adres URL zasad zachowania poufności informacji. Logo musi być przezroczystym obrazem 48 x 48 lub 50 x 50 pikseli w pliku GIF, PNG lub JPEG o rozmiarze 15 KB lub mniejszym. Spróbuj zmienić wartości i wyświetlić wynikającą stronę logowania.

## <a name="live-sdk-support"></a>Obsługa zestawu SDK na żywo

Po włączeniu obsługi zestawu SDK na żywo wszystkie utworzone wpisy tajne aplikacji będą obsługiwane w magazynach danych usługi Azure AD i koncie Microsoft. Dzięki temu aplikacja może być zintegrowana bezpośrednio z usługą kont Microsoft (login.live.com). Jeśli chcesz skompilować aplikację bezpośrednio przy użyciu konta Microsoft (w przeciwieństwie do korzystania z punktu końcowego v 2.0), upewnij się, że włączono obsługę zestawu SDK na żywo.

Wyłączenie obsługi dynamicznego zestawu SDK zapewnia, że wpis tajny aplikacji jest zapisany tylko w magazynie danych usługi Azure AD. Magazyn danych usługi Azure AD zawiera regulacje klasy korporacyjnej, które pozwalają na spełnienie określonych standardów, takich jak zgodność FISMA. W przypadku włączenia obsługi zestawu SDK na żywo aplikacja może nie uzyskać zgodności z pewnymi standardami.

Jeśli kiedykolwiek planujesz korzystanie z punktu końcowego v 2.0, możesz bezpiecznie wyłączyć obsługę zestawu SDK na żywo.