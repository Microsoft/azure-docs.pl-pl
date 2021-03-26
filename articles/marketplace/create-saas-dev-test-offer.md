---
title: Tworzenie oferty testowej
description: Jak utworzyć oddzielną ofertę programistyczną na potrzeby testowania oferty produkcyjnej w komercyjnym programie w witrynie Microsoft Partner Center.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 893d38d7dcf2ef0910bc46d3e9bfd168c2a89162
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105543367"
---
# <a name="create-a-test-offer"></a>Tworzenie oferty testowej

Aby opracowywać w osobnym środowisku z oferty produkcyjnej, utworzysz oddzielną ofertę testową i programistyczną oraz oddzielną ofertę produkcyjną (PROD). Aby uzyskać informacje na temat korzyści wynikających z używania oddzielnej oferty DEWELOPERSKIej, zobacz [Planowanie oferty SaaS](plan-saas-offer.md#test-offer).

Skonfigurujesz większość ustawień w ofertach DEV i PROD. Na przykład Oficjalny język marketingowy i zasoby, takie jak zrzuty ekranu i logo, powinny być takie same. W przypadkach, w których konfiguracja jest taka sama, można kopiować i wklejać pola z planów w ofercie DEV do planów w ofercie PRODUKCYJNEj.

W poniższych sekcjach opisano różnice między ofertami DEV i PROD.

## <a name="offer-setup-page"></a>Strona konfiguracji oferty

Zalecamy użycie tego samego aliasu w polu **alias** obu ofert, ale dołączenie "_test" do aliasu oferty dev. Na przykład jeśli alias oferty PRODUKCYJNEj to "contososolution", alias oferty DEV powinien mieć wartość "contososolution_test". W ten sposób można łatwo określić, którą ofertę DEV z oferty PRODUKCYJNEj.

W sekcji **potencjalni klienci** Skorzystaj z tabeli platformy Azure lub środowiska testowego programu CRM dla oferty dev. Użyj zamierzonego systemu zarządzania potencjalnymi klientami dla oferty PRODUKCYJNEj.

## <a name="properties-page"></a>Strona właściwości

Skonfiguruj Tę stronę w taki sam sposób, jak w ofertach DEV i PROD.

## <a name="offer-listing-page"></a>Strona z listą ofert

Skonfiguruj Tę stronę w taki sam sposób, jak w ofertach DEV i PROD.

## <a name="preview-audience"></a>Podgląd odbiorców

W ofercie DEV Uwzględnij nazwy główne użytkowników usługi Azure Active Directory (AAD) lub konto Microsoft (MSA) adresy e-mail deweloperów i testerów, w tym siebie. Główna nazwa użytkownika dla użytkownika w usłudze AAD może różnić się od wiadomości e-mail danego użytkownika. Na przykład program jane.doe@contoso.com nie będzie działał, ale janedoe@contoso.com będzie. Wyznaczeni użytkownicy będą mieli dostęp do oferty DEV po udostępnieniu linku do **wersji zapoznawczej** w fazie tworzenia i testowania.

W ofercie PRODUKCYJNEj Podaj nazwę główną użytkownika usługi Azure AD lub adres Microsoft e-mail użytkowników, którzy będą sprawdzać poprawność oferty przed wybraniem **przycisku Przejdź na żywo** w celu opublikowania oferty na żywo.

## <a name="technical-configuration-page"></a>Strona konfiguracji technicznej

W tej tabeli opisano różnice między ustawieniami ofert DEWELOPERSKIch i ofertami PRODUKCYJNymi.

***Tabela 1: różnice w konfiguracji technicznej***

| Ustawienie | Oferta DEV | Oferta PRODUKCYJNa |
| ------------ | ------------- | ------------- |
| Adres URL strony docelowej | Wprowadź punkt końcowy tworzenia i testowania. | Wprowadź punkt końcowy produkcji. |
| Element webhook połączenia | Wprowadź punkt końcowy tworzenia i testowania. | Wprowadź punkt końcowy produkcji. |
| Identyfikator dzierżawy Azure Active Directory | Wprowadź identyfikator dzierżawy rejestracji aplikacji testowej (identyfikator katalogu usługi AAD). | Wprowadź identyfikator dzierżawy rejestracji aplikacji produkcyjnej. |
| Identyfikator aplikacji usługi Azure Active Directory | Wprowadź identyfikator aplikacji do rejestracji aplikacji testowej (identyfikator klienta). | Wprowadź identyfikator aplikacji do rejestracji aplikacji produkcyjnej. |
||||

## <a name="plan-overview-page"></a>Strona przegląd planu

Podczas tworzenia planów zalecamy użycie tego samego _identyfikatora planu_ i _nazwy planu_ w ofertach dev i prod, z wyjątkiem dołączenia identyfikatora planu do oferty dev w **_test**. Na przykład jeśli identyfikator planu w ofercie PRODUKCYJNEj to "Enterprise", identyfikator planu w ofercie DEV powinien mieć wartość "enterprise_test". W ten sposób można łatwo określić, którą ofertę DEV z oferty PRODUKCYJNEj. Utworzysz plany w ofercie PRODUKCYJNEj z modelami cenowymi i cenami, które użytkownik zdecyduje najlepiej dla oferty.

### <a name="plan-listing"></a>Zaplanuj listę

Na karcie **Planowanie przeglądu planu**  >   wprowadź ten sam opis planu w planach dev i prod.

### <a name="pricing-and-availability-page"></a>Strona cennika i dostępność

Ta sekcja zawiera wskazówki dotyczące uzupełniania   >  **cen i dostępności** przeglądu planu.

#### <a name="markets"></a>Wprowadza

Wybierz te same rynki dla ofert DEV i PROD.

#### <a name="pricing"></a>Ceny

Skorzystaj z oferty DEV, aby eksperymentować z modelami cenowymi. Po sprawdzeniu, który model cen lub modele najlepiej sprawdza się, utworzysz plany w ofercie PRODUKCYJNEj z modelami cenowymi i cenami, które chcesz.

Oferta DEV powinna mieć plany z zerowymi lub niskimi cenami w planach. Oferta PRODUKCYJNa będzie miała ceny, które mają być naliczane klientom.

> [!IMPORTANT]
> Zakupy dokonywane w wersji zapoznawczej zostaną przetworzone dla ofert DEWELOPERSKIch i PROD. Jeśli oferta ma cenę $100/miesiąc, opłata zostanie naliczona na $100. Jeśli tak się stanie, możesz otworzyć [bilet pomocy technicznej](support.md) i wystawić wypłatę za pełną kwotę (bez opłaty za usługę magazynu).

#### <a name="pricing-model"></a>Model cen

Użyj tego samego modelu cen w planach ofert DEWELOPERSKIch i PROD. Na przykład jeśli plan w ofercie PRODUKCYJNEj ma stawkę ryczałtową, miesięczny okres rozliczeniowy, skonfiguruj plan w ofercie DEV przy użyciu tego samego modelu.

Aby zmniejszyć koszty testowania modeli cenowych, w tym wymiarów mierników niestandardowych dla portalu Marketplace, zalecamy skonfigurowanie sekcji **cennika** na karcie **ceny i dostępność** w ofercie dev z niższymi cenami niż Oferta produkcyjna. Poniżej przedstawiono niektóre wskazówki, które można wykonać podczas ustawiania cen dla planów w ofercie DEV.

***Tabela 2: wytyczne dotyczące cen***

| Cena | Komentarz |
| ------------ | ------------- |
| $0,00 | Ustaw łączny koszt transakcji równy zero, aby nie mieć wpływu finansowego. Użyj tej ceny podczas wykonywania wywołań do interfejsów API pomiarów lub testowania planów zakupu w ofercie podczas opracowywania rozwiązania. |
| $0,01 – $49,99 | Użyj tego zakresu cen do testowania analiz, raportowania i procesu zakupu. |
| $50,00 i więcej | Użyj tego zakresu cen do testowania wypłaty. Aby uzyskać informacje na temat naszego harmonogramu płatności, zobacz [harmonogramy i procesy wypłaty](/partner-center/payout-policy-details). |
|||

Aby uniknąć naliczania opłat za usługę w sklepie na test, Otwórz [bilet pomocy technicznej](support.md).

#### <a name="free-trial"></a>Bezpłatna wersja próbna

Nie włączaj bezpłatnej wersji próbnej oferty DEV.

## <a name="co-sell-with-microsoft-page"></a>Sprzedawanie ze stroną firmy Microsoft

Nie należy konfigurować karty **współsprzedaży z firmą Microsoft** w ramach oferty dev.

## <a name="resell-through-csps"></a>Odsprzedaż za pośrednictwem dostawców CSP

Nie należy konfigurować karty **odsprzedaży za pomocą dostawcy CSP** oferty dev.

## <a name="next-steps"></a>Następne kroki

- [Testowanie i publikowanie oferty SaaS](test-publish-saas-offer.md)
