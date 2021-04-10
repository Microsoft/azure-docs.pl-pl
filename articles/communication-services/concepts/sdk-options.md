---
title: Zestawy SDK i interfejsy API REST dla usług Azure Communications Services
titleSuffix: An Azure Communication Services concept document
description: Dowiedz się więcej na temat zestawów SDK usług Azure Communication Services i interfejsów API REST.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/25/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 92324d68eabfb1885a482a7f539140f93be77596
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605199"
---
# <a name="sdks-and-rest-apis"></a>Zestawy SDK i interfejsy API REST

Możliwości usług Azure Communication Services są koncepcyjnie zorganizowane w sześć obszarów. Większość obszarów ma całkowicie zestaw SDK, które zostały zaprogramowane do opublikowanych interfejsów API REST, których można używać bezpośrednio przez Internet. Zestaw SDK wywoływania używa własnościowych interfejsów sieciowych i jest obecnie zamknięty — źródło. Przykłady i szczegółowe informacje techniczne dotyczące zestawów SDK są publikowane w [repozytorium GitHub usługi Azure Communication Services](https://github.com/Azure/communication).

## <a name="rest-apis"></a>Interfejsy API REST
Interfejsy API usług komunikacyjnych są udokumentowane wraz z innymi interfejsami API REST platformy Azure w [docs.Microsoft.com](/rest/api/azure/). W tej dokumentacji przedstawiono sposób struktury komunikatów HTTP i przedstawiono wskazówki dotyczące korzystania z programu Poster. Ta dokumentacja jest również oferowana w formacie Swagger w serwisie [GitHub](https://github.com/Azure/azure-rest-api-specs).


## <a name="sdks"></a>Zestawy SDK

| Zestaw | Przestrzenie nazw| Protokoły | Możliwości |
|------------------------|-------------------------------------|---------------------------------|--------------------------------------------------------------------------------------------|
| Azure Resource Manager | Azure. ResourceManager. Komunikacja | [REST](https://docs.microsoft.com/rest/api/communication/communicationservice)| Udostępnianie zasobów usług komunikacyjnych i zarządzanie nimi|
| Wspólne | Azure. Communications. Common| REST | Udostępnia typy podstawowe dla innych zestawów SDK |
| Tożsamość | Azure. Communications. Identity| [REST](https://docs.microsoft.com/rest/api/communication/communicationidentity)| Zarządzanie użytkownikami, tokenami dostępu|
| Numery telefonów _(beta)_| Azure. Communications. unports| [REST](https://docs.microsoft.com/rest/api/communication/phonenumberadministration)| Uzyskaj numery telefonów i zarządzaj nimi |
| Czat | Azure. Communications. Chat| [](https://docs.microsoft.com/rest/api/communication/) Zastąp zastrzeżonym sygnalizowaniem | Dodawanie rozmowy na podstawie tekstu w czasie rzeczywistym do aplikacji |
| SMS| Azure. Communications. SMS | [REST](https://docs.microsoft.com/rest/api/communication/sms)| Wysyłanie i odbieranie wiadomości SMS|
| Rozmów| Azure. komunikacja. wywoływanie | Transport własnościowy | Korzystanie z głosu, wideo, udostępniania ekranu i innych funkcji komunikacji danych w czasie rzeczywistym |

Zestawy SDK Azure Resource Manager, tożsamości i SMS koncentrują się na integracji usługi, a w wielu przypadkach występują problemy z bezpieczeństwem w przypadku integrowania tych funkcji w aplikacjach użytkowników końcowych. Zestawy SDK Common and Chat są odpowiednie dla aplikacji usług i klienta. Zestaw SDK wywoływania jest przeznaczony dla aplikacji klienckich. Zestaw SDK skoncentrowany na scenariuszach usług jest w trakcie opracowywania.


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


## <a name="rest-api-throttles"></a>Ograniczenia interfejsu API REST
Niektóre interfejsy API REST i odpowiednie metody zestawu SDK mają limity ograniczania, które należy zastanowić. Przekroczenie tych limitów ograniczania spowoduje wyzwolenie  `429 - Too Many Requests` odpowiedzi na błąd. Limity te można zwiększyć przez [żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

| Interfejs API                                                                                                                          | Ograniczenie            |
|------------------------------------------------------------------------------------------------------------------------------|---------------------|
| [Wszystkie interfejsy API planu numeru telefonu wyszukiwania](https://docs.microsoft.com/rest/api/communication/phonenumberadministration)         | 4 żądania/dzień      |
| [Zakup planu numeru telefonu](https://docs.microsoft.com/rest/api/communication/phonenumberadministration/purchasesearch) | 1 zakup w miesiącu  |
| [Wyślij wiadomość SMS](https://docs.microsoft.com/rest/api/communication/sms/send)                                                       | 200 żądań na minutę |


## <a name="sdk-platform-support-details"></a>Szczegóły obsługi platformy SDK

### <a name="ios-and-android"></a>iOS i Android 

- Usługi komunikacyjne dla systemu iOS zestawy SDK dla systemu iOS w wersji 13 + i Xcode 11 +.
- Zestawy SDK Java dla systemu Android — docelowy poziom interfejsu API systemu Android 21 + i Android Studio 4.0 +

### <a name="net"></a>.NET 

Z wyjątkiem wywoływania, pakiety usług komunikacyjnych Target .NET Standard 2,0, które obsługują platformy wymienione poniżej.

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
> Ta sekcja zawiera wskazówki dotyczące interfejsów API REST i zestawów SDK oznaczonych jako **stabilne**. Interfejsy API oznaczone jako wersja wstępna, wersja zapoznawcza lub wersja beta mogą być zmieniane lub przestarzałe **bez powiadomienia**.

W przyszłości firma Microsoft może wycofać wersje zestawów SDK usług komunikacyjnych, a firma Microsoft może wprowadzić istotne zmiany w interfejsach API REST i wydanych zestawach SDK. Usługi komunikacyjne platformy Azure będą *ogólnie* zgodne z dwoma zasadami obsługi w przypadku wycofywania wersji usługi:

- Przed koniecznością zmiany kodu z powodu zmiany interfejsu usług komunikacyjnych otrzymasz powiadomienie co najmniej trzy lata. Wszystkie udokumentowane interfejsy API REST i interfejsy API zestawu SDK zwykle korzystają z co najmniej trzech lat ostrzeżenia przed zlikwidowaniem interfejsów.
- Przed zainstalowaniem zestawów SDK w najnowszej wersji pomocniczej zostanie powiadomiony co najmniej jeden rok. Te wymagane aktualizacje nie wymagają żadnych zmian w kodzie, ponieważ znajdują się w tej samej wersji głównej. Jest to szczególnie istotne w przypadku bibliotek wywołujących i rozmów, które mają składniki czasu rzeczywistego, które często wymagają aktualizacji zabezpieczeń i wydajności. Zdecydowanie zachęcamy do zachowania zaktualizowanych zestawów SDK usług komunikacyjnych.

### <a name="api-and-sdk-decommissioning-examples"></a>Przykłady dotyczące likwidowania interfejsów API i zestawu SDK

**Wersja v24 interfejsu API REST usługi SMS została zintegrowana z aplikacją. Usługa Azure Communication releases V25.**

Zanim te interfejsy API przestaną działać i zostanie wymuszone uaktualnienie do V25, otrzymasz ostrzeżenie o trzech latach. Ta aktualizacja może wymagać zmiany kodu.

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
