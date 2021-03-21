---
title: Samouczek — dostęp i dostosowywanie portalu dla deweloperów — API Management platformy Azure | Microsoft Docs
description: Postępuj zgodnie z tym samouczkiem, aby dowiedzieć się, jak dostosować Portal deweloperów API Management, automatycznie wygenerowaną i w pełni dostosowywalną witrynę sieci Web za pomocą dokumentacji interfejsów API.
services: api-management
author: mikebudzynski
ms.service: api-management
ms.topic: tutorial
ms.date: 11/16/2020
ms.author: apimpm
ms.openlocfilehash: 90544fbafe7393630c3f3fbc694ae367eccb7f90
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96012985"
---
# <a name="tutorial-access-and-customize-the-developer-portal"></a>Samouczek: dostęp i dostosowywanie portalu dla deweloperów

*Portal dla deweloperów* to automatycznie generowana, w pełni dostosowywalna witryna sieci Web z dokumentacją interfejsów API. W przypadku, gdy konsumenci interfejsu API mogą odnajdywać interfejsy API, dowiedzieć się, jak ich używać, i zażądać dostępu.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dostęp do zarządzanej wersji portalu dla deweloperów
> * Nawigowanie po interfejsie administracyjnym
> * Dostosowywanie zawartości
> * Publikowanie zmian
> * Wyświetlanie opublikowanego portalu

Więcej szczegółowych informacji można znaleźć w portalu dla deweloperów w [portalu deweloperów API Management platformy Azure](api-management-howto-developer-portal.md).

:::image type="content" source="media/api-management-howto-developer-portal-customize/cover.png" alt-text="Portal dla deweloperów API Management — tryb administratora" border="false":::

## <a name="prerequisites"></a>Wymagania wstępne

- Wykonaj procedury przedstawione w następującym przewodniku Szybki start: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md)
- Importowanie i publikowanie wystąpienia usługi Azure API Management. Aby uzyskać więcej informacji, zobacz [Importowanie i publikowanie](import-and-publish.md)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-the-portal-as-an-administrator"></a>Dostęp do portalu jako administrator

Wykonaj poniższe kroki, aby uzyskać dostęp do zarządzanej wersji portalu.

1. W [Azure Portal](https://portal.azure.com)przejdź do wystąpienia API Management.
1. Na górnym pasku nawigacyjnym wybierz przycisk **Portal dla deweloperów** . Zostanie otwarta nowa karta przeglądarki z wersją administracyjną portalu.

## <a name="understand-the-portals-administrative-interface"></a>Informacje o interfejsie administracyjnym portalu

### <a name="default-content"></a>Zawartość domyślna 

Jeśli po raz pierwszy uzyskujesz dostęp do portalu, domyślna zawartość jest inicjowana automatycznie w tle. Zawartość domyślna została zaprojektowana w celu pokazania możliwości portalu i zminimalizowania dostosowań potrzebnych do spersonalizowania portalu. Więcej informacji o tym, co obejmuje zawartość portalu, można znaleźć w temacie [Omówienie portalu deweloperów w usłudze Azure API Management](api-management-howto-developer-portal.md).

### <a name="visual-editor"></a>Edytor wizualny

Zawartość portalu można dostosować za pomocą edytora wizualnego. 
* Sekcje menu po lewej stronie umożliwiają tworzenie lub modyfikowanie stron, multimediów, układów, menu, stylów lub ustawień witryny sieci Web. 
* Elementy menu u dołu pozwalają przełączać się między okienkami ekranu (na przykład Mobile lub Desktop), wyświetlać elementy portalu widoczne dla uwierzytelnionych lub anonimowych użytkowników albo zapisywać lub cofać akcje.
* Dodaj wiersze do strony, klikając niebieską ikonę ze znakiem plus. 
* Widżety (na przykład tekst, obrazy lub lista interfejsów API) można dodać, naciskając szarą ikonę ze znakiem plus.
* Zmień rozmieszczenie elementów na stronie za pomocą interakcji typu "przeciągnij i upuść". 

### <a name="layouts-and-pages"></a>Układy i strony

:::image type="content" source="media/api-management-howto-developer-portal-customize/pages-layouts.png" alt-text="Strony i układy" border="false":::

Układy definiują sposób wyświetlania stron. Na przykład w zawartości domyślnej istnieją dwa układy: jeden ma zastosowanie do strony głównej, a drugi do wszystkich pozostałych stron.

Układ zostanie zastosowany do strony, dopasowując jej szablon adresu URL do adresu URL strony. Na przykład układ z szablonem adresu URL programu `/wiki/*` zostanie zastosowany do każdej strony z `/wiki/` segmentem w adresie URL: `/wiki/getting-started` , `/wiki/styles` itp.

Na powyższym obrazie zawartość z układu jest oznaczona kolorem niebieskim, podczas gdy strona jest oznaczona kolorem czerwonym. Sekcje menu są odpowiednio oznaczone.

### <a name="styling-guide"></a>Przewodnik po stylu

:::image type="content" source="media/api-management-howto-developer-portal-customize/styling-guide.png" alt-text="Przewodnik po stylu" border="false":::

Przewodnik po stylu jest panelem utworzonym z projektantami. Pozwala na wyświetlanie i Ustawianie stylu wszystkich elementów wizualizacji w portalu. Style są hierarchiczne — wiele elementów dziedziczy właściwości z innych elementów. Na przykład elementy Button używają kolorów dla tekstu i tła. Aby zmienić kolor przycisku, należy zmienić oryginalny wariant koloru.

Aby edytować wariant, zaznacz go i wybierz ikonę ołówka, która pojawia się na górze. Po wprowadzeniu zmian w oknie podręcznym zamknij je.

### <a name="save-button"></a>Przycisk Zapisz

:::image type="content" source="media/api-management-howto-developer-portal-customize/save-button.png" alt-text="Przycisk Zapisz" border="false":::

Za każdym razem, gdy wprowadzisz zmiany w portalu, musisz zapisać je ręcznie, wybierając przycisk **Zapisz** w menu u dołu lub naciśnij klawisze [Ctrl] + [S]. Po zapisaniu zmian zmodyfikowana zawartość jest automatycznie przekazywana do usługi API Management.

## <a name="customize-the-portals-content"></a>Dostosuj zawartość portalu

Przed udostępnieniem portalu Gościom należy spersonalizować automatycznie wygenerowaną zawartość. Zalecane zmiany obejmują układy, style i zawartość strony głównej.

> [!NOTE]
> Ze względu na kwestie związane z integracją nie można usunąć ani przenieść następujących stron pod innym adresem URL:,,,,,,,,,,, `/404` `/500` `/captcha` `/change-password` `/config.json` `/confirm/invitation` `/confirm-v2/identities/basic/signup` `/confirm-v2/password` `/internal-status-0123456789abcdef` `/publish` `/signin` `/signin-sso` `/signup` .

### <a name="home-page"></a>Strona główna

Domyślna strona **główna** jest wypełniana zawartością zastępczą. Można usunąć całe sekcje zawierające tę zawartość lub zachować strukturę i dostosować elementy po jednym. Zamień wygenerowany tekst i obrazy na własne i upewnij się, że linki wskazują wybrane lokalizacje.

### <a name="layouts"></a>Układy

Zastąp automatycznie wygenerowany logo na pasku nawigacyjnym własnym obrazem.

### <a name="styling"></a>Style

Chociaż nie musisz dostosowywać żadnych stylów, możesz rozważyć dostosowanie poszczególnych elementów. Na przykład zmień kolor podstawowy, aby pasował do koloru marki.

### <a name="customization-example"></a>Przykład dostosowania

W poniższym filmie wideo pokazano, jak edytować zawartość portalu, dostosować wygląd witryny sieci Web i opublikować zmiany.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="publish-the-portal"></a><a name="publish"></a> Publikowanie portalu

Aby Portal i najnowsze zmiany były dostępne dla odwiedzających, należy je *opublikować* . Portal można opublikować w interfejsie administracyjnym portalu lub z poziomu Azure Portal.

### <a name="publish-from-the-administrative-interface"></a>Publikowanie z poziomu interfejsu administracyjnego

1. Upewnij się, że Zapisano zmiany, wybierając ikonę **Zapisz** .
1. W sekcji **operacje** menu wybierz pozycję **Opublikuj witrynę sieci Web** . Ta operacja może potrwać kilka minut.  

    :::image type="content" source="media/api-management-howto-developer-portal-customize/publish-portal.png" alt-text="Publikowanie portalu" border="false":::

### <a name="publish-from-the-azure-portal"></a>Publikowanie z Azure Portal

1. W [Azure Portal](https://portal.azure.com)przejdź do wystąpienia API Management.
1. W menu po lewej stronie w obszarze **Portal deweloperów** wybierz pozycję **Omówienie portalu**.
1. W oknie **Przegląd portalu** wybierz pozycję **Publikuj**.

    :::image type="content" source="media/api-management-howto-developer-portal-customize/pubish-portal-azure-portal.png" alt-text="Publikowanie portalu z Azure Portal":::

> [!NOTE]
> Po zmianie konfiguracji usługi API Management należy ponownie opublikować Portal. Na przykład Opublikuj ponownie Portal po przypisaniu domeny niestandardowej, zaktualizowaniu dostawców tożsamości, ustawieniu delegowania lub określeniu warunków logowania i produktów.


## <a name="visit-the-published-portal"></a>Odwiedź opublikowany Portal

Po opublikowaniu portalu możesz uzyskać do niego dostęp przy użyciu tego samego adresu URL, na przykład w panelu administracyjnym `https://contoso-api.developer.azure-api.net` . Wyświetl je w oddzielnej sesji przeglądarki (przy użyciu incognito lub prywatnego trybu przeglądania) jako zewnętrznego gościa.

## <a name="apply-the-cors-policy-on-apis"></a>Stosowanie zasad CORS w interfejsach API

Aby umożliwić odwiedzającym portalu testowanie interfejsów API za pomocą wbudowanej konsoli interaktywnej, Włącz funkcję CORS (Udostępnianie zasobów między źródłami) w interfejsach API. Aby uzyskać szczegółowe informacje, zobacz [Omówienie portalu deweloperów usługi Azure API Management](api-management-howto-developer-portal.md#cors).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o portalu dla deweloperów:

- [Portal deweloperów usługi Azure API Management — omówienie](api-management-howto-developer-portal.md)
- [Przeprowadź migrację do nowego portalu dla deweloperów](developer-portal-deprecated-migration.md) z przestarzałego starszego portalu.