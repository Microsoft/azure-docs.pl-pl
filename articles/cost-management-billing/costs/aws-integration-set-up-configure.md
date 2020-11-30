---
title: Konfigurowanie integracji platformy AWS z usługą Azure Cost Management
description: W tym artykule przedstawiono sposób ustawiania i konfigurowania integracji raportów o kosztach i użyciu platformy AWS z usługą Azure Cost Management.
author: bandersmsft
ms.author: banders
ms.date: 10/23/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: matrive
ms.openlocfilehash: 2b8a008decc41a5686fb2c8d9fee271f95f0fef3
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96122405"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>Ustawianie i konfigurowanie integracji raportów o kosztach i użyciu platformy AWS

Dzięki integracji raportów o kosztach i użyciu (CUR, Cost and Usage report) platformy Amazon Web Services (AWS) można monitorować i kontrolować wydatki dotyczące platformy AWS w usłudze Azure Cost Management. Dzięki integracji wydatki dotyczące platformy AWS i platformy Azure można monitorować i kontrolować w jednym miejscu w witrynie Azure Portal. W tym artykule wyjaśniono, jak skonfigurować integrację i ustawić ją tak, aby można było używać funkcji usługi Azure Cost Management do analizowania kosztów i przeglądania budżetów.

Usługa Azure Cost Management przetwarza raport o kosztach i użyciu platformy AWS, który jest przechowywany w zasobniku S3 przy użyciu poświadczeń dostępu do platformy AWS w celu pobrania definicji raportu i plików CSV w formacie GZIP.

Obejrzyj wideo [How to set up Connectors for AWS in Cost Management](https://www.youtube.com/watch?v=Jg5KC1cx5cA) (Jak skonfigurować łączniki dla usługi AWS w usłudze Cost Management), aby dowiedzieć się więcej o sposobie konfigurowania integracji raportów usługi AWS. Aby obejrzeć inne wideo, odwiedź [kanał usługi Cost Management w serwisie YouTube](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/Jg5KC1cx5cA]

## <a name="create-a-cost-and-usage-report-in-aws"></a>Tworzenie raportu o kosztach i użyciu platformy AWS

Wykorzystanie raportu o kosztach i użyciu to zalecany przez platformę AWS sposób zbierania i przetwarzania kosztów platformy AWS. Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [raportu o kosztach i użyciu platformy AWS](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html).

Aby utworzyć raport o kosztach i użyciu, przejdź do strony **Cost & Usage Reports** (Raporty o kosztach i użyciu) w konsoli rozliczeń i zarządzania kosztami platformy AWS i wykonaj następujące czynności:

1. Zaloguj się do konsoli zarządzania platformy AWS i otwórz [konsolę rozliczeń i zarządzania kosztami](https://console.aws.amazon.com/billing).
2. W okienku nawigacji wybierz pozycję **Cost & Usage Reports** (Raporty o kosztach i użyciu).
3. Wybierz pozycję **Create report** (Utwórz raport).
4. W polu **Report name** (Nazwa raportu) wprowadź nazwę raportu.
5. W obszarze **Additional report details** (Dodatkowe szczegóły raportu) wybierz pozycję **Include resource IDs** (Uwzględnij identyfikatory zasobów).
6. W obszarze **Data refresh settings** (Ustawienia odświeżania danych) wybierz, czy chcesz, aby raport o kosztach i użyciu platformy AWS odświeżał się, gdy platforma AWS zastosuje wobec Twojego konta zwroty, środki lub opłaty za pomoc techniczną po sfinalizowaniu rachunku. Po odświeżeniu raportu nowy raport zostaje przekazany do usługi Amazon S3. Zalecamy pozostawienie tego ustawienia.
7. Wybierz opcję **Dalej**.
8. Dla elementu **S3 bucket** (Zasobnik S3) wybierz polecenie **Configure** (Skonfiguruj).
9. W oknie dialogowym Configure S3 Bucket (Konfigurowanie zasobnika S3) wprowadź nazwę zasobnika i region, w którym chcesz utworzyć nowy zasobnik, a następnie wybierz pozycję **Next** (Dalej).
10. Zaznacz pozycję **I have confirmed that this policy is correct** (Potwierdzam, że te zasady są poprawne), a następnie kliknij pozycję **Save** (Zapisz).
11. (Opcjonalnie) W polu Report path prefix (Prefiks ścieżki raportu) wprowadź prefiks ścieżki raportu, który ma poprzedzać nazwę raportu.
Jeśli nie określisz prefiksu, domyślnym prefiksem jest nazwa określona dla raportu. Zakres dat ma format `/report-name/date-range/`.
12. W obszarze **Time unit** (Jednostka czasu) wybierz opcję **Hourly** (Co godzinę).
13. W przypadku pozycji **Report versioning** (Wersja raportu) wybierz, czy każda wersja raportu ma zastępować poprzednią wersję czy mają się pojawiać dodatkowe nowe raporty.
14. W obszarze **Enable data integration for** (Włącz integrację danych dla) nie trzeba nic zaznaczać.
15. W obszarze **Compression** (Kompresja) wybierz pozycję **GZIP**.
16. Wybierz opcję **Dalej**.
17. Po przejrzeniu ustawień raportu wybierz pozycję **Review and Complete** (Przejrzyj i zakończ).

    Zanotuj nazwę raportu. Zostanie ona użyta w dalszych krokach.

Rozpoczęcie dostarczania raportów do zasobnika usługi Amazon S3 może zająć platformie AWS do 24 godzin. Po rozpoczęciu dostarczania platforma AWS aktualizuje pliki raportów o kosztach i użyciu platformy AWS co najmniej raz dziennie. Konfigurowanie środowiska platformy AWS możesz kontynuować, nie czekając na rozpoczęcie dostarczania.

## <a name="create-a-role-and-policy-in-aws"></a>Tworzenie roli i zasad na platformie AWS

Usługa Azure Cost Management kilka razy dziennie uzyskuje dostęp do zasobnika S3, w którym jest zlokalizowany raport o kosztach i użyciu. Usługa musi mieć dostęp do poświadczeń w celu sprawdzenia, czy są nowe dane. Tworzysz rolę i zasady na platformie AWS, aby umożliwić usłudze Cost Management dostęp do nich.

Aby włączyć dostęp oparty na rolach do konta AWS w usłudze Cost Management, rola zostaje utworzona w konsoli platformy AWS. Musisz mieć _rolę ARN_ i _identyfikator zewnętrzny_ z konsoli platformy AWS. Później należy użyć ich na stronie **Utwórz łącznik platformy AWS** w usłudze Cost Management.

Użyj kreatora tworzenia nowej roli:

1. Zaloguj się do konsoli platformy AWS i wybierz pozycję **Services** (Usługi).
2. Z listy usług wybierz pozycję **IAM** (Zarządzanie dostępem i tożsamościami).
3. Wybierz pozycję **Roles** (Role), a następnie wybierz pozycję **Create Role** (Utwórz rolę).
4. Na następnej stronie wybierz pozycję **Another AWS account** (Inne konto AWS).
5. W polu **Account ID** (Identyfikator konta ) wprowadź **432263259397**.
6. W obszarze **Options** (Opcje) wybierz pozycję **Require external ID** (Wymagaj zewnętrznego identyfikatora) (to najlepsze rozwiązanie, gdy ta rola zostanie przyjęta przez inny podmiot).
7. W polu **External ID** (Identyfikator zewnętrzny) wprowadź identyfikator zewnętrzny, który jest udostępnionym kodem dostępu między rolą AWS a usługą Azure Cost Management. Ten sam identyfikator zewnętrzny jest również używany na stronie **Nowy łącznik** w usłudze Cost Management. Firma Microsoft zaleca stosowanie zasad silnych kodów dostępu podczas wprowadzania identyfikatora zewnętrznego.
    > [!NOTE]
    > Nie zmieniaj zaznaczenia dla opcji **Require MFA** (Wymaganie usługi MFA). Pole powinno pozostać niezaznaczone.
8. Wybierz opcję **Dalej: Permissions** (Dalej: uprawnienia).
9. Wybierz pozycję **Create policy** (Utwórz zasady). Zostanie otwarta nowa karta przeglądarki. Jest to miejsce, w którym utworzysz zasady.
10. Wybierz pozycję **Choose a service** (Wybierz usługę).

Skonfiguruj uprawnienia dotyczące raportu o kosztach i użyciu:

1. Wprowadź **raport o kosztach i użyciu**.
2. Wybierz kolejno pozycje **Access level (Poziom dostępu)**  > **Read (Odczyt)**  > **DescribeReportDefinitions**. Ten krok umożliwia usłudze Cost Management odczytywanie, jakie raporty CUR są zdefiniowane, i ustalanie, czy są one zgodne z wymaganiami wstępnymi definicji raportu.
3. Wybierz pozycję **Add additional permissions** (Dodaj dodatkowe uprawnienia).

Skonfiguruj uprawnienie dotyczące zasobnika S3 i obiektów:

1. Wybierz pozycję **Choose a service** (Wybierz usługę).
2. Wprowadź zasobnik **S3**.
3. Wybierz kolejno pozycje **Access level (Poziom dostępu)**  > **List (Lista)**  > **ListBucket**. To działanie umożliwia pobranie listy obiektów w zasobniku S3.
4. Wybierz kolejno pozycje **Access level (Poziom dostępu)**  > **Read (Odczyt)**  > **GetObject**. To działanie umożliwia pobranie plików rozliczeń.
5. Wybierz pozycję **Resources** (Zasoby).
6. Wybierz **zasobnik — Add ARN** (Dodaj rolę ARN).
7. W polu **Bucket name** (Nazwa zasobnika) wprowadź zasobnik używany do przechowywania plików CUR.
8. Wybierz **obiekt — Add ARN** (Dodaj rolę ARN).
9. W polu **Bucket name** (Nazwa zasobnika) wprowadź zasobnik używany do przechowywania plików CUR.
10. W polu **Object name** (Nazwa obiektu) wybierz pozycję **Any** (Dowolne).
11. Wybierz pozycję **Add additional permissions** (Dodaj dodatkowe uprawnienia).

Skonfiguruj uprawnienia dla usługi Cost Explorer:

1. Wybierz pozycję **Choose a service** (Wybierz usługę).
2. Wprowadź pozycję **Cost Explorer Service** (Usługa Cost Explorer).
3. Wybierz pozycję **All Cost Explorer Service actions (ce:\*)** (Wszystkie działania usługi Cost Explorer). To działanie umożliwia sprawdzenie, czy kolekcja jest poprawna.
4. Wybierz pozycję **Add additional permissions** (Dodaj dodatkowe uprawnienia).

Dodaj uprawnienie dla organizacji AWS:

1. Wprowadź **organizacje**.
2. Wybierz kolejno pozycje **Access level (Poziom dostępu)**  > **List (Lista)**  > **ListAccounts**. To działanie umożliwia pobranie nazw kont.
3. W polu **Review policy** (Przejrzyj zasady) wprowadź nazwę nowych zasad. Sprawdź, czy wprowadzono poprawne informacje, a następnie wybierz pozycję **Create Policy** (Utwórz zasady).
4. Wróć do poprzedniej karty i odśwież stronę przeglądarki. Na pasku wyszukiwania wyszukaj nowe zasady.
5. Wybierz opcję **Dalej: Review** (Dalej: przegląd).
6. Wprowadź nazwę nowej roli. Sprawdź, czy wprowadzono poprawne informacje, a następnie wybierz pozycję **Create Role** (Utwórz rolę).

    Zanotuj rolę ARN i identyfikator zewnętrzny użyty w poprzednich krokach podczas tworzenia roli. Zostaną one użyte później podczas konfigurowania łącznika usługi Azure Cost Management.

Kod JSON zasad powinien przypominać poniższy przykład. Zastąp element _bucketname_ nazwą Twojego zasobnika S3.

```JSON
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
"organizations:ListAccounts",
             "ce:*",
             "cur:DescribeReportDefinitions"
            ],
            "Resource": "*"
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::bucketname",
                "arn:aws:s3:::bucketname/*"
            ]
        }
    ]
}
```

## <a name="set-up-a-new-connector-for-aws-in-azure"></a>Konfigurowanie nowego łącznika dla platformy AWS na platformie Azure

Skorzystaj z poniższych informacji, aby utworzyć łącznik platformy AWS i rozpocząć monitorowanie kosztów dotyczących platformy AWS:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Przejdź do strony głównej platformy Azure, klikając pozycję **Strona główna** w menu po lewej stronie (ikona menu w kształcie hamburgera z 3 liniami).
3. Przejdź do pozycji **Narzędzia** > **Cost Management** w dolnej części strony.
3. W obszarze **Ustawienia** wybierz pozycję **Łączniki dla platformy AWS**.  
4. Wybierz pozycję **+ Dodaj** w górnej części strony, aby utworzyć łącznik.  
    :::image type="content" source="./media/aws-integration-setup-configure/aws-connector.png" alt-text="Przykład pokazujący ustawienie Łączniki dla platformy AWS" :::
1. Na stronie **Utwórz łącznik**  w polu **Nazwa wyświetlana** wprowadź nazwę łącznika.  
    :::image type="content" source="./media/aws-integration-setup-configure/create-aws-connector01.png" alt-text="Przykładowa strona do tworzenia łącznika platformy AWS" :::
1. Ewentualnie możesz wybrać domyślną grupę zarządzania. Zostaną tam zapisane wszystkie wykryte połączone konta. Można to skonfigurować później.
1. W sekcji **Rozliczenia** ustaw opcję **Automatyczne odnawianie** w pozycji **Włączone**, jeśli chcesz zapewnić ciągłość operacji. W przypadku wybrania opcji automatycznej należy wybrać subskrypcję rozliczeniową.
1. W polu **Rola ARN** wprowadź wartość użytą podczas konfigurowania roli na platformie AWS.
1. W polu **Identyfikator zewnętrzny** wprowadź wartość użytą podczas konfigurowania roli na platformie AWS.
1. W polu **Nazwa raportu** wprowadź nazwę utworzoną na platformie AWS.
1. Wybierz przycisk **Dalej**, a następnie wybierz pozycję **Utwórz**.

Pojawienie się nowych zakresów platformy AWS, skonsolidowanych kont platformy AWS, połączonych kont platformy AWS i ich danych dotyczących kosztów może potrwać kilka godzin.

Po utworzeniu łącznika zalecamy przypisanie do niego kontroli dostępu. Użytkownicy mają przypisane uprawnienia do nowo wykrytych zakresów: skonsolidowane konto platformy AWS i połączone konta platformy AWS. Użytkownik tworzący łącznik jest właścicielem łącznika, skonsolidowanego konta i wszystkich połączonych kont.

Przypisanie uprawnień łącznika do użytkowników po wystąpieniu wykrycia nie powoduje przypisania uprawnień do istniejących zakresów platformy AWS. Zamiast tego tylko nowe połączone konta mają przypisane uprawnienia.

## <a name="take-additional-steps"></a>Dodatkowe kroki

- Jeśli jeszcze tego nie zrobiono, [skonfiguruj grupy zarządzania](../../governance/management-groups/overview.md#initial-setup-of-management-groups).
- Sprawdź, czy nowe zakresy są dodawane do selektora zakresu. Wybierz pozycję **Odśwież**, aby wyświetlić najnowsze dane.
- Na stronie **Łączniki chmury** wybierz swój łącznik i wybierz pozycję **Przejdź do konta rozliczeniowego**, aby przypisać połączone konto do grup zarządzania.

> [!NOTE]
> Grupy zarządzania nie są obecnie obsługiwane w przypadku klientów umowy MCA. Klienci umowy MCA mogą utworzyć łącznik, aby wyświetlać swoje dane platformy AWS. Jednak klienci umowy MCA nie mogą wyświetlać kosztów platformy Azure i AWS razem w ramach grupy zarządzania.

## <a name="manage-aws-connectors"></a>Zarządzanie łącznikami platformy AWS

Po wybraniu łącznika na stronie **Łączniki dla platformy AWS** można wykonać następujące czynności:

- Wybrać pozycję **Przejdź do konta rozliczeniowego**, aby wyświetlić informacje dotyczące skonsolidowanego konta platformy AWS.
- Wybrać pozycję **Kontrola dostępu**, aby zarządzać przypisaniem roli dla łącznika.
- Wybrać pozycję **Edytuj**, aby zaktualizować łącznik. Nie można zmienić numeru konta platformy AWS, ponieważ pojawia się on w nazwie ARN roli. Jednak można utworzyć nowy łącznik.
- Wybrać pozycję **Sprawdź**, aby ponownie uruchomić test weryfikacyjny w celu upewnienia się, że usługa Cost Management może zbierać dane przy użyciu ustawień łącznika.

:::image type="content" source="./media/aws-integration-setup-configure/aws-connector-details.png" alt-text="Szczegóły przykładowego łącznika platformy AWS" :::

## <a name="set-up-azure-management-groups"></a>Konfigurowanie grup zarządzania platformy Azure

Umieść subskrypcje platformy Azure i połączone konta platformy AWS w tej samej grupie zarządzania, aby utworzyć pojedynczą lokalizację, w której można wyświetlić informacje o dostawcach międzychmurowych. Jeśli środowisko platformy Azure nie zostało jeszcze skonfigurowane w grupach zarządzania, zobacz [Konfiguracja początkowa grup zarządzania](../../governance/management-groups/overview.md#initial-setup-of-management-groups).

Jeśli chcesz rozdzielić koszty, możesz utworzyć grupę zarządzania, która zawiera tylko połączone konta platformy AWS.

## <a name="set-up-an-aws-consolidated-account"></a>Konfigurowanie skonsolidowanego konta platformy AWS

Skonsolidowane konto platformy AWS łączy rozliczenia i płatność dla wielu kont platformy AWS. Działa również jako połączone konto platformy AWS. Użyj linku na stronie łącznika platformy AWS, aby wyświetlić szczegóły skonsolidowanego konta platformy AWS. 

:::image type="content" source="./media/aws-integration-setup-configure/aws-consolidated-account01.png" alt-text="Przykładowe szczegóły skonsolidowanego konta platformy AWS" :::

Na tej stronie można:

- Wybrać pozycję **Aktualizuj**, aby zaktualizować zbiorczo skojarzenia połączonych kont platformy AWS z grupą zarządzania.
- Wybrać pozycję **Kontrola dostępu**, aby ustawić przypisanie roli dla zakresu.

### <a name="permissions-for-an-aws-consolidated-account"></a>Uprawnienia dla skonsolidowanego konta platformy AWS

Domyślnie uprawnienia dla skonsolidowanego konta platformy AWS są ustawiane podczas tworzenia konta na podstawie uprawnień łącznika platformy AWS. Twórca łącznika jest jego właścicielem.

Poziomem dostępu można zarządzać przy użyciu strony **Poziom dostępu** skonsolidowanego konta platformy AWS. Jednak połączone konta platformy AWS nie dziedziczą uprawnień do skonsolidowanego konta platformy AWS.

## <a name="set-up-an-aws-linked-account"></a>Konfigurowanie połączonego konta platformy AWS

Połączone konto platformy AWS to miejsce, w którym tworzy się zasoby platformy AWS i zarządza nimi. Połączone konto działa również jako granica zabezpieczeń.

Na tej stronie można:

- Wybrać pozycję **Aktualizuj**, aby zaktualizować skojarzenie połączonego konta platformy AWS z grupą zarządzania.
- Wybrać pozycję **Kontrola dostępu**, aby ustawić przypisanie roli dla zakresu.

:::image type="content" source="./media/aws-integration-setup-configure/aws-linked-account01.png" alt-text="Przykład strony połączonego konta platformy AWS" :::

### <a name="permissions-for-an-aws-linked-account"></a>Uprawnienia dla połączonego konta platformy AWS

Domyślnie uprawnienia dla połączonego konta platformy AWS są ustawiane podczas tworzenia na podstawie uprawnień łącznika platformy AWS. Twórca łącznika jest jego właścicielem. Poziomem dostępu można zarządzać przy użyciu strony **Poziom dostępu** połączonego konta platformy AWS. Połączone konta platformy AWS nie dziedziczą uprawnień ze skonsolidowanego konta platformy AWS.

Połączone konta platformy AWS zawsze dziedziczą uprawnienia z grupy zarządzania, do której należą.

## <a name="next-steps"></a>Następne kroki

- Po skonfigurowaniu i ustawieniu integracji raportów o kosztach i użyciu platformy AWS możesz kontynuować, przechodząc do artykułu [Zarządzanie kosztami i użyciem usług AWS](aws-integration-manage.md).
- Jeśli nie znasz analizy kosztów, zobacz przewodnik Szybki start [Poznawanie i analizowanie kosztów za pomocą funkcji Analiza kosztów](quick-acm-cost-analysis.md).
- Jeśli nie znasz budżetów na platformie Azure, zobacz [Tworzenie budżetów platformy Azure i zarządzanie nimi](tutorial-acm-create-budgets.md).
