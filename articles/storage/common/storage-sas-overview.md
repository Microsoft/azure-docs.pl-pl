---
title: Udzielanie ograniczonego dostępu do danych za pomocą sygnatur dostępu współdzielonego (SAS)
titleSuffix: Azure Storage
description: Informacje o używaniu sygnatur dostępu współdzielonego (SAS) do delegowania dostępu do zasobów usługi Azure Storage, w tym obiektów blob, kolejek, tabel i plików.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/28/2020
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.openlocfilehash: 51e73222233602491b0c8ed3835d032610c68e0d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104722790"
---
# <a name="grant-limited-access-to-azure-storage-resources-using-shared-access-signatures-sas"></a>Udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego (SAS)

Sygnatura dostępu współdzielonego zapewnia bezpieczny dostęp delegowany do zasobów na koncie magazynu. Za pomocą sygnatury dostępu współdzielonego masz szczegółową kontrolę nad sposobem, w jaki klient może uzyskać dostęp do danych. Na przykład:
 
- Zasoby, do których klient może uzyskać dostęp.

- Jakie uprawnienia mają do tych zasobów.

- Jak długo sygnatura dostępu współdzielonego jest prawidłowa.

## <a name="types-of-shared-access-signatures"></a>Typy sygnatur dostępu współdzielonego

Usługa Azure Storage obsługuje trzy typy sygnatur dostępu współdzielonego:

- Sygnatura dostępu współdzielonego użytkownika

- SAS usługi

- SAS konta

### <a name="user-delegation-sas"></a>Sygnatura dostępu współdzielonego użytkownika

Sygnatura dostępu współdzielonego delegowania użytkownika jest zabezpieczana za pomocą poświadczeń Azure Active Directory (Azure AD), a także uprawnień określonych dla sygnatury dostępu współdzielonego. Sygnatura dostępu współdzielonego użytkownika ma zastosowanie tylko do magazynu obiektów BLOB.

Aby uzyskać więcej informacji o funkcji sygnatury dostępu współdzielonego delegowania użytkowników, zobacz [Tworzenie skojarzeń zabezpieczeń dla delegowania użytkowników (API REST)](/rest/api/storageservices/create-user-delegation-sas).

### <a name="service-sas"></a>SAS usługi

Sygnatura dostępu współdzielonego usługi jest zabezpieczona za pomocą klucza konta magazynu. Sygnatura dostępu współdzielonego usługi deleguje dostęp do zasobu tylko w jednej z usług Azure Storage: BLOB Storage, queue storage, Table Storage lub Azure Files.

Aby uzyskać więcej informacji na temat sygnatury dostępu współdzielonego usługi, zobacz [Tworzenie sygnatury dostępu współdzielonego usługi (API REST)](/rest/api/storageservices/create-service-sas).

### <a name="account-sas"></a>SAS konta

Sygnatura dostępu współdzielonego konta jest zabezpieczona za pomocą klucza konta magazynu. Sygnatura dostępu współdzielonego konta deleguje dostęp do zasobu w co najmniej jednej usłudze magazynu. Wszystkie operacje dostępne za pośrednictwem sygnatury dostępu współdzielonego usługi lub użytkownika są również dostępne za pośrednictwem SYGNATURy dostępu współdzielonego konta. 

Można również delegować dostęp do następujących:

- Operacje na poziomie usług (na przykład **właściwości usługi pobierania/ustawiania** i **pobierania statystyk usługi** ). 

- Operacje odczytu, zapisu i usuwania, które nie są dozwolone przy użyciu sygnatury dostępu współdzielonego usługi. 

Aby uzyskać więcej informacji na temat sygnatury dostępu współdzielonego konta, [Utwórz sygnaturę dostępu współdzielonego konta (API REST)](/rest/api/storageservices/create-account-sas).

> [!NOTE]
> Firma Microsoft zaleca używanie poświadczeń usługi Azure AD, jeśli to możliwe, jako najlepszych rozwiązań w zakresie zabezpieczeń zamiast używania klucza konta, co może być bardziej łatwe. Gdy projekt aplikacji wymaga sygnatur dostępu współdzielonego w celu uzyskania dostępu do usługi BLOB Storage, Użyj poświadczeń usługi Azure AD, aby utworzyć SYGNATURę czasową delegowania użytkowników, jeśli jest to możliwe dla najwyższej jakości zabezpieczeń. Aby uzyskać więcej informacji, zobacz [Autoryzuj dostęp do obiektów blob i kolejek przy użyciu Azure Active Directory](storage-auth-aad.md).

Sygnatura dostępu współdzielonego może przyjmować jedną z następujących dwóch form:

- **Sygnatury dostępu współdzielonego ad hoc**. Podczas tworzenia sygnatury dostępu współdzielonego ad hoc w identyfikatorze URI SAS jest określony czas rozpoczęcia, czas wygaśnięcia i uprawnienia. Każdy typ SYGNATURy dostępu współdzielonego może być sygnaturą dostępu współdzielonego ad hoc.

- **SAS usługi z zapisanymi zasadami dostępu**. Przechowywane zasady dostępu są definiowane w kontenerze zasobów, który może być kontenerem obiektów blob, tabelą, kolejką lub udziałem plików. Przechowywane zasady dostępu mogą służyć do zarządzania ograniczeniami dla co najmniej jednego sygnatury dostępu współdzielonego usługi. W przypadku kojarzenia sygnatury dostępu współdzielonego usługi z przechowywanymi zasadami, skojarzenie SAS dziedziczy ograniczenia &mdash; czas rozpoczęcia, czas wygaśnięcia i uprawnienia &mdash; zdefiniowane dla przechowywanych zasad dostępu.

> [!NOTE]
> Sygnatura dostępu współdzielonego użytkownika lub sygnatury dostępu współdzielonego konta musi być ad hoc. Przechowywane zasady dostępu nie są obsługiwane w przypadku skojarzeń zabezpieczeń delegowania użytkownika lub konta SAS.

## <a name="how-a-shared-access-signature-works"></a>Jak działa sygnatura dostępu współdzielonego

Sygnatura dostępu współdzielonego jest podpisanym identyfikatorem URI, który wskazuje co najmniej jeden zasób magazynu. Identyfikator URI zawiera token, który zawiera specjalny zestaw parametrów zapytania. Token wskazuje, w jaki sposób zasoby mogą być dostępne przez klienta. Jeden z parametrów zapytania, sygnatura jest zbudowany z parametrów sygnatury dostępu współdzielonego i podpisany przy użyciu klucza, który został użyty do utworzenia sygnatury dostępu współdzielonego. Ta sygnatura jest używana przez usługę Azure Storage do autoryzacji dostępu do zasobu magazynu.

> [!NOTE]
> Nie jest możliwe przeprowadzenie inspekcji generacji tokenów SAS. Każdy użytkownik, który ma uprawnienia do generowania tokenu SAS, przy użyciu klucza konta lub przypisania roli RBAC platformy Azure, może to zrobić bez znajomości właściciela konta magazynu. Należy zachować ostrożność, aby ograniczyć uprawnienia umożliwiające użytkownikom generowanie tokenów SAS. Aby uniemożliwić użytkownikom generowanie SYGNATURy dostępu współdzielonego podpisane przy użyciu klucza konta dla obciążeń obiektów blob i kolejek, można uniemożliwić dostęp do konta magazynu za pomocą klucza. Aby uzyskać więcej informacji, zobacz [zapobieganie autoryzacji za pomocą klucza współużytkowanego](shared-key-authorization-prevent.md).

### <a name="sas-signature-and-authorization"></a>Sygnatura dostępu współdzielonego i autoryzacja

Token sygnatury dostępu współdzielonego można podpisać za pomocą klucza delegowania użytkownika lub klucza konta magazynu (klucza wspólnego). 

#### <a name="signing-a-sas-token-with-a-user-delegation-key"></a>Podpisywanie tokenu sygnatury dostępu współdzielonego za pomocą klucza delegowania użytkownika

Token sygnatury dostępu współdzielonego można podpisać przy użyciu *klucza delegowania użytkownika* , który został utworzony przy użyciu poświadczeń Azure Active Directory (Azure AD). Sygnatura dostępu współdzielonego delegowania użytkownika jest podpisywana przy użyciu klucza delegowania użytkownika.

Aby uzyskać klucz, a następnie utworzyć SYGNATURę dostępu współdzielonego, podmiot zabezpieczeń usługi Azure AD musi mieć przypisaną rolę platformy Azure obejmującą `Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey` akcję. Aby uzyskać więcej informacji, zobacz [Tworzenie sygnatury dostępu współdzielonego dla delegowania użytkowników (interfejs API REST)](/rest/api/storageservices/create-user-delegation-sas).

#### <a name="signing-a-sas-token-with-an-account-key"></a>Podpisywanie tokenu sygnatury dostępu współdzielonego przy użyciu klucza konta

Sygnatura dostępu współdzielonego usługi i sygnatura dostępu współdzielonego konta są podpisane przy użyciu klucza konta magazynu. Aby utworzyć sygnaturę dostępu współdzielonego, która jest podpisana przy użyciu klucza konta, aplikacja musi mieć dostęp do klucza konta.

Gdy żądanie zawiera token sygnatury dostępu współdzielonego, żądanie jest autoryzowane w zależności od tego, jak token sygnatury dostępu współdzielonego jest podpisany. Klucz dostępu lub poświadczenia używane do tworzenia tokenu SAS są również używane przez usługę Azure Storage w celu udzielania dostępu klientowi, który ma sygnaturę SAS.

Poniższa tabela zawiera podsumowanie sposobu autoryzacji poszczególnych typów tokenów SAS.

| Typ sygnatury dostępu współdzielonego | Typ autoryzacji |
|-|-|
| Sygnatura dostępu współdzielonego użytkownika (tylko magazyn obiektów BLOB) | Azure AD |
| SAS usługi | Klucz wspólny |
| SAS konta | Klucz wspólny |

Firma Microsoft zaleca korzystanie z funkcji dostępu współdzielonego delegowania użytkowników w celu zapewnienia bezpieczeństwa.

### <a name="sas-token"></a>Token SAS

Token sygnatury dostępu współdzielonego jest ciągiem generowanym po stronie klienta, na przykład przy użyciu jednej z bibliotek klienta usługi Azure Storage. Token sygnatury dostępu współdzielonego nie jest śledzony przez usługę Azure Storage w jakikolwiek sposób. Po stronie klienta można utworzyć nieograniczoną liczbę tokenów SAS. Po utworzeniu sygnatury dostępu współdzielonego można je rozesłać do aplikacji klienckich, które wymagają dostępu do zasobów na koncie magazynu.

Aplikacje klienckie zapewniają identyfikator URI sygnatury dostępu współdzielonego do usługi Azure Storage w ramach żądania. Następnie usługa sprawdza parametry sygnatury dostępu współdzielonego i sygnaturę, aby sprawdzić, czy jest ona prawidłowa. Jeśli usługa sprawdza, czy podpis jest prawidłowy, żądanie jest autoryzowane. W przeciwnym razie żądanie zostanie odrzucone z kodem błędu 403 (dostęp zabroniony).

Oto przykład identyfikatora URI sygnatury dostępu współdzielonego usługi, pokazującego identyfikator URI zasobu i token sygnatury dostępu współdzielonego. Ponieważ token sygnatury dostępu współdzielonego składa się z ciągu zapytania identyfikatora URI, identyfikator URI zasobu musi poprzedzać znak zapytania, a następnie token sygnatury dostępu współdzielonego:

![Składniki identyfikatora URI sygnatury dostępu współdzielonego usługi](./media/storage-sas-overview/sas-storage-uri.png)

## <a name="when-to-use-a-shared-access-signature"></a>Kiedy używać sygnatury dostępu współdzielonego

Użyj sygnatury dostępu współdzielonego, aby zapewnić bezpieczny dostęp do zasobów na koncie magazynu wszystkim klientom, którzy nie mają w inny sposób uprawnień do tych zasobów.

Typowym scenariuszem, w którym jest przydatny sygnatura dostępu współdzielonego, jest usługa, w której użytkownicy odczytują i zapisują własne dane na koncie magazynu. W scenariuszu, w którym dane użytkowników są przechowywane na koncie magazynu, zwykle występują dwa wzorce projektowe:

1. Klienci przekazują i pobierają dane za pośrednictwem usługi serwera proxy frontonu, która przeprowadza uwierzytelnianie. Ta usługa frontonu proxy umożliwia weryfikację reguł firmy. Jednak w przypadku dużych ilości danych lub transakcji o dużych ilościach Tworzenie usługi, która może skalować w celu dopasowania do popytu, może być kosztowne lub trudne.

   ![Diagram scenariusza: usługa frontonu serwera proxy](./media/storage-sas-overview/sas-storage-fe-proxy-service.png)

2. Uproszczona usługa uwierzytelnia klienta zgodnie z potrzebami, a następnie generuje sygnaturę dostępu współdzielonego. Gdy aplikacja kliencka uzyska sygnaturę dostępu współdzielonego, może uzyskać bezpośredni dostęp do zasobów konta magazynu. Uprawnienia dostępu są definiowane przez SAS i dla interwału dozwolonego przez SAS. Sygnatura dostępu współdzielonego zmniejsza konieczność kierowania wszystkich danych przez usługę serwera proxy frontonu.

   ![Diagram scenariusza: usługa dostawcy SAS](./media/storage-sas-overview/sas-storage-provider-service.png)

Wiele usług rzeczywistych może korzystać z hybrydowych dwóch metod. Na przykład niektóre dane mogą być przetwarzane i weryfikowane za pośrednictwem serwera proxy frontonu. Inne dane są zapisywane i/lub odczytywane bezpośrednio przy użyciu sygnatury dostępu współdzielonego.

Ponadto sygnatura dostępu współdzielonego jest wymagana, aby autoryzować dostęp do obiektu źródłowego w ramach operacji kopiowania w określonych scenariuszach:

- W przypadku kopiowania obiektu BLOB do innego obiektu BLOB znajdującego się w innym koncie magazynu. 
  
  Opcjonalnie można użyć sygnatury dostępu współdzielonego, aby autoryzować również dostęp do docelowego obiektu BLOB.

- Kopiowanie pliku do innego pliku, który znajduje się na innym koncie magazynu. 

  Opcjonalnie można użyć sygnatury dostępu współdzielonego, aby autoryzować również dostęp do pliku docelowego.

- Podczas kopiowania obiektu BLOB do pliku lub pliku do obiektu BLOB. 

  Należy używać sygnatury dostępu współdzielonego, nawet jeśli obiekty źródłowe i docelowe znajdują się w ramach tego samego konta magazynu.

## <a name="best-practices-when-using-sas"></a>Najlepsze rozwiązania dotyczące korzystania z sygnatury dostępu współdzielonego

W przypadku używania sygnatur dostępu współdzielonego w aplikacjach należy znać dwa potencjalne zagrożenia:

- W przypadku przecieków sygnatury dostępu współdzielonego mogą one być używane przez każdego, kto je uzyska, co może spowodować naruszenie zabezpieczeń konta magazynu.

- Jeśli sygnatura dostępu współdzielonego dostarczona do aplikacji klienckiej wygaśnie i aplikacja nie będzie mogła pobrać nowego skojarzenia zabezpieczeń z usługi, funkcjonalność aplikacji może być utrudniona.

Poniższe zalecenia dotyczące korzystania z sygnatur dostępu współdzielonego mogą pomóc w ograniczeniu ryzyka:

- Do tworzenia lub rozpowszechniania sygnatury dostępu współdzielonego **Używaj protokołu HTTPS** . Jeśli sygnatura dostępu współdzielonego jest przesyłana przez protokół HTTP i przechwycona, atakujący wykonuje atak typu man-in-the-Middle w celu odczytania sygnatury dostępu współdzielonego. Następnie mogą używać tego sygnatury dostępu współdzielonego tak samo jak w przypadku zamierzonego użytkownika. Może to spowodować naruszenie poufnych danych lub umożliwienie uszkodzenia danych przez złośliwego użytkownika.

- **Gdy jest to możliwe, Użyj skojarzenia zabezpieczeń delegowania użytkowników.** Sygnatura dostępu współdzielonego w ramach delegowania użytkowników zapewnia wyższy dostęp do SKOJARZEŃ zabezpieczeń usługi lub konta SAS. Sygnatura dostępu współdzielonego delegowania użytkownika jest zabezpieczona przy użyciu poświadczeń usługi Azure AD, dzięki czemu nie trzeba przechowywać klucza konta w kodzie.

- **Ma plan odwołania dla sygnatury dostępu współdzielonego.** Upewnij się, że jest przygotowana odpowiedź w przypadku naruszenia zabezpieczeń SAS.

- **Definiowanie zasad dostępu przechowywanego dla SYGNATURy usługi.** Przechowywane zasady dostępu umożliwiają Odwoływanie uprawnień dla skojarzeń zabezpieczeń usługi bez konieczności ponownego generowania kluczy konta magazynu. Ustaw czas wygaśnięcia na te bardzo daleko w przyszłość (lub nieskończonie) i upewnij się, że jest on regularnie aktualizowany, aby przenieść go dalej do przyszłości.

- **W przypadku sygnatury dostępu współdzielonego usługi ad hoc SAS lub SYGNATURy dostępu współdzielonego konta użyj niemal okresu ważności.** W ten sposób nawet w przypadku naruszenia zabezpieczeń SAS jest on ważny tylko przez krótki czas. To rozwiązanie jest szczególnie ważne, jeśli nie można odwołać się do przechowywanych zasad dostępu. Długoterminowe czasy wygaśnięcia również ograniczają ilość danych, które mogą być zapisywane w obiekcie blob przez ograniczenie czasu dostępnego do przekazania do niego.

- **Klienci mają automatycznie odnawiać sygnaturę dostępu współdzielonego w razie potrzeby.** Klienci powinni odnawiać sygnaturę dostępu współdzielonego przed upływem terminu wygaśnięcia, aby umożliwić ponowną próbę w przypadku niedostępności usługi dostarczającej sygnaturę dostępu współdzielonego. W niektórych przypadkach może to być niepotrzebne. Na przykład można zamierzyć użycie sygnatury dostępu współdzielonego na potrzeby niewielkiej liczby natychmiastowych operacji krótkoterminowych. Te operacje powinny zostać ukończone w okresie ważności. W związku z tym nie oczekuje się odnowienia sygnatury dostępu współdzielonego. Jeśli jednak masz klienta, który rutynowo przesyła żądania za pośrednictwem sygnatury dostępu współdzielonego, możliwość wygaśnięcia jest dostępna. 

- **Należy zachować ostrożność w przypadku uruchomienia sygnatury dostępu współdzielonego.** Jeśli ustawisz godzinę rozpoczęcia dla sygnatury dostępu współdzielonego z bieżącą godziną, awarie mogą wystąpić sporadycznie przez pierwsze kilka minut. Jest to spowodowane różnymi maszynami, które mają nieco inne aktualne czasy (nazywane pochyleniem zegara). Ogólnie rzecz biorąc Ustaw czas rozpoczęcia na co najmniej 15 minut w przeszłości. Lub nie ustawiaj jej wcale, co spowoduje, że będzie ona natychmiast ważna we wszystkich przypadkach. To samo ogólnie dotyczy czasu wygaśnięcia i pamiętaj, że w dowolnym kierunku każdego żądania może być pochylone do 15 minut. W przypadku klientów korzystających z wersji REST starszej niż 2012-02-12 maksymalny czas trwania sygnatury dostępu współdzielonego, która nie odwołuje się do przechowywanych zasad, wynosi 1 godzinę. Wszystkie zasady, które określają dłuższy okres niż 1 godzina, zakończą się niepowodzeniem.

- **Należy zachować ostrożność przy użyciu formatu sygnatury dostępu współdzielonego.** W przypadku niektórych narzędzi (takich jak AzCopy) wymagane formaty DateTime to "+% Y-% m-% dT% H:%M:% SZ". W tym formacie uwzględniono kilka sekund. 

- **Być specyficzne dla zasobu do uzyskania dostępu.** Najlepszym rozwiązaniem w zakresie zabezpieczeń jest zapewnienie użytkownikowi minimalnych wymaganych uprawnień. Jeśli użytkownik potrzebuje tylko dostępu do odczytu do pojedynczej jednostki, udziel im dostępu do odczytu dla tej pojedynczej jednostki, a dostęp do odczytu/zapisu/usuwania nie zostanie usunięty do wszystkich jednostek. Pozwala to również zmniejszyć szkody w przypadku naruszenia bezpieczeństwa SAS, ponieważ SAS ma mniej mocy w ręce osoby atakującej.

- **Należy pamiętać, że konto będzie rozliczane za użycie, w tym za pośrednictwem sygnatury dostępu współdzielonego.** W przypadku zapewnienia dostępu do zapisu do obiektu BLOB użytkownik może zdecydować się na przekazanie obiektu BLOB 200 GB. Jeśli przyznano im dostęp do odczytu, może to potrwać pobranie 10 razy, co naliczane jest 2 TB kosztów ruchu wychodzącego. Ponownie Udostępnij ograniczone uprawnienia, aby pomóc w ograniczeniu potencjalnych działań złośliwych użytkowników. Użyj krótkich sygnatur dostępu współdzielonego, aby zmniejszyć to zagrożenie (ale jest to świadome przesunięcia zegara w czasie zakończenia).

- **Sprawdzanie poprawności danych pisanych przy użyciu sygnatury dostępu współdzielonego.** Gdy aplikacja kliencka zapisuje dane na koncie magazynu, należy pamiętać, że mogą wystąpić problemy z tymi danymi. Aby sprawdzić poprawność danych, należy wykonać tę walidację po zapisaniu danych i przed jej użyciem przez aplikację. Ta metoda zapewnia również ochronę przed uszkodzonymi lub złośliwymi danymi, które są zapisywane na koncie, przez użytkownika, który prawidłowo nabył sygnaturę dostępu współdzielonego, lub przez użytkownika korzystającego z przecieków SAS.

- **Informacje o tym, kiedy nie należy używać sygnatury dostępu współdzielonego.** Czasami ryzyko związane z konkretną operacją w odniesieniu do konta magazynu ma większe korzyści wynikające z użycia sygnatury dostępu współdzielonego. W przypadku takich operacji Utwórz usługę warstwy środkowej, która zapisuje dane na koncie magazynu po przeprowadzeniu weryfikacji reguły biznesowej, uwierzytelniania i inspekcji. Ponadto czasami łatwiej jest zarządzać dostępem w inny sposób. Na przykład jeśli chcesz, aby wszystkie obiekty blob w kontenerze były dostępne publicznie, możesz utworzyć kontener jako publiczny, a nie dostarczając sygnatury dostępu współdzielonego do każdego klienta, aby uzyskać dostęp.

- **Użyj Azure Monitor i dzienników usługi Azure Storage do monitorowania aplikacji.** Błędy autoryzacji mogą wystąpić z powodu awarii w usłudze dostawcy SAS. Mogą one również wystąpić z przypadkowym usunięciem przechowywanych zasad dostępu. Możesz użyć rejestrowania usługi Azure Monitor i magazynu Analytics, aby obserwować wszelkie wystąpienia błędów autoryzacji tego typu. Aby uzyskać więcej informacji, zobacz [metryki usługi Azure Storage w Azure monitor](../blobs/monitor-blob-storage.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json) i [Azure Storage Analytics rejestrowania](storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Magazyn nie śledzi liczby sygnatur dostępu współdzielonego, które zostały wygenerowane dla konta magazynu, a interfejs API nie może podawać tych informacji. Jeśli musisz znać liczbę sygnatur dostępu współdzielonego, które zostały wygenerowane dla konta magazynu, musisz ręcznie śledzić numer.

## <a name="get-started-with-sas"></a>Wprowadzenie do SAS

Aby rozpocząć pracę z sygnaturami dostępu współdzielonego, zapoznaj się z następującymi artykułami dotyczącymi każdego typu SAS.

### <a name="user-delegation-sas"></a>Sygnatura dostępu współdzielonego użytkownika

- [Tworzenie sygnatury dostępu współdzielonego użytkownika dla kontenera lub obiektu BLOB przy użyciu programu PowerShell](../blobs/storage-blob-user-delegation-sas-create-powershell.md)
- [Tworzenie sygnatury dostępu współdzielonego użytkownika dla kontenera lub obiektu BLOB za pomocą interfejsu wiersza polecenia platformy Azure](../blobs/storage-blob-user-delegation-sas-create-cli.md)
- [Tworzenie sygnatury dostępu współdzielonego użytkownika dla kontenera lub obiektu BLOB przy użyciu platformy .NET](../blobs/storage-blob-user-delegation-sas-create-dotnet.md)

### <a name="service-sas"></a>SAS usługi

- [Tworzenie sygnatury dostępu współdzielonego usługi dla kontenera lub obiektu BLOB przy użyciu platformy .NET](../blobs/sas-service-create.md)

### <a name="account-sas"></a>SAS konta

- [Tworzenie sygnatury dostępu współdzielonego konta przy użyciu platformy .NET](storage-account-sas-create-dotnet.md)

## <a name="next-steps"></a>Następne kroki

- [Delegowanie dostępu za pomocą sygnatury dostępu współdzielonego (API REST)](/rest/api/storageservices/delegate-access-with-shared-access-signature)
- [Tworzenie sygnatury dostępu współdzielonego (API REST) delegowania użytkownika](/rest/api/storageservices/create-user-delegation-sas)
- [Tworzenie sygnatury dostępu współdzielonego usługi (interfejs API REST)](/rest/api/storageservices/create-service-sas)
- [Tworzenie sygnatury dostępu współdzielonego konta (interfejs API REST)](/rest/api/storageservices/create-account-sas)
