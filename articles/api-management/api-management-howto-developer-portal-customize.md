---
title: Samouczek — uzyskiwanie dostępu do portalu dla deweloperów i dostosowywanie go — Azure API Management | Microsoft Docs
description: Postępuj zgodnie z tym samouczkiem, aby dowiedzieć się, jak dostosować API Management portal dla deweloperów, automatycznie wygenerowaną, w pełni dostosowywaną witrynę internetową z dokumentacją interfejsów API.
services: api-management
author: mikebudzynski
ms.service: api-management
ms.topic: tutorial
ms.date: 11/16/2020
ms.author: apimpm
ms.openlocfilehash: 7c341dee3106530715248355da4412b97ed30980
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739624"
---
# <a name="tutorial-access-and-customize-the-developer-portal"></a>Samouczek: uzyskiwanie dostępu do portalu dla deweloperów i dostosowywanie go

Portal *dla deweloperów to* automatycznie generowana, w pełni dostosowywalna witryna internetowa z dokumentacją interfejsów API. Jest to miejsce, w którym użytkownicy interfejsu API mogą odnajdywać Twoje interfejsy API, uczyć się ich używać i żądać dostępu.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Uzyskiwanie dostępu do zarządzanej wersji portalu dla deweloperów
> * Nawigowanie po interfejsie administracyjnym
> * Dostosowywanie zawartości
> * Publikowanie zmian
> * Wyświetlanie opublikowanego portalu

Więcej szczegółów można znaleźć w portalu dla deweloperów w witrynie [Azure API Management omówienie portalu dla deweloperów.](api-management-howto-developer-portal.md)

:::image type="content" source="media/api-management-howto-developer-portal-customize/cover.png" alt-text="API Management dla deweloperów — tryb administratora" border="false":::

## <a name="prerequisites"></a>Wymagania wstępne

- Wykonaj procedury przedstawione w następującym przewodniku Szybki start: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md)
- Zaimportuj i opublikuj wystąpienie API Management Azure. Aby uzyskać więcej informacji, zobacz [Importowanie i publikowanie](import-and-publish.md)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-the-portal-as-an-administrator"></a>Uzyskiwanie dostępu do portalu jako administrator

Wykonaj poniższe kroki, aby uzyskać dostęp do zarządzanej wersji portalu.

1. W [Azure Portal](https://portal.azure.com)przejdź do swojego API Management wystąpienia.
1. Wybierz przycisk **Portal deweloperów** na górnym pasku nawigacyjnym. Zostanie otwarta nowa karta przeglądarki z wersją administracyjną portalu.


## <a name="developer-portal-architectural-concepts"></a>Pojęcia dotyczące architektury portalu dla deweloperów

Składniki portalu można logicznie podzielić na dwie kategorie: *kod* i *zawartość*.

### <a name="code"></a>Kod

Kod jest utrzymywany w repozytorium [GitHub](https://github.com/Azure/api-management-developer-portal) API Management portalu deweloperów i zawiera:

- **Widżety** — reprezentują elementy wizualne i łączą kod HTML, JavaScript, możliwość stylów, ustawienia i mapowanie zawartości. Przykłady to obraz, akapit tekstowy, formularz, lista interfejsów API itp.
- **Definicje stylów** — określanie stylu widżetów
- **Aparat** — który generuje statyczne strony internetowe na podstawie zawartości portalu i jest napisany w języku JavaScript
- **Edytor wizualizacji** — umożliwia dostosowywanie i tworzenie w przeglądarce

### <a name="content"></a>Zawartość

Zawartość jest podzielona na dwie podkategorie: *zawartość portalu* i *API Management zawartości*.

*Zawartość portalu* jest specyficzna dla portalu i obejmuje:

- **Strony** — na przykład strona docelowa, samouczki interfejsu API, wpisy w blogu
- **Multimedia** — obrazy, animacje i inna zawartość oparta na plikach
- **Układy** — szablony dopasowane do adresu URL i definiujące sposób wyświetlania stron
- **Style** — wartości definicji stylów, takich jak czcionki, kolory, obramowania
- **Ustawienia** — konfiguracje, takie jak favicon, metadane witryny internetowej

    Zawartość portalu, z wyjątkiem multimediów, jest wyrażona jako dokumenty JSON.

*API Management zawiera* jednostki, takie jak interfejsy API, operacje, produkty, subskrypcje.
## <a name="understand-the-portals-administrative-interface"></a>Opis interfejsu administracyjnego portalu

### <a name="default-content"></a>Zawartość domyślna 

Jeśli uzyskujesz dostęp do portalu po raz pierwszy, zawartość domyślna jest automatycznie aprowizowana w tle. Zawartość domyślna została zaprojektowana w celu zaprezentowania możliwości portalu i zminimalizowania dostosowań wymaganych do spersonalizowania portalu. Więcej informacji na temat zawartości portalu można znaleźć w tesłudze Azure API Management developer portal overview (Omówienie portalu dla deweloperów usługi [Azure API Management).](api-management-howto-developer-portal.md)

### <a name="visual-editor"></a>Edytor wizualizacji

Zawartość portalu można dostosować za pomocą edytora wizualizacji. 
* Sekcje menu po lewej stronie umożliwiają tworzenie lub modyfikowanie stron, multimediów, układów, menu, stylów lub ustawień witryny internetowej. 
* Elementy menu w dolnej części umożliwiają przełączanie się między widokami (na przykład dla urządzeń przenośnych lub komputerów stacjonarnych), wyświetlanie elementów portalu widocznych dla uwierzytelnionych lub anonimowych użytkowników albo zapisywanie lub cofanie akcji.
* Dodaj wiersze do strony, klikając niebieską ikonę ze znakiem plus. 
* Widżety (na przykład tekst, obrazy lub lista interfejsów API) można dodać, naciskając szarą ikonę ze znakiem plus.
* Rozmieszczanie elementów na stronie za pomocą interakcji przeciągania i upuszczania. 

### <a name="layouts-and-pages"></a>Układy i strony

:::image type="content" source="media/api-management-howto-developer-portal-customize/pages-layouts.png" alt-text="Strony i układy" border="false":::

Układy definiują sposób wyświetlania stron. Na przykład w domyślnej zawartości istnieją dwa układy: jeden dotyczy strony głównej, a drugi do wszystkich pozostałych stron.

Układ jest stosowany do strony przez dopasowanie szablonu adresu URL do adresu URL strony. Na przykład układ z szablonem adresu URL zostanie zastosowany do każdej strony z segmentem w adresie `/wiki/*` `/wiki/` URL: `/wiki/getting-started` , `/wiki/styles` itp.

Na poprzedniej ilustracji zawartość należąca do układu jest oznaczona kolorem niebieskim, a strona jest oznaczona kolorem czerwonym. Sekcje menu są odpowiednio oznaczone.

### <a name="styling-guide"></a>Przewodnik dotyczący stylów

:::image type="content" source="media/api-management-howto-developer-portal-customize/styling-guide.png" alt-text="Przewodnik dotyczący stylów" border="false":::

Przewodnik po stylu to panel utworzony z myślą o projektantach. Umożliwia nadzorowanie i nadanie stylu wszystkim elementom wizualnym w portalu. Styl jest hierarchiczny — wiele elementów dziedziczy właściwości z innych elementów. Na przykład elementy przycisku używają kolorów tekstu i tła. Aby zmienić kolor przycisku, musisz zmienić oryginalny wariant koloru.

Aby edytować wariant, wybierz go i wybierz ikonę ołówka, która jest wyświetlana u góry. Po wymuszeniu zmian w oknie podręcznym zamknij je.

### <a name="save-button"></a>Przycisk Zapisz

:::image type="content" source="media/api-management-howto-developer-portal-customize/save-button.png" alt-text="Przycisk Zapisz" border="false":::

Za każdym razem, gdy dokonasz zmiany w portalu,  musisz zapisać ją ręcznie, wybierając przycisk Zapisz w menu u dołu lub naciskając klawisze [Ctrl]+[S]. Po zapisaniu zmian zmodyfikowana zawartość zostanie automatycznie przesłana do usługi API Management service.

## <a name="customize-the-portals-content"></a>Dostosowywanie zawartości portalu

Zanim udostępnisz portal odwiedzającym, należy spersonalizować automatycznie wygenerowaną zawartość. Zalecane zmiany obejmują układy, style i zawartość strony głównej.

> [!NOTE]
> Ze względu na zagadnienia dotyczące integracji nie można usunąć ani przenieść następujących stron pod innym adresem URL: `/404` , , , , , , , , `/500` , , , `/captcha` , , `/change-password` `/config.json` `/confirm/invitation` `/confirm-v2/identities/basic/signup` `/confirm-v2/password` `/internal-status-0123456789abcdef` `/publish` `/signin` `/signin-sso` `/signup` .

### <a name="home-page"></a>Strona główna

Domyślna **strona główna** jest wypełniona zawartością symbolu zastępczego. Możesz usunąć całe sekcje zawierające tę zawartość lub zachować strukturę i dostosować elementy jeden po drugiej. Zastąp wygenerowany tekst i obraz własnymi i upewnij się, że linki wskazują żądane lokalizacje.

### <a name="layouts"></a>Układy

Zastąp automatycznie wygenerowane logo na pasku nawigacyjnym własnym obrazem.

### <a name="styling"></a>Style

Chociaż nie musisz dostosowywać żadnych stylów, możesz rozważyć dostosowanie określonych elementów. Na przykład zmień kolor podstawowy, aby dopasować go do koloru marki.

### <a name="customization-example"></a>Przykład dostosowywania

W poniższym filmie wideo pokazujemy, jak edytować zawartość portalu, dostosowywać wygląd witryny internetowej i publikować zmiany.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="publish-the-portal"></a><a name="publish"></a> Publikowanie portalu

Aby udostępnić odwiedzającym portal i jego najnowsze zmiany, należy go *opublikować.* Portal można opublikować w interfejsie administracyjnym portalu lub za pomocą Azure Portal.

### <a name="publish-from-the-administrative-interface"></a>Publikowanie z interfejsu administracyjnego

1. Upewnij się, że zmiany zostały zapisane, wybierając **ikonę** Zapisz.
1. W sekcji **Operacje** menu wybierz pozycję **Opublikuj witrynę internetową.** Ta operacja może potrwać kilka minut.  

    :::image type="content" source="media/api-management-howto-developer-portal-customize/publish-portal.png" alt-text="Publikowanie portalu" border="false":::

### <a name="publish-from-the-azure-portal"></a>Publikowanie z Azure Portal

1. W [Azure Portal](https://portal.azure.com)przejdź do swojego API Management wystąpienia.
1. W menu po lewej stronie w obszarze **Portal deweloperów** wybierz pozycję **Przegląd portalu.**
1. W **oknie Przegląd portalu** wybierz pozycję **Opublikuj**.

    :::image type="content" source="media/api-management-howto-developer-portal-customize/pubish-portal-azure-portal.png" alt-text="Publikowanie portalu z Azure Portal":::

> [!NOTE]
> Portal należy opublikować ponownie po API Management konfiguracji usługi. Na przykład ponownie opublikować portal po przypisaniu domeny niestandardowej, zaktualizowaniu dostawców tożsamości, ustawieniu delegowania lub określeniu warunków logowania i produktu.


## <a name="visit-the-published-portal"></a>Odwiedź opublikowany portal

Po opublikowaniu portalu możesz uzyskać do niego dostęp pod tym samym adresem URL co panel administracyjny, na przykład `https://contoso-api.developer.azure-api.net` . Wyświetl go w osobnej sesji przeglądarki (przy użyciu trybu przeglądania incognito lub prywatnego) jako zewnętrzny odwiedzający.

## <a name="apply-the-cors-policy-on-apis"></a>Stosowanie zasad CORS w interfejsach API

Aby umożliwić odwiedzającym portal testowanie interfejsów API za pośrednictwem wbudowanej konsoli interaktywnej, włącz funkcję CORS (współużytkowanie zasobów między źródłami) w interfejsach API. Aby uzyskać szczegółowe informacje, zobacz [Azure API Management developer portal FAQ (Azure API Management portal dla deweloperów — często zadawane pytania).](developer-portal-faq.md#cors)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o portalu dla deweloperów:

- [Portal deweloperów usługi Azure API Management — omówienie](api-management-howto-developer-portal.md)
- [Migracja do nowego portalu dla deweloperów](developer-portal-deprecated-migration.md) z przestarzałego starszego portalu.