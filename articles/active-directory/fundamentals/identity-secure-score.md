---
title: Co to jest bezpieczna ocena tożsamości? -Azure Active Directory
description: Jak korzystać z bezpiecznego wyniku tożsamości w celu usprawnienia stan zabezpieczeń katalogu
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: tilarso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c319489fe4c884cd5de48ac2d3e47e7beb3026f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90705490"
---
# <a name="what-is-the-identity-secure-score-in-azure-active-directory"></a>Co to jest wskaźnik bezpieczeństwa tożsamości w usłudze Azure Active Directory?

Jak bezpieczna jest Twoja dzierżawa usługi Azure AD? Jeśli nie wiesz, jak odpowiedzieć na to pytanie, w tym artykule wyjaśniono, jak bezpieczna ocena tożsamości pomaga monitorować i ulepszać stan zabezpieczeń tożsamości.

## <a name="what-is-an-identity-secure-score"></a>Co to jest wskaźnik bezpieczeństwa tożsamości?

Wynikowy test tożsamości jest liczbą z przedziału od 1 do 223, która działa jako wskaźnik określający, w jaki sposób użytkownik ma zalecenia dotyczące najlepszych rozwiązań w zakresie zabezpieczeń. Każda akcja ulepszania w ramach bezpiecznego wyniku tożsamości jest dostosowywana do określonej konfiguracji.  

![Wskaźnik bezpieczeństwa](./media/identity-secure-score/identity-secure-score-overview.png)

Ten wskaźnik ułatwi Ci:

- Obiektywne mierzenie poziomu bezpieczeństwa tożsamości
- Planowanie poprawy bezpieczeństwa tożsamości
- Sprawdzanie powodzenia wprowadzonych udoskonaleń

Dostęp do wskaźnika i powiązanych informacji można uzyskać na pulpicie nawigacyjnym wskaźnika bezpieczeństwa tożsamości. Informacje prezentowane na tym pulpicie nawigacyjnym to:

- Twój bezpieczny wynik tożsamości
- Wykres porównawczy pokazujący, jak bezpieczny wynik tożsamości jest porównywany z innymi dzierżawcami w tej samej branży i o podobnym rozmiarze
- Wykres trendu przedstawiający sposób, w jaki Twój bezpieczny wynik tożsamości został zmieniony z upływem czasu
- Lista możliwych ulepszeń

Poprzez wykonanie akcji wprowadzania ulepszeń możliwe jest:

- Ulepszanie stan zabezpieczeń i oceny
- Korzystaj z funkcji dostępnych dla Twojej organizacji w ramach inwestycji związanych z tożsamościami

## <a name="how-do-i-get-my-secure-score"></a>Jak mogę uzyskać swój wskaźnik bezpieczeństwa?

Wynik bezpiecznego tożsamości jest dostępny we wszystkich wersjach usługi Azure AD. Organizacje mogą uzyskać dostęp do swoich tożsamości, które pochodzą z **Azure Portal**  >  **Azure Active Directory**  >  **bezpieczeństwa**  >  .

## <a name="how-does-it-work"></a>Jak to działa?

Co 48 godzin platforma Azure analizuje Twoją konfigurację zabezpieczeń i porównuje Twoje ustawienia z rekomendowanymi najlepszymi rozwiązaniami. Na podstawie wyniku tej oceny dla katalogu zostanie obliczony nowy wynik. Istnieje możliwość, że Konfiguracja zabezpieczeń nie jest w pełni wyrównana z najlepszymi wskazówkami, a akcje poprawy są tylko częściowo spełnione. W tych scenariuszach otrzymasz tylko część maksymalnej liczby dostępnych dla kontrolki.

Każda rekomendacja jest mierzona w oparciu o konfigurację usługi Azure AD. Jeśli używasz produktów innych firm, aby włączyć zalecenie dotyczące najlepszych rozwiązań, możesz wskazać tę konfigurację w ustawieniach akcji ulepszania. Istnieje również możliwość ustawienia zaleceń, które mają być ignorowane, jeśli nie mają zastosowania do danego środowiska. Zignorowana rekomendacja nie wpływa na obliczenia Twojego wskaźnika.

![Ignoruj lub Oznacz akcję jako objętą przez inną firmę](./media/identity-secure-score/identity-secure-score-ignore-or-third-party-reccomendations.png)

## <a name="how-does-it-help-me"></a>Jak to może mi pomóc?

Wskaźnik bezpieczeństwa jest pomocny w następujących kwestiach:

- Obiektywne mierzenie poziomu bezpieczeństwa tożsamości
- Planowanie poprawy bezpieczeństwa tożsamości
- Sprawdzanie powodzenia wprowadzonych udoskonaleń

## <a name="what-you-should-know"></a>Co należy wiedzieć

### <a name="who-can-use-the-identity-secure-score"></a>Kto może używać wskaźnika bezpieczeństwa tożsamości?

Wskaźnik bezpieczeństwa tożsamości może być używany przez następujące role:

- Administrator globalny
- Administrator zabezpieczeń
- Czytelnicy zabezpieczeń

### <a name="how-are-controls-scored"></a>Jak są oceniane kontrolki?

Kontrolki mogą być oceniane na dwa sposoby. Niektóre są oceniane w sposób binarny — otrzymasz 100% oceny, jeśli masz funkcję lub ustawienie skonfigurowane na podstawie naszego zalecenia. Inne wyniki są obliczane jako wartość procentowa całkowitej konfiguracji. Na przykład jeśli zalecenie dotyczące ulepszeń postanowi 30 punktów, jeśli chronisz wszystkich użytkowników za pomocą usługi MFA i masz tylko 5 z 100 łącznej liczby użytkowników, otrzymasz częściowy wynik wokół 2 punktów (5 chronionych/100 łącznie * 30 maksymalnych wartości = 2.

### <a name="what-does-not-scored-mean"></a>Co oznacza informacja [Nieoceniane]?

Akcje oznaczone jako [Nieoceniane] to takie, które możesz wykonywać w swojej organizacji, ale które nie będą oceniane, ponieważ nie są uwzględnione w narzędziu (jeszcze!). Ciągle możesz więc zwiększyć swój poziom bezpieczeństwa, ale w tej chwil nie otrzymasz za to punktów.

### <a name="how-often-is-my-score-updated"></a>Jak często mój wskaźnik jest aktualizowany?

Wskaźnik jest obliczany raz dziennie (około 1:00 czasu PST). W przypadku wprowadzania zmiany w mierzonej akcji wskaźnik zostanie automatycznie zaktualizowany następnego dnia. Zanim zmiana zostanie odzwierciedlona w Twoim wskaźniku, może minąć do 48 godzin.

### <a name="my-score-changed-how-do-i-figure-out-why"></a>Mój wskaźnik uległ zmianie. Jak ustalić przyczynę tego?

Przejdź do [Centrum zabezpieczeń Microsoft 365](https://security.microsoft.com/), w którym znajdziesz swój kompletny wynik firmy Microsoft. Możesz łatwo zobaczyć wszystkie zmiany w bezpiecznym wyniku, przeglądając szczegółowe zmiany na karcie historia.

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>Czy bezpieczny wynik mierzy ryzyko naruszenia?

W skrócie: nie. Wynik zabezpieczony nie wyraża bezwzględnej miary tego, co jest przyczyną naruszenia. Wyraża on zakres, w jakim zostały przez Ciebie wdrożone funkcje odsuwające ryzyko naruszenia bezpieczeństwa. Żadna usługa nie może zagwarantować, że nie zostanie naruszona, a metoda zabezpieczeń nie powinna być interpretowana jako gwarancja w jakikolwiek sposób.

### <a name="how-should-i-interpret-my-score"></a>Jak mam zinterpretować swój wskaźnik?

Punkty otrzymujesz za skonfigurowanie rekomendowanych funkcji zabezpieczeń lub wykonanie zadań związanych z zabezpieczeniami (na przykład przeczytanie raportów). Niektóre akcje są oceniane za częściowe ukończenie. Należy do nich na przykład włączenie uwierzytelniania wieloskładnikowego (MFA) dla użytkowników. Bezpieczny wynik jest bezpośrednio reprezentatywny dla używanych usług zabezpieczeń firmy Microsoft. Należy pamiętać, że zabezpieczenia muszą być zrównoważone z użytecznością. Wszystkie kontrole bezpieczeństwa wpływają na użytkowników. Kontrole o niskim wpływie na użytkowników powinny mieć niewielkie przełożenie na codzienne operacje użytkowników lub nie mieć go wcale.

Aby wyświetlić historię oceny, przejdź do [Centrum zabezpieczeń Microsoft 365](https://security.microsoft.com/) i zapoznaj się z ogólnymi wynikami firmy Microsoft. Zmiany w ogólnym łącznym wyniku można przejrzeć, klikając pozycję Wyświetl historię. Wybierz konkretną datę, aby zobaczyć, które kontrole były włączone w tym dniu i jakie punkty zostały Ci przyznane za każdą z nich.

### <a name="how-does-the-identity-secure-score-relate-to-the-microsoft-365-secure-score"></a>W jaki sposób bezpieczny wskaźnik tożsamości odnosi się do Microsoft 365 zabezpieczenia?

[Microsoft Secure Score](/office365/securitycompliance/microsoft-secure-score) zawiera pięć odrębnych kategorii kontrolek i oceny:

- Tożsamość
- Dane
- Urządzenia
- Infrastruktura
- Apps

Wskaźnik zabezpieczeń tożsamości reprezentuje część tożsamości zabezpieczeń firmy Microsoft. Taka nakładanie się oznacza, że Twoje zalecenia dotyczące oceny tożsamości i oceny tożsamości w firmie Microsoft są takie same.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej na temat bezpiecznego oceny firmy Microsoft](/office365/securitycompliance/microsoft-secure-score)