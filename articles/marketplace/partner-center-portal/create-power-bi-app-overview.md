---
title: Omówienie tworzenia aplikacji usługi Power BI — witryna Azure Marketplace
description: W tym artykule opisano kroki wysokiego poziomu publikowania aplikacji usługi Power BI w witrynie Microsoft AppSource. Dostępne są również wymagania techniczne i biznesowe, które musi spełnić aplikacja Usługi Power BI, aby zostać opublikowane w komercyjnym rynku.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: 8f050a43cbdf4ab29df55cd5526eb231c301b271
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732676"
---
# <a name="power-bi-app-creation-overview"></a>Omówienie tworzenia aplikacji usługi Power BI

> [!IMPORTANT]
> Przenosimy zarządzanie ofertami aplikacji Usługi Power BI z portalu Cloud Partner do Centrum partnerów. Dopóki oferty nie zostaną zmigrowane, postępuj zgodnie z instrukcjami [w ofercie aplikacji Power BI](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/power-bi/cpp-power-bi-offer) dla portalu Cloud Partner Portal, aby zarządzać ofertami.

W tym artykule wyjaśniono, jak opublikować aplikację usługi Power BI w witrynie Microsoft [AppSource](https://appsource.microsoft.com/). Aplikacja Power BI pakuje konfigurowalną zawartość, w tym zestawy danych, raporty i pulpity nawigacyjne. Następnie można używać aplikacji z innymi platformami usługi Power BI przy użyciu usługi AppSource, wykonywać dostosowania i dostosowania dozwolone przez dewelopera i łączyć je z własnymi danymi.

## <a name="publishing-benefits"></a>Korzyści związane z publikowaniem

Korzyści z publikowania na rynku komercyjnym:

- Promuj swoją firmę, korzystając z marki Microsoft.
- Potencjalnie dotrzeć do ponad 100 milionów użytkowników usługi Office 365 i Dynamics 365 w usłudze AppSource i ponad 200 000 organizacji za pośrednictwem portalu Azure Marketplace.
- Otrzymuj wysokiej jakości potencjalnych klientów z tych rynków.
- Niech twoje usługi będą promowane przez zespoły firmy Microsoft i telesprzedaży.

## <a name="overview"></a>Omówienie

:::image type="content" source="media/power-bi-app-publishing-steps.png" alt-text="omówienie kroków publikowania aplikacji usługi Power BI" border="false":::

Oto kluczowe kroki publikowania:

1. Tworzenie aplikacji w usłudze Power BI. Otrzymasz link do instalacji pakietu, który jest głównym zasobem technicznym oferty. Wyślij pakiet testowy do przedprodukcji przed utworzeniem oferty w Centrum partnerów. Aby uzyskać szczegółowe informacje, zobacz [Co to są aplikacje usługi Power BI?](https://docs.microsoft.com/power-bi/service-template-apps-overview).
2. Dodaj materiały marketingowe, takie jak oficjalna nazwa, opis i logo.
3. Dołącz dokumenty prawne i pomocnicze oferty, takie jak warunki użytkowania, polityka prywatności, zasady pomocy technicznej i pomoc użytkownika.
4. Utwórz ofertę: Użyj Centrum partnerskiego, aby edytować szczegóły, w tym opis oferty, materiały marketingowe, informacje prawne, informacje o pomocy technicznej i specyfikacje zasobów.
5. Prześlij go do publikacji.
6. Monitoruj proces w Centrum partnerów, w którym zespół dołączania appsource testuje, sprawdza poprawność i certyfikuje aplikację.
7. Po uzyskaniu certyfikatu przejrzyj aplikację w jej środowisku testowym i zwolnij ją. Spowoduje to wyświetlenia go w u źródła aplikacji AppSource (zostanie ono włączone).
8. W usłudze Power BI wyślij pakiet do produkcji. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie wydaniem aplikacji usługi Power BI](https://docs.microsoft.com/power-bi/service-template-apps-create#manage-the-template-app-release).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przejrzyj poniższe łącza, które zawierają szablony, porady i przykłady.

- [Tworzenie aplikacji usługi Power BI](https://docs.microsoft.com/power-bi/service-template-apps-create)
- [Wskazówki dotyczące tworzenia aplikacji usługi Power BI](https://docs.microsoft.com/power-bi/service-template-apps-tips)
- [Samples](https://docs.microsoft.com/power-bi/service-template-apps-samples)

## <a name="requirements"></a>Wymagania

Aby oferta aplikacji Usługi Power BI została opublikowana w komercyjnym rynku, musi spełniać następujące wymagania techniczne i biznesowe.

### <a name="technical-requirements"></a>Wymagania techniczne

Głównym zasobem technicznym, którego potrzebujesz, jest [aplikacja Power BI.](https://go.microsoft.com/fwlink/?linkid=2028636) Jest to zbiór podstawowych zestawów danych, raportów lub pulpitów nawigacyjnych. Zawiera również opcjonalne połączone usługi i osadzone zestawy danych, wcześniej znane jako [pakiet zawartości](https://docs.microsoft.com/power-bi/service-organizational-content-pack-introduction). Aby uzyskać więcej informacji na temat tworzenia tego typu aplikacji, zobacz [Co to są aplikacje usługi Power BI?](https://go.microsoft.com/fwlink/?linkid=2028636).

#### <a name="get-an-installation-web-address"></a>Uzyskaj adres internetowy instalacji

Aplikację usługi Power BI można tworzyć tylko w środowisku [usługi Power BI.](https://powerbi.microsoft.com/)

1. Zaloguj się przy za pomocą [licencji usługi Power BI Pro](https://docs.microsoft.com/power-bi/service-admin-purchasing-power-bi-pro).
2. Tworzenie i testowanie aplikacji w usłudze Power BI.
3. Po otrzymaniu adresu internetowego instalacji aplikacji dodaj go do strony **Konfiguracja techniczna** w Centrum partnerów.

Po utworzeniu i przetestowaniu aplikacji w usłudze Power BI zapisz adres internetowy instalacji aplikacji, ponieważ jest on potrzebny do [utworzenia oferty aplikacji usługi Power BI](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-power-bi-app-offer).

### <a name="business-requirements"></a>Wymagania biznesowe

Wymogi biznesowe obejmują zobowiązania proceduralne, umowne i prawne. Musisz:

- Bądź zarejestrowanym wydawcą rynku komercyjnego. Jeśli nie jesteś zarejestrowany, wykonaj czynności opisane w programie [Become a Commercial Marketplace Publisher](https://docs.microsoft.com/azure/marketplace/become-publisher).
- Podaj zawartość, która spełnia kryteria, aby twoja oferta była wyświetlana w u źródła usług AppSource. Aby uzyskać więcej informacji, zobacz [Czy aplikacja do wyświetlenia w u źródła aplikacji? Oto jak](https://appsource.microsoft.com/blogs/have-an-app-to-list-on-appsource-here-s-how).
- Zgadzam się na [naruszenie zasad zachowania poufności informacji firmy Microsoft.](https://privacy.microsoft.com/privacystatement)

## <a name="next-steps"></a>Następne kroki

- [Tworzenie oferty aplikacji usługi Power BI w Centrum partnerskim](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-power-bi-app-offer)