---
title: Często zadawane pytania dotyczące migracji do Centrum partnerskiego | Portal Azure Marketplace
description: W tym artykule opisano często zadawane pytania dotyczące migracji ofert z usługi portal Cloud Partner do Centrum partnerskiego.
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: mingshen
ms.openlocfilehash: 672153eba4aa2b739b67694f939c4796b39ac4c6
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/05/2020
ms.locfileid: "81274382"
---
# <a name="frequently-asked-questions-for-migrating-from-the-cloud-partner-portal-to-partner-center"></a>Często zadawane pytania dotyczące migracji z portal Cloud Partner do Centrum partnerskiego

W tym artykule opisano często zadawane pytania dotyczące migracji ofert z portal Cloud Partner do Centrum partnerskiego.

## <a name="what-does-offer-migration-mean"></a>Jak ma znaczenie migracja oferty?

Przenosimy dane oferty z portal Cloud Partner do Centrum partnerskiego ze zmianami w środowisku publikowania i zarządzania ofertą.

| Obszar  | Zmiany  |
|-------|----------|
| **Publikowanie i oferowanie środowiska zarządzania** | Będziesz mieć Ulepszone środowisko użytkownika dzięki intuicyjnemu interfejsowi w centrum partnerskim. Aby uzyskać więcej informacji, zobacz [jakie są różnice między centrum partnerskim a Portal Cloud partner?](#what-are-the-differences-between-partner-center-and-the-cloud-partner-portal) |
| **Dostępność ofert w portalu Marketplace** | Brak zmian. Jeśli Twoja oferta znajduje się na żywo w portalu Marketplace, będzie ona nadal aktywna podczas i po zakończeniu migracji. |
| **Nowe zakupy i wdrożenia** | Brak zmian. Klienci będą nadal mogli kupować i wdrażać oferty bez przerw w działaniu. |
| **Wypłaty** | Wszelkie zakupy i wdrożenia, które wystąpiły w trakcie lub po migracji, będą nadal wynagradzane jako normalne. |
|**Integracje interfejsów API z istniejącymi [interfejsami api Portal Cloud partner](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)** | Istniejące portal Cloud Partner interfejsy API będą nadal obsługiwane po migracji i istniejącej integracji będą nadal działać. Aby uzyskać więcej informacji, zobacz [Portal Cloud partner interfejsów API REST będą obsługiwane po migracji?](#will-the-cloud-partner-portal-rest-apis-be-supported-post-migration) |
| | |

## <a name="can-i-still-access-the-cloud-partner-portal-and-manage-my-offers-during-migration"></a>Czy nadal mogę uzyskać dostęp do portal Cloud Partner i zarządzać moimi ofertami podczas migracji?

Oferty zostaną zmigrowane do Centrum partnerskiego od 13 kwietnia w ciągu miesiąca z maja. W tym okresie będzie możliwe uzyskanie dostępu do portal Cloud Partner w zwykły sposób, z wyjątkiem sytuacji, w której planowana jest migracja.
Podczas migrowania ofert będzie mieć stan "Zablokowano — przechodzenie do Centrum partnerskiego", jak pokazano na poniższym zrzucie ekranu. Okres migracji powinien być krótszy niż 24 godziny. W tym czasie nie będzie można wprowadzać żadnych aktualizacji do ofert. Po zakończeniu migracji ofert otrzymasz powiadomienie e-mail.

:::image type="content" source="media/migration-faq/all-offers-1.png" alt-text="Ilustruje stan migrowanych ofert.":::

Po przeprowadzeniu migracji ofert będą one w trybie tylko **do odczytu przez ograniczony czas** w Portal Cloud partner. Ich stan zmieni się na "przeniesiono do Centrum partnerskiego" i Dołącz link do oferty w centrum partnerskim, jak pokazano na poniższych zrzutach ekranu. Od tego momentu będziesz zarządzać aktualizacjami dla wszystkich ofert lub tworzyć nowe oferty wyłącznie za pośrednictwem Centrum partnerskiego,

:::image type="content" source="media/migration-faq/all-offers-2.png" alt-text="Przedstawia komunikat dotyczący ofert, które zostały poddane migracji do Centrum partnerskiego":::

:::image type="content" source="media/migration-faq/offer-has-moved.png" alt-text="Ilustruje stronę portal Cloud Partner dla zmigrowanej oferty.":::

## <a name="how-will-i-create-new-offers"></a>Jak można utworzyć nowe oferty?

W portal Cloud Partner nastąpi przekierowanie do tworzenia nowych ofert w centrum partnerskim

:::image type="content" source="media/migration-faq/create-new-offer-1.png" alt-text="Ilustruje menu, aby utworzyć nową ofertę w portal Cloud Partner":::

Po wybraniu nowej oferty zobaczysz komunikat, taki jak poniższe.

:::image type="content" source="media/migration-faq/create-new-offer-2.png" alt-text="Ilustruje komunikat otrzymany podczas tworzenia nowej oferty w programie CPP":::

## <a name="do-i-need-to-create-a-new-account-to-manage-offers-in-partner-center"></a>Czy muszę utworzyć nowe konto, aby zarządzać ofertami w centrum partnerskim?

Nie. Twoje podstawowe konto zostanie zachowane i będzie ono już zarządzane w centrum partnerskim. Oznacza to, że jeśli jesteś istniejącym partnerem, możesz użyć istniejących poświadczeń konta portal Cloud Partner, aby zalogować się w centrum partnerskim po migracji. Tylko oferty i skojarzone środowisko zarządzania są przenoszone z portal Cloud Partner do Centrum partnerskiego. Prosimy o to, aby nie tworzyć żadnych nowych kont, ponieważ oferty nie będą skojarzone z nowym kontem.

## <a name="i-see-a-message-in-the-cloud-partner-portal-to-activate-my-account-what-does-this-mean"></a>Zobaczysz komunikat w portal Cloud Partner, aby aktywować moje konto. co to znaczy?

Potrzebujemy kilku dodatkowych informacji, aby prawidłowo migrować Twoje konto do Centrum partnerskiego i umożliwić zarządzanie ofertami w centrum partnerskim po zakończeniu migracji oferty. Aby uzyskać więcej informacji na temat aktywacji konta, zobacz [Migracja konta z Portal Cloud partner do Centrum partnerskiego](https://docs.microsoft.com/azure/marketplace/partner-center-portal/account-migration-from-cpp-to-pc).

Kroki wymagane do ukończenia aktywacji konta różnią się w zależności od roli konta.

| Rola konta | Kroki aktywacji |
|--------------|----------------|
|Właściciel | Przejdź do strony [profil wydawcy](https://cloudpartner.azure.com/#profile) w Portal Cloud partner a następnie kliknij link na banerze, aby aktywować. Nastąpi przekierowanie do Centrum partnerskiego, aby ukończyć aktywację konta. |
| Współautor | Tylko użytkownik na koncie z rolą właściciela może aktywować konto. Skontaktuj się z właścicielami konta, aby ukończyć aktywację konta. Właściciele Twojego konta powinni być wyświetlani w wiadomości transparentu. |
| | |

## <a name="im-having-trouble-logging-in-to-my-account-and-opening-a-support-ticket"></a>Mam problem z zalogowaniem się do mojego konta i otwarciem biletu pomocy technicznej

Jeśli nie możesz zalogować się do konta, możesz otworzyć [bilet pomocy technicznej](https://partner.microsoft.com/support/v2/?stage=1).

## <a name="where-can-i-find-documentation-on-the-new-partner-center-publishing-experience"></a>Gdzie mogę znaleźć dokumentację dotyczącą nowego środowiska publikowania w centrum partnerskim?

Przejdź do [dokumentacji komercyjnej portalu Marketplace](https://docs.microsoft.com/azure/marketplace/). Następnie rozwiń pozycję  >  **komercyjny Portal Marketplace w centrum partnerskim****Utwórz nową ofertę** , aby wyświetlić tematy pomocy dotyczące tworzenia poszczególnych typów ofert.

:::image type="content" source="media/migration-faq/marketplace-help-topics.png" alt-text="Ilustruje tematy pomocy dla Centrum partnerskiego":::

### <a name="what-are-the-differences-between-partner-center-and-the-cloud-partner-portal"></a>Czym różnią się centrum partnerskie i portal Cloud Partner?

Poniższe różnice między portal Cloud Partner i centrum partnerskim mogą być zauważalne.

### <a name="modular-publishing-capabilities"></a>Modularne możliwości publikowania

Centrum partnerskie udostępnia opcję publikowania modularnego, która pozwala wybrać zmiany, które mają zostać opublikowane, zamiast zawsze publikować wszystkie aktualizacje jednocześnie. Na przykład poniższy zrzut ekranu pokazuje, że jedyne zmiany wybrane do opublikowania to zmiany **Właściwości** i **oferty**.

:::image type="content" source="media/migration-faq/review-and-publish-migration.png" alt-text="Ilustruje stronę podglądu i publikowania":::

Aktualizacje, które nie są publikowane, są zapisywane jako wersje robocze. Kontynuuj korzystanie z wersji zapoznawczej oferty w celu zweryfikowania oferty przed jej udostępnieniem publicznie.

### <a name="rich-text-format"></a>Format tekstu sformatowanego

Polepszanie oferty i opisu planu przy użyciu edytora tekstu sformatowanego na stronie aukcji oferty i planu.

:::image type="content" source="media/migration-faq/rich-text-editor-migration.png" alt-text="Ilustruje Edytor tekstu sformatowanego":::

### <a name="enhanced-preview-options"></a>Rozszerzone opcje podglądu

Centrum partnerskie zawiera [funkcję Compare](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#compare-changes-to-marketplace-offers) z ulepszonymi opcjami filtrowania. Dzięki temu można porównać z wersjami zapoznawczymi i na żywo oferty.

:::image type="content" source="media/migration-faq/enhanced-preview.png" alt-text="Ilustruje funkcję porównania":::

### <a name="branding-and-navigation-changes"></a>Zmiany znakowania i nawigacji

Zauważysz pewne zmiany znakowania. Na przykład "jednostki SKU" są oznaczone jako "plany" w centrum partnerskim.

:::image type="content" source="media/migration-faq/plan-overview-migration.png" alt-text="Ilustruje przegląd planu.":::

Ponadto informacje, które zostały użyte do udostępnienia w **witrynie Marketplace** lub**Torefront szczegóły** (usługa doradcza, aplikacja Power BI) w Portal Cloud partner, są zbierane na stronie z **listą ofert** w centrum partnerskim.

:::image type="content" source="media/migration-faq/offer-listing-migration.png" alt-text="Ilustruje stronę z listą ofert.":::

Informacje używane do udostępniania dla jednostek SKU na jednej stronie portal Cloud Partner mogą być teraz zbierane na kilku stronach w centrum partnerskim:

* Strona konfigurowania planu
* Strona aukcji planu
* Zaplanuj stronę dostępności
* Zaplanuj stronę konfiguracji technicznej, jak pokazano na tym zrzucie ekranu.

:::image type="content" source="media/migration-faq/tech-config-migration.png" alt-text="Ilustruje stronę konfiguracja techniczna planu.":::

Identyfikator oferty jest teraz pokazywany na pasku nawigacyjnym po lewej stronie oferty.

:::image type="content" source="media/migration-faq/offer-id-offer-overview.png" alt-text="Przedstawia menu nawigacji po lewej stronie z IDENTYFIKATORem oferty.":::

### <a name="stop-selling-an-offer"></a>Zatrzymaj sprzedawanie oferty

Możesz poprosić o [zaprzestanie sprzedaży oferty](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan) w witrynie Marketplace bezpośrednio z poziomu portalu Centrum partnerskiego. Ta opcja jest dostępna na stronie **Przegląd oferty** oferty.

:::image type="content" source="media/migration-faq/stop-selling-migration.png" alt-text="Przedstawia stronę przeglądu oferty z opcją Zatrzymaj sprzedawanie.":::

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>Jakie strony w centrum partnerskim odpowiadają stronom używanym w portal Cloud Partner?

W poniższej tabeli przedstawiono odpowiednie linki między tymi dwoma portalami.

| Strona | portal Cloud Partner łącze | Link Centrum partnerskiego |
|------|---------------------------|---------------------|
| **Strona ze wszystkimi ofertami** | https://cloudpartner.azure.com/#alloffers | https://partner.microsoft.com/dashboard/commercial-marketplace/overview |
| **Strona wszystkich wydawców** | https://cloudpartner.azure.com/#publishers | https://partner.microsoft.com/dashboard/account/v3/publishers/list |
| **Profil wydawcy** | https://cloudpartner.azure.com/#profile | https://partner.microsoft.com/dashboard/account/management |
| **Strona użytkowników** | https://cloudpartner.azure.com/#users | https://partner.microsoft.com/dashboard/account/usermanagement |
| **Strona historia** | https://cloudpartner.azure.com/#history | Funkcja History nie jest jeszcze obsługiwana w centrum partnerskim. |
| **Pulpit nawigacyjny usługi Insights** | https://cloudpartner.azure.com/#insights | https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary |
| **Raport wypłaty** | https://cloudpartner.azure.com/#insights/payout | https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| | |

## <a name="will-the-cloud-partner-portal-rest-apis-be-supported-post-migration"></a>Czy portal Cloud Partner interfejsy API REST będą obsługiwane po migracji?

Interfejsy API portal Cloud Partner są zintegrowane z centrum partnerskim i będą nadal działały po przeprowadzeniu migracji ofert do Centrum partnerskiego. W ramach integracji wprowadzono niewielkie zmiany. Przejrzyj zmiany w poniższej tabeli, aby upewnić się, że kod nadal działa po zakończeniu migracji do Centrum partnerskiego.

| **Interfejs API** | **Zmień opis** | **Wpływ** |
| ------- | ---------------------- | ---------- |
| Publikuj publikowanie, GoLive, Anuluj | W przypadku zmigrowanych ofert nagłówek odpowiedzi będzie miał inny format, ale będzie nadal działał w taki sam sposób, co oznacza ścieżkę względną do pobrania stanu operacji. | Podczas wysyłania dowolnego z odpowiednich żądań POST dla oferty nagłówek lokalizacji będzie miał jeden z dwóch formatów w zależności od stanu migracji oferty:<ul><li>Oferty niemigrowane<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Zmigrowane oferty<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| Pobierz operację | W przypadku ofert, które wcześniej obsługiwały pole "notification-email" w odpowiedzi, to pole będzie przestarzałe i nie będzie już zwracane dla zmigrowanych ofert. | W przypadku zmigrowanych ofert nie będą już wysyłane powiadomienia do listy wiadomości e-mail określonych w żądaniach. Zamiast tego usługa API zostanie wyrównuje z procesem powiadomień e-mail w centrum partnerskim w celu wysyłania wiadomości e-mail. W każdym przypadku powiadomienia będą wysyłane na adres e-mail ustawiony w sekcji informacje kontaktowe sprzedawcy ustawień konta w centrum partnerskim w celu powiadomienia o postępie operacji.<br><br>Przejrzyj adres e-mail określony w sekcji informacje kontaktowe sprzedawcy w obszarze [Ustawienia konta](https://partner.microsoft.com/dashboard/account/management) w centrum partnerskim, aby upewnić się, że dla powiadomień została podana poprawna wiadomość e-mail.  |
| | | |
