---
title: Hostowanie kodu źródłowego aplikacji mobilnej w chmurze za pomocą usług GitHub i Azure DevOps
description: Dowiedz się więcej o usługach do hostowania kodu aplikacji mobilnej w chmurze za pomocą usług firmy Microsoft.
author: codemillmatt
ms.assetid: 12a8a079-9b3c-4faf-2222-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: fdac28d50b9e03445c128a87a850294ba69fcaf9
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2020
ms.locfileid: "84450899"
---
# <a name="cloud-hosted-source-code-management-services"></a>Usługi zarządzania kodem źródłowym hostowane w chmurze
Jeśli masz zespoły deweloperów mające wielu członków zespołu, którzy pracują nad tą samą bazą kodu, musisz znaleźć odpowiednie miejsce do hostowania kodu. Przechowywanie danych w chmurze i posiadanie scentralizowanego repozytorium umożliwia wszystkim użytkownikom przekazywanie i edytowanie plików kodu oraz zarządzanie nimi. Mogą również współistnieć z innymi deweloperami w projektach. Kod może być łatwo dostępny niezależnie od tego, czy masz połączenie z Internetem.

Hosting w chmurze jest znacznie łatwiejszy niż w przypadku opcji lokalnych. Wymaga to mniejszej konfiguracji sprzętowej i umożliwia organizacjom ukończenie procesu implementacji w bardziej Agile sposób.

## <a name="benefits-of-hosting-source-code-in-the-cloud"></a>Zalety hostingu kodu źródłowego w chmurze
- **Scentralizowany magazyn w chmurze:** Wyświetlaj dane z dowolnego miejsca i zarządzaj nimi.
- **Lepsza współpraca i kod czyszczący:** Śledź kod w zespołach i Zarządzaj projektami w celu zapewnienia ciągłego dostarczania doskonałego oprogramowania.
- **Łatwiejsze do pobrania:** Łatwe Współtworzenie projektów.
- **Szybszy cykl wydania:** Pracuj szybciej w zespołach i łatwo twórz swoje projekty.
- **Zmniejsz koszt:** Nie martw się o utrzymywanie własnego sprzętu, serwerów, sieci VPN i tak dalej.

Użyj następujących usług, aby hostować dane aplikacji w chmurze.

## <a name="github"></a>GitHub
[GitHub](https://github.com/) to usługa hostingu repozytorium typu "open source", która obsługuje projekty kodu źródłowego w różnych różnych językach programowania. GitHub śledzi różne zmiany wprowadzone w każdej iteracji.

**Najważniejsze funkcje**
- Użyj hostingu kodu, aby zachować cały kod w jednym miejscu. Repozytoria prywatne, publiczne i open source są wyposażone w narzędzia ułatwiające hostowanie, wersję i wydanie kodu.
- Przejrzyj kod i Użyj wbudowanych narzędzi do przeglądu, aby sprawdzić, czy kod jest istotną częścią procesu zespołu:
    - Ochrona gałęzi, proponowanie zmian i przeglądanie żądań.
    - Różnice w miejscu, komentarze w kontekście i uzyskaj jasne Opinie.
- Koordynuj wczesne i niezrównane działania i Dowiedz się więcej dzięki narzędziom do zarządzania projektami w usłudze GitHub:
    - Zapoznaj się z dużym obrazem projektu.
    - Skorzystaj z tablic zadań, które znajdują się bezpośrednio obok kodu w usłudze GitHub.
    - Przeciągnij karty, aby przypisać problemy lub żądania ściągnięcia do członków zespołu.
    - Ustaw punkty kontrolne, aby organizować i śledzić postęp.
    - Zapisuj uwagi dotyczące pomysłów do przechwytywania, które mogą być przydatne, ale nie należy do określonego problemu lub żądania ściągnięcia.
- Łatwo Odkrywaj i wybieraj odpowiednie narzędzia, aby zapewnić lepszą komunikację i automatyzację pracy, kupując aplikacje z [witryny GitHub Marketplace](https://github.com/marketplace).
- Zarządzanie zespołami i ich rozwój przy użyciu: 
    - Role użytkowników ułatwiające organizowanie zespołów i uprawnień dostępu.
    - Narzędzia wątku dyskusji umożliwiające zachowanie konwersacji w temacie i zespole.
    - Wytyczne społeczności umożliwiające szybkie konfigurowanie nowych członków zespołu przy użyciu konta.
- Przeglądaj i gwiazdy popularne projekty, aby je obserwować.
- Sieci i Dowiedz się od innych osób w branży.

**Dokumentacja**
- [GitHub](https://github.com/)
- [Przewodniki GitHub](https://guides.github.com/)
- [Forum społeczności GitHub](https://github.community/)
- [Portal GitHub Marketplace](https://github.com/marketplace)

## <a name="azure-devops"></a>Azure DevOps
[Usługa Azure DevOps](https://azure.microsoft.com/services/devops/) obsługuje [Azure Repos](https://azure.microsoft.com/services/devops/repos/) i [Kontrola wersji serwera Team Foundation (TFVC)](https://docs.microsoft.com/azure/devops/repos/tfvc/index?view=azure-devops) jako opcje kontroli źródła. Ma nieograniczone bezpłatne repozytoria prywatne z zespołowymi przeglądami kodu, zaawansowanym zarządzaniem plikami, wyszukiwaniem kodu i zasadami rozgałęzień w celu zapewnienia wysokiej jakości kodu. Azure Repos doskonale nadaje się dla małych projektów i dużych organizacji, które wymagają obsługi natywnych Azure Active Directory i zaawansowanych zasad.
    
**Najważniejsze funkcje**
- Używaj nieograniczonego, hostowanego w chmurze repozytorium kodu źródłowego git dla Twoich repozytoriów publicznych i prywatnych:
    - Uzyskaj pomoc techniczną dla dowolnego klienta git.
    - Użyj elementów webhook i integracji interfejsu API.
- Rozpocznij od następnej kompilacji od żądania ściągnięcia repozytorium:
    - Współpracuj nad tworzeniem lepszego kodu przy użyciu dyskusji wielowątkowej i ciągłej integracji dla każdej zmiany.
    - Skonfiguruj ciągłą integrację/ciągłe dostarczanie (CI/CD), aby automatycznie wyzwalać kompilacje, testy i wdrożenia przy użyciu każdego zakończonego żądania ściągnięcia. Możesz użyć Azure Pipelines lub narzędzi.
    - Ochrona jakości kodu za pomocą zasad gałęzi.
- Obsługuj scentralizowany system kontroli wersji za pomocą Kontrola wersji serwera Team Foundation, który obejmuje przegląd kodu.
- Nawiąż połączenie z kodem przy użyciu Xcode, zaćmienia, IntelliJ, Android Studio, Visual Studio, Visual Studio Code i innych.
- Korzystaj z zaawansowanego wyszukiwania kodu semantycznego.
- Uzyskaj korzyści z funkcji gotowych dla przedsiębiorstw. Usługa Azure DevOps ma natywną integrację z usługą Azure Active Directory, która upraszcza proces zarządzania dostępem do repozytoriów kodu.
- Zapewnianie jakości kodu za pomocą zasad gałęzi, takich jak minimalna liczba przeglądów kodu, wymagania dotyczące udanych kompilacji oraz wymuszanie strategii scalania git.
- Połącz swoje ulubione środowisko programistyczne, aby uzyskać dostęp do repozytoriów i zarządzać pracą.

**Dokumentacja**
- [Wprowadzenie do Azure Repos](https://azure.microsoft.com/services/devops/repos/) 
- [Dokumentacja Azure Repos](/azure/devops/repos/?view=azure-devops)