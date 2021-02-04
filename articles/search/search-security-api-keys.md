---
title: Uwierzytelnianie klucza interfejsu API
titleSuffix: Azure Cognitive Search
description: Klucz interfejsu API kontroluje dostęp przychodzący do punktu końcowego usługi. Klucze administratora przyznają prawo do zapisu. Klucze zapytań można utworzyć dla dostępu tylko do odczytu.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: 8b2e85744923fb2e7e474e049df1536aebc56f3c
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99536937"
---
# <a name="create-and-manage-api-keys-for-an-azure-cognitive-search-service"></a>Tworzenie i zarządzanie kluczami interfejsu API dla usługi Wyszukiwanie poznawcze platformy Azure

Wszystkie żądania do usługi wyszukiwania wymagają klucza interfejsu API tylko do odczytu, który został wygenerowany w ramach danej usługi. Klucz interfejsu API jest jedynym mechanizmem uwierzytelniania żądań przychodzących do punktu końcowego usługi wyszukiwania i jest wymagany dla każdego żądania. 

+ W [rozwiązaniach REST](search-get-started-rest.md) `api-key` jest zazwyczaj określona w nagłówku żądania

+ W [rozwiązaniach platformy .NET](search-howto-dotnet-sdk.md)klucz jest często określany jako ustawienie konfiguracji, a następnie przenoszona jako [AzureKeyCredential](/dotnet/api/azure.azurekeycredential)

Klucze interfejsu API można wyświetlać i zarządzać nimi w [Azure Portal](https://portal.azure.com)lub za pomocą [programu PowerShell](/powershell/module/az.search), interfejsu [wiersza polecenia platformy Azure lub usługi](/cli/azure/search) [API REST](/rest/api/searchmanagement/).

:::image type="content" source="media/search-manage/azure-search-view-keys.png" alt-text="Strona portalu, Pobieranie ustawień, sekcja kluczy" border="false":::

## <a name="what-is-an-api-key"></a>Co to jest klucz interfejsu API?

Klucz interfejsu API jest unikatowym ciągiem zawierającym losowo wygenerowane numery i litery, które są przesyłane na każde żądanie do usługi wyszukiwania. Usługa zaakceptuje żądanie, jeśli zarówno samo żądanie, jak i klucz są prawidłowe. 

Do uzyskiwania dostępu do usługi wyszukiwania są używane dwa typy kluczy: administrator (odczyt i zapis) i zapytanie (tylko do odczytu).

|Klucz|Opis|Limity|  
|---------|-----------------|------------|  
|Administrator|Przyznaje pełne prawa do wszystkich operacji, w tym możliwość zarządzania usługą, tworzenia i usuwania indeksów, indeksatorów i źródeł danych.<br /><br /> Dwa klucze administratora, określane jako klucze *podstawowe* i *pomocnicze* w portalu, są generowane podczas tworzenia usługi i mogą być osobno ponownie generowane na żądanie. Posiadanie dwóch kluczy umożliwia przewinięcie jednego klucza podczas korzystania z drugiego klucza w celu uzyskania ciągłego dostępu do usługi.<br /><br /> Klucze administratora są określone tylko w nagłówkach żądań HTTP. Nie można umieścić klucza interfejsu API administratora w adresie URL.|Maksymalnie 2 na usługę|  
|Zapytanie|Przyznaje dostęp tylko do odczytu do indeksów i dokumentów i są zazwyczaj dystrybuowane do aplikacji klienckich, które wysyłają żądania wyszukiwania.<br /><br /> Klucze zapytań są tworzone na żądanie.<br /><br /> Klucze zapytania można określić w nagłówku żądania HTTP dla operacji wyszukiwania, sugestii lub wyszukiwania. Alternatywnie można przekazać klucz zapytania jako parametr w adresie URL. W zależności od sposobu, w jaki aplikacja kliencka formułuje żądanie, może być łatwiejsze przekazanie klucza jako parametru zapytania:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2020-06-30&api-key=[query key]`|50 za usługę|  

 Wizualnie nie istnieje różnica między kluczem administratora lub kluczem zapytania. Oba klucze są ciągami składającymi się z 32 losowo generowanych znaków alfanumerycznych. W przypadku utraty informacji o typie klucza określonego w aplikacji można [sprawdzić wartości klucza w portalu](https://portal.azure.com).  

> [!NOTE]  
> Jest to uznawane za słabe rozwiązanie w zakresie zabezpieczeń, które umożliwia przekazywanie poufnych danych, takich jak `api-key` w identyfikatorze URI żądania. Z tego powodu usługa Azure Wyszukiwanie poznawcze akceptuje tylko klucz zapytania jako `api-key` ciąg zapytania i należy unikać tego, chyba że zawartość indeksu powinna być publicznie dostępna. Zgodnie z ogólną regułą zalecamy przekazanie `api-key` nagłówka jako żądania.  

## <a name="find-existing-keys"></a>Znajdowanie istniejących kluczy

Klucze dostępu można uzyskać w portalu lub za pomocą [programu PowerShell](/powershell/module/az.search), interfejsu [wiersza polecenia platformy Azure](/cli/azure/search)lub [API REST](/rest/api/searchmanagement/).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyświetl listę [usług wyszukiwania](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) dla Twojej subskrypcji.
1. Wybierz usługę i na stronie Przegląd kliknij pozycję **Ustawienia**  > **klucze** , aby wyświetlić klucze administratora i kwerendy.

   :::image type="content" source="media/search-security-overview/settings-keys.png" alt-text="Strona portalu, ustawienia widoku, sekcja klucze" border="false":::

## <a name="create-query-keys"></a>Utwórz klucze zapytań

Klucze zapytań są używane na potrzeby dostępu tylko do odczytu do dokumentów w indeksie dla operacji docelowych kolekcji dokumentów. Zapytania wyszukiwania, filtrowania i sugestii to wszystkie operacje, które pobierają klucz zapytania. Wszystkie operacje tylko do odczytu, które zwracają dane systemowe lub definicje obiektów, takie jak definicja indeksu lub stan indeksatora, wymagają klucza administratora.

Ograniczanie dostępu i operacji w aplikacjach klienckich jest niezbędne do ochrony zasobów wyszukiwania w usłudze. Zawsze używaj klucza zapytania zamiast klucza administratora dla dowolnego zapytania pochodzącego z aplikacji klienckiej.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wyświetl listę [usług wyszukiwania](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)  dla Twojej subskrypcji.
3. Wybierz usługę i na stronie Przegląd kliknij pozycję **Ustawienia**  > **klucze**.
4. Kliknij pozycję **Zarządzaj kluczami zapytań**.
5. Użyj klucza zapytania już wygenerowanego dla usługi lub Utwórz do 50 nowych kluczy zapytań. Domyślny klucz zapytania nie ma nazwy, ale dodatkowe klucze zapytania można nazwać do zarządzania.

   :::image type="content" source="media/search-security-overview/create-query-key.png" alt-text="Tworzenie lub używanie klucza zapytania" border="false":::

> [!Note]
> Przykład kodu przedstawiający użycie klucza zapytania można znaleźć w [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

<a name="regenerate-admin-keys"></a>

## <a name="regenerate-admin-keys"></a>Wygeneruj ponownie klucze administratora

Dla każdej usługi są tworzone dwa klucze administracyjne, dzięki czemu można obrócić klucz podstawowy przy użyciu klucza pomocniczego dla ciągłości biznesowej.

1. Na stronie   > **klucze** ustawień skopiuj klucz pomocniczy.
2. W przypadku wszystkich aplikacji zaktualizuj ustawienia klucza interfejsu API, aby użyć klucza pomocniczego.
3. Wygeneruj ponownie klucz podstawowy.
4. Zaktualizuj wszystkie aplikacje, aby użyć nowego klucza podstawowego.

Jeśli przypadkowo ponownie wygenerowano oba klucze jednocześnie, wszystkie żądania klientów korzystające z tych kluczy zakończą się niepowodzeniem z niedozwolonym protokołem HTTP 403. Jednak zawartość nie zostanie usunięta i nie jest trwale zablokowana. 

Nadal możesz uzyskać dostęp do usługi za pomocą portalu lub programowo. Funkcje zarządzania działają za pomocą identyfikatora subskrypcji, a nie klucza interfejsu API usługi, a tym samym nadal dostępne, nawet jeśli klucze interfejsu API nie są. 

Po utworzeniu nowych kluczy za pośrednictwem portalu lub warstwy zarządzania dostęp jest przywracany do zawartości (indeksy, indeksatory, źródła danych, mapy synonimów) po utworzeniu nowych kluczy i udostępnieniu tych kluczy na żądaniach.

## <a name="secure-api-keys"></a>Klucze secure API

Za pomocą [uprawnień opartych na rolach](search-security-rbac.md)można usuwać lub odczytywać klucze, ale nie można zastąpić klucza za pomocą hasła zdefiniowanego przez użytkownika ani używać Active Directory jako podstawowej metodologii uwierzytelniania do uzyskiwania dostępu do operacji wyszukiwania. 

Zabezpieczenia klucza są zapewnione przez ograniczenie dostępu za pośrednictwem portalu lub Menedżer zasobów interfejsów (program PowerShell lub interfejs wiersza polecenia). Jak wspomniano, Administratorzy subskrypcji mogą wyświetlać i generować ponownie wszystkie klucze interfejsu API. Jako środek ostrożności Przejrzyj przypisania ról, aby zrozumieć, kto ma dostęp do kluczy administratora.

+ Na pulpicie nawigacyjnym usługi kliknij pozycję **Kontrola dostępu (IAM)** , a następnie kartę **przypisania ról** , aby wyświetlić przypisania ról dla usługi.

Członkowie następujących ról mogą wyświetlać i ponownie generować klucze: właściciel, współautor, [Search Service Współautorzy](../role-based-access-control/built-in-roles.md#search-service-contributor)

## <a name="see-also"></a>Zobacz też

+ [Zabezpieczenia w usłudze Azure Wyszukiwanie poznawcze](search-security-overview.md)
+ [Kontrola dostępu oparta na rolach na platformie Azure na platformie Azure Wyszukiwanie poznawcze](search-security-rbac.md)
+ [Zarządzanie przy użyciu programu PowerShell](search-manage-powershell.md) 