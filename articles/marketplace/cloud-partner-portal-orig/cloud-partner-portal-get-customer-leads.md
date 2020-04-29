---
title: Konfigurowanie potencjalnych klientów | Portal Azure Marketplace
description: Skonfiguruj potencjalnych klientów w portal Cloud Partner.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 56012fb2a907a6db6f87554660ee36b99a3dcbf9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80280324"
---
<a name="get-customer-leads"></a>Pozyskiwanie potencjalnych klientów
==================

W tym artykule opisano sposób tworzenia potencjalnych klientów za pomocą portal Cloud Partner. Możesz połączyć te potencjalni klienci z systemem CRM i zintegrować je z potoku sprzedaży.

## <a name="leads"></a>Leads

Potencjalni klienci są zainteresowani lub wdrażają produkty z [witryny Azure Marketplace](https://azuremarketplace.microsoft.com/) lub z [AppSource](https://appsource.microsoft.com).

### <a name="azure-marketplace"></a>Azure Marketplace

1.  Klient potrzebuje "wersji testowej" dla oferty. Testowanie dysków to przyspieszona okazja do natychmiastowego udostępnienia firmie potencjalnych klientom bez żadnych przeszkód. Wszystkie dyski testowe generują potencjalnego klienta dla klienta, który chce próbować uzyskać więcej informacji. Dowiedz się więcej o testowaniu dysków w witrynie [Azure Marketplace Test Drive](https://azuremarketplace.azureedge.net/documents/azure-marketplace-test-drive-program.pdf).

    ![Przykłady dotyczące dysków testowych Marketplace](./media/cloud-partner-portal-get-customer-leads/test-drive-offer.png)
 

<!-- -->

1. Klient wyraził zgodę na udostępnianie informacji po wybraniu pozycji "Pobierz teraz". Ten **potencjalny klient jest potencjalnym liderem** biznesowym, gdzie udostępniamy informacje o klientach, którzy wyrażają zainteresowanie uzyskaniem produktu. Potencjalny klient to Góra lejka pozyskiwania.

   ![Pobierz opcję teraz](./media/cloud-partner-portal-get-customer-leads/get-it-now-button.png)

1. Klient wybierze opcję "Kup" w [witrynie Azure Portal](https://portal.azure.com/) , aby uzyskać produkt. Ten potencjalny klient jest **aktywnym** liderem, gdzie udostępniamy informacje o kliencie, który rozpoczął wdrażanie produktu.

   ![Opcja zakupu](./media/cloud-partner-portal-get-customer-leads/purchase-button.png)


### <a name="appsource"></a>AppSource

1.  Klient potrzebował "Test Drive" dla oferty. Testowanie dysków to przyspieszona okazja do natychmiastowego udostępnienia firmie potencjalnych klientom bez żadnych przeszkód. Wszystkie dyski testowe generują potencjalnego klienta dla klienta, który chce próbować uzyskać więcej informacji. Dowiedz się więcej o dyskach testowych na [AppSource testowym](https://appsource.microsoft.com/blogs/want-to-try-an-app-take-a-test-drive).

    ![Przykład dysku testowego](./media/cloud-partner-portal-get-customer-leads/test-drive-offer-2.png)

2.  Klient wyraził zgodę na udostępnianie informacji po wybraniu pozycji "Pobierz teraz". Ten **potencjalny klient jest potencjalnym liderem** , gdzie udostępniamy informacje o klientach, którzy wyrażają zainteresowanie uzyskaniem produktu. Potencjalny klient to Góra lejka pozyskiwania.

      ![Pobierz opcję teraz](./media/cloud-partner-portal-get-customer-leads/get-it-now-button-2.png)


3.  Klient wybierze opcję "Skontaktuj się z nami" w swojej ofercie. Ten potencjalny klient jest **aktywnym** liderem, w którym udostępniamy informacje o kliencie, który prosi o podanie informacji o produkcie.

    ![Opcja kontakt ze mną](./media/cloud-partner-portal-get-customer-leads/contact-me-image.png)

<a name="lead-data"></a>Dane potencjalnego klienta
---------

Każdy potencjalny klient, który otrzymasz w procesie pozyskiwania klientów, ma dane w określonych polach. Ponieważ będziesz otrzymywać potencjalni klienci z wielu kroków, najlepszym sposobem obsługi potencjalnych klientów jest cofnięcie duplikatu i spersonalizowanie kolejnych działań. W ten sposób każdy klient otrzymuje odpowiedni komunikat i tworzysz unikatową relację.

### <a name="lead-source"></a>Źródło potencjalnego klienta

Format dla źródła potencjalnego klienta to**Oferta** **akcji** |   **źródłowej**-

**Źródła**: "AzureMarketplace", "AzurePortal", "TestDrive" i "APPSOURCE (SPZA)"

**Akcje**:
- "INS" — instalacja. Ta akcja znajduje się w witrynie Azure Marketplace lub AppSource, gdy klient kupuje produkt.
- "PLT" — oznacza próbę przetestowania partnera. Ta akcja jest włączona w AppSource, gdy klient korzysta z opcji kontakt ze mną.
- "DNC" — nie należy kontaktować się z. Ta akcja jest włączona w witrynie AppSource, gdy Partner, który został krzyżowo wymieniony na stronie Twojej aplikacji, otrzymuje prośbę o kontakt. Udostępniamy swoje nagłówki, że ten klient został krzyżowo wymieniony w Twojej aplikacji, ale nie trzeba się z nimi skontaktować.
- "Utwórz" — Ta akcja jest dostępna tylko w witrynie Azure Portal i jest generowana, gdy klient kupi ofertę na swoje konto.
- "StartTestDrive" — Ta akcja dotyczy tylko dysków testowych i jest generowana, gdy klient uruchamia ich dysk testowy.

**Oferty**

W poniższych przykładach przedstawiono unikatowe identyfikatory przypisane do wydawcy i konkretną ofertę: punkt kontrolny. Check-r77-10sg-BYOL, Bitnami. openedxcypress i Docusign. 3701c77e-1cfa-4c56-91e6-3ed0b622145a.


### <a name="customer-info"></a>Informacje o kliencie

Pola w poniższym przykładzie pokazują informacje o kliencie, które znajdują się w potencjalnym kliencie.
- Imię: Jan
- LastName: Kowalski
- Poczta e-mail:\@jsmith Microsoft.com
- Telefon: 1234567890
- Kraj: US
- Firma: Microsoft
- Tytuł: dyrektor ds

>[!Note]
>Nie wszystkie dane w poprzednim przykładzie są zawsze dostępne dla każdego potencjalnego klienta.

Aktywnie pracujemy nad ulepszaniem potencjalnych klientów, dlatego jeśli istnieje pole danych, którego nie widzisz tutaj, ale chcesz, [Prześlij nam swoją opinię](mailto:AzureMarketOnboard@microsoft.com).

<a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>Jak połączyć system CRM z portal Cloud Partner
------------------------------------------------------------

Aby rozpocząć uzyskiwanie potencjalnych klientów, utworzyliśmy nasz łącznik zarządzania potencjalnym liderem na portal Cloud Partner, dzięki czemu możesz łatwo podłączyć informacje o programie CRM i nawiązać połączenie. Teraz można łatwo korzystać z potencjalnych klientów wygenerowanych przez platformę Marketplace bez znacznego wysiłku inżynieryjnego do integracji z systemem zewnętrznym.

![Łącznik zarządzania potencjalnymi klientami](./media/cloud-partner-portal-get-customer-leads/lead-management-connector.png)

Możemy napisać potencjalni klienci do różnych systemów CRM lub bezpośrednio do tabeli usługi Azure Storage, w której można zarządzać potencjalnymi klientami. Każdy z poniższych linków zawiera instrukcje dotyczące łączenia się z możliwymi miejscami docelowymi potencjalnych klientów:

-   [Dynamics CRM Online](./cloud-partner-portal-lead-management-instructions-dynamics.md) , aby uzyskać instrukcje dotyczące konfigurowania usługi Dynamics CRM Online na potrzeby uzyskiwania potencjalnych klientów.
-   [Zapoznaj się z](./cloud-partner-portal-lead-management-instructions-marketo.md) instrukcjami dotyczącymi konfigurowania konfiguracji lidera usługi Marketo w celu uzyskania potencjalnych klientów.
-    Uzyskaj instrukcje dotyczące konfigurowania wystąpienia usługi Salesforce [, aby uzyskać](./cloud-partner-portal-lead-management-instructions-salesforce.md) potencjalnych klientów.
-    [Azure Table](./cloud-partner-portal-lead-management-instructions-azure-table.md) , aby uzyskać instrukcje dotyczące konfigurowania konta usługi Azure Storage na potrzeby uzyskiwania potencjalnych klientów w tabeli platformy Azure.
-   [Punkt końcowy https](./cloud-partner-portal-lead-management-instructions-https.md) , aby uzyskać instrukcje dotyczące konfigurowania punktu końcowego HTTPS w celu uzyskania potencjalnych klientów.

Po skonfigurowaniu miejsca docelowego potencjalnego klienta i opublikowaniu oferty będziemy sprawdzać poprawność połączenia i wysyłać do Ciebie potencjalnego klienta. Gdy przeglądasz ofertę przed rozpoczęciem pracy, możesz również przetestować połączenie z liderem, próbując uzyskać swoją ofertę w środowisku wersji zapoznawczej. Ważne jest, aby upewnić się, że Twoje ustawienia lidera są aktualne, dzięki czemu nie utracisz żadnych potencjalnych klientów, więc pamiętaj, aby zaktualizować te połączenia, gdy coś się zmieniło.

<a name="what-next"></a>Co dalej?
----------

Po zakończeniu konfigurowania technicznego należy uwzględnić te potencjalni klienci w obecnej strategii marketingowej i procesów operacyjnych & sprzedaży. Bardzo interesuje Cię lepsze zrozumienie ogólnego procesu sprzedaży i chcemy ściśle współpracować z udostępnianiem wysokiej jakości potencjalnych klientów i wystarczającej ilości danych, aby zapewnić pomyślne działanie. Będziemy nam poznamy Twoją opinię na temat sposobu, w jaki możemy zoptymalizować i zwiększyć liczbę potencjalnych klientów, którzy wyślą Ci dodatkowe dane w celu pomyślnego przeprowadzenia tych klientów. Poinformuj nas o tym, czy interesuje Cię [przekazywanie opinii](mailto:AzureMarketOnboard@microsoft.com) i sugestii, aby ułatwić zespołowi ds. sprzedaży pomyślną pracę z potencjalnymi klientami portalu Marketplace.
