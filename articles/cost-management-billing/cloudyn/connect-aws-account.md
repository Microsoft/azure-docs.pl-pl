---
title: Łączenie konta Amazon Web Services z usługą Cloudyn na platformie Azure
description: Połącz konto Amazon Web Services, aby wyświetlać dane dotyczące kosztów i użycia w raportach usługi Cloudyn.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: de0fd1a27ca97545ccdd534908eb802993514017
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690174"
---
# <a name="connect-an-amazon-web-services-account"></a>Łączenie konta Amazon Web Services

Dostępne są dwie opcje łączenia konta Amazon Web Services (AWS) z usługą Cloudyn. Możesz nawiązać połączenie za pomocą roli systemu Zarządzanie dostępem i tożsamościami lub konta tylko do odczytu użytkownika tego systemu. Rola systemu Zarządzanie dostępem i tożsamościami jest zalecana, ponieważ umożliwia delegowanie dostępu ze zdefiniowanymi uprawnieniami do zaufanych jednostek. Rola systemu Zarządzanie dostępem i tożsamościami nie wymaga udostępniania długoterminowych kluczy dostępu. Po połączeniu konta AWS z usługą Cloudyn, dane dotyczące kosztów i użycia są dostępne w raportach usługi Cloudyn. Ten dokument zawiera informacje dotyczące sposobu używania obu tych opcji.

Aby uzyskać więcej informacji na temat tożsamości systemu Zarządzanie dostępem i tożsamościami platformy AWS, zobacz [Tożsamości (użytkownicy, grupy i role)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html).

Ponadto włączone zostaną szczegółowe raporty dotyczące rozliczeń platformy AWS, a informacje będą przechowywane w zasobniku usługi AWS Simple Storage Service (S3). Szczegółowe raporty dotyczące rozliczeń obejmują naliczane co godziny opłaty rozliczeniowe wraz z informacjami o tagach i zasobach. Przechowywanie raportów umożliwia pobranie ich za pomocą usługi Cloudyn do Twojego zasobnika i wyświetlenie informacji w jego raportach.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]
## <a name="aws-role-based-access"></a>Dostęp oparty na rolach platformy AWS

W poniższych sekcjach omówiono tworzenie roli tylko do odczytu systemu Zarządzanie dostępem i tożsamościami w celu zapewnienia dostępu do usługi Cloudyn.

### <a name="get-your-cloudyn-account-external-id"></a>Uzyskiwanie zewnętrznego identyfikatora konta usługi Cloudyn

Pierwszym krokiem jest uzyskanie unikatowego hasła połączenia z portalu Cloudyn. Jest on używany na platformie AWS jako **identyfikator zewnętrzny**.

1. Otwórz portal Cloudyn z poziomu witryny Azure Portal lub przejdź na stronę [https://azure.cloudyn.com](https://azure.cloudyn.com) i zaloguj się.
2. Kliknij symbol koła zębatego, a następnie wybierz pozycję **Konta w chmurze**.
3. W obszarze Zarządzanie kontami wybierz kartę **Konta platformy AWS**, a następnie kliknij pozycję **Dodaj nowe +** .
4. W oknie dialogowym **Dodawanie konta platformy AWS** skopiuj **identyfikator zewnętrzny**, a następnie zapisz wartość na potrzeby kroków tworzenia roli platformy AWS w następnej sekcji. Identyfikator zewnętrzny jest unikatowy dla Twojego konta. Na poniższej ilustracji przykładowy identyfikator zewnętrzny to _Contoso_, po którym następuje liczba. Twój identyfikator jest inny.  
    ![Identyfikator zewnętrzny wyświetlany w polu dodawania konta platformy AWS](./media/connect-aws-account/external-id.png)

### <a name="add-aws-read-only-role-based-access"></a>Dodawanie dostępu tylko do odczytu opartego na rolach na platformie AWS

1. Zaloguj się do konsoli platformy AWS na stronie [https://console.aws.amazon.com/iam/home](https://console.aws.amazon.com/iam/home), a następnie wybierz pozycję **Roles (Role)** .
2. Kliknij pozycję **Create Role (Utwórz rolę)** , a następnie wybierz pozycję **Another AWS account (Inne konto platformy AWS)** .
3. W polu **Account ID (Identyfikator konta)** wklej ciąg `432263259397`. Ten identyfikator konta określa konto modułu zbierającego dane usługi Cloudyn przypisane przez platformę AWS do tej usługi. Użyj wyświetlonego identyfikatora konta.
4. Obok pozycji **Options (Opcje)** wybierz pozycję **Require external ID (Wymagaj identyfikatora zewnętrznego)** . Wklej unikatową wartość, która została wcześniej skopiowana z pola **Identyfikator zewnętrzny** w usłudze Cloudyn. Następnie kliknij pozycję **Dalej: Permissions** (Dalej: uprawnienia).  
    ![Wklej zewnętrzny identyfikator z usługi Cloudyn na stronie Create role (Tworzenie roli)](./media/connect-aws-account/create-role01.png)
5. W obszarze **Attach permissions policies (Dołączanie zasad uprawnień)** w polu wyszukiwania filtru **Policy type (Typ zasad)** wpisz ciąg `ReadOnlyAccess`, wybierz pozycję **ReadOnlyAccess**, a następnie kliknij pozycję **Next: Review** (Dalej: przegląd).  
    ![wybierz dostęp tylko do odczytu na liście nazw zasad](./media/connect-aws-account/readonlyaccess.png)
6. Na stronie Review (Przegląd) upewnij się, że wybrane opcje są poprawne, a następnie wpisz **nazwę roli**. Przykład: *Azure-Cost-Mgt*. Wprowadź **opis roli**. Na przykład _Przypisanie roli dla usługi Cloudyn_, a następnie kliknij pozycję **Create role (Utwórz rolę)** .
7. Na liście **Roles (Role)** kliknij utworzoną rolę, a następnie skopiuj wartość z pola **Role ARN (Nazwa ARN roli)** na stronie Summary (Podsumowanie). Użyj wartości z pola Role ARN (Nazwa ARN roli) później podczas rejestrowania konfiguracji w usłudze Cloudyn.  
    ![skopiuj wartość pola Role ARN (Nazwa ARN roli) ze strony podsumowania](./media/connect-aws-account/role-arn.png)

### <a name="configure-aws-iam-role-access-in-cloudyn"></a>Konfigurowanie dostępu do systemu Zarządzanie dostępem i tożsamościami w usłudze Cloudyn

1. Otwórz portal Cloudyn z poziomu witryny Azure Portal lub przejdź na stronę https://azure.cloudyn.com/ i zaloguj się.
2. Kliknij symbol koła zębatego, a następnie wybierz pozycję **Konta w chmurze**.
3. W obszarze Zarządzanie kontami wybierz kartę **Konta platformy AWS**, a następnie kliknij pozycję **Dodaj nowe +** .
4. W polu **Nazwa konta** wpisz nazwę konta.
5. Obok pozycji **Typ dostępu** wybierz pozycję **Rola systemu Zarządzanie dostępem i tożsamościami**.
6. W polu **role ARN** wklej wcześniej skopiowaną wartość, a następnie kliknij pozycję **Zapisz**.  
    ![wklej nazwę ARN roli w polu dodawania konta platformy AWS](./media/connect-aws-account/add-aws-account-box.png)


Twoje konto platformy AWS zostanie wyświetlone na liście kont. Wyświetlony na liście **identyfikator właściciela** jest zgodny z wartością pola Nazwa ARN roli. Obok informacji o **stanie konta** powinien znajdować się symbol zielonego znacznika wyboru wskazujący, że usługa Cloudyn może uzyskać dostęp do konta platformy AWS. Dopóki szczegółowe rozliczenia platformy AWS nie zostaną włączone, stan konsolidacji będzie wyświetlany jako **autonomiczny**.

![Stan konta platformy AWS wyświetlany na stronie Zarządzanie kontami](./media/connect-aws-account/aws-account-status01.png)

Usługa Cloudyn rozpoczyna zbieranie danych i wypełnianie raportów. Następnie należy [włączyć szczegółowe rozliczenia platformy AWS](#enable-detailed-aws-billing).


## <a name="aws-user-based-access"></a>Dostęp oparty na użytkownikach na platformie AWS

W poniższych sekcjach omówiono tworzenie roli tylko do odczytu w celu zapewnienia dostępu do usługi Cloudyn.

### <a name="add-aws-read-only-user-based-access"></a>Dodawanie dostępu tylko do odczytu opartego na użytkownikach na platformie AWS

1. Zaloguj się do konsoli platformy AWS na stronie [https://console.aws.amazon.com/iam/home](https://console.aws.amazon.com/iam/home), a następnie wybierz pozycję **Users (Użytkownicy)** .
2. Kliknij pozycję **Add User** (Dodaj użytkownika).
3. W polu **User name (Nazwa użytkownika)** wpisz nazwę użytkownika.
4. W polu **Access type (Typ dostępu)** wybierz pozycję **Programmatic access (Dostęp programowy)** , następnie kliknij pozycję **Next: Permissions** (Dalej: uprawnienia).  
    ![wprowadź nazwę użytkownika na stronie Add user (Dodawanie użytkownika)](./media/connect-aws-account/add-user01.png)
5. Na potrzeby uprawnień wybierz pozycję **Attach existing policies directly (Bezpośrednio dołącz istniejące zasady)** .
6. W obszarze **Attach permissions policies (Dołączanie zasad uprawnień)** w polu wyszukiwania filtru **Policy type (Typ zasad)** wpisz ciąg `ReadOnlyAccess`, wybierz pozycję **ReadOnlyAccess**, a następnie kliknij pozycję **Next: Review** (Dalej: przegląd).  
    ![wybierz pozycję ReadOnlyAccess, aby ustawić uprawnienia dla użytkownika](./media/connect-aws-account/set-permission-for-user.png)
7. Na stronie Review (Przegląd) upewnij się, że wybrane opcje są poprawne, a następnie kliknij pozycję **Create user (Utwórz użytkownika)** .
8. Na stronie Complete (Kończenie) wyświetlany jest identyfikatory kluczy dostępu i tajny klucz dostępu. Te informacje służą do konfigurowania rejestracji w usłudze Cloudyn.
9. Kliknij pozycję **Download .csv (Pobierz plik CSV)** , a następnie zapisz plik credentials.csv w bezpiecznej lokalizacji.  
    ![kliknij pozycję Download .csv (Pobierz plik CSV), aby zapisać poświadczenia](./media/connect-aws-account/download-csv.png)

### <a name="configure-aws-iam-user-based-access-in-cloudyn"></a>Konfigurowanie dostępu opartego na użytkownikach systemu Zarządzanie dostępem i tożsamościami platformy AWS w usłudze Cloudyn

1. Otwórz portal Cloudyn z poziomu witryny Azure Portal lub przejdź na stronę https://azure.cloudyn.com/ i zaloguj się.
2. Kliknij symbol koła zębatego, a następnie wybierz pozycję **Konta w chmurze**.
3. W obszarze Zarządzanie kontami wybierz kartę **Konta platformy AWS**, a następnie kliknij pozycję **Dodaj nowe +** .
4. W polu **Nazwa konta** wpisz nazwę konta.
5. Obok pozycji **Typ dostępu** wybierz pozycję **Użytkownik systemu Zarządzanie dostępem i tożsamościami**.
6. W polu **Klucz dostępu** wklej wartość **identyfikatora klucza dostępu** z pliku credentials.csv.
7. W polu **Klucz tajny** wklej wartość **tajnego klucza dostępu** z pliku credentials.csv, a następnie kliknij pozycję **Zapisz**.  

Twoje konto platformy AWS zostanie wyświetlone na liście kont. Obok informacji o **stanie konta** powinien znajdować się symbol zielonego znacznika wyboru.

Usługa Cloudyn rozpoczyna zbieranie danych i wypełnianie raportów. Następnie należy [włączyć szczegółowe rozliczenia platformy AWS](#enable-detailed-aws-billing).

## <a name="enable-detailed-aws-billing"></a>Włączanie szczegółowych rozliczeń platformy AWS

Wykonaj następujące kroki, aby uzyskać nazwę ARN roli platformy AWS. Nazwa ARN roli jest używana do udzielania uprawnień do odczytu w zasobniku rozliczeń.

1. Zaloguj się do konsoli platformy AWS na stronie [https://console.aws.amazon.com](https://console.aws.amazon.com), a następnie wybierz pozycję **Services (Usługi)** .
2. W polu wyszukiwania usług wpisz *IAM*, a następnie wybierz tę opcję.
3. Wybierz pozycję **Roles (Role)** z menu po lewej stronie.
4. Na liście ról wybierz rolę utworzoną na potrzeby dostępu do usługi Cloudyn.
5. Kliknij na stronie Roles Summary (Podsumowanie ról), aby skopiować **nazwę ARN roli**. Zachowaj nazwę ARN roli w celu użycia w kolejnych krokach.

### <a name="create-an-s3-bucket"></a>Tworzenie zasobnika usługi S3

Zasobnik usługi S3 jest tworzony w celu przechowywania szczegółowych informacji o rozliczeniach.

1. Zaloguj się do konsoli platformy AWS na stronie [https://console.aws.amazon.com](https://console.aws.amazon.com), a następnie wybierz pozycję **Services (Usługi)** .
2. W polu Service Search (Wyszukiwanie usługi) wpisz ciąg *S3*, a następnie wybierz pozycję **S3**.
3. Na stronie Amazon S3 kliknij pozycję **Create bucket (Utwórz zasobnik)** .
4. W kreatorze tworzenia zasobnika wybierz nazwę i region zasobnika, a następnie kliknij pozycję **Next (Dalej)** .  
    ![przykładowa informacja na stronie tworzenia zasobnika](./media/connect-aws-account/create-bucket.png)
5. Na stronie **Set properties (Ustaw właściwości)** zachowaj wartości domyślne, a następnie kliknij pozycję **Next (Dalej)** .
6. Na stronie Review (Przegląd) kliknij pozycję **Create bucket (Utwórz zasobnik)** . Zostanie wyświetlona lista zasobników.
7. Kliknij utworzony zasobnik i wybierz kartę **Permissions (Uprawnienia)** , a następnie wybierz pozycję **Bucket Policy (Zasady zasobnika)** . Zostanie otwarty edytor zasad zasobnika.
8. Skopiuj poniższy przykładowy kod JSON i wklej go w edytorze zasad zasobnika.
   - Zastąp ciąg `<BillingBucketName>` nazwą zasobnika usługi S3.
   - Zastąp ciąg `<ReadOnlyUserOrRole>` nazwą ARN roli lub użytkownika, która została wcześniej skopiowana.

   ```json
   {
      "Version": "2012-10-17",
      "Id": "Policy1426774604000",
      "Statement": [
          {
              "Sid": "Stmt1426774604000",
              "Effect": "Allow",
              "Principal": {
                  "AWS": "arn:aws:iam::386209384616:root"
              },
              "Action": [
                  "s3:GetBucketAcl",
                  "s3:GetBucketPolicy"
              ],
              "Resource": "arn:aws:s3:::<BillingBucketName>"
          },
          {
              "Sid": "Stmt1426774604001",
              "Effect": "Allow",
              "Principal": {
                  "AWS": "arn:aws:iam::386209384616:root"
              },
              "Action": "s3:PutObject",
              "Resource": "arn:aws:s3:::<BillingBucketName>/*"
          },
          {
              "Sid": "Stmt1426774604002",
              "Effect": "Allow",
              "Principal": {
                  "AWS": "<ReadOnlyUserOrRole>"
              },
              "Action": [
                  "s3:List*",
                  "s3:Get*"
              ],
              "Resource": "arn:aws:s3:::<BillingBucketName>/*"
          }
      ]
   }
   ```

9. Kliknij pozycję **Zapisz**.  
    ![kliknij pozycję Save (Zapisz) w edytorze zasad zasobnika](./media/connect-aws-account/bucket-policy-editor.png)


### <a name="enable-aws-billing-reports"></a>Włączanie raportów rozliczeń platformy AWS

Po utworzeniu i skonfigurowaniu zasobnika usługi S3 przejdź do obszaru [Billing Preferences (Preferencje rozliczeń)](https://console.aws.amazon.com/billing/home?#/preference) w konsoli platformy AWS.

1. Na stronie Preferencje wybierz pozycję **Receive Billing Reports (Odbieraj raporty dotyczące rozliczeń)** .
2. W obszarze **Receive Billing Reports (Odbieranie raportów dotyczących rozliczeń)** wprowadź nazwę utworzonego zasobnika, a następnie kliknij przycisk **Verify (Weryfikuj)** .  
3. Wybierz wszystkie cztery opcje szczegółowości raportu, a następnie kliknij pozycje **Save preferences (Zapisz preferencje)** .  
    ![wybierz stopień szczegółowości, aby włączyć raporty](./media/connect-aws-account/enable-reports.png)

Usługa Cloudyn pobiera szczegółowe informacje dotyczące rozliczeń z zasobnika usługi S3, a następnie wypełnia raporty po włączeniu szczegółowego rozliczania. Szczegółowe dane dotyczące rozliczeń pojawią się w konsoli usługi Cloudyn w ciągu 24 godzin. Gdy dostępne są szczegółowe dane dotyczące rozliczeń, stan konsolidacji konta będzie miał wartość **Consolidated (Skonsolidowany)** . Stan konta będzie miał wartość **Completed (Ukończono)** .

![stan konsolidacji wyświetlany na karcie kont platformy AWS](./media/connect-aws-account/consolidated-status.png)

Niektóre raporty dotyczące optymalizacji mogą wymagać danych z kilku dni, aby uzyskać odpowiedni rozmiar próbki danych w celu otrzymania dokładnych rekomendacji.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat usługi Cloudyn, przejdź do samouczka usługi Cloudyn [Przeglądanie użycia i kosztów](tutorial-review-usage.md).
