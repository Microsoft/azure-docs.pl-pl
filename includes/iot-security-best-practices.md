---
title: plik dołączany
description: plik dołączany
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 4fdb891d668d99644d8a9ed9c15d158e65d53ba5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "72793127"
---
Zabezpieczanie infrastruktury Internet rzeczy (IoT) wymaga rygorystycznej strategii dotyczącej zabezpieczeń. Ta strategia wymaga zabezpieczania danych w chmurze, ochrony integralności danych podczas przesyłania przez publiczny Internet oraz bezpiecznego udostępniania urządzeń. Każda warstwa kompiluje większą gwarancję bezpieczeństwa w ogólnej infrastrukturze.

## <a name="secure-an-iot-infrastructure"></a>Zabezpieczanie infrastruktury IoT

Ta strategia z zakresu zabezpieczeń może być opracowana i wykonywana z aktywnym udziałem różnych graczy związanych z produkcją, programowaniem i wdrażaniem urządzeń i infrastruktury IoT. Poniżej znajduje się ogólny opis tych graczy.

* **Producent/Integrator sprzętu IoT**: zazwyczaj gracze są producentami wdrażanego sprzętu IoT, integratoruje sprzęt od różnych producentów lub dostawcy udostępniający sprzęt dla wdrożenia IoT wytwarzanego lub zintegrowanego przez innych dostawców.

* **Deweloper rozwiązań IoT**: rozwój rozwiązania IoT zwykle odbywa się przez dewelopera rozwiązań. Ten programista może być częścią zespołu pracującego w domu lub integratora systemu (SI) w ramach tego działania. Deweloperzy rozwiązań IoT mogą opracowywać różne składniki rozwiązania IoT od podstaw, integrować różne składniki poza półkami lub typu open-source lub przyjmować Akceleratory rozwiązań z niewielkim adaptacją.

* **Wdrażanie rozwiązań IoT**: po opracowaniu rozwiązania IoT należy je wdrożyć w tym polu. Ten proces obejmuje wdrożenie sprzętu, połączenia urządzeń oraz wdrażanie rozwiązań w urządzeniach sprzętowych lub w chmurze.

* **Operator rozwiązania IoT**: po wdrożeniu rozwiązania IoT wymagane są długoterminowe operacje, monitorowanie, uaktualnienia i konserwacja. Te zadania mogą być wykonywane przez zespół pracujący w firmie, który obejmuje specjalistów technologicznych, operacje sprzętowe i zespoły konserwacyjne oraz specjalistów ds. domen, którzy monitorują prawidłowe zachowanie ogólnej infrastruktury IoT.

Poniższe sekcje zawierają najlepsze rozwiązania dla każdego z tych graczy, które ułatwiają tworzenie, wdrażanie i obsługiwanie bezpiecznej infrastruktury IoT.

## <a name="iot-hardware-manufacturerintegrator"></a>Producent/Integrator sprzętu IoT

Poniżej przedstawiono najlepsze rozwiązania dla producentów sprzętu IoT i integratorów sprzętu.

* **Zakres sprzętu do minimalnych wymagań**: projekt sprzętu powinien zawierać minimalne funkcje wymagane do działania sprzętu i nic więcej. Przykładem jest dołączenie portów USB tylko w razie potrzeby do działania urządzenia. Te dodatkowe funkcje otwierają urządzenie w poszukiwaniu niepożądanych wektorów ataków, które powinny być nieuniknione.

* **Sprawdź**poprawność sprzętu: Kompiluj w mechanizmy wykrywające fizyczne manipulowanie, takie jak otwieranie urządzenia lub usuwanie części urządzenia. Te sygnały naruszenia mogą być częścią strumienia danych przekazanego do chmury, co może spowodować wygenerowanie alertów dla tych zdarzeń.

* **Kompiluj na bezpiecznym sprzęcie**: Jeśli KWS zezwala, funkcje zabezpieczeń, takie jak bezpieczny i zaszyfrowany magazyn, lub funkcje rozruchu oparte na moduł TPM (TPM). Te funkcje zwiększają bezpieczeństwo urządzeń i zapewniają ochronę ogólnej infrastruktury IoT.

* **Zabezpieczanie uaktualnień**: aktualizacje oprogramowania układowego w trakcie okresu istnienia urządzenia są nieuniknione. Tworzenie urządzeń z bezpiecznymi ścieżkami dla uaktualnień i gwarancja kryptograficzna wersji oprogramowania układowego umożliwi zabezpieczenie urządzenia podczas uaktualnień i po ich zakończeniu.

## <a name="iot-solution-developer"></a>Deweloper rozwiązań IoT

Poniżej przedstawiono najlepsze rozwiązania dla deweloperów rozwiązań IoT:

* **Postępuj zgodnie z metodologią opracowywania bezpiecznych oprogramowania**: Programowanie bezpiecznego oprogramowania wymaga założenia zabezpieczeń, od chwili rozpoczęcia projektu do jego implementacji, testowania i wdrażania. Wszystkie platformy, Języki i narzędzia mają wpływ na tę metodologię. Cykl programowania w zakresie zabezpieczeń firmy Microsoft zawiera podejście krok po kroku dotyczące tworzenia bezpiecznego oprogramowania.

* **Wybierz oprogramowanie Open Source z opieką**: oprogramowanie Open Source oferuje możliwość szybkiego tworzenia rozwiązań. Wybierając oprogramowanie typu "open source", należy wziąć pod uwagę poziom aktywności społeczności dla każdego składnika typu "open source". Aktywna społeczność gwarantuje, że oprogramowanie jest obsługiwane i że problemy są wykrywane i rozwiązane. Alternatywnie, zaciemnienie i nieaktywne projekty oprogramowania Open Source mogą nie być obsługiwane i problemy nie są prawdopodobnie odnajdywane.

* **Integracja z opieką**: wiele wad zabezpieczeń oprogramowania istnieje na granicy bibliotek i interfejsów API. Funkcje, które mogą nie być wymagane dla bieżącego wdrożenia, mogą być nadal dostępne za pośrednictwem warstwy interfejsu API. Aby zapewnić ogólne zabezpieczenia, upewnij się, że wszystkie interfejsy składników są zintegrowane z lukami w zabezpieczeniach.

## <a name="iot-solution-deployer"></a>Wdrażanie rozwiązań IoT

Poniżej przedstawiono najlepsze rozwiązania dotyczące wdrażania rozwiązań IoT:

* **Bezpieczne wdrażanie sprzętu**: wdrożenia IoT mogą wymagać wdrożenia sprzętu w niezabezpieczonych lokalizacjach, takich jak publiczne miejsca lub nienadzorowane ustawienia regionalne. W takich sytuacjach należy upewnić się, że wdrożenie sprzętowe nie jest w maksymalnym zakresie. Jeśli na sprzęcie są dostępne porty USB lub inne, upewnij się, że są one chronione w sposób bezpieczny. Wiele wektorów ataków może korzystać z nich jako punktów wejścia.

* **Zabezpieczanie kluczy uwierzytelniania**: podczas wdrażania każde urządzenie wymaga identyfikatorów urządzeń i skojarzonych kluczy uwierzytelniania generowanych przez usługę w chmurze. Przechowuj te klucze fizycznie bezpiecznie, nawet po wdrożeniu. Każdy złamany klucz może być używany przez złośliwe urządzenie do zamaskowanego jako istniejące urządzenie.

## <a name="iot-solution-operator"></a>Operator rozwiązania IoT

Poniżej przedstawiono najlepsze rozwiązania dotyczące operatorów rozwiązań IoT:

* **Dbaj**o aktualność systemu: Upewnij się, że systemy operacyjne urządzeń i wszystkie sterowniki urządzeń są uaktualnione do najnowszych wersji. Jeśli włączysz aktualizacje automatyczne w systemie Windows 10 (IoT lub innych jednostkach SKU), firma Microsoft zachowuje aktualność, zapewniając bezpieczny system operacyjny dla urządzeń IoT. Utrzymywanie Aktualności innych systemów operacyjnych (takich jak Linux) zapewnia również ochronę przed złośliwymi atakami.

* **Ochrona przed złośliwymi działaniami**: w przypadku zezwolenia na system operacyjny Zainstaluj najnowsze funkcje oprogramowania antywirusowego i chroniącego przed złośliwym kodem w poszczególnych systemach operacyjnych urządzeń. To rozwiązanie może pomóc w ograniczeniu ryzyka związanego z większością zewnętrznych zagrożeń. Większość nowoczesnych systemów operacyjnych można chronić przed zagrożeniami, wykonując odpowiednie kroki.

* **Inspekcja często**: Inspekcja infrastruktury IoT pod kątem problemów związanych z zabezpieczeniami polega na kluczu odpowiedzi na zdarzenia związane z bezpieczeństwem. Większość systemów operacyjnych zapewnia wbudowane rejestrowanie zdarzeń, które należy przejrzeć często, aby upewnić się, że nie nastąpiło naruszenie zabezpieczeń. Informacje o inspekcji mogą być wysyłane jako oddzielne strumienie telemetrii do usługi w chmurze, gdzie można ją przeanalizować.

* **Fizyczna ochrona infrastruktury IoT**: najgorsze ataki zabezpieczeń dotyczące infrastruktury IoT są uruchamiane przy użyciu fizycznego dostępu do urządzeń. Ważną kwestią bezpieczeństwa jest ochrona przed złośliwym użyciem portów USB i innego dostępu fizycznego. Jednym z kluczy do odkrywania naruszeń, które mogły wystąpić, jest rejestrowanie dostępu fizycznego, takie jak użycie portu USB. Ponownie system Windows 10 (IoT i inne jednostki SKU) umożliwia szczegółowe rejestrowanie tych zdarzeń.

* **Ochrona poświadczeń w chmurze**: poświadczenia uwierzytelniania w chmurze używane do konfigurowania i obsługi wdrożenia IoT są prawdopodobnie Najprostszym sposobem uzyskania dostępu i naruszenia bezpieczeństwa systemu IoT. Aby chronić poświadczenia, należy często zmieniać hasło i zrezygnować z używania tych poświadczeń na komputerach publicznych.

Możliwości różnych urządzeń IoT są różne. Niektóre urządzenia mogą być komputerami z typowymi systemami operacyjnymi stacjonarnymi, a niektóre urządzenia mogą mieć bardzo lekkie systemy operacyjne. Najlepsze rozwiązania w zakresie zabezpieczeń mogą dotyczyć tych urządzeń w różnych stopniach. Jeśli zostały spełnione, należy postępować zgodnie z dodatkowymi rozwiązaniami dotyczącymi zabezpieczeń i wdrażania.

Niektóre starsze i ograniczone urządzenia mogą nie zostać zaprojektowane specjalnie na potrzeby wdrażania IoT. Te urządzenia mogą nie mieć możliwości szyfrowania danych, nawiązywania połączenia z Internetem lub zapewniają zaawansowaną inspekcję. W takich przypadkach nowoczesne i bezpieczne bramy pól można agregować dane ze starszych urządzeń i zapewnić zabezpieczenia wymagane do łączenia tych urządzeń za pośrednictwem Internetu. Bramy pól mogą zapewniać bezpieczne uwierzytelnianie, negocjowanie szyfrowanych sesji, otrzymywanie poleceń z chmury i wiele innych funkcji zabezpieczeń.