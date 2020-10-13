---
title: Microsoft Azure Data Box Disk — omówienie zabezpieczeń | Microsoft Docs
description: W tym artykule opisano funkcje zabezpieczeń usługi Azure Data Box dotyczących urządzenia, usługi i danych, które znajdują się na urządzeniu Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: alkohli
ms.openlocfilehash: 132c072ad4cbee5cc3ac49695aa34480dc827400
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89565767"
---
# <a name="azure-data-box-security-and-data-protection"></a>Zabezpieczenia i ochrona danych w usłudze Azure Data Box

Data Box to bezpieczne rozwiązanie do ochrony danych, które gwarantuje, że wyłącznie upoważnione podmioty będą mogły uzyskać dostęp do danych, zmodyfikować je lub usunąć. W tym artykule opisano funkcje zabezpieczeń usługi Azure Data Box, które pomagają chronić poszczególne składniki rozwiązania Data Box i przechowywane w nich dane. 

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="data-flow-through-components"></a>Przepływ danych za pośrednictwem składników

Rozwiązanie Microsoft Azure Data Box obejmuje cztery główne składniki, które wzajemnie ze sobą współdziałają:

- **Usługa Azure Data Box hostowana na platformie Azure** — usługa do zarządzania, umożliwiająca zamówienie urządzenia, skonfigurowanie go oraz śledzenie realizacji zamówienia.
- **Urządzenie Data Box** — urządzenie do transferu dostarczane w celu zaimportowania danych lokalnych na platformę Azure. 
- **Klienci/hosty, do których podłączane jest urządzenie** — klienci w infrastrukturze lokalnej, do których podłącza się urządzenie Data Box i które zawierają dane wymagające ochrony.
- **Magazyn w chmurze** — lokalizacja w chmurze platformy Azure, w której są przechowywane dane. Zazwyczaj jest to konto magazynu połączone z utworzonym zasobem usługi Azure Data Box.

Na poniższym diagramie przedstawiono przepływ danych za pośrednictwem rozwiązania Azure Data Box z lokalnego na platformę Azure i różnych funkcji zabezpieczeń, które są przeznaczone do przepływu danych przez rozwiązanie. Ten przepływ dotyczy kolejności importu dla urządzenie Data Box.

![urządzenie Data Box zabezpieczenia importowania](media/data-box-security/data-box-security-2.png)

Na poniższym diagramie przedstawiono kolejność eksportowania dla urządzenie Data Box.

![urządzenie Data Box zabezpieczenia eksportu](media/data-box-security/data-box-security-3.png)

Ponieważ dane są przepływane przez to rozwiązanie, rejestrowane są zdarzenia i dzienniki są generowane. Aby uzyskać więcej informacji, przejdź do:

- [Śledzenie i rejestrowanie zdarzeń dla Azure Data Box zamówień importu](data-box-logs.md).
- [Śledzenie i rejestrowanie zdarzeń dla Azure Data Box zamówień eksportu](data-box-export-logs.md)

## <a name="security-features"></a>Funkcje zabezpieczeń

Data Box to bezpieczne rozwiązanie do ochrony danych, które gwarantuje, że wyłącznie upoważnione podmioty będą mogły uzyskać dostęp do danych, zmodyfikować je lub usunąć. Zabezpieczenia rozwiązania obejmują zabezpieczenia urządzenia i powiązanej usługi, zapewniające bezpieczeństwo przechowywanych danych. 

### <a name="data-box-device-protection"></a>Ochrona urządzenia Data Box

Urządzenie Data Box jest chronione przez następujące funkcje:

- Wytrzymała obudowa urządzenia odporna na wstrząsy, negatywny wpływ transportu i warunki otoczenia. 
- Wykrywanie naruszeń sprzętu i oprogramowania, które uniemożliwiają dalsze operacje związane z urządzeniem.
- Uruchamia się tylko oprogramowanie urządzenia Data Box.
- Urządzenie uruchamia się w stanie zablokowanym.
- Dostęp do urządzenia jest możliwy po podaniu hasła odblokowania urządzenia.
- Poświadczenia dostępu umożliwiające kopiowanie danych do i z urządzenia. Wszystkie uprawnienia dostępu do strony **poświadczenia urządzenia** w Azure Portal są rejestrowane w [dziennikach aktywności](data-box-logs.md#query-activity-logs-during-setup).

### <a name="data-box-data-protection"></a>Ochrona danych na urządzeniu Data Box

Dane przesyłane do i z rozwiązania Data Box są chronione przez następujące funkcje:

- 256-bitowe szyfrowanie AES magazynowanych danych.
- W przypadku transmitowanych danych można używać zaszyfrowanych protokołów. Zalecamy użycie protokołu SMB 3,0 z szyfrowaniem w celu ochrony danych podczas kopiowania ich z serwerów danych.
- Bezpieczne usunięcie danych z urządzenia po zakończeniu przekazywania danych na platformę Azure. Wymazywane dane są zgodne z wytycznymi zawartymi w [dodatku A dla dysków twardych usługi ATA w standardach NIST 800-88r1](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-88r1.pdf). Zdarzenie wymazywania danych jest rejestrowane w [historii kolejności](data-box-logs.md#download-order-history).

### <a name="data-box-service-protection"></a>Ochrona usługi Data Box

Usługa Data Box jest chroniona przez następujące funkcje.

- Aby uzyskać dostęp do usługi Data Box, organizacja musi mieć subskrypcję platformy Azure obejmującą usługę Data Box. Subskrypcja określa funkcje, do których masz dostęp w witrynie Azure Portal.
- Ponieważ usługa Data Box jest hostowana na platformie Azure, chronią ją funkcje zabezpieczeń platformy Azure. Aby uzyskać więcej informacji na temat funkcji zabezpieczeń platformy Microsoft Azure, zobacz [Centrum zaufania Microsoft Azure](https://www.microsoft.com/TrustCenter/Security/default.aspx).
- Dostęp do kolejności urządzenie Data Box można kontrolować za pomocą ról platformy Azure. Aby uzyskać więcej informacji, zobacz [Konfigurowanie kontroli dostępu dla kolejności urządzenie Data Box](data-box-logs.md#set-up-access-control-on-the-order)
- W usłudze Data Box jest przechowywane hasło odblokowania służące do odblokowania urządzenia w usłudze.
- W usłudze Data Box są przechowywane szczegóły zamówienia i jego stan. Te informacje są usuwane po usunięciu zamówienia.

## <a name="managing-personal-data"></a>Zarządzanie danymi osobowymi

W usłudze Azure Data Box dane osobowe są zbierane i wyświetlane w następujących kluczowych przypadkach:

- **Ustawienia powiadomień** — podczas tworzenia zamówienia w ustawieniach powiadomień są wprowadzane adresy e-mail użytkowników. Te informacje są widoczne dla administratora. Te informacje są usuwane z usługi, gdy zadanie zmieni stan na końcowy lub gdy zamówienie zostanie usunięte.

- **Szczegóły zamówienia** — po utworzeniu zamówienia na platformie Azure zostaną zapisane dane kontaktowe użytkowników, adres wysyłki, adres e-mail. Zapisane informacje obejmują:

  - Nazwa kontaktu
  - Numer telefonu
  - Poczta e-mail
  - Adres
  - City (Miasto)
  - Kod pocztowy
  - State
  - Kraj/Województwo/Region
  - Numer konta operatora
  - Numer śledzenia dostawy

    Szczegóły zamówienia są usuwane z usługi Data Box po zakończeniu zadania lub usunięciu zamówienia.

- **Adres wysyłkowy** — po złożeniu zamówienia usługa Data Box przekazuje adres wysyłkowy operatorom zewnętrznym, na przykład firmie UPS lub DHL. 

Aby uzyskać więcej informacji, zapoznaj się z Zasadami ochrony prywatności firmy Microsoft w [Centrum zaufania](https://www.microsoft.com/trustcenter).


## <a name="security-guidelines-reference"></a>Dokumentacja wytycznych dotyczących zabezpieczeń

W usłudze Data Box są zaimplementowane następujące wytyczne dotyczące zabezpieczeń: 

|Wytyczna   |Opis   |
|---------|---------|
|[IEC 60529 IP52](https://www.iec.ch/)    | Dotyczy ochrony przed wodą i pyłem         |
|[ISTA 2A](https://ista.org/docs/2Aoverview.pdf)     | Dotyczy odporności na niekorzystne warunki podczas transportu          |
|[NIST SP 800-147](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-147.pdf)      | Dotyczy bezpiecznej aktualizacji oprogramowania układowego         |
|[FIPS 140-2 Level 2](https://csrc.nist.gov/csrc/media/publications/fips/140/2/final/documents/fips1402.pdf)      | Dotyczy ochrony danych         |
|Dodatek A dla dysków twardych usługi ATA w obszarze [NIST SP 88r1](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-88r1.pdf)      | Dotyczy oczyszczania danych         |

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z [wymaganiami rozwiązania Data Box](data-box-system-requirements.md).
- Poznaj [ograniczenia usługi Data Box](data-box-limits.md).
- Szybko wdróż usługę [Azure Data Box](data-box-quickstart-portal.md) w witrynie Azure Portal.
