---
title: Jak skanować zasobniki usługi Amazon S3
description: W tym przewodniku opisano szczegółowe informacje dotyczące skanowania zasobników usługi Amazon S3.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 04/21/2021
ms.custom: references_regions
ms.openlocfilehash: 75a7cba1e47509e3186ab519d0d8ca82dd315373
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815526"
---
# <a name="azure-purview-connector-for-amazon-s3"></a>Łącznik Azure Purview dla usługi Amazon S3

Ten przewodnik zawiera wyjaśnienie, jak używać usługi Azure Purview do skanowania danych bez struktury przechowywanych obecnie w standardowych zasobnikach usługi Amazon S3 i odnajdywania typów poufnych informacji w danych. W tym przewodniku opisano również sposób identyfikowania zasobników usługi Amazon S3, w których dane są obecnie przechowywane w celu zapewnienia łatwej ochrony informacji i zapewnienia zgodności danych.

W przypadku tej usługi użyj programu Purview, aby zapewnić konto Microsoft dostępu do usług AWS, na których zostanie uruchomiony skaner Purview. Skaner Purview używa tego dostępu do zasobników usługi Amazon S3 do odczytywania danych, a następnie raportuje wyniki skanowania, w tym tylko metadane i klasyfikację, na platformę Azure. Raporty klasyfikacji i etykietowania programu Purview mogą analizować i przeglądać wyniki skanowania danych.

W tym przewodniku dowiesz się, jak dodać zasobniki usługi Amazon S3 jako zasoby programu Purview i utworzyć skanowanie danych usługi Amazon S3.

## <a name="purview-scope-for-amazon-s3"></a>Zakres purview dla usługi Amazon S3

Poniższy zakres jest specyficzny dla rejestrowania i skanowania zasobników usługi Amazon S3 jako źródeł danych Purview.

|Zakres  |Opis  |
|---------|---------|
|**Limity danych**     |    Usługa skanera Purview obecnie obsługuje skanowanie zasobników usługi Amazon S3 w celu przechowywania do 100 GB danych na dzierżawę.     |
|**Typy plików**     | Usługa skanera Purview obsługuje obecnie następujące typy plików: <br><br>.avro, .csv, .doc, .docm, .docx, .dot, .json, .odp, .ods, .ppt, .orc, .parquet, .pdf, .pot, .pps, .ppsx, .ppt, .pptm, .pptx, .psv, .ssv, .tsv, .txt, .xlc, .xls, .xlsb, .xlsm, .xlsx, .xlsx, .xlt, .xml        |
|**Regiony**     | Łącznik Purview dla usługi Amazon S3 jest obecnie wdrażany tylko w regionach **AWS Wschodnie stany USA (Ohio)** i **Europa (Londyn).** <br><br>Aby uzyskać więcej informacji, zobacz [Storage and scanning regions (Przechowywanie i skanowanie regionów).](#storage-and-scanning-regions)   |
|     |         |

Aby uzyskać więcej informacji, zobacz udokumentowane limity programu Purview w:

- [Zarządzanie i zwiększanie limitów przydziału zasobów za pomocą usługi Azure Purview](how-to-manage-quotas.md)
- [Obsługiwane źródła danych i typy plików w usłudze Azure Purview](sources-and-scans.md)
- [Używanie prywatnych punktów końcowych dla konta usługi Purview](catalog-private-link.md)
### <a name="storage-and-scanning-regions"></a>Regiony magazynowania i skanowania

W poniższej tabeli mapowane są regiony, w których są przechowywane dane, na region, w którym będą skanowane przez usługę Azure Purview.

> [!IMPORTANT]
> Klienci zostaną naliczone opłaty za wszystkie powiązane opłaty za transfer danych zgodnie z regionem ich zasobnika.
>

| Region magazynu | Region skanowania |
| ------------------------------- | ------------------------------------- |
| Wschodnie stany USA (Ohio)                  | Wschodnie stany USA (Ohio)                        |
| Wschodnie usa (N. Wirginia)           | Wschodnie usa (N. Wirginia)                       |
| Zachodnie stany USA (N. Kalifornia)         | Wschodnie stany USA (Ohio)                        |
| Zachodnie stany USA (Oregon)                | Wschodnie stany USA (Ohio)                        |
| Afryka (Kapsztad)              | Europa (Zabłocie)                    |
| Azja i Pacyfik (Hongkong)        | Azja i Pacyfik (Sydney)                   |
| Azja i Pacyfik (Mumbaj)           | Azja i Pacyfik (Sydney)                   |
| Azja i Pacyfik (Osaka-Local)      | Azja i Pacyfik (Sydney)                   |
| Azja i Pacyfik (Seul)            | Azja i Pacyfik (Sydney)                   |
| Azja i Pacyfik (Singapur)        | Azja i Pacyfik (Sydney)                   |
| Azja i Pacyfik (Sydney)           | Azja i Pacyfik (Sydney)                  |
| Azja i Pacyfik (Tokio)            | Azja i Pacyfik (Sydney)                 |
| Kanada (środkowa)                | Wschodnie stany USA (Ohio)                        |
| Chiny (Waszyngton)                 | Nieobsługiwane                    |
| Chiny (Ningxia)                 | Nieobsługiwane                   |
| Europa (Zabłocie)              | Europa (Zabłocie)                    |
| Europa (Irlandia)                | Europa (Irlandia)                   |
| Europa (Londyn)                 | Europa (Irlandia)                   |
| Europa (Zabłocie)                  | Europa (Zabłocie)                    |
| Europa (Paryż)                  | Europa (Zabłocie)                    |
| Europa (Włochy)              | Europa (Zabłocie)                    |
| Bliski Wschód (Azja)           | Europa (Zabłocie)                    |
| Ameryka Południowa (São Paulo)       | Wschodnie stany USA (Ohio)                        |
| | |

## <a name="prerequisites"></a>Wymagania wstępne

Przed dodaniem zasobników usługi Amazon S3 jako źródeł danych Purview i skanowaniem danych S3 upewnij się, że zostały spełnione następujące wymagania wstępne.

> [!div class="checklist"]
> * Musisz być administratorem źródła danych usługi Azure Purview.
> * [Utwórz konto programu Purview,](#create-a-purview-account) jeśli jeszcze go nie masz
> * [Tworzenie poświadczeń programu Purview dla skanowania zasobnika usług AWS](#create-a-purview-credential-for-your-aws-bucket-scan)
> * [Tworzenie nowej roli AWS do użycia z usługą Purview](#create-a-new-aws-role-for-purview)
> * [Konfigurowanie skanowania w celu zaszyfrowania zasobników usługi Amazon S3,](#configure-scanning-for-encrypted-amazon-s3-buckets)jeśli ma to zastosowanie
> * Podczas dodawania zasobników jako zasobów programu Purview potrzebne będą wartości [](#retrieve-your-amazon-s3-bucket-name)nazwy magazynu ARN usług [AWS,](#retrieve-your-new-role-arn)nazwy zasobnika, a czasami identyfikatora konta [AWS.](#locate-your-aws-account-id)

### <a name="create-a-purview-account"></a>Tworzenie konta programu Purview

- **Jeśli masz już konto usługi Purview,** możesz kontynuować konfiguracje wymagane do obsługi usług AWS S3. Rozpocznij od [utworzenia poświadczeń programu Purview dla skanowania zasobnika usług AWS.](#create-a-purview-credential-for-your-aws-bucket-scan)

- **Jeśli musisz utworzyć konto programu Purview,** postępuj zgodnie z instrukcjami w tesłudze Tworzenie wystąpienia konta [usługi Azure Purview.](create-catalog-portal.md) Po utworzeniu konta wróć tutaj, aby ukończyć konfigurację i rozpocząć korzystanie z łącznika purview dla usługi Amazon S3.

### <a name="create-a-purview-credential-for-your-aws-bucket-scan"></a>Tworzenie poświadczeń programu Purview dla skanowania zasobnika usług AWS

W tej procedurze opisano sposób tworzenia nowego poświadczenia programu Purview do użycia podczas skanowania zasobników usług AWS.

> [!TIP]
> Podczas konfigurowania skanowania możesz również utworzyć nowe poświadczenie w [trakcie procesu](#create-a-scan-for-one-or-more-amazon-s3-buckets). W takim przypadku w polu **Poświadczenie** wybierz pozycję **Nowy.**
>

1. W aplikacji Purview przejdź do **Centrum zarządzania** i w obszarze Zabezpieczenia **i dostęp** wybierz **pozycję Poświadczenia.**

1. Wybierz **pozycję** Nowy i w okienku **Nowe poświadczenia,** które zostanie wyświetlone po prawej stronie, użyj następujących pól, aby utworzyć poświadczenia programu Purview:

    |Pole |Opis  |
    |---------|---------|
    |**Nazwa**     |Wprowadź znaczącą nazwę dla tego poświadczenia lub użyj wartości domyślnej.        |
    |**Opis**     |Wprowadź opcjonalny opis tego poświadczenia, na przykład `Used to scan the tutorial S3 buckets`         |
    |**Metoda uwierzytelniania**     |Wybierz pozycję Role ARN (Rola **ARN),** ponieważ używasz roli ARN do uzyskiwania dostępu do zasobnika.         |
    |**konto Microsoft identyfikator**     |Kliknij, aby skopiować tę wartość do schowka. Użyj tej wartości jako **identyfikatora konto Microsoft podczas** tworzenia roli [ARN na platformie AWS.](#create-a-new-aws-role-for-purview)           |
    |**Identyfikator zewnętrzny**     |Kliknij, aby skopiować tę wartość do schowka. Użyj tej wartości jako identyfikatora **zewnętrznego** podczas [tworzenia roli ARN na platformie AWS.](#create-a-new-aws-role-for-purview)        |
    |**Role ARN**     | Po utworzeniu [roli usługi Amazon IAM](#create-a-new-aws-role-for-purview)przejdź do swojej roli w obszarze IAM, skopiuj wartość Role **ARN** i wprowadź ją tutaj. Na przykład: `arn:aws:iam::284759281674:role/S3Role`. <br><br>Aby uzyskać więcej informacji, zobacz [Pobieranie nowej funkcji ARN roli](#retrieve-your-new-role-arn). |
    | | |

    Po **zakończeniu** tworzenia poświadczeń wybierz pozycję Utwórz.

1. Jeśli nie zostało to jeszcze skopiowane, skopiuj i wklej wartości **identyfikatorów konto Microsoft** i **Identyfikator** zewnętrzny do użycia podczas tworzenia nowej roli AWS dla programu [Purview](#create-a-new-aws-role-for-purview), co jest następnym krokiem.

Aby uzyskać więcej informacji na temat poświadczeń programu Purview, zobacz Credentials for source authentication in Azure Purview (Poświadczenia [do uwierzytelniania źródłowego w usłudze Azure Purview).](manage-credentials.md)

### <a name="create-a-new-aws-role-for-purview"></a>Tworzenie nowej roli AWS dla aplikacji Purview

Ta procedura wymaga wprowadzenia wartości dla identyfikatora konta platformy Azure i identyfikatora zewnętrznego podczas tworzenia roli platformy AWS.

Jeśli nie masz tych wartości, zlokalizuj je najpierw w [poświadczeniu programu Purview.](#create-a-purview-credential-for-your-aws-bucket-scan)

**Aby zlokalizować identyfikator konta Microsoft i identyfikator zewnętrzny:**

1. W aplikacji Purview przejdź do strony **Zabezpieczenia centrum zarządzania** i uzyskaj dostęp  >  **do**  >  **poświadczeń**.

1. Wybierz poświadczenia utworzone na [podstawie skanowania zasobnika usług AWS,](#create-a-purview-credential-for-your-aws-bucket-scan)a następnie na pasku narzędzi wybierz pozycję **Edytuj**.

1. W **okienku** Edytowanie poświadczeń, które jest wyświetlane po prawej stronie, skopiuj wartości **identyfikatorów konto Microsoft i** **Identyfikator** zewnętrzny do oddzielnego pliku lub przydają się one do wklejania do odpowiedniego pola na platformie AWS.

    Na przykład:

    [![Zlokalizuj konto Microsoft identyfikatorów i identyfikatorów zewnętrznych. ](./media/register-scan-amazon-s3/locate-account-id-external-id.png)](./media/register-scan-amazon-s3/locate-account-id-external-id.png#lightbox)


**Aby utworzyć rolę AWS dla aplikacji Purview:**

1.  Otwórz **konsolę Amazon Web Services,** a następnie w obszarze **Zabezpieczenia, tożsamość i zgodność** wybierz pozycję **IAM**.

1. Wybierz **pozycję Role,** a następnie **pozycję Utwórz rolę.**

1. Wybierz **pozycję Another AWS account (Inne konto AWS),** a następnie wprowadź następujące wartości:

    |Pole  |Opis  |
    |---------|---------|
    |**Identyfikator konta**     |    Wprowadź identyfikator konta Microsoft. Na przykład: `615019938638`     |
    |**Identyfikator zewnętrzny**     |   W obszarze opcji wybierz **pozycję Wymagaj identyfikatora zewnętrznego...,** a następnie wprowadź identyfikator zewnętrzny w wyznaczonym polu. <br>Na przykład: `e7e2b8a3-0a9f-414f-a065-afaf4ac6d994`     |
    | | |

    Na przykład:

    ![Dodaj identyfikator konta Microsoft do konta platformy AWS.](./media/register-scan-amazon-s3/aws-create-role-amazon-s3.png)

1. W obszarze **Tworzenie roli > Dołącz** zasady uprawnień odfiltruj wyświetlane uprawnienia do usługi **S3.** Wybierz **pozycję AmazonS3ReadOnlyAccess,** a następnie wybierz pozycję **Dalej: Tagi**.

    ![Wybierz zasady ReadOnlyAccess dla nowej roli skanowania Amazon S3.](./media/register-scan-amazon-s3/aws-permission-role-amazon-s3.png)

    > [!IMPORTANT]
    > Zasady **AmazonS3ReadOnlyAccess** zapewniają minimalne uprawnienia wymagane do skanowania zasobników usługi S3 i mogą również obejmować inne uprawnienia.
    >
    >Aby zastosować tylko minimalne uprawnienia wymagane do skanowania zasobników, utwórz nowe zasady z uprawnieniami wymienionymi w tece Minimalne uprawnienia dla zasad [usług AWS](#minimum-permissions-for-your-aws-policy)w zależności od tego, czy chcesz przeskanować pojedynczy zasobnik, czy wszystkie zasobniki na koncie. 
    >
    >Zastosuj nowe zasady do roli zamiast **AmazonS3ReadOnlyAccess.**

1. W **obszarze Dodaj tagi (opcjonalnie)** możesz opcjonalnie utworzyć znaczący tag dla tej nowej roli. Przydatne tagi umożliwiają organizowanie, śledzenie i kontrolowanie dostępu dla każdej tworzyć roli.

    Wprowadź nowy klucz i wartość tagu zgodnie z potrzebami. Gdy wszystko będzie gotowe lub jeśli chcesz pominąć ten krok, wybierz pozycję **Dalej:** Przegląd, aby przejrzeć szczegóły roli i ukończyć tworzenie roli.

    ![Dodaj znaczący tag do organizowania, śledzenia lub kontrolowania dostępu dla nowej roli.](./media/register-scan-amazon-s3/add-tag-new-role.png)

1. W obszarze **Przegląd** wykonaj następujące czynności:

    - W **polu Nazwa roli** wprowadź znaczącą nazwę roli
    - W **polu Opis** roli wprowadź opcjonalny opis, aby zidentyfikować przeznaczenie roli
    - W sekcji **Zasady** upewnij się, że do roli są dołączone poprawne zasady **(AmazonS3ReadOnlyAccess).**

    Następnie wybierz **pozycję Utwórz rolę,** aby ukończyć proces.

    Na przykład:

    ![Przejrzyj szczegóły przed utworzeniem roli.](./media/register-scan-amazon-s3/review-role.png)


### <a name="configure-scanning-for-encrypted-amazon-s3-buckets"></a>Konfigurowanie skanowania w celu zaszyfrowania zasobników usługi Amazon S3

Zasobniki usług AWS obsługują wiele typów szyfrowania. W przypadku zasobników, które używają **szyfrowania AWS-KMS,** do włączenia skanowania jest wymagana specjalna konfiguracja.

> [!NOTE]
> W przypadku zasobników, które nie korzystają z szyfrowania, szyfrowania AES-256 lub AWS-KMS S3, pomiń tę sekcję i przejdź do sekcji Pobieranie nazwy zasobnika [Usługi Amazon S3.](#retrieve-your-amazon-s3-bucket-name)
>

**Aby sprawdzić typ szyfrowania używany w zasobnikach usługi Amazon S3:**

1. Na platformie AWS przejdź do pozycji **Storage**  >  **S3** > wybierz pozycję **Zasobniki** z menu po lewej stronie.

    ![Wybierz kartę Zasobniki usługi Amazon S3.](./media/register-scan-amazon-s3/check-encryption-type-buckets.png)

1. Wybierz zasobnik, który chcesz sprawdzić. Na stronie szczegółów zasobnika wybierz **kartę Właściwości** i przewiń w dół do obszaru **Domyślne** szyfrowanie.

    - Jeśli wybrany zasobnik jest skonfigurowany dla czegokolwiek oprócz szyfrowania **AWS-KMS,** w tym jeśli domyślne szyfrowanie zasobnika ma wartość **Wyłączone,** pomiń pozostałą część tej procedury i kontynuuj pobieranie nazwy zasobnika [Amazon S3.](#retrieve-your-amazon-s3-bucket-name)

    - Jeśli wybrany zasobnik jest skonfigurowany do szyfrowania **AWS-KMS,** kontynuuj zgodnie z poniższym opisem, aby dodać nowe zasady, które umożliwiają skanowanie zasobnika za pomocą niestandardowego szyfrowania **AWS-KMS.**

    Na przykład:

    ![Wyświetlanie zasobnika usługi Amazon S3 skonfigurowanego z szyfrowaniem AWS-KMS](./media/register-scan-amazon-s3/default-encryption-buckets.png)

**Aby dodać nowe zasady umożliwiające skanowanie zasobnika przy użyciu niestandardowego szyfrowania AWS-KMS:**

1. Na platformie AWS przejdź do **opcji Services**  >   IAM Policies (Zasady **IAM**  >   **usług)** i wybierz **pozycję Create policy (Utwórz zasady).**

1. Na karcie **Tworzenie zasad**  >  **Edytor wizualizacji** zdefiniuj zasady przy użyciu następujących wartości:

    |Pole  |Opis  |
    |---------|---------|
    |**Usługa**     |  Wprowadź i wybierz **pozycję KMS**.       |
    |**Akcje**     | W **obszarze Poziom dostępu** wybierz pozycję **Zapis,** aby rozwinąć **sekcję** Zapis.<br>Po rozwinięciu wybierz tylko opcję **Odszyfruj.**        |
    |**Zasoby**     |Wybierz określony zasób lub **pozycję Wszystkie zasoby.**         |
    | | |

    Gdy wszystko będzie gotowe, wybierz pozycję **Przejrzyj zasady,** aby kontynuować.

    ![Utwórz zasady skanowania zasobnika przy użyciu szyfrowania AWS-KMS.](./media/register-scan-amazon-s3/create-policy-kms.png)

1. Na stronie **Przeglądanie zasad** wprowadź zrozumiałą nazwę zasad i opcjonalny opis, a następnie wybierz pozycję **Utwórz zasady.**

    Nowo utworzone zasady zostaną dodane do listy zasad.

1. Dołącz nowe zasady do roli dodanej do skanowania.

    1. Wróć do strony **Role IAM**  >   i wybierz wcześniej dodaną [rolę.](#create-a-new-aws-role-for-purview)

    1. Na karcie **Uprawnienia** wybierz pozycję **Dołącz zasady.**

        ![Na karcie Uprawnienia swojej roli wybierz pozycję Dołącz zasady.](./media/register-scan-amazon-s3/iam-attach-policies.png)

    1. Na stronie **Dołączanie uprawnień** wyszukaj i wybierz nowe zasady utworzone powyżej. Wybierz **pozycję Dołącz zasady,** aby dołączyć zasady do roli.

        Strona **Podsumowanie** zostanie zaktualizowana, a nowe zasady zostaną dołączone do Twojej roli.

        ![Wyświetl zaktualizowaną stronę Podsumowanie z nowymi zasadami dołączonymi do roli.](./media/register-scan-amazon-s3/attach-policy-role.png)

### <a name="retrieve-your-new-role-arn"></a>Pobieranie nowej funkcji ARN roli

Podczas tworzenia skanowania zasobnika [Usługi Amazon S3](#create-a-scan-for-one-or-more-amazon-s3-buckets)należy zarejestrować rolę ARN platformy AWS i skopiować ją do programu Purview.

**Aby pobrać rolę ARN:**

1. W obszarze Role zarządzania tożsamościami i dostępem **(IAM)** usług AWS wyszukaj i wybierz nową rolę  >   utworzoną [dla programu Purview.](#create-a-purview-credential-for-your-aws-bucket-scan)

1. Na stronie Podsumowanie **roli** wybierz przycisk Kopiuj do **schowka** po prawej stronie **wartości role ARN.**

    ![Skopiuj wartość ARN roli do schowka.](./media/register-scan-amazon-s3/aws-copy-role-purview.png)

1. Wklej tę wartość w bezpiecznej lokalizacji, gotowej do użycia podczas tworzenia [skanowania zasobnika usługi Amazon S3.](#create-a-scan-for-one-or-more-amazon-s3-buckets)

### <a name="retrieve-your-amazon-s3-bucket-name"></a>Pobieranie nazwy zasobnika usługi Amazon S3

Nazwa zasobnika usługi Amazon S3 będzie potrzebna do skopiowania go do aplikacji Purview podczas tworzenia skanowania [zasobnika usługi Amazon S3](#create-a-scan-for-one-or-more-amazon-s3-buckets)

**Aby pobrać nazwę zasobnika:**

1. Na platformie AWS przejdź do pozycji **Storage**  >  **S3** > wybierz pozycję **Zasobniki** z menu po lewej stronie.

    ![Wyświetl kartę Zasobniki usługi Amazon S3.](./media/register-scan-amazon-s3/check-encryption-type-buckets.png)

1. Wyszukaj i wybierz zasobnik, aby wyświetlić stronę szczegółów zasobnika, a następnie skopiuj nazwę zasobnika do schowka.

    Na przykład:

    ![Pobierz i skopiuj adres URL zasobnika S3.](./media/register-scan-amazon-s3/retrieve-bucket-url-amazon.png)

    Wklej nazwę zasobnika w bezpiecznym pliku i dodaj do niego prefiks, aby utworzyć wartość, która należy wprowadzić podczas konfigurowania zasobnika jako zasobu `s3://` programu Purview.

    Na przykład: `s3://purview-tutorial-bucket`

> [!NOTE]
> Tylko poziom główny zasobnika jest obsługiwany jako źródło danych Purview. Na przykład następujący adres URL, który zawiera podfolder, *nie jest* obsługiwany: `s3://purview-tutorial-bucket/view-data`
>

### <a name="locate-your-aws-account-id"></a>Lokalizowanie identyfikatora konta usług AWS

Identyfikator konta usług AWS będzie potrzebny do zarejestrowania konta usług AWS jako źródła danych Purview wraz ze wszystkimi jego zasobnikami.

Identyfikator konta usług AWS jest identyfikatorem, za pomocą których logujesz się do konsoli usług AWS. Można go również znaleźć po zalogowaniu się na pulpicie nawigacyjnym IAM, po lewej stronie pod opcjami nawigacji i u góry jako numeryczną część adresu URL logowania:

Na przykład:

![Pobierz identyfikator konta usług AWS.](./media/register-scan-amazon-s3/aws-locate-account-id.png)


## <a name="add-a-single-amazon-s3-bucket-as-a-purview-resource"></a>Dodawanie pojedynczego zasobnika usługi Amazon S3 jako zasobu purview

Użyj tej procedury, jeśli masz tylko jeden zasobnik S3, który chcesz zarejestrować w aplikacji Purview jako źródło danych, lub jeśli masz wiele zasobników na koncie usług AWS, ale nie chcesz rejestrować ich wszystkich w aplikacji Purview.

**Aby dodać zasobnik:** 

1. Uruchom portal Purview przy użyciu dedykowanego łącznika Purview dla adresu URL usługi Amazon S3. Ten adres URL został dostarczony przez zespół zarządzania produktem łącznika Amazon S3 Purview.

    ![Uruchom portal Programu Purview.](./media/register-scan-amazon-s3/purview-portal-amazon-s3.png)

1. Przejdź do strony Źródła **usługi** Azure Purview i wybierz **ikonę Zarejestruj** ![ się.](./media/register-scan-amazon-s3/register-button.png) > **Amazon S3**  >  **Kontynuuj**.

    ![Dodaj zasobnik amazon AWS jako źródło danych Purview.](./media/register-scan-amazon-s3/add-s3-datasource-to-purview.png)

    > [!TIP]
    > Jeśli masz wiele [kolekcji](manage-data-sources.md#manage-collections) i chcesz dodać usługę Amazon  S3 do określonej kolekcji, wybierz widok Mapy w prawym górnym rogu, a następnie wybierz ikonę **Zarejestruj.** ![](./media/register-scan-amazon-s3/register-button.png) wewnątrz kolekcji.
    >

1. W **okienku Rejestrowanie źródeł (Amazon S3)** wprowadź następujące informacje:

    |Pole  |Opis  |
    |---------|---------|
    |**Nazwa**     |Wprowadź znaczącą nazwę lub użyj podanej wartości domyślnej.         |
    |**Adres URL zasobnika**     | Wprowadź adres URL zasobnika usługi AWS przy użyciu następującej składni:   `s3://<bucketName>`     <br><br>**Uwaga:** Upewnij się, że używasz tylko poziomu głównego zasobnika, bez żadnych podfolderów. Aby uzyskać więcej informacji, zobacz [Pobieranie nazwy zasobnika Usługi Amazon S3.](#retrieve-your-amazon-s3-bucket-name) |
    |**Wybieranie kolekcji** |Jeśli wybrano opcję zarejestrowania źródła danych w kolekcji, ta kolekcja jest już wymieniona. <br><br>Wybierz inną kolekcję zgodnie z potrzebami,  wybierz pozycję **Brak,** aby nie przypisać kolekcji, lub pozycję Nowa, aby utworzyć nową kolekcję. <br><br>Aby uzyskać więcej informacji na temat kolekcji purview, zobacz [Manage data sources in Azure Purview (Zarządzanie źródłami danych w usłudze Azure Purview).](manage-data-sources.md#manage-collections)|
    | | |

    Gdy wszystko będzie gotowe, wybierz pozycję **Zakończ,** aby ukończyć rejestrację.

Przejdź do [tematu Tworzenie skanowania dla co najmniej jednego zasobnika usługi Amazon S3.](#create-a-scan-for-one-or-more-amazon-s3-buckets).

## <a name="add-an-amazon-account-as-a-purview-resource"></a>Dodawanie konta Amazon jako zasobu purview

Użyj tej procedury, jeśli masz wiele zasobników S3 na koncie Amazon i chcesz zarejestrować wszystkie z nich jako źródła danych Purview.

Podczas [konfigurowania skanowania](#create-a-scan-for-one-or-more-amazon-s3-buckets)będzie można wybrać określone zasobniki do przeskanowania, jeśli nie chcesz skanować ich wszystkich razem.

**Aby dodać konto Amazon:**
1. Uruchom portal Purview przy użyciu dedykowanego łącznika Purview dla adresu URL usługi Amazon S3. Ten adres URL został dostarczony przez zespół zarządzania produktem łącznika Amazon S3 Purview.

    ![Launch Connector for Amazon S3 dedicated Purview portal (Uruchamianie łącznika dla dedykowanego portalu Purview usługi Amazon S3)](./media/register-scan-amazon-s3/purview-portal-amazon-s3.png)

1. Przejdź do strony Źródła **usługi** Azure Purview i wybierz **ikonę Zarejestruj** ![ się.](./media/register-scan-amazon-s3/register-button.png) > **Konta Amazon**  >  **Kontynuuj**.

    ![Dodaj konto Amazon jako źródło danych Purview.](./media/register-scan-amazon-s3/add-s3-account-to-purview.png)

    > [!TIP]
    > Jeśli masz wiele [kolekcji](manage-data-sources.md#manage-collections) i chcesz dodać usługę Amazon  S3 do określonej kolekcji, wybierz widok Mapy w prawym górnym rogu, a następnie wybierz ikonę **Zarejestruj.** ![](./media/register-scan-amazon-s3/register-button.png) wewnątrz kolekcji.
    >

1. W **okienku Rejestrowanie źródeł (Amazon S3)** wprowadź następujące informacje:

    |Pole  |Opis  |
    |---------|---------|
    |**Nazwa**     |Wprowadź znaczącą nazwę lub użyj podanej wartości domyślnej.         |
    |**Identyfikator konta usług AWS**     | Wprowadź identyfikator konta usług AWS. Aby uzyskać więcej informacji, zobacz [Lokalizowanie identyfikatora konta usług AWS](#locate-your-aws-account-id)|
    |**Wybieranie kolekcji** |Jeśli wybrano opcję zarejestrowania źródła danych z kolekcji, ta kolekcja jest już wymieniona. <br><br>Wybierz inną kolekcję zgodnie z potrzebami,  **pozycję Brak,** aby nie przypisać kolekcji, lub pozycję Nowa, aby teraz utworzyć nową kolekcję. <br><br>Aby uzyskać więcej informacji na temat kolekcji purview, zobacz Manage data sources in Azure Purview (Zarządzanie [źródłami danych w usłudze Azure Purview).](manage-data-sources.md#manage-collections)|
    | | |

    Gdy wszystko będzie gotowe, wybierz pozycję **Zakończ,** aby ukończyć rejestrację.

Przejdź do [tematu Tworzenie skanowania dla co najmniej jednego zasobnika usługi Amazon S3.](#create-a-scan-for-one-or-more-amazon-s3-buckets)

## <a name="create-a-scan-for-one-or-more-amazon-s3-buckets"></a>Tworzenie skanowania dla co najmniej jednego zasobnika usługi Amazon S3

Po dodaniu zasobników jako źródeł danych programu Purview można skonfigurować skanowanie do uruchamiania w zaplanowanych odstępach czasu lub natychmiast.

1. Przejdź do obszaru Źródła **usługi** Azure Purview, a następnie wykonaj jedną z następujących czynności:

    - W widoku **mapy wybierz** pozycję Nowe **skanowanie Ikona** Nowego ![ skanowania.](./media/register-scan-amazon-s3/new-scan-button.png) w polu źródła danych.
    - W widoku **Lista umieść** wskaźnik myszy na wierszu źródła danych, a następnie wybierz pozycję **Nowe skanowanie** Ikona ![ nowego skanowania. ](./media/register-scan-amazon-s3/new-scan-button.png) .

1. W **okienku Skanuj...,** które zostanie otwarte po prawej stronie, zdefiniuj następujące pola, a następnie wybierz pozycję **Kontynuuj:**

    |Pole  |Opis  |
    |---------|---------|
    |**Nazwa**     |  Wprowadź zrozumiałą nazwę skanowania lub użyj wartości domyślnej.       |
    |**Typ** |Wyświetlane tylko w przypadku dodania konta usług AWS ze wszystkimi zasobnikami. <br><br>Bieżące opcje obejmują tylko **wszystkie usługi**  >  **Amazon S3.** Bądź na bieżąco, aby uzyskać więcej opcji do wyboru w przypadku rozszerzania macierzy obsługi aplikacji Purview. |
    |**Poświadczenie**     |  Wybierz poświadczenie Purview z twoją rolą ARN. <br><br>**Porada:** Jeśli chcesz utworzyć nowe poświadczenie w tej chwili, wybierz pozycję **Nowy.** Aby uzyskać więcej informacji, zobacz Create a Purview credential for your AWS bucket scan (Tworzenie poświadczeń [programu Purview dla skanowania zasobnika usług AWS).](#create-a-purview-credential-for-your-aws-bucket-scan)     |
    | **Amazon S3**    |   Wyświetlane tylko w przypadku dodania konta usług AWS ze wszystkimi dołączonymi zasobnikami. <br><br>Wybierz co najmniej jeden zasobnik do skanowania lub wybierz pozycję **Zaznacz** wszystko, aby przeskanować wszystkie zasobniki na koncie.      |
    | | |

    Program Purview automatycznie sprawdza, czy rola ARN jest prawidłowa oraz czy zasobniki i obiekty w zasobnikach są dostępne, a następnie kontynuują pracę w przypadku powodzenia połączenia.

    > [!TIP]
    > Aby wprowadzić różne wartości i przetestować połączenie samodzielnie przed kontynuowaniem, wybierz pozycję **Test connection** u dołu po prawej stronie przed wybraniem **przycisku Kontynuuj.**
    >

1. W **okienku Wybierz zestaw reguł** skanowania wybierz domyślny zestaw reguł **usługi AmazonS3** lub wybierz pozycję **Nowy** zestaw reguł skanowania, aby utworzyć nowy niestandardowy zestaw reguł. Po wybraniu zestawu reguł wybierz pozycję **Kontynuuj.**

    Jeśli wybierzesz opcję utworzenia nowego niestandardowego zestawu reguł skanowania, użyj kreatora, aby zdefiniować następujące ustawienia:

    |Pane  |Opis  |
    |---------|---------|
    |**Nowy zestaw reguł skanowania** /<br>**Opis reguły skanowania**    |   Wprowadź znaczącą nazwę i opcjonalny opis zestawu reguł      |
    |**Wybieranie typów plików**     | Wybierz wszystkie typy plików, które chcesz uwzględnić podczas skanowania, a następnie wybierz pozycję **Kontynuuj.**<br><br>Aby dodać nowy typ pliku, wybierz pozycję **Nowy typ pliku** i zdefiniuj następujące elementy: <br>- Rozszerzenie pliku, które chcesz dodać <br>— Opcjonalny opis  <br>- Określa, czy zawartość pliku ma niestandardowy ogranicznik, czy jest typem pliku systemowego. Następnie wprowadź niestandardowy ogranicznik lub wybierz systemowy typ pliku. <br><br>Wybierz **pozycję Utwórz,** aby utworzyć niestandardowy typ pliku.     |
    |**Wybieranie reguł klasyfikacji**     |   Przejdź do i wybierz reguły klasyfikacji, które chcesz uruchomić w zestawie danych.      |
    |     |         |

    Gdy **wszystko** będzie gotowe, wybierz pozycję Utwórz, aby utworzyć zestaw reguł.

1. W **okienku Ustawianie wyzwalacza skanowania** wybierz jedną z następujących opcji, a następnie wybierz pozycję **Kontynuuj:**

    - **Cykliczne** konfigurowanie harmonogramu skanowania cyklicznego
    - **Raz,** aby skonfigurować skanowanie, które rozpoczyna się natychmiast

1. W **okienku Przeglądanie** skanowania sprawdź szczegóły skanowania, aby potwierdzić, że  są poprawne, a  następnie wybierz pozycję Zapisz lub Zapisz i **uruchom,** jeśli w poprzednim okienku wybrano pozycję Raz.

    > [!NOTE]
    > Po jego zakończeniu skanowanie może potrwać do 24 godzin. Będziesz mieć możliwość przejrzenia  raportów szczegółowych informacji i przeszukania katalogu 24 godziny po każdym skanowaniu.
    >

Aby uzyskać więcej informacji, zobacz [Explore Purview scanning results (Eksplorowanie wyników skanowania w aplikacji Purview).](#explore-purview-scanning-results)

## <a name="explore-purview-scanning-results"></a>Eksplorowanie wyników skanowania w aplikacji Purview

Po zakończeniu skanowania programu Purview w zasobnikach usługi Amazon S3 przejdź do szczegółów w obszarze Źródła programu **Purview,** aby wyświetlić historię skanowania.

Wybierz źródło danych, aby wyświetlić jego  szczegóły, a następnie wybierz kartę Skany, aby wyświetlić wszystkie aktualnie uruchomione lub ukończone skanowania.
Jeśli dodano konto usług AWS z wieloma zasobnikami, historia skanowania dla każdego zasobnika jest wyświetlana w ramach konta.

Na przykład:

![Pokaż skanowania zasobników usług AWS S3 w źródle konta AWS.](./media/register-scan-amazon-s3/account-scan-history.png)

Użyj innych obszarów aplikacji Purview, aby dowiedzieć się więcej o zawartości w twoim obszarze danych, w tym o zasobnikach usługi Amazon S3:

- **Wyszukaj katalog danych Purview i** odfiltruj go, aby uzyskać określony zasobnik. Na przykład:

    ![Wyszukaj w wykazie zasoby usług AWS S3.](./media/register-scan-amazon-s3/search-catalog-screen-aws.png)

- **Wyświetlanie raportów szczegółowych** informacji w celu wyświetlenia statystyk klasyfikacji, etykiet czułości, typów plików i innych szczegółów dotyczących zawartości.

    Wszystkie raporty usługi Purview Insight zawierają wyniki skanowania usługi Amazon S3 oraz pozostałe wyniki ze źródeł danych platformy Azure. Jeśli ma to zastosowanie, do opcji filtrowania raportów dodano dodatkowy typ zasobu **Amazon S3.**

    Aby uzyskać więcej informacji, zobacz Understand Insights in Azure Purview (Informacje [o szczegółowych informacjach w usłudze Azure Purview).](concept-insights.md)

## <a name="minimum-permissions-for-your-aws-policy"></a>Minimalne uprawnienia dla zasad usług AWS

Domyślna procedura tworzenia [roli AWS](#create-a-new-aws-role-for-purview) dla aplikacji Purview do użycia podczas skanowania zasobników S3 korzysta z zasad **AmazonS3ReadOnlyAccess.**

Zasady **AmazonS3ReadOnlyAccess** zapewniają minimalne uprawnienia wymagane do skanowania zasobników usługi S3 i mogą również obejmować inne uprawnienia.

Aby zastosować tylko minimalne uprawnienia wymagane do skanowania zasobników, utwórz nowe zasady z uprawnieniami wymienionymi w poniższych sekcjach, w zależności od tego, czy chcesz przeskanować pojedynczy zasobnik, czy wszystkie zasobniki na koncie.

Zastosuj nowe zasady do roli zamiast **AmazonS3ReadOnlyAccess.**

### <a name="individual-buckets"></a>Pojedyncze zasobniki

Podczas skanowania poszczególnych zasobników S3 minimalne uprawnienia usługi AWS obejmują:

- `GetBucketLocation`
- `GetBucketPublicAccessBlock`
- `GetObject`
- `ListBucket`

Pamiętaj, aby zdefiniować zasób o określonej nazwie zasobnika. Na przykład:

```json
{
"Version": "2012-10-17",
"Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetBucketLocation",
                "s3:GetBucketPublicAccessBlock",
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": "arn:aws:s3:::<bucketname>"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": "arn:aws:s3::: <bucketname>/*"
        }
    ]
}
```

### <a name="all-buckets-in-your-account"></a>Wszystkie zasobniki na Twoim koncie

Podczas skanowania wszystkich zasobników na koncie usług AWS minimalne uprawnienia usługi AWS obejmują:

- `GetBucketLocation`
- `GetBucketPublicAccessBlock`
- `GetObject`
- `ListAllMyBuckets`
- `ListBucket`.

Pamiętaj, aby zdefiniować zasób z symbolami wieloznacznymi. Na przykład:

```json
{
"Version": "2012-10-17",
"Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetBucketLocation",
                "s3:GetBucketPublicAccessBlock",
                "s3:GetObject",
                "s3:ListAllMyBuckets",
                "s3:ListBucket"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": "*"
        }
    ]
}
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o raportach usługi Azure Purview Insight:

> [!div class="nextstepaction"]
> [Opis analizy w usłudze Azure Purview](concept-insights.md)
