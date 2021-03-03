---
title: Typy dysków testowych, Microsoft Commercial Marketplace
description: Typy dysków testowych w komercyjnej witrynie Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 06/19/2020
ms.author: trkeya
author: trkeya
ms.openlocfilehash: 2addf415c39691b4e662f304522a418aa8a778c2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101730375"
---
# <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager dysk testowy

Użyj tego typu, jeśli masz ofertę w witrynie Azure Marketplace lub AppSource, ale chcesz utworzyć dysk testowy z tylko zasobami platformy Azure. Szablon Azure Resource Manager (ARM) jest kodowanym kontenerem zasobów platformy Azure, które są projektowane w celu najlepszego reprezentowania rozwiązania. Program Test Drive przyjmuje podany szablon ARM i wdraża wszystkie wymagane przez niego zasoby w grupie zasobów. Jest to jedyna opcja dysku testowego dla oferty maszyny wirtualnej lub aplikacji platformy Azure.

Jeśli nie znasz tego szablonu ARM, zapoznaj [się z tematem Azure Resource Manager?](../azure-resource-manager/management/overview.md) i [zrozumieć strukturę i składnię szablonów ARM](../azure-resource-manager/templates/template-syntax.md) , aby lepiej zrozumieć sposób kompilowania i testowania własnych szablonów.

Aby uzyskać informacje na temat dysku testowego aplikacji **hostowanej** lub **logicznej** , zobacz [co to jest dysk testowy?](what-is-test-drive.md)

## <a name="technical-configuration"></a>Konfiguracja techniczna

Szablon wdrożenia zawiera wszystkie zasoby platformy Azure, które składają się na Twoje rozwiązanie. Produkty, które pasują do tego scenariusza, korzystają tylko z zasobów platformy Azure. Ustaw następujące właściwości w centrum partnerskim:

- **Regiony** (wymagane) — obecnie istnieją 26 regionów obsługiwanych przez platformę Azure, w których można udostępnić dysk testowy. Zwykle warto udostępnić dysk testowy w regionach, w których przewidujesz największą liczbę klientów, dzięki czemu można wybrać najbliższy region dla najlepszej wydajności. Musisz się upewnić, że Twoja subskrypcja może wdrażać wszystkie wymagane przez siebie regiony.

- **Wystąpienia** — wybierz typ (gorąca lub zimna) oraz liczbę dostępnych wystąpień, które będą mnożone przez liczbę regionów, w których oferta jest dostępna.

  - **Gorąca** — ten typ wystąpienia jest wdrożony i oczekuje na dostęp w wybranym regionie. Klienci mogą natychmiast uzyskać dostęp do *gorącego* wystąpienia dysku testowego, a nie muszą czekać na wdrożenie. Jest to, że te wystąpienia są zawsze uruchamiane w ramach subskrypcji platformy Azure, dzięki czemu będą one miały większy koszt przestoju. Zdecydowanie zaleca się, aby miało co najmniej jedno *aktywne* wystąpienie, ponieważ większość *klientów nie chce* czekać na pełne wdrożenia, co powoduje odrzucanie w przypadku użycia klienta w przypadku braku dostępnego wystąpienia.

  - **Zimne** — ten typ wystąpienia reprezentuje łączną liczbę wystąpień, które mogą być wdrożone w poszczególnych regionach. Zimne wystąpienia wymagają, aby cały dysk testowy Menedżer zasobów szablon do wdrożenia, gdy klient zażąda dysku testowego, więc *zimne* wystąpienia są znacznie wolniejsze, aby można było ładować je od *aktywnych* wystąpień. Wadą jest to, że musisz tylko uregulować czas trwania testu, ale *nie* zawsze działa w ramach subskrypcji platformy Azure, tak jak w przypadku wystąpienia *aktywnego* .

- **Test Azure Resource Manager szablonu** — Przekaż plik zip zawierający szablon Azure Resource Manager. Dowiedz się więcej o tworzeniu szablonu Azure Resource Manager w artykule Szybki Start [Tworzenie i wdrażanie szablonów Azure Resource Manager przy użyciu Azure Portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

    > [!note]
    > Aby opublikować pomyślnie, należy sprawdzić poprawność formatowania szablonu ARM. Dwa sposoby to zrobić (1) za pomocą [Narzędzia API online](/rest/api/resources/deployments/validate) lub (2) z [wdrożeniem testowym](../azure-resource-manager/templates/deploy-portal.md).

- **Czas trwania dysku testowego** (wymagane) — wprowadź liczbę godzin aktywności dysku testowego. Po upływie tego czasu test kończy się automatycznie. Używaj tylko liczb całkowitych (na przykład godziny "2" są prawidłowe, "1,5" nie jest).

## <a name="write-the-test-drive-template"></a>Napisz szablon dysku testowego

Po zaprojektowaniu żądanego pakietu zasobów Napisz i skompiluj szablon ARM na dysku testowym. Ze względu na to, że na dysku testowym są uruchamiane wdrożenia w trybie w pełni zautomatyzowanego, szablony stacji testowych mają pewne ograniczenia:

### <a name="parameters"></a>Parametry

Większość szablonów zawiera zestaw parametrów, które definiują nazwy zasobów, rozmiary zasobów (takie jak typy kont magazynu lub rozmiary maszyn wirtualnych), nazwy użytkowników i hasła, nazwy DNS i tak dalej. Podczas wdrażania rozwiązań przy użyciu Azure Portal można ręcznie wypełnić wszystkie te parametry, wybrać dostępne nazwy DNS lub konta magazynu itd.

![Lista parametrów w Azure Resource Manager](media/test-drive/resource-manager-parameters.png)

Jednak Test Drive działa automatycznie, bez interakcji człowieka, dlatego obsługuje tylko ograniczony zestaw kategorii parametrów. Jeśli parametr w szablonie ARM dysku testowego nie należy do jednej z obsługiwanych kategorii, należy zamienić ten parametr na zmienną lub wartość stałą.

Możesz użyć dowolnej prawidłowej nazwy dla parametrów; Test Drive rozpoznaje kategorię parametrów przy użyciu wartości typu Metadata. Określ typ metadanych dla każdego parametru szablonu, w przeciwnym razie szablon nie przejdzie walidacji:

```JSON
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

> [!NOTE]
> Wszystkie parametry są opcjonalne, dlatego jeśli nie chcesz korzystać z żadnego z nich, nie musisz.

### <a name="accepted-parameter-metadata-types"></a>Akceptowane typy metadanych parametrów

| Typ metadanych   | Typ parametru  | Opis     | Przykładowa wartość    |
|---|---|---|---|
| **baseUri**     | ciąg          | Podstawowy identyfikator URI pakietu wdrożeniowego| `https://<..>.blob.core.windows.net/<..>` |
| **uż**    | ciąg          | Nowa losowa nazwa użytkownika.| admin68876      |
| **hasło**    | ciąg zabezpieczony    | Nowe hasło losowe | LP! \^ 2KH ACS     |
| **Identyfikator sesji**   | ciąg          | Identyfikator sesji unikatowego dysku testowego (GUID)    | b8c8693e-5673-449c-badd-257a405a6dee |

#### <a name="baseuri"></a>baseUri

Test Drive Inicjuje ten parametr przy użyciu **podstawowego identyfikatora URI** pakietu wdrożeniowego, aby można było użyć tego parametru do konstruowania identyfikatora URI dowolnego pliku zawartego w pakiecie.

```JSON
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

Użyj tego parametru wewnątrz szablonu, aby utworzyć identyfikator URI dowolnego pliku z pakietu wdrożeniowego na dysku testowym. Poniższy przykład pokazuje, jak utworzyć identyfikator URI połączonego szablonu:

```JSON
"templateLink": {
  "uri": "[concat(parameters('baseuri'),'templates/solution.json')]",
  "contentVersion": "1.0.0.0"
}
```

#### <a name="username"></a>nazwa użytkownika

Test Drive Inicjuje ten parametr przy użyciu nowej losowej nazwy użytkownika:

```JSON
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

Przykładowa wartość: `admin68876`

Możesz użyć losowo lub stałych nazw użytkowników dla rozwiązania.

#### <a name="password"></a>hasło

Test Drive Inicjuje ten parametr przy użyciu nowego hasła losowego:

```JSON
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

Przykładowa wartość:  `Lp!ACS^2kh`

Do rozwiązania można użyć haseł losowych lub stałych.

#### <a name="session-id"></a>Identyfikator sesji

Test Drive Inicjuje ten parametr z unikatowym identyfikatorem GUID reprezentującym identyfikator sesji dysku testowego:

```JSON
"parameters": {
  ...
  "sessionid": {
    "type": "string",
    "metadata": {
      "type": "sessionid",
      "description": "Unique test drive session id."
    }
  },
  ...
}
```

Przykładowa wartość: `b8c8693e-5673-449c-badd-257a405a6dee`

Tego parametru można użyć do unikatowego identyfikowania sesji dysku testowego, jeśli jest to konieczne.

### <a name="unique-names"></a>Unikatowe nazwy

Niektóre zasoby platformy Azure, takie jak konta magazynu lub nazwy DNS, wymagają globalnie unikatowych nazw. Oznacza to, że za każdym razem, gdy Stacja testowa wdraża szablon ARM, tworzy nową grupę zasobów o unikatowej nazwie dla wszystkich zasobów. W związku z tym należy użyć funkcji [uniquestring](../azure-resource-manager/templates/template-functions.md) połączonej z nazwami zmiennych w identyfikatorach grup zasobów, aby wygenerować losowe unikatowe wartości:

```JSON
"variables": {
  ...
  "domainNameLabel": "[concat('contosovm',uniquestring(resourceGroup().id))]",
  "storageAccountName": "[concat('contosodisk',uniquestring(resourceGroup().id))]",
  ...
}
```

Upewnij się, że parametry parametrów/zmiennych () są połączone `contosovm` za pomocą unikatowego ciągu wyjściowego ( `resourceGroup().id` ), ponieważ gwarantuje to unikatowość i niezawodność każdej zmiennej.

Na przykład większość nazw zasobów nie może zaczynać się od cyfry, ale funkcja Unique String może zwrócić ciąg, który rozpoczyna się od cyfry. Dlatego jeśli używasz nieprzetworzonego unikatowego ciągu, wdrożenie zakończy się niepowodzeniem.

Dodatkowe informacje o regułach nazewnictwa zasobów i ograniczeniach można znaleźć w [tym artykule](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

### <a name="deployment-location"></a>Lokalizacja wdrożenia

Możesz sprawdzić, czy dysk jest dostępny w różnych regionach świadczenia usługi Azure. Pomysłem jest umożliwienie użytkownikowi wyboru najbliższego regionu, aby zapewnić środowisko użytkownika Beast.

Gdy Test Drive tworzy wystąpienie laboratorium, zawsze tworzy grupę zasobów w regionie wybranym przez użytkownika, a następnie wykonuje szablon wdrożenia w tym kontekście grupy. Dlatego szablon powinien wybrać lokalizację wdrożenia z grupy zasobów:

```JSON
"variables": {
  ...
  "location": "[resourceGroup().location]",
  ...
}
```

A następnie użyj tej lokalizacji dla każdego zasobu dla określonego wystąpienia laboratorium:

```JSON
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

Upewnij się, że Twoja subskrypcja może wdrożyć wszystkie wybrane zasoby w każdym z wybranych regionów. Upewnij się również, że obrazy maszyn wirtualnych są dostępne we wszystkich regionach, które zostaną włączone. w przeciwnym razie szablon wdrożenia nie będzie działał w niektórych regionach.

### <a name="outputs"></a>Dane wyjściowe

Zwykle z szablonami Menedżer zasobów można wdrożyć bez tworzenia danych wyjściowych. Wynika to z faktu, że znane są wszystkie wartości używane do wypełniania parametrów szablonu i zawsze można ręcznie sprawdzić właściwości dowolnego zasobu.

Jednak dla Menedżer zasobów szablonów, ważne jest, aby powrócić do wszystkich informacji, które są wymagane do uzyskania dostępu do laboratorium (identyfikatorów URI witryny sieci Web, nazw hostów maszyn wirtualnych, nazw użytkowników i haseł). Upewnij się, że wszystkie nazwy wyjściowe są odczytywane, ponieważ te zmienne są prezentowane klientowi.

Nie ma żadnych ograniczeń związanych z danymi wyjściowymi szablonu. Test Drive konwertuje wszystkie wartości wyjściowe na ciągi, więc Jeśli wysyłasz obiekt do danych wyjściowych, użytkownik zobaczy ciąg JSON.

Przykład:

```JSON
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

Nie zapomnij o limitach subskrypcji i usług. Jeśli na przykład chcesz wdrożyć maksymalnie 10 4-rdzeniowe maszyny wirtualne, musisz upewnić się, że subskrypcja używana dla laboratorium umożliwia korzystanie z rdzeni 40. Aby uzyskać więcej informacji na temat limitów subskrypcji i usług platformy Azure, zobacz [limity dotyczące subskrypcji i usług platformy Azure, limity przydziału i ograniczenia](../azure-resource-manager/management/azure-subscription-service-limits.md). Ponieważ można jednocześnie wykonać wiele testów, sprawdź, czy subskrypcja może obsłużyć liczbę rdzeni pomnożonych przez łączną liczbę współbieżnych dysków testowych, które mogą być wykonywane.

### <a name="what-to-upload"></a>Co należy przekazać

Szablon ARM programu Test Drive został przekazany jako plik zip, który może zawierać różne artefakty wdrożenia, ale musi mieć jeden plik o nazwie `main-template.json` . Jest to szablon wdrożenia ARM używany do tworzenia wystąpienia dla laboratorium. Jeśli masz dodatkowe zasoby wykraczające poza ten plik, możesz odwoływać się do nich jako zasoby zewnętrzne wewnątrz szablonu lub dołączyć je do pliku zip.

Podczas publikowania certyfikacji, dysk testowy rozpakuje pakiet wdrożeniowy i umieszcza jego zawartość w kontenerze obiektów BLOB wewnętrznego dysku testowego. Struktura kontenera odzwierciedla strukturę pakietu wdrożeniowego:

| package.zip                       | Kontener obiektów BLOB na dysku testowym         |
|---|---|
| `main-template.json`                | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/main-template.json`  |
| `templates/solution.json`           | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/templates/solution.json` |
| `scripts/warmup.ps1`                | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/scripts/warmup.ps1`  |
|||

Nazywamy identyfikator URI tego podstawowego identyfikatora URI kontenera obiektów BLOB. Ponieważ każda wersja laboratorium ma własny kontener obiektów blob, każda wersja laboratorium ma własny podstawowy identyfikator URI. Na dysku testowym można przekazać podstawowy identyfikator URI niespakowanego pakietu wdrożeniowego do szablonu za pomocą parametrów szablonu.

### <a name="transform-template-examples-for-test-drive"></a>Przykłady szablonów transformacji dla dysku testowego

Proces przekształcania architektury zasobów na dysk testowy Menedżer zasobów szablon może być zniechęcające. Aby uzyskać dodatkową pomoc, zobacz te przykłady, jak najlepiej przetwarzać bieżące szablony wdrożenia na [co to jest dysk testowy?](what-is-test-drive.md#transforming-examples)

## <a name="test-drive-deployment-subscription-details"></a>Szczegóły subskrypcji wdrożenia dysku testowego

Ostatnia sekcja do ukończenia polega na automatycznym wdrożeniu dysków testowych przez połączenie subskrypcji platformy Azure i usługi Azure Active Directory (AD).

![Szczegóły subskrypcji wdrożenia dysku testowego](media/test-drive/deployment-subscription-details.png)

1. Uzyskaj **Identyfikator subskrypcji platformy Azure**. Pozwala to na dostęp do usług platformy Azure i Azure Portal. Subskrypcja polega na tym, że raportowane jest użycie zasobów, a usługi są rozliczane. Jeśli nie masz jeszcze oddzielnej subskrypcji platformy Azure dla dysków testowych, utwórz ją. Identyfikatory subskrypcji platformy Azure (np `1a83645ac-1234-5ab6-6789-1h234g764ghty1` .) można znaleźć, logując się do Azure Portal i wybierając **subskrypcje** z menu po lewej stronie.

   ![Subskrypcje platformy Azure](media/test-drive/azure-subscriptions.png)

2. Uzyskaj **Identyfikator dzierżawy usługi Azure AD**. Jeśli masz już dostępny identyfikator dzierżawy, możesz go znaleźć w **Azure Active Directory**  >  **Właściwości**  >  **katalogu**:

   ![Właściwości Azure Active Directory](media/test-drive/azure-active-directory-properties.png)

   Jeśli nie masz identyfikatora dzierżawy, Utwórz nowy w Azure Active Directory. Aby uzyskać pomoc dotyczącą konfigurowania dzierżawy, zobacz [Szybki Start: Konfigurowanie dzierżawy](../active-directory/develop/quickstart-create-new-tenant.md).

3. **Identyfikator aplikacja usługi Azure AD** — Utwórz i zarejestruj nową aplikację. Ta aplikacja zostanie użyta do wykonania operacji w wystąpieniu dysku testowego.

   1. Przejdź do nowo utworzonego katalogu lub już istniejącego katalogu i wybierz Azure Active Directory w okienku filtru.
   2. Wyszukaj **rejestracje aplikacji** i wybierz pozycję **Dodaj**.
   3. Podaj nazwę aplikacji.
   4. Wybierz **Typ** **aplikacji sieci Web/interfejsu API**.
   5. Podaj dowolną wartość w adresie URL logowania, to pole nie jest używane.
   6. Wybierz przycisk **Utwórz**.
   7. Po utworzeniu aplikacji wybierz pozycję **Właściwości**  >  **Ustaw aplikację jako wiele dzierżawców** , a następnie **Zapisz**.

4. Wybierz pozycję **Zapisz**.

5. Skopiuj identyfikator aplikacji dla tej zarejestrowanej aplikacji i wklej go w polu Testuj dysk.

   ![Szczegóły identyfikatora aplikacji usługi Azure AD](media/test-drive/azure-ad-application-id-detail.png)

6. Ponieważ korzystamy z aplikacji do wdrożenia w ramach subskrypcji, musimy dodać aplikację jako współautor do subskrypcji:

   1. Wybierz typ **subskrypcji** , która jest używana dla dysku testowego.
   1. Wybierz pozycję **Kontrola dostępu (IAM)** .
   1. Wybierz kartę **przypisania ról** , a następnie pozycję **Dodaj przypisanie roli**.

      ![Dodawanie nowego podmiotu Access Control](media/test-drive/access-control-principal.jpg)

   1. Ustaw **rolę** i **Przypisz dostęp do programu** tak, jak pokazano. W polu **Wybierz** wprowadź nazwę aplikacji usługi Azure AD. Wybierz aplikację, do której chcesz przypisać rolę **współautor** .

      ![Dodaj uprawnienia](media/test-drive/access-control-permissions.jpg)

   1. Wybierz pozycję **Zapisz**.

7. Wygeneruj klucz uwierzytelniania **aplikacja usługi Azure AD** . W obszarze **klucze** Dodaj **Opis klucza**, ustaw czas trwania **nigdy nie wygasa** (klucz, który wygasł, spowoduje przerwanie pracy w środowisku produkcyjnym), a następnie wybierz pozycję **Zapisz**. Skopiuj tę wartość i wklej ją do wymaganego pola Test Drive.

![Pokazuje klucze aplikacji usługi Azure AD](media/test-drive/azure-ad-app-keys.png)

## <a name="republish"></a>Ponowne publikowanie

Teraz, po zakończeniu wszystkich pól testowych, należy **ponownie opublikować** swoją ofertę. Po przekazaniu certyfikatu na dysku testowym Przetestuj środowisko klienta w wersji zapoznawczej oferty:

1. Uruchom test w interfejsie użytkownika.
1. Otwórz subskrypcję platformy Azure w ramach Azure Portal.
1. Sprawdź, czy twój dysk testowy jest poprawnie wdrażany.

   ![Azure Portal](media/test-drive/azure-portal.png)

Nie usuwaj żadnych wystąpień testowych, dla których Zainicjowano obsługę klientów; Usługa dysku testowego automatycznie czyści te grupy zasobów po zakończeniu działania klienta.

Gdy będziesz mieć doświadczenie z ofertą wersji zapoznawczej, **przejdziesz na żywo**. Istnieje ostateczny proces przeglądu, który umożliwia podwójne sprawdzenie całego środowiska kompleksowego. Jeśli odrzucimy ofertę, wyślemy wiadomość e-mail z zespołem inżynieryjnym dotyczącym oferty wyjaśniającej, co należy naprawić.

## <a name="next-steps"></a>Następne kroki

- Jeśli zostały podane instrukcje dotyczące tworzenia oferty w centrum partnerskim, użyj strzałki wstecz, aby powrócić do tego tematu.
- Dowiedz się więcej o innych typach dysków testowych na [co to jest dysk testowy?](what-is-test-drive.md)