---
title: Konfigurowanie właściwości oferty maszyny wirtualnej w witrynie Azure Marketplace
description: Dowiedz się, jak skonfigurować właściwości oferty maszyny wirtualnej w witrynie Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: 281553782774c31ec8cfaf614542fd739c4d3dd9
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629533"
---
# <a name="how-to-configure-virtual-machine-offer-properties"></a>Jak skonfigurować właściwości oferty maszyny wirtualnej

Na stronie **Właściwości** (wybierz z menu po lewej stronie w centrum partnerskim) Zdefiniuj kategorie używane do grupowania oferty maszyny wirtualnej (VM) w witrynie Azure Marketplace, wersji aplikacji oraz umów prawnych, które obsługują Twoją ofertę.

## <a name="select-a-category"></a>Wybierz kategorię

Wybierz kategorie i podkategorie, aby umieścić ofertę w odpowiednich obszarach wyszukiwania portalu Azure Marketplace. Pamiętaj, aby opisać w dalszej części opisu oferty, jak Twoja oferta obsługuje te kategorie.

- Wybierz kategorię podstawową.
- Aby dodać drugą opcjonalną kategorię (pomocniczą), wybierz łącze **+ Kategorie** .
- Wybierz maksymalnie dwie podkategorie dla kategorii podstawowa i/lub pomocnicza. Jeśli żadna Podkategoria nie ma zastosowania do oferty, wybierz pozycję **nie dotyczy**. Naciśnij klawisz Ctrl i kliknij, aby wybrać drugą podkategorię.

Zapoznaj się z pełną listą kategorii i podkategorii w artykule [Oferta z najlepszymi rozwiązaniami](gtm-offer-listing-best-practices.md). Oferty maszyn wirtualnych są zawsze wyświetlane w kategorii **obliczenia** w witrynie Azure Marketplace.

## <a name="provide-terms-and-conditions"></a>Podaj warunki i postanowienia

W obszarze **prawne** Podaj warunki i postanowienia dotyczące oferty. Dostępne są dwie opcje:

- [Użyj standardowej umowy z opcjonalnymi zmianami](#use-the-standard-contract)
- [Korzystanie z własnych warunków i postanowień](#use-your-own-terms-and-conditions)

Aby dowiedzieć się więcej o standardowej umowie i opcjonalnych zmianach, zobacz artykuł [Standardowy dla portalu komercyjnego firmy Microsoft](standard-contract.md). Możesz pobrać plik PDF [kontraktu standardowego](https://go.microsoft.com/fwlink/?linkid=2041178) (Upewnij się, że blokowanie wyskakujących okienek jest wyłączone).

### <a name="use-the-standard-contract"></a>Korzystanie z kontraktu standardowego

Aby uprościć proces zakupów dla klientów i zmniejszyć złożoność prawną dla dostawców oprogramowania, firma Microsoft oferuje standardowy kontrakt, którego możesz użyć dla ofert w portalu komercyjnym. W przypadku oferowania oprogramowania zgodnie z umową standardowa klienci muszą tylko raz odczytywać i akceptować ją i nie muszą tworzyć niestandardowych warunków i postanowień.

1. Zaznacz pole wyboru **Użyj standardowego kontraktu dla komercyjnego portalu firmy Microsoft** .

   ![Ilustruje pole wyboru Użyj standardowego kontraktu dla komercyjnej witryny Marketplace firmy Microsoft.](partner-center-portal/media/use-standard-contract.png)

1. W oknie dialogowym **potwierdzenia** wybierz pozycję **Zaakceptuj**. W zależności od rozmiaru ekranu, może być konieczne przewinięcie w górę w celu wyświetlenia go.
1. Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

   > [!NOTE]
   > Po opublikowaniu oferty przy użyciu standardowej umowy dotyczącej komercyjnej witryny Marketplace nie można użyć własnych niestandardowych warunków i postanowień. Oferuj swoje rozwiązanie w ramach standardowej umowy z opcjonalnymi zmianami lub w ramach własnych warunków i postanowień.

#### <a name="add-amendments-to-the-standard-contract-optional"></a>Dodaj zmiany do standardowej umowy (opcjonalnie)

Dostępne są dwa rodzaje zmian: *uniwersalne* i *niestandardowe*.

##### <a name="add-universal-amendment-terms"></a>Dodaj postanowienia dotyczące uniwersalnej zmiany

W obszarze **postanowienia dotyczące zmiany uniwersalnej do standardowej umowy Marketplace firmy Microsoft** wprowadź swoje uniwersalne postanowienia dotyczące zmiany. W tym polu można wprowadzić nieograniczoną liczbę znaków. Te warunki są wyświetlane klientom w AppSource, w witrynie Azure Marketplace i/lub Azure Portal podczas odnajdywania i przepływu zakupów.

##### <a name="add-one-or-more-custom-amendments"></a>Dodawanie co najmniej jednej zmiany niestandardowej

1. W obszarze **niestandardowe zmiany warunki do standardowej umowy dotyczącej komercyjnej witryny Marketplace firmy Microsoft** wybierz łącze **Dodaj termin poprawki niestandardowej (maks. 10)** .
2. Wprowadź **niestandardowe warunki zmiany** w polu.
3. W polu wprowadź **Identyfikator dzierżawy** . Tylko klienci powiązani z identyfikatorami dzierżawy określonymi dla tych terminów niestandardowych będą widzieć je w przepływie zakupu oferty w Azure Portal.

   > [!TIP]
   > Identyfikator dzierżawy identyfikuje Twojego klienta na platformie Azure. Możesz polecić klienta dla tego identyfikatora i znaleźć go, przechodząc do [**https://portal.azure.com**](https://portal.azure.com)  >  **Azure Active Directory**  >  **Właściwości**. Wartość identyfikatora katalogu jest identyfikator dzierżawy (na przykład `50c464d3-4930-494c-963c-1e951d15360e` ). Możesz również wyszukać identyfikator dzierżawy organizacji klienta przy użyciu adresu URL nazwy domeny, co [to jest mój Microsoft Azure i identyfikator dzierżawy pakietu Office 365?](https://www.whatismytenantid.com/).

4. Opcjonalnie wprowadź przyjazny **Opis** identyfikatora dzierżawy. Ten opis pomaga zidentyfikować klienta, którego celem jest zmiana.
5. Aby dodać inny identyfikator dzierżawy, wybierz łącze **Dodaj identyfikator dzierżawy klienta (maks. 10)** i powtórz kroki 3 i 4. Możesz dodać maksymalnie 20 identyfikatorów dzierżawców.
6. Aby dodać kolejny termin zmiany, powtórz kroki od 1 do 5. Możesz dostarczyć do dziesięciu postanowień w ramach oferty.
7. Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

### <a name="use-your-own-terms-and-conditions"></a>Korzystanie z własnych warunków i postanowień

Możesz określić własne warunki i postanowienia zamiast standardowej umowy. Aby wypróbować ofertę, klienci muszą zaakceptować te warunki.

1. W obszarze **prawne** wyczyść pole wyboru **Użyj standardowej umowy dotyczącej komercyjnej witryny Microsoft Marketplace** .
1. W polu **warunki i** postanowienia wprowadź do 10 000 znaków tekstu.

   > [!NOTE]
   > Jeśli potrzebujesz dłuższego opisu, wprowadź pojedynczy adres internetowy wskazujący, gdzie można znaleźć warunki i postanowienia. Będzie ona wyświetlana klientom jako aktywne łącze.

1. Wybierz pozycję **Zapisz wersję roboczą** przed przejściem do następnej karty w menu po lewej stronie, Wyświetl **listę oferty**.

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie wpisu oferty maszyny wirtualnej](azure-vm-create-listing.md)
