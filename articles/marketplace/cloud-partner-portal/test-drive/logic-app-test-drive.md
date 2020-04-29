---
title: Dysk testowy aplikacji logiki | Portal Azure Marketplace
description: Wyjaśnia, jak skompilować swój dysk testowy, który łączy się z wystąpieniem programu Dynamics AX/CRM lub innym zasobem poza platformą Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 766f893d71ca0830fe8b69c50145603c6544cc3f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80278386"
---
<a name="logic-app-test-drive"></a>Dysk testowy aplikacji logiki
====================

Ten artykuł jest przeznaczony dla wydawców, którzy mają swoją ofertę w witrynie AppSource, i chcą skompilować swój dysk testowy, który łączy się z wystąpieniem programu Dynamics AX/CRM lub innym zasobem poza platformą Azure.

<a name="how-to-build-a-logic-app-test-drive"></a>Jak utworzyć dysk testowy aplikacji logiki
-----------------------------------

Dokumentacja dotycząca dysków testowych dla usługi Logic App test Drives jest obecnie dostępna w serwisie GitHub na potrzeby [operacji](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) i [zaangażowania klientów](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app). Aby dowiedzieć się więcej, przejdź na stronę.

<a name="how-to-publish-a-test-drive"></a>Jak opublikować dysk testowy
---------------------------

Teraz, gdy masz już skompilowany dysk testowy, w tej sekcji omówiono wszystkie pola wymagane do pomyślnego opublikowania dysku testowego.

![Włącz funkcję Test Drive](./media/azure-resource-manager-test-drive/howtopub1.png)

Pierwsze i najważniejsze pole polega na przełączeniu, czy chcesz, aby test formularza ze wszystkimi wymaganymi polami został przedstawiony do wypełnienia. Po wybraniu opcji **nie** formularz zostanie wyłączony, a w przypadku ponownego opublikowania z wyłączonym dyskiem testowym dysk testowy zostanie usunięty z produkcji.

*Uwaga*: Jeśli wszystkie dyski testowe są używane w sposób aktywny przez użytkowników, te dyski testowe będą nadal działać do momentu wygaśnięcia sesji.

### <a name="details"></a>Szczegóły

Następna sekcja do wypełnienia to szczegółowe informacje o ofercie dotyczącej wersji testowej.

![Szczegóły sterownika testu](./media/azure-resource-manager-test-drive/howtopub2.png)

**Opis —** *[pole wymagane]* to miejsce, w którym napiszesz główny opis dotyczący tego, co znajduje się na dysku testowym. Klient będzie miał tu miejsce, aby zapoznać się z scenariuszami, które będą obejmowały Twój produkt. 

**Podręcznik użytkownika —** *[pole wymagane]* jest to szczegółowy przewodnik po środowisku testowym. Klient otworzy ten element i będzie mógł zapoznać się z dokładnie tym, co chcesz zrobić w całej wersji testowej. Ważne jest, aby zawartość tego programu była łatwa do zrozumienia i wykonania. (Musi być plikiem PDF)

**Film wideo demonstracyjnej wersji testowej —** \[zalecane\] podobne do podręcznika użytkownika. najlepiej jest dodać samouczek wideo dotyczący środowiska testowego. Klient będzie oglądać ten element przed lub w trakcie jego testowania i może przeszukiwać dokładnie te elementy, które mają być wykonywane na całym dysku testowym. Ważne jest, aby zawartość tego programu była łatwa do zrozumienia i wykonania.

- **Nazwa** — tytuł filmu wideo
- **Link** — musi być OSADZONYM adresem URL z usługi YouTube lub Vimeo. Przykład dotyczący sposobu uzyskiwania osadzonego adresu URL jest następujący:
- **Miniatura** — musi być pikseli obrazu o wysokiej jakości (533x324). Zalecane jest przełączenie zrzutu ekranu na część środowiska testowego.

Poniżej przedstawiono sposób wyświetlania tych pól dla klienta w środowisku testowym.

![Sprawdzanie i działanie pól na dysku testowym](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>Konfiguracja techniczna

Następna sekcja do wypełnienia to miejsce, w którym konfigurujesz aplikację logiki dysku testowego i definiujesz, w jaki sposób konkretne wystąpienia testów są wykonywane.

![Testuj konfigurację techniczną](./media/azure-resource-manager-test-drive/howtopub5_logicapp.png)

- **Region** - *[pole wymagane]* wybrany region to miejsce, w którym można wybrać lokalizację, w której są wdrażane zasoby aplikacji logiki dysku testowego.

    *Uwaga:* Jeśli aplikacja logiki ma zasoby niestandardowe, które są przechowywane w regionie, upewnij się, że w tym miejscu wybrano region. Najlepszym sposobem jest w **pełni wdrażanie aplikacji logiki lokalnie w ramach subskrypcji platformy Azure w portalu i upewnienie się, że działa** przed zapisaniem jej w tym miejscu.

- **Maksymalna liczba współbieżnych dysków** - testowych *[pole wymagane]* liczbę wystąpień testowych, które są już wdrożone i oczekujące na dostęp w wybranym regionie. Klienci mogą natychmiast uzyskać dostęp do tych dysków testowych zamiast czekać na wdrożenie.

    *Uwaga:* Jeśli korzystasz z seminarium internetowe/klasy, w której chcesz, aby wszyscy N studentów mogli korzystać z wersji testowej, zaleca się opublikowanie z N liczbami aktywnych wystąpień, a następnie, gdy Klasa jest w trybie failover w celu ponownego opublikowania w normalnej liczbie aktywnych wystąpień.

- **Czas trwania dysku testowego (w godzinach) —** *[pole wymagane]* czas trwania okresu, przez który dysk testowy pozostanie aktywny w \# godzinach. Po upływie tego czasu test kończy się automatycznie.

- **Nazwa grupy zasobów platformy Azure —** *[pole wymagane]* wpisz nazwę grupy zasobów, w której są zapisywane dyski testowe aplikacji logiki.

- **Przypisywanie nazwy aplikacji logiki —** *[pole wymagane]* zapis w aplikacji logiki, która jest używana do przypisywania użytkownika na dysku testowym przed jego pobraniem przez klienta, należy napisać tutaj nazwę tej aplikacji logiki. Upewnij się, że ten plik jest zapisany w powyższej grupie zasobów.

- Cofanie aprowizacji **nazwy aplikacji logiki —** *[pole wymagane]* zapis w nazwie aplikacji logiki w celu anulowania aprowizacji wszystkich zasobów utworzonych na dysku testowym. Upewnij się, że ten plik jest zapisany w powyższej grupie zasobów.

- **Informacje o dostępie —** *[pole wymagane]* po otrzymaniu przez klienta dysku testowego są dla nich widoczne informacje o dostępie. Te instrukcje mają na celu udostępnienie przydatnych parametrów wyjściowych na dysku testowym Menedżer zasobów szablonu. Aby uwzględnić parametry wyjściowe, należy użyć podwójnych nawiasów klamrowych (na przykład **{{outputName}}**), które zostaną poprawnie wstawione w lokalizacji. (W tym miejscu zalecane jest formatowanie ciągu HTML do renderowania w frontonie).

### <a name="test-drive-deployment-subscription-details"></a>Szczegóły subskrypcji wdrożenia dysku testowego

Ostatnia sekcja do wypełnienia polega na automatycznym wdrożeniu dysków testowych przez połączenie subskrypcji platformy Azure i usługi Azure Active Directory (AD).

![Szczegóły subskrypcji wdrożenia dysku testowego](./media/azure-resource-manager-test-drive/subdetails1.png)

**Identyfikator subskrypcji platformy Azure** *[pole wymagane]* pozwala to na dostęp do usług platformy Azure i Azure Portal. Subskrypcja polega na tym, że raportowane jest użycie zasobów, a usługi są rozliczane. Jeśli nie masz jeszcze **oddzielnej** subskrypcji platformy Azure dla dysków testowych, przejdź dalej i utwórz ją. Identyfikatory subskrypcji platformy Azure można znaleźć, logując się do Azure Portal i przechodząc do subskrypcji w menu po lewej stronie.
(Przykład: "a83645ac-1234-5ab6-6789-1h234g764ghty")

![Subskrypcje platformy Azure](./media/azure-resource-manager-test-drive/subdetails2.png)

**Identyfikator dzierżawy usługi Azure AD** *[pole wymagane]* Jeśli masz już dostępny identyfikator dzierżawy, możesz go znaleźć poniżej w obszarze właściwości\> — identyfikator katalogu.

![Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails3.png)

W przeciwnym razie Utwórz nową dzierżawę w Azure Active Directory.

![Ekran właściwości Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails4.png)

! Azure Active Directory] (./Media/Azure-Resource-Manager-Test-Drive/subdetails5.png)

![Dzierżawy Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails6.png)

**Identyfikator aplikacja usługi Azure AD** *[pole wymagane]* następnym krokiem jest utworzenie i zarejestrowanie nowej aplikacji. Ta aplikacja zostanie użyta do wykonania operacji w wystąpieniu dysku testowego.

1. Przejdź do nowo utworzonego katalogu lub już istniejącego katalogu i wybierz pozycję Azure Active Directory w okienku Filtr.
2. Wyszukaj ciąg "Rejestracje aplikacji" i kliknij pozycję "Dodaj"
3. Podaj nazwę aplikacji.
4. Wybierz typ as "Web App/API"
5. Podaj dowolną wartość w adresie URL logowania, w którym\'t nie korzystamy z tego pola.
6. Kliknij przycisk Utwórz.
7. Po utworzeniu aplikacji przejdź do pozycji właściwości —\> Ustaw aplikację jako wielodostępną i naciśnij pozycję Zapisz.

Kliknij pozycję Zapisz. Ostatnim krokiem jest pobranie identyfikatora aplikacji dla tej zarejestrowanej aplikacji i wklejenie go w polu Test Drive tutaj.

![Identyfikator aplikacji usługi Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails7.png)

Przy użyciu aplikacji do wdrożenia w ramach subskrypcji musimy dodać aplikację jako współautora subskrypcji. Poniższe instrukcje są następujące:

1. Przejdź do bloku subskrypcje i wybierz odpowiednią subskrypcję, która jest używana tylko dla wersji testowej.
1. Kliknij pozycję **Kontrola dostępu (IAM)**.
1. Kliknij kartę **przypisania ról** .  ![Azure Active Directory, dodawanie nowego Access Control podmiotu zabezpieczeń](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. Kliknij pozycję **Dodaj przypisanie roli**.
1. Ustaw rolę jako **współautor**.
1. Wpisz nazwę aplikacji usługi Azure AD i wybierz aplikację, w której ma zostać przypisana rola.
    ![Uprawnienia Azure Active Directory](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. Kliknij przycisk **Zapisz**.

**Klucz aplikacja usługi Azure AD-** *[pole wymagane]* końcowe pole ma generować klucz uwierzytelniania. W obszarze klucze Dodaj Opis klucza, ustaw czas trwania, aby nigdy nie wygaśnie, a następnie wybierz pozycję Zapisz. **Ważne** jest, aby uniknąć posiadania klucza wygasłego, co spowoduje uszkodzenie dysku testowego w środowisku produkcyjnym. Skopiuj tę wartość i wklej ją do wymaganego pola Test Drive.

![Sekcja kluczy Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails8.png)

> [!CAUTION]
> Nie można użyć wersji zapoznawczej rejestracji aplikacji platformy Azure, ponieważ nie generuje ona obecnie klucza zakodowanego algorytmem Base64.


<a name="next-steps"></a>Następne kroki
----------

Teraz, gdy masz wszystkie wypełnione pola dysku testowego, przejdź przez i **ponownie Opublikuj** swoją ofertę. Gdy Twój test przeszedł proces certyfikacji, należy zapoznać się z obszernym testem środowiska klienta w **wersji zapoznawczej** oferty. Uruchom test w interfejsie użytkownika i sprawdź, czy dyski testowe są w pełni wdrożone prawidłowo.

Należy pamiętać, że nie usuwasz żadnej części dysku testowego, ponieważ są one inicjowane dla klientów, więc Usługa dysków testowych automatycznie wyczyści te grupy zasobów po zakończeniu działania klienta.

Gdy będziesz mieć doświadczenie z ofertą wersji zapoznawczej, teraz możesz **przejść na żywo**. Po opublikowaniu oferty od firmy Microsoft jest dostępny końcowy proces przeglądu, który umożliwia dokładne sprawdzenie całego środowiska. Jeśli z jakiegoś powodu oferta zostanie odrzucona, wyślemy powiadomienie do osoby kontaktowej inżyniera o swojej ofercie wyjaśniającej, co będzie musiało zostać naprawione.

Jeśli masz więcej pytań, szukasz porad dotyczących rozwiązywania problemów lub chcesz, aby test został pomyślnie przeszedł, przejdź do [sekcji często zadawane pytania, rozwiązywanie problemów, & najlepsze rozwiązania](./marketing-and-best-practices.md).
