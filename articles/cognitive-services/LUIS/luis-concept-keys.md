---
title: Jak używać kluczy tworzenia i środowiska uruchomieniowego w programie LUIS
titleSuffix: Azure Cognitive Services
description: LUIS używa dwóch kluczy, klucza tworzenia do tworzenia modelu i klucza czasu wykonywania do wykonywania zapytań dotyczących punktu końcowego przewidywania za pomocą wyrażenia długości użytkownika.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 954e7a22ae6b242c6221119c688259e4ce629a2a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82101063"
---
# <a name="authoring-and-runtime-keys"></a>Tworzenie i klucze środowiska uruchomieniowego

Language Understanding (LUIS) ma dwie usługi i zestawy interfejsów API:

* Tworzenie (wcześniej _znane jako_programowe)
* Środowisko uruchomieniowe przewidywania

Istnieje kilka typów kluczy, w zależności od rodzaju usługi, z którą chcesz współpracować i jak chcesz z nią korzystać.

## <a name="non-azure-resources-for-luis"></a>Zasoby spoza platformy Azure dla usługi LUIS

### <a name="starter-key"></a>Klucz początkowy

Gdy zaczniesz korzystać z LUIS, zostanie utworzony **klucz startowy** . Ten zasób udostępnia następujące informacje:

* bezpłatne żądania dotyczące usługi autorstwa za pomocą portalu LUIS lub interfejsów API (w tym zestawów SDK)
* bezpłatne żądania punktu końcowego przewidywania 1 000 miesięcznie za pomocą przeglądarki, interfejsu API lub zestawów SDK

## <a name="azure-resources-for-luis"></a>Zasoby platformy Azure dla usługi LUIS

<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

LUIS umożliwia korzystanie z trzech typów zasobów platformy Azure:

|Klucz|Przeznaczenie|Usługa poznawcze`kind`|Usługa poznawcze`type`|
|--|--|--|--|
|[Klucz tworzenia](#programmatic-key)|Dostęp do danych aplikacji i zarządzanie nimi za pomocą tworzenia, uczenia, publikowania i testowania. Utwórz klucz tworzenia LUIS, jeśli zamierzasz programowo tworzyć aplikacje LUIS.<br><br>Celem tego `LUIS.Authoring` klucza jest umożliwienie:<br>* programowe zarządzanie aplikacjami i modelami Language Understanding, w tym szkolenia i publikowanie<br> * Kontroluj uprawnienia do zasobu tworzenia, przypisując osoby do [roli współautor](#contributions-from-other-authors).|`LUIS.Authoring`|`Cognitive Services`|
|[Klucz predykcyjny](#prediction-endpoint-runtime-key)| Zapytania prognozowania żądań punktów końcowych. Utwórz klucz przewidywania LUIS, zanim aplikacja kliencka zażąda prognoz wykraczających poza żądania 1 000 dostarczone przez zasób początkowy. |`LUIS`|`Cognitive Services`|
|[Klucz zasobu usługi poznawczej — wiele usług](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|Żądania punktu końcowego przewidywania zapytań udostępniane z LUIS i innymi obsługiwanymi Cognitive Services.|`CognitiveServices`|`Cognitive Services`|

Po zakończeniu procesu tworzenia zasobu [Przypisz klucz](luis-how-to-azure-subscription.md) do aplikacji w portalu Luis.

Ważne jest, aby tworzyć aplikacje LUIS w [regionach](luis-reference-regions.md#publishing-regions) , w których chcesz publikować i wysyłać zapytania.

> [!CAUTION]
> Dla wygody wiele przykładów korzysta z [klucza początkowego](#starter-key) , ponieważ oferuje on kilka bezpłatnych wywołań punktów końcowych przewidywania w swoim [limicie przydziału](luis-limits.md#key-limits).


### <a name="query-prediction-resources"></a>Zasoby przewidywania zapytań

* Klucz czasu wykonywania może być używany dla wszystkich aplikacji LUIS lub określonych aplikacji LUIS.
* Nie należy używać klucza środowiska uruchomieniowego do tworzenia aplikacji LUIS.

Punkt końcowy LUIS środowiska uruchomieniowego akceptuje dwa style zapytania, oba używają klucza środowiska uruchomieniowego punktu końcowego przewidywania, ale w różnych miejscach.

Punkt końcowy używany do uzyskiwania dostępu do środowiska uruchomieniowego używa poddomeny, która jest unikatowa dla regionu zasobu, `{region}` w poniższej tabeli.

## <a name="assignment-of-the-key"></a>Przypisanie klucza

Klucz środowiska uruchomieniowego można [przypisać](luis-how-to-azure-subscription.md) w [portalu Luis](https://www.luis.ai) lub za pośrednictwem odpowiednich interfejsów API.

## <a name="key-limits"></a>Limity kluczy

Można utworzyć maksymalnie 10 kluczy tworzenia na region na subskrypcję.

Zobacz [limity kluczy](luis-limits.md#key-limits) i [regiony platformy Azure](luis-reference-regions.md).

Publikowanie regionów różni się od regionów tworzenia. Upewnij się, że tworzysz aplikację w regionie tworzenia odpowiadającą regionowi publikowania, który ma znajdować się aplikacja kliencka.

## <a name="key-limit-errors"></a>Błędy limitu kluczy
W przypadku przekroczenia limitu przydziału transakcji na sekundę (TPS) pojawia się błąd HTTP 429. W przypadku przekroczenia limitu przydziału transakcji na miesiąc (TPS) pojawia się błąd HTTP 403.

## <a name="contributions-from-other-authors"></a>Wkłady od innych autorów

**W przypadku [tworzenia migrowanych aplikacji zasobów](luis-migration-authoring.md) **: _Współautorzy_ są zarządzani w Azure Portal dla zasobu tworzenia przy użyciu strony **Kontrola dostępu (IAM)** . Dowiedz się, [jak dodać użytkownika](luis-how-to-collaborate.md)przy użyciu adresu e-mail współpracownika i roli _współautor_ .

W **przypadku aplikacji, które nie zostały jeszcze zmigrowane**: Wszyscy _współpracownicy_ są ZARZĄDZAni w portalu LUIS na stronie **Zarządzanie > współpracownikami** .

## <a name="move-transfer-or-change-ownership"></a>Przenoszenie, przenoszenie lub zmiana własności

Aplikacja jest definiowana przez zasoby platformy Azure, które są określane przez subskrypcję właściciela.

Możesz przenieść swoją aplikację LUIS. Skorzystaj z następujących zasobów dokumentacji w Azure Portal lub interfejs wiersza polecenia platformy Azure:

* [Przenoszenie aplikacji między zasobami tworzenia LUIS](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Przenieś zasób do nowej grupy zasobów lub subskrypcji](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Przenoszenie zasobu w ramach tej samej subskrypcji lub między subskrypcjami](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)

Aby przenieść [własność](../../cost-management-billing/manage/billing-subscription-transfer.md) subskrypcji:

**W przypadku użytkowników, którzy zostali zmigrowani z zmigrowanymi [zasobami tworzenia zasobów](luis-migration-authoring.md) **: jako właściciel zasobu można dodać `contributor`.

**Dla użytkowników, którzy nie zostali jeszcze zmigrowani**: wyeksportuj aplikację jako plik JSON. Inny użytkownik LUIS może zaimportować aplikację, w związku z czym staje się właścicielem aplikacji. Nowa aplikacja będzie mieć inny identyfikator aplikacji.

## <a name="access-for-private-and-public-apps"></a>Dostęp do aplikacji prywatnych i publicznych

W przypadku aplikacji **prywatnej** dostęp do środowiska uruchomieniowego jest dostępny dla właścicieli i współautorów. W przypadku aplikacji **publicznej** dostęp do środowiska uruchomieniowego jest dostępny dla wszystkich użytkowników, którzy korzystają z własnej [usługi poznawczej](../cognitive-services-apis-create-account.md) platformy Azure lub zasobu środowiska uruchomieniowego [Luis](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) , i mają identyfikator aplikacji publicznej.

Obecnie nie istnieje katalog aplikacji publicznych.

### <a name="authoring-access"></a>Dostęp do autorstwa
Dostęp do aplikacji z portalu [Luis](luis-reference-regions.md#luis-website) lub [interfejsów API tworzenia](https://go.microsoft.com/fwlink/?linkid=2092087) jest kontrolowany przez zasób tworzenia platformy Azure.

Właściciel i wszyscy Współautorzy mają dostęp do tworzenia aplikacji.

|Dostęp do tworzenia obejmuje|Uwagi|
|--|--|
|Dodaj lub Usuń klucze punktów końcowych||
|Eksportowanie wersji||
|Eksportowanie dzienników punktów końcowych||
|Wersja importowana||
|Tworzenie aplikacji jako publicznej|Gdy aplikacja jest publiczna, każda osoba mająca klucz tworzenia lub punktu końcowego może wysyłać zapytania do aplikacji.|
|Modyfikuj model|
|Publikowanie|
|Zapoznaj się z punktem końcowym wyrażenia długości na potrzeby [aktywnego uczenia](luis-how-to-review-endpoint-utterances.md)|
|Szkolenie|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>Dostęp w czasie wykonywania przewidywania punktu końcowego

Dostęp do zapytania do punktu końcowego przewidywania jest kontrolowany przez ustawienie na stronie **Informacje o aplikacji** w sekcji **Zarządzanie** .

|[Prywatny punkt końcowy](#runtime-security-for-private-apps)|[Publiczny punkt końcowy](#runtime-security-for-public-apps)|
|:--|:--|
|Dostępne dla właściciela i współautorów|Dostępne dla właściciela, współautorów i innych osób, które wiedzą o IDENTYFIKATORze aplikacji|

Można kontrolować, kto widzi klucz środowiska uruchomieniowego LUIS, wywołując go w środowisku serwer-serwer. Jeśli używasz LUIS z bot, połączenie między bot i LUIS jest już bezpieczne. W przypadku bezpośredniego wywoływania punktu końcowego LUIS należy utworzyć interfejs API po stronie serwera (na przykład [funkcję](https://azure.microsoft.com/services/functions/)platformy Azure) z dostępem kontrolowanym (na przykład [AAD](https://azure.microsoft.com/services/active-directory/)). Gdy interfejs API po stronie serwera jest wywoływany i uwierzytelniany, a Autoryzacja jest weryfikowana, należy przekazać wywołanie do LUIS. Chociaż ta strategia nie zapobiega atakom typu man-in-the-Middle, zasłania klucz i adres URL punktu końcowego od użytkowników, umożliwia śledzenie dostępu i umożliwia dodanie rejestrowania odpowiedzi na punkt końcowy (na przykład [Application Insights](https://azure.microsoft.com/services/application-insights/)).

#### <a name="runtime-security-for-private-apps"></a>Zabezpieczenia środowiska uruchomieniowego dla prywatnych aplikacji

Środowisko uruchomieniowe aplikacji prywatnej jest dostępne tylko dla następujących:

|Klucz i użytkownik|Wyjaśnienie|
|--|--|
|Klucz autorstwa właściciela| Do 1000 trafień punktów końcowych|
|Współautorzy/klucze autorstwa| Do 1000 trafień punktów końcowych|
|Dowolny klucz przypisany do LUIS przez autora lub współpracownika/współautora|Na podstawie warstwy użycia klucza|

#### <a name="runtime-security-for-public-apps"></a>Zabezpieczenia środowiska uruchomieniowego dla aplikacji publicznych

Gdy aplikacja zostanie skonfigurowana jako publiczna, _dowolny_ prawidłowy klucz Luis Authoring lub klucz punktu końcowego Luis może wysyłać zapytania do aplikacji, o ile klucz nie użył całego limitu przydziału punktów końcowych.

Użytkownik, który nie jest właścicielem lub współautorem, może uzyskać dostęp do środowiska uruchomieniowego aplikacji publicznej tylko wtedy, gdy podano identyfikator aplikacji. LUIS nie ma publicznego _rynku_ ani innego sposobu wyszukiwania aplikacji publicznej.

Publiczna aplikacja jest publikowana we wszystkich regionach, dzięki czemu użytkownik z kluczem zasobów LUIS opartym na regionie może uzyskać dostęp do aplikacji w dowolnym regionie, który jest skojarzony z kluczem zasobu.

## <a name="transfer-of-ownership"></a>Przeniesienie własności

LUIS nie ma koncepcji przenoszenia własności zasobu.

## <a name="securing-the-endpoint"></a>Zabezpieczanie punktu końcowego

Można kontrolować, kto może wyświetlać klucz punktu końcowego środowiska uruchomieniowego LUIS prognoz, wywołując go w środowisku serwer-serwer. Jeśli używasz LUIS z bot, połączenie między bot i LUIS jest już bezpieczne. W przypadku bezpośredniego wywoływania punktu końcowego LUIS należy utworzyć interfejs API po stronie serwera (na przykład [funkcję](https://azure.microsoft.com/services/functions/)platformy Azure) z dostępem kontrolowanym (na przykład [AAD](https://azure.microsoft.com/services/active-directory/)). Gdy wywoływany jest interfejs API po stronie serwera, a uwierzytelnianie i autoryzacja są weryfikowane, należy przekazać wywołanie do LUIS. Chociaż ta strategia nie zapobiega atakom typu man-in-the-Middle, zasłania swój punkt końcowy od użytkowników, umożliwia śledzenie dostępu i umożliwia dodanie rejestrowania odpowiedzi na punkt końcowy (na przykład [Application Insights](https://azure.microsoft.com/services/application-insights/)).

## <a name="next-steps"></a>Następne kroki

* Opis pojęć związanych z [przechowywaniem wersji](luis-concept-version.md) .
* Dowiedz się [, jak tworzyć klucze](luis-how-to-azure-subscription.md).
