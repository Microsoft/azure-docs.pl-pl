---
title: Azure AD Connect Health i prywatność użytkowników | Microsoft Docs
description: W tym dokumencie opisano prywatność użytkowników przy użyciu Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58ecdfefa79716bcc0a69063293d5a1067693a78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89182364"
---
# <a name="user-privacy-and-azure-ad-connect-health"></a>Prywatność użytkowników i Azure AD Connect Health 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>Ten artykuł dotyczy Azure AD Connect Health i poufności użytkowników.  Informacje dotyczące Azure AD Connect i prywatności użytkowników znajdują się w [tym](reference-connect-user-privacy.md)artykule.

## <a name="user-privacy-classification"></a>Klasyfikacja prywatności użytkownika
Azure AD Connect Health znajduje się w kategorii **procesor danych** klasyfikacji Rodo. Jako potok przetwarzający dane Usługa udostępnia usługi przetwarzania danych klientom najważniejszych partnerów i klientów końcowych. Azure AD Connect Health nie generuje danych użytkownika i nie ma niezależnej kontroli nad tym, jakie dane osobowe są zbierane i jak są wykorzystywane. Pobieranie, agregacja, analiza i raportowanie danych w Azure AD Connect Health są oparte na istniejących danych lokalnych. 

## <a name="data-retention-policy"></a>Zasady przechowywania danych
Azure AD Connect Health nie generuje raportów, nie przeprowadza analizy ani nie udostępnia szczegółowych informacji poza 30 dni. W związku z tym Azure AD Connect Health nie przechowują, przetwarzają ani nie przechowują danych dłużej niż 30 dni. Ten projekt jest zgodny z przepisami Rodo, regulacjami zgodności ochrony prywatności firmy Microsoft i zasadami przechowywania danych usługi Azure AD. 

Dane dotyczące serwerów z aktywną **usługą kondycji są nieaktualne** w **przypadku alertów** z ponad 30 kolejnych dni sugerują, że żadne dane nie dotarły do usługi Connect Health w tym okresie. Te serwery zostaną wyłączone i nie będą wyświetlane w portalu programu Connect Health. Aby ponownie włączyć serwery, należy odinstalować i [ponownie zainstalować agenta kondycji](how-to-connect-health-agent-install.md). Należy pamiętać, że nie dotyczy to **ostrzeżeń** z tym samym typem alertu. Ostrzeżenia wskazują, że na serwerze z alertami nie ma częściowych danych. 
 
## <a name="disable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Wyłącz zbieranie i monitorowanie danych w Azure AD Connect Health
Azure AD Connect Health umożliwia zatrzymanie zbierania danych dla każdego pojedynczego monitorowanego serwera lub wystąpienia monitorowanej usługi. Można na przykład zatrzymać zbieranie danych dla poszczególnych serwerów usług AD FS (Active Directory Federation Services) monitorowanych przy użyciu Azure AD Connect Health. Możesz również zatrzymać zbieranie danych dla całego wystąpienia usług ADFS, które jest monitorowane przy użyciu Azure AD Connect Health. Po wybraniu tej opcji odpowiednie serwery zostaną usunięte z portalu Azure AD Connect Health po zatrzymaniu zbierania danych. 

>[!IMPORTANT]
> Aby usunąć monitorowane serwery z Azure AD Connect Health, musisz mieć uprawnienia administratora globalnego usługi Azure AD lub roli współautor w usłudze Azure RBAC.
>
> Usuwanie wystąpienia serwera lub usługi z Azure AD Connect Health nie jest akcją odwracalną. 

### <a name="what-to-expect"></a>Czego można oczekiwać?
Jeśli zatrzymasz zbieranie i monitorowanie danych dla pojedynczego monitorowanego serwera lub wystąpienia monitorowanej usługi, zwróć uwagę na następujące kwestie:

- Po usunięciu wystąpienia monitorowanej usługi wystąpienie zostanie usunięte z listy Azure AD Connect Health monitorowania usługi w portalu. 
- Po usunięciu monitorowanego serwera lub wystąpienia monitorowanej usługi Agent kondycji nie zostanie odinstalowany ani usunięty z serwerów. Agent kondycji jest skonfigurowany tak, aby nie wysyłał danych do Azure AD Connect Health. Należy ręcznie odinstalować agenta kondycji na wcześniej monitorowanych serwerach.
- Jeśli Agent kondycji nie został odinstalowany przed wykonaniem tego kroku, mogą pojawić się zdarzenia błędów na serwerach związanych z agentem kondycji.
- Wszystkie dane należące do wystąpienia monitorowanej usługi zostaną usunięte zgodnie z zasadami przechowywania danych Microsoft Azure.

### <a name="disable-data-collection-and-monitoring-for-an-instance-of-a-monitored-service"></a>Wyłączanie zbierania i monitorowania danych dla wystąpienia monitorowanej usługi
Zobacz [Jak usunąć wystąpienie usługi z Azure AD Connect Health](how-to-connect-health-operations.md#delete-a-service-instance-from-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-a-monitored-server"></a>Wyłącz zbieranie i monitorowanie danych dla monitorowanego serwera
Zobacz [Jak usunąć serwer z Azure AD Connect Health](how-to-connect-health-operations.md#delete-a-server-from-the-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-all-monitored-services-in-azure-ad-connect-health"></a>Wyłącz zbieranie i monitorowanie danych dla wszystkich monitorowanych usług w Azure AD Connect Health
Azure AD Connect Health udostępnia również opcję zatrzymania zbierania danych przez **wszystkie** zarejestrowane usługi w dzierżawie. Przed podjęciem działania Zalecamy dokładne rozważenie i pełne potwierdzenie wszystkich administratorów globalnych. Po rozpoczęciu procesu program Connect Health przestanie odbierać, przetwarzać i zgłaszać wszystkie dane wszystkich usług. Istniejące dane w usłudze Connect Health będą przechowywane przez nie dłużej niż 30 dni.
Jeśli chcesz zatrzymać zbieranie danych określonego serwera, wykonaj kroki opisane w sekcji Usuwanie określonych serwerów. Aby zatrzymać zbieranie danych dzierżawy, wykonaj następujące kroki, aby zatrzymać zbieranie danych i usunąć wszystkie usługi dzierżawy.

1. Kliknij pozycję **Ustawienia ogólne** w obszarze Konfiguracja w bloku głównym. 
2. Kliknij przycisk **Zatrzymaj zbieranie danych** w górnej części bloku. Inne opcje ustawień konfiguracji dzierżawy zostaną wyłączone po rozpoczęciu procesu.  
 
   ![Zatrzymaj zbieranie danych](./media/reference-connect-health-user-privacy/gdpr4.png)
  
3. Upewnij się, że lista dołączanych usług ma wpływ na zatrzymywanie zbierania danych. 
4. Wprowadź dokładną nazwę dzierżawy, aby włączyć przycisk **Usuń** akcję
5. Kliknij przycisk **Usuń** , aby wyzwolić usunięcie wszystkich usług. Program Connect Health przestanie odbierać, przetwarzać i zgłaszać dane wysyłane z usług dołączanych. Cały proces może potrwać do 24 godzin. Należy zauważyć, że ten krok nie jest odwracalny. 
6. Po zakończeniu procesu w programie Connect Health nie będą widoczne żadne zarejestrowane usługi. 

   ![Po zatrzymaniu zbierania danych](./media/reference-connect-health-user-privacy/gdpr5.png)

## <a name="re-enable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Ponownie Włącz zbieranie i monitorowanie danych w Azure AD Connect Health
Aby ponownie włączyć monitorowanie w Azure AD Connect Health dla wcześniej usuniętej usługi monitorowanej, należy odinstalować i [ponownie zainstalować agenta kondycji](how-to-connect-health-agent-install.md) na wszystkich serwerach.

### <a name="re-enable-data-collection-and-monitoring-for-all-monitored-services"></a>Ponownie Włącz zbieranie i monitorowanie danych dla wszystkich monitorowanych usług

Zbieranie danych dla dzierżawców może zostać wznowione w Azure AD Connect Health. Przed podjęciem działania Zalecamy dokładne rozważenie i pełne potwierdzenie wszystkich administratorów globalnych.

>[!IMPORTANT]
> Poniższe kroki będą dostępne po 24 godzinach działania wyłączenia.
> Po włączeniu zbierania danych informacje o danych szczegółowych i monitorowaniu w programie Connect Health nie będą zawierać żadnych starszych danych zebranych przed. 

1. Kliknij pozycję **Ustawienia ogólne** w obszarze Konfiguracja w bloku głównym. 
2. Kliknij przycisk **Włącz zbieranie danych** w górnej części bloku. 
 
   ![Włączanie zbierania danych](./media/reference-connect-health-user-privacy/gdpr6.png)
 
3. Podaj dokładną nazwę dzierżawy, aby aktywować przycisk **Włącz** .
4. Kliknij przycisk **Włącz** , aby udzielić uprawnienia do zbierania danych w usłudze Connect Health. Zmiana zostanie wkrótce zastosowana. 
5. Postępuj zgodnie z [procesem instalacji](how-to-connect-health-agent-install.md) , aby ponownie zainstalować agenta na monitorowanych serwerach, a usługi będą obecne w portalu.  


## <a name="next-steps"></a>Następne kroki
* [Zapoznaj się z zasadami zachowania poufności informacji firmy Microsoft w centrum zaufania](https://www.microsoft.com/trustcenter)
* [Azure AD Connect i prywatność użytkowników](reference-connect-user-privacy.md)

