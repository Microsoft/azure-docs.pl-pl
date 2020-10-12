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
ms.openlocfilehash: a9fe083818c5850ad05556b18911c19fe19e0e72
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90987395"
---
# <a name="privacy-guidelines-for-voice-assistants-on-windows"></a>Wskazówki dotyczące prywatności asystentów głosowych w systemie Windows

Należy pamiętać, że użytkownicy uzyskują jasne informacje o sposobie zbierania i używania ich danych głosowych oraz o tym, że mają one kontrolę nad tym, czy i jak odbywa się ta kolekcja. Te podstawowe aspekty dotyczące prywatności — *ujawnienie* i *zgoda* — są szczególnie ważne w przypadku asystentów głosowych zintegrowanych z systemem Windows, które mają zapewniony zawsze nasłuchiwanie.

Deweloperzy tworzący asystentów głosowych w systemie Windows muszą zawierać jasne elementy interfejsu użytkownika w aplikacjach, które odzwierciedlają możliwości nasłuchiwania przez asystenta.

> [!NOTE]
> Niedostarczenie odpowiedniego ujawnienia i zgody dla aplikacji asystenta, w tym po aktualizacji aplikacji, może spowodować, że asystent przestanie być dostępny do aktywacji głosowej do momentu rozwiązania problemów z ochroną prywatności.

## <a name="minimum-requirements-for-feature-inclusion"></a>Minimalne wymagania dotyczące dołączania funkcji

Użytkownicy systemu Windows mogą wyświetlać i kontrolować dostępność aplikacji asystenta w programie **`Settings > Privacy > Voice activation`** .

 > [!div class="mx-imgBorder"]
 > [![Zrzut ekranu przedstawia opcje sterujące dostępności Cortany. ](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png "Wpis ustawienia prywatności aktywacji głosu systemu Windows dla aplikacji Asystenta")](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png#lightbox)

Aby można było korzystać z tej listy, skontaktuj się z firmą Microsoft w winvoiceassistants@microsoft.com celu rozpoczęcia pracy. Domyślnie użytkownicy będą musieli jawnie włączyć aktywację głosu dla nowego asystenta w programie, w **`Settings > Privacy > Voice Activation`** którym aplikacja może łączyć się z usługą `ms-settings:privacy-voiceactivation` . Gdy aplikacja zostanie uruchomiona i używała interfejsów API, zostanie wyświetlona na liście dozwolonych aplikacji `Windows.ApplicationModel.ConversationalAgent` . Ustawienia aktywacji głosu będą modyfikowane, gdy aplikacja uzyska zgodę na mikrofon od użytkownika.

Ponieważ ustawienia prywatności systemu Windows zawierają informacje o tym, jak działa Aktywacja głosowa i ma standardowy interfejs użytkownika do kontrolowania uprawnień, ujawniania i zgody są spełnione. Asystent pozostanie na tej liście dozwolonych, o ile nie będzie:

* Wprowadzając w błąd lub Niewiadom użytkownika o aktywacji głosu lub obsłudze danych głosowych przez Asystenta
* Niesłuszne zakłócanie pracy z innym asystentem
* Przerwij wszystkie inne odpowiednie zasady firmy Microsoft

Jeśli którykolwiek z powyższych elementów zostanie odnaleziony, firma Microsoft może usunąć z listy dozwolonych asystenta, dopóki problemy nie zostaną rozwiązane.

> [!NOTE]
> We wszystkich przypadkach uprawnienie do aktywacji głosu wymaga uprawnień do mikrofonu. Jeśli aplikacja Asystenta nie ma dostępu do mikrofonu, nie będzie kwalifikować się do aktywacji głosu i zostanie wyświetlona w ustawieniach prywatności aktywacji głosu w stanie wyłączenia.

## <a name="additional-requirements-for-inclusion-in-microphone-consent"></a>Dodatkowe wymagania dotyczące włączania w przypadku zgody na mikrofon

Autorzy asystentów, którzy chcą ułatwić i gładszy, aby użytkownicy mogli zrezygnować z aktywacji głosowej, mogą to zrobić, spełniając dodatkowe wymagania w celu odpowiedniego spełnienia ujawniania i zgody bez dodatkowej podróży do strony ustawień. Po zatwierdzeniu aktywacja głosu stanie się dostępna natychmiast po udzieleniu użytkownikowi uprawnienia do mikrofonu do aplikacji asystenta. Aby można było zakwalifikować się do tego, aplikacja asystenta musi wykonać następujące czynności **przed** wyświetleniem monitu o zgodę na korzystanie z mikrofonu (na przykład za pomocą `AppCapability.RequestAccessAsync` interfejsu API):

1. Podaj jasne i wyraźne wskazanie użytkownikowi, że aplikacja chce nasłuchiwać głosu dla słowa kluczowego użytkownika, *nawet jeśli aplikacja nie jest uruchomiona*, a użytkownik chce wyrazić zgodę
1. Podaj odpowiednie informacje dotyczące użycia danych i zasad ochrony prywatności, takie jak link do oficjalnych deklaracji zachowania poufności informacji
1. Unikaj żadnej dyrektywy ani wiodących wyrazów (na przykład "kliknij przycisk tak w następnym monicie") w przepływie pracy, który zachowanie funkcji przechwytywania dźwięku

Jeśli aplikacja wykonuje wszystkie powyższe działania, kwalifikuje się do włączenia funkcji aktywacji głosu przy użyciu zgody na korzystanie z mikrofonu. Skontaktuj się z pomocą techniczną winvoiceassistants@microsoft.com , aby uzyskać więcej informacji i zapoznać się z pierwszym użyciem.

> [!NOTE]
> Aktywacja głosowa powyżej blokady nie kwalifikuje się do automatycznego włączenia z dostępem do mikrofonu i nadal będzie wymagać od użytkownika odwiedzenia strony prywatność aktywacji głosowej w celu włączenia dostępu do blokady powyżej.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Poznaj najlepsze rozwiązania dla asystentów głosowych w systemie Windows](windows-voice-assistants-best-practices.md)