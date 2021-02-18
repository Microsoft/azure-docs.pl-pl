---
title: Architektura klienta i serwera
titleSuffix: An Azure Communication Services concept document
description: Dowiedz się więcej o architekturze usługi komunikacyjnej.
author: mikben
manager: mikben
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 2cc7e1d0c108d45383a68258fb6f62fcab87d1f7
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653819"
---
# <a name="client-and-server-architecture"></a>Architektura klienta i serwera

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

<!--
> [!WARNING]
> This document is under construction and needs the following items to be addressed: 
> - Need to add security best practices for token management here
> - Reference docs:
> - https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/create-a-token-object
> - https://docs.microsoft.com/azure/aks/operator-best-practices-identity
> - https://docs.microsoft.com/cloud-app-security/api-tokens?view=gestures-1.0-->

Każda aplikacja usług Azure Communications Services będzie miała **aplikacje klienckie** korzystające z **usług** w celu ułatwienia łączności między osobami. Na tej stronie przedstawiono typowe elementy architektury w różnych scenariuszach.

## <a name="user-access-management"></a>Zarządzanie dostępem użytkowników

Biblioteki klienckie usług Azure Communications Services wymagają `user access tokens` bezpiecznego uzyskiwania dostępu do zasobów usług komunikacyjnych. `User access tokens` powinny być generowane i zarządzane przez zaufaną usługę z powodu poufnego charakteru tokenu i parametrów połączenia wymaganych do ich wygenerowania. Niepowodzenie poprawnego zarządzania tokenami dostępu może spowodować naliczenie dodatkowych opłat z powodu nieprawidłowego wykorzystania zasobów. Zdecydowanie zaleca się korzystanie z zaufanej usługi do zarządzania użytkownikami. Usługa zaufana generuje tokeny i przekazuje je do klienta przy użyciu odpowiedniego szyfrowania. Przykładowy przepływ architektury można znaleźć poniżej:

:::image type="content" source="../media/scenarios/archdiagram-access.png" alt-text="Diagram przedstawiający architekturę tokenu dostępu użytkowników.":::

Aby uzyskać dodatkowe informacje, zobacz [najlepsze rozwiązania związane z zarządzaniem tożsamościami](../../security/fundamentals/identity-management-best-practices.md)

## <a name="browser-communication"></a>Komunikacja przeglądarki

Biblioteki klienckie JavaScript dotyczące usługi Azure Communications umożliwiają aplikacjom sieci Web zaawansowaną obsługę tekstu, głosu i wideo. Aplikacja bezpośrednio współdziała z usługami Azure Communications Services za pomocą biblioteki klienckiej w celu uzyskania dostępu do płaszczyzny danych i dostarczania tekstu, głosu i wideo w czasie rzeczywistym. Przykładowy przepływ architektury można znaleźć poniżej:

:::image type="content" source="../media/scenarios/archdiagram-browser.png" alt-text="Diagram przedstawiający architekturę przeglądarki dla usług komunikacyjnych.":::

## <a name="native-app-communication"></a>Komunikacja natywna aplikacji

Wiele scenariuszy najlepiej obsługuje aplikacje natywne. Usługi komunikacyjne platformy Azure obsługują komunikację między przeglądarką a aplikacją.  Podczas tworzenia natywnego środowiska aplikacji przy użyciu powiadomień wypychanych użytkownicy będą mogli odbierać wywołania nawet wtedy, gdy aplikacja nie jest uruchomiona. Usługi komunikacyjne systemu Azure ułatwiają to zintegrowane powiadomienia wypychane w usłudze Google Firebase, Apple Push Notification Service i powiadomieniach wypychanych systemu Windows. Przykładowy przepływ architektury można znaleźć poniżej:

:::image type="content" source="../media/scenarios/archdiagram-app.png" alt-text="Diagram przedstawiający architekturę usług komunikacyjnych do komunikacji natywnej aplikacji.":::

## <a name="voice-and-sms-over-the-public-switched-telephony-network-pstn"></a>Komunikacja głosowa i SMS za pośrednictwem publicznej sieci telefonicznej (PSTN)

Komunikacja przez system telefonii może znacząco zwiększyć zasięg aplikacji. W celu obsługi scenariuszy głosowych i SMS w sieci PSTN usługi Azure Communication Services ułatwiają [pozyskiwanie numerów telefonów](../quickstarts/telephony-sms/get-phone-number.md) bezpośrednio z Azure Portal lub przy użyciu interfejsów API REST i bibliotek klienckich. Po pobraniu numerów telefonów mogą one być używane do uzyskiwania dostępu do klientów korzystających zarówno z połączeń PSTN, jak i SMS w scenariuszach przychodzących i wychodzących. Przykładowy przepływ architektury można znaleźć poniżej:

> [!Note]
> W publicznej wersji zapoznawczej udostępnianie numerów telefonów US jest dostępne dla klientów, którzy mają adresy rozliczeń znajdujące się w Stanach Zjednoczonych i Kanadzie. 

:::image type="content" source="../media/scenarios/archdiagram-pstn.png" alt-text="Diagram przedstawiający architekturę PSTN usług komunikacyjnych.":::

Aby uzyskać więcej informacji na temat numerów telefonów PSTN, zobacz [typy numerów telefonów](../concepts/telephony-sms/plan-solution.md)

## <a name="humans-communicating-with-bots-and-other-services"></a>Ludzie komunikują się z botów i innymi usługami

Usługi komunikacyjne platformy Azure obsługują komunikację między ludźmi, chociaż kanały tekstowe i głosowe, z usługami, które bezpośrednio uzyskują dostęp do płaszczyzny danych usług Azure Communication Services. Na przykład możesz mieć bot odpowiedzi na przychodzące rozmowy telefoniczne lub uczestniczyć w rozmowie w sieci Web. Usługi komunikacyjne platformy Azure udostępniają biblioteki klienckie, które umożliwiają wywoływanie i rozmowę z tymi scenariuszami. Przykładowy przepływ architektury można znaleźć poniżej:

:::image type="content" source="../media/scenarios/archdiagram-bot.png" alt-text="Diagram przedstawiający architekturę bot usług komunikacyjnych.":::

## <a name="networking"></a>Sieć

Możesz chcieć zamieścić dowolne dane między użytkownikami, aby na przykład zsynchronizować udostępnioną rzeczywistość mieszana lub środowisko gier. Płaszczyzna danych w czasie rzeczywistym używana do komunikacji tekstu, głosu i wideo jest dostępna bezpośrednio na dwa sposoby:

- **Wywoływanie biblioteki klienta** — urządzenia w wywołaniu mają dostęp do interfejsów API do wysyłania i otrzymywania danych za pośrednictwem kanału wywołania. Jest to najprostszy sposób, aby dodać komunikację z danymi do istniejącej interakcji.
- **STUN/Włącz** usługi Azure Communications Services udostępniają STUN i włączają dostępne dla Ciebie usługi. Dzięki temu można utworzyć silnie dostosowaną warstwę transportową na podstawie tych znormalizowanych elementów podstawowych. Możesz tworzyć własnych klientów zgodnych ze standardami lub używać bibliotek typu open source, takich jak [WinRTC](https://github.com/microsoft/winrtc).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie tokenów dostępu użytkowników](../quickstarts/access-tokens.md)

Aby uzyskać więcej informacji, zobacz następujące artykuły:

- Informacje o [uwierzytelnianiu](../concepts/authentication.md)
- Dowiedz się więcej o [typach numerów telefonów](../concepts/telephony-sms/plan-solution.md)

- [Dodawanie czatu do aplikacji](../quickstarts/chat/get-started.md)
- [Dodawanie połączenia głosowego do aplikacji](../quickstarts/voice-video-calling/getting-started-with-calling.md)