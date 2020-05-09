---
title: Wskazówki dotyczące prywatności asystentów głosowych w systemie Windows
titleSuffix: Azure Cognitive Services
description: Instrukcje włączania aktywacji głosowej dla agenta głosowego są domyślnie włączone.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 436367ede4f4be323b5334a201b1c9fb8f7f28e8
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997514"
---
# <a name="privacy-guidelines-for-voice-assistants-on-windows"></a>Wskazówki dotyczące prywatności asystentów głosowych w systemie Windows

Należy pamiętać, że użytkownicy uzyskują jasne informacje o sposobie zbierania i używania ich danych głosowych oraz o tym, że mają one kontrolę nad tym, czy i jak odbywa się ta kolekcja. Te podstawowe aspekty dotyczące prywatności — *ujawnienie* i *zgoda* — są szczególnie ważne w przypadku asystentów głosowych zintegrowanych z systemem Windows, które mają zapewniony zawsze nasłuchiwanie.

Deweloperzy tworzący asystentów głosowych w systemie Windows muszą zawierać jasne elementy interfejsu użytkownika w aplikacjach, które odzwierciedlają możliwości nasłuchiwania przez asystenta.

> [!NOTE]
> Niedostarczenie odpowiedniego ujawnienia i zgody dla aplikacji asystenta, w tym po aktualizacji aplikacji, może spowodować, że asystent przestanie być dostępny do aktywacji głosowej do momentu rozwiązania problemów z ochroną prywatności. 

## <a name="minimum-requirements-for-feature-inclusion"></a>Minimalne wymagania dotyczące dołączania funkcji

Użytkownicy systemu Windows mogą wyświetlać i kontrolować dostępność aplikacji asystenta w programie **`Settings > Privacy > Voice activation`**.

 > [!div class="mx-imgBorder"]
 > [![Prywatność — lista aplikacji](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png "Wpis ustawienia prywatności aktywacji głosu systemu Windows dla aplikacji Asystenta")](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png#lightbox)

Aby można było korzystać z tej listy, aplikacja musi:

1. Zapoznaj się z tym, że użytkownicy będą nasłuchiwać słowa kluczowego, nawet jeśli aplikacja nie jest uruchomiona, a co to jest słowo kluczowe
1. Dołącz opis sposobu użycia danych głosowych użytkownika, w tym link lub odwołanie do odpowiednich zasad ochrony prywatności
1. Poinformuj użytkowników, że oprócz dowolnych ustawień w aplikacji użytkownicy mogą wyświetlać i modyfikować opcje prywatności w programie **`Settings > Privacy > Voice activation`**, opcjonalnie dołączając link do protokołu do `ms-settings:privacy-voiceactivation` bezpośredniego dostępu

Po spełnieniu tych wymagań i uzyskaniu zgody od firmy Microsoft aplikacja asystenta zostanie wyświetlona na liście aplikacji do aktywacji głosowej po jej `Windows.ApplicationModel.ConversationalAgent` zarejestrowaniu w interfejsach API, a użytkownicy będą mogli wyrazić zgodę na aktywację aplikacji za pomocą słowa kluczowego. Domyślnie oba te ustawienia są `Off` i wymagają od użytkownika ręcznego odwiedzania strony ustawień w celu włączenia.

> [!NOTE]
> We wszystkich przypadkach uprawnienie do aktywacji głosu wymaga uprawnień do mikrofonu. Jeśli aplikacja Asystenta nie ma dostępu do mikrofonu, nie będzie kwalifikować się do aktywacji głosu i zostanie wyświetlona w ustawieniach prywatności aktywacji głosu w stanie wyłączenia.

## <a name="additional-requirements-for-inclusion-in-microphone-consent"></a>Dodatkowe wymagania dotyczące włączania w przypadku zgody na mikrofon

Autorzy asystentów, którzy chcą ułatwić i gładszy, aby użytkownicy mogli zrezygnować z aktywacji głosu, mogą wykonać te czynności, spełniając kilka dodatkowych wymagań. Po pomyślnym wykonaniu tych opcji standardowe ustawienia aktywacji głosu odblokowywane przez urządzenia będą domyślnie dostępne `On` tylko raz (i tylko raz) dostęp do mikrofonu zostanie udzielony dla aplikacji. Eliminuje to konieczność dodatkowej podróży do ustawień przed aktywowaniem asystenta przez głos.

Dodatkowe wymagania polegają na tym, że aplikacja asystenta musi:

1. **Przed** wyświetleniem monitu o zgodę na korzystanie z mikrofonu `AppCapability.RequestAccessAsync` (na przykład za pomocą interfejsu API), podaj wyraźne wskazanie użytkownikowi, że aplikacja asystenta chce słuchać słowa kluczowego użytkownika, nawet jeśli aplikacja nie jest uruchomiona, a wolisz o zgodzie użytkownika
2. Dołącz wszystkie istotne informacje dotyczące użycia danych i zasad ochrony prywatności **przed** zażądaniem dostępu do mikrofonu lub `Windows.ApplicationModel.ConversationalAgent` użyciem interfejsów API
3. Unikaj żadnej dyrektywy ani wiodących wyrazów (na przykład "kliknij przycisk tak w następnym monicie") w przepływie pracy Odblokuj zachowanie przechwytywania audio i zażądaj uprawnienia

Po spełnieniu tych wymagań aplikacja kwalifikującego się asystenta zostanie wyświetlona na liście aplikacji, które kwalifikują się do `enabled` aktywacji głosu w stanie po udzieleniu dostępu do mikrofonu.

> [!NOTE]
> Aktywacja głosowa powyżej blokady nie kwalifikuje się do automatycznego włączenia z dostępem do mikrofonu i nadal będzie wymagać od użytkownika odwiedzenia strony prywatność aktywacji głosowej w celu włączenia dostępu do blokady powyżej.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Poznaj najlepsze rozwiązania dla asystentów głosowych w systemie Windows](windows-voice-assistants-best-practices.md)