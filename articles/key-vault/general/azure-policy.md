---
title: Integrowanie usługi Azure Key Vault z usługą Azure Policy
description: Dowiedz się, jak zintegrować Azure Key Vault z Azure Policy
author: msmbaldwin
ms.author: mbaldwin
ms.date: 03/31/2021
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: cddc7b931bf59412d4a7ec8e6b0eecfe148f3d5e
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749280"
---
# <a name="integrate-azure-key-vault-with-azure-policy"></a>Integrowanie usługi Azure Key Vault z usługą Azure Policy

[Azure Policy](../../governance/policy/index.yml) to narzędzie do zarządzania, które umożliwia użytkownikom inspekcję środowiska platformy Azure i zarządzanie nimi na dużą skalę. Azure Policy umożliwia zastosowanie zabezpieczeń zasobów platformy Azure w celu zapewnienia, że są one zgodne z przypisanymi regułami zasad. Umożliwia użytkownikom przeprowadzanie inspekcji, wymuszanie w czasie rzeczywistym i korygowanie środowiska platformy Azure. Wyniki inspekcji przeprowadzanych przez zasady będą dostępne dla użytkowników na pulpicie nawigacyjnym zgodności, gdzie będą mogli zobaczyć szczegółowe informacje o tym, które zasoby i składniki są zgodne, a które nie.  Aby uzyskać więcej informacji, zobacz [Overview of the Azure Policy service (Omówienie usługi Azure Policy service).](../../governance/policy/overview.md)

Przykładowe scenariusze użycia:

- Chcesz poprawić poziom zabezpieczeń firmy, wdrażając wymagania dotyczące minimalnych rozmiarów kluczy i maksymalnych okresów ważności certyfikatów w magazynach kluczy firmy, ale nie wiesz, które zespoły będą zgodne, a które nie.
- Obecnie nie masz rozwiązania do przeprowadzania inspekcji w całej organizacji lub przeprowadzasz ręczne inspekcje środowiska, prosząc poszczególne zespoły w organizacji o zgłoszenie ich zgodności. Szukasz sposobu automatyzacji tego zadania, przeprowadzania inspekcji w czasie rzeczywistym i zagwarantowania dokładności inspekcji.
- Chcesz wymusić firmowe zasady zabezpieczeń i zablokować osobom tworzenie certyfikatów z podpisem własnym, ale nie masz zautomatyzowanego sposobu blokowania ich tworzenia. 
- Chcesz złagonić wymagania dla zespołów testowych, ale chcesz zachować ścisłą kontrolę nad środowiskiem produkcyjnym. Potrzebujesz prostego zautomatyzowanego sposobu oddzielenia wymuszania zasobów.
- Chcesz mieć pewność, że możesz wycofać wymuszanie nowych zasad w przypadku problemu z witryną na żywo. Potrzebujesz rozwiązania jednym kliknięciem, aby wyłączyć wymuszanie zasad. 
- Korzystasz z rozwiązania innej firmy do inspekcji środowiska i chcesz skorzystać z wewnętrznej oferty firmy Microsoft.

## <a name="types-of-policy-effects-and-guidance"></a>Typy efektów zasad i wskazówki

**Inspekcja:** gdy efekt zasad zostanie ustawiony na inspekcję, zasady nie będą powodować żadnych zmian w środowisku. Alerty będą dotyczyć tylko składników, takich jak certyfikaty, które nie są zgodne z definicjami zasad w określonym zakresie, przez oznaczenie tych składników jako niezgodnych na pulpicie nawigacyjnym zgodności zasad. Inspekcja jest domyślna, jeśli nie wybrano żadnego efektu zasad.

**Odmów:** jeśli efekt zasad jest ustawiony na odmów, zasady będą blokować tworzenie nowych składników, takich jak certyfikaty, a także blokować nowe wersje istniejących składników, które nie są zgodne z definicją zasad. Nie ma to wpływu na istniejące niezgodne zasoby w magazynie kluczy. Funkcje "inspekcji" będą nadal działać.

## <a name="available-built-in-policy-definitions"></a>Dostępne definicje zasad "wbudowanych"

Key Vault utworzył zestaw zasad, których można użyć do zarządzania kluczami, certyfikatami i obiektami tajnymi. Te zasady są "wbudowane", co oznacza, że nie wymagają pisania żadnych niestandardowych danych JSON, aby je włączyć, i są dostępne w Azure Portal do przypisania. Nadal można dostosować niektóre parametry do potrzeb organizacji.

# <a name="certificate-policies"></a>[Zasady certyfikatów](#tab/certificates)

### <a name="certificates-should-have-the-specified-maximum-validity-period-preview"></a>Certyfikaty powinny mieć określony maksymalny okres ważności (wersja zapoznawcza)

Te zasady umożliwiają zarządzanie maksymalnym okresem ważności certyfikatów przechowywanych w magazynie kluczy. Dobrym rozwiązaniem w zakresie zabezpieczeń jest ograniczenie maksymalnego okresu ważności certyfikatów. Jeśli bezpieczeństwo klucza prywatnego certyfikatu zostanie naruszone bez wykrycia, użycie krótkotrwałych certyfikatów minimalizuje ramy czasowe dla bieżących szkód i zmniejsza wartość certyfikatu dla osoby atakującej.

### <a name="certificates-should-use-allowed-key-types-preview"></a>Certyfikaty powinny używać dozwolonych typów kluczy (wersja zapoznawcza)

Te zasady umożliwiają ograniczenie typu certyfikatów, które mogą być w magazynie kluczy. Możesz użyć tych zasad, aby upewnić się, że klucze prywatne certyfikatu mają kopię zapasową RSA, ECC lub moduł HSM. Z poniższej listy można wybrać dozwolone typy certyfikatów.

- RSA
- RSA — MODUŁ HSM
- Ecc
- ECC — moduł HSM

### <a name="certificates-should-have-the-specified-lifetime-action-triggers-preview"></a>Certyfikaty powinny mieć określone wyzwalacze akcji okresu istnienia (wersja zapoznawcza)

Te zasady umożliwiają zarządzanie akcją okresu istnienia określoną dla certyfikatów, które są w ciągu określonej liczby dni od ich wygaśnięcia lub osiągną określoną wartość procentową ich okresu użytkowania.

### <a name="certificates-should-be-issued-by-the-specified-integrated-certificate-authority-preview"></a>Certyfikaty powinny być wystawiane przez określony zintegrowany urząd certyfikacji (wersja zapoznawcza)

Jeśli używasz zintegrowanego urzędu certyfikacji usługi Key Vault (Digicert lub GlobalSign) i chcesz, aby użytkownicy używali jednego lub jednego z tych dostawców, możesz użyć tych zasad do inspekcji lub wymuszenia wyboru. Tych zasad można również użyć do inspekcji lub odmowy tworzenia certyfikatów z podpisem własnym w magazynie kluczy.

### <a name="certificates-should-be-issued-by-the-specified-non-integrated-certificate-authority-preview"></a>Certyfikaty powinny być wystawiane przez określony, nie zintegrowany urząd certyfikacji (wersja zapoznawcza)

Jeśli używasz wewnętrznego urzędu certyfikacji lub urzędu certyfikacji, który nie jest zintegrowany z magazynem kluczy i chcesz, aby użytkownicy korzystali z urzędu certyfikacji z listy, możesz użyć tych zasad do utworzenia listy dozwolonych urzędów certyfikacji według nazwy wystawcy. Tych zasad można również użyć do inspekcji lub odmowy tworzenia certyfikatów z podpisem własnym w magazynie kluczy.

### <a name="certificates-using-elliptic-curve-cryptography-should-have-allowed-curve-names-preview"></a>Certyfikaty korzystające z kryptografii krzywej eliptycznej powinny mieć dozwolone nazwy krzywych (wersja zapoznawcza)

Jeśli używasz kryptografii krzywej wielokropka lub certyfikatów ECC, możesz dostosować dozwoloną listę nazw krzywych z poniższej listy. Opcja domyślna zezwala na wszystkie poniższe nazwy krzywych.

- P-256
- P-256K
- P-384
- P-521

## <a name="certificates-using-rsa-cryptography-manage-minimum-key-size-for-rsa-certificates-preview"></a>Certyfikaty korzystające z kryptografii RSA Zarządzaj minimalnym rozmiarem klucza dla certyfikatów RSA (wersja zapoznawcza)

Jeśli używasz certyfikatów RSA, możesz wybrać minimalny rozmiar klucza, który musi mieć certyfikaty. Możesz wybrać jedną opcję z poniższej listy.

- 2048 bitów
- 3072 bity
- 4096 bitów

## <a name="manage-certificates-that-are-within-a-specified-number-of-days-of-expiration-preview"></a>Zarządzanie certyfikatami w ciągu określonej liczby dni wygaśnięcia (wersja zapoznawcza)

W usłudze może wystąpić błąd, jeśli certyfikat, który nie jest odpowiednio monitorowany, nie zostanie obrócony przed jego wygaśnięciem. Te zasady mają kluczowe znaczenie dla upewnień się, że certyfikaty przechowywane w magazynie kluczy są monitorowane. Zaleca się stosowanie tych zasad wielokrotnie z różnymi progami wygasania, na przykład dla progów 180, 90, 60 i 30 dni. Te zasady mogą służyć do monitorowania i ujednania wygasania certyfikatów w organizacji.

# <a name="key-policies"></a>[Kluczowe zasady](#tab/keys)

### <a name="keys-should-not-be-active-for-longer-than-the-specified-number-of-days-preview"></a>Klucze nie powinny być aktywne dłużej niż określona liczba dni (wersja zapoznawcza)

Jeśli chcesz się upewnić, że klucze nie były aktywne dłużej niż przez określoną liczbę dni, możesz użyć tych zasad do inspekcji czasu aktywności klucza.

**Jeśli klucz ma ustawioną datę aktywacji**, te zasady obliczą  liczbę dni, które upłynęły od daty aktywacji klucza do bieżącej daty. Jeśli liczba dni przekroczy ustawiony próg, klucz zostanie oznaczony jako niezgodny z zasadami.

**Jeśli klucz nie ma ustawionej daty** aktywacji, te zasady obliczą liczbę  dni, które upłynęły od daty utworzenia klucza do bieżącej daty. Jeśli liczba dni przekroczy ustawiony próg, klucz zostanie oznaczony jako niezgodny z zasadami.

### <a name="keys-should-be-the-specified-cryptographic-type-rsa-or-ec-preview"></a>Klucze powinny być określonym typem kryptograficznym RSA lub EC (wersja zapoznawcza)

Te zasady umożliwiają ograniczenie typu kluczy, które mogą być w magazynie kluczy. Możesz użyć tych zasad, aby upewnić się, że klucze mają kopię zapasową RSA, ECC lub moduł HSM. Z poniższej listy można wybrać dozwolone typy certyfikatów.

- RSA
- RSA — MODUŁ HSM
- Ecc
- ECC — moduł HSM

### <a name="keys-using-elliptic-curve-cryptography-should-have-the-specified-curve-names-preview"></a>Klucze korzystające z kryptografii krzywej eliptycznej powinny mieć określone nazwy krzywych (wersja zapoznawcza)

Jeśli używasz kryptografii krzywej wielokropka lub kluczy ECC, możesz dostosować dozwoloną listę nazw krzywych z poniższej listy. Opcja domyślna zezwala na wszystkie poniższe nazwy krzywych.

- P-256
- P-256K
- P-384
- P-521

### <a name="keys-should-have-expirations-dates-set-preview"></a>Klucze powinny mieć ustawione daty wygaśnięcia (wersja zapoznawcza)

Te zasady przeprowadza inspekcję wszystkich kluczy w magazynach kluczy i flaguje klucze, które nie mają daty wygaśnięcia ustawionej jako niezgodne. Można również użyć tych zasad, aby zablokować tworzenie kluczy, które nie mają ustawionej daty wygaśnięcia.

### <a name="keys-should-have-more-than-the-specified-number-of-days-before-expiration-preview"></a>Klucze powinny mieć więcej niż określoną liczbę dni przed wygaśnięciem (wersja zapoznawcza)

Jeśli klucz jest zbyt blisko wygaśnięcia, organizacyjne opóźnienie rotacji klucza może spowodować jego przeostwo. Klucze powinny być obracane o określonej liczbie dni przed wygaśnięciem, aby zapewnić wystarczającą ilość czasu na reakcję na awarię. Te zasady umożliwiają inspekcję kluczy, które są zbyt zbliżone do daty wygaśnięcia i umożliwiają ustawienie tego progu w dniach. Można również użyć tych zasad, aby zapobiec tworzeniu nowych kluczy, które są zbyt zbliżone do daty wygaśnięcia.

### <a name="keys-should-be-backed-by-a-hardware-security-module-preview"></a>Klucze powinny być wsadowe przez sprzętowy moduł zabezpieczeń (wersja zapoznawcza)

Moduł HSM to sprzętowy moduł zabezpieczeń, który przechowuje klucze. Moduł HSM zapewnia fizyczną warstwę ochrony kluczy kryptograficznych. Klucz kryptograficzny nie może opuścić fizycznego modułu HSM, który zapewnia wyższy poziom zabezpieczeń niż klucz oprogramowania. Niektóre organizacje mają wymagania dotyczące zgodności, które nakazuje korzystanie z kluczy HSM. Użyj tych zasad, aby inspekcję wszystkich kluczy przechowywanych w magazynie kluczy, dla których nie jest kopii zapasowej modułu HSM. Można również użyć tych zasad, aby zablokować tworzenie nowych kluczy, które nie są kopii zapasowej modułu HSM. Te zasady będą stosowane do wszystkich typów kluczy, RSA i ECC.

### <a name="keys-using-rsa-cryptography-should-have-a-specified-minimum-key-size-preview"></a>Klucze korzystające z kryptografii RSA powinny mieć określony minimalny rozmiar klucza (wersja zapoznawcza)

Używanie kluczy RSA o mniejszych rozmiarach kluczy nie jest bezpiecznym rozwiązaniem w zakresie projektowania. Użytkownik może podlegać standardom inspekcji i certyfikacji, które nakłonią do użycia minimalnego rozmiaru klucza. Poniższe zasady umożliwiają ustawienie minimalnego wymaganego rozmiaru klucza w magazynie kluczy. Możesz wykonać inspekcję kluczy, które nie spełniają tego wymagania minimalnego. Te zasady mogą również służyć do blokowania tworzenia nowych kluczy, które nie spełniają wymagań minimalnego rozmiaru klucza.

### <a name="keys-should-have-the-specified-maximum-validity-period-preview"></a>Klucze powinny mieć określony maksymalny okres ważności (wersja zapoznawcza)

Zarządzaj wymaganiami organizacji w zakresie zgodności, określając maksymalny czas (w dniach), przez jaki klucz może być ważny w magazynie kluczy. Klucze, które są ważne dłużej niż ustawiony próg, zostaną oznaczone jako niezgodne. Można również użyć tych zasad, aby zablokować tworzenie nowych kluczy, których data wygaśnięcia jest ustawiona dłużej niż określony maksymalny okres ważności.

# <a name="secret-policies"></a>[Zasady dotyczące tajnych danych](#tab/secrets)

### <a name="secrets-should-not-be-active-for-longer-than-the-specified-number-of-days-preview"></a>Wpisy tajne nie powinny być aktywne dłużej niż określona liczba dni (wersja zapoznawcza)

Jeśli chcesz się upewnić, że wpisy tajne nie były aktywne dłużej niż określona liczba dni, możesz użyć tych zasad, aby sprawdzić, jak długo wpis tajny był aktywny.

**Jeśli klucz tajny ma ustawioną** datę aktywacji, te zasady obliczą liczbę dni, które upłynęły od daty aktywacji klucza tajnego do bieżącej daty.  Jeśli liczba dni przekroczy ustawiony próg, klucz tajny zostanie oznaczony jako niezgodny z zasadami.

**Jeśli klucz tajny nie ma** ustawionej daty aktywacji, te zasady obliczą  liczbę dni, które upłynęły od daty utworzenia klucza tajnego do bieżącej daty. Jeśli liczba dni przekroczy ustawiony próg, klucz tajny zostanie oznaczony jako niezgodny z zasadami.

### <a name="secrets-should-have-content-type-set-preview"></a>Wpisy tajne powinny mieć ustawiony typ zawartości (wersja zapoznawcza)

Dowolny zwykły tekst lub zakodowany plik może być przechowywany jako klucz tajny magazynu kluczy. Jednak organizacja może chcieć ustawić różne zasady rotacji i ograniczenia dotyczące haseł, ciągów połączeń lub certyfikatów przechowywanych jako klucze. Tag typu zawartości może pomóc użytkownikowi zobaczyć, co jest przechowywane w obiekcie tajnym bez odczytywania wartości tego wpisu tajnego. Tych zasad można użyć do inspekcji wpisów tajnych, które nie mają ustawionego tagu typu zawartości. Możesz również użyć tych zasad, aby zapobiec utworzeniu nowych wpisów tajnych, jeśli nie mają ustawionego tagu typu zawartości.

### <a name="secrets-should-have-expiration-date-set-preview"></a>Wpisy tajne powinny mieć ustawioną datę wygaśnięcia (wersja zapoznawcza)

Te zasady przeprowadza inspekcję wszystkich wpisów tajnych w magazynie kluczy i flaguje wpisy tajne, które nie mają daty wygaśnięcia ustawionej jako niezgodne. Możesz również użyć tych zasad, aby zablokować tworzenie wpisów tajnych, które nie mają ustawionej daty wygaśnięcia.

### <a name="secrets-should-have-more-than-the-specified-number-of-days-before-expiration-preview"></a>Wpisy tajne powinny mieć więcej niż określoną liczbę dni przed wygaśnięciem (wersja zapoznawcza)

Jeśli klucz tajny jest zbyt blisko wygaśnięcia, opóźnienie organizacyjne w celu rotacji tego tajnego może spowodować jego wygaśnięcie. Wpisy tajne powinny być obracane o określonej liczbie dni przed wygaśnięciem, aby zapewnić wystarczającą ilość czasu na zareagowanie na awarię. Te zasady umożliwiają inspekcję wpisów tajnych, które są zbyt zbliżone do daty wygaśnięcia, i umożliwiają ustawienie tego progu w dniach. Możesz również użyć tych zasad, aby zapobiec tworzeniu nowych wpisów tajnych, które są zbyt zbliżone do daty wygaśnięcia.

### <a name="secrets-should-have-the-specified-maximum-validity-period-preview"></a>Wpisy tajne powinny mieć określony maksymalny okres ważności (wersja zapoznawcza)

Zarządzaj wymaganiami organizacji w zakresie zgodności, określając maksymalny czas (w dniach), przez jaki klucz tajny może być ważny w magazynie kluczy. Wpisy tajne, które są ważne dłużej niż ustawiony próg, zostaną oznaczone jako niezgodne. Można również użyć tych zasad, aby zablokować tworzenie nowych wpisów tajnych, których data wygaśnięcia jest ustawiona dłużej niż określony maksymalny okres ważności.

---

## <a name="example-scenario"></a>Przykładowy scenariusz

Zarządzasz magazynem kluczy używanym przez wiele zespołów, który zawiera 100 certyfikatów, i chcesz się upewnić, że żaden z certyfikatów w magazynie kluczy nie jest ważny dłużej niż 2 lata.

1. Aby przypisać  certyfikaty, należy określić zasady maksymalnego okresu ważności, określić, że maksymalny okres ważności certyfikatu wynosi 24 miesiące, a następnie ustawić efekt zasad na "inspekcję". 
1. Raport zgodności można wyświetlić na stronie Azure Portal [i](#view-compliance-results)wykryć, że 20 certyfikatów jest niezgodnych i ważnych przez > 2 lata, a pozostałe certyfikaty są zgodne. 
1. Należy skontaktować się z właścicielami tych certyfikatów i przekazać nowe wymaganie dotyczące zabezpieczeń, które oznacza, że certyfikaty nie mogą być ważne dłużej niż 2 lata. Niektóre zespoły odpowiadają, a 15 certyfikatów zostało odnowionych z maksymalnym okresem ważności 2 lat lub krótszym. Inne zespoły nie odpowiadają i nadal masz 5 niezgodnych certyfikatów w magazynie kluczy.
1. Możesz zmienić efekt zasad przypisanych do "odmów". 5 niezgodnych certyfikatów nie zostanie odwołanych i nadal będą działać. Nie można ich jednak odnowić z okresem ważności dłuższym niż 2 lata. 

## <a name="enabling-and-managing-a-key-vault-policy-through-the-azure-portal"></a>Włączanie zasad zarządzania Key Vault za pośrednictwem Azure Portal

### <a name="select-a-policy-definition"></a>Wybieranie definicji zasad

1. Zaloguj się w witrynie Azure Portal. 
1. Wyszukaj pozycję "Zasady" na pasku wyszukiwania i wybierz **pozycję Zasady.**

    ![Zrzut ekranu przedstawiający pasek wyszukiwania.](../media/policy-img1.png)

1. W oknie Zasady wybierz pozycję **Definicje**.

    ![Zrzut ekranu przedstawiający opcję Definicje.](../media/policy-img2.png)

1. W filtrze kategoria usuń zaznaczenie opcji **Zaznacz wszystko** i wybierz **pozycję Key Vault**. 

    ![Zrzut ekranu przedstawiający filtr kategoria i wybraną Key Vault kategorii.](../media/policy-img3.png)

1. Teraz powinno być możliwe wyświetlanie wszystkich zasad dostępnych w publicznej wersji zapoznawczej dla Azure Key Vault. Upewnij się, że przeczytasz i rozumiesz sekcję wskazówki dotyczące zasad powyżej i wybierz zasady, które chcesz przypisać do zakresu.  

    ![Zrzut ekranu przedstawiający zasady dostępne w publicznej wersji zapoznawczej.](../media/policy-img4.png)

### <a name="assign-a-policy-to-a-scope"></a>Przypisywanie zasad do zakresu 

1. Wybierz zasady, które chcesz zastosować. W tym przykładzie są wyświetlane zasady Zarządzaj **okresem** ważności certyfikatu. Kliknij przycisk Przypisz w lewym górnym rogu.

    ![Zrzut ekranu przedstawiający zasady Zarządzaj okresem ważności certyfikatu.](../media/policy-img5.png)
  
1. Wybierz subskrypcję, w której chcesz zastosować zasady. Zakres można ograniczyć tylko do jednej grupy zasobów w ramach subskrypcji. Jeśli chcesz zastosować zasady do całej subskrypcji i wykluczyć niektóre grupy zasobów, możesz również skonfigurować listę wykluczeń. Ustaw selektor wymuszania zasad na wartość **Włączone,** jeśli chcesz, aby  zasady (inspekcja lub odmowa) wystąpiły, lub wyłączone, aby wyłączyć efekt (inspekcja lub odmowa). 

    ![Zrzut ekranu przedstawiający miejsce, w którym można ograniczyć zakres do tylko jednej grupy zasobów w ramach subskrypcji.](../media/policy-img6.png)

1. Kliknij kartę Parametry w górnej części ekranu, aby określić maksymalny okres ważności w chcień. Wybierz **pozycję** audit or **deny** (przejmij lub odmów), aby uzyskać efekt zasad zgodnie ze wskazówkami w powyższych sekcjach. Następnie wybierz przycisk Przejrzyj i utwórz. 

    ![Zrzut ekranu przedstawiający kartę Parametry, na której można określić maksymalny okres ważności w chętnych miesiącach.](../media/policy-img7.png)

### <a name="view-compliance-results"></a>Wyświetlanie wyników zgodności

1. Wstecz do bloku Zasady i wybierz kartę Zgodność. Kliknij przypisanie zasad, dla których chcesz wyświetlić wyniki zgodności.

    ![Zrzut ekranu przedstawiający kartę Zgodność, na której można wybrać przypisanie zasad, dla którego chcesz wyświetlić wyniki zgodności.](../media/policy-img8.png)

1. Na tej stronie można filtrować wyniki według zgodnych lub niezgodnych magazynów. W tym miejscu można wyświetlić listę niezgodnych magazynów kluczy w zakresie przypisania zasad. Magazyn jest uznawany za niezgodny, jeśli którykolwiek ze składników (certyfikatów) w magazynie jest niezgodny. Możesz wybrać pojedynczy magazyn, aby wyświetlić poszczególne niezgodne składniki (certyfikaty). 


    ![Zrzut ekranu przedstawiający listę niezgodnych magazynów kluczy w zakresie przypisania zasad.](../media/policy-img9.png)

1. Wyświetlanie nazw składników w magazynie, które są niezgodne


    ![Zrzut ekranu przedstawiający miejsce, w którym można wyświetlić nazwy niezgodnych składników w magazynie.](../media/policy-img10.png)

1. Jeśli chcesz sprawdzić, czy użytkownikom nie odmawia się możliwości tworzenia zasobów w magazynie kluczy, możesz kliknąć kartę Zdarzenia składników **(wersja zapoznawcza),** aby wyświetlić podsumowanie operacji odmowy certyfikatów z żądaniem i znacznikami czasu żądań. 


    ![Omówienie sposobu działania Azure Key Vault](../media/policy-img11.png)

## <a name="feature-limitations"></a>Ograniczenia funkcji

Przypisanie zasad z efektem "odmów" może potrwać do 30 minut (średni przypadek) i 1 godzinę (najgorszy przypadek), aby rozpocząć odmawianie tworzenia niezgodnych zasobów. Ocena zasad istniejących składników w magazynie może potrwać do 1 godziny (średni przypadek) i 2 godziny (najgorszy przypadek), zanim wyniki zgodności będą dostępne w interfejsie użytkownika portalu. Jeśli wyniki zgodności są wyświetlane jako "Nie uruchomio", może to być spowodowane następującymi przyczynami:
- Zasady nie zostały jeszcze ukończone. W scenariuszu najgorszego przypadku początkowe opóźnienie oceny może potrwać do 2 godzin. 
- W zakresie przypisania zasad nie ma żadnych magazynów kluczy.
- W zakresie przypisania zasad nie ma żadnych magazynów kluczy z certyfikatami.

> [!NOTE]
> Azure Policy dostawcy [usługi Resouce,](../../governance/policy/concepts/definition-structure.md#resource-provider-modes)takie jak Azure Key Vault, zawierają informacje o zgodności na [stronie Zgodność](../../governance/policy/how-to/get-compliance-data.md#component-compliance) składników.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [usłudze Azure Policy service](../../governance/policy/overview.md)
- Zobacz Key Vault przykłady: [Key Vault wbudowane definicje zasad](../../governance/policy/samples/built-in-policies.md#key-vault)
