---
title: Publikowanie oferty aplikacji Power BI | Portal Azure Marketplace
description: Opublikuj ofertę aplikacji Power BI w witrynie Microsoft AppSource Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: eb31520c81a4064edbe54a0256b694c4ad88fb49
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82141712"
---
# <a name="publish-a-power-bi-app-offer"></a>Publikowanie oferty aplikacji Power BI

>[!Important]
>Od 13 kwietnia 2020 rozpocznie się zarządzanie ofertami Power BI aplikacji do Centrum partnerskiego. Po przeprowadzeniu migracji utworzysz oferty w centrum partnerskim i zarządzaj nimi. Postępuj zgodnie z instrukcjami w temacie [Power BI — omówienie tworzenia aplikacji](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-power-bi-app-offer) , aby zarządzać migrowanymi ofertami.

Ostatnim krokiem po zdefiniowaniu oferty w portal Cloud Partner i utworzeniu skojarzonych zasobów technicznych jest przesłanie oferty do opublikowania. Aby rozpocząć ten proces, w lewym okienku okna **Nowa oferta** wybierz pozycję **Publikuj**. Aby uzyskać więcej informacji, zobacz temat [Publikowanie ofert w portalu Azure Marketplace i AppSource](../manage-offers/cpp-publish-offer.md).


## <a name="publishing-steps"></a>Kroki publikowania

Oto główne kroki w procesie publikowania:

![Etapy procesu publikowania dla oferty aplikacji Power BI](./media/publishing-process-steps.png)

W tej tabeli opisano każdy krok i podano szacowany czas ukończenia:

|   Etap publikowania            |   Time     |   Opis                                                                  |
| --------------------         |------------| ----------------                                                               |
| Sprawdzanie wymagań wstępnych       | 15 minut     | Informacje o ofercie i ustawienia oferty są weryfikowane.                            |
| Certyfikacja                | 1-7 dni   | Zespół certyfikacji Power BI analizuje swoją ofertę. Zespół uruchamia aplikację Power BI za pośrednictwem testu ręcznego weryfikacji przez zainstalowanie aplikacji za pomocą podanego adresu URL instalacji. Podstawowe walidacje są wykonywane w ramach procesu certyfikacji aplikacji (opisanego w dalszej części tego dokumentu).         |
| Tworzenie pakietów                    | \<1 godzina  | Zasoby techniczne oferty są pakowane do użytku przez klienta.                        |
| Rejestracja generacji potencjalnego klienta | \<1 godzina  | Systemy liderów zostały skonfigurowane i wdrożone.                                      |
| Wydawca przygotowania            | \-         | Zakończenie przeglądu końcowego i potwierdzenia przed rozpoczęciem tej oferty. Teraz masz również link umożliwiający wyświetlenie podglądu oferty. Gdy zobaczysz, jak wygląda Podgląd, wybierz pozycję **Przejdź na żywo** na karcie **stan** . Spowoduje to wysłanie żądania do zespołu dołączania w celu wyświetlenia listy aplikacji w witrynie AppSource.    |
| Na żywo                         | \<3 godziny | Twoja oferta jest teraz publicznie wyświetlana na liście ("Live") w witrynie AppSource, a klienci mogą wyświetlać swoją aplikację i wdrażać ją w ramach subskrypcji Power BI. Otrzymasz również wiadomość e-mail z potwierdzeniem. W prawej kolumnie na karcie **wszystkie oferty** możesz zobaczyć stan wszystkich ofert. Na karcie **stan** można zobaczyć szczegółowy stan przepływu publikacji dla swojej oferty. |
|   |   |

Ukończenie tego procesu może potrwać do ośmiu dni. Po przeprowadzeniu tych kroków publikowania w sekcji [AppSource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20) Power BI Apps zostanie wyświetlona oferta aplikacji Power BI.


### <a name="app-certification-process"></a>Proces certyfikacji aplikacji

Zespół ds. dołączania firmy Microsoft używa tego procesu do weryfikacji oferty Power BI aplikacji:

1. Przejrzyj dokumenty prawne i linki pomocy.
2. Sprawdź poprawność informacji kontaktowych pomocy technicznej.
3. Użyj adresu URL Instalatora, aby sprawdzić poprawność instalacji.
4. Skanuj aplikację pod kątem złośliwego oprogramowania i innej złośliwej zawartości.
5. Sprawdź, czy wyświetlana zawartość jest zgodna z opisem aplikacji.
6. Sprawdź, czy operacje związane z aplikacją działają zgodnie z oczekiwaniami w Power BI. Zespół otwiera raporty i pulpity nawigacyjne z przykładowymi danymi, nawiązuje połączenie z niestandardowymi źródłami danych, odświeża dane i tak dalej.

Zespół certyfikacji dostarcza informacje zwrotne o problemach.  Więcej informacji o wymaganiach dotyczących aplikacji Power BI można znaleźć w [dokumentacji aplikacji Power BI](https://go.microsoft.com/fwlink/?linkid=2028636).


## <a name="next-steps"></a>Następne kroki

Zalecamy regularne monitorowanie aplikacji w [witrynie AppSource Marketplace](https://appsource.microsoft.com).  W celu uzyskania wglądu w informacje o klientach portalu Marketplace i użyciu aplikacji należy również użyć funkcji [Informacje o sprzedawcy](../../cloud-partner-portal-orig/si-getting-started.md) [Portal Cloud partner](https://cloudpartner.azure.com/#insights) . Na koniec możesz [zaktualizować swoją ofertę](./cpp-update-existing-offer.md).
