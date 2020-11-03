---
title: Przeszukaj zaszyfrowaną zawartość magazynu obiektów blob platformy Azure
titleSuffix: Azure Cognitive Search
description: Dowiedz się, jak indeksować i wyodrębniać tekst z zaszyfrowanych dokumentów w usłudze Azure Blob Storage przy użyciu usługi Azure Wyszukiwanie poznawcze.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/02/2020
ms.openlocfilehash: f0295c27f1d193b0dcd7829a11b4aabe0edb659b
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286348"
---
# <a name="how-to-index-encrypted-blobs-using-blob-indexers-and-skillsets-in-azure-cognitive-search"></a>Jak indeksować zaszyfrowane obiekty blob za pomocą indeksatorów obiektów blob i umiejętności na platformie Wyszukiwanie poznawcze Azure

W tym artykule pokazano, jak za pomocą [usługi azure wyszukiwanie poznawcze](search-what-is-azure-search.md) indeksować dokumenty, które zostały wcześniej zaszyfrowane w ramach [usługi Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) przy użyciu [Azure Key Vault](../key-vault/general/overview.md). Zwykle indeksator nie może wyodrębnić zawartości z szyfrowanych plików, ponieważ nie ma dostępu do klucza szyfrowania. Jednak korzystając z niestandardowej umiejętności [DecryptBlobFile](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile) , a następnie [DocumentExtractionSkill](cognitive-search-skill-document-extraction.md), można zapewnić dostęp do klucza do odszyfrowania plików, a następnie z nich z nich korzystać. Spowoduje to odblokowanie możliwości indeksowania tych dokumentów bez naruszenia stanu szyfrowania przechowywanych dokumentów.

Począwszy od wcześniej zaszyfrowanych całych dokumentów (tekstu bez struktury), takich jak PDF, HTML, DOCX i PPTX w usłudze Azure Blob Storage, w tym przewodniku jest używany program SMS i interfejsy API REST wyszukiwania do wykonywania następujących zadań:

> [!div class="checklist"]
> * Zdefiniuj potok, który odszyfruje dokumenty i wyodrębni z nich tekst.
> * Zdefiniuj indeks do przechowywania danych wyjściowych.
> * Wykonaj potok, aby utworzyć i załadować indeks.
> * Eksplorowanie wyników przy użyciu wyszukiwania pełnotekstowego i zaawansowanej składni zapytań.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Otwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne

W tym przykładzie przyjęto założenie, że pliki zostały już przekazane do usługi Azure Blob Storage i zaszyfrowane w procesie. Jeśli potrzebujesz pomocy przy wstępnym przekazywaniu i zaszyfrowaniu plików, zapoznaj się z [tym samouczkiem](../storage/blobs/storage-encrypt-decrypt-blobs-key-vault.md) , aby dowiedzieć się, jak to zrobić.

+ [Azure Storage](https://azure.microsoft.com/services/storage/)
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) w tej samej subskrypcji co usługa Azure wyszukiwanie poznawcze. Magazyn kluczy musi mieć włączoną ochronę **nietrwałego usuwania** i **przeczyszczania** .
+ [Wyszukiwanie poznawcze platformy Azure](search-create-service-portal.md) w [warstwie rozliczeniowej](search-sku-tier.md#tiers) (podstawowa lub wyższa, w dowolnym regionie)
+ [Funkcja platformy Azure](https://azure.microsoft.com/services/functions/)
+ [Aplikacja klasyczna narzędzia Postman](https://www.getpostman.com/)

## <a name="1---create-services-and-collect-credentials"></a>1 — Tworzenie usług i zbieranie poświadczeń

### <a name="set-up-the-custom-skill"></a>Skonfiguruj niestandardową umiejętność

W tym przykładzie używamy projektu przykładowej [DecryptBlobFile](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile) z repozytorium usługi GitHub [Azure Search](https://github.com/Azure-Samples/azure-search-power-skills) . W tej sekcji zostanie wdrożona umiejętność dla funkcji platformy Azure, która będzie mogła być używana w zestawu umiejętności. Wbudowany skrypt wdrażania tworzy zasób funkcji platformy Azure o nazwie rozpoczynając od **psdbf-Function-App-** i ładuje umiejętność. Zostanie wyświetlony monit o podanie subskrypcji i grupy zasobów. Pamiętaj, aby wybrać tę samą subskrypcję, w której znajduje się wystąpienie Azure Key Vault.

W rezultacie umiejętność DecryptBlobFile Pobiera adres URL i token sygnatury dostępu współdzielonego dla każdego obiektu BLOB jako dane wejściowe i pobiera pobrany, odszyfrowany plik przy użyciu kontraktu odwołania do pliku, którego oczekuje platforma Azure Wyszukiwanie poznawcze. Odwołaj to DecryptBlobFile wymaga klucza szyfrowania w celu wykonania odszyfrowywania. W ramach konfiguracji utworzysz również zasady dostępu, które przyznają dostęp funkcji DecryptBlobFile do klucza szyfrowania w Azure Key Vault.

1. Kliknij przycisk **Wdróż na platformie Azure** znajdujący się na [stronie docelowej DecryptBlobFile](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile#deployment), która spowoduje otwarcie podanego szablonu Menedżer zasobów w Azure Portal.

1. Wybierz **subskrypcję, w której istnieje wystąpienie Azure Key Vault** (ten przewodnik nie będzie działał w przypadku wybrania innej subskrypcji), a następnie wybierz istniejącą grupę zasobów lub Utwórz nową (Jeśli tworzysz nową, musisz również wybrać region do wdrożenia).

1. Wybierz pozycję **Recenzja + Utwórz** , upewnij się, że zgadzasz się na warunki, a następnie wybierz pozycję **Utwórz** , aby wdrożyć funkcję platformy Azure.

    ![Szablon ARM w portalu](media/indexing-encrypted-blob-files/arm-template.jpg "Szablon ARM w portalu")

1. Poczekaj na zakończenie wdrożenia.

1. Przejdź do wystąpienia Azure Key Vault w portalu. [Utwórz zasady dostępu](../key-vault/general/assign-access-policy-portal.md) w Azure Key Vault, które udzielają kluczowego dostępu do niestandardowej umiejętności.
 
    1. W obszarze **Ustawienia** wybierz pozycję **zasady dostępu** , a następnie wybierz pozycję **Dodaj zasady dostępu** .
     
       ![Dodawanie zasad dostępu do magazynu kluczy](media/indexing-encrypted-blob-files/keyvault-access-policies.jpg "Zasady dostępu magazynu kluczy")

    1. W obszarze **Konfiguruj z szablonu** wybierz pozycję **Azure Data Lake Storage lub Azure Storage**.

    1. Dla podmiotu zabezpieczeń wybierz wdrożone wystąpienie funkcji platformy Azure. Można wyszukać go przy użyciu prefiksu zasobu, który został użyty do utworzenia go w kroku 2, który ma domyślną wartość prefiksu **psdbf-Function-App**.

    1. Nie zaznaczaj niczego dla opcji autoryzowanej aplikacji.
     
        ![Szablon dodawania zasad dostępu do magazynu kluczy](media/indexing-encrypted-blob-files/keyvault-add-access-policy.jpg "Szablon zasad dostępu magazynu kluczy")

    1. Pamiętaj, aby kliknąć przycisk **Zapisz** na stronie zasady dostępu przed przejściem do rzeczywistego dodawania zasad dostępu.
     
         ![Zasady dostępu zapisywania magazynu kluczy](media/indexing-encrypted-blob-files/keyvault-save-access-policy.jpg "Zapisz zasady dostępu do magazynu kluczy")

1. Przejdź do funkcji **psdbf-Function-App** w portalu i zanotuj następujące właściwości, ponieważ będą one potrzebne w dalszej części przewodnika:

    1. Adres URL funkcji, który można znaleźć w obszarze **podstawy** na stronie głównej dla funkcji.
    
        ![Adres URL funkcji](media/indexing-encrypted-blob-files/function-uri.jpg "Gdzie można znaleźć adres URL funkcji platformy Azure")

    1. Kod klucza hosta, który można znaleźć, przechodząc do **kluczy aplikacji** , klikając, aby wyświetlić klucz **domyślny** i kopiując wartość.
     
        ![Kod klucza hosta funkcji](media/indexing-encrypted-blob-files/function-host-key.jpg "Gdzie znaleźć kod klucza hosta funkcji platformy Azure")

### <a name="cognitive-services"></a>Cognitive Services

Wzbogacanie AI i wykonywanie zestawu umiejętności są obsługiwane przez Cognitive Services, w tym analiza tekstu i przetwarzanie obrazów na potrzeby przetwarzania języka naturalnego i obrazu. Jeśli celem było ukończenie rzeczywistego prototypu lub projektu, w tym momencie należy zastanowić się, Cognitive Services (w tym samym regionie co usługa Azure Wyszukiwanie poznawcze), aby można było dołączyć go do operacji indeksowania.

W tym ćwiczeniu można jednak pominąć Inicjowanie obsługi zasobów, ponieważ usługa Azure Wyszukiwanie poznawcze może nawiązać połączenie Cognitive Services w tle i zapewnić 20 bezpłatnych transakcji dla indeksatora. Po przetworzeniu 20 dokumentów, indeksator zakończy się niepowodzeniem, chyba że klucz Cognitive Services zostanie dołączony do zestawu umiejętności. W przypadku większych projektów Zaplanuj obsługę Cognitive Services w warstwie płatność zgodnie z rzeczywistym użyciem. Aby uzyskać więcej informacji, zobacz [Attach Cognitive Services](cognitive-search-attach-cognitive-services.md). Należy pamiętać, że klucz Cognitive Services jest wymagany do uruchomienia zestawu umiejętności z więcej niż 20 dokumentów nawet wtedy, gdy żadna z wybranych umiejętności poznawczych nie łączy się z Cognitive Services (na przykład w przypadku podanej zestawu umiejętności, jeśli nie zostaną do niej dodane żadne umiejętności).

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Ostatnim składnikiem jest usługa Azure Wyszukiwanie poznawcze, którą można [utworzyć w portalu](search-create-service-portal.md). Aby ukończyć ten przewodnik, możesz skorzystać z warstwy Bezpłatna. 

Podobnie jak w przypadku funkcji platformy Azure, poświęć chwilę na zebranie klucza administratora. Ponadto po rozpoczęciu tworzenia struktury żądań należy podać punkt końcowy i klucz interfejsu API administratora używany do uwierzytelniania każdego żądania.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Pobierz klucz API i adres URL administratora dla usługi Azure Wyszukiwanie poznawcze

1. [Zaloguj się do Azure Portal](https://portal.azure.com/)i na stronie **Przegląd** usługi wyszukiwania Pobierz nazwę usługi wyszukiwania. Nazwę usługi można potwierdzić, przeglądając adres URL punktu końcowego. Jeśli adres URL punktu końcowego to `https://mydemo.search.windows.net` , nazwa usługi to `mydemo` .

2. W obszarze **Ustawienia**  >  **klucze** Uzyskaj klucz administratora dla pełnych praw do usługi. Istnieją dwa wymienne klucze administratora zapewniające ciągłość działania w przypadku, gdy trzeba ją wycofać. W przypadku żądań dotyczących dodawania, modyfikowania i usuwania obiektów można użyć klucza podstawowego lub pomocniczego.

   ![Pobieranie nazwy usługi i administratora oraz kluczy zapytań](media/search-get-started-javascript/service-name-and-keys.png)

Wszystkie żądania wymagają klucza API-Key w nagłówku każdego żądania wysyłanego do usługi. Prawidłowy klucz ustanawia zaufanie dla każdego żądania, między aplikacją wysyłającą żądanie a usługą, która go obsługuje.

## <a name="2---set-up-postman"></a>2 — Konfigurowanie wpisu

Zainstaluj i skonfiguruj program ogłaszający.

### <a name="download-and-install-postman"></a>Pobierz i zainstaluj program Poster

1. Pobierz [kod źródłowy kolekcji Poster](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/index-encrypted-blobs/Index%20encrypted%20Blob%20files.postman_collection.json).
1. Wybierz **File** pozycję  >  **Importuj** plik, aby zaimportować kod źródłowy do programu Poster.
1. Wybierz kartę **kolekcje** , a następnie wybierz przycisk **...** (wielokropek).
1. Wybierz pozycję **Edit** (Edytuj). 
   
   ![Aplikacja do publikowania po wyświetleniu nawigacji](media/indexing-encrypted-blob-files/postman-edit-menu.jpg "Przejdź do menu Edycja w programie Poster")
1. W oknie dialogowym **Edycja** wybierz kartę **zmienne** . 

Na karcie **zmienne** można dodawać wartości, które zaswapki są księgowane w każdym momencie napotkania określonej zmiennej wewnątrz podwójnych nawiasów klamrowych. Na przykład, program Poster zastępuje symbol `{{admin-key}}` wartością bieżącą ustawioną przez użytkownika `admin-key` . Program ogłaszający tworzy podstawienie w adresach URL, nagłówkach, treści żądania i tak dalej. 

Aby uzyskać wartość dla `admin-key` , użyj zanotowanego wcześniej klucza API usługi Azure wyszukiwanie poznawcze administrator. Ustaw `search-service-name` nazwę usługi Wyszukiwanie poznawcze platformy Azure, której używasz. Ustaw `storage-connection-string` wartość przy użyciu wartości na karcie **klucze dostępu** konta magazynu i ustaw `storage-container-name` nazwę kontenera obiektów BLOB na tym koncie magazynu, w którym są przechowywane zaszyfrowane pliki. Ustaw `function-uri` na adres URL funkcji platformy Azure, który zanotowano wcześniej, i ustaw go `function-code` na kod klucza hosta funkcji platformy Azure, który zanotowano wcześniej. Można pozostawić wartości domyślne dla innych wartości.

![Karta zmiennych aplikacji po wprowadzeniu](media/indexing-encrypted-blob-files/postman-variables-window.jpg "Okno zmiennych autora")


| Zmienna    | Skąd uzyskać |
|-------------|-----------------|
| `admin-key` | Na stronie **klucze** usługi Azure wyszukiwanie poznawcze.  |
| `search-service-name` | Nazwa usługi Azure Wyszukiwanie poznawcze. Adres URL to `https://{{search-service-name}}.search.windows.net` . | 
| `storage-connection-string` | Na koncie magazynu na karcie **klucze dostępu** wybierz pozycję **Klucz1**  >  **Parametry połączenia**. | 
| `storage-container-name` | Nazwa kontenera obiektów blob, który ma zaszyfrowane pliki do indeksowania. | 
| `function-uri` |  W funkcji platformy Azure w obszarze **podstawy** na stronie głównej. | 
| `function-code` | W funkcji platformy Azure, przechodząc do pozycji **klucze aplikacji** , klikając, aby wyświetlić klucz **domyślny** i kopiując wartość. | 
| `api-version` | Pozostaw jako **2020-06-30**. |
| `datasource-name` | Pozostaw jako **zaszyfrowane obiekty blob-ds**. | 
| `index-name` | Pozostaw jako **zaszyfrowane obiekty blob-IDX**. | 
| `skillset-name` | Pozostaw jako **zaszyfrowane obiekty blob-SS**. | 
| `indexer-name` | Pozostaw jako **zaszyfrowane obiekty blob-IXR**. | 

### <a name="review-the-request-collection-in-postman"></a>Przeglądanie kolekcji żądań w programie Poster

Po uruchomieniu tego przewodnika należy wydać cztery żądania HTTP: 

- **Umieść żądanie utworzenia indeksu** : ten indeks zawiera dane używane przez usługę Azure wyszukiwanie poznawcze i zwraca.
- **Opublikuj żądanie utworzenia źródła danych** : to źródło danych łączy usługę Azure wyszukiwanie poznawcze z kontem magazynu i z tego powodu zaszyfrowanymi plikami obiektów BLOB. 
- **Umieść żądanie utworzenia zestawu umiejętności** : zestawu umiejętności określa niestandardową definicję umiejętności dla funkcji platformy Azure, która będzie odszyfrować dane pliku obiektu BLOB, a [DocumentExtractionSkill](cognitive-search-skill-document-extraction.md) do wyodrębnienia tekstu z każdego dokumentu po jego odszyfrowaniu.
- **Umieść żądanie utworzenia indeksatora** : uruchomienie indeksatora odczytuje dane, stosuje zestawu umiejętności i przechowuje wyniki. Należy uruchomić to żądanie jako ostatnie.

[Kod źródłowy](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/index-encrypted-blobs/Index%20encrypted%20Blob%20files.postman_collection.json) zawiera kolekcję programu Poster, która ma cztery żądania, a także kilka przydatnych żądań uzupełniających. Aby wystawić żądania, w programie Poster wybierz kartę dla żądań i wybierz pozycję **Wyślij** dla każdego z nich.

## <a name="3---monitor-indexing"></a>3 — monitorowanie indeksowania

Indeksowanie i wzbogacenie rozpoczyna się zaraz po przesłaniu żądania utworzenia indeksatora. W zależności od liczby dokumentów na koncie magazynu indeksowanie może chwilę potrwać. Aby dowiedzieć się, czy indeksator nadal działa, użyj żądania **Get indeksatora** dostarczonego jako część kolekcji Poster i przejrzyj odpowiedź, aby dowiedzieć się, czy indeksator jest uruchomiony, lub wyświetlić informacje o błędach i ostrzeżeniach.  

W przypadku korzystania z warstwy Bezpłatna jest oczekiwany następujący komunikat: `"Could not extract content or metadata from your document. Truncated extracted text to '32768' characters"` . Ten komunikat jest wyświetlany, ponieważ indeksowanie obiektów BLOB w warstwie Bezpłatna ma [limit 32 KB na ekstrakcji znaków](search-limits-quotas-capacity.md#indexer-limits). Ten komunikat dla tego zestawu danych nie zostanie wyświetlony w wyższych warstwach. 

## <a name="4---search"></a>4 — Wyszukiwanie

Po zakończeniu wykonywania indeksatora można uruchomić niektóre zapytania, aby sprawdzić, czy dane zostały pomyślnie odszyfrowane i indeksowane. Przejdź do usługi Azure Wyszukiwanie poznawcze w portalu i Użyj [Eksploratora wyszukiwania](search-explorer.md) do uruchamiania zapytań dotyczących indeksowanych danych.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy zaszyfrowane pliki zostały pomyślnie zindeksowane, możesz wykonać [iterację w tym potoku, dodając więcej umiejętności poznawczych](cognitive-search-defining-skillset.md). Dzięki temu można wzbogacić się i uzyskać dodatkowe informacje dotyczące danych.

Jeśli pracujesz z pozostałymi zaszyfrowanymi danymi, możesz chcieć zbadać funkcje szyfrowania indeksu dostępne w usłudze Azure Wyszukiwanie poznawcze. Mimo że indeksator wymaga odszyfrowania danych do celów indeksowania, gdy indeks istnieje, można go zaszyfrować przy użyciu klucza zarządzanego przez klienta. Dzięki temu dane będą zawsze szyfrowane, gdy są przechowywane. Aby uzyskać więcej informacji, zobacz [Konfigurowanie kluczy zarządzanych przez klienta do szyfrowania danych w usłudze Azure wyszukiwanie poznawcze](search-security-manage-encryption-keys.md).