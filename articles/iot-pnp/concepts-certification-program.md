---
title: Program urządzenia z certyfikatem platformy Azure | Microsoft Docs
description: Informacje o programie certyfikowanych urządzeń platformy Azure.
author: koichih
ms.author: koichih
ms.date: 09/25/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 9580f1c7a29b19bb926a7079b95cb3ed0b314d05
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91580033"
---
# <a name="what-is-the-azure-certified-device-program"></a>Co to jest program urządzenia z certyfikatem platformy Azure?

Program certyfikowany na platformie Azure zapewnia bezproblemową współpracę z usługami platformy Azure. Program korzysta z narzędzi, usług i portalu Marketplace w celu udostępnienia wiedzy branżowej i najlepszych rozwiązań dla społeczności użytkowników urządzeń i konstruktorów rozwiązania.

Ten program jest przeznaczony do:

- **Zapewnij klientom pewność:** Klienci mogą bezpiecznie zakupić urządzenia certyfikowane przez firmę Microsoft do użycia z usługami platformy Azure.

- **Pomóż klientom znaleźć odpowiednie urządzenia dla swoich rozwiązań:** Konstruktory urządzeń mogą publikować unikatowe możliwości swoich urządzeń w ramach procesu certyfikacji. Klienci mogą łatwo znaleźć produkty, które pasują do swoich potrzeb.

- **Promuj certyfikowane urządzenia:** Konstruktory urządzeń mogą uzyskać lepszą widoczność, kontaktować się z klientami i korzystać z marki urządzenia z certyfikatem platformy Azure.

W tym artykule opisano, jak:

- Dołącz firmę do programu Device Certified dla systemu Azure.
- Określ, której certyfikacji dotyczy Twoje urządzenie.
- Znajdź wymagania programu i inne zasoby, aby rozpocząć pracę z testowaniem.
- Sprawdź poprawność urządzenia za pomocą portalu certyfikowanego urządzenia platformy Azure.

## <a name="onboarding"></a>Dołączanie

Aby certyfikowania urządzeń w [portalu Azure Certified Device](https://aka.ms/acdp), należy wykonać następujące czynności:

1. Upewnij się, że Twoja firma ma konto Azure Active Directory przy użyciu dzierżawy służbowej.
2. Dołącz do [Microsoft Partner Network (MPN)](https://partner.microsoft.com/) przy użyciu swojego konta.
3. Zaloguj się do [portalu urządzenia z certyfikatem platformy Azure](https://aka.ms/acdp) po dołączeniu do usługi MPN.
4. Przejrzyj i podpisz [umowę programu](https://aka.ms/acdagreement) na stronie profil firmy

### <a name="company-profile"></a>Profil firmy

Aby zarządzać profilem firmy w portalu urządzenia z certyfikatem platformy Azure, wybierz pozycję **profil firmy**. Profil firmy zawiera adres URL, adres e-mail i logo firmy. Zaakceptuj umowę programu na tej stronie przed kontynuowaniem wszelkich operacji certyfikacji.

Na stronie opis urządzenia w katalogu certyfikowane urządzenia Azure są wykorzystywane informacje o profilu firmy.

## <a name="choose-the-certification"></a>Wybierz certyfikat

Istnieją trzy różne certyfikaty, z których każdy koncentruje się na dostarczaniu innej wartości klienta. W zależności od typu używanego urządzenia i odbiorców docelowych można wybrać najbardziej odpowiednie certyfikaty lub certyfikaty:

### <a name="azure-certified-device"></a>Certyfikowane urządzenie platformy Azure

_Certyfikowanie urządzeń z certyfikatem platformy Azure_ sprawdza, czy urządzenie może nawiązać połączenie z usługą Azure IoT Hub i bezpiecznie udostępniać je za pomocą usługi Device Provisioning (DPS). Ten certyfikat odzwierciedla funkcjonalność i współdziałanie urządzenia, które jest niezbędną linią bazową dla bardziej zaawansowanych certyfikatów.

- Aby dowiedzieć się więcej, zapoznaj się z [wymaganiami dotyczącymi certyfikacji](https://aka.ms/acdrequirements).
- Aby dowiedzieć się więcej o używaniu usługi DPS do łączenia urządzenia z usługą Azure IoT Hub, zobacz [Omówienie urządzeń aprowizacji](../iot-dps/about-iot-dps.md).

### <a name="iot-plug-and-play"></a>IoT Plug and Play

_Certyfikacja Plug and Play IoT_, przyrostowa Certyfikacja certyfikatu urządzenia z certyfikatem platformy Azure, upraszcza proces kompilowania urządzeń bez niestandardowego kodu urządzenia. Plug and Play IoT umożliwia partnerom sprzętu tworzenie urządzeń, które można łatwo zintegrować z rozwiązaniami w chmurze opartymi na platformie Azure IoT Central i rozwiązaniach innych firm.

- Aby dowiedzieć się więcej, zapoznaj się z [wymaganiami dotyczącymi certyfikacji](https://aka.ms/acdiotpnprequirements).
- Aby dowiedzieć się więcej na temat przygotowywania urządzenia do testowania Plug and Play IoT, zobacz [jak certyfikowanie urządzeń Plug and Play IoT](howto-certify-device.md).

### <a name="edge-managed"></a>Zarządzane przez brzeg

_Certyfikacja zarządzana_za pomocą programu Edge, przyrostowa Certyfikacja certyfikatu urządzenia z certyfikatem platformy Azure, koncentruje się na standardach zarządzania urządzeniami dla urządzeń z systemem Windows, Linux lub RTO. Obecnie ta certyfikacja programu koncentruje się na zgodności środowiska uruchomieniowego Edge w przypadku wdrażania modułów i zarządzania nimi.

> [!TIP]
> Ten program był wcześniej znany jako _program certyfikacji IoT Edge_.

- Aby dowiedzieć się więcej, zapoznaj się z [wymaganiami dotyczącymi certyfikacji](https://aka.ms/acdedgemanagedrequirements).
- Aby dowiedzieć się więcej na temat IoT Edge, zobacz [Omówienie usługi IoT Edge](../iot-edge/about-iot-edge.md).
- Aby dowiedzieć się więcej o obsługiwanych systemach operacyjnych i kontenerach, zobacz [IoT Edge obsługiwanych systemach](../iot-edge/support.md).

## <a name="use-the-azure-certified-device-portal"></a>Korzystanie z portalu Azure Certified Device

Ta sekcja zawiera podsumowanie sposobu korzystania z [portalu certyfikowanych urządzeń platformy Azure](https://certify.azure.com). Aby dowiedzieć się więcej, zobacz artykuł [wprowadzenie do pracy z portalem](https://aka.ms/acdhelp).

Aby zatwierdzić urządzenie w programie certyfikowanym na platformie Azure, wykonaj następujące cztery kroki:

1. Podaj szczegóły urządzenia
2. Testowanie urządzenia
3. Prześlij i Ukończ przegląd
4. Publikowanie w katalogu certyfikowanych urządzeń platformy Azure (opcjonalnie)

### <a name="provide-device-details"></a>Podaj szczegóły urządzenia

Dla każdego urządzenia, które chcesz zatwierdzić, użyj formularzy w portalu certyfikacji, aby zarejestrować szczegółowe informacje o sprzęcie urządzenia, instrukcjach instalacji i materiałach marketingowych:

- **Informacje o urządzeniu:** Zbiera informacje o urządzeniu, takie jak jego nazwa, opis, szczegóły sprzętu i system operacyjny.
- **Wprowadzenie — Przewodnik**: dokument PDF, za pomocą którego klient może szybko korzystać z produktu. Dostępne są [przykładowe szablony](https://aka.ms/GSTemplate) .
- **Szczegóły marketingowe:** Podaj informacje marketingowe przeznaczone dla klientów dla Twojego urządzenia, takie jak informacje o obrazie i dystrybutorze.
- **Dodatkowe certyfikaty branżowe:** Opcjonalna sekcja, która umożliwia podanie informacji o innych certyfikatach urządzenia.

### <a name="test-the-device"></a>Testowanie urządzenia

Ta faza współdziała z urządzeniem i uruchamia serię testów, gdy urządzenie używa programu DPS do nawiązywania połączenia z IoT Hub. Po zakończeniu można wyświetlić zestaw plików dziennika z wynikami testu urządzenia.

Portal certyfikacji zawiera instrukcje dotyczące łączenia się z wystąpieniem IoT Hub używanym do testowania. Połączenie usługi DPS można ustanowić za pomocą dowolnej z [obsługiwanych metod zaświadczania](https://aka.ms/acdAttestation).

Zespół urządzeń z certyfikatem platformy Azure może skontaktować się z konstruktorem urządzeń w celu przeprowadzenia dalszej ręcznej weryfikacji urządzenia.

### <a name="submit-and-publish"></a>Prześlij i Opublikuj

Ostatnim wymaganym etapem jest przesłanie projektu do przeglądu. Ten krok powiadamia członka zespołu urządzenia z certyfikatem platformy Azure o kompletności, w tym informacje dotyczące urządzenia i marketingu oraz Przewodnik wprowadzający. Członek zespołu może skontaktować się z Tobą w adresie e-mail firmy dostarczony wcześniej z pytaniami lub edytować żądania przed zatwierdzeniem.

Jeśli urządzenie wymaga dalszej weryfikacji ręcznej w ramach certyfikacji, w tej chwili otrzymasz powiadomienie.

Gdy urządzenie jest certyfikowane, można opublikować szczegóły produktu w wykazie certyfikowanych urządzeń platformy Azure przy użyciu funkcji **Publikuj w katalogu** na stronie Podsumowanie produktu.

## <a name="if-you-have-questions"></a>Jeśli masz pytania

Skontaktuj się z zespołem pod [iotcert@microsoft.com](mailto:iotcert@microsoft.com?subject=Azure%20Certified%20Device%20question) warunkiem, jeśli masz jakieś pytania dotyczące programów certyfikacji, portalu certyfikacji lub wykazu urządzeń z certyfikatem platformy Azure.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz przegląd programu Device Certified dla systemu Azure, sugerowanym następnym krokiem jest zapoznanie się z [tematem certyfikowania urządzeń Plug and Play IoT](howto-certify-device.md).
