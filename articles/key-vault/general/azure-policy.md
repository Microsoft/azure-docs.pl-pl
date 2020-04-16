---
title: Integracja usługi Azure Key Vault z zasadami platformy Azure
description: Dowiedz się, jak zintegrować usługę Azure Key Vault z zasadami platformy Azure
author: msmbaldwin
ms.author: mbaldwin
ms.date: 01/28/2020
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.openlocfilehash: 6b54dc27f8a3e88dedb0552b1ac7fb675d75121a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424594"
---
# <a name="integrate-azure-key-vault-with-azure-policy"></a>Integracja usługi Azure Key Vault z zasadami platformy Azure

[Usługa Azure Policy](../../governance/policy/index.yml) to narzędzie ładu, które umożliwia użytkownikom inspekcję środowiska platformy Azure i zarządzanie nim na dużą skalę. Usługa Azure Policy umożliwia umieszczanie barierek na zasobach platformy Azure, aby upewnić się, że są one zgodne z przypisanymi regułami zasad. Umożliwia użytkownikom wykonywanie inspekcji, wymuszania w czasie rzeczywistym i korygowania środowiska platformy Azure. Wyniki audytów przeprowadzanych przez zasady będą dostępne dla użytkowników na pulpicie nawigacyjnym zgodności, gdzie będą mogli zobaczyć, które zasoby i składniki są zgodne, a które nie.  Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Policy](../../governance/policy/overview.md).

Przykładowe scenariusze użycia:

- Chcesz poprawić poziom zabezpieczeń firmy, implementując wymagania dotyczące minimalnych rozmiarów kluczy i maksymalnych okresów ważności certyfikatów w magazynach kluczy firmy, ale nie wiesz, które zespoły będą zgodne, a które nie. 
- Obecnie nie masz rozwiązania do przeprowadzania inspekcji w całej organizacji lub przeprowadzasz ręczne audyty środowiska, prosząc poszczególne zespoły w organizacji o zgłoszenie ich zgodności. Szukasz sposobu, aby zautomatyzować to zadanie, przeprowadzić audyty w czasie rzeczywistym i zagwarantować dokładność inspekcji.
- Chcesz wymusić zasady zabezpieczeń firmy i uniemożliwić osobom tworzenie certyfikatów z podpisem własnym, ale nie masz zautomatyzowanego sposobu blokowania ich tworzenia. 
- Chcesz rozluźnić niektóre wymagania dla zespołów testowych, ale chcesz zachować ścisłą kontrolę nad środowiskiem produkcyjnym. Potrzebujesz prostego, zautomatyzowanego sposobu na oddzielenie egzekwowania zasobów. 
- Chcesz mieć pewność, że można wycofać wymuszanie nowych zasad w przypadku problemu z witryną na żywo. Aby wyłączyć wymuszanie zasad, potrzebujesz rozwiązania jednym kliknięciem. 
- Polegasz na rozwiązaniu innej firmy do inspekcji środowiska i chcesz użyć wewnętrznej oferty firmy Microsoft. 

## <a name="types-of-policy-effects-and-guidance"></a>Rodzaje efektów politycznych i wytycznych

**Inspekcja**: Gdy efekt zasad jest ustawiony na inspekcję, zasady nie spowoduje żadnych zmian w środowisku. Będzie tylko ostrzegać do składników, takich jak certyfikaty, które nie są zgodne z definicjami zasad w określonym zakresie, oznaczając te składniki jako niezgodne w pulpicie nawigacyjnym zgodności zasad. Inspekcja jest domyślna, jeśli nie wybrano żadnego efektu zasad. 

**Odmów:** Gdy efekt zasad jest ustawiony na odmowę, zasady zablokują tworzenie nowych składników, takich jak certyfikaty, a także zablokują nowe wersje istniejących składników, które nie są zgodne z definicją zasad. Nie ma to wpływu na istniejące niezgodne zasoby w magazynie kluczy. Możliwości "audytu" będą nadal działać.

## <a name="available-built-in-policy-definitions"></a>Dostępne definicje zasad "Wbudowane"

Usługa Key Vault utworzyła zestaw zasad, które można przypisać do typowych scenariuszy do zarządzania certyfikatami. Te zasady są "wbudowane", co oznacza, że nie wymagają pisania żadnych niestandardowych JSON, aby je włączyć i są one dostępne w witrynie Azure portal do przypisania. Nadal można dostosować niektóre parametry do potrzeb organizacji. 

Osiem zasad podglądu są następujące.

### <a name="manage-certificate-validity-period-preview"></a>Zarządzanie okresem ważności certyfikatu (wersja zapoznawcza)

Ta zasada umożliwia zarządzanie maksymalnym okresem ważności certyfikatów przechowywanych w magazynie kluczy. Jest dobrą praktyką bezpieczeństwa, aby ograniczyć maksymalny okres ważności certyfikatów. Jeśli klucz prywatny certyfikatu został naruszony bez wykrycia, użycie certyfikatów krótkotrwałych minimalizuje ramy czasowe bieżących uszkodzeń i zmniejsza wartość certyfikatu dla osoby atakującej. 

### <a name="manage-allowed-certificate-key-types-preview"></a>Zarządzanie dozwolonymi typami kluczy certyfikatu (wersja zapoznawcza)
Ta zasada umożliwia ograniczenie typu certyfikatów, które mogą znajdować się w magazynie kluczy. Za pomocą tej zasady można się upewnić, że klucze prywatne certyfikatów są RSA, ECC lub są wspierane przez moduł HSM. Można wybrać jedną z poniższych list typów certyfikatów, które są dozwolone.
- RSA
- RSA - HSM
- Ecc 
- ECC - HSM 

### <a name="manage-certificate-lifetime-action-triggers-preview"></a>Zarządzanie wyzwalaczami akcji okresu istnienia certyfikatu (wersja zapoznawcza)

Ta zasada umożliwia zarządzanie akcją dożywotnią określoną dla certyfikatów, które znajdują się w ciągu określonej liczby dni od ich wygaśnięcia lub osiągnęły określony procent ich okresu użytkowania. 

### <a name="manage-certificates-issued-by-an-integrated-ca-preview"></a>Zarządzanie certyfikatami wystawionymi przez zintegrowany urząd certyfikacji (wersja zapoznawcza)

Jeśli używasz zintegrowanego urzędu certyfikacji usługi Key Vault (Digicert lub GlobalSign) i chcesz, aby użytkownicy używali jednego lub jednego z tych dostawców, możesz użyć tej zasady do inspekcji lub wymuszenia wyboru. Ta zasada może być również używana do inspekcji lub odmowy tworzenia certyfikatów z podpisem własnym w magazynie kluczy. 

### <a name="manage-certificates-issued-by-an-integrated-ca-preview"></a>Zarządzanie certyfikatami wystawionymi przez zintegrowany urząd certyfikacji (wersja zapoznawcza)

Jeśli używany jest wewnętrzny urząd certyfikacji lub urząd certyfikacji nieskomunikowana z magazynem kluczy i użytkownicy mają używać urzędu certyfikacji z listy, którą udostępniasz, można użyć tej zasady do utworzenia listy dozwolonych urzędów certyfikacji według nazwy wystawcy. Ta zasada może być również używana do inspekcji lub odmowy tworzenia certyfikatów z podpisem własnym w magazynie kluczy. 

### <a name="manage-allowed-curve-names-for-elliptic-curve-cryptography-certificates-preview"></a>Zarządzanie dozwolonymi nazwami krzywych dla certyfikatów kryptografii krzywej eliptycznej (wersja zapoznawcza)
Jeśli używasz kryptografii krzywej eliptycznej lub certyfikatów ECC, możesz dostosować dozwoloną listę nazw krzywych z poniższej listy. Opcja domyślna zezwala na wszystkie następujące nazwy krzywych. 
- P-256
- P-256K
- P-384
- P-521

### <a name="manage-minimum-key-size-for-rsa-certificates-preview"></a>Zarządzanie minimalnym rozmiarem klucza dla certyfikatów RSA (wersja zapoznawcza)
Jeśli używasz certyfikatów RSA, możesz wybrać minimalny rozmiar klucza, który muszą mieć certyfikaty. Możesz wybrać jedną opcję z poniższej listy. 
- 2048 bit
- 3072 bit
- 4096 bitów

### <a name="manage-certificates-that-are-within-a-specified-number-of-days-of-expiration-preview"></a>Zarządzanie certyfikatami, które znajdują się w ciągu określonej liczby dni od wygaśnięcia (wersja zapoznawcza)
Usługa może wystąpić awaria, jeśli certyfikat, który nie jest odpowiednio monitorowany nie jest obracany przed jego wygaśnięciem. Ta zasada ma kluczowe znaczenie dla upewnienia się, że certyfikaty przechowywane w magazynie kluczy są monitorowane. Zaleca się stosowanie tej zasady wiele razy z różnymi progami wygaśnięcia, na przykład przy progach 180, 90, 60 i 30-dniowych. Ta zasada może służyć do monitorowania i klasyfikowania wygaśnięcia certyfikatu w organizacji. 

## <a name="example-scenario"></a>Przykładowy scenariusz

Zarządzasz magazynem kluczy używanym przez wiele zespołów, który zawiera 100 certyfikatów, i chcesz się upewnić, że żaden z certyfikatów w magazynie kluczy nie jest ważny dłużej niż 2 lata.

1. Należy przypisać zasady [okresu ważności certyfikatu,](#manage-certificate-validity-period-preview) określić, że maksymalny okres ważności certyfikatu wynosi 24 miesiące, i ustawić efekt zasad na "inspekcja". 
1. Możesz wyświetlić [raport zgodności w witrynie Azure portal](#view-compliance-results)i odkryć, że 20 certyfikatów są niezgodne i ważne przez > 2 lat, a pozostałe certyfikaty są zgodne. 
1. Kontakt z właścicielami tych certyfikatów i poinformować nowe wymagania zabezpieczeń, że certyfikaty nie mogą być ważne przez okres dłuższy niż 2 lata. Niektóre zespoły reagują, a 15 certyfikatów zostało odnowionych z maksymalnym okresem ważności wynoszącym co najmniej 2 lata. Inne zespoły nie odpowiadają, a nadal masz 5 niezgodnych certyfikatów w magazynie kluczy.
1. Możesz zmienić efekt zasad przypisanych do "odmów". 5 niezgodnych certyfikatów nie są odwołane i nadal działają. Nie można ich jednak odnowić z okresem ważności dłuższym niż 2 lata. 

## <a name="enabling-and-managing-a-key-vault-policy-through-the-azure-portal"></a>Włączanie zasad usługi Key Vault i zarządzanie nimi za pośrednictwem witryny Azure portal

### <a name="select-a-policy-definition"></a>Wybieranie definicji zasad

1. Zaloguj się w witrynie Azure Portal. 
1. Wyszukaj "Zasady" na pasku wyszukiwania i wybierz **zasady**.

    ![Omówienie działania usługi Azure Key Vault](../media/policy-img1.png)

1. W oknie Zasady wybierz pozycję **Definicje**.

    ![Omówienie działania usługi Azure Key Vault](../media/policy-img2.png)

1. W filtrze kategorii usuń zaznaczenie **zaznaczania wszystkich** i wybierz pozycję **Przechowalnia kluczy**. 

    ![Omówienie działania usługi Azure Key Vault](../media/policy-img3.png)

1. Teraz powinieneś być w stanie zobaczyć wszystkie zasady dostępne dla public preview dla usługi Azure Key Vault. Upewnij się, że przeczytałeś i zrozumiałeś sekcję wskazówek dotyczących zasad powyżej i wybierz zasadę, którą chcesz przypisać do zakresu.  

    ![Omówienie działania usługi Azure Key Vault](../media/policy-img4.png)

### <a name="assign-a-policy-to-a-scope"></a>Przypisywanie zasad do zakresu 

1. Wybierz zasady, które chcesz zastosować, w tym przykładzie pokazano zasady **Zarządzaj okresem ważności certyfikatu.** Kliknij przycisk Przypisz w lewym górnym rogu.

    ![Omówienie działania usługi Azure Key Vault](../media/policy-img5.png)
  
1. Wybierz subskrypcję, w której ma zostać zastosowana zasada. Można ograniczyć zakres tylko do jednej grupy zasobów w ramach subskrypcji. Jeśli chcesz zastosować zasady do całej subskrypcji i wykluczyć niektóre grupy zasobów, możesz również skonfigurować listę wykluczeń. Ustaw selektor wymuszania zasad na **Włączone,** jeśli chcesz, aby efekt zasad (inspekcji lub odmowy) wystąpił lub **wyłączone,** aby wyłączyć efekt (inspekcja lub odmowa). 

    ![Omówienie działania usługi Azure Key Vault](../media/policy-img6.png)

1. Kliknij kartę parametry w górnej części ekranu, aby określić maksymalny okres ważności w miesiącach, które chcesz. Wybierz **inspekcję** lub **odmów** dla wpływu zasad, postęp zgodnie ze wskazówkami w sekcjach powyżej. Następnie wybierz przycisk Recenzja + Utwórz. 

    ![Omówienie działania usługi Azure Key Vault](../media/policy-img7.png)

### <a name="view-compliance-results"></a>Wyświetlanie wyników zgodności

1. Wróć do bloku Zasady i wybierz kartę zgodności.

    ![Omówienie działania usługi Azure Key Vault](../media/policy-img8.png)

1. Na tej stronie można filtrować wyniki według zgodnych lub niezgodnych magazynów. W tym miejscu można wyświetlić listę niezgodnych magazynów kluczy w zakresie przypisania zasad. Magazyn jest uważany za niezgodny, jeśli którykolwiek ze składników (certyfikatów) w przechowalni są niezgodne. Można wybrać indywidualną przechowalnię, aby wyświetlić poszczególne niezgodne składniki (certyfikaty). 


    ![Omówienie działania usługi Azure Key Vault](../media/policy-img9.png)

1. Wyświetlanie nazwy komponentów w przechowalni, które są niezgodne


    ![Omówienie działania usługi Azure Key Vault](../media/policy-img10.png)

1. Jeśli chcesz sprawdzić, czy użytkownikom odmawia się możliwości tworzenia zasobów w magazynie kluczy, możesz kliknąć kartę **Zdarzenia składników (podgląd),** aby wyświetlić podsumowanie operacji odmowy certyfikatu z żądaniem i sygnaturami czasowymi żądań. 


    ![Omówienie działania usługi Azure Key Vault](../media/policy-img11.png)

## <a name="feature-limitations"></a>Ograniczenia funkcji

Przypisanie zasad z efektem "odmowa" może potrwać do 30 minut (przeciętny przypadek) i 1 godzinę (najgorszy przypadek), aby rozpocząć odmawianie tworzenia niezgodnych zasobów. Ocena zasad istniejących składników w przechowalni może potrwać do 1 godziny (średni przypadek) i 2 godziny (najgorszy przypadek), zanim wyniki zgodności będą widoczne w interfejsie użytkownika portalu. Jeśli wyniki zgodności są wyświetlane jako "Nie rozpoczęte", może to być spowodowane następującymi przyczynami:
- Wycena polisy nie została jeszcze zakończona. Opóźnienie oceny początkowej może potrwać do 2 godzin w najgorszym przypadku. 
- W zakresie przypisania zasad nie ma magazynów kluczy.
- Nie ma żadnych magazynów kluczy z certyfikatami w zakresie przypisania zasad. 

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [usłudze Azure Policy](../../governance/policy/overview.md)
- Zobacz przykłady magazynu kluczy: [wbudowane definicje zasad usługi Key Vault](../../governance/policy/samples/built-in-policies.md#key-vault)