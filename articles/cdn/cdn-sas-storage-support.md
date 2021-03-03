---
title: Używanie Azure CDN z sygnaturą dostępu współdzielonego | Microsoft Docs
description: Azure CDN obsługuje używanie sygnatury dostępu współdzielonego (SAS) do przyznawania ograniczonego dostępu do prywatnych kontenerów magazynu.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/21/2018
ms.author: allensu
ms.openlocfilehash: ccf55e0e3986de8afe23cb646d4df743b576900c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101725326"
---
# <a name="using-azure-cdn-with-sas"></a>Używanie Azure CDN z sygnaturą dostępu współdzielonego

Po skonfigurowaniu konta magazynu dla usługi Azure Content Delivery Network (CDN) do użycia w celu przechowania zawartości w pamięci podręcznej, domyślnie każda osoba znająca adresy URL kontenerów magazynu może uzyskać dostęp do plików, które zostały przekazane. Aby chronić pliki na koncie magazynu, można ustawić dostęp do kontenerów magazynu z publicznego do prywatnego. Jednak w takim przypadku nikt nie będzie mógł uzyskać dostępu do plików. 

Jeśli chcesz przyznać ograniczony dostęp do prywatnych kontenerów magazynu, możesz użyć funkcji sygnatury dostępu współdzielonego konta usługi Azure Storage. Sygnatura dostępu współdzielonego to identyfikator URI, który zapewnia ograniczone prawa dostępu do zasobów usługi Azure Storage bez ujawniania klucza konta. Możesz udostępnić sygnatury dostępu współdzielonego klientom, którzy nie są zaufani przy użyciu klucza konta magazynu, ale do kogo chcesz delegować dostęp do określonych zasobów konta magazynu. Przez dystrybuowanie identyfikatora URI sygnatury dostępu współdzielonego do tych klientów można przyznać im dostęp do zasobu przez określony czas.
 
Za pomocą sygnatury dostępu współdzielonego można definiować różne parametry, które mają dostęp do obiektu BLOB, takie jak godziny rozpoczęcia i wygaśnięcia, uprawnienia (odczyt/zapis) i zakresy adresów IP. W tym artykule opisano, jak używać sygnatury dostępu współdzielonego w połączeniu z Azure CDN. Aby uzyskać więcej informacji na temat sygnatury dostępu współdzielonego, w tym sposobu tworzenia jej opcji i parametrów, zobacz [using Shared Access Signatures (SAS)](../storage/common/storage-sas-overview.md).

## <a name="setting-up-azure-cdn-to-work-with-storage-sas"></a>Konfigurowanie Azure CDN do pracy z funkcją SAS magazynu
Poniższe trzy opcje są zalecane w przypadku używania sygnatury dostępu współdzielonego z Azure CDN. Wszystkie opcje zakładają, że utworzono już działające SAS (Zobacz wymagania wstępne). 
 
### <a name="prerequisites"></a>Wymagania wstępne
Aby rozpocząć, Utwórz konto magazynu, a następnie Wygeneruj sygnaturę dostępu współdzielonego dla zasobu. Można generować dwa typy sygnatur dostępu: SAS lub SAS usługi. Aby uzyskać więcej informacji, zobacz [typy sygnatur dostępu współdzielonego](../storage/common/storage-sas-overview.md#types-of-shared-access-signatures).

Po wygenerowaniu tokenu SAS można uzyskać dostęp do pliku magazynu obiektów BLOB przez dołączenie `?sv=<SAS token>` go do adresu URL. Ten adres URL ma następujący format: 

`https://<account name>.blob.core.windows.net/<container>/<file>?sv=<SAS token>`
 
Na przykład:
 ```
https://democdnstorage1.blob.core.windows.net/container1/demo.jpg?sv=2017-07-29&ss=b&srt=co&sp=r&se=2038-01-02T21:30:49Z&st=2018-01-02T13:30:49Z&spr=https&sig=QehoetQFWUEd1lhU5iOMGrHBmE727xYAbKJl5ohSiWI%3D
```

Aby uzyskać więcej informacji na temat parametrów ustawień, zobacz [uwagi dotyczące parametrów SAS](#sas-parameter-considerations) i [sygnatury dostępu współdzielonego](../storage/common/storage-sas-overview.md#how-a-shared-access-signature-works).

![Ustawienia SAS sieci CDN](./media/cdn-sas-storage-support/cdn-sas-settings.png)

### <a name="option-1-using-sas-with-pass-through-to-blob-storage-from-azure-cdn"></a>Opcja 1: używanie sygnatury dostępu współdzielonego z przekazywaniem do magazynu obiektów blob z programu Azure CDN

Ta opcja jest najprostszym i używa jednego tokenu sygnatury dostępu współdzielonego, który jest przesyłany z Azure CDN do serwera pochodzenia.
 
1. Wybierz punkt końcowy, wybierz pozycję **reguły buforowania**, a następnie wybierz pozycję **Buforuj każdy unikatowy adres URL** z listy **buforowanie ciągu zapytania** .

    ![Reguły buforowania sieci CDN](./media/cdn-sas-storage-support/cdn-caching-rules.png)

2. Po skonfigurowaniu sygnatury dostępu współdzielonego na koncie magazynu musisz użyć tokenu sygnatury dostępu współdzielonego z adresami URL punktu końcowego usługi CDN i serwerem źródłowym, aby uzyskać dostęp do pliku. 
   
   Otrzymany adres URL punktu końcowego usługi CDN ma następujący format: `https://<endpoint hostname>.azureedge.net/<container>/<file>?sv=<SAS token>`

   Na przykład:   
   ```
   https://demoendpoint.azureedge.net/container1/demo.jpg/?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   
3. Dostosuj czas trwania pamięci podręcznej przy użyciu reguł buforowania lub dodając `Cache-Control` nagłówki na serwerze źródłowym. Ponieważ Azure CDN traktuje token sygnatury dostępu współdzielonego jako zwykły ciąg zapytania, najlepszym rozwiązaniem jest skonfigurowanie czasu trwania buforowania, który wygasa o lub przed upływem czasu wygaśnięcia sygnatury dostępu współdzielonego. W przeciwnym razie, jeśli plik jest buforowany przez dłuższy czas, niż jest aktywny, plik może być dostępny z serwera pochodzenia Azure CDN po upływie czasu wygaśnięcia sygnatury dostępu współdzielonego. Jeśli wystąpi taka sytuacja, a chcesz uniemożliwić dostęp do pamięci podręcznej, należy wykonać operację przeczyszczania pliku, aby wyczyścić ją z pamięci podręcznej. Aby uzyskać informacje o ustawianiu czasu trwania pamięci podręcznej na Azure CDN, zobacz [kontrola Azure CDN zachowanie buforowania przy użyciu reguł buforowania](cdn-caching-rules.md).

### <a name="option-2-hidden-cdn-sas-token-using-a-rewrite-rule"></a>Opcja 2: ukryty token SAS sieci CDN przy użyciu reguły ponownego zapisu
 
Ta opcja jest dostępna tylko dla **Azure CDN Premium z profilów Verizon** . Za pomocą tej opcji można zabezpieczyć magazyn obiektów BLOB na serwerze źródłowym. Możesz użyć tej opcji, jeśli nie potrzebujesz określonych ograniczeń dostępu do pliku, ale chcesz uniemożliwić użytkownikom dostęp do źródła danych źródłowych bezpośrednio w celu usprawnienia Azure CDN odciążania. Token sygnatury dostępu współdzielonego, który jest nieznany dla użytkownika, jest wymagany dla każdego, kto uzyskuje dostęp do plików znajdujących się w określonym kontenerze serwera pochodzenia. Jednak ze względu na regułę ponownego zapisywania adresu URL token sygnatury dostępu współdzielonego nie jest wymagany w punkcie końcowym usługi CDN.
 
1. Użyj [aparatu reguł](./cdn-verizon-premium-rules-engine.md) , aby utworzyć regułę ponownego zapisywania adresu URL. Propagowanie nowych reguł trwa do 4 godzin.

   ![Przycisk zarządzania CDN](./media/cdn-sas-storage-support/cdn-manage-btn.png)

   ![Przycisk aparatu reguł sieci CDN](./media/cdn-sas-storage-support/cdn-rules-engine-btn.png)

   Następująca przykładowa reguła ponownego zapisywania adresu URL używa wzorca wyrażenia regularnego z grupą przechwytywania i punktem końcowym o nazwie *sasstoragedemo*:
   
   Źródło:   
   `(container1/.*)`


   Miejsce docelowe:   
   ```
   $1?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![Reguła ponownego zapisywania adresu URL usługi CDN — pozostała ](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
    ![ reguła ponownego zapisywania adresu URL sieci CDN — uprawnienie](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

2. Gdy Nowa reguła stanie się aktywna, każdy może uzyskać dostęp do plików w określonym kontenerze w punkcie końcowym usługi CDN, niezależnie od tego, czy używali tokenu SAS w adresie URL. Oto format: `https://<endpoint hostname>.azureedge.net/<container>/<file>`
 
   Na przykład:   
   `https://sasstoragedemo.azureedge.net/container1/demo.jpg`
       

3. Dostosuj czas trwania pamięci podręcznej przy użyciu reguł buforowania lub dodając `Cache-Control` nagłówki na serwerze źródłowym. Ponieważ Azure CDN traktuje token sygnatury dostępu współdzielonego jako zwykły ciąg zapytania, najlepszym rozwiązaniem jest skonfigurowanie czasu trwania buforowania, który wygasa o lub przed upływem czasu wygaśnięcia sygnatury dostępu współdzielonego. W przeciwnym razie, jeśli plik jest buforowany przez dłuższy czas, niż jest aktywny, plik może być dostępny z punktu końcowego Azure CDN po upływie czasu wygaśnięcia sygnatury dostępu współdzielonego. Jeśli wystąpi taka sytuacja, a chcesz uniemożliwić dostęp do pamięci podręcznej, należy wykonać operację przeczyszczania pliku, aby wyczyścić ją z pamięci podręcznej. Aby uzyskać informacje o ustawianiu czasu trwania pamięci podręcznej na Azure CDN, zobacz [kontrola Azure CDN zachowanie buforowania przy użyciu reguł buforowania](cdn-caching-rules.md).

### <a name="option-3-using-cdn-security-token-authentication-with-a-rewrite-rule"></a>Opcja 3: używanie uwierzytelniania tokenu zabezpieczającego sieci CDN z regułą ponownego zapisu

Aby można było korzystać z uwierzytelniania Azure CDN tokenów zabezpieczających, musisz mieć **Azure CDN Premium z profilu Verizon** . Ta opcja jest najbezpieczniejsza i dostosowywalna. Dostęp klienta jest oparty na parametrach zabezpieczeń ustawionych w tokenie zabezpieczającym. Po utworzeniu i skonfigurowaniu tokenu zabezpieczającego będzie on wymagany dla wszystkich adresów URL punktów końcowych usługi CDN. Jednak ze względu na regułę ponownego zapisywania adresu URL token sygnatury dostępu współdzielonego nie jest wymagany w punkcie końcowym usługi CDN. Jeśli token sygnatury dostępu współdzielonego później stanie się nieprawidłowy, Azure CDN nie będzie już można ponownie sprawdzić poprawności zawartości z serwera pochodzenia.

1. [Utwórz token zabezpieczający Azure CDN](./cdn-token-auth.md#setting-up-token-authentication) i aktywuj go przy użyciu aparatu reguł dla punktu końcowego usługi CDN i ścieżki, w której użytkownicy mogą uzyskać dostęp do pliku.

   Adres URL punktu końcowego tokenu zabezpieczającego ma następujący format:   
   `https://<endpoint hostname>.azureedge.net/<container>/<file>?<security_token>`
 
   Na przykład:   
   ```
   https://sasstoragedemo.azureedge.net/container1/demo.jpg?a4fbc3710fd3449a7c99986bkquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
       
   Opcje parametrów uwierzytelniania tokenu zabezpieczającego są inne niż opcje parametrów dla tokenu SAS. Jeśli podczas tworzenia tokenu zabezpieczającego zdecydujesz się używać czasu wygaśnięcia, należy ustawić tę samą wartość jako czas wygaśnięcia tokenu SAS. Dzięki temu zagwarantujesz przewidywalność czasu wygaśnięcia. 
 
2. Użyj [aparatu reguł](./cdn-verizon-premium-rules-engine.md) , aby utworzyć regułę ponownego zapisywania adresu URL, aby umożliwić dostęp tokenu SAS do wszystkich obiektów BLOB w kontenerze. Propagowanie nowych reguł trwa do 4 godzin.

   Następująca przykładowa reguła ponownego zapisywania adresu URL używa wzorca wyrażenia regularnego z grupą przechwytywania i punktem końcowym o nazwie *sasstoragedemo*:
   
   Źródło:   
   `(container1/.*)`
   
   Miejsce docelowe:   
   ```
   $1&sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![Reguła ponownego zapisywania adresu URL usługi CDN — pozostała ](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
    ![ reguła ponownego zapisywania adresu URL sieci CDN — uprawnienie](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

3. W przypadku odnowienia sygnatury dostępu współdzielonego należy zaktualizować regułę ponownego zapisywania adresów URL przy użyciu nowego tokenu sygnatury dostępu współdzielonego. 

## <a name="sas-parameter-considerations"></a>Zagadnienia dotyczące parametrów SAS

Ponieważ parametry sygnatury dostępu współdzielonego nie są widoczne dla Azure CDN, Azure CDN nie może zmienić jego zachowania dostarczania na podstawie tych parametrów. Ograniczenia określonych parametrów mają zastosowanie tylko w przypadku żądań, które Azure CDNją na serwerze źródłowym, a nie dla żądań z klienta do Azure CDN. Takie rozróżnienie jest ważne, aby wziąć pod uwagę podczas ustawiania parametrów SAS. Jeśli te zaawansowane funkcje są wymagane i używasz [opcji 3](#option-3-using-cdn-security-token-authentication-with-a-rewrite-rule), ustaw odpowiednie ograniczenia dotyczące tokenu zabezpieczeń Azure CDN.

| Nazwa parametru SAS | Opis |
| --- | --- |
| Rozpocznij | Czas, który Azure CDN może rozpocząć dostęp do pliku obiektu BLOB. Ze względu na pochylenie zegara (gdy sygnał zegara dociera do różnych składników), wybierz czas 15 minut wcześniej, jeśli chcesz, aby zasób był dostępny od razu. |
| End | Czas, po upływie którego Azure CDN nie może uzyskać dostępu do pliku obiektu BLOB. Poprzednio buforowane pliki w Azure CDN są nadal dostępne. Aby kontrolować czas wygaśnięcia pliku, ustaw odpowiedni czas wygaśnięcia w tokenie zabezpieczającym Azure CDN lub Przeczyść element zawartości. |
| Dozwolone adresy IP | Opcjonalny. Jeśli używasz **Azure CDN z Verizon**, możesz ustawić ten parametr na zakresy zdefiniowane w [Azure CDN z zakresów adresów IP Verizon Edge](./cdn-pop-list-api.md). Jeśli używasz **Azure CDN z Akamai**, nie można ustawić parametru zakresy adresów IP, ponieważ adresy IP nie są statyczne.|
| Dozwolone protokoły | Protokoły dozwolone dla żądania wysłanego za pomocą sygnatury dostępu współdzielonego konta. Ustawienie HTTPS jest zalecane.|

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat sygnatury dostępu współdzielonego, zobacz następujące artykuły:
- [Używanie sygnatury dostępu współdzielonego (SAS)](../storage/common/storage-sas-overview.md)
- [Sygnatury dostępu współdzielonego, część 2: Tworzenie i używanie SAS z magazynem obiektów BLOB](../storage/common/storage-sas-overview.md)

Aby uzyskać więcej informacji na temat konfigurowania uwierzytelniania tokenów, zobacz [Zabezpieczanie zasobów platformy Azure Content Delivery Network przy użyciu uwierzytelniania tokenu](./cdn-token-auth.md).