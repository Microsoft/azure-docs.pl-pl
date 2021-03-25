---
title: Biblioteki klienckie i interfejsy API REST dla usług Azure Communications Services
titleSuffix: An Azure Communication Services concept document
description: Dowiedz się więcej na temat zestawów SDK usług Azure Communication Services i interfejsów API REST.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: effd7658bbfe7359e1f99f9452857824c2c45c2f
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105107894"
---
# <a name="client-libraries-and-rest-apis"></a>Biblioteki klienta i interfejsy API REST

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]


Możliwości usług Azure Communication Services są koncepcyjnie zorganizowane w sześć obszarów. Niektóre obszary mają w pełni otwarte zestawy SDK. Zestaw SDK wywoływania używa własnościowych interfejsów sieciowych i jest obecnie zamknięty — źródło, a Biblioteka rozmów zawiera zależność zamkniętego źródła. Przykłady i dodatkowe szczegóły techniczne dotyczące zestawów SDK są publikowane w [repozytorium GitHub usługi Azure Communication Services](https://github.com/Azure/communication).

## <a name="client-libraries"></a>Biblioteki klienta

| Zestaw               | Protokoły             |Otwórz a źródło zamknięte| Przestrzenie nazw                          | Możliwości                                                      |
| ---------------------- | --------------------- | ---|-------------------------- | --------------------------------------------------------------------------- |
| Azure Resource Manager | REST | Otwórz            | Azure. ResourceManager. Komunikacja | Udostępnianie zasobów usług komunikacyjnych i zarządzanie nimi             |
| Wspólne                 | REST | Otwórz               | Azure. Communications. Common          | Udostępnia typy podstawowe dla innych zestawów SDK |
| Tożsamość         | REST | Otwórz               | Azure. Communications. Identity  | Zarządzanie użytkownikami, tokenami dostępu |
| Numery telefonów         | REST | Otwórz               | Azure. Communications. unports  | Zarządzanie numerami telefonów |
| Czat                   | Zastąp zastrzeżonym sygnalizowaniem | Otwórz z zamknąłm pakietem sygnalizującym Źródło    | Azure. Communications. Chat            | Dodawanie rozmowy na podstawie tekstu w czasie rzeczywistym do aplikacji  |
| SMS                    | REST | Otwórz              | Azure. Communications. SMS             | Wysyłanie i odbieranie wiadomości SMS |
| Rozmów                | Transport własnościowy | Zamknięty |Azure. komunikacja. wywoływanie         | Wykorzystywanie głosu, wideo, udostępniania ekranu i innych możliwości komunikacji danych w czasie rzeczywistym          |

Należy pamiętać, że zestawy SDK Azure Resource Manager, tożsamości i SMS koncentrują się na integracji usługi, a w wielu przypadkach występują problemy z bezpieczeństwem w przypadku integrowania tych funkcji w aplikacjach użytkowników końcowych. Zestawy SDK Common and Chat są odpowiednie dla aplikacji usług i klienta. Zestaw SDK wywoływania jest przeznaczony dla aplikacji klienckich. Zestaw SDK skoncentrowany na scenariuszach usług jest w trakcie opracowywania.

### <a name="languages-and-publishing-locations"></a>Języki i lokalizacje publikowania

Lokalizacje publikowania poszczególnych pakietów SDK są szczegółowo opisane poniżej.

| Warstwowy           | JavaScript | .NET | Python | Java SE | iOS | Android | Inne                          |
| -------------- | ---------- | ---- | ------ | ---- | -------------- | -------------- | ------------------------------ |
| Azure Resource Manager | -         | [NuGet](https://www.nuget.org/packages/Azure.ResourceManager.Communication)    |   [PyPi](https://pypi.org/project/azure-mgmt-communication/)    |  -  | -              | -  | [Przejdź za pośrednictwem usługi GitHub](https://github.com/Azure/azure-sdk-for-go/releases/tag/v46.3.0) |
| Wspólne         | [npm](https://www.npmjs.com/package/@azure/communication-common)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Common/)    | Nie dotyczy      | [Maven](https://search.maven.org/search?q=a:azure-communication-common)   | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)            | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-common)             | -                              |
| Tożsamość | [npm](https://www.npmjs.com/package/@azure/communication-identity)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Identity)    | [PyPi](https://pypi.org/project/azure-communication-identity/)      | [Maven](https://search.maven.org/search?q=a:azure-communication-identity)   | -              | -              | -                            |
| Numery telefonów | [npm](https://www.npmjs.com/package/@azure/communication-phone-numbers)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.PhoneNumbers)    | [PyPi](https://pypi.org/project/azure-communication-phonenumbers/)      | [Maven](https://search.maven.org/search?q=a:azure-communication-phonenumbers)   | -              | -              | -                            |
| Czat           | [npm](https://www.npmjs.com/package/@azure/communication-chat)        | [NuGet](https://www.nuget.org/packages/Azure.Communication.Chat)     | [PyPi](https://pypi.org/project/azure-communication-chat/)     | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)  | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | -                              |
| SMS            | [npm](https://www.npmjs.com/package/@azure/communication-sms)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Sms)    | [PyPi](https://pypi.org/project/azure-communication-sms/)       | [Maven](https://search.maven.org/artifact/com.azure/azure-communication-sms)   | -              | -              | -                              |
| Rozmów        | [npm](https://www.npmjs.com/package/@azure/communication-calling)         | -      | -      | -     | [GitHub](https://github.com/Azure/Communication/releases)     | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-calling/)            | -                              |
| Dokumentacja referencyjna     | [docs](https://azure.github.io/azure-sdk-for-js/communication.html)         | [docs](https://azure.github.io/azure-sdk-for-net/communication.html)      | -      | [docs](http://azure.github.io/azure-sdk-for-java/communication.html)     | [docs](/objectivec/communication-services/calling/)      | [docs](/java/api/com.azure.communication.calling)            | -                              |

## <a name="rest-apis"></a>Interfejsy API REST

Interfejsy API usług komunikacyjnych są udokumentowane wraz z innymi interfejsami API REST platformy Azure w [docs.Microsoft.com](/rest/api/azure/). W tej dokumentacji przedstawiono sposób struktury komunikatów HTTP i przedstawiono wskazówki dotyczące korzystania z programu Poster. Ta dokumentacja jest również oferowana w formacie Swagger w serwisie [GitHub](https://github.com/Azure/azure-rest-api-specs).

## <a name="additional-support-details"></a>Dodatkowe szczegóły pomocy technicznej

### <a name="ios-and-android-support-details"></a>szczegóły pomocy technicznej dla systemów iOS i Android

- Usługi komunikacyjne dla systemu iOS zestawy SDK dla systemu iOS w wersji 13 + i Xcode 11 +.
- Zestawy SDK Java dla systemu Android — docelowy poziom interfejsu API systemu Android 21 + i Android Studio 4.0 +

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

## <a name="calling-sdk-timeouts"></a>Trwa wywoływanie limitów czasu zestawu SDK

Następujące limity czasu dotyczą usług komunikacyjnych wywołujących zestawy SDK:

| Akcja           | Limit czasu w sekundach |
| -------------- | ---------- |
| Uczestnik ponownego łączenia/usuwania | 120 |
| Dodawanie lub usuwanie nowego modalności wywołania (uruchamianie/zatrzymywanie udostępniania wideo lub ekranu) | 40 |
| Limit czasu operacji transferu wywołań | 60 |
| limit czasu ustanowienia wywołania 1:1 | 85 |
| Limit czasu ustanowienia połączenia z grupą | 85 |
| Limit czasu ustanowienia połączenia PSTN | 115 |
| Podnieś poziom 1:1 wywołania do grupy limit czasu wywołania | 115 |


## <a name="api-stability-expectations"></a>Oczekiwania dotyczące stabilności interfejsu API

> [!IMPORTANT]
> Ta sekcja zawiera wskazówki dotyczące interfejsów API REST i zestawów SDK oznaczonych jako **stabilne**. Interfejsy API oznaczone jako wersja wstępna, wersja zapoznawcza lub wersja beta mogą być zmieniane lub przestarzałe **bez powiadomienia**.

W przyszłości firma Microsoft może wycofać wersje zestawów SDK usług komunikacyjnych, a firma Microsoft może wprowadzić istotne zmiany w interfejsach API REST i wydanych zestawach SDK. Usługi komunikacyjne platformy Azure będą *ogólnie* zgodne z dwoma zasadami obsługi w przypadku wycofywania wersji usługi:

- Przed koniecznością zmiany kodu z powodu zmiany interfejsu usług komunikacyjnych otrzymasz powiadomienie co najmniej trzy lata. Wszystkie udokumentowane interfejsy API REST i interfejsy API zestawu SDK zwykle korzystają z co najmniej trzech lat ostrzeżenia przed zlikwidowaniem interfejsów.
- Przed zainstalowaniem zestawów SDK w najnowszej wersji pomocniczej zostanie powiadomiony co najmniej jeden rok. Te wymagane aktualizacje nie wymagają żadnych zmian w kodzie, ponieważ znajdują się w tej samej wersji głównej. Jest to szczególnie istotne w przypadku bibliotek wywołujących i rozmów, które mają składniki czasu rzeczywistego, które często wymagają aktualizacji zabezpieczeń i wydajności. Zdecydowanie zachęcamy do zachowania zaktualizowanych zestawów SDK usług komunikacyjnych.

### <a name="api-and-sdk-decommissioning-examples"></a>Przykłady dotyczące likwidowania interfejsów API i zestawu SDK

**Wersja v24 interfejsu API REST usługi SMS została zintegrowana z aplikacją. Usługa Azure Communication releases V25.**

Otrzymasz 3-letnie ostrzeżenie, zanim te interfejsy API przestaną działać i zostaną wymuszone do aktualizacji do V25. Ta aktualizacja może wymagać zmiany kodu.

**Do aplikacji zintegrowano wersję 2.02 zestawu SDK wywołania. Usługa Azure Communication releases w wersji 2.05.**

Może być konieczne zaktualizowanie do wersji 2.05 zestawu SDK wywołującego w ciągu 12 miesięcy od wydania programu v 2.05. Powinno to być proste zastąpienie artefaktu bez konieczności zmiany kodu, ponieważ 2.05 v jest w wersji głównej v2 i nie ma żadnych istotnych zmian.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz następujące omówienia zestawu SDK:

- [Omówienie wywoływania zestawu SDK](../concepts/voice-video-calling/calling-sdk-features.md)
- [Omówienie zestawu SDK rozmowy](../concepts/chat/sdk-features.md)
- [Omówienie zestawu SDK programu SMS](../concepts/telephony-sms/sdk-features.md)

Aby rozpocząć pracę z usługami Azure Communications Services:

- [Tworzenie zasobów komunikacyjnych platformy Azure](../quickstarts/create-communication-resource.md)
- Generuj [tokeny dostępu użytkowników](../quickstarts/access-tokens.md)
