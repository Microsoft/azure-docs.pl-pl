---
title: Konfigurowanie właściwości oferty maszyny wirtualnej na Azure Marketplace
description: Dowiedz się, jak skonfigurować właściwości oferty maszyny wirtualnej na Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: 5942368ba1709127b815a35676b716955c1bee8f
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819092"
---
# <a name="configure-virtual-machine-offer-properties"></a>Konfigurowanie właściwości oferty maszyny wirtualnej

Na  stronie Właściwości (wybierz z menu nav po lewej stronie) definiujesz kategorie używane do grupowania oferty maszyny wirtualnej w usługach Azure Marketplace, wersję aplikacji i kontrakty prawne, które obsługują twoją ofertę.

## <a name="select-a-category"></a>Wybierz kategorię

Wybierz kategorie i podkategorie, aby umieścić ofertę w odpowiednich obszarach Azure Marketplace wyszukiwania. Pamiętaj, aby w dalszej części opisu oferty opisać, w jaki sposób oferta obsługuje te kategorie.

- Wybierz kategorię Podstawowa.
- Aby dodać drugą opcjonalną kategorię (pomocniczą), wybierz **link +Kategorie.**
- Wybierz maksymalnie dwie podkategorie dla kategorii Podstawowa i/lub Pomocnicza. Jeśli żadna podkategoria nie ma zastosowania do Twojej oferty, wybierz **pozycję Nie dotyczy.** Naciśnij klawisze Ctrl+kliknięcie, aby wybrać drugą podkategorię.

Zobacz pełną listę kategorii i podkategorii w [tece Najlepsze rozwiązania](gtm-offer-listing-best-practices.md)dotyczące ofert. Oferty maszyn wirtualnych są zawsze wyświetlane w kategorii **Obliczenia** na Azure Marketplace.

## <a name="provide-terms-and-conditions"></a>Podaj warunki i postanowienia

W **obszarze Prawne** podaj warunki i postanowienia dotyczące oferty. Dostępne są dwie opcje:

- [Używanie umowy standardowej z opcjonalnymi poprawkami](#use-the-standard-contract)
- [Korzystanie z własnych warunków i postanowień](#use-your-own-terms-and-conditions)

Aby dowiedzieć się więcej na temat umowy standardowej i opcjonalnych poprawek, zobacz umowa standardowa for the Microsoft commercial marketplace (Platforma handlowa [firmy Microsoft).](standard-contract.md) Możesz pobrać plik [PDF umowa standardowa](https://go.microsoft.com/fwlink/?linkid=2041178) (upewnij się, że blokowanie wyskakujących okienek jest wyłączone).

### <a name="use-the-standard-contract"></a>Używanie kontraktu standardowego

Aby uprościć proces zaopatrzenia dla klientów i ograniczyć złożoność prawna dostawców oprogramowania, firma Microsoft oferuje standardowe umowy, których można użyć na potrzeby ofert na platformie handlowej. Jeśli oferujesz oprogramowanie w ramach umowy standardowej, klienci muszą je przeczytać i zaakceptować tylko raz i nie musisz tworzyć niestandardowych warunków i postanowień.

1. Zaznacz pole wyboru Użyj umowa standardowa dla platformy **handlowej firmy Microsoft.**

   ![Ilustruje użycie umowa standardowa dla platformy handlowej firmy Microsoft pole wyboru.](partner-center-portal/media/use-standard-contract.png)

1. W **oknie dialogowym** Potwierdzenie wybierz pozycję **Zaakceptuj**. W zależności od rozmiaru ekranu może być konieczne przewinięcie w górę w celu jego zobaczenia.
1. Przed **kontynuowaniem wybierz pozycję Zapisz** wersje robocze.

   > [!NOTE]
   > Po opublikowaniu oferty przy użyciu umowa standardowa na platformie handlowej nie można używać własnych niestandardowych warunków i postanowień. Możesz zaoferować rozwiązanie w ramach umowy standardowej z opcjonalnymi poprawkami lub zgodnie z własnymi warunkami i postanowieniami.

#### <a name="add-amendments-to-the-standard-contract-optional"></a>Dodawanie poprawek do umowy standardowej (opcjonalnie)

Dostępne są dwa rodzaje poprawek: *uniwersalny i* *niestandardowy*.

##### <a name="add-universal-amendment-terms"></a>Dodawanie uniwersalnych terminów poprawek

W polu **Universal amendment terms to the standard contract for Microsoft's commercial marketplace** (Uniwersalne poprawki do umowy standardowej dla platformy handlowej firmy Microsoft) wprowadź uniwersalne postanowienia dotyczące poprawek. W tym polu możesz wprowadzić nieograniczoną liczbę znaków. Te terminy są wyświetlane klientom w usłudze AppSource, Azure Marketplace i/lub Azure Portal podczas odnajdywania i przepływu zakupu.

##### <a name="add-one-or-more-custom-amendments"></a>Dodawanie co najmniej jednej poprawki niestandardowej

1. W **obszarze Niestandardowe postanowienia dotyczące poprawek umowa standardowa na** platformie handlowej firmy Microsoft wybierz link Dodaj niestandardowy termin poprawki (maks. **10).**
2. Wprowadź w **polu terminy dotyczące** niestandardowych poprawek.
3. Wprowadź identyfikator **dzierżawy** w polu. Tylko klienci skojarzone z identyfikatorami dzierżawy, które określisz dla tych terminów niestandardowych, będą widzieć ich w przepływie zakupu oferty w Azure Portal.

   > [!TIP]
   > Identyfikator dzierżawy identyfikuje klienta na platformie Azure. Możesz poprosić klienta o ten identyfikator i znaleźć go, przechodząc do [**https://portal.azure.com**](https://portal.azure.com)  >  **Azure Active Directory**  >  **Właściwości**. Wartość identyfikatora katalogu to identyfikator dzierżawy (na przykład `50c464d3-4930-494c-963c-1e951d15360e` ). Możesz również sprawdzić identyfikator dzierżawy organizacji klienta przy użyciu jego adresu URL nazwy domeny w tesłudze: Co to jest mój Microsoft Azure i identyfikator dzierżawy usługi [Office 365?](https://www.whatismytenantid.com/).

4. Opcjonalnie wprowadź przyjazny **opis** identyfikatora dzierżawy. Ten opis ułatwia zidentyfikowanie klienta, dla których dotyczy poprawka.
5. Aby dodać kolejny identyfikator dzierżawy, wybierz link Dodaj identyfikator dzierżawy klienta **(maksymalnie 10)** i powtórz kroki 3 i 4. Można dodać maksymalnie 20 identyfikatorów dzierżaw.
6. Aby dodać kolejny termin poprawki, powtórz kroki od 1 do 5. Możesz podać maksymalnie dziesięć niestandardowych terminów poprawek dla niestandardowych warunków dla oferty.
7. Wybierz **pozycję Zapisz roboczą** przed kontynuowaniem.

### <a name="use-your-own-terms-and-conditions"></a>Korzystanie z własnych warunków i postanowień

Zamiast korzystać z umowy standardowej, możesz podać własne warunki i postanowienia. Klienci muszą zaakceptować te warunki, aby wypróbować Twoją ofertę.

1. W **obszarze Prawne** wyczyść pole wyboru Użyj umowa standardowa dla komercyjnej platformy handlowej firmy **Microsoft.**
1. W **polu Warunki i postanowienia** wprowadź maksymalnie 10 000 znaków tekstu.

   > [!NOTE]
   > Jeśli potrzebujesz dłuższego opisu, wprowadź pojedynczy adres internetowy, który wskazuje, gdzie można znaleźć warunki i postanowienia. Będzie on wyświetlany klientom jako aktywny link.

1. Wybierz **pozycję Zapisz roboczą** przed kontynuowaniem do następnej karty w menu nav po lewej stronie: **Lista ofert.**

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie wpisu oferty maszyny wirtualnej](azure-vm-create-listing.md)
