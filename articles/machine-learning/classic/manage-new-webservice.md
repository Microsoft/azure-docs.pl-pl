---
title: 'ML Studio (klasyczny): zarządzanie usługami sieci Web — Azure'
description: Zarządzaj Machine Learning nowymi i klasycznymi usługami sieci Web za pomocą portalu Microsoft Azure Machine Learning Web Services. Ponieważ klasyczne usługi sieci Web i nowe usługi sieci Web bazują na różnych podstawowych technologiach, są nieco inne możliwości zarządzania dla każdego z nich.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 02/28/2017
ms.openlocfilehash: d3aea23c816b9d8b32b0563d3116cdc2b92e080f
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101094306"
---
# <a name="manage-a-web-service-using-the-azure-machine-learning-studio-classic-web-services-portal"></a>Zarządzanie usługą sieci Web przy użyciu portalu usług sieci Web Azure Machine Learning Studio (klasyczny)

**dotyczy:** ![ Dotyczy. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasyczny) nie ma ![ zastosowania do.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


Machine Learning nowych i klasycznych usług sieci Web można zarządzać przy użyciu portalu Microsoft Azure Machine Learning Web Services. Ponieważ klasyczne usługi sieci Web i nowe usługi sieci Web bazują na różnych podstawowych technologiach, są nieco inne możliwości zarządzania dla każdego z nich.

W portalu Machine Learning Web Services można:

* Monitoruj, w jaki sposób usługa sieci Web jest używana.
* Skonfiguruj opis, zaktualizuj klucze usługi sieci Web (tylko nowe), Aktualizuj klucz konta magazynu (tylko nowe), Włącz rejestrowanie i Włącz lub Wyłącz przykładowe dane.
* Usuń usługę sieci Web.
* Tworzenie, usuwanie lub aktualizowanie planów rozliczeń (tylko nowe).
* Dodawanie i usuwanie punktów końcowych (tylko wersja klasyczna)

>[!NOTE]
>Można także zarządzać klasycznymi usługami sieci Web w [Machine Learning Studio (klasyczny)](https://studio.azureml.net) na karcie **usługi sieci Web** .

## <a name="permissions-to-manage-new-resources-manager-based-web-services"></a>Uprawnienia do zarządzania nowymi zasobami na podstawie usług sieci Web opartych na Menedżerze zasobów

Nowe usługi sieci Web są wdrażane jako zasoby platformy Azure. W związku z tym musisz mieć odpowiednie uprawnienia do wdrażania nowych usług sieci Web i zarządzania nimi.  Aby wdrożyć nowe usługi sieci Web lub zarządzać nimi, musisz mieć przypisaną rolę współautora lub administratora w subskrypcji, w której wdrożono usługę sieci Web. Jeśli zapraszasz innego użytkownika do obszaru roboczego usługi Machine Learning, musisz przypisać je do roli współautora lub administratora w subskrypcji, aby można było wdrożyć usługi sieci Web lub zarządzać nimi. 

Jeśli użytkownik nie ma odpowiednich uprawnień dostępu do zasobów w portalu usług sieci Web Azure Machine Learning, wystąpi następujący błąd podczas próby wdrożenia usługi sieci Web:

*Wdrożenie usługi sieci Web nie powiodło się. To konto nie ma wystarczających uprawnień dostępu do subskrypcji platformy Azure, która zawiera obszar roboczy. Aby można było wdrożyć usługę sieci Web na platformie Azure, to samo konto musi zostać zaproszone do obszaru roboczego i mieć dostęp do subskrypcji platformy Azure, która zawiera obszar roboczy.*

Aby uzyskać więcej informacji na temat tworzenia obszaru roboczego, zobacz temat [Tworzenie i udostępnianie obszaru roboczego Azure Machine Learning Studio (klasycznego)](create-workspace.md).

Aby uzyskać więcej informacji na temat ustawiania uprawnień dostępu, zobacz [Przypisywanie ról platformy Azure przy użyciu Azure Portal](../../role-based-access-control/role-assignments-portal.md).


## <a name="manage-new-web-services"></a>Zarządzanie nowymi usługami sieci Web
Aby zarządzać nowymi usługami sieci Web:

1. Zaloguj się do portalu [usług sieci Web Microsoft Azure Machine Learning](https://services.azureml.net/quickstart) przy użyciu konta Microsoft Azure — Użyj konta skojarzonego z subskrypcją platformy Azure.
2. W menu kliknij pozycję **usługi sieci Web**.

Zostanie wyświetlona lista wdrożonych usług sieci Web dla Twojej subskrypcji. 

Aby zarządzać usługą sieci Web, kliknij przycisk usługi sieci Web. Na stronie usługi sieci Web możesz:

* Kliknij usługę sieci Web, aby ją zarządzać.
* Kliknij plan rozliczeń dla usługi sieci Web, aby go zaktualizować.
* Usuń usługę sieci Web.
* Skopiuj usługę sieci Web i Wdróż ją w innym regionie.

Po kliknięciu usługi sieci Web zostanie otwarta strona szybkiego startu usługi sieci Web. Na stronie szybkiego startu usługi sieci Web dostępne są dwie opcje menu umożliwiające zarządzanie usługą sieci Web:

* **Pulpit nawigacyjny** — umożliwia wyświetlenie użycia usługi sieci Web.
* **Konfiguracja** — umożliwia dodawanie tekstu opisowego, aktualizowanie klucza dla konta magazynu skojarzonego z usługą sieci Web oraz włączanie lub wyłączanie przykładowych danych.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Monitorowanie sposobu korzystania z usługi sieci Web
Kliknij kartę **pulpit nawigacyjny** .

Na pulpicie nawigacyjnym możesz wyświetlać ogólne użycie usługi sieci Web w danym okresie czasu. Możesz wybrać okres do wyświetlenia z menu rozwijanego okres w prawym górnym rogu wykresów użycia. Na pulpicie nawigacyjnym są wyświetlane następujące informacje:

* **Żądania w czasie** przedstawiają wykres etapowy liczby żądań w wybranym okresie. Może pomóc w ustaleniu, czy występuje wzrost użycia.
* **Żądania żądanie-odpowiedź** zawiera łączną liczbę wywołań Request-Response odebranych przez usługę w wybranym okresie i ich liczbę nie powiodła się.
* **Średni czas obliczeń Request-Response** wyświetla średni czas wymagany do wykonania odebranych żądań.
* **Żądania usługi Batch** zawierają informacje o całkowitej liczbie żądań wsadowych odebranych przez usługę w wybranym okresie oraz o tym, ile z nich zakończyło się niepowodzeniem.
* **Średnie opóźnienie zadania** przedstawia średni czas wymagany do wykonania odebranych żądań.
* **Błędy** przedstawiają zagregowaną liczbę błędów, które wystąpiły podczas wywołań usługi sieci Web.
* **Koszty usług** przedstawiają opłaty za plan rozliczeniowy skojarzony z usługą.

### <a name="configuring-the-web-service"></a>Konfigurowanie usługi sieci Web
Kliknij opcję **Konfiguruj** menu.

Można zaktualizować następujące właściwości:

* **Opis** umożliwia wprowadzenie opisu usługi sieci Web.
* **Tytuł** umożliwia wprowadzenie tytułu usługi sieci Web
* **Klucze** umożliwiają obracanie podstawowych i pomocniczych kluczy interfejsu API.
* **Klucz konta magazynu** umożliwia zaktualizowanie klucza konta magazynu skojarzonego z usługą sieci Web. 
* **Włącz przykładowe dane** umożliwiają dostarczenie przykładowych danych, których można użyć do przetestowania usługi Request-Response. Jeśli usługa sieci Web została utworzona w Machine Learning Studio (klasyczny), przykładowe dane są pobierane z danych używanych do uczenia modelu. Jeśli usługa została utworzona programowo, dane są pobierane z przykładowych danych dostarczonych jako część pakietu JSON.

### <a name="managing-billing-plans"></a>Zarządzanie planami rozliczeń
Kliknij opcję **plany** menu na stronie szybkiego startu usług sieci Web. Możesz również kliknąć plan skojarzony z określoną usługą sieci Web, aby zarządzać tym planem.

* **Nowy** umożliwia utworzenie nowego planu.
* **Dodawanie/usuwanie wystąpienia planu** pozwala na "skalować w poziomie" istniejący plan w celu dodania pojemności.
* **Uaktualnianie/obniżanie** poziomu pozwala na "skalowanie w górę" istniejącego planu w celu dodania pojemności.
* Wartość **Usuń** umożliwia usunięcie planu.

Kliknij plan, aby wyświetlić jego pulpit nawigacyjny. Pulpit nawigacyjny umożliwia tworzenie migawek lub Planowanie użycia w wybranym okresie. Aby wybrać okres do wyświetlenia, kliknij listę rozwijaną **okres** w prawym górnym rogu pulpitu nawigacyjnego. 

Pulpit nawigacyjny plan zawiera następujące informacje:

* **Opis planu** zawiera informacje o kosztach i pojemności skojarzone z planem.
* **Planowanie użycia** przedstawia liczbę transakcji i godzin obliczeniowych, które zostały naliczone względem planu.
* **Usługi sieci Web** wyświetlają liczbę usług sieci Web, które korzystają z tego planu.
* **Najpopularniejsza usługa sieci Web według wywołań** wyświetla cztery pierwsze usługi sieci Web, które powodują naliczanie opłat za plan.
* **Najważniejsze usługi sieci Web według obliczeń godz** . przedstawia cztery pierwsze usługi sieci Web, które używają zasobów obliczeniowych, które są naliczane zgodnie z planem.

## <a name="manage-classic-web-services"></a>Zarządzanie klasycznymi usługami sieci Web
> [!NOTE]
> Procedury przedstawione w tej sekcji dotyczą zarządzania klasycznymi usługami sieci Web za pomocą portalu Azure Machine Learning Web Services. Aby uzyskać informacje na temat zarządzania klasycznymi usługami sieci Web za pomocą Machine Learning Studio (klasyczny) i Azure Portal, zobacz [Zarządzanie obszarem roboczym programu Azure Machine Learning Studio (klasycznego)](manage-workspace.md).
> 
> 

Aby zarządzać klasycznymi usługami sieci Web:

1. Zaloguj się do portalu [usług sieci Web Microsoft Azure Machine Learning](https://services.azureml.net/quickstart) przy użyciu konta Microsoft Azure — Użyj konta skojarzonego z subskrypcją platformy Azure.
2. W menu kliknij pozycję **klasyczne usługi sieci Web**.

Aby zarządzać klasyczną usługą sieci Web, kliknij pozycję **klasyczne usługi sieci Web**. Na stronie klasycznej usługi sieci Web możesz:

* Kliknij usługę sieci Web, aby wyświetlić skojarzone punkty końcowe.
* Usuń usługę sieci Web.

W przypadku zarządzania klasyczną usługą sieci Web poszczególne punkty końcowe są zarządzane oddzielnie. Po kliknięciu usługi sieci Web na stronie usługi sieci Web zostanie otwarta lista punktów końcowych skojarzonych z usługą. 

Na stronie klasycznego punktu końcowego usługi sieci Web można dodawać i usuwać punkty końcowe usługi. Aby uzyskać więcej informacji na temat dodawania punktów końcowych, zobacz [Tworzenie punktów końcowych](create-endpoint.md).

Kliknij jeden z punktów końcowych, aby otworzyć stronę szybkiego startu usługi sieci Web. Na stronie Szybki Start dostępne są dwie opcje menu umożliwiające zarządzanie usługą sieci Web:

* **Pulpit nawigacyjny** — umożliwia wyświetlenie użycia usługi sieci Web.
* **Konfiguracja** — umożliwia dodawanie tekstu opisowego, Włączanie i wyłączanie rejestrowania błędów, aktualizowanie klucza dla konta magazynu skojarzonego z usługą sieci Web oraz włączanie i wyłączanie przykładowych danych.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Monitorowanie sposobu korzystania z usługi sieci Web
Kliknij kartę **pulpit nawigacyjny** .

Na pulpicie nawigacyjnym możesz wyświetlać ogólne użycie usługi sieci Web w danym okresie czasu. Możesz wybrać okres do wyświetlenia z menu rozwijanego okres w prawym górnym rogu wykresów użycia. Na pulpicie nawigacyjnym są wyświetlane następujące informacje:

* **Żądania w czasie** przedstawiają wykres etapowy liczby żądań w wybranym okresie. Może pomóc w ustaleniu, czy występuje wzrost użycia.
* **Żądania żądanie-odpowiedź** zawiera łączną liczbę wywołań Request-Response odebranych przez usługę w wybranym okresie i ich liczbę nie powiodła się.
* **Średni czas obliczeń Request-Response** wyświetla średni czas wymagany do wykonania odebranych żądań.
* **Żądania usługi Batch** zawierają informacje o całkowitej liczbie żądań wsadowych odebranych przez usługę w wybranym okresie oraz o tym, ile z nich zakończyło się niepowodzeniem.
* **Średnie opóźnienie zadania** przedstawia średni czas wymagany do wykonania odebranych żądań.
* **Błędy** przedstawiają zagregowaną liczbę błędów, które wystąpiły podczas wywołań usługi sieci Web.
* **Koszty usług** przedstawiają opłaty za plan rozliczeniowy skojarzony z usługą.

### <a name="configuring-the-web-service"></a>Konfigurowanie usługi sieci Web
Kliknij opcję **Konfiguruj** menu.

Można zaktualizować następujące właściwości:

* **Opis** umożliwia wprowadzenie opisu usługi sieci Web. Opis jest polem wymaganym.
* **Rejestrowanie** umożliwia włączenie lub wyłączenie rejestrowania błędów w punkcie końcowym. Aby uzyskać więcej informacji na temat rejestrowania, zobacz Włączanie [rejestrowania dla usług sieci web Machine Learning](web-services-logging.md).
* **Włącz przykładowe dane** umożliwiają dostarczenie przykładowych danych, których można użyć do przetestowania usługi Request-Response. Jeśli usługa sieci Web została utworzona w Machine Learning Studio (klasyczny), przykładowe dane są pobierane z danych używanych do uczenia modelu. Jeśli usługa została utworzona programowo, dane są pobierane z przykładowych danych dostarczonych jako część pakietu JSON.