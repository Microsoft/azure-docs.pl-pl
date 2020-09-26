---
title: Jak bezpiecznie uruchomić usługę zarządzania certyfikatami magazynu OPC — platforma Azure | Microsoft Docs
description: Opisuje sposób bezpiecznego uruchamiania usługi zarządzania certyfikatami magazynu OPC na platformie Azure oraz przegląd innych wytycznych dotyczących zabezpieczeń, które należy wziąć pod uwagę.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: d702a3cca6a24819d2d184dc6df09153c9dcc860
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91281885"
---
# <a name="run-the-opc-vault-certificate-management-service-securely"></a>Bezpieczne uruchamianie usługi zarządzania certyfikatami magazynu OPC

> [!IMPORTANT]
> Gdy aktualizujemy ten artykuł, zobacz [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) , aby uzyskać najbardziej aktualną zawartość.

W tym artykule opisano sposób bezpiecznego uruchamiania usługi zarządzania certyfikatami magazynu OPC na platformie Azure oraz przegląd innych wytycznych dotyczących zabezpieczeń, które należy wziąć pod uwagę.

## <a name="roles"></a>Role

### <a name="trusted-and-authorized-roles"></a>Zaufane i autoryzowane role

Mikrousługa magazynu OPC umożliwia różnym rolom dostęp do różnych części usługi.

> [!IMPORTANT]
> Podczas wdrażania skrypt dodaje tylko użytkownika, który uruchamia skrypt wdrożenia jako użytkownik dla wszystkich ról. W przypadku wdrożenia produkcyjnego należy zapoznać się z tym przypisaniem roli i zmienić konfigurację odpowiednio zgodnie z poniższymi wskazówkami. To zadanie wymaga ręcznego przypisania ról i usług w portalu aplikacji Azure Active Directory (Azure AD) w przedsiębiorstwie.

### <a name="certificate-management-service-roles"></a>Role usługi zarządzania certyfikatami

Mikrousługa magazynu OPC definiuje następujące role:

- **Czytelnik**: Domyślnie każdy uwierzytelniony użytkownik w dzierżawie ma dostęp do odczytu. 
  - Dostęp do odczytu do aplikacji i żądań certyfikatów. Może wyświetlać listę aplikacji i żądań certyfikatów oraz wykonywać dla nich zapytania. Również informacje o odnajdywaniu urządzeń i certyfikaty publiczne są dostępne z dostępem do odczytu.
- **Składnik zapisywania**: rola składnika zapisywania zostaje przypisana do użytkownika, aby dodać uprawnienia do zapisu dla określonych zadań. 
  - Dostęp do odczytu i zapisu do aplikacji i żądań certyfikatów. Może rejestrować, aktualizować i wyrejestrować aplikacje. Może tworzyć żądania certyfikatów i uzyskiwać zatwierdzone klucze prywatne i certyfikaty. Można również usunąć klucze prywatne.
- **Osoba zatwierdzająca**: rola osoby zatwierdzającej jest przypisana do użytkownika w celu zatwierdzenia lub odrzucenia żądań certyfikatów. Rola nie obejmuje żadnej innej roli.
  - Oprócz roli osoby zatwierdzającej, aby uzyskać dostęp do interfejsu API mikrousług magazynu OPC, użytkownik musi mieć również uprawnienie do podpisywania klucza w Azure Key Vault, aby można było podpisać certyfikaty.
  - Rola składnika zapisywania i osoby zatwierdzającej powinna być przypisana do różnych użytkowników.
  - Główną rolą osoby zatwierdzającej jest zatwierdzenie generacji i odrzucenie żądań certyfikatów.
- **Administrator**: rola administratora jest przypisana do użytkownika w celu zarządzania grupami certyfikatów. Rola nie obsługuje roli osoby zatwierdzającej, ale zawiera rolę składnika zapisywania.
  - Administrator może zarządzać grupami certyfikatów, zmieniać konfigurację i odwołać certyfikaty aplikacji, wydając nową listę odwołania certyfikatów (CRL).
  - W idealnym przypadku role składnika zapisywania, osoby zatwierdzającej i administratora są przypisywane do różnych użytkowników. Aby zwiększyć bezpieczeństwo, użytkownik z rolą osoby zatwierdzającej lub administratora wymaga również uprawnień do podpisywania kluczy w Key Vault, do wystawiania certyfikatów lub odnowienia certyfikatu urzędu certyfikacji wystawcy.
  - Oprócz roli administratora mikrousług rola obejmuje, ale nie ogranicza się do:
    - Odpowiedzialność za administrowanie wdrażaniem praktyk w zakresie zabezpieczeń urzędu certyfikacji.
    - Zarządzanie generowaniem, odwoływaniem i zawieszeniem certyfikatów. 
    - Zarządzanie cyklem życia kluczy kryptograficznych (na przykład odnowienie kluczy urzędu certyfikacji wystawcy).
    - Instalacja, konfiguracja i konserwacja usług, które obsługują urząd certyfikacji.
    - Codzienne operacje na usługach. 
    - Kopia zapasowa i odzyskiwanie bazy danych.

### <a name="other-role-assignments"></a>Inne przypisania ról

Podczas uruchamiania usługi należy również wziąć pod uwagę następujące role:

- Właściciel kontraktu zakupu certyfikatu z zewnętrznym głównym urzędem certyfikacji (na przykład gdy właściciel kupuje certyfikaty od zewnętrznego urzędu certyfikacji lub prowadzi urząd certyfikacji, który jest podrzędny wobec zewnętrznego urzędu certyfikacji).
- Opracowywanie i sprawdzanie poprawności urzędu certyfikacji.
- Przegląd rekordów inspekcji.
- Pracownicy, którzy obsługują urząd certyfikacji lub zarządzają urządzeniami fizycznymi i w chmurze, ale nie są bezpośrednio Zaufani do wykonywania operacji urzędu certyfikacji, znajdują się w roli *autoryzowanej* . Aby można było wykonać zestaw zadań w autoryzowanej roli, należy również udokumentować.

### <a name="review-memberships-of-trusted-and-authorized-roles-quarterly"></a>Przeglądanie członkostw zaufanych i autoryzowanych ról kwartalnie

Zapoznaj się z członkostwem zaufanych i autoryzowanych ról co najmniej kwartalnie. Upewnij się, że zbiór osób (dla procesów ręcznych) lub tożsamości usług (dla zautomatyzowanych procesów) w każdej roli jest minimalny.

### <a name="role-separation-between-certificate-requester-and-approver"></a>Separacja ról między obiektem żądającym certyfikatu a osobą zatwierdzającą

Proces wystawiania certyfikatów musi wymusić separację ról między rolami żądającym certyfikatu a zaakceptowaniem certyfikatu (osobami lub systemami zautomatyzowanymi). Wystawianie certyfikatów musi być autoryzowane przez rolę osoby zatwierdzającej certyfikat, która sprawdza, czy osoba żądająca certyfikatu ma autoryzację do uzyskiwania certyfikatów. Osoby, które posiadają rolę osoby zatwierdzającej certyfikat, muszą być osobą autoryzowaną w formie formalnie.

### <a name="restrict-assignment-of-privileged-roles"></a>Ograniczanie przypisania ról uprzywilejowanych

Należy ograniczyć Przypisanie ról uprzywilejowanych, takich jak autoryzacja członkostwa w grupie Administratorzy i osoby zatwierdzające, do ograniczonego zestawu autoryzowanego personelu. Wszelkie zmiany ról uprzywilejowanych muszą mieć dostęp odwołany w ciągu 24 godzin. Na koniec zapoznaj się z przypisaniami ról uprzywilejowanych co kwartał i Usuń niepotrzebne lub wygasłe przydziały.

### <a name="privileged-roles-should-use-two-factor-authentication"></a>Role uprzywilejowane powinny korzystać z uwierzytelniania dwuskładnikowego.

Użyj uwierzytelniania wieloskładnikowego (nazywanego również uwierzytelnianiem dwuskładnikowym) w przypadku interaktywnych logowania osób zatwierdzających i administratorów do usługi.

## <a name="certificate-service-operation-guidelines"></a>Wytyczne dotyczące operacji usługi certyfikatów

### <a name="operational-contacts"></a>Kontakty operacyjne

Usługa certyfikatów musi mieć aktualny plan odpowiedzi na zabezpieczenia w pliku, który zawiera szczegółowe kontakty dotyczące odpowiedzi na zdarzenia operacyjne.

### <a name="security-updates"></a>Aktualizacje zabezpieczeń

Wszystkie systemy muszą być stale monitorowane i aktualizowane przy użyciu najnowszych aktualizacji zabezpieczeń.

> [!IMPORTANT]
> Repozytorium GitHub usługi magazynu OPC jest stale aktualizowana przy użyciu poprawek zabezpieczeń. Monitoruj te aktualizacje i stosuj je do usługi w regularnych odstępach czasu.

### <a name="security-monitoring"></a>Monitorowanie zabezpieczeń

Zasubskrybuj lub Zaimplementuj odpowiednie monitorowanie zabezpieczeń. Na przykład Zasubskrybuj centralne rozwiązanie do monitorowania (takie jak Azure Security Center lub rozwiązanie do monitorowania pakietu Office 365) i skonfiguruj je odpowiednio w celu zapewnienia, że zdarzenia zabezpieczeń są przesyłane do rozwiązania do monitorowania.

> [!IMPORTANT]
> Domyślnie usługa magazynu OPC jest wdrażana za pomocą [platformy Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/devops) jako rozwiązanie do monitorowania. Dodawanie rozwiązania zabezpieczeń, takiego jak [Azure Security Center](https://azure.microsoft.com/services/security-center/) , jest zdecydowanie zalecane.

### <a name="assess-the-security-of-open-source-software-components"></a>Ocena zabezpieczeń składników oprogramowania typu open source

Wszystkie składniki typu "open source" używane w ramach produktu lub usługi muszą być wolne od średnich lub większej liczby luk w zabezpieczeniach.

> [!IMPORTANT]
> Podczas kompilacji ciągłej integracji repozytorium GitHub usługi magazynu OPC skanuje wszystkie składniki pod kątem luk w zabezpieczeniach. Monitoruj te aktualizacje w witrynie GitHub i stosuj je do usługi w regularnych odstępach czasu.

### <a name="maintain-an-inventory"></a>Obsługa spisu

Przechowywanie spisu zasobów dla wszystkich hostów produkcyjnych (w tym trwałych maszyn wirtualnych), urządzeń, wszystkich wewnętrznych zakresów adresów IP, adresów VIP i publicznych nazw domen DNS. Za każdym razem, gdy dodasz lub usuniesz system, adres IP urządzenia, VIP lub publiczną domenę DNS, należy zaktualizować spis w ciągu 30 dni.

#### <a name="inventory-of-the-default-azure-opc-vault-microservice-production-deployment"></a>Spis domyślnego wdrażania mikrousług magazynu platformy Azure OPC 

Na platformie Azure:
- **Plan App Service**: plan usługi App Service dla hostów usług. Domyślna wartość S1.
- **App Service** mikrousługi: Host usługi magazynu OPC.
- **App Service** dla przykładowej aplikacji: Host przykładowej aplikacji magazynu OPC.
- **Key Vault Standard**: przechowywanie wpisów tajnych i kluczy Azure Cosmos DB dla usług sieci Web.
- **Key Vault Premium**: do hostowania kluczy urzędu certyfikacji wystawcy, dla usługi podpisywania oraz konfiguracji magazynu i magazynu kluczy prywatnych aplikacji.
- **Azure Cosmos DB**: baza danych dla żądań aplikacji i certyfikatów. 
- **Application Insights**: (opcjonalnie) monitorowanie rozwiązania dla usług i aplikacji sieci Web.
- **Rejestracja aplikacji usługi Azure AD**: Rejestracja dla przykładowej aplikacji, usługi i modułu brzegowego.

W przypadku usług Cloud Services, wszystkie nazwy hostów, grupy zasobów, nazwy zasobów, identyfikatory subskrypcji i identyfikatory dzierżawy używane do wdrożenia usługi powinny być udokumentowane. 

W Azure IoT Edge lub na lokalnym serwerze IoT Edge:
- **Moduł IoT Edge magazynu OPC**: do obsługi globalnego serwera odnajdywania OPC UA sieci fabryki. 

W przypadku urządzeń IoT Edge nazwy hostów i adresy IP powinny być udokumentowane. 

### <a name="document-the-certification-authorities-cas"></a>Udokumentowanie urzędów certyfikacji

Dokumentacja hierarchii urzędu certyfikacji musi zawierać wszystkie obsługiwane urzędy certyfikacji. Obejmuje to wszystkie powiązane podrzędne urzędy certyfikacji, nadrzędne urzędy certyfikacji i główne urzędy certyfikacji, nawet jeśli nie są zarządzane przez usługę. Zamiast formalnej dokumentacji można dostarczyć wyczerpujący zestaw wszystkich certyfikatów urzędu certyfikacji, które nie wygasły.

> [!NOTE]
> Przykładowa aplikacja magazynu OPC obsługuje pobieranie wszystkich certyfikatów używanych i wygenerowanych w usłudze w celu uzyskania dokumentacji.

### <a name="document-the-issued-certificates-by-all-certification-authorities-cas"></a>Udokumentowanie wystawionych certyfikatów przez wszystkie urzędy certyfikacji (CA)

Podaj wyczerpujący zestaw wszystkich certyfikatów wystawionych w ciągu ostatnich 12 miesięcy.

> [!NOTE]
> Przykładowa aplikacja magazynu OPC obsługuje pobieranie wszystkich certyfikatów używanych i wygenerowanych w usłudze w celu uzyskania dokumentacji.

### <a name="document-the-standard-operating-procedure-for-securely-deleting-cryptographic-keys"></a>Udokumentowanie standardowej procedury operacyjnej w celu bezpiecznego usunięcia kluczy kryptograficznych

W okresie istnienia urzędu certyfikacji usunięcie klucza może wystąpić tylko rzadko. Jest to dlatego, że żaden użytkownik nie Key Vault ma przypisanego prawa do usuwania certyfikatu i nie ma żadnych interfejsów API, aby usunąć certyfikat wystawcy urzędu certyfikacji. Ręczna standardowa procedura operacyjna służąca do bezpiecznego usuwania kluczy kryptograficznych urzędu certyfikacji jest dostępna tylko przez bezpośredni dostęp do Key Vault w Azure Portal. Możesz również usunąć grupę certyfikatów w Key Vault. Aby zapewnić natychmiastowe usunięcie, wyłącz funkcję [usuwania nietrwałego Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) .

## <a name="certificates"></a>Certyfikaty

### <a name="certificates-must-comply-with-minimum-certificate-profile"></a>Certyfikaty muszą być zgodne z minimalnym profilem certyfikatu

Usługa magazynu OPC jest urzędem certyfikacji online, który wystawia certyfikaty jednostek końcowych dla subskrybentów. Mikrousługa magazynu OPC jest zgodna z poniższymi wskazówkami w implementacji domyślnej.

- Wszystkie certyfikaty muszą zawierać następujące pola X. 509, zgodnie z poniższym opisem:
  - Zawartość pola wersji musi być v3. 
  - Zawartość pola numer seryjny musi zawierać co najmniej 8 bajtów entropii uzyskanych z FIPS (Federal Information Processing Standards) 140, zatwierdzony generator liczb losowych.<br>
    > [!IMPORTANT]
    > Numer seryjny magazynu OPC jest domyślnie 20 bajtów i jest uzyskiwany z generatora liczb losowych kryptograficznych systemu operacyjnego. Generator liczb losowych to FIPS 140 zatwierdzony na urządzeniach z systemem Windows, ale nie w systemie Linux. Należy wziąć pod uwagę podczas wybierania wdrożenia usługi używającej maszyn wirtualnych z systemem Linux lub kontenerów platformy Docker systemu Linux, na których OpenSSL technologii nie jest zgodny ze standardem FIPS 140.
  - Pola issuerUniqueID i subjectUniqueID nie mogą być obecne.
  - Certyfikaty jednostki końcowej muszą być zidentyfikowane przy użyciu rozszerzenia podstawowych warunków ograniczających, zgodnie z IETF RFC 5280.
  - Pole pathLenConstraint musi mieć wartość 0 dla certyfikatu wystawiającego urzędu certyfikacji. 
  - Rozszerzenie rozszerzonego użycia klucza musi być obecne i musi zawierać minimalny zestaw rozszerzonych identyfikatorów obiektów użycia klucza (OID). Nie można określić identyfikatora OID anyExtendedKeyUsage (2.5.29.37.0). 
  - Rozszerzenie punktu dystrybucji listy CRL musi być obecne w certyfikacie urzędu certyfikacji wystawcy.<br>
    > [!IMPORTANT]
    > Rozszerzenie CDP jest obecne w certyfikatach urzędu certyfikacji magazynu OPC. Niemniej jednak urządzenia OPC UA używają metod niestandardowych do dystrybucji list CRL.
  - Rozszerzenie dostępu do informacji o urzędach musi być obecne w certyfikatach subskrybenta.<br>
    > [!IMPORTANT]
    > Rozszerzenie dostępu do informacji o urzędach jest obecne w certyfikatach subskrybenta magazynu OPC. Niemniej jednak urządzenia OPC UA używają metod niestandardowych do dystrybucji informacji o urzędzie certyfikacji wystawcy.
- Należy używać zatwierdzonych algorytmów asymetrycznych, długości kluczy, funkcji skrótu i trybów uzupełniania.
  - Jedynymi obsługiwanymi algorytmami są RSA i SHA-2.
  - RSA może służyć do szyfrowania, wymiany kluczy i podpisywania.
  - Szyfrowanie RSA musi używać tylko trybów uzupełniania OAEP, RSA-KEM lub RSA-PSS.
  - Wymagane są długości kluczy większe niż lub równe 2048 bitów.
  - Użyj rodziny SHA-2 algorytmów wyznaczania wartości skrótu (SHA256, SHA384 i SHA512).
  - Klucze głównego urzędu certyfikacji RSA o typowym okresie istnienia większym lub równym 20 lat muszą mieć wartość 4096 bitów lub większą.
  - Klucze urzędu certyfikacji wystawcy RSA muszą mieć co najmniej 2048 bitów. Jeśli data wygaśnięcia certyfikatu urzędu certyfikacji przypada po 2030, klucz urzędu certyfikacji musi mieć wartość 4096 bitów lub większą.
- Okres istnienia certyfikatu
  - Certyfikaty głównego urzędu certyfikacji: maksymalny okres ważności certyfikatu dla głównych urzędów certyfikacji nie może przekroczyć 25 lat.
  - Podrzędny urząd certyfikacji lub certyfikaty wystawcy urzędu certyfikacji w trybie online: maksymalny okres ważności certyfikatu dla urzędów certyfikacji w trybie online i tylko problem z certyfikatami subskrybenta nie może przekroczyć 6 lat. Dla tych urzędów certyfikacji pokrewny klucz podpisu prywatnego nie może być używany dłużej niż 3 lata w celu wystawiania nowych certyfikatów.<br>
    > [!IMPORTANT]
    > Certyfikat wystawcy, który jest generowany w domyślnej mikrousługi magazynu OPC bez zewnętrznego głównego urzędu certyfikacji, jest traktowany jak podrzędny urząd certyfikacji online z odpowiednimi wymaganiami i okresami istnienia. Domyślny okres istnienia to 5 lat, z długością klucza większą lub równą 2048.
  - Wszystkie klucze asymetryczne muszą mieć maksymalny okres 5 lat, a zalecany okres ważności wynosi 1 rok.<br>
    > [!IMPORTANT]
    > Domyślnie okresy istnienia certyfikatów aplikacji wystawionych w magazynie OPC mają okres istnienia wynoszący 2 lata i powinny być zastępowane co roku. 
  - Za każdym razem, gdy certyfikat zostanie odnowiony, jest odnawiany przy użyciu nowego klucza.
- OPC rozszerzenia specyficzne dla UA w certyfikatach wystąpień aplikacji
  - Rozszerzenie subjectAltName zawiera identyfikator URI i nazwy hostów aplikacji. Mogą to być również adresy FQDN, IPv4 i IPv6.
  - Użycie tego przykładu obejmuje bity digitalSignature, inkeyEncipherment i dataEncipherment.
  - ExtendedKeyUsage zawiera obiektu ServerAuth i clientAuth.
  - AuthorityKeyIdentifier jest określony w podpisanych certyfikatach.

### <a name="ca-keys-and-certificates-must-meet-minimum-requirements"></a>Klucze i certyfikaty urzędu certyfikacji muszą spełniać minimalne wymagania

- **Klucze prywatne**: klucze RSA muszą mieć co najmniej 2048 bitów. Jeśli data wygaśnięcia certyfikatu urzędu certyfikacji przypada po 2030, klucz urzędu certyfikacji musi mieć wartość 4096 bitów lub większą.
- **Okres istnienia**: maksymalny okres ważności certyfikatu dla urzędów certyfikacji w trybie online i tylko problem z certyfikatami subskrybenta nie może przekroczyć 6 lat. Dla tych urzędów certyfikacji pokrewny klucz podpisu prywatnego nie może być używany dłużej niż 3 lata w celu wystawiania nowych certyfikatów.

### <a name="ca-keys-are-protected-using-hardware-security-modules"></a>Klucze urzędu certyfikacji są chronione za pomocą sprzętowych modułów zabezpieczeń

OpcVault używa Azure Key Vault Premium, a klucze są chronione przez moduły zabezpieczeń (HSM) poziomu 2 standardu FIPS 140-2. 

Moduły kryptograficzne, które Key Vault używają, czy moduł HSM lub oprogramowanie są sprawdzone pod kątem FIPS. Klucze utworzone lub zaimportowane jako chronione przez moduł HSM są przetwarzane w module HSM, zweryfikowane pod kątem standardu FIPS 140-2 Level 2. Klucze utworzone lub zaimportowane jako chronione przez oprogramowanie są przetwarzane w ramach modułów kryptograficznych zweryfikowanych pod kątem standardu FIPS 140-2 Level 1.

## <a name="operational-practices"></a>Praktyki operacyjne

### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-enrollment"></a>Udokumentowanie i Obsługa standardowych praktyk infrastruktury PKI na potrzeby rejestracji certyfikatów

Udokumentowanie i Obsługa standardowych procedur operacyjnych (SPD) dotyczących sposobu wystawiania certyfikatów przez urzędy certyfikacji, w tym: 
- Sposób identyfikowania i uwierzytelniania subskrybenta. 
- Jak żądanie certyfikatu jest przetwarzane i sprawdzane (jeśli ma to zastosowanie), należy również uwzględnić sposób przetwarzania żądań odnowienia i ponownego generowania certyfikatu. 
- Jak wystawione certyfikaty są dystrybuowane do subskrybentów. 

SOP magazynu OPC jest opisany w [architekturze magazynu OPC](overview-opc-vault-architecture.md) i [zarządzać usługą certyfikatów magazynu OPC](howto-opc-vault-manage.md). Poniżej zastosowana jest procedura "OPC Unified Architecture Specification część 12: odnajdywanie i usługi globalne".


### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-revocation"></a>Udokumentowanie i Obsługa standardowych praktyk infrastruktury PKI na potrzeby odwoływania certyfikatów

Proces odwoływania certyfikatu został opisany w [architekturze magazynu OPC](overview-opc-vault-architecture.md) i [Zarządzanie usługą certyfikatów magazynu OPC](howto-opc-vault-manage.md).
    
### <a name="document-ca-key-generation-ceremony"></a>Procedury generowania klucza urzędu certyfikacji 

Generowanie klucza urzędu certyfikacji wystawcy w mikrousłudze magazynu OPC jest uproszczone ze względu na bezpieczny magazyn w Azure Key Vault. Aby uzyskać więcej informacji, zobacz [Zarządzanie usługą certyfikatów magazynu OPC](howto-opc-vault-manage.md).

Jednak w przypadku korzystania z zewnętrznego głównego urzędu certyfikacji generacja klucza urzędu certyfikacji procedury musi spełniać poniższe wymagania.

Procedury generowania klucza urzędu certyfikacji należy wykonać w odniesieniu do opisanego skryptu, który zawiera co najmniej następujące elementy: 
- Definicja ról i obowiązków uczestników.
- Zatwierdzenie do przeprowadzenia generowania klucza urzędu certyfikacji procedury.
- Sprzęt kryptograficzny i materiały aktywacji wymagane przez procedury.
- Przygotowanie sprzętu (w tym aktualizowanie i wylogowywanie informacji o zasobach i konfiguracji).
- Instalacja systemu operacyjnego.
- Określone kroki wykonywane podczas generowania klucza urzędu certyfikacji procedury, takie jak: 
  - Instalacja i konfiguracja aplikacji urzędu certyfikacji.
  - Generowanie klucza urzędu certyfikacji.
  - Kopia zapasowa klucza urzędu certyfikacji.
  - Podpisywanie certyfikatu urzędu certyfikacji.
  - Importuj klucze podpisane w chronionym module HSM usługi.
  - Zamykanie systemu urzędu certyfikacji.
  - Przygotowanie materiałów do magazynowania.


## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak bezpiecznie zarządzać magazynem OPC, możesz:

> [!div class="nextstepaction"]
> [Bezpieczne urządzenia OPC UA z magazynem OPC](howto-opc-vault-secure.md)
