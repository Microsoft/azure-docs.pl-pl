---
title: Jak skanować pakiety usługi Amazon S3
description: Ten przewodnik zawiera opis szczegółowych informacji o sposobie skanowania zasobników usługi Amazon S3.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 03/21/2021
ms.custom: references_regions
ms.openlocfilehash: f77bd69f8266d9461481cd0a12a7b70107622de5
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104773457"
---
# <a name="azure-purview-connector-for-amazon-s3"></a>Łącznik usługi Azure kontrolą dla systemu Amazon S3

Ten przewodnik zawiera informacje na temat sposobu korzystania z usługi Azure kontrolą w celu skanowania danych niestrukturalnych przechowywanych obecnie w pakietach w warstwie Standardowa Amazon S3 i wykrywania, jakie typy informacji poufnych istnieją w danych. W tym przewodniku opisano również sposób identyfikowania zasobników usługi Amazon S3, w których dane są obecnie przechowywane, aby ułatwić ochronę informacji i zgodność danych.

W przypadku tej usługi Użyj kontrolą, aby zapewnić konto Microsoft z bezpiecznym dostępem do AWS, gdzie będzie działać skaner kontrolą. Skaner kontrolą korzysta z tego dostępu do zasobników usługi Amazon S3 do odczytywania danych, a następnie raportuje wyniki skanowania, w tym tylko metadane i klasyfikacje, z powrotem do platformy Azure. Korzystając z klasyfikacji kontrolą i etykietowania raportów, można analizować i przeglądać wyniki skanowania danych.

W tym przewodniku krok po kroku znajdziesz więcej informacji na temat dodawania zasobników usługi Amazon S3 jako zasobów kontrolą i tworzenia skanowania danych usługi Amazon S3.

## <a name="purview-scope-for-amazon-s3"></a>Kontrolą zakres dla usługi Amazon S3

Poniższy zakres dotyczy rejestrowania i skanowania zasobników usługi Amazon S3 jako kontrolą źródła danych.

|Zakres  |Opis  |
|---------|---------|
|**Limity danych**     |    Usługa skaner kontrolą obsługuje obecnie skanowanie zasobników usługi Amazon S3 dla maksymalnie 100 GB danych na dzierżawcę.     |
|**Typy plików**     | Usługa skaner kontrolą obsługuje obecnie następujące typy plików: <br><br>. avro,. csv,. doc,. docm,. docx,. dot,. JSON,. ODP,. ods,. ODT,. Orc,. Parquet,. PDF,. pot,. PPS,. ppsx,. ppt,. pptm,. pptx,. PSV,. SSV,. tsv,. txt,. xlc,. xls,. xlsb,. xlsm,. xlsx,. XML        |
|**Regiony**     | Łącznik kontrolą dla usługi Amazon S3 jest obecnie wdrażany tylko w regionach **AWS USA (Ohio)** i **Europa (Frankfurt)** . <br><br>Aby uzyskać więcej informacji, zobacz [regiony magazynu i skanowania](#storage-and-scanning-regions).   |
|     |         |

Aby uzyskać więcej informacji, zobacz udokumentowane limity kontrolą w:

- [Zarządzanie przydziałami i zwiększanie przydziałów zasobów za pomocą usługi Azure kontrolą](how-to-manage-quotas.md)
- [Obsługiwane źródła danych i typy plików w usłudze Azure kontrolą](sources-and-scans.md)
### <a name="storage-and-scanning-regions"></a>Obszary magazynu i skanowania

W poniższej tabeli zawarto mapy regionów, w których dane są przechowywane w regionie, w którym byłyby skanowane przez usługę Azure kontrolą.

> [!IMPORTANT]
> Klienci będą obciążani opłatami za wszystkie powiązane opłaty za transfer danych zgodnie z regionem ich przedziału.
>

| Region magazynu | Region skanowania |
| ------------------------------- | ------------------------------------- |
| Wschodnie stany USA (Ohio)                  | Wschodnie stany USA (Ohio)                        |
| Wschodnie stany USA (N. Wirginia           | Wschodnie stany USA (Ohio) lub Wschodnie stany USA (N. Wirginia                       |
| Zachodnie stany USA (N. Obywateli         | Wschodnie stany USA (Ohio)                        |
| Zachodnie stany USA (Oregon)                | Wschodnie stany USA (Ohio)                        |
| Afryka (Przylądek)              | Europa (Frankfurt)                    |
| Azja i Pacyfik (Hongkong SAR)        | Europa (Frankfurt) lub Azja i Pacyfik (Sydney)                   |
| Azja i Pacyfik (Mumbaj)           | Europa (Frankfurt) lub Azja i Pacyfik (Sydney)                   |
| Azja i Pacyfik (Osaka — lokalna)      | Europa (Frankfurt) lub Azja i Pacyfik (Sydney)                   |
| Azja i Pacyfik (Seul)            | Europa (Frankfurt) lub Azja i Pacyfik (Sydney)                   |
| Azja i Pacyfik (Singapur)        | Europa (Frankfurt) lub Azja i Pacyfik (Sydney)                   |
| Azja i Pacyfik (Sydney)           | Europa (Frankfurt) lub Azja i Pacyfik (Sydney)                  |
| Azja i Pacyfik (Tokio)            | Europa (Frankfurt) lub Azja i Pacyfik (Sydney)                 |
| Kanada (środkowe)                | Wschodnie stany USA (Ohio)                        |
| Chiny (Pekin)                 | Nieobsługiwane                    |
| Chiny (Ningxia)                 | Nieobsługiwane                   |
| Europa (Frankfurt)              | Europa (Frankfurt)                    |
| Europa (Irlandia)                | Europa (Frankfurt) lub Europa (Irlandia)                   |
| Europa (Londyn)                 | Europa (Frankfurt) lub Europa (Irlandia)                   |
| Europa (Mediolan)                  | Europa (Frankfurt)                    |
| Europa (Paryż)                  | Europa (Frankfurt)                    |
| Europa (Sztokholm)              | Europa (Frankfurt)                    |
| Bliski Wschód (Bahrajn)           | Europa (Frankfurt)                    |
| Ameryka Południowa (so Paulo)       | Wschodnie stany USA (Ohio)                        |
| | |

## <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że zostały wykonane następujące wymagania wstępne przed dodaniem zasobników usługi Amazon S3 jako kontrolą źródła danych i skanowania danych S3.

- Musisz być administratorem źródła danych usługi Azure kontrolą.

- W przypadku dodawania zasobników jako zasobów kontrolą potrzebne są wartości [AWS ARN](#retrieve-your-new-role-arn), [datazasobnika](#retrieve-your-amazon-s3-bucket-name)i czasami [Identyfikator konta AWS](#locate-your-aws-account-id).

### <a name="create-a-purview-account"></a>Utwórz konto kontrolą

- **Jeśli masz już konto kontrolą,** możesz kontynuować konfigurację wymaganą do obsługi AWS S3. Zacznij od [utworzenia poświadczenia kontrolą dla skanowania](#create-a-purview-credential-for-your-aws-bucket-scan)przedziału AWS.

- **Jeśli musisz utworzyć konto kontrolą,** postępuj zgodnie z instrukcjami w temacie [Tworzenie wystąpienia konta usługi Azure kontrolą](create-catalog-portal.md). Po utworzeniu konta Wróć tutaj, aby zakończyć konfigurację i rozpocząć korzystanie z łącznika kontrolą dla usługi Amazon S3.

### <a name="create-a-purview-credential-for-your-aws-bucket-scan"></a>Utwórz poświadczenie kontrolą dla skanowania przedziału AWS

W tej procedurze opisano sposób tworzenia nowego poświadczenia kontrolą do użycia podczas skanowania przedziałów AWS.

> [!TIP]
> Podczas [konfigurowania skanowania](#create-a-scan-for-your-amazon-s3-bucket)można także utworzyć nowe poświadczenie w trakcie procesu. W takim przypadku w polu **Credential (poświadczenia** ) wybierz pozycję **New (nowy**).
>

1. W programie kontrolą przejdź do **centrum zarządzania** i w obszarze **zabezpieczenia i dostęp** wybierz pozycję **poświadczenia**.

1. Wybierz pozycję **Nowy**, a następnie w okienku **nowe poświadczenie** , które pojawia się po prawej stronie, użyj następujących pól, aby utworzyć poświadczenia kontrolą:

    |Pole |Opis  |
    |---------|---------|
    |**Nazwa**     |Wprowadź zrozumiałą nazwę dla tego poświadczenia lub użyj wartości domyślnej.        |
    |**Opis**     |Wprowadź opcjonalny opis dla tego poświadczenia, na przykład `Used to scan the tutorial S3 buckets`         |
    |**Metoda uwierzytelniania**     |Wybierz pozycję **role ARN**, ponieważ używasz roli ARN do uzyskiwania dostępu do Twojego zasobnika.         |
    |**Identyfikator konto Microsoft**     |Kliknij, aby skopiować tę wartość do Schowka. Użyj tej wartości jako **identyfikatora konto Microsoft** podczas [tworzenia roli ARN w AWS](#create-a-new-aws-role-for-purview).           |
    |**Identyfikator zewnętrzny**     |Kliknij, aby skopiować tę wartość do Schowka. Użyj tej wartości jako **identyfikatora zewnętrznego** podczas [tworzenia roli ARN w AWS.](#create-a-new-aws-role-for-purview)        |
    |**ARN roli**     | Po [utworzeniu roli usługi Amazon IAM](#create-a-new-aws-role-for-purview)przejdź do swojej roli w obszarze IAM, skopiuj wartość **ARN roli** i wprowadź ją tutaj. Na przykład: `arn:aws:iam::284759281674:role/S3Role`. <br><br>Aby uzyskać więcej informacji, zobacz artykuł [pobieranie nowej roli ARN](#retrieve-your-new-role-arn). |
    | | |

    Wybierz pozycję **Utwórz** po zakończeniu tworzenia poświadczenia.

Aby uzyskać więcej informacji o poświadczeniach kontrolą, zobacz [dokumentację publicznej wersji zapoznawczej usługi Azure kontrolą](manage-credentials.md).

### <a name="create-a-new-aws-role-for-purview"></a>Utwórz nową rolę AWS dla kontrolą

1.  Otwórz konsolę **Amazon Web Services** i w obszarze **zabezpieczenia, tożsamość i zgodność** wybierz pozycję **IAM**.

1. Wybierz pozycję **role** , a następnie **Utwórz rolę**.

1. Wybierz **inne konto AWS**, a następnie wprowadź następujące wartości:

    |Pole  |Opis  |
    |---------|---------|
    |**Identyfikator konta**     |    Wprowadź identyfikator konta Microsoft. Na przykład: `615019938638`     |
    |**Identyfikator zewnętrzny**     |   W obszarze Opcje wybierz pozycję **Wymagaj zewnętrznego identyfikatora..**., a następnie wprowadź identyfikator zewnętrzny w wydzielonym polu. <br>Na przykład: `e7e2b8a3-0a9f-414f-a065-afaf4ac6d994`    <br><br>Ten identyfikator zewnętrzny można znaleźć podczas.  |
    | | |

    > [!NOTE]
    > W obszarze poświadczenia **centrum zarządzania** kontrolą można znaleźć wartości zarówno **identyfikatora konta Microsoft** , jak i **identyfikatora zewnętrznego**  >   , gdzie zostały [utworzone poświadczenia kontrolą](#create-a-purview-credential-for-your-aws-bucket-scan).
    >

    Na przykład:

    ![Dodaj identyfikator konta Microsoft do konta AWS.](./media/register-scan-amazon-s3/aws-create-role-amazon-s3.png)

1. W obszarze **Utwórz rolę > Dołącz zasady uprawnień** , odfiltruj uprawnienia wyświetlane do poziomu **S3**. Wybierz pozycję **AmazonS3ReadOnlyAccess**, a następnie wybierz pozycję **Next (dalej): Tagi**.

    ![Wybierz zasady ReadOnlyAccess dla nowej roli skanowania usługi Amazon S3.](./media/register-scan-amazon-s3/aws-permission-role-amazon-s3.png)

1. W obszarze **Dodaj Tagi (opcjonalnie)** można opcjonalnie utworzyć tag zrozumiały dla nowej roli. Przydatne Tagi umożliwiają organizowanie, śledzenie i kontrolowanie dostępu dla każdej roli, którą utworzysz.

    W razie potrzeby wprowadź nowy klucz i wartość dla tagu. Gdy wszystko będzie gotowe, lub jeśli chcesz pominąć ten krok, wybierz kolejno pozycje **Dalej: przegląd** , aby przejrzeć szczegóły roli i ukończyć tworzenie roli.

    ![Dodaj tag zrozumiały, aby zorganizować, śledzić lub kontrolować dostęp dla nowej roli.](./media/register-scan-amazon-s3/add-tag-new-role.png)

1. W obszarze **Recenzja** wykonaj następujące czynności:

    - W polu **Nazwa roli** wprowadź zrozumiałą nazwę roli
    - W polu **Opis roli** wprowadź opcjonalny opis, aby zidentyfikować cel roli
    - W sekcji **zasady** upewnij się, że odpowiednie zasady (**AmazonS3ReadOnlyAccess**) są dołączone do roli.

    Następnie wybierz pozycję **Utwórz rolę** , aby ukończyć proces.

    Na przykład:

    ![Przejrzyj szczegóły przed utworzeniem roli.](./media/register-scan-amazon-s3/review-role.png)


### <a name="configure-scanning-for-encrypted-amazon-s3-buckets"></a>Konfigurowanie skanowania dla zaszyfrowanych zasobników usługi Amazon S3

Zasobniki AWS obsługują wiele typów szyfrowania. W przypadku zasobników korzystających z szyfrowania **AWS-KMS** do włączenia skanowania wymagana jest specjalna konfiguracja.

> [!NOTE]
> W przypadku zasobników, które używają szyfrowania AES-256 lub AWS-KMS S3, Pomiń tę sekcję i Kontynuuj [pobieranie nazwy pakietu Amazon S3](#retrieve-your-amazon-s3-bucket-name).
>

**Aby sprawdzić typ szyfrowania używany w zasobnikach usługi Amazon S3:**

1. W AWS przejdź do **magazynu**  >  **S3** > i wybierz pozycję **zasobniki** z menu po lewej stronie.

    ![Wybierz kartę Amazon S3 zasobników.](./media/register-scan-amazon-s3/check-encryption-type-buckets.png)

1. Wybierz zasobnik, który chcesz sprawdzić. Na stronie Szczegóły zasobnika wybierz kartę **Właściwości** i przewiń w dół do **domyślnego obszaru szyfrowania** .

    - Jeśli wybrany zasobnik jest skonfigurowany dla wszystkiego, ale AWS z szyfrowaniem **usługi KMS** , w tym jeśli domyślne szyfrowanie dla przedziału jest **wyłączone**, pomiń pozostałą część tej procedury i Kontynuuj [pobieranie nazwy pakietu Amazon S3](#retrieve-your-amazon-s3-bucket-name).

    - Jeśli wybrany zasobnik jest skonfigurowany pod kątem szyfrowania **AWS-KMS** , Kontynuuj zgodnie z poniższym opisem, aby dodać nowe zasady, które umożliwiają skanowanie zasobnika przy użyciu niestandardowego szyfrowania **AWS-KMS** .

    Na przykład:

    ![Wyświetlanie zasobnika usługi Amazon S3 skonfigurowanego za pomocą AWS — szyfrowanie usługi KMS](./media/register-scan-amazon-s3/default-encryption-buckets.png)

**Aby dodać nowe zasady umożliwiające skanowanie zasobnika przy użyciu niestandardowego szyfrowania AWS-KMS:**

1. W AWS przejdź do **usług usługi**  >   **IAM**  >   i wybierz pozycję **Utwórz zasady**.

1. Na karcie **Tworzenie**  >  **edytora wizualnego** zasad Zdefiniuj zasady przy użyciu następujących wartości:

    |Pole  |Opis  |
    |---------|---------|
    |**Usługa**     |  Wprowadź i wybierz pozycję **KMS**.       |
    |**Akcje**     | W obszarze **poziom dostępu** wybierz pozycję **Zapisz** , aby rozwinąć sekcję **zapis** .<br>Po rozwinięciu wybierz tylko opcję **odszyfrowania** .        |
    |**Zasoby**     |Wybierz określony zasób lub **wszystkie zasoby**.         |
    | | |

    Gdy skończysz, wybierz pozycję **Przejrzyj zasady** , aby kontynuować.

    ![Utwórz zasady do skanowania przedziału za pomocą szyfrowania AWS-KMS.](./media/register-scan-amazon-s3/create-policy-kms.png)

1. Na stronie **Przegląd zasad** wprowadź nazwę opisową zasad i opcjonalny opis, a następnie wybierz pozycję **Utwórz zasady**.

    Nowo utworzone zasady zostaną dodane do listy zasad.

1. Dołącz nowe zasady do roli dodanej do skanowania.

    1. Przejdź z powrotem do strony role **mapy IAM**  >   i wybierz rolę, która została dodana [wcześniej](#create-a-new-aws-role-for-purview).

    1. Na karcie **uprawnienia** wybierz pozycję **Dołącz zasady**.

        ![Na karcie uprawnienia roli wybierz pozycję Dołącz zasady.](./media/register-scan-amazon-s3/iam-attach-policies.png)

    1. Na stronie **Dołącz uprawnienia** Wyszukaj i wybierz nowo utworzone zasady. Wybierz pozycję **Dołącz zasady** , aby dołączyć zasady do roli.

        Strona **Podsumowanie** zostanie zaktualizowana o nowe zasady dołączone do Twojej roli.

        ![Wyświetl zaktualizowaną stronę podsumowania zawierającą nowe zasady dołączone do roli.](./media/register-scan-amazon-s3/attach-policy-role.png)

### <a name="retrieve-your-new-role-arn"></a>Pobierz nową rolę ARN

Musisz zarejestrować rolę AWS ARN i skopiować ją do kontrolą podczas [tworzenia skanowania dla zasobnika usługi Amazon S3](#create-a-scan-for-your-amazon-s3-bucket).

**Aby pobrać rolę ARN:**

1. W obszarze Role **zarządzania tożsamościami i dostępem AWS (IAM)**  >   Wyszukaj i wybierz nową rolę [utworzoną dla kontrolą](#create-a-purview-credential-for-your-aws-bucket-scan).

1. Na stronie **Podsumowanie** roli wybierz przycisk **Kopiuj do schowka** z prawej strony **roli ARN** wartość.

    ![Skopiuj wartość ARN roli do Schowka.](./media/register-scan-amazon-s3/aws-copy-role-purview.png)

1. Wklej tę wartość w bezpiecznej lokalizacji, która jest gotowa do użycia podczas [tworzenia skanowania dla zasobnika usługi Amazon S3](#create-a-scan-for-your-amazon-s3-bucket).

### <a name="retrieve-your-amazon-s3-bucket-name"></a>Pobieranie nazwy zasobnika usługi Amazon S3

Musisz mieć nazwę pakietu Amazon S3, aby skopiować go do kontrolą podczas [tworzenia skanowania dla zasobnika Amazon S3](#create-a-scan-for-your-amazon-s3-bucket)

**Aby pobrać nazwę zasobnika:**

1. W AWS przejdź do **magazynu**  >  **S3** > i wybierz pozycję **zasobniki** z menu po lewej stronie.

    ![Wyświetl kartę Amazon S3 przedziały.](./media/register-scan-amazon-s3/check-encryption-type-buckets.png)

1. Wyszukaj i wybierz swój zasobnik, aby wyświetlić stronę szczegóły zasobnika, a następnie skopiuj nazwę zasobnika do Schowka.

    Na przykład:

    ![Pobierz i skopiuj adres URL przedziału S3.](./media/register-scan-amazon-s3/retrieve-bucket-url-amazon.png)

    Wklej nazwę przedziału w bezpiecznym pliku i Dodaj `s3://` do niej prefiks, aby utworzyć wartość, którą należy wprowadzić podczas konfigurowania przedziału jako zasób kontrolą.

    Na przykład: `s3://purview-tutorial-bucket`

> [!NOTE]
> Tylko poziom główny zasobnika jest obsługiwany jako źródło danych kontrolą. Na przykład następujący adres URL, który zawiera podfolder, *nie* jest obsługiwany: `s3://purview-tutorial-bucket/view-data`
>

### <a name="locate-your-aws-account-id"></a>Znajdź identyfikator konta AWS

Musisz mieć identyfikator konta AWS, aby zarejestrować swoje konto AWS jako źródło danych kontrolą wraz ze wszystkimi swoimi zasobnikami.

Identyfikator konta AWS jest IDENTYFIKATORem używanym do logowania się do konsoli AWS. Można go również znaleźć po zalogowaniu się na pulpicie nawigacyjnym mapy IAM, po lewej stronie w obszarze Opcje nawigacji i u góry, jako numeryczna część adresu URL logowania:

Na przykład:

![Pobierz swój identyfikator konta AWS.](./media/register-scan-amazon-s3/aws-locate-account-id.png)


## <a name="add-a-single-amazon-s3-bucket-as-a-purview-resource"></a>Dodaj pojedynczy zasobnik usługi Amazon S3 jako zasób kontrolą

Użyj tej procedury, jeśli masz tylko pojedynczy zasobnik S3, który chcesz zarejestrować, aby kontrolą jako źródło danych, lub jeśli masz wiele zasobników na koncie AWS, ale nie chcesz rejestrować wszystkich z nich w usłudze kontrolą.

1. Uruchom Portal kontrolą przy użyciu dedykowanego łącznika kontrolą dla adresu URL usługi Amazon S3. Ten adres URL został dostarczony przez zespół zarządzający produktem usługi Amazon S3 kontrolą Connector.

    ![Uruchom Portal kontrolą.](./media/register-scan-amazon-s3/purview-portal-amazon-s3.png)

1. Przejdź do strony **źródła** usługi Azure kontrolą i wybierz pozycję **zarejestruj** ![ Rejestr ikona.](./media/register-scan-amazon-s3/register-button.png) > **Amazon S3**  >  **Kontynuuj**.

    ![Dodaj pakiet Amazon AWS jako źródło danych kontrolą.](./media/register-scan-amazon-s3/add-s3-datasource-to-purview.png)

    > [!TIP]
    > Jeśli masz wiele [kolekcji](manage-data-sources.md#manage-collections) i chcesz dodać dane z usługi Amazon S3 do określonej kolekcji, wybierz **Widok mapy** w prawym górnym rogu, a następnie wybierz ikonę Zarejestruj  ![ rejestr.](./media/register-scan-amazon-s3/register-button.png) w kolekcji.
    >

1. W otwartym okienku **Rejestr sources (Amazon S3)** wprowadź następujące informacje:

    |Pole  |Opis  |
    |---------|---------|
    |**Nazwa**     |Wprowadź zrozumiałą nazwę lub użyj podanej wartości domyślnej.         |
    |**Adres URL zasobnika**     | Wprowadź adres URL przedziału AWS, używając następującej składni:   `s3://<bucketName>`     <br><br>**Uwaga**: Upewnij się, że używasz tylko głównego poziomu przedziału bez podfolderów. Aby uzyskać więcej informacji, zobacz [pobieranie nazwy zasobnika usługi Amazon S3](#retrieve-your-amazon-s3-bucket-name). |
    |**Wybierz kolekcję** |Jeśli wybrano opcję zarejestrowania źródła danych z kolekcji, ta kolekcja już znajduje się na liście. <br><br>Wybierz inną kolekcję w razie potrzeby, **Brak** do przypisania **kolekcji lub Utwórz nową kolekcję** teraz. <br><br>Aby uzyskać więcej informacji na temat kolekcji kontrolą, zobacz [Zarządzanie źródłami danych w usłudze Azure kontrolą](manage-data-sources.md#manage-collections).|
    | | |

    Gdy skończysz, wybierz pozycję **Zakończ** , aby zakończyć rejestrację.

Kontynuuj [Tworzenie skanowania dla zasobnika usługi Amazon S3.](#create-a-scan-for-your-amazon-s3-bucket).

## <a name="add-all-of-your-amazon-s3-buckets-as-purview-resources"></a>Dodaj wszystkie zasobniki usługi Amazon S3 jako zasoby kontrolą

Użyj tej procedury, jeśli masz wiele zasobników S3 na koncie Amazon i chcesz zarejestrować wszystkie jako kontrolą źródła danych.

1. Uruchom Portal kontrolą przy użyciu dedykowanego łącznika kontrolą dla adresu URL usługi Amazon S3. Ten adres URL został dostarczony przez zespół zarządzający produktem usługi Amazon S3 kontrolą Connector.

    ![Uruchom łącznik dla dedykowanego portalu usługi Amazon S3 kontrolą](./media/register-scan-amazon-s3/purview-portal-amazon-s3.png)

1. Przejdź do strony **źródła** usługi Azure kontrolą i wybierz pozycję **zarejestruj** ![ Rejestr ikona.](./media/register-scan-amazon-s3/register-button.png) > **Konta**  >  usługi Amazon **Kontynuuj**.

    ![Dodaj konto Amazon jako źródło danych kontrolą.](./media/register-scan-amazon-s3/add-s3-account-to-purview.png)

    > [!TIP]
    > Jeśli masz wiele [kolekcji](manage-data-sources.md#manage-collections) i chcesz dodać dane z usługi Amazon S3 do określonej kolekcji, wybierz **Widok mapy** w prawym górnym rogu, a następnie wybierz ikonę Zarejestruj  ![ rejestr.](./media/register-scan-amazon-s3/register-button.png) w kolekcji.
    >

1. W otwartym okienku **Rejestr sources (Amazon S3)** wprowadź następujące informacje:

    |Pole  |Opis  |
    |---------|---------|
    |**Nazwa**     |Wprowadź zrozumiałą nazwę lub użyj podanej wartości domyślnej.         |
    |**Identyfikator konta AWS**     | Wprowadź identyfikator konta AWS. Aby uzyskać więcej informacji, zobacz [lokalizowanie identyfikatora konta usługi AWS](#locate-your-aws-account-id)|
    |**Wybierz kolekcję** |Jeśli wybrano opcję zarejestrowania źródła danych z kolekcji, ta kolekcja już znajduje się na liście. <br><br>Wybierz inną kolekcję w razie potrzeby, **Brak** do przypisania **kolekcji lub Utwórz nową kolekcję** teraz. <br><br>Aby uzyskać więcej informacji na temat kolekcji kontrolą, zobacz [Zarządzanie źródłami danych w usłudze Azure kontrolą](manage-data-sources.md#manage-collections).|
    | | |

    Gdy skończysz, wybierz pozycję **Zakończ** , aby zakończyć rejestrację.

Kontynuuj [Tworzenie skanowania dla zasobnika usługi Amazon S3](#create-a-scan-for-your-amazon-s3-bucket).

## <a name="create-a-scan-for-your-amazon-s3-bucket"></a>Tworzenie skanowania dla zasobnika usługi Amazon S3

Po dodaniu zasobników jako kontrolą źródła danych można skonfigurować skanowanie do uruchamiania w zaplanowanych odstępach czasu lub natychmiast.

1. Przejdź do obszaru **źródła** usługi Azure kontrolą, a następnie wykonaj jedną z następujących czynności:

    - W **widoku mapy** wybierz pozycję **Nowy Skanuj** ![ nową ikonę skanowania.](./media/register-scan-amazon-s3/new-scan-button.png) w polu Źródło danych.
    - W **widoku listy** Umieść kursor nad wierszem źródła danych, a następnie wybierz pozycję **nowe Skanuj** ![ nową ikonę skanowania. ](./media/register-scan-amazon-s3/new-scan-button.png)

1. W okienku **Skanuj...** , które jest otwierane po prawej stronie, Zdefiniuj następujące pola, a następnie wybierz pozycję **Kontynuuj**:

    |Pole  |Opis  |
    |---------|---------|
    |**Nazwa**     |  Wprowadź zrozumiałą nazwę skanowania lub użyj wartości domyślnej.       |
    |**Typ** |Wyświetlane tylko wtedy, gdy dodano konto AWS, z uwzględnieniem wszystkich zasobników. <br><br>Bieżące opcje obejmują tylko **wszystkie** usługi  >  **Amazon S3**. Bądź na bieżąco, aby dowiedzieć się więcej o opcjach umożliwiających wybranie macierzy pomocy technicznej kontrolą. |
    |**Poświadczenie**     |  Wybierz poświadczenie kontrolą z rolą ARN. <br><br>**Porada**: Jeśli chcesz teraz utworzyć nowe poświadczenie, wybierz pozycję **Nowy**. Aby uzyskać więcej informacji, zobacz [Tworzenie poświadczeń kontrolą dla skanowania](#create-a-purview-credential-for-your-aws-bucket-scan)przedziału AWS.     |
    |     |         |

    Kontrolą automatycznie sprawdza, czy rola ARN jest prawidłowa i czy zasobnik i obiekt w zasobniku są dostępne, a następnie kontynuuje, jeśli połączenie zakończy się pomyślnie.

    > [!TIP]
    > Aby wprowadzić różne wartości i przetestować połączenie przed kontynuowaniem, wybierz pozycję **Test connection** w prawym dolnym rogu przed wybraniem pozycji **Kontynuuj**.
    >

1. W okienku **Wybierz zestaw reguł skanowania** wybierz zestaw reguł domyślnych **AmazonS3** lub wybierz opcję **Nowy skan zestawu** reguł, aby utworzyć nowy niestandardowy zestaw reguł. Po wybraniu zestawu reguł Wybierz pozycję **Kontynuuj**.

    Jeśli wybierzesz opcję utworzenia nowego niestandardowego zestawu reguł skanowania, użyj kreatora do zdefiniowania następujących ustawień:

    |Pane  |Opis  |
    |---------|---------|
    |**Nowy zestaw reguł skanowania** /<br>**Opis reguły skanowania**    |   Wprowadź zrozumiałą nazwę i opcjonalny opis dla zestawu reguł      |
    |**Wybierz typy plików**     | Zaznacz wszystkie typy plików, które chcesz dołączyć do skanowania, a następnie wybierz pozycję **Kontynuuj**.<br><br>Aby dodać nowy typ pliku, wybierz pozycję **nowy typ pliku** i Zdefiniuj następujące elementy: <br>-Rozszerzenie pliku, które chcesz dodać <br>— Opcjonalny opis  <br>-Czy zawartość pliku ma niestandardowy ogranicznik, czy też jest typem pliku systemowego. Następnie wprowadź niestandardowy ogranicznik lub wybierz typ pliku systemowego. <br><br>Wybierz pozycję **Utwórz** , aby utworzyć niestandardowy typ pliku.     |
    |**Wybierz reguły klasyfikacji**     |   Przejdź do i wybierz reguły klasyfikacji, które mają być uruchamiane w zestawie danych.      |
    |     |         |

    Wybierz pozycję **Utwórz** , gdy skończysz, aby utworzyć zestaw reguł.

1. W okienku **Ustawianie wyzwalacza skanowania** wybierz jedną z następujących opcji, a następnie wybierz pozycję **Kontynuuj**:

    - **Cyklicznie** skonfigurować harmonogram skanowania cyklicznego
    - **Raz** , aby skonfigurować skanowanie, które zaczyna się od razu

1. W okienku **przeglądanie skanowania** Sprawdź szczegóły skanowania, aby upewnić się, że są poprawne, a następnie wybierz opcję **Zapisz** lub **Zapisz i uruchom** w przypadku wybrania **raz** w poprzednim okienku.

    > [!NOTE]
    > Po uruchomieniu skanowania może potrwać do 24 godzin. Po uruchomieniu każdego skanowania będzie można przejrzeć **raporty usługi Insights** i przeszukać katalog 24 godziny.
    >

Aby uzyskać więcej informacji, zobacz [Eksplorowanie wyników skanowania kontrolą](#explore-purview-scanning-results).

## <a name="explore-purview-scanning-results"></a>Poznaj wyniki skanowania kontrolą

Po zakończeniu skanowania kontrolą w pakietach usługi Amazon S3 przejdź do szczegółów w obszarze **źródła** kontrolą, aby wyświetlić historię skanowania.

Wybierz źródło danych, aby wyświetlić jego szczegóły, a następnie wybierz kartę **skanowania** , aby wyświetlić wszystkie aktualnie uruchomione lub zakończone skanowania.
Jeśli dodano konto AWS z wieloma zasobnikami, historia skanowania dla każdego przedziału jest pokazywana w ramach konta.

Na przykład:

![Pokaż skanowania przedziału S3 w środowisku AWS w ramach źródła konta AWS.](./media/register-scan-amazon-s3/account-scan-history.png)

Skorzystaj z innych obszarów kontrolą, aby uzyskać szczegółowe informacje o zawartości, w tym w zasobnikach usługi Amazon S3:

- **Przeszukaj kontrolą Data Catalog** i odfiltruj określony zasobnik. Na przykład:

    ![Wyszukaj w wykazie zasoby AWS S3.](./media/register-scan-amazon-s3/search-catalog-screen-aws.png)

- **Wyświetlaj szczegółowe raporty** , aby wyświetlić statystyki dotyczące klasyfikacji, etykiet informacji o poufności, typów plików oraz więcej szczegółowych informacji o zawartości.

    Wszystkie raporty kontrolą Insights obejmują wyniki skanowania usługi Amazon S3, a także pozostałe wyniki ze źródeł danych platformy Azure. W razie potrzeby do opcji filtrowania raportu Dodano dodatkowy typ zasobu **Amazon S3** .

    Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure kontrolą](concept-insights.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o raportach usługi Azure kontrolą Insights:

> [!div class="nextstepaction"]
> [Opis analizy w usłudze Azure Purview](concept-insights.md)
