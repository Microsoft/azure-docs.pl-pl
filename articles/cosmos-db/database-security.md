---
title: Zabezpieczenia bazy danych — Azure Cosmos DB
description: Dowiedz się, jak Azure Cosmos DB zapewnia ochronę bazy danych i bezpieczeństwo danych danych.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/21/2020
ms.author: mjbrown
ms.openlocfilehash: 19b4c8466e88159839ce1f43a5ba282b1bb3ec9e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94636932"
---
# <a name="security-in-azure-cosmos-db---overview"></a>Zabezpieczenia w usłudze Azure Cosmos DB — omówienie
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

W tym artykule omówiono najlepsze rozwiązania dotyczące zabezpieczeń baz danych i kluczowe funkcje oferowane przez usługę Azure Cosmos DB, które pomagają zapobiegać naruszeniom, wykrywać je i reagować na nie.

## <a name="whats-new-in-azure-cosmos-db-security"></a>Co nowego w zabezpieczeniach Azure Cosmos DB

Szyfrowanie w stanie spoczynku jest teraz dostępne dla dokumentów i kopii zapasowych przechowywanych w Azure Cosmos DB we wszystkich regionach świadczenia usługi Azure. Szyfrowanie w spoczynku jest automatycznie stosowane dla nowych i istniejących klientów w tych regionach. Nie ma potrzeby konfigurowania żadnych elementów; zapewnia to takie same doskonałe opóźnienia, przepływność, dostępność i funkcjonalność, jak przede wszystkim korzyści płynące z tego, że dane są bezpieczne i bezpieczne przy użyciu szyfrowania w stanie spoczynku.

## <a name="how-do-i-secure-my-database"></a>Jak mogę zabezpieczyć moją bazę danych

Bezpieczeństwo danych jest współdzieloną odpowiedzialnością klienta i dostawcy bazy danych. W zależności od wybranego dostawcy bazy danych ilość odpowiedzialności może się różnić. W przypadku wybrania rozwiązania lokalnego należy udostępnić wszystkim użytkownikom końcowym ochronę fizyczną sprzętu, co nie jest łatwym zadaniem. W przypadku wybrania dostawcy bazy danych w chmurze PaaS, takiego jak Azure Cosmos DB, obszar zainteresowania znacznie zmniejszy się. Na poniższym obrazie, które zostały zapożyczone przez [współużytkowane obowiązki firmy Microsoft w celu uzyskania](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91) oficjalnego dokumentu w chmurze, pokazuje, jak Twoja odpowiedzialność maleje z dostawcą PaaS, takim jak Azure Cosmos DB.

:::image type="content" source="./media/database-security/nosql-database-security-responsibilities.png" alt-text="Obowiązki dostawcy i klienta bazy danych":::

Na powyższym diagramie przedstawiono składniki zabezpieczeń w chmurze wysokiego poziomu, ale elementy, które trzeba zajmować się w przypadku rozwiązania bazy danych? I jak można porównać ze sobą rozwiązania?

Zalecamy poniższą listę kontrolną wymagań, na których należy porównać systemy baz danych:

- Ustawienia zabezpieczeń sieci i zapory
- Uwierzytelnianie użytkowników i precyzyjne kontrolki użytkownika
- Możliwość replikowania danych globalnie dla błędów regionalnych
- Możliwość przełączenia w tryb failover z jednego centrum danych do innego
- Lokalna replikacja danych w centrum danych
- Automatyczne kopie zapasowe danych
- Przywracanie usuniętych danych z kopii zapasowych
- Ochrona i izolowanie poufnych danych
- Monitorowanie ataków
- Reagowanie na ataki
- Możliwość, aby dane o ogrodzeniu geograficznego były zgodne z ograniczeniami ładu danych
- Ochrona fizyczna serwerów w chronionych centrach danych
- Certyfikaty

Chociaż może wydawać się oczywiste, ostatnie [naruszenia bazy danych na dużą skalę](https://thehackernews.com/2017/01/mongodb-database-security.html) przypominają nam o prostym, ale krytycznym znaczeniu dla następujących wymagań:

- Aktualizowane serwery z poprawkami
- Protokół HTTPS domyślnie/szyfrowanie TLS
- Konta administracyjne z silnymi hasłami

## <a name="how-does-azure-cosmos-db-secure-my-database"></a>Jak usługa Azure Cosmos DB zabezpiecza moją bazę danych

Przyjrzyjmy się powyższej liście — ile z tych wymagań dotyczących zabezpieczeń zapewnia Azure Cosmos DB? Każdy jeden z nich.

Przyjrzyjmy się szczegółowo Dig.

|Wymagania dotyczące zabezpieczeń|Podejście zabezpieczeń Azure Cosmos DB|
|---|---|
|Bezpieczeństwo sieci|Korzystanie z zapory IP jest pierwszą warstwą ochrony, aby zabezpieczyć bazę danych. Azure Cosmos DB obsługuje kontroli dostępu opartej na protokole IP na potrzeby obsługi zapory przychodzącej. Kontroli dostępu opartej na protokole IP są podobne do reguł zapory używanych przez tradycyjne systemy baz danych, ale są rozszerzane, dzięki czemu konto bazy danych usługi Azure Cosmos jest dostępne tylko z zatwierdzonego zestawu maszyn lub usług w chmurze. Więcej informacji znajduje się w artykule [obsługa Azure Cosmos DB zapory](how-to-configure-firewall.md) .<br><br>Azure Cosmos DB umożliwia włączenie określonego adresu IP (168.61.48.0), zakresu adresów IP (168.61.48.0/8) oraz kombinacji adresów IP i zakresów. <br><br>Wszystkie żądania pochodzące z maszyn spoza tej listy dozwolonych są blokowane przez Azure Cosmos DB. Żądania z zatwierdzonych maszyn i usług w chmurze muszą ukończyć proces uwierzytelniania, aby uzyskać kontrolę dostępu do zasobów.<br><br> Możesz użyć [tagów usługi sieci wirtualnej](../virtual-network/service-tags-overview.md) , aby uzyskać izolację sieci i chronić zasoby Azure Cosmos DB z poziomu ogólnego Internetu. Podczas tworzenia reguł zabezpieczeń należy używać tagów usługi zamiast określonych adresów IP. Określając nazwę tagu usługi (na przykład AzureCosmosDB) w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić.|
|Autoryzacja|Azure Cosmos DB używa kodu uwierzytelniania komunikatów (HMAC) dla autoryzacji. <br><br>Każde żądanie jest zmieszane przy użyciu klucza tajnego konta, a kolejny zakodowany skrót Base-64 jest wysyłany z każdym wywołaniem do Azure Cosmos DB. Aby sprawdzić poprawność żądania, usługa Azure Cosmos DB używa poprawnego klucza tajnego i właściwości do wygenerowania skrótu, a następnie porównuje wartość z tą w żądaniu. Jeśli dwie wartości są zgodne, operacja jest autoryzowana pomyślnie, a żądanie jest przetwarzane, w przeciwnym razie wystąpi błąd autoryzacji i żądanie zostało odrzucone.<br><br>Możesz użyć [klucza podstawowego](#primary-keys)lub [tokenu zasobu](secure-access-to-data.md#resource-tokens) , co pozwala uzyskać szczegółowy dostęp do zasobu, takiego jak dokument.<br><br>Dowiedz się więcej w temacie [Zabezpieczanie dostępu do zasobów Azure Cosmos DB](secure-access-to-data.md).|
|Użytkownicy i uprawnienia|Przy użyciu klucza podstawowego dla konta można tworzyć zasoby użytkowników i zasoby uprawnień dla poszczególnych baz danych. Token zasobu jest skojarzony z uprawnieniem w bazie danych i określa, czy użytkownik ma dostęp (do odczytu i zapisu, tylko do odczytu lub brak dostępu) do zasobu aplikacji w bazie danych. Zasoby aplikacji obejmują kontener, dokumenty, załączniki, procedury składowane, wyzwalacze i UDF. Token zasobu jest następnie używany podczas uwierzytelniania w celu zapewnienia lub odmowy dostępu do zasobu.<br><br>Dowiedz się więcej w temacie [Zabezpieczanie dostępu do zasobów Azure Cosmos DB](secure-access-to-data.md).|
|Integracja z usługą Active Directory (Azure RBAC)| Możesz także zapewnić lub ograniczyć dostęp do konta Cosmos, bazy danych, kontenera i ofert (przepływności) przy użyciu funkcji kontroli dostępu (IAM) w Azure Portal. IAM zapewnia kontrolę dostępu opartą na rolach i integruje się z Active Directory. Możesz użyć wbudowanych ról lub ról niestandardowych dla użytkowników indywidualnych i grup. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [integracji Active Directory](role-based-access-control.md) .|
|Replikacja globalna|Azure Cosmos DB oferuje dystrybucję globalną gotowe, która umożliwia replikację danych do każdego z dostępnych w całym świecie centrów dane platformy Azure z kliknięciem przycisku. Replikacja globalna pozwala skalować globalnie i zapewniać dostęp do danych na całym świecie o małym opóźnieniu.<br><br>W kontekście zabezpieczeń replikacja globalna zapewnia ochronę danych przed awariami regionalnymi.<br><br>Dowiedz się więcej z artykułu [Distribute data globally](distribute-data-globally.md) (Globalna dystrybucja danych).|
|Praca w trybie failover w regionach|Jeśli dane zostały zreplikowane w więcej niż jednym centrum danych, Azure Cosmos DB automatycznie przenoszone przez operacje, gdy regionalne centrum danych przejdzie do trybu offline. Można utworzyć listę priorytetów regionów trybu failover przy użyciu regionów, w których dane są replikowane. <br><br>Więcej informacji na temat [regionalnych trybu failover znajduje się w Azure Cosmos DB](high-availability.md).|
|Replikacja lokalna|Nawet w ramach jednego centrum danych Azure Cosmos DB automatycznie replikuje dane pod kątem wysokiej dostępności, zapewniając wybór [poziomów spójności](consistency-levels.md). Ta replikacja gwarantuje dostępność na 99,99 [% dla wszystkich](https://azure.microsoft.com/support/legal/sla/cosmos-db) kont w jednym regionie, a wszystkie konta wieloregionowe ze spójną spójnością i 99,999% odczytu na wszystkich wieloregionowych kontach baz danych.|
|Zautomatyzowane kopie zapasowe online|Kopie zapasowe baz danych usługi Azure Cosmos są regularnie tworzone i przechowywane w geograficznie nadmiarowym magazynie. <br><br>Dowiedz się więcej w temacie [Automatyczne tworzenie kopii zapasowych online i przywracanie za pomocą Azure Cosmos DB](online-backup-and-restore.md).|
|Przywracanie usuniętych danych|Zautomatyzowane kopie zapasowe w trybie online mogą służyć do odzyskiwania danych, które można przypadkowo usunąć do 30 dni po zdarzeniu. <br><br>Dowiedz się więcej w temacie [Automatyczne tworzenie kopii zapasowych online i przywracanie za pomocą Azure Cosmos DB](online-backup-and-restore.md)|
|Ochrona i izolowanie poufnych danych|Wszystkie dane w regionach na liście nowości? jest teraz szyfrowana w stanie spoczynku.<br><br>Dane osobowe i inne dane poufne mogą być izolowane do określonego kontenera, a dostęp do odczytu i zapisu mogą być ograniczone do określonych użytkowników.|
|Monitoruj ataki|Korzystając z [rejestrowania inspekcji i dzienników aktywności](./monitor-cosmos-db.md), można monitorować konto pod kątem normalnej i nieprawidłowej aktywności. Możesz zobaczyć, jakie operacje zostały wykonane względem zasobów, kto zainicjował operację, po wystąpieniu operacji, stan operacji i wiele innych, jak pokazano na zrzucie ekranu poniższej tabeli.|
|Reagowanie na ataki|Po skontaktowaniu się z pomocą techniczną platformy Azure w celu zgłoszenia potencjalnego ataku zostanie rozpoczęty proces reagowania na zdarzenia z 5 etapami. Proces 5-etapowy polega na przywróceniu normalnego zabezpieczenia usługi i operacji tak szybko, jak to możliwe po wykryciu problemu i rozpoczęciu badania.<br><br>Dowiedz się więcej w [Microsoft Azure odpowiedzi na zabezpieczenia w chmurze](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).|
|Ogrodzenie geograficzne|Azure Cosmos DB zapewnia zarządzanie danymi dla suwerennych regionów (na przykład Niemiec, Chin, US Gov).|
|Chronione obiekty|Dane w Azure Cosmos DB są przechowywane w dysków SSD w chronionych centrach danych platformy Azure.<br><br>Dowiedz się więcej w [globalnych centrach danych firmy Microsoft](https://www.microsoft.com/en-us/cloud-platform/global-datacenters)|
|Szyfrowanie HTTPS/SSL/TLS|Wszystkie połączenia do Azure Cosmos DB obsługują protokół HTTPS. Azure Cosmos DB obsługuje również protokół TLS 1,2.<br>Istnieje możliwość wymuszenia minimalnej wersji protokołu TLS po stronie serwera. Aby to zrobić, skontaktuj się z firmą [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com) .|
|Szyfrowanie danych magazynowanych|Wszystkie dane przechowywane w Azure Cosmos DB są szyfrowane w stanie spoczynku. Dowiedz się więcej w temacie [szyfrowanie Azure Cosmos DB](./database-encryption-at-rest.md) w stanie spoczynku|
|Serwery z poprawkami|Jako zarządzana baza danych Azure Cosmos DB eliminuje konieczność zarządzania i poprawiania serwerów, które są wykonywane automatycznie.|
|Konta administracyjne z silnymi hasłami|Trudno jest sądzić, że nawet musimy wymienić ten wymóg, ale w przeciwieństwie do niektórych naszych konkurentów nie ma możliwości posiadania konta administracyjnego bez hasła w Azure Cosmos DB.<br><br> Zabezpieczenia przez uwierzytelnianie oparte na kluczach tajnych TLS i HMAC są domyślnie rozszerzania.|
|Certyfikaty zabezpieczeń i ochrony danych| Najbardziej aktualną listę certyfikatów zawiera ogólna [Witryna zgodności platformy Azure](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings) , a także najnowszy [dokument zgodności platformy Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) ze wszystkimi certyfikatami (Search for Cosmos). Aby uzyskać bardziej skoncentrowany odczyt, zapoznaj się z dniem 25 kwietnia 2018. [Azure #CosmosDB: Secure, Private, zgodny, który zawiera SOC 1/2 Type 2, HITRUST, PCI DSS Level 1, ISO 27001, HIPAA, FedRAMP High i wiele innych.

Poniższy zrzut ekranu pokazuje, jak można użyć rejestrowania inspekcji i dzienników aktywności do monitorowania konta: :::image type="content" source="./media/database-security/nosql-database-security-application-logging.png" alt-text="dzienniki aktywności dla Azure Cosmos DB":::

<a id="primary-keys"></a>

## <a name="primary-keys"></a>Klucze podstawowe

Klucze podstawowe zapewniają dostęp do wszystkich zasobów administracyjnych dla konta bazy danych. Klucze podstawowe:

- Zapewnianie dostępu do kont, baz danych, użytkowników i uprawnień. 
- Nie można użyć, aby zapewnić szczegółowy dostęp do kontenerów i dokumentów.
- Są tworzone podczas tworzenia konta.
- Można ją wygenerować w dowolnym momencie.

Każde konto zawiera dwa klucze podstawowe: klucz podstawowy i klucz pomocniczy. Dwa klucze polega na tym, że można generować lub przetwarzać klucze, zapewniając ciągły dostęp do konta i danych.

Oprócz dwóch kluczy podstawowych dla konta Cosmos DB istnieją dwa klucze tylko do odczytu. Te klucze tylko do odczytu zezwalają na operacje odczytu tylko na tym koncie. Klucze tylko do odczytu nie zapewniają dostępu do uprawnień do odczytu zasobów.

Klucze podstawowe, pomocnicze, tylko do odczytu i odczyt-zapis można pobrać i ponownie wygenerować przy użyciu Azure Portal. Aby uzyskać instrukcje, zobacz [Wyświetlanie, kopiowanie i ponowne generowanie kluczy dostępu](manage-with-cli.md#regenerate-account-key).

:::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-portal.png" alt-text="Kontrola dostępu (IAM) w Azure Portal-demonstrowanie zabezpieczeń bazy danych NoSQL":::

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat kluczy podstawowych i tokenów zasobów, zobacz [Zabezpieczanie dostępu do danych Azure Cosmos DB](secure-access-to-data.md).

Aby uzyskać więcej informacji na temat rejestrowania inspekcji, zobacz [Azure Cosmos DB rejestrowanie diagnostyczne](./monitor-cosmos-db.md).

Aby uzyskać więcej informacji o certyfikatach firmy Microsoft, zobacz [Centrum zaufania Azure](https://azure.microsoft.com/support/trust-center/).