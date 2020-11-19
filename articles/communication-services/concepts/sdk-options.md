---
title: Biblioteki klienckie i interfejsy API REST dla usług Azure Communications Services
titleSuffix: An Azure Communication Services concept document
description: Dowiedz się więcej na temat bibliotek klienckich usług Azure Communication Services i interfejsów API REST.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/18/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: f26f3e6e80bf854ff4f57503aa43de2558f07a1e
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888559"
---
# <a name="client-libraries-and-rest-apis"></a>Biblioteki klienta i interfejsy API REST

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Możliwości usług Azure Communication Services są koncepcyjnie zorganizowane w sześć obszarów. Niektóre obszary mają w pełni otwarte biblioteki klienta. Biblioteka klienta wywołującego używa własnościowych interfejsów sieciowych i jest aktualnie ZAMKNIĘTA — źródło, a Biblioteka rozmów zawiera zależność zamkniętego źródła. Przykłady i dodatkowe szczegóły techniczne dotyczące bibliotek klienckich są publikowane w [repozytorium GitHub usług Azure Communication Services](https://github.com/Azure/communication).

## <a name="client-libraries"></a>Biblioteki klienckie

| Zestaw               | Protokoły             |Otwórz a źródło zamknięte| Przestrzenie nazw                          | Możliwości                                                      |
| ---------------------- | --------------------- | ---|-------------------------- | --------------------------------------------------------------------------- |
| Azure Resource Manager | REST | Otwórz            | Azure. ResourceManager. Komunikacja | Udostępnianie zasobów usług komunikacyjnych i zarządzanie nimi             |
| Wspólne                 | REST | Otwórz               | Azure. Communications. Common          | Udostępnia typy podstawowe dla innych bibliotek klienta |
| Administracja         | REST | Otwórz               | Azure. Communications. Administration  | Zarządzanie użytkownikami, tokenami dostępu i numerami telefonów, przydzielanie zgodnych ze standardami STUN i włączanie serwerów |
| Czat                   | Zastąp zastrzeżonym sygnalizowaniem | Otwórz z zamknąłm pakietem sygnalizującym Źródło    | Azure. Communications. Chat            | Dodawanie rozmowy na podstawie tekstu w czasie rzeczywistym do aplikacji  |
| SMS                    | REST | Otwórz              | Azure. Communications. SMS             | Wysyłanie i odbieranie wiadomości SMS |
| Rozmów                | Transport własnościowy | Zamknięty |Azure. komunikacja. wywoływanie         | Wykorzystywanie głosu, wideo, udostępniania ekranu i innych możliwości komunikacji danych w czasie rzeczywistym          |

Należy pamiętać, że biblioteki klienta Azure Resource Manager, Administracja i SMS są skoncentrowane na integracji usługi i w wielu przypadkach występują problemy z bezpieczeństwem w przypadku integrowania tych funkcji w aplikacjach użytkowników końcowych. Popularne biblioteki klienta i rozmowy są odpowiednie dla aplikacji usługi i klienta. Biblioteka klienta wywołującego jest przeznaczona dla aplikacji klienckich. Biblioteka klienta ukierunkowana na scenariusze usług jest w trakcie opracowywania.

### <a name="languages-and-publishing-locations"></a>Języki i lokalizacje publikowania

Poniżej przedstawiono szczegółowo lokalizacje publikowania poszczególnych pakietów biblioteki klienta. 

| Obszar           | JavaScript | .NET | Python | Java SE | iOS | Android | Inne                          |
| -------------- | ---------- | ---- | ------ | ---- | -------------- | -------------- | ------------------------------ |
| Azure Resource Manager | -         | [NuGet](https://www.nuget.org/packages/Azure.ResourceManager.Communication)    |   [PyPi](https://pypi.org/project/azure-mgmt-communication/)    |  -  | -              | -  | [Przejdź za pośrednictwem usługi GitHub](https://github.com/Azure/azure-sdk-for-go/releases/tag/v46.3.0) |
| Wspólne         | [npm](https://www.npmjs.com/package/@azure/communication-common)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Common/)    | Nie dotyczy      | [Maven](https://search.maven.org/search?q=a:azure-communication-common)   | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases/tag/1.0.0-beta.1)            | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-common)             | -                              |
| Administracja | [npm](https://www.npmjs.com/package/@azure/communication-administration)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Administration)    | [PyPi](https://pypi.org/project/azure-communication-administration/)      | [Maven](https://search.maven.org/search?q=a:azure-communication-administration)   | -              | -              | -                            |
| Czat           | [npm](https://www.npmjs.com/package/@azure/communication-chat)        | [NuGet](https://www.nuget.org/packages/Azure.Communication.Chat)     | [PyPi](https://pypi.org/project/azure-communication-chat/)     | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)  | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | -                              |
| SMS            | [npm](https://www.npmjs.com/package/@azure/communication-sms)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Sms)    | [PyPi](https://pypi.org/project/azure-communication-sms/)       | [Maven](https://search.maven.org/artifact/com.azure/azure-communication-sms)   | -              | -              | -                              |
| Rozmów        | [npm](https://www.npmjs.com/package/@azure/communication-calling)         | -      | -      | -     | [GitHub](https://github.com/Azure/Communication/releases/tag/v1.0.0-beta.2)     | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-calling/)            | -                              |
| Dokumentacja referencyjna     | [docs](https://azure.github.io/azure-sdk-for-js/communication.html)         | [docs](https://azure.github.io/azure-sdk-for-net/communication.html)      | -      | [docs](http://azure.github.io/azure-sdk-for-java/communication.html)     | (Obj-C) ✔️     | ✔️            | -                              |
## <a name="rest-apis"></a>Interfejsy API REST

Interfejsy API usług komunikacyjnych są udokumentowane wraz z innymi interfejsami API REST platformy Azure w [docs.Microsoft.com](/rest/api/azure/). W tej dokumentacji przedstawiono sposób struktury komunikatów HTTP i przedstawiono wskazówki dotyczące korzystania z programu Poster. Ta dokumentacja jest również oferowana w formacie Swagger w serwisie [GitHub](https://github.com/Azure/azure-rest-api-specs).

## <a name="additional-support-details"></a>Dodatkowe szczegóły pomocy technicznej

### <a name="ios-and-android-support-details"></a>szczegóły pomocy technicznej dla systemów iOS i Android

- Usługi komunikacyjne dla systemu iOS biblioteki klienckie dla systemu iOS w wersji 13 + i Xcode 11 +.
- Biblioteki klienta Java dla systemu Android — docelowy poziom interfejsu API systemu Android 21 + i Android Studio 4.0 +

### <a name="net-support-details"></a>Szczegóły pomocy technicznej platformy .NET

Z wyjątkiem wywoływania usług komunikacyjnych pakiety docelowe .NET Standard 2,0, które obsługują platformy wymienione poniżej.

Obsługa za pośrednictwem .NET Framework 4.6.1
- Windows 10, 8,1, 8 i 7
- Windows Server 2012 R2, 2012 i 2008 R2 z dodatkiem SP1

Obsługa za pośrednictwem programu .NET Core 2,0:
- Windows 10 (1607 +), 7 z dodatkiem SP1 +, 8,1
- Windows Server 2008 R2 z dodatkiem SP1 +
- Maksymalna wersja systemu OS X 10.12 +
- Wiele wersji/dystrybucji systemu Linux
- PLATFORMY UWP 10.0.16299 (RS3) wrzesień 2017
- Unity 2018,1
- Mono 5,4
- Platforma Xamarin iOS 10,14
- Xamarin Mac 3,8

## <a name="api-stability-expectations"></a>Oczekiwania dotyczące stabilności interfejsu API 

> [!IMPORTANT]
> Ta sekcja zawiera wskazówki dotyczące interfejsów API REST i bibliotek klienckich oznaczonych jako **stabilne**. Interfejsy API oznaczone jako wersja wstępna, wersja zapoznawcza lub wersja beta mogą być zmieniane lub przestarzałe **bez powiadomienia**. Obecnie usługi komunikacyjne platformy Azure znajdują się w **publicznej wersji zapoznawczej**, a interfejsy API są oznaczone jako takie.

W przyszłości firma Microsoft może wycofać wersje bibliotek klienckich usług komunikacyjnych, a firma Microsoft może wprowadzić istotne zmiany w interfejsach API REST i wydanych bibliotekach klienta. Usługi komunikacyjne platformy Azure będą *ogólnie* zgodne z dwoma zasadami obsługi w przypadku wycofywania wersji usługi:

- Przed koniecznością zmiany kodu z powodu zmiany interfejsu usług komunikacyjnych otrzymasz powiadomienie co najmniej trzy lata. Wszystkie udokumentowane interfejsy API REST i interfejsy API biblioteki klienta zwykle korzystają z co najmniej trzech lat ostrzeżenia przed zlikwidowaniem interfejsów.
- Przed zaktualizowaniem zestawów bibliotek klienta do najnowszej wersji pomocniczej zostanie powiadomiony co najmniej jeden rok. Te wymagane aktualizacje nie wymagają żadnych zmian w kodzie, ponieważ znajdują się w tej samej wersji głównej. Jest to szczególnie istotne w przypadku bibliotek wywołujących i rozmów, które mają składniki czasu rzeczywistego, które często wymagają aktualizacji zabezpieczeń i wydajności. Zdecydowanie zachęcamy do utrzymania zaktualizowanych bibliotek klienta usług komunikacyjnych.

### <a name="api-and-client-library-decommissioning-examples"></a>Przykłady dotyczące likwidowania biblioteki interfejsu API i klienta

**Wersja v24 interfejsu API REST usługi SMS została zintegrowana z aplikacją. Usługa Azure Communication releases V25.**

Otrzymasz 3-letnie ostrzeżenie, zanim te interfejsy API przestaną działać i zostaną wymuszone do aktualizacji do V25. Ta aktualizacja może wymagać zmiany kodu.

**Do aplikacji zintegrowano wersję 2.02 biblioteki klienta. Usługa Azure Communication releases w wersji 2.05.**

Może być konieczne zaktualizowanie do wersji v 2.05 biblioteki klienta wywołującego w ciągu 12 miesięcy od wydania programu v 2.05. Powinno to być proste zastąpienie artefaktu bez konieczności zmiany kodu, ponieważ 2.05 v jest w wersji głównej v2 i nie ma żadnych istotnych zmian.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz następujące omówienia biblioteki klienta:

- [Omówienie wywoływania biblioteki klienta](../concepts/voice-video-calling/calling-sdk-features.md)
- [Omówienie biblioteki klienta rozmowy](../concepts/chat/sdk-features.md)
- [Omówienie biblioteki klienta programu SMS](../concepts/telephony-sms/sdk-features.md)

Aby rozpocząć pracę z usługami Azure Communications Services:

- [Tworzenie zasobów komunikacyjnych platformy Azure](../quickstarts/create-communication-resource.md)
- Generuj [tokeny dostępu użytkowników](../quickstarts/access-tokens.md)