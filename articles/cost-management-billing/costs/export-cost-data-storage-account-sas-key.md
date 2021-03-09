---
title: Eksportowanie danych kosztów za pomocą klucza SAS konta usługi Azure Storage
description: Ten artykuł pomaga partnerom utworzyć klucz sygnatury dostępu współdzielonego i skonfigurować Cost Managemente eksporty.
author: bandersmsft
ms.author: banders
ms.date: 03/08/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 16302a6bcc3bf41432500d2fdaa4ec27c28dd5b3
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102509684"
---
# <a name="export-cost-data-with-an-azure-storage-account-sas-key"></a>Eksportowanie danych kosztów za pomocą klucza SAS konta usługi Azure Storage

Poniższe informacje dotyczą tylko partnerów firmy Microsoft.

Często partnerzy nie mają własnych subskrypcji platformy Azure w dzierżawie, które są skojarzone z umową Partnerskią firmy Microsoft. Partnerzy z planem umowy partnerskiej firmy Microsoft, którzy są administratorami globalnymi konta rozliczeniowego, mogą eksportować i kopiować dane kosztów do konta magazynu w innej dzierżawie przy użyciu klucza usługi dostępu współdzielonego (SAS). Innymi słowy, konto magazynu z kluczem sygnatury dostępu współdzielonego pozwala partnerowi na korzystanie z konta magazynu poza umową partnerskią w celu otrzymywania wyeksportowanych informacji. Ten artykuł pomaga partnerom utworzyć klucz sygnatury dostępu współdzielonego i skonfigurować Cost Managemente eksporty.

## <a name="requirements"></a>Wymagania

- Musisz być partnerem z umową Partnerskią firmy Microsoft i mieć klienci z planem platformy Azure.
- Musisz być administratorem globalnym dla konta rozliczeniowego organizacji partnera.
- Musisz mieć dostęp, aby skonfigurować konto magazynu znajdujące się w innej dzierżawie organizacji partnerskiej. Użytkownik jest odpowiedzialny za utrzymywanie uprawnień i dostępu do danych podczas eksportowania danych do konta magazynu.

## <a name="configure-azure-storage-with-a-sas-key"></a>Konfigurowanie usługi Azure Storage przy użyciu klucza SAS

Pobierz token SAS konta magazynu lub utwórz go przy użyciu Azure Portal. Aby utworzyć na Azure Portal, wykonaj następujące czynności. Aby dowiedzieć się więcej o kluczach SAS, zobacz [udzielanie ograniczonego dostępu do danych za pomocą sygnatur dostępu współdzielonego (SAS).](../../storage/common/storage-sas-overview.md)

1. Przejdź do konta magazynu w Azure Portal.
    - Jeśli Twoje konto ma dostęp do wielu dzierżawców, przełącz katalogi, aby uzyskać dostęp do konta magazynu. Wybierz swoje konto w prawym górnym rogu Azure Portal a następnie wybierz pozycję **Przełącz katalogi**.
    - Może być konieczne zalogowanie się do Azure Portal przy użyciu odpowiedniego konta dzierżawy w celu uzyskania dostępu do konta magazynu.
1. W menu po lewej stronie wybierz pozycję **sygnatura dostępu współdzielonego**.  
    :::image type="content" source="./media/export-cost-data-storage-account-sas-key/storage-shared-access-signature.png" alt-text="Zrzut ekranu przedstawiający skonfigurowaną sygnaturę dostępu współdzielonego usługi Azure Storage." lightbox="./media/export-cost-data-storage-account-sas-key/storage-shared-access-signature.png" :::
1. Skonfiguruj token z tymi samymi ustawieniami, które zostały zidentyfikowane w poprzednim obrazie.
    1. Wybierz pozycję **obiekt BLOB** dla _dozwolonych usług_.
    1. Wybierz **usługę**, **kontener** i **obiekt** dla _dozwolonych typów zasobów_.
    1. Wybierz opcję **Odczyt**, **zapis**, **usuwanie**, **Wyświetlanie**, **Dodawanie** i **Tworzenie** dla _dozwolonych uprawnień_.
    1. Wybierz datę wygaśnięcia i daty. Pamiętaj, aby zaktualizować token eksportu SAS przed jego wygaśnięciem. Im dłuższy czas konfigurowania przed wygaśnięciem, tym dłużej eksport zostanie uruchomiony przed potrzebą nowego tokenu sygnatury dostępu współdzielonego.
1. Wybierz opcję **tylko https** dla _dozwolonych protokołów_.
1. Wybierz pozycję **podstawowa** dla _preferowanej warstwy routingu_.
1. Wybierz pozycję **Klucz1** dla _klucza podpisywania_. W przypadku rotacji lub aktualizacji klucza używanego do podpisywania tokenu SAS należy ponownie wygenerować nowy token sygnatury dostępu współdzielonego w celu wyeksportowania.
1. Wybierz pozycję **Generuj sygnaturę dostępu współdzielonego i parametry połączenia**.
    Pokazana wartość **tokenu sygnatury dostępu współdzielonego** to token, który jest wymagany podczas konfigurowania eksportu.

## <a name="create-a-new-export-with-a-sas-token"></a>Tworzenie nowego eksportu z tokenem SAS

Przejdź do sekcji **eksporty** w zakresie konta rozliczeniowego i Utwórz nowy eksport, wykonując poniższe kroki.

1. Wybierz przycisk **Utwórz**.
1. Skonfiguruj szczegóły eksportu tak jak w przypadku normalnego eksportu. Eksport można skonfigurować tak, aby korzystał z istniejącego katalogu lub kontenera lub można określić nowy katalog lub kontener, a eksport utworzy je dla Ciebie.
1. Podczas konfigurowania magazynu wybierz opcję **Użyj tokenu sygnatury dostępu współdzielonego**.  
    :::image type="content" source="./media/export-cost-data-storage-account-sas-key/new-export.png" alt-text="Zrzut ekranu przedstawiający nowy eksport, w którym wybiera się token SAS." lightbox="./media/export-cost-data-storage-account-sas-key/new-export.png" :::
1. Wprowadź nazwę konta magazynu i wklej je w tokenie SAS.
1. Określ istniejący kontener lub katalog lub Zidentyfikuj nowe, które mają zostać utworzone.
1. Wybierz przycisk **Utwórz**.

Eksport oparty na tokenach SAS działa tylko wtedy, gdy token pozostaje ważny. Zresetuj token przed upływem bieżącego miesiąca lub eksport przestanie działać. Ponieważ token zapewnia dostęp do konta magazynu, należy chronić token tak dokładnie, jak wszystkie inne poufne informacje. Użytkownik jest odpowiedzialny za zachowanie uprawnień i dostępu do danych podczas eksportowania danych do konta magazynu.

## <a name="troubleshoot-exports-using-sas-tokens"></a>Rozwiązywanie problemów z eksportami przy użyciu tokenów SAS

Poniżej przedstawiono typowe problemy, które mogą wystąpić podczas konfigurowania lub używania eksportów opartych na tokenach SAS.

- Nie widzisz opcji klucza sygnatury dostępu współdzielonego w Azure Portal.
  - Sprawdź, czy jesteś partnerem z umową Partnerskią firmy Microsoft i masz uprawnienia administratora globalnego do konta rozliczeniowego. Są jedynymi osobami, które mogą eksportować za pomocą klucza SAS.

- Podczas próby skonfigurowania eksportu zostanie wyświetlony następujący komunikat o błędzie:

    **Upewnij się, że token sygnatury dostępu współdzielonego jest prawidłowy dla usługi BLOB Service, jest prawidłowy dla typów zasobów kontenera i obiektów i ma uprawnienia: Dodaj polecenie Utwórz odczyt zapisu Usuń. (Kod błędu usługi magazynu: AuthorizationResourceTypeMismatch)**

    - Upewnij się, że poprawnie konfigurujesz i wygenerujesz klucz sygnatury dostępu współdzielonego w usłudze Azure Storage.

- Po utworzeniu eksportu nie jest wyświetlany pełny klucz sygnatury dostępu współdzielonego.
  - Nie widzisz klucza, oczekiwane zachowanie. Po skonfigurowaniu eksportu sygnatury dostępu współdzielonego klucz jest ukryty ze względów bezpieczeństwa.

- Nie możesz uzyskać dostępu do konta magazynu z dzierżawy, w której skonfigurowano eksport.
  - Jest to oczekiwane zachowanie. Jeśli konto magazynu znajduje się w innej dzierżawie, musisz najpierw przejść do tej dzierżawy w Azure Portal, aby znaleźć konto magazynu.

- Eksport nie powiedzie się z powodu błędu związanego z tokenem sygnatury dostępu współdzielonego.
  - Eksport działa tylko wtedy, gdy token sygnatury dostępu współdzielonego jest nieprawidłowy. Utwórz nowy klucz i Uruchom eksport.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat eksportowania danych Cost Management, zobacz [Tworzenie i eksportowanie danych](tutorial-export-acm-data.md).
- Aby uzyskać informacje na temat eksportowania dużych ilości danych użycia, zobacz temat [pobieranie dużych zestawów DataSet z eksportami](ingest-azure-usage-at-scale.md).
