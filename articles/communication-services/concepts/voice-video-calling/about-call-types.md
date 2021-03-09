---
title: Koncepcje głosowe i wideo w usłudze Azure Communications Services
titleSuffix: An Azure Communication Services concept document
description: Dowiedz się więcej o typach wywołań usług komunikacyjnych.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 66a5ed65b0582aafd641473163e1b273d4a4263d
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102489654"
---
# <a name="voice-and-video-concepts"></a>Pojęcia dotyczące głosu i wideo

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


Usługi Azure Communications Services umożliwiają udostępnianie i odbieranie jednego do jednego lub grupy wywołań głosowych i wideo. Twoje wywołania mogą być nawiązywane z innymi urządzeniami połączonymi z Internetem i zwykłymi numerami telefonów. Za pomocą bibliotek klienckich usług komunikacyjnych w języku JavaScript, Android lub iOS można tworzyć aplikacje, które umożliwiają użytkownikom głosowanie na siebie w prywatnych konwersacjach lub w dyskusjach grupowych. Usługi komunikacyjne platformy Azure obsługują wywołania do i z usług lub botów.

## <a name="call-types-in-azure-communication-services"></a>Typy wywołań w usłudze Azure Communications Services

Istnieje wiele typów wywołań, które można wprowadzić w usłudze Azure Communications Services. Typ wywoływanych wywołań określa schemat sygnalizowania, przepływy ruchu multimedialnego i model cen.

### <a name="voice-over-ip-voip"></a>Voice over IP (VoIP)

Gdy użytkownik aplikacji wywołuje innego użytkownika aplikacji za pośrednictwem połączenia internetowego lub danych, wywołanie jest nawiązywane za pośrednictwem protokołu Voice over IP (VoIP). W tym przypadku zarówno sygnalizowanie, jak i przepływy multimediów przez Internet.

### <a name="public-switched-telephone-network-pstn"></a>Publiczna przełączona sieć telefoniczna (PSTN)

Za każdym razem, gdy użytkownicy współpracują z tradycyjnym numerem telefonu, wywołania są obsługiwane przez telefon PSTN (publiczne przełączane sieci telefoniczne). Aby móc tworzyć i odbierać wywołania PSTN, należy dodać funkcje telefonii do zasobu usług Azure Communications Services. W takim przypadku sygnalizowanie i nośniki używają kombinacji technologii opartych na protokole IP i PSTN do łączenia użytkowników.

### <a name="one-to-one-call"></a>Wywołanie "jeden do jednego"

Wywołanie "jeden do jednego" w usłudze Azure Communications Services ma miejsce, gdy jeden z użytkowników nawiązuje połączenie z innym użytkownikiem przy użyciu jednej z naszych bibliotek klienckich. Wywołaniem może być VoIP lub PSTN.

### <a name="group-call"></a>Wywołanie grupy

Wywołanie grupy w usłudze Azure Communications Services odbywa się, gdy trzy lub więcej osób komunikują się ze sobą. Dowolna kombinacja połączeń VoIP i użytkowników połączonych z siecią PSTN może być obecna w wywołaniu grupy. Wywołanie "jeden do jednego" można przekonwertować na wywołanie grupy przez dodanie kolejnych uczestników wywołania. Jednym z tych uczestników może być bot.

### <a name="supported-video-standards"></a>Obsługiwane standardy wideo
Obsługujemy H. 264 (MPEG-4)

### <a name="video-quality"></a>Jakość wideo 
Obsługujemy do pełnej rozdzielczości HD w natywnych zestawach SDK (iOS, Android). W przypadku zestawu SDK dla sieci Web (JS) jest obsługiwany standardowy dysk HD 720. Jakość zależy od dostępnej przepustowości.

### <a name="rooms-concept"></a>Koncepcje pokojów
Pokoje są zestawem interfejsów API i zestawów SDK, które umożliwiają łatwe dodawanie funkcji audio, wideo, udostępniania ekranu, sieci PSTN i wiadomości SMS do witryny internetowej lub aplikacji natywnych.
W trakcie korzystania z wersji zapoznawczej można użyć identyfikatora grupy do dołączenia do tej samej konwersacji. Można utworzyć dowolną liczbę identyfikatorów grup i oddzielić użytkowników od "pokojów". Przechodzenie do przodu spowoduje zwiększenie kontroli wokół "pokojów"

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wprowadzenie do wywoływania](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Aby uzyskać więcej informacji, zobacz następujące artykuły:
- Zapoznaj się z ogólnymi [przepływami wywołań](../call-flows.md)
- [Typy numerów telefonów](../telephony-sms/plan-solution.md)
- Dowiedz się więcej na temat [możliwości wywoływania biblioteki klienta](../voice-video-calling/calling-sdk-features.md)
