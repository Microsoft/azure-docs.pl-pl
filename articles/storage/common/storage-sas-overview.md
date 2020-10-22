---
title: Udzielanie ograniczonego dostępu do danych za pomocą sygnatur dostępu współdzielonego (SAS)
titleSuffix: Azure Storage
description: Informacje o używaniu sygnatur dostępu współdzielonego (SAS) do delegowania dostępu do zasobów usługi Azure Storage, w tym obiektów blob, kolejek, tabel i plików.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.openlocfilehash: 218946fcfbf2268ff3b06eab839d69e6b370e891
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92367911"
---
# <a name="grant-limited-access-to-azure-storage-resources-using-shared-access-signatures-sas"></a>Udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego (SAS)

Sygnatura dostępu współdzielonego zapewnia bezpieczny dostęp delegowany do zasobów na koncie magazynu bez naruszania zabezpieczeń danych. Za pomocą sygnatury dostępu współdzielonego masz szczegółową kontrolę nad sposobem, w jaki klient może uzyskać dostęp do danych. Można kontrolować, jakie zasoby mogą uzyskać dostęp do tych zasobów oraz jak długo SYGNATURa czasowa jest ważna, między innymi parametrami.

## <a name="types-of-shared-access-signatures"></a>Typy sygnatur dostępu współdzielonego

Usługa Azure Storage obsługuje trzy typy sygnatur dostępu współdzielonego:

- **Sygnatura dostępu współdzielonego użytkownika.** Sygnatura dostępu współdzielonego delegowania użytkownika jest zabezpieczana za pomocą poświadczeń Azure Active Directory (Azure AD), a także uprawnień określonych dla sygnatury dostępu współdzielonego. Sygnatura dostępu współdzielonego użytkownika ma zastosowanie tylko do magazynu obiektów BLOB.

    Aby uzyskać więcej informacji o funkcji sygnatury dostępu współdzielonego delegowania użytkowników, zobacz [Tworzenie skojarzeń zabezpieczeń dla delegowania użytkowników (API REST)](/rest/api/storageservices/create-user-delegation-sas).

- **Sygnatura dostępu współdzielonego usługi.** Sygnatura dostępu współdzielonego usługi jest zabezpieczona za pomocą klucza konta magazynu. Sygnatura dostępu współdzielonego usługi deleguje dostęp do zasobu tylko w jednej z usług Azure Storage: BLOB Storage, queue storage, Table Storage lub Azure Files.

    Aby uzyskać więcej informacji na temat sygnatury dostępu współdzielonego usługi, zobacz [Tworzenie sygnatury dostępu współdzielonego usługi (API REST)](/rest/api/storageservices/create-service-sas).

- **Sygnatura dostępu współdzielonego konta.** Sygnatura dostępu współdzielonego konta jest zabezpieczona za pomocą klucza konta magazynu. Sygnatura dostępu współdzielonego konta deleguje dostęp do zasobu w co najmniej jednej usłudze magazynu. Wszystkie operacje dostępne za pośrednictwem sygnatury dostępu współdzielonego usługi lub użytkownika są również dostępne za pośrednictwem SYGNATURy dostępu współdzielonego konta. Ponadto za pomocą sygnatury dostępu współdzielonego konta można delegować dostęp do operacji, które są stosowane na poziomie usługi, takich jak **Pobierz/ustaw właściwości usługi** i **Pobierz operacje statystyki usługi** . Możesz również delegować dostęp do operacji odczytu, zapisu i usuwania dla kontenerów obiektów blob, tabel, kolejek i udziałów plików, co jest niedozwolone w wypadku sygnatury dostępu współdzielonego usługi. 

    Aby uzyskać więcej informacji na temat sygnatury dostępu współdzielonego konta, [Utwórz sygnaturę dostępu współdzielonego konta (API REST)](/rest/api/storageservices/create-account-sas).

> [!NOTE]
> Firma Microsoft zaleca używanie poświadczeń usługi Azure AD, jeśli to możliwe, jako najlepszych rozwiązań w zakresie zabezpieczeń zamiast używania klucza konta, co może być bardziej łatwe. Gdy projekt aplikacji wymaga sygnatur dostępu współdzielonego w celu uzyskania dostępu do usługi BLOB Storage, Użyj poświadczeń usługi Azure AD, aby utworzyć SYGNATURę czasową delegowania użytkowników, jeśli jest to możliwe dla najwyższej jakości zabezpieczeń. Aby uzyskać więcej informacji, zobacz [Autoryzuj dostęp do obiektów blob i kolejek przy użyciu Azure Active Directory](storage-auth-aad.md).

Sygnatura dostępu współdzielonego może przyjmować jedną z dwóch form:

- **Sygnatury dostępu współdzielonego ad hoc:** Podczas tworzenia SYGNATURy dostępu współdzielonego ad hoc są wszystkie określone w identyfikatorze URI sygnatury dostępu współdzielonego (lub implikowane, jeśli czas rozpoczęcia zostanie pominięty). Każdy typ SYGNATURy dostępu współdzielonego może być sygnaturą dostępu współdzielonego ad hoc.
- **SAS usługi z zapisanymi zasadami dostępu:** Przechowywane zasady dostępu są definiowane w kontenerze zasobów, który może być kontenerem obiektów blob, tabelą, kolejką lub udziałem plików. Przechowywane zasady dostępu mogą służyć do zarządzania ograniczeniami dla co najmniej jednego sygnatury dostępu współdzielonego usługi. W przypadku kojarzenia sygnatury dostępu współdzielonego usługi z przechowywanymi zasadami, skojarzenie SAS dziedziczy ograniczenia &mdash; czas rozpoczęcia, czas wygaśnięcia i uprawnienia &mdash; zdefiniowane dla przechowywanych zasad dostępu.

> [!NOTE]
> Sygnatura dostępu współdzielonego użytkownika lub sygnatury dostępu współdzielonego konta musi być ad hoc. Przechowywane zasady dostępu nie są obsługiwane w przypadku skojarzeń zabezpieczeń delegowania użytkownika lub konta SAS.

## <a name="how-a-shared-access-signature-works"></a>Jak działa sygnatura dostępu współdzielonego

Sygnatura dostępu współdzielonego jest podpisanym identyfikatorem URI, który wskazuje co najmniej jeden zasób magazynu i zawiera token zawierający specjalny zestaw parametrów zapytania. Token wskazuje, w jaki sposób zasoby mogą być dostępne przez klienta. Jeden z parametrów zapytania, sygnatura jest zbudowany z parametrów sygnatury dostępu współdzielonego i podpisany przy użyciu klucza, który został użyty do utworzenia sygnatury dostępu współdzielonego. Ta sygnatura jest używana przez usługę Azure Storage do autoryzacji dostępu do zasobu magazynu.

### <a name="sas-signature-and-authorization"></a>Sygnatura dostępu współdzielonego i autoryzacja

Token sygnatury dostępu współdzielonego można podpisać na jeden z dwóch sposobów:

- Za pomocą *klucza delegowania użytkownika* , który został utworzony przy użyciu poświadczeń Azure Active Directory (Azure AD). Sygnatura dostępu współdzielonego delegowania użytkownika jest podpisywana przy użyciu klucza delegowania użytkownika.

    Aby uzyskać klucz delegowania użytkownika i utworzyć sygnaturę dostępu współdzielonego, podmiot zabezpieczeń usługi Azure AD musi mieć przypisaną rolę platformy Azure, która zawiera akcję **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** . Aby uzyskać szczegółowe informacje na temat ról platformy Azure z uprawnieniami do uzyskiwania klucza delegowania użytkownika, zobacz temat [Tworzenie sygnatury dostępu współdzielonego użytkownika (API REST)](/rest/api/storageservices/create-user-delegation-sas).

- Z kluczem konta magazynu (klucz współużytkowany). Sygnatura dostępu współdzielonego usługi i sygnatura dostępu współdzielonego konta są podpisane przy użyciu klucza konta magazynu. Aby utworzyć sygnaturę dostępu współdzielonego, która jest podpisana przy użyciu klucza konta, aplikacja musi mieć dostęp do klucza konta.

Gdy żądanie zawiera token sygnatury dostępu współdzielonego, żądanie jest autoryzowane w zależności od tego, jak token sygnatury dostępu współdzielonego jest podpisany. Klucz dostępu lub poświadczenia używane do tworzenia tokenu SAS są również używane przez usługę Azure Storage w celu udzielania dostępu klientowi, który ma sygnaturę SAS.

W poniższej tabeli zestawiono, w jaki sposób każdy typ tokenu SAS jest autoryzowany, gdy jest zawarty w żądaniu do usługi Azure Storage:

| Typ sygnatury dostępu współdzielonego | Typ autoryzacji |
|-|-|
| Sygnatura dostępu współdzielonego użytkownika (tylko magazyn obiektów BLOB) | Azure AD |
| SAS usługi | Klucz wspólny |
| SAS konta | Klucz wspólny |

Firma Microsoft zaleca korzystanie z funkcji dostępu współdzielonego delegowania użytkowników w celu zapewnienia bezpieczeństwa.

### <a name="sas-token"></a>Token SAS

Token sygnatury dostępu współdzielonego jest ciągiem generowanym po stronie klienta, na przykład przy użyciu jednej z bibliotek klienta usługi Azure Storage. Token sygnatury dostępu współdzielonego nie jest śledzony przez usługę Azure Storage w jakikolwiek sposób. Po stronie klienta można utworzyć nieograniczoną liczbę tokenów SAS. Po utworzeniu sygnatury dostępu współdzielonego można je rozesłać do aplikacji klienckich, które wymagają dostępu do zasobów na koncie magazynu.

Gdy aplikacja kliencka zapewnia identyfikator URI sygnatury dostępu współdzielonego do usługi Azure Storage w ramach żądania, usługa sprawdza parametry i podpis sygnatury dostępu współdzielonego, aby sprawdzić, czy jest ona prawidłowa do autoryzowania żądania. Jeśli usługa sprawdza, czy podpis jest prawidłowy, żądanie jest autoryzowane. W przeciwnym razie żądanie zostanie odrzucone z kodem błędu 403 (dostęp zabroniony).

Oto przykład identyfikatora URI sygnatury dostępu współdzielonego usługi przedstawiający identyfikator URI zasobu i token sygnatury dostępu współdzielonego:

![Składniki identyfikatora URI sygnatury dostępu współdzielonego usługi](./media/storage-sas-overview/sas-storage-uri.png)

## <a name="when-to-use-a-shared-access-signature"></a>Kiedy używać sygnatury dostępu współdzielonego

Użyj sygnatury dostępu współdzielonego, jeśli chcesz zapewnić bezpieczny dostęp do zasobów na koncie magazynu do dowolnego klienta, który nie ma w inny sposób uprawnień do tych zasobów.

Typowym scenariuszem, w którym jest przydatny sygnatura dostępu współdzielonego, jest usługa, w której użytkownicy odczytują i zapisują własne dane na koncie magazynu. W scenariuszu, w którym dane użytkowników są przechowywane na koncie magazynu, zwykle występują dwa wzorce projektowe:

1. Klienci przekazują i pobierają dane za pośrednictwem usługi serwera proxy frontonu, która przeprowadza uwierzytelnianie. Ta usługa frontonu serwera proxy umożliwia sprawdzenie poprawności reguł firmy, ale w przypadku dużych ilości danych lub transakcji dużych ilości, tworzenie usługi, która może być skalowana w celu dopasowania do popytu, może być kosztowne lub trudne.

   ![Diagram scenariusza: usługa frontonu serwera proxy](./media/storage-sas-overview/sas-storage-fe-proxy-service.png)

1. Uproszczona usługa uwierzytelnia klienta zgodnie z potrzebami, a następnie generuje sygnaturę dostępu współdzielonego. Gdy aplikacja kliencka uzyska sygnaturę dostępu współdzielonego, może uzyskać dostęp do zasobów konta magazynu bezpośrednio z uprawnieniami zdefiniowanymi przez SYGNATURę dostępu współdzielonego oraz interwałem dozwolonym przez skojarzenie SAS. Sygnatura dostępu współdzielonego zmniejsza konieczność kierowania wszystkich danych przez usługę serwera proxy frontonu.

   ![Diagram scenariusza: usługa dostawcy SAS](./media/storage-sas-overview/sas-storage-provider-service.png)

Wiele usług rzeczywistych może korzystać z hybrydowych dwóch metod. Na przykład niektóre dane mogą być przetwarzane i weryfikowane za pośrednictwem serwera proxy frontonu, podczas gdy inne dane są zapisywane i/lub odczytywane bezpośrednio przy użyciu sygnatury dostępu współdzielonego.

Ponadto sygnatura dostępu współdzielonego jest wymagana, aby autoryzować dostęp do obiektu źródłowego w ramach operacji kopiowania w określonych scenariuszach:

- W przypadku kopiowania obiektu BLOB do innego obiektu BLOB znajdującego się na innym koncie magazynu należy użyć sygnatury dostępu współdzielonego, aby autoryzować dostęp do źródłowego obiektu BLOB. Opcjonalnie można użyć sygnatury dostępu współdzielonego, aby autoryzować również dostęp do docelowego obiektu BLOB.
- W przypadku kopiowania pliku do innego pliku, który znajduje się na innym koncie magazynu, należy użyć sygnatury dostępu współdzielonego, aby autoryzować dostęp do pliku źródłowego. Opcjonalnie można użyć sygnatury dostępu współdzielonego, aby autoryzować również dostęp do pliku docelowego.
- Podczas kopiowania obiektu BLOB do pliku lub pliku do obiektu BLOB należy użyć sygnatury dostępu współdzielonego, aby autoryzować dostęp do obiektu źródłowego, nawet jeśli obiekty źródłowe i docelowe znajdują się w ramach tego samego konta magazynu.

## <a name="best-practices-when-using-sas"></a>Najlepsze rozwiązania dotyczące korzystania z sygnatury dostępu współdzielonego

W przypadku używania sygnatur dostępu współdzielonego w aplikacjach należy znać dwa potencjalne zagrożenia:

- W przypadku przecieków sygnatury dostępu współdzielonego mogą one być używane przez każdego, kto je uzyska, co może spowodować naruszenie zabezpieczeń konta magazynu.
- Jeśli sygnatura dostępu współdzielonego dostarczona do aplikacji klienckiej wygaśnie i aplikacja nie będzie mogła pobrać nowego skojarzenia zabezpieczeń z usługi, funkcjonalność aplikacji może być utrudniona.

Poniższe zalecenia dotyczące korzystania z sygnatur dostępu współdzielonego mogą pomóc w ograniczeniu ryzyka:

- Do tworzenia lub rozpowszechniania sygnatury dostępu współdzielonego **Używaj protokołu HTTPS** . Jeśli sygnatura dostępu współdzielonego jest przenoszona przez protokół HTTP i przechwycona, osoba atakująca wykonująca atak typu man-in-the-middle może odczytać sygnaturę dostępu współdzielonego, a następnie użyć jej w taki sam sposób, jak w przypadku zamierzonego użytkownika.
- **Gdy jest to możliwe, Użyj skojarzenia zabezpieczeń delegowania użytkowników.** Sygnatura dostępu współdzielonego w ramach delegowania użytkowników zapewnia wyższy dostęp do SKOJARZEŃ zabezpieczeń usługi lub konta SAS. Sygnatura dostępu współdzielonego delegowania użytkownika jest zabezpieczona przy użyciu poświadczeń usługi Azure AD, dzięki czemu nie trzeba przechowywać klucza konta w kodzie.
- **Ma plan odwołania dla sygnatury dostępu współdzielonego.** Upewnij się, że jest przygotowana odpowiedź w przypadku naruszenia zabezpieczeń SAS.
- **Definiowanie zasad dostępu przechowywanego dla SYGNATURy usługi.** Przechowywane zasady dostępu umożliwiają Odwoływanie uprawnień dla skojarzeń zabezpieczeń usługi bez konieczności ponownego generowania kluczy konta magazynu. Ustaw czas wygaśnięcia na te bardzo daleko w przyszłość (lub nieskończonie) i upewnij się, że jest on regularnie aktualizowany, aby przenieść go dalej do przyszłości.
- **W przypadku sygnatury dostępu współdzielonego usługi ad hoc SAS lub SYGNATURy dostępu współdzielonego konta użyj niemal okresu ważności.** W ten sposób nawet w przypadku naruszenia zabezpieczeń SAS jest on ważny tylko przez krótki czas. To rozwiązanie jest szczególnie ważne, jeśli nie można odwołać się do przechowywanych zasad dostępu. Długoterminowe czasy wygaśnięcia również ograniczają ilość danych, które mogą być zapisywane w obiekcie blob przez ograniczenie czasu dostępnego do przekazania do niego.
- **Klienci mają automatycznie odnawiać sygnaturę dostępu współdzielonego w razie potrzeby.** Klienci powinni odnawiać sygnaturę dostępu współdzielonego przed upływem terminu wygaśnięcia, aby umożliwić ponowną próbę w przypadku niedostępności usługi dostarczającej sygnaturę dostępu współdzielonego. Jeśli sygnatura dostępu współdzielonego ma być używana w przypadku niewielkiej liczby natychmiastowych, krótkoterminowych operacji, które będą wykonywane w okresie wygaśnięcia, może to być niepotrzebne, ponieważ nie jest oczekiwane odnowienie sygnatury dostępu współdzielonego. Jeśli jednak masz klienta, który rutynowo przesyła żądania za pośrednictwem sygnatury dostępu współdzielonego, możliwość wygaśnięcia jest dostępna. Kluczową kwestią jest zrównoważenie potrzeb, aby sygnatura dostępu współdzielonego była krótkoterminowa (jak wcześniej zostało to określone) z koniecznością zapewnienia, że klient żąda odnowienia na początku (w celu uniknięcia zakłóceń spowodowanych wygaśnięciem sygnatury dostępu współdzielonego przed pomyślnym odnowieniem).
- **Należy zachować ostrożność w przypadku uruchomienia sygnatury dostępu współdzielonego.** Jeśli ustawisz godzinę rozpoczęcia dla sygnatury dostępu współdzielonego z bieżącym czasem, możesz zaobserwować błędy występujące sporadycznie przez pierwsze kilka minut, z powodu różnych maszyn, które mają nieco inne bieżące czasy (nazywane pochyleniem zegara). Ogólnie rzecz biorąc Ustaw czas rozpoczęcia na co najmniej 15 minut w przeszłości. Lub nie ustawiaj jej wcale, co spowoduje, że będzie ona natychmiast ważna we wszystkich przypadkach. To samo ogólnie dotyczy czasu wygaśnięcia i pamiętaj, że w dowolnym kierunku każdego żądania może być pochylone do 15 minut. W przypadku klientów korzystających z wersji REST starszej niż 2012-02-12 maksymalny czas trwania sygnatury dostępu współdzielonego, która nie odwołuje się do przechowywanych zasad, to 1 godzina, a wszystkie zasady, które określają dłuższy termin, nie powiodą się.
- **Należy zachować ostrożność przy użyciu formatu sygnatury dostępu współdzielonego.** Jeśli ustawisz godzinę rozpoczęcia i/lub wygaśnięcie dla sygnatury dostępu współdzielonego, dla niektórych narzędzi (na przykład narzędzie wiersza polecenia AzCopy) potrzebujesz formatu DateTime "+% Y-% m-% dT% H:%M:% SZ", w szczególności w ciągu sekund, aby można było korzystać z tokenu SAS.
- **Być specyficzne dla zasobu do uzyskania dostępu.** Najlepszym rozwiązaniem w zakresie zabezpieczeń jest zapewnienie użytkownikowi minimalnych wymaganych uprawnień. Jeśli użytkownik potrzebuje tylko dostępu do odczytu do pojedynczej jednostki, udziel im dostępu do odczytu dla tej pojedynczej jednostki, a dostęp do odczytu/zapisu/usuwania nie zostanie usunięty do wszystkich jednostek. Pozwala to również zmniejszyć szkody w przypadku naruszenia bezpieczeństwa SAS, ponieważ SAS ma mniej mocy w ręce osoby atakującej.
- **Należy pamiętać, że konto będzie rozliczane za użycie, w tym za pośrednictwem sygnatury dostępu współdzielonego.** W przypadku zapewnienia dostępu do zapisu do obiektu BLOB użytkownik może zdecydować się na przekazanie obiektu BLOB 200 GB. Jeśli przyznano im dostęp do odczytu, może to potrwać pobranie 10 razy, co naliczane jest 2 TB kosztów ruchu wychodzącego. Ponownie Udostępnij ograniczone uprawnienia, aby pomóc w ograniczeniu potencjalnych działań złośliwych użytkowników. Użyj krótkich sygnatur dostępu współdzielonego, aby zmniejszyć to zagrożenie (ale jest to świadome przesunięcia zegara w czasie zakończenia).
- **Sprawdzanie poprawności danych pisanych przy użyciu sygnatury dostępu współdzielonego.** Gdy aplikacja kliencka zapisuje dane na koncie magazynu, należy pamiętać, że mogą wystąpić problemy z tymi danymi. Jeśli aplikacja wymaga zweryfikowania lub autoryzacji danych przed gotowością do użycia, należy przeprowadzić tę weryfikację po zapisaniu danych i wcześniejszym wykorzystaniu jej przez aplikację. Ta metoda zapewnia również ochronę przed uszkodzonymi lub złośliwymi danymi, które są zapisywane na koncie, przez użytkownika, który prawidłowo nabył sygnaturę dostępu współdzielonego, lub przez użytkownika korzystającego z przecieków SAS.
- **Informacje o tym, kiedy nie należy używać sygnatury dostępu współdzielonego.** Czasami ryzyko związane z konkretną operacją w odniesieniu do konta magazynu ma większe korzyści wynikające z użycia sygnatury dostępu współdzielonego. W przypadku takich operacji Utwórz usługę warstwy środkowej, która zapisuje dane na koncie magazynu po przeprowadzeniu weryfikacji reguły biznesowej, uwierzytelniania i inspekcji. Ponadto czasami łatwiej jest zarządzać dostępem w inny sposób. Na przykład jeśli chcesz, aby wszystkie obiekty blob w kontenerze były dostępne publicznie, możesz utworzyć kontener jako publiczny, a nie dostarczając sygnatury dostępu współdzielonego do każdego klienta, aby uzyskać dostęp.
- **Użyj Azure Monitor i dzienników usługi Azure Storage do monitorowania aplikacji.** Za pomocą rejestrowania Azure Monitor i usługi Storage Analytics można sprawdzić, czy nie występują błędy autoryzacji z powodu przestoju w usłudze dostawcy SAS lub przypadkowego usunięcia przechowywanych zasad dostępu. Aby uzyskać więcej informacji, zobacz [metryki usługi Azure Storage w Azure monitor](monitor-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) i [analityka magazynu platformy Azure rejestrowania](storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="get-started-with-sas"></a>Wprowadzenie do SAS

Aby rozpocząć pracę z sygnaturami dostępu współdzielonego, zapoznaj się z następującymi artykułami dotyczącymi każdego typu SAS.

### <a name="user-delegation-sas"></a>Sygnatura dostępu współdzielonego użytkownika

- [Tworzenie sygnatury dostępu współdzielonego użytkownika dla kontenera lub obiektu BLOB przy użyciu programu PowerShell](../blobs/storage-blob-user-delegation-sas-create-powershell.md)
- [Tworzenie sygnatury dostępu współdzielonego użytkownika dla kontenera lub obiektu BLOB za pomocą interfejsu wiersza polecenia platformy Azure](../blobs/storage-blob-user-delegation-sas-create-cli.md)
- [Tworzenie sygnatury dostępu współdzielonego użytkownika dla kontenera lub obiektu BLOB przy użyciu platformy .NET](../blobs/storage-blob-user-delegation-sas-create-dotnet.md)

### <a name="service-sas"></a>SAS usługi

- [Tworzenie sygnatury dostępu współdzielonego usługi dla kontenera lub obiektu BLOB przy użyciu platformy .NET](../blobs/storage-blob-service-sas-create-dotnet.md)

### <a name="account-sas"></a>SAS konta

- [Tworzenie sygnatury dostępu współdzielonego konta przy użyciu platformy .NET](storage-account-sas-create-dotnet.md)

## <a name="next-steps"></a>Następne kroki

- [Delegowanie dostępu za pomocą sygnatury dostępu współdzielonego (API REST)](/rest/api/storageservices/delegate-access-with-shared-access-signature)
- [Tworzenie sygnatury dostępu współdzielonego (API REST) delegowania użytkownika](/rest/api/storageservices/create-user-delegation-sas)
- [Tworzenie sygnatury dostępu współdzielonego usługi (interfejs API REST)](/rest/api/storageservices/create-service-sas)
- [Tworzenie sygnatury dostępu współdzielonego konta (interfejs API REST)](/rest/api/storageservices/create-account-sas)
