---
title: Omówienie struktury interfejsu użytkownika usług Azure Communication Services
titleSuffix: An Azure Communication Services conceptual document
description: Dowiedz się więcej na temat struktury interfejsu użytkownika usług Azure Communication Services
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 47a32815ded5809edfde856a38c69ec7c6fd6fdf
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103493360"
---
# <a name="azure-communication-services-ui-framework"></a>Struktura interfejsu użytkownika usług Azure Communication Services

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

:::image type="content" source="../media/ui-framework/pre-and-custom-composite.png" alt-text="Porównanie między wstępnie skompilowanymi i niestandardowymi kompilacjami":::

Struktura interfejsu użytkownika usług Azure Communication Services ułatwia tworzenie nowoczesnych środowisk użytkowników do obsługi komunikacji. Udostępnia on bibliotekę składników interfejsu użytkownika gotowych do użycia w środowisku produkcyjnym, które można upuszczać do aplikacji:

- **Składniki złożone** — te składniki to między innymi kluczowe rozwiązania, które implementują typowe scenariusze komunikacji. Możesz szybko dodać do swoich aplikacji środowisko wywołujące lub czat wideo. Elementy złożone to składniki Open Source utworzone przy użyciu składników podstawowych.
- **Składniki podstawowe** — te składniki są blokami konstrukcyjnymi typu open source, które umożliwiają tworzenie niestandardowych funkcji komunikacji. Składniki są oferowane do możliwości wywoływania i rozmowy, które mogą być połączone ze środowiskami kompilacji. 

Te biblioteki klienta interfejsu użytkownika wykorzystują zarówno [język projektowania Fluent firmy Microsoft](https://developer.microsoft.com/fluentui/) , jak i zasoby. Interfejs użytkownika Fluent zawiera podstawę dla struktury interfejsu użytkownika, która została sprawdzonej przetestowana przez produkty firmy Microsoft.

## <a name="differentiating-components-and-composites"></a>**Rozróżnianie składników i elementów złożonych**

Podstawowe **składniki** są oparte na podstawowych bibliotekach klienckich usług Azure Communication Services i implementują podstawowe akcje, takie jak inicjowanie podstawowych bibliotek klienckich, renderowanie filmów wideo i udostępnianie kontrolek użytkownika w celu wyciszenia, wideo włączania/wyłączania, itp. Te **składniki podstawowe** umożliwiają tworzenie własnych niestandardowych środowisk do układania przy użyciu wstępnie utworzonych, produkcyjnych składników komunikacji gotowej do produkcji.

:::image type="content" source="../media/ui-framework/component-overview.png" alt-text="Omówienie składnika dla struktury interfejsu użytkownika":::

**Składniki złożone** łączą wiele **składników podstawowych** w celu utworzenia pełniejszych środowisk komunikacyjnych. Te składniki wyższego poziomu można łatwo zintegrować z istniejącą aplikacją, aby porzucić w pełni fledge środowisko komunikacyjne bez zadania tworzenia go od podstaw. Deweloperzy mogą skoncentrować się na tworzeniu otaczającego środowiska i przepływie do swoich aplikacji, a także pozostawiać złożoność komunikacji na składniki złożone.

:::image type="content" source="../media/ui-framework/composite-overview.png" alt-text="Omówienie funkcji złożonych dla struktury interfejsu użytkownika":::

## <a name="what-ui-framework-is-best-for-my-project"></a>Jaka Struktura interfejsu użytkownika jest Najlepsza dla mojego projektu?

Zrozumienie tych wymagań ułatwi wybranie odpowiedniej biblioteki klienta:

- **Ile potrzebujesz dostosowania?** Podstawowe biblioteki klienckie usługi Azure Communication nie mają środowiska użytkownika i zostały zaprojektowane w taki sposób, aby można było tworzyć dowolne środowiska. Składniki struktury interfejsu użytkownika zapewniają zasoby interfejsu użytkownika kosztem ograniczonego dostosowywania.
- **Czy są wymagane funkcje spotkania?** System spotkań ma kilka unikatowych możliwości, które nie są obecnie dostępne w podstawowych bibliotekach klienckich usług Azure Communication Services, takich jak rozmyte tło i podniesione.
- **Jakie platformy są ukierunkowane?** Różne platformy mają różne możliwości.

Szczegółowe informacje o dostępności funkcji w różnych zestawach [SDK interfejsu użytkownika są dostępne w tym miejscu](ui-sdk-features.md), ale poniżej przedstawiono podsumowanie najważniejszych zalet.

|Biblioteka/zestaw SDK klienta|Złożoność implementacji|    Możliwość dostosowywania|  Rozmów| Czat| [Współdziałanie zespołów](./../teams-interop.md)
|---|---|---|---|---|---|---|
|Składniki złożone|Niski|Niski|✔|✔|✕
|Składniki podstawowe|Średnia|Średnia|✔|✔|✕
|Podstawowe biblioteki klienckie|Wys.|Wys.|✔|✔ |✔

## <a name="cost"></a>Koszt

Użycie struktur interfejsu użytkownika platformy Azure nie ma dodatkowego kosztu ani pomiaru na platformie Azure. Płacisz tylko za użycie podstawowej usługi, korzystając z tych samych liczników wywołania, rozmowy i PSTN.

## <a name="supported-use-cases"></a>Obsługiwane przypadki użycia

Rozmów

- Dołącz do wywołania usługi Azure Communications Services z IDENTYFIKATORem grupy

Okno

- Dołącz do rozmowy z usługą Azure Communication Services z IDENTYFIKATORem wątku

## <a name="supported-identities"></a>Obsługiwane tożsamości:

Tożsamość usługi komunikacyjnej platformy Azure jest wymagana do zainicjowania struktury interfejsu użytkownika i uwierzytelnienia w usłudze. Aby uzyskać więcej informacji na temat uwierzytelniania, zobacz [tokeny](../../quickstarts/access-tokens.md) [uwierzytelniania](../authentication.md) i dostępu


## <a name="recommended-architecture"></a>Zalecana architektura 

:::image type="content" source="../media/ui-framework/framework-architecture.png" alt-text="Zalecana architektura interfejsu użytkownika architektura z architekturą klienta i serwera ":::

Składniki złożone i podstawowe są inicjowane przy użyciu tokenu dostępu do usług Azure Communication Services. Tokeny dostępu należy kupować z usług Azure Communication Services za pośrednictwem zaufanej usługi, którą zarządzasz. Aby uzyskać więcej informacji, zobacz [Przewodnik Szybki Start: tworzenie tokenów dostępu](../../quickstarts/access-tokens.md) i [zaufanych usług](../../tutorials/trusted-service-tutorial.md) .

Te biblioteki klienckie wymagają również kontekstu wywołania lub rozmowy, do których zostaną dołączeni. Podobnie jak tokeny dostępu użytkowników, ten kontekst powinien być rozpowszechniany na klientach za pośrednictwem zaufanej usługi. Poniższa lista zawiera podsumowanie funkcji inicjalizacji i zarządzania zasobami, które należy operacjonalizować.

| Zobowiązania contoso                                 | Obowiązki struktury interfejsu użytkownika                         |
|----------------------------------------------------------|-----------------------------------------------------------------|
| Udostępnianie tokenu dostępu z platformy Azure                    | Przekazuj dany token dostępu, aby zainicjować składniki        |
| Funkcja Refresh                                 | Odświeżanie tokenu dostępu przy użyciu funkcji udostępnionej przez dewelopera          |
| Pobierz/Przekaż informacje o dołączeniu do wywołania lub rozmowy          | Przekazuj informacje o wywołaniu i rozmowie, aby zainicjować składniki |
| Pobierz/Przekaż informacje o użytkowniku dla dowolnego niestandardowego modelu danych | Przekazuj niestandardowy model danych do składników do renderowania          |
