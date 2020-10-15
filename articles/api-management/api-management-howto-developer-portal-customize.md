---
title: Dostęp do zarządzanego portalu deweloperów — API Management platformy Azure | Microsoft Docs
description: Dowiedz się, jak używać zarządzanej wersji portalu dla deweloperów w API Management.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/05/2020
ms.author: apimpm
ms.openlocfilehash: 3ceb8fd154e8ad533f4bf6bc9eb0ec3900749f8b
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92075369"
---
# <a name="access-and-customize-developer-portal"></a>Dostęp i dostosowywanie portalu dla deweloperów

Portal dla deweloperów to automatycznie generowana, w pełni dostosowywalna witryna sieci Web z dokumentacją interfejsów API. W przypadku, gdy konsumenci interfejsu API mogą odnajdywać interfejsy API, dowiedzieć się, jak ich używać, i zażądać dostępu.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Dostęp do zarządzanej wersji portalu dla deweloperów
> * Nawigowanie po interfejsie administracyjnym
> * Dostosowywanie zawartości
> * Publikowanie zmian
> * Wyświetlanie opublikowanego portalu

Więcej szczegółowych informacji można znaleźć w portalu dla deweloperów w [portalu deweloperów API Management platformy Azure](api-management-howto-developer-portal.md).

![Portal dla deweloperów API Management — tryb administratora](media/api-management-howto-developer-portal-customize/cover.png)

## <a name="prerequisites"></a>Wymagania wstępne

- Wykonaj procedury przedstawione w następującym przewodniku Szybki start: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md)
- Importowanie i publikowanie wystąpienia usługi Azure API Management. Aby uzyskać więcej informacji, zobacz [Importowanie i publikowanie](import-and-publish.md)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-the-portal-as-an-administrator"></a>Dostęp do portalu jako administrator

Wykonaj poniższe kroki, aby uzyskać dostęp do zarządzanej wersji portalu.

1. Przejdź do wystąpienia usługi API Management w Azure Portal.
1. Kliknij przycisk **Portal dla deweloperów** na górnym pasku nawigacyjnym. Zostanie otwarta nowa karta przeglądarki z wersją administracyjną portalu.

## <a name="understand-the-portals-administrative-interface"></a>Informacje o interfejsie administracyjnym portalu

### <a name="default-content"></a>Zawartość domyślna 

Jeśli po raz pierwszy uzyskujesz dostęp do portalu, domyślna zawartość zostanie automatycznie zainicjowana w tle. Zawartość domyślna została zaprojektowana w celu pokazania możliwości portalu i zminimalizowania liczby dostosowań potrzebnych do spersonalizowania portalu. Więcej informacji o tym, co obejmuje zawartość portalu, można znaleźć w temacie [Omówienie portalu deweloperów w usłudze Azure API Management](api-management-howto-developer-portal.md).

### <a name="visual-editor"></a>Edytor wizualny

Zawartość portalu można dostosować za pomocą edytora wizualnego. Sekcje menu po lewej stronie umożliwiają tworzenie lub modyfikowanie stron, multimediów, układów, menu, stylów lub ustawień witryny sieci Web. Elementy menu u dołu pozwalają przełączać się między okienkami ekranu (na przykład Mobile lub Desktop), wyświetlać elementy portalu widoczne dla uwierzytelnionych lub anonimowych użytkowników albo zapisywać lub cofać akcje.

Aby dodać wiersze do strony, kliknij niebieską ikonę ze znakiem plus. Widżety (na przykład tekst, obrazy lub lista interfejsów API) można dodać, naciskając szarą ikonę ze znakiem plus. Można zmienić rozmieszczenie elementów na stronie za pomocą interakcji przeciągnij i upuść. 

### <a name="layouts-and-pages"></a>Układy i strony

![Strony i układy](media/api-management-howto-developer-portal-customize/pages-layouts.png)

Układy definiują sposób wyświetlania stron. Na przykład w zawartości domyślnej istnieją dwa układy — jeden ma zastosowanie do strony głównej, a drugi do wszystkich pozostałych stron.

Układ zostanie zastosowany do strony, dopasowując jej szablon adresu URL do adresu URL strony. Na przykład układ z szablonem adresu URL programu `/wiki/*` zostanie zastosowany do każdej strony z `/wiki/` segmentem w adresie URL: `/wiki/getting-started` , `/wiki/styles` itp.

Na powyższym obrazie zawartość należące do układu jest oznaczona kolorem niebieskim, podczas gdy strona jest oznaczona kolorem czerwonym. Sekcje menu są odpowiednio oznaczone.

### <a name="styling-guide"></a>Przewodnik po stylu

![Przewodnik po stylu](media/api-management-howto-developer-portal-customize/styling-guide.png)

Przewodnik po stylu jest panelem utworzonym z projektantami. Pozwala na wyświetlanie i Ustawianie stylu wszystkich elementów wizualizacji w portalu. Style są hierarchiczne — wiele elementów dziedziczy właściwości z innych elementów. Na przykład elementy Button używają kolorów dla tekstu i tła. Aby zmienić kolor przycisku, należy zmienić oryginalny wariant koloru.

Aby edytować wariant, kliknij go i wybierz ikonę ołówka, która pojawia się na górze. Po wprowadzeniu zmian w oknie podręcznym zamknij je.

### <a name="save-button"></a>Przycisk Zapisz

![Przycisk Zapisz](media/api-management-howto-developer-portal-customize/save-button.png)

Za każdym razem, gdy wprowadzisz zmiany w portalu, musisz zapisać je ręcznie, naciskając przycisk **Zapisz** w menu u dołu. Po zapisaniu zmian zmodyfikowana zawartość jest automatycznie przekazywana do usługi API Management.

## <a name="customize-the-portals-content"></a>Dostosuj zawartość portalu

Przed udostępnieniem portalu Gościom należy spersonalizować automatycznie wygenerowaną zawartość. Zalecane zmiany obejmują układy, style i zawartość strony głównej.

> [!NOTE]
> Ze względu na kwestie związane z integracją nie można usunąć ani przenieść następujących stron pod innym adresem URL:,,,,,,,,,,, `/404` `/500` `/captcha` `/change-password` `/config.json` `/confirm/invitation` `/confirm-v2/identities/basic/signup` `/confirm-v2/password` `/internal-status-0123456789abcdef` `/publish` `/signin` `/signin-sso` `/signup` .

### <a name="home-page"></a>Strona główna

Domyślna strona **główna** jest wypełniana zawartością fikcyjną. Można usunąć całe sekcje z zawartością lub zachować strukturę i dostosować elementy po jednym. Zamień wygenerowany tekst i obrazy na własne i upewnij się, że linki wskazują wybrane lokalizacje.

### <a name="layouts"></a>Układy

Zastąp automatycznie wygenerowany logo na pasku nawigacyjnym własnym obrazem.

### <a name="styling"></a>Style

Chociaż nie musisz dostosowywać żadnych stylów, możesz rozważyć dostosowanie poszczególnych elementów. Na przykład zmień kolor podstawowy, aby pasował do koloru marki.

### <a name="customization-example"></a>Przykład dostosowania

W poniższym filmie wideo pokazano, jak edytować zawartość portalu, dostosować wygląd witryny sieci Web i opublikować zmiany.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="publish-the-portal"></a><a name="publish"> </a>Publikowanie portalu

Aby Portal i najnowsze zmiany były dostępne dla odwiedzających, należy je opublikować.

1. Upewnij się, że Zapisano zmiany, klikając ikonę **Zapisz** .
1. Kliknij pozycję **Publikuj witrynę sieci Web** w sekcji **operacje** menu. Ta operacja może potrwać kilka minut.  
    ![Publikowanie portalu](media/api-management-howto-developer-portal-customize/publish-portal.png)

> [!NOTE]
> Portal należy ponownie opublikować po API Management zmian konfiguracji usługi, takich jak przypisywanie domeny niestandardowej, aktualizowanie dostawców tożsamości, Ustawianie delegowania, określanie warunków logowania i postanowień.

## <a name="visit-the-published-portal"></a>Odwiedź opublikowany Portal

Po opublikowaniu portalu możesz uzyskać do niego dostęp przy użyciu tego samego adresu URL, na przykład w panelu administracyjnym `https://contoso-api.developer.azure-api.net` . Wyświetl je w oddzielnym sesji przeglądarki (incognito/Private Mode) jako gość zewnętrzny.

## <a name="apply-the-cors-policy-on-apis"></a>Stosowanie zasad CORS w interfejsach API

Musisz włączyć funkcję CORS (Udostępnianie zasobów między źródłami) w interfejsach API, aby umożliwić odwiedzającym portalu testowanie interfejsów API za pomocą wbudowanej konsoli interaktywnej. Aby uzyskać więcej informacji, zapoznaj się z [artykułem dotyczącym tej dokumentacji](api-management-howto-developer-portal.md#cors) .

## <a name="next-steps"></a>Następne kroki
- [Optymalizowanie i oszczędzanie wydatków w chmurze](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

Dowiedz się więcej o portalu dla deweloperów:

- [Portal deweloperów API Management platformy Azure — omówienie](api-management-howto-developer-portal.md)