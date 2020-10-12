---
title: Pakiet wykrywania zabezpieczeń z usługą Azure Application Insights
description: Monitoruj aplikacje za pomocą usługi Azure Application Insights i inteligentnego wykrywania pod kątem potencjalnych problemów z zabezpieczeniami.
ms.topic: conceptual
ms.date: 12/12/2017
ms.openlocfilehash: 1ed304e903bb50591e61e294b6701f8268f9d8e1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87081631"
---
# <a name="application-security-detection-pack-preview"></a>Pakiet wykrywania zabezpieczeń aplikacji (wersja zapoznawcza)

Application Insights automatycznie analizuje dane telemetryczne wygenerowane przez aplikację i wykrywa potencjalne problemy z zabezpieczeniami. Ta funkcja umożliwia zidentyfikowanie potencjalnych problemów z zabezpieczeniami i ich obsługę poprzez rozwiązanie aplikacji lub podjęcie niezbędnych środków bezpieczeństwa.

Ta funkcja nie wymaga żadnych specjalnych ustawień, innych niż [Konfigurowanie aplikacji do wysyłania telemetrii](./usage-overview.md).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Kiedy mam ten typ powiadomienia inteligentnego wykrywania?
Wykryto trzy typy problemów z zabezpieczeniami:
1. Niezabezpieczony dostęp do adresu URL: adres URL w aplikacji jest uzyskiwany za pośrednictwem protokołów HTTP i HTTPS. Zazwyczaj adres URL, który akceptuje żądania HTTPS nie powinien akceptować żądań HTTP. Może to wskazywać na błąd lub problem z zabezpieczeniami w aplikacji.
2. Niezabezpieczona forma: formularz (lub inne żądanie POST) w aplikacji używa protokołu HTTP zamiast HTTPS. Użycie protokołu HTTP może osłabić dane użytkownika, które są wysyłane przez formularz.
3. Podejrzane działania użytkowników: aplikacja jest dostępna z wielu krajów/regionów przez tego samego użytkownika w tym samym czasie. Na przykład ten sam użytkownik uzyskał dostęp do aplikacji z Hiszpanii i Stany Zjednoczone w tej samej godzinie. To wykrywanie wskazuje potencjalnie złośliwy dostęp do aplikacji.

## <a name="does-my-app-definitely-have-a-security-issue"></a>Czy moja aplikacja ma w nieskończony problem z zabezpieczeniami?
Nie, powiadomienie nie oznacza, że aplikacja ma w nieskończoność problem z zabezpieczeniami. Wykrywanie dowolnego z powyższych scenariuszy może w wielu przypadkach wskazywać na problem z zabezpieczeniami. Jednak wykrywanie może mieć naturalne uzasadnienie biznesowe i można je zignorować.

## <a name="how-do-i-fix-the-insecure-url-access-detection"></a>Jak mogę naprawić wykrywania "niezabezpieczonego dostępu do adresów URL"?
1. **Klasyfikacja.** Powiadomienie zawiera liczbę użytkowników, którzy uzyskują dostęp do niezabezpieczonych adresów URL, a także adres URL, który był najbardziej narażony przez Niezabezpieczony dostęp. Może to pomóc w przypisaniu priorytetu do problemu.
2. **Scope.** Jaki procent użytkowników uzyska dostęp do niezabezpieczonych adresów URL? Ile adresów URL miało wpływ? Te informacje można uzyskać na podstawie powiadomienia.
3. **Sprawdź.** Wykrywanie zawiera listę niezabezpieczonych żądań oraz listę adresów URL i użytkowników, których dotyczy problem, aby pomóc w dalszej analizie problemu.

## <a name="how-do-i-fix-the-insecure-form-detection"></a>Jak mogę naprawić wykrywania "niezabezpieczonego formularza"?
1. **Klasyfikacja.** Powiadomienie zawiera liczbę niezabezpieczonych formularzy i liczby użytkowników, których dane zostały naruszone. Może to pomóc w przypisaniu priorytetu do problemu.
2. **Scope.** Który formularz został uwzględniony w największej liczbie niezabezpieczonych elementów i jaki jest rozkład niezabezpieczonych operacji w czasie? Te informacje można uzyskać na podstawie powiadomienia.
3. **Sprawdź.** Wykrywanie zawiera listę niezabezpieczonych formularzy i podział liczby niezabezpieczonych transformacje dla każdego formularza, co pomoże w dalszej analizie problemu.

## <a name="how-do-i-fix-the-suspicious-user-activity-detection"></a>Jak mogę naprawić wykrywanie podejrzanych działań użytkowników?
1. **Klasyfikacja.** Powiadomienie zawiera liczbę różnych użytkowników, którzy wykorzystali podejrzane zachowanie. Może to pomóc w przypisaniu priorytetu do problemu.
2. **Scope.** Z których krajów/regionów pochodzą podejrzane żądania? Który użytkownik był najbardziej podejrzany? Te informacje można uzyskać na podstawie powiadomienia.
3. **Sprawdź.** Wykrywanie zawiera listę podejrzanych użytkowników i listę krajów/regionów dla każdego użytkownika, co pomoże w dalszej analizie problemu.
