---
title: Konfigurowanie kont magazynu dla platformy Cloudyn na platformie Azure
description: W tym artykule opisano sposób konfigurowania kont magazynu platformy Azure i zasobników magazynu platformy AWS dla platformy Cloudyn.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ms.custom: secdec18
ROBOTS: NOINDEX
ms.openlocfilehash: 130542833f2f5e0d4ae63cfa7f681b390e46fa1e
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689664"
---
# <a name="configure-storage-accounts-for-cloudyn"></a>Konfigurowanie kont magazynu dla platformy Cloudyn

<!--- intent: As a Cloudyn user, I want to configure Cloudyn to use my cloud service provider storage account to store my reports. -->

Raporty Cloudyn można zapisywać w portalu Cloudyn, magazynie platformy Azure lub zasobnikach magazynu platformy AWS. Zapisywanie raportów w portalu Cloudyn jest bezpłatne. Natomiast zapisywanie raportów w magazynie dostawcy usług w chmurze jest opcjonalne i wiąże się z dodatkowymi kosztami. Ten artykuł pomaga w konfigurowaniu kont magazynu platformy Azure oraz zasobników magazynu platformy Amazon Web Services (AWS) w celu przechowywania raportów.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Musisz mieć konto magazynu platformy Azure lub zasobnik magazynu Amazon.

Jeśli nie masz konta magazynu platformy Azure, musisz je utworzyć. Aby uzyskać więcej informacji na temat tworzenia konta magazynu platformy Azure, zobacz [Tworzenie konta magazynu](../../storage/common/storage-account-create.md).

Jeśli nie masz zasobnika usługi Simple Storage Service (S3) AWS, musisz go utworzyć. Aby uzyskać więcej informacji na temat tworzenia zasobnika S3, zobacz [Create a Bucket (Tworzenie zasobnika)](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html).

## <a name="configure-your-azure-storage-account"></a>Konfigurowanie konta magazynu platformy Azure

Konfigurowanie magazynu platformy Azure do użycia przez platformę Cloudyn jest proste. Zbierz szczegółowe informacje o koncie magazynu i skopiuj je do portalu Cloudyn.

1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).
2. Kliknij pozycję **Wszystkie usługi**, wybierz pozycję **Konta magazynu**, przewiń do konta magazynu, którego chcesz użyć, a następnie wybierz konto.
3. Na stronie konta magazynu w sekcji **Ustawienia** kliknij pozycję **Klucze dostępu**.
4. Skopiuj wartości pól **Nazwa konta magazynu** i **Parametry połączenia** w obszarze klucz1.  
   ![Kopiowanie nazwy konta magazynu i parametrów połączenia](./media/storage-accounts/azure-storage-access-keys.png)  
5. Otwórz portal Cloudyn z poziomu witryny Azure Portal lub przejdź na stronę [https://azure.cloudyn.com](https://azure.cloudyn.com) i zaloguj się.
6. Kliknij symbol koła zębatego, a następnie wybierz pozycję **Zarządzanie magazynem raportów**.
7. Kliknij pozycję **Dodaj nowy +** i upewnij się, że wybrano platformę Microsoft Azure. Wklej nazwę konta magazynu platformy Azure w obszarze **Nazwa**. Wklej **parametry połączenia** w odpowiednim obszarze. Wprowadź nazwę kontenera, a następnie kliknij przycisk **Zapisz**.  
   ![Wklejanie nazwy konta magazynu platformy Azure i parametrów połączenia w oknie Dodawanie nowego magazynu raportów](./media/storage-accounts/azure-cloudyn-storage.png)

   Nowy wpis magazynu raportów platformy Azure zostanie wyświetlony na liście kont magazynu.  
    ![Nowy wpis magazynu raportów platformy Azure na liście](./media/storage-accounts/azure-storage-entry.png)


Teraz możesz zapisywać raporty w magazynie platformy Azure. W dowolnym raporcie kliknij pozycję **Akcje**, a następnie wybierz pozycję **Zaplanuj raport**. Nazwij raport, a następnie dodaj własny adres URL lub użyj automatycznie utworzonego adresu URL. Wybierz pozycję **Zapisz w magazynie**, a następnie wybierz konto magazynu. Wprowadź prefiks, który zostanie dołączony do nazwy pliku raportu. Wybierz format pliku CSV lub JSON, a następnie zapisz raport.

## <a name="configure-an-aws-storage-bucket"></a>Konfigurowanie zasobnika magazynu platformy AWS

Platforma Cloudyn używa istniejących poświadczeń platformy AWS: Użytkownik lub Rola, aby zapisywać raporty w zasobniku. W celu przetestowania dostępu platforma Cloudyn próbuje zapisać w zasobniku mały plik tekstowy o nazwie _check-bucket-permission.txt_.

Rolę lub użytkownika platformy Cloudyn udostępnij za pomocą uprawnienia PutObject w zasobniku. Następnie użyj istniejącego zasobnika lub utwórz nowy zasobnik, aby zapisywać raporty. Na koniec zdecyduj, jak zarządzać klasą magazynu, ustaw reguły cyklu życia lub usuń zbędne pliki.

###  <a name="assign-permissions-to-your-aws-user-or-role"></a>Przypisywanie uprawnień do użytkownika lub roli platformy AWS

Podczas tworzenia nowych zasad należy podać dokładne uprawnienia potrzebne do zapisywania raportów w zasobniku S3.

1. Zaloguj się do konsoli platformy AWS i wybierz pozycję **Services (Usługi)** .
2. Wybierz pozycję **IAM (Zarządzanie dostępem i tożsamościami)** z listy usług.
3. Wybierz pozycję **Policies (Zasady)** po lewej stronie konsoli, a następnie kliknij pozycję **Create Policy (Utwórz zasady)** .
4. Kliknij kartę **JSON**.
5. Poniższe zasady umożliwiają zapisanie raportu w zasobniku S3. Skopiuj i wklej poniższy przykład zasad do karty **JSON**. Zastąp zmienną &lt;bucketname&gt; nazwą Twojego zasobnika.

   ```json
   {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Sid":  "CloudynSaveReport2S3",
        "Effect":      "Allow",
        "Action": [
          "s3:PutObject"
        ],
        "Resource": [
          "arn:aws:s3:::<bucketname>/*"
        ]
      }
    ]
   }
   ```

6. Kliknij przycisk **Review policy (Przejrzyj zasady)** .  
    ![Zasady JSON platformy AWS z przykładowymi informacjami](./media/storage-accounts/aws-policy.png)  
7. Na stronie Review policy (Przegląd zasad) wpisz nazwę zasad. Na przykład _CloudynSaveReport2S3_.
8. Kliknij pozycję **Create policy (Utwórz zasady)** .

### <a name="attach-the-policy-to-a-cloudyn-role-or-user-in-your-account"></a>Dołączanie zasad do roli lub użytkownika platformy Cloudyn w ramach konta

Aby dołączyć nowe zasady, należy otworzyć konsolę platformy AWS i edytować rolę lub użytkownika platformy Cloudyn.

1. Zaloguj się do konsoli platformy AWS i wybierz pozycję **Services (Usługi)** , a następnie z listy usług wybierz pozycję **IAM (Zarządzanie dostępem i tożsamościami)** .
2. Wybierz opcję **Roles (Role)** lub **Users (Użytkownicy)** z lewej strony konsoli.

**W przypadku ról:**

  1. Kliknij nazwę roli platformy Cloudyn.
  2. Na karcie **Permissions (Uprawnienia)** kliknij pozycję **Attach Policy (Dołącz zasady)** .
  3. Wyszukaj utworzone zasady i wybierz je, a następnie kliknij przycisk **Attach Policy (Dołącz zasady)** .
    ![Przykładowe zasady dołączone do roli platformy Cloudyn](./media/storage-accounts/aws-attach-policy-role.png)

**W przypadku użytkowników:**

1. Wybierz użytkownika platformy Cloudyn.
2. Na karcie **Permissions (Uprawnienia)** kliknij pozycję **Add permissions (Dodaj uprawnienia)** .
3. W sekcji **Grant Permission (Udzielanie uprawnienia)** wybierz pozycję **Attach existing policies directly (Dołącz istniejące zasady bezpośrednio)** .
4. Wyszukaj utworzone zasady i wybierz je, a następnie kliknij przycisk **Next: Review** (Dalej: przegląd).
5. Na stronie Add permissions to role name (Dodawanie uprawnień do nazwy roli) kliknij pozycję **Add permissions (Dodaj uprawnienia)** .  
    ![Przykładowe zasady dołączone do użytkownika platformy Cloudyn](./media/storage-accounts/aws-attach-policy-user.png)


### <a name="optional-set-permission-with-bucket-policy"></a>Opcjonalnie: Ustawianie uprawnienia przy użyciu zasad zasobnika

Uprawnienie do tworzenia raportów w zasobniku S3 możesz również ustawić przy użyciu zasad zasobników. W klasycznym widoku S3:

1. Utwórz lub wybierz istniejący zasobnik.
2. Wybierz kartę **Permissions (Uprawnienia)** , a następnie kliknij pozycję **Bucket policy (Zasady zasobnika)** .
3. Skopiuj i wklej następujący przykład zasad. Zastąp zmienne &lt;bucket\_name&gt; i &lt;Cloudyn\_principle&gt; nazwą ARN zasobnika. Zastąp nazwę ARN roli lub użytkownika używanych przez platformę Cloudyn.

   ```
   {
   "Id": "Policy1485775646248",
   "Version": "2012-10-17",
   "Statement": [
    {
      "Sid": "SaveReport2S3",
      "Action": [
        "s3:PutObject"
      ],
      "Effect": "Allow",
      "Resource": "<bucket_name>/*",
      "Principal": {
        "AWS": [
          "<Cloudyn_principle>"
        ]
      }
    }
   ]
   }
   ```

4. W edytorze Bucket policy (Zasady zasobnika) kliknij przycisk **Save (Zapisz)** .

### <a name="add-aws-report-storage-to-cloudyn"></a>Dodawanie magazynu raportów platformy AWS do platformy Cloudyn

1. Otwórz portal Cloudyn z poziomu witryny Azure Portal lub przejdź na stronę [https://azure.cloudyn.com](https://azure.cloudyn.com) i zaloguj się.
2. Kliknij symbol koła zębatego, a następnie wybierz pozycję **Zarządzanie magazynem raportów**.
3. Kliknij pozycję **Dodaj nowy +** i upewnij się, że wybrano platformę AWS.
4. Wybierz konto i zasobnik magazynu. Nazwa zasobnika magazynu platformy AWS zostanie automatycznie wypełniona.  
    ![Przykładowe informacje w oknie Dodawanie nowego magazynu raportów](./media/storage-accounts/aws-cloudyn-storage.png)  
5. Kliknij przycisk **Zapisz**, a następnie przycisk **OK**.

    Nowy wpis magazynu raportów platformy AWS zostanie wyświetlony na liście kont magazynu.  
    ![Nowy wpis magazynu raportów platformy AWS wyświetlany na liście kont magazynu](./media/storage-accounts/aws-storage-entry.png)


Teraz możesz zapisywać raporty w magazynie platformy Azure. W dowolnym raporcie kliknij pozycję **Akcje**, a następnie wybierz pozycję **Zaplanuj raport**. Nazwij raport, a następnie dodaj własny adres URL lub użyj automatycznie utworzonego adresu URL. Wybierz pozycję **Zapisz w magazynie**, a następnie wybierz konto magazynu. Wprowadź prefiks, który zostanie dołączony do nazwy pliku raportu. Wybierz format pliku CSV lub JSON, a następnie zapisz raport.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z artykułem [Informacje o raportach platformy Cloudyn](understanding-cost-reports.md), aby poznać podstawową strukturę i funkcje raportów platformy Cloudyn.
