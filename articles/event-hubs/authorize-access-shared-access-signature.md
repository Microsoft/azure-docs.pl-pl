---
title: Autoryzuj dostęp za pomocą sygnatury dostępu współdzielonego w usłudze Azure Event Hubs
description: Ten artykuł zawiera informacje dotyczące autoryzowania dostępu do zasobów platformy Azure Event Hubs przy użyciu sygnatur dostępu współdzielonego (SAS).
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 6a2d7385f82864e8d378055333377fb9c3f73c19
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85323126"
---
# <a name="authorizing-access-to-event-hubs-resources-using-shared-access-signatures"></a>Autoryzowanie dostępu do zasobów Event Hubs przy użyciu sygnatur dostępu współdzielonego
Sygnatura dostępu współdzielonego (SAS) zapewnia możliwość udzielenia ograniczonego dostępu do zasobów w przestrzeni nazw Event Hubs. Funkcja SAS chroni dostęp do Event Hubs zasobów na podstawie reguł autoryzacji. Te reguły są konfigurowane w przestrzeni nazw lub jednostce (centrum zdarzeń lub tematu). Ten artykuł zawiera omówienie modelu SAS i przegląd najlepszych rozwiązań dotyczących sygnatury dostępu współdzielonego.

## <a name="what-are-shared-access-signatures"></a>Co to są sygnatury dostępu współdzielonego?
Sygnatura dostępu współdzielonego (SAS) zapewnia delegowany dostęp do Event Hubs zasobów na podstawie reguł autoryzacji. Reguła autoryzacji ma nazwę, jest skojarzona z określonymi prawami i przenosi parę kluczy kryptograficznych. Aby wygenerować tokeny SAS, należy użyć nazwy i klucza reguły za pośrednictwem klientów Event Hubs lub we własnym kodzie. Klient może następnie przekazać token do Event Hubs, aby potwierdzić autoryzację dla żądanych operacji.

Sygnatura dostępu współdzielonego to mechanizm autoryzacji oparty na odszkodowaniach korzystający z prostych tokenów. Przy użyciu sygnatury dostępu współdzielonego klucze nigdy nie są przesyłane do sieci. Klucze są używane do kryptograficznego podpisywania informacji, które mogą zostać później zweryfikowane przez usługę. Sygnatury dostępu współdzielonego można użyć podobnie jak w przypadku schematu nazwy użytkownika i hasła, w którym klient ma bezpośredni dostęp do nazwy reguły autoryzacji i pasującego klucza. Sygnatury dostępu współdzielonego można użyć podobnie jak w przypadku federacyjnego modelu zabezpieczeń, w którym klient otrzymuje ograniczony czas i podpisany token dostępowy z usługi tokenu zabezpieczającego bez konieczności przypisywania klucza podpisywania.

> [!NOTE]
> Usługa Azure Event Hubs obsługuje autoryzowanie do Event Hubs zasobów przy użyciu usługi Azure Active Directory (Azure AD). Autoryzowanie użytkowników lub aplikacji przy użyciu tokenu OAuth 2,0 zwróconego przez usługę Azure AD zapewnia doskonałe zabezpieczenia i łatwość użycia w odniesieniu do sygnatur dostępu współdzielonego (SAS). W przypadku usługi Azure AD nie ma potrzeby przechowywania tokenów w kodzie i ryzyka potencjalnych luk w zabezpieczeniach.
>
> Jeśli to możliwe, firma Microsoft zaleca korzystanie z usługi Azure AD z aplikacjami Event Hubs platformy Azure. Aby uzyskać więcej informacji, zobacz [Autoryzuj dostęp do zasobu platformy Azure Event Hubs przy użyciu Azure Active Directory](authorize-access-azure-active-directory.md).

> [!IMPORTANT]
> Tokeny SAS (sygnatury dostępu współdzielonego) mają kluczowe znaczenie dla ochrony Twoich zasobów. Zapewniając stopień szczegółowości, sygnatura dostępu współdzielonego umożliwia klientom dostęp do zasobów Event Hubs. Nie powinny być udostępniane publicznie. W przypadku udostępniania, jeśli jest to wymagane w celu rozwiązywania problemów, należy rozważyć użycie ograniczonej wersji plików dziennika lub usunięcie tokenów sygnatury dostępu współdzielonego (jeśli istnieją) z plików dziennika, i upewnić się, że zrzuty ekranu nie zawierają informacji o sygnaturze dostępu współdzielonego.

## <a name="shared-access-authorization-policies"></a>Zasady autoryzacji dostępu współdzielonego
Każda przestrzeń nazw Event Hubs i każda jednostka Event Hubs (wystąpienie centrum zdarzeń lub temat Kafka) ma zasady autoryzacji dostępu współdzielonego, które składają się z reguł. Zasady na poziomie przestrzeni nazw mają zastosowanie do wszystkich jednostek w przestrzeni nazw, niezależnie od ich konfiguracji poszczególnych zasad.
Dla każdej reguły zasad autoryzacji użytkownik wybiera trzy informacje: imię i nazwisko, zakres i prawa. Nazwa jest unikatową nazwą w tym zakresie. Zakres jest identyfikatorem URI danego zasobu. W przypadku przestrzeni nazw Event Hubs zakres jest w pełni kwalifikowaną nazwą domeny (FQDN), taką jak `https://<yournamespace>.servicebus.windows.net/` .

Prawa dostarczone przez regułę zasad mogą być kombinacją:
- **Wyślij** — daje prawo do wysyłania komunikatów do jednostki
- **Nasłuchiwanie** — daje prawo do nasłuchiwania lub odbierania do jednostki
- **Zarządzanie** — zapewnia prawo do zarządzania topologią przestrzeni nazw, w tym tworzenia i usuwania jednostek

> [!NOTE]
> Prawo **Zarządzanie** obejmuje prawa do **wysyłania** i **nasłuchiwania** .

W obszarze nazw lub zasadach jednostki można przechowywać maksymalnie 12 reguł autoryzacji dostępu współdzielonego, zapewniając miejsce na trzy zestawy reguł, z których każdy obejmuje prawa podstawowe, oraz kombinację operacji wysyłania i nasłuchiwania. Ten limit podkreśla, że magazyn zasad SAS nie jest przeznaczony do magazynu kont użytkowników ani usług. Jeśli aplikacja musi udzielić dostępu do Event Hubs zasobów na podstawie tożsamości użytkowników lub usług, należy wdrożyć usługę tokenu zabezpieczającego, która wystawia tokeny sygnatury dostępu współdzielonego po sprawdzeniu uwierzytelniania

Do reguły autoryzacji przypisywany jest **klucz podstawowy** i **klucz pomocniczy**. Klucze te są kryptograficznie silnymi kluczami. Nie tracą ani nie wycieków. Będą one zawsze dostępne w Azure Portal. Można użyć dowolnego z wygenerowanych kluczy i można je wygenerować w dowolnym momencie. W przypadku ponownego wygenerowania lub zmiany klucza w zasadach wszystkie wcześniej wystawione tokeny na podstawie tego klucza staną się natychmiast nieprawidłowe. Jednak stałe połączenia utworzone na podstawie takich tokenów będą nadal działały do momentu wygaśnięcia tokenu.

Podczas tworzenia przestrzeni nazw Event Hubs reguła zasad o nazwie **RootManageSharedAccessKey** jest tworzona automatycznie dla przestrzeni nazw. Te zasady mają uprawnienia do **zarządzania** całą przestrzenią nazw. Zaleca się traktowanie tej reguły, takiej jak administracyjne konto główne i nie używanie jej w aplikacji. Dodatkowe reguły zasad można utworzyć na karcie **Konfiguracja** dla przestrzeni nazw w portalu za pośrednictwem programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

## <a name="best-practices-when-using-sas"></a>Najlepsze rozwiązania dotyczące korzystania z sygnatury dostępu współdzielonego
W przypadku używania sygnatur dostępu współdzielonego w aplikacjach należy znać dwa potencjalne zagrożenia:

- W przypadku przecieków sygnatury dostępu współdzielonego mogą one być używane przez każdego, kto je uzyska, co może spowodować naruszenie zasobów Event Hubs.
- Jeśli sygnatura dostępu współdzielonego dostarczona do aplikacji klienckiej wygaśnie i aplikacja nie może pobrać nowego skojarzenia zabezpieczeń z usługi, może to utrudnić działanie aplikacji.

Poniższe zalecenia dotyczące korzystania z sygnatur dostępu współdzielonego mogą pomóc w ograniczeniu ryzyka:

- **Klienci mają automatycznie odnawiać sygnaturę dostępu WSPÓŁdzielonego w razie potrzeby**: klienci powinni odnowić sygnaturę dostępu współdzielonego przed wygaśnięciem, aby umożliwić ponowną próbę, jeśli usługa dostarczająca sygnaturę dostępu współdzielonego Jeśli sygnatura dostępu współdzielonego ma być używana w przypadku niewielkiej liczby natychmiastowych, krótkoterminowych operacji, które powinny być wykonane w okresie wygaśnięcia, może to być niepotrzebne, ponieważ nie jest oczekiwane odnowienie sygnatury dostępu współdzielonego. Jednak jeśli masz klienta, który rutynowo przesyła żądania za pośrednictwem sygnatury dostępu współdzielonego, to możliwość wygaśnięcia jest dostępna. Kluczową kwestią jest zrównoważenie potrzeb, aby sygnatura dostępu współdzielonego była krótkoterminowa (jak wcześniej zostało to określone) z koniecznością zapewnienia, że klient żąda odnowienia na początku, aby uniknąć przerw w działaniu z powodu wygaśnięcia sygnatury dostępu współdzielonego przed pomyślnym odnowieniem.
- **Należy zachować ostrożność w czasie uruchamiania sygnatury dostępu współdzielonego**: Jeśli ustawisz **teraz** czas rozpoczęcia dla sygnatury dostępu współdzielonego, to z powodu pochylenia zegara (różnice w bieżącym czasie w zależności od różnych maszyn) błędy mogą być nieprzerwanie zaobserwowane przez pierwsze kilka minut. Ogólnie rzecz biorąc Ustaw czas rozpoczęcia na co najmniej 15 minut w przeszłości. Lub nie ustawiaj jej wcale, co spowoduje, że będzie ona natychmiast ważna we wszystkich przypadkach. To samo ogólnie dotyczy czasu wygaśnięcia. Należy pamiętać, że w dowolnym kierunku zegara może być pochylony do 15 minut na dowolnym z tych żądań. 
- **Być zależne od zasobu, do którego ma zostać uzyskany dostęp**: najlepszym rozwiązaniem w zakresie zabezpieczeń jest zapewnienie użytkownikowi minimalnych wymaganych uprawnień. Jeśli użytkownik potrzebuje tylko dostępu do odczytu do pojedynczej jednostki, udziel im dostępu do odczytu dla tej pojedynczej jednostki, a dostęp do odczytu/zapisu/usuwania nie zostanie usunięty do wszystkich jednostek. Pomaga również zmniejszyć szkody w przypadku naruszenia bezpieczeństwa SAS, ponieważ SAS ma mniej mocy w ręce osoby atakującej.
- **Nie zawsze używaj sygnatury dostępu współdzielonego**: czasami ryzyko związane z konkretną operacją na Event Hubs ma większe korzyści wynikające z użycia sygnatury dostępu współdzielonego. W przypadku takich operacji Utwórz usługę warstwy środkowej, która zapisuje dane w Event Hubs po sprawdzeniu poprawności reguły biznesowej, uwierzytelnianiu i inspekcji.
- **Zawsze używaj protokołu HTTPS**: zawsze używaj protokołu HTTPS do tworzenia lub dystrybuowania sygnatury dostępu współdzielonego. Jeśli sygnatura dostępu współdzielonego jest przenoszona za pośrednictwem protokołu HTTP i przechwycona, osoba atakująca wykonująca dołączenie w środku jest w stanie odczytać sygnaturę dostępu współdzielonego, a następnie użyć jej w taki sam sposób, jak w przypadku zamierzonego użytkownika.

## <a name="conclusion"></a>Podsumowanie
Sygnatury dostępu współdzielonego są przydatne do udostępniania ograniczonych uprawnień do Event Hubs zasobów klientom. Są one istotną częścią modelu zabezpieczeń dla każdej aplikacji korzystającej z usługi Azure Event Hubs. Jeśli przestrzegasz najlepszych rozwiązań wymienionych w tym artykule, możesz użyć sygnatury dostępu współdzielonego, aby zapewnić większą elastyczność dostępu do zasobów, bez naruszania zabezpieczeń aplikacji.

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującymi artykułami: 

- [Uwierzytelniaj żądania do Event Hubs platformy Azure z aplikacji przy użyciu Azure Active Directory](authenticate-application.md)
- [Uwierzytelnianie zarządzanej tożsamości za pomocą Azure Active Directory w celu uzyskania dostępu do zasobów Event Hubs](authenticate-managed-identity.md)
- [Uwierzytelnianie żądań na platformie Azure Event Hubs przy użyciu sygnatur dostępu współdzielonego](authenticate-shared-access-signature.md)
- [Autoryzuj dostęp do zasobów Event Hubs przy użyciu Azure Active Directory](authorize-access-azure-active-directory.md)


