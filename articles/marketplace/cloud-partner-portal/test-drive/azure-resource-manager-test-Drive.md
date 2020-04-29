---
title: Azure Resource Manager dysk testowy | Portal Azure Marketplace
description: Tworzenie dysku testowego Marketplace przy użyciu Azure Resource Manager
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 6125aa010d8676518b84f866343b01f95246160f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80275938"
---
# <a name="azure-resource-manager-test-drive"></a>Wersja testowa usługi Azure Resource Manager

Ten artykuł jest przeznaczony dla wydawców, którzy mają swoją ofertę w witrynie Azure Marketplace lub którzy znajdują się w witrynie AppSource, ale chcą skompilować swój dysk testowy tylko za pomocą zasobów platformy Azure.

Szablon Azure Resource Manager (Menedżer zasobów) jest zakodowanym kontenerem zasobów platformy Azure, które są projektowane w celu najlepszego reprezentowania rozwiązania. Jeśli nie znasz Menedżer zasobów szablonu, zapoznaj się z informacjami na temat [Menedżer zasobów szablonów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) i szablonów [Menedżer zasobów tworzenia](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) , aby upewnić się, jak tworzyć i testować własne szablony.

Co oznacza, że jest on pobierany Menedżer zasobów szablon i wdraża wszystkie zasoby wymagane z tego szablonu Menedżer zasobów w grupie zasobów.

Jeśli zdecydujesz się na utworzenie Azure Resource Manager dysku testowego, wymagania są następujące:

- Kompilowanie, testowanie i przekazywanie Menedżer zasobów szablonu na dysku testowym.
- Skonfiguruj wszystkie wymagane metadane i ustawienia, aby umożliwić korzystanie ze stacji testowej.
- Opublikuj ponownie ofertę z włączonym dyskiem testowym.

## <a name="how-to-build-an-azure-resource-manager-test-drive"></a>Jak utworzyć Azure Resource Manager dysk testowy

Oto proces tworzenia Azure Resource Manager dysku testowego:

1. Projektuj elementy, które mają być wykonywane przez klientów na diagramie przepływu.
1. Zdefiniuj środowiska, które mają być kompilowane przez klientów.
1. Zgodnie z powyższymi definicjami Zdecyduj, jakie elementy i zasoby są potrzebne klientom do osiągnięcia takich czynności: na przykład wystąpienie D365 lub witryna sieci Web z bazą danych.
1. Utwórz projekt lokalnie i przetestuj środowisko pracy.
1. Pakowanie środowiska we wdrożeniu szablonu ARM i z tego miejsca:
    1. Zdefiniuj, jakie części zasobów są parametrami wejściowymi;
    1. Jakie zmienne są;
    1. Jakie dane wyjściowe są przekazywane do środowiska klienta.
1. Publikuj, Testuj i przejdź na żywo.

Najważniejszym elementem dotyczącym tworzenia dysku testowego Azure Resource Manager jest zdefiniowanie scenariuszy, które mają być używane przez klientów. Czy jesteś produktem zapory i chcesz obejrzeć, jak dobrze obsługujesz ataki przed iniekcją skryptów? Czy jesteś produktem magazynu i chcesz obejrzeć, jak szybka i łatwa w obsłudze rozwiązanie kompresuje pliki?

Upewnij się, że poświęcasz wystarczającą ilość czasu na ocenę najlepszego sposobu wyświetlania produktu. W odniesieniu do wszystkich wymaganych zasobów, które są potrzebne, ponieważ sprawia, że pakowanie szablonu Menedżer zasobów jest wystarczająco prostsze.

Aby nadal korzystać z naszego przykładu zapory, architektura może być niezbędnym adresem URL publicznego adresu IP dla usługi i innym publicznym adresem URL dla witryny sieci Web chronionej przez zaporę. Każdy adres IP jest wdrażany na maszynie wirtualnej i połączony ze sobą za pomocą sieciowej grupy zabezpieczeń i interfejsu sieciowego.

Po zaprojektowaniu żądanego pakietu zasobów program będzie teraz mógł pisać i kompilować szablon Menedżer zasobów na dysku testowym.

## <a name="writing-test-drive-resource-manager-templates"></a>Pisanie Menedżer zasobów szablonów na dysku testowym

Test na dysku uruchamia wdrożenia w trybie w pełni zautomatyzowanej i z tego powodu szablony na dysku testowym mają pewne ograniczenia opisane poniżej.

### <a name="parameters"></a>Parametry

Większość szablonów ma zestaw parametrów. Parametry definiują nazwy zasobów, rozmiary zasobów (na przykład typy kont magazynu lub maszyn wirtualnych), nazwy użytkowników i hasła, nazwy DNS i tak dalej. Podczas wdrażania rozwiązań przy użyciu Azure Portal można ręcznie wypełnić wszystkie te parametry, wybrać dostępne nazwy DNS lub konta magazynu itd.

![Lista parametrów w Azure Resource Manager](./media/azure-resource-manager-test-drive/param1.png)

Jednak wersja testowa działa w pełni automatyczny tryb bez interakcji z człowiekiem, dlatego obsługuje tylko ograniczony zestaw kategorii parametrów. Jeśli parametr na dysku testowym Menedżer zasobów szablon nie należy do jednej z obsługiwanych kategorii, należy **zastąpić ten parametr wartością zmiennej lub stałą.**

Można użyć dowolnej prawidłowej nazwy parametrów, a Test Drive rozpoznaje kategorię parametrów przy użyciu wartości typu Metadata. **Dla każdego parametru szablonu należy określić typ metadanych**, w przeciwnym razie szablon nie zostanie przekazana walidacji:

```json
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username"
    }
  },
  ...
}
```

Należy również pamiętać, że **wszystkie parametry są opcjonalne**, więc jeśli użytkownik\'nie chce korzystać z któregoś z nich,\'nie trzeba.

### <a name="accepted-parameter-metadata-types"></a>Akceptowane typy metadanych parametrów

| Typ metadanych   | Typ parametru  | Opis     | Przykładowa wartość    |
|---|---|---|---|
| **baseUri**     | ciąg          | Podstawowy identyfikator URI pakietu wdrożeniowego| https:\//\<.\. \>. blob.Core.Windows.NET/\<\..\> |
| **uż**    | ciąg          | Nowa losowa nazwa użytkownika.| admin68876      |
| **hasło**    | ciąg zabezpieczony    | Nowe hasło losowe | LP! 2kh\^ACS     |
| **Identyfikator sesji**   | ciąg          | Identyfikator sesji unikatowego dysku testowego (GUID)    | b8c8693e-5673-449c-badd-257a405a6dee |

#### <a name="baseuri"></a>baseUri

Test Drive Inicjuje ten parametr przy użyciu **podstawowego identyfikatora URI** pakietu wdrożeniowego, dlatego można użyć tego parametru do skonstruowania identyfikatora URI dowolnego pliku zawartego w pakiecie.

```json
"parameters": {
  ...
  "baseuri": {
    "type": "string",
    "metadata": {
      "type": "baseuri",
      "description": "Base Uri of the deployment package."
    }
  },
  ...
}
```

Wewnątrz szablonu można użyć tego parametru do konstruowania identyfikatora URI dowolnego pliku z pakietu wdrożeniowego na dysku testowym. W poniższym przykładzie przedstawiono sposób konstruowania identyfikatora URI połączonego szablonu:

```json
"templateLink": {
  "uri": "[concat(parameters('baseuri'),'templates/solution.json')]",
  "contentVersion": "1.0.0.0"
}
```

#### <a name="username"></a>nazwa użytkownika

Test Drive Inicjuje ten parametr przy użyciu nowej losowej nazwy użytkownika:

```json
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username",
      "description": "Solution admin name."
    }
  },
  ...
}
```

Przykładowa wartość:

    admin68876

Możesz użyć losowo lub stałych nazw użytkowników dla rozwiązania.

#### <a name="password"></a>hasło

Test Drive Inicjuje ten parametr przy użyciu nowego hasła losowego:

```json
"parameters": {
  ...
  "password": {
    "type": "securestring",
    "metadata": {
      "type": "password",
      "description": "Solution admin password."
    }
  },
  ...
}
```

Przykładowa wartość:

    Lp!ACS^2kh

Do rozwiązania można użyć haseł losowych lub stałych.

#### <a name="session-id"></a>Identyfikator sesji

Stacja testowa inicjalizuje ten parametr z unikatowym identyfikatorem GUID reprezentującym identyfikator sesji dysku testowego:

```json
"parameters": {
  ...
  "sessionid": {
    "type": "string",
    "metadata": {
      "type": "sessionid",
      "description": "Unique Test Drive session id."
    }
  },
  ...
}
```

Przykładowa wartość:

    b8c8693e-5673-449c-badd-257a405a6dee

Tego parametru można użyć do unikatowego identyfikowania sesji dysku testowego, jeśli jest to konieczne.

### <a name="unique-names"></a>Unikatowe nazwy

Niektóre zasoby platformy Azure, takie jak konta magazynu lub nazwy DNS, wymagają globalnie unikatowych nazw.

Oznacza to, że za każdym razem, gdy dysk testowy wdraża szablon Menedżer zasobów, tworzy **nową grupę zasobów o unikatowej nazwie** dla wszystkich\' zasobów. W związku z tym jest wymagane użycie funkcji [uniquestring](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions#uniquestring) połączonej z nazwami zmiennych w identyfikatorach grup zasobów w celu wygenerowania losowych wartości unikatowych:

```json
"variables": {
  ...
  "domainNameLabel": "[concat('contosovm',uniquestring(resourceGroup().id))]",
  "storageAccountName": "[concat('contosodisk',uniquestring(resourceGroup().id))]",
  ...
}
```

Upewnij się, że łączysz parametry/ciągi zmiennych (\'contosovm\') z unikatowym ciągiem wyjściowym\'(Resources ().\'ID), ponieważ gwarantuje to unikatowość i niezawodność każdej zmiennej.

Na przykład większość nazw zasobów nie może zaczynać się od cyfry, ale funkcja Unique String może zwrócić ciąg, który rozpoczyna się od cyfry. Dlatego jeśli używasz nieprzetworzonego unikatowego ciągu, wdrożenie zakończy się niepowodzeniem. 

Dodatkowe informacje o regułach nazewnictwa zasobów i ograniczeniach można znaleźć w [tym artykule](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

### <a name="deployment-location"></a>Lokalizacja wdrożenia

Możesz sprawdzić, czy dysk jest dostępny w różnych regionach świadczenia usługi Azure. Pomysłem jest umożliwienie użytkownikowi wyboru najbliższego regionu, aby zapewnić środowisko użytkownika Beast.

Gdy Test Drive tworzy wystąpienie laboratorium, zawsze tworzy grupę zasobów w regionie wybranym przez użytkownika, a następnie wykonuje szablon wdrożenia w tym kontekście grupy. Dlatego szablon powinien wybrać lokalizację wdrożenia z grupy zasobów:

```json
"variables": {
  ...
  "location": "[resourceGroup().location]",
  ...
}
```

A następnie użyj tej lokalizacji dla każdego zasobu dla określonego wystąpienia laboratorium:

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/publicIPAddresses",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/virtualNetworks",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/networkInterfaces",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Compute/virtualMachines",
    "location": "[variables('location')]",
    ...
  }
]
```

Musisz się upewnić, że Twoja subskrypcja może wdrożyć wszystkie zasoby, które mają zostać wdrożone w każdym z regionów, które wybierasz. Należy również upewnić się, że obrazy maszyn wirtualnych są dostępne we wszystkich regionach, które mają być włączone. w przeciwnym razie szablon wdrożenia nie będzie działał w niektórych regionach.

### <a name="outputs"></a>Dane wyjściowe

Zwykle za pomocą szablonów Menedżer zasobów można wdrożyć bez tworzenia żadnych danych wyjściowych. Wynika to z faktu, że znane są wszystkie wartości używane do wypełniania parametrów szablonu i zawsze można ręcznie sprawdzić właściwości dowolnego zasobu.

W przypadku Menedżer zasobów szablonów w wersji testowej\'ważne jest jednak, aby powrócić do wszystkich informacji, które są wymagane do uzyskania dostępu do laboratorium (identyfikatorów URI witryny sieci Web, nazw hostów maszyn wirtualnych, nazw użytkowników i haseł). Upewnij się, że wszystkie nazwy wyjściowe są odczytywane, ponieważ te zmienne są prezentowane klientowi.

Nie ma żadnych ograniczeń związanych z danymi wyjściowymi szablonu. Pamiętaj, że Test Drive konwertuje wszystkie wartości wyjściowe na **ciągi**, więc Jeśli wysyłasz obiekt do danych wyjściowych, użytkownik ZOBACZY ciąg JSON.

Przykład:

```json
"outputs": {
  "Host Name": {
    "type": "string",
    "value": "[reference(variables('pubIpId')).dnsSettings.fqdn]"
  },
  "User Name": {
    "type": "string",
    "value": "[parameters('adminName')]"
  },
  "Password": {
    "type": "string",
    "value": "[parameters('adminPassword')]"
  }
}
```

### <a name="subscription-limits"></a>Limity subskrypcji

Należy pamiętać, że należy wziąć pod uwagę limity subskrypcji i usług. Jeśli na przykład chcesz wdrożyć do dziesięciu maszyn wirtualnych z systemem 10 rdzeni, musisz upewnić się, że subskrypcja używana dla laboratorium umożliwia korzystanie z 40 rdzeni.

Więcej informacji na temat limitów subskrypcji i usług platformy Azure można znaleźć w [tym artykule](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits). Ponieważ można jednocześnie wykonać wiele testów, sprawdź, czy subskrypcja może obsłużyć \# rdzenie pomnożone przez łączną liczbę współbieżnych dysków testowych, które mogą być wykonywane.

### <a name="what-to-upload"></a>Co należy przekazać

Szablon Menedżer zasobów testowy jest przekazywany jako plik zip, który może zawierać różne artefakty wdrożenia, ale musi mieć jeden plik o nazwie **Main-Template. JSON**. Ten plik jest Azure Resource Manager szablon wdrożenia, a Stacja testowa używa go do tworzenia wystąpienia laboratorium.

Jeśli masz dodatkowe zasoby wykraczające poza ten plik, możesz odwoływać się do niego jako zasób zewnętrzny wewnątrz szablonu lub dołączyć zasób do pliku zip.

Podczas publikowania certyfikacji, dysk testowy rozpakuje pakiet wdrożeniowy i umieszcza jego zawartość w kontenerze obiektów BLOB wewnętrznego dysku testowego. Struktura kontenera odzwierciedla strukturę pakietu wdrożeniowego:

| plik Package. zip                       | Kontener obiektów BLOB na dysku testowym         |
|---|---|
| Main-Template. JSON                | \//\<https:\... \>. blob.Core.Windows.NET/\<\... \>/Main-Template.JSON  |
| templates/Solution. JSON           | \//\<https:\... \>. blob.Core.Windows.NET/\<\... \>/templates/Solution.JSON |
| skrypty/rozgrzewania. ps1                | \//\<https:\... \>. blob.Core.Windows.NET/\<\... \>/scripts/warmup.ps1  |


Nazywamy identyfikator URI tego podstawowego identyfikatora URI kontenera obiektów BLOB. Każda wersja laboratorium ma swój własny kontener obiektów blob, a w związku z tym każda wersja laboratorium ma własny podstawowy identyfikator URI. Na dysku testowym można przekazać podstawowy identyfikator URI niespakowanego pakietu wdrożeniowego do szablonu za pomocą parametrów szablonu.

## <a name="transforming-template-examples-for-test-drive"></a>Przekształcanie przykładów szablonu dla dysku testowego

Proces polegający na przydzieleniu architektury zasobów do systemu testowego Menedżer zasobów szablonu może być zniechęcające. Aby ułatwić Ci uproszczenie tego procesu,\'warto zapoznać się z przykładami dotyczącymi najlepszego [przekształcania bieżących szablonów wdrażania w tym miejscu](./transforming-examples-for-test-drive.md).

## <a name="how-to-publish-a-test-drive"></a>Jak opublikować dysk testowy

Teraz, gdy masz już skompilowany dysk testowy, w tej sekcji omówiono wszystkie pola wymagane do pomyślnego opublikowania dysku testowego.

![Włączanie stacji testowej w interfejsie użytkownika](./media/azure-resource-manager-test-drive/howtopub1.png)

Pierwsze i najważniejsze pole polega na przełączeniu, czy chcesz, aby dla oferty był włączony dysk testowy. Po wybraniu **opcji tak** pozostała część formularza ze wszystkimi wymaganymi polami zostanie wypełniona. Po wybraniu opcji **nie** formularz zostanie wyłączony, a w przypadku ponownego opublikowania z wyłączonym dyskiem testowym dysk testowy zostanie usunięty z produkcji.

Uwaga: Jeśli istnieją wszystkie testy używane aktywnie przez użytkowników, te dyski testowe będą nadal działać do momentu wygaśnięcia sesji.

### <a name="details"></a>Szczegóły

Następna sekcja do wypełnienia to szczegółowe informacje o ofercie dotyczącej wersji testowej.

![Szczegółowe informacje na temat dysku testowego](./media/azure-resource-manager-test-drive/howtopub2.png)

**Opis —** *wymagane* jest, aby napisać główny opis dotyczący tego, co znajduje się na dysku testowym. Klient będzie miał tu miejsce, aby zapoznać się z scenariuszami, które będą obejmowały Twój produkt. 

**Ręczna —** *wymagane* przez użytkownika to szczegółowy przewodnik dotyczący środowiska testowego. Klient otworzy ten element i będzie mógł zapoznać się z dokładnie tym, co chcesz zrobić w całej wersji testowej. Ważne jest, aby zawartość tego programu była łatwa do zrozumienia i wykonania. (Musi być plikiem PDF)

**Film wideo demonstracyjnej wersji testowej —** *zalecane* podobne do podręcznika użytkownika. najlepiej jest dodać samouczek wideo dotyczący środowiska testowego. Klient będzie oglądać ten element przed lub w trakcie jego testowania i może przeszukiwać dokładnie te elementy, które mają być wykonywane na całym dysku testowym. Ważne jest, aby zawartość tego programu była łatwa do zrozumienia i wykonania.

- **Nazwa** — tytuł filmu wideo
- **Link** — musi być OSADZONYM adresem URL z Twojej rury lub wideo. Przykład dotyczący sposobu uzyskiwania osadzonego adresu URL jest następujący:
- **Miniatura** — musi być pikseli obrazu o wysokiej jakości (533x324). Zalecane jest przełączenie zrzutu ekranu na część środowiska testowego.

Poniżej przedstawiono sposób wyświetlania tych pól dla klienta w środowisku testowym.

![Lokalizacja pól testowych w ofercie Marketplace](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>Konfiguracja techniczna

Następna sekcja do wypełnienia polega na przekazaniu Menedżer zasobów szablonu i zdefiniowaniu sposobu działania wystąpień testowych.

![](./media/azure-resource-manager-test-drive/howtopub5.png)

**Wystąpienia —** *wymagane* to miejsce, w którym można skonfigurować liczbę potrzebnych wystąpień, w jakich regionach i jak szybko klienci mogą uzyskać dysk testowy.

- **Wystąpienia** — wybrane regiony to miejsce, w którym można wybrać lokalizację, w której jest wdrażany Menedżer zasobów szablon. Zaleca się tylko wybranie jednego regionu, w którym oczekuje się, że klienci znajdują się na.
- **Gorąca** liczba wystąpień testowych, które są już wdrożone i oczekują na dostęp w wybranym regionie. Klienci mogą natychmiast uzyskać dostęp do tych dysków testowych zamiast czekać na wdrożenie. Jest to, że te wystąpienia są zawsze uruchamiane w ramach subskrypcji platformy Azure, dzięki czemu będą one miały większy koszt przestoju. Zdecydowanie zaleca się, aby istniało **co najmniej jedno aktywne wystąpienie**, ponieważ większość klientów nie chce czekać na zakończenie pełnych wdrożeń, a więc istnieje spadek użycia klienta.
- Liczba **rozgrzanych** wystąpień testów na region, który został wdrożony, a następnie maszyna wirtualna została zatrzymana i zapisana w usłudze Azure Storage. Czas oczekiwania dla wystąpień rozgrzanych jest wolniejszy niż w przypadku aktywnych wystąpień, ale koszt przestoju magazynu jest również tańszy.
- **Zimna** liczba wystąpień testów na region, które mogą zostać wdrożone. Zimne wystąpienia wymagają, aby cały dysk testowy Menedżer zasobów szablon do przechodzenia przez wdrożenie w momencie, gdy klient zażądający dysku testowego, więc jest wolniejsze niż wystąpienia gorąca lub ciepłe. Jednak ten kompromis polega na tym, że płacisz tylko za czas trwania testu.

W tej chwili oblicza łączną liczbę potencjalnych współbieżnych dysków testowych, które mają być dostępne, i sprawdź, czy limit przydziału dla subskrypcji może obsłużyć tę współbieżną ilość:

**(Liczba wybranych regionów x aktywnych wystąpień) + (liczba wybranych regionów x rdzeni) + (liczba wybranych regionów x zimnych wystąpień)**

**Czas trwania dysku testowego (w godzinach) —** *wymagany* czas trwania okresu ważności dysku testowego (w \# godzinach). Po upływie tego czasu test kończy się automatycznie.

**Test Menedżer zasobów szablonu —** *wymagane* przekazanie szablonu Menedżer zasobów w tym miejscu. To jest plik, który został skompilowany w poprzedniej sekcji. Nazwij główny plik szablonu: "Main-Template. JSON" i upewnij się, że szablon Menedżer zasobów zawiera parametry wyjściowe dla wymaganych zmiennych kluczowych. (Musi to być plik. zip)

**Informacje o dostępie —** *wymagane* po otrzymaniu przez klienta dysku testowego są dla nich widoczne informacje o dostępie. Te instrukcje mają na celu udostępnienie przydatnych parametrów wyjściowych na dysku testowym Menedżer zasobów szablonu. Aby uwzględnić parametry wyjściowe, należy użyć podwójnych nawiasów klamrowych (na przykład **{{outputName}}**), które zostaną poprawnie wstawione w lokalizacji. (W tym miejscu zalecane jest formatowanie ciągu HTML do renderowania w frontonie).

### <a name="test-drive-deployment-subscription-details"></a>Szczegóły subskrypcji wdrożenia dysku testowego

Ostatnia sekcja do wypełnienia polega na automatycznym wdrożeniu dysków testowych przez połączenie subskrypcji platformy Azure i usługi Azure Active Directory (AD).

![Szczegóły subskrypcji wdrożenia dysku testowego](./media/azure-resource-manager-test-drive/subdetails1.png)

**Identyfikator subskrypcji platformy Azure —** *wymagane* jest przyznanie dostępu do usług platformy Azure i Azure Portal. Subskrypcja polega na tym, że raportowane jest użycie zasobów, a usługi są rozliczane. Jeśli nie masz jeszcze **oddzielnej** subskrypcji platformy Azure dla dysków testowych, przejdź dalej i utwórz ją. Identyfikatory subskrypcji platformy Azure można znaleźć, logując się do Azure Portal i przechodząc do subskrypcji w menu po lewej stronie. (Przykład: "a83645ac-1234-5ab6-6789-1h234g764ghty")

![Subskrypcje platformy Azure](./media/azure-resource-manager-test-drive/subdetails2.png)

**Identyfikator dzierżawy usługi Azure AD —** *wymagany* , jeśli masz już dostępny identyfikator dzierżawy, możesz go znaleźć poniżej w obszarze\> właściwości — identyfikator katalogu.

![Właściwości Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails3.png)

W przeciwnym razie Utwórz nową dzierżawę w Azure Active Directory.

![Lista dzierżawców Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails4.png)

![Zdefiniuj organizację, domenę i kraj/region dla dzierżawy usługi Azure AD](./media/azure-resource-manager-test-drive/subdetails5.png)

![Potwierdź wybór](./media/azure-resource-manager-test-drive/subdetails6.png)

**Identyfikator aplikacja usługi Azure AD —** *w następnym kroku trzeba utworzyć* i zarejestrować nową aplikację. Ta aplikacja zostanie użyta do wykonania operacji w wystąpieniu dysku testowego.

1. Przejdź do nowo utworzonego katalogu lub już istniejącego katalogu i wybierz pozycję Azure Active Directory w okienku Filtr.
2. Wyszukaj ciąg "Rejestracje aplikacji" i kliknij pozycję "Dodaj"
3. Podaj nazwę aplikacji.
4. Wybierz typ as "Web App/API"
5. Podaj dowolną wartość w adresie URL logowania, w którym\'t nie korzystamy z tego pola.
6. Kliknij przycisk Utwórz.
7. Po utworzeniu aplikacji przejdź do pozycji właściwości —\> Ustaw aplikację jako wielodostępną i naciśnij pozycję Zapisz.

Kliknij pozycję Zapisz. Ostatnim krokiem jest pobranie identyfikatora aplikacji dla tej zarejestrowanej aplikacji i wklejenie go w polu Test Drive tutaj.

![Szczegóły identyfikatora aplikacji usługi Azure AD](./media/azure-resource-manager-test-drive/subdetails7.png)

Przy użyciu aplikacji do wdrożenia w ramach subskrypcji musimy dodać aplikację jako współautora subskrypcji. Poniższe instrukcje są następujące:

1. Przejdź do bloku subskrypcje i wybierz odpowiednią subskrypcję, która jest używana tylko dla wersji testowej.
1. Kliknij pozycję **Kontrola dostępu (IAM)**.
1. Kliknij kartę **przypisania ról** .  ![Dodawanie nowego podmiotu Access Control](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. Kliknij pozycję **Dodaj przypisanie roli**.
1. Ustaw rolę jako **współautor**.
1. Wpisz nazwę aplikacji usługi Azure AD i wybierz aplikację, w której ma zostać przypisana rola.
    ![Dodaj uprawnienia](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. Kliknij przycisk **Zapisz**.

**Klucz aplikacja usługi Azure AD —** to pole jest *wymagane* do wygenerowania klucza uwierzytelniania. W obszarze klucze Dodaj Opis klucza, ustaw czas trwania, aby nigdy nie wygaśnie, a następnie wybierz pozycję Zapisz. **Ważne** jest, aby uniknąć posiadania klucza wygasłego, co spowoduje uszkodzenie dysku testowego w środowisku produkcyjnym. Skopiuj tę wartość i wklej ją do wymaganego pola Test Drive.

![Pokazuje klucze aplikacji usługi Azure AD](./media/azure-resource-manager-test-drive/subdetails8.png)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz wszystkie wypełnione pola dysku testowego, przejdź przez i **ponownie Opublikuj** swoją ofertę. Po przekazaniu certyfikacji na dysku testowym należy skorzystać z szerokiego testowania środowiska klienta w **wersji zapoznawczej** oferty. Uruchom test w interfejsie użytkownika, a następnie otwórz swoją subskrypcję platformy Azure w ramach Azure Portal i sprawdź, czy dyski testowe są w pełni wdrożone prawidłowo.

![Azure Portal](./media/azure-resource-manager-test-drive/subdetails9.png)

Należy pamiętać, że nie należy usuwać żadnych wystąpień testowych, ponieważ są one obsługiwane dla klientów, dzięki czemu usługa Stacja testowa będzie automatycznie czyścić te grupy zasobów po zakończeniu pracy z nią.

Gdy będziesz mieć doświadczenie z ofertą wersji zapoznawczej, teraz możesz **przejść na żywo**. Po opublikowaniu oferty od firmy Microsoft jest dostępny końcowy proces przeglądu, który umożliwia dokładne sprawdzenie całego środowiska. Jeśli z jakiegoś powodu oferta zostanie odrzucona, wyślemy powiadomienie do osoby kontaktowej inżyniera o swojej ofercie wyjaśniającej, co będzie musiało zostać naprawione.

Jeśli masz więcej pytań, szukasz porad dotyczących rozwiązywania problemów lub chcesz, aby test został pomyślnie przeszedł, przejdź do [sekcji często zadawane pytania, rozwiązywanie problemów, & najlepsze rozwiązania](./marketing-and-best-practices.md).
